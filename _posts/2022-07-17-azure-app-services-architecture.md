---
layout: post
author: Lucas
title: Azure App Service Architecture
subtitle: Dissecting Antares
categories: [app service, azure]
tags: [azure, cloud, vnet, app service, dns]
---
<!--🔴 🟠 ⚫ ⚪ 🟣 🟢 🟡 🔵-->

## App Service Within Azure Architecture

![Azure - Antares Architecture](/assets/images/azure_antares_architecture.png)

- The architecture for App Service is called Antares.
- The Control Plane API requests start from ARM (Azure Resource Manager) to Antares' Geomaster.
- The Geomaster is a component that serves as a load balancer keeping track of state of App Service's stamps (scale units) around the world and directing API call traffic.
- There are dozens to thousands of stampsin each Azure region.
- Each scale unit, or stamp, consists of ~1000 virtual machines, also called servers or workers.

## App Service Creation Flow

![App Service app creation flow](/assets/images/app_service_creation_flow.png)

1. User makes a request to create a new site.
2. ARM makes sure user has access to the resource to allow the given operation (create in this case) and forwards the requests to App Service Geo-Master.
3. Geo-Master finds the best suitable scale unit for the user’s request and forwards the request.
4. The scale unit creates the new application.
5. Geo-Master reports success on the create request.

## Stamp Architecture

![App Service stamp architecture](/assets/images/app_service_stamp_architecture.png)

- There are different types of workers inside of a stamp:
  - **Web workers**: the vast majority of workers in a stamp. It's the server that runs the app. They can be shared between clients or dedicated to a single client, depending on the App Service Plan.
  - **Front end**: a layer 7 (HTTP) load balancer that distributes requests too all web workers allocated for an app. It uses round-robin by default.
  - **File servers**: worker that mounts to Blob Storage containing data needed to run app (code, images, etc) and exposes it as a network drive, which in turn is mapped by the web worker as a local drive. Any file-relateed r/w operation performed by the app passes through a file server.
  - **API controller**: performs management operations inside stamp. It is to the Control Plane what the Front end is to the Runtime Plane. Receives API calls and orchestrates the steps to fulfill the requests. Examples:
    - When Geo-Master passes an API call to create a new application, the API controller orchestrates the required steps to create the application at the scale unit.
    - When you use the Azure portal to reset your application, it’s the API controller that notifies all Web Workers currently allocated to your application to restart your app.
  - **Azure SQL**: persists app metadata, only accessed by the data role.
  - **Data role**: a cache layer between the SQL database and all the other workers. Examples:
    - Web workers ask Data role for website configuration.
    - Front end workers ask Data role for list of workers they can route an app's requests to.
  - **Publisher**: exposes FTP functionality for customers to access their application content and logs in the Blob Storages and file servers. It also gives customers another way of deploying apps.

## Web Worker Architecture

![Antares dynamic website provisioning - control flow](/assets/images/antares_dynamic_prov_control.png)

- Basic components:
  - **HTTP.sys**: receives requests (by matching, as seen above) based on URL and port, then sends it to HTTP Request Queue.
  - **HTTP Request Queue**: sends requests received via site-specific binding to the site specific HTTP request queue, and sends all other requests to the MiniARR HTTP request queue.
  - **MiniARR Worker Process**: only receives requests for websites not yet set up. Tells DWAS to create the structure for the website requested.
  - **DWAS**: receives input from two components:
    - From **MiniARR**, triggering a dynamic website provisining. DWAS:
      1. receives host name from MiniArr and uses it to fetch site config (`StartSiteContext`) from DataRole.
      2. generates `applicationHost.config`, `rootweb.config`, etc into its temp directory.
          - Both config files are created with "transform pipeline" code, under `Microsoft.Web.Hosting\Utilities\Transformers`.
          - The transform pipeline can inject user-specific config settings into the `.config` files, such as Virtual Directories, Apps, Handler Mappings (PHP/Python support), etc.
      3. generates low-privileged user identity to run site code.
      4. creates a local directory for site, and sets up a symlink to the site's root directory. (eg. `C:\DWASFiles\Sites\foo\VirtualDir0` pointing to `\\FileServerIP1\volume-3-default\<webspaceGuid>\<siteGuid>`)
      5. copies config files from temp to local directory at `config\`.
      6. creates a site-specific binding in HTTP.sys and an HTTP request queue, asking to be notified once queue gets a requests.
      7. registers with Data Role to receive change notifications to website config.
      8. sets up rest of state in machine needed to run the site (VNet and MSI integration, cert installation, local cache hydration, etc), based on site config.
    - From **site-specific HTTP request queue**, triggering DWAS to:
      1. spin up and initializes a Worker Process (`w3wp.exe`) for the site.
      2. create a sandbox by virtualizing `D:\home` which points to the site's root directory and only allows access to that SMB path.
    - From **DataRole** (outside of Web Worker) to change site configuration. DWAS:
      1. receives a notification from DataRole by long-polling for it.
      2. fetches new `StartSiteContext` from DataRole and compares with previous version.
      3. overwrites `.config` files if there are changes and orchestrates the changes to ensure new config is applied.
          - Overwriting the `.config` files causes AppDomain recycles for ASP.NET apps, and can cause child-process recycles for other stacks.
          - Config changes such as an update to app-settings usually require worker process recycle.
      4. gets informed about internal system changes such as movement of storage volumes through long-polling calls to DataRole as well.
          - Change to the site's root path requires worker process recycle.
  - **Worker Process**: sandboxed site-specific worker process that dequeues requests in its corresponding queue, then processes it through a module pipeline, much like in IIS (out of scope for this post).
    - Its initialization process is similar to IIS as well, requiring DWAS to guide it through start-up, and then signal when it is ready to start processing requests.
    - The process is under a "job object", which imposes restrictions on the amount of memory and CPU the `w3wp.exe` can utilize.
    - Again, like IIS, the worker process expects to retrieve config files from the DWAS local directory at `C:\DWASFiles\Sites\foo\config\`.

## IIS Overview

- Antares, in its initial version, was more of an IIS-as-a-service.
- IIS' model for hosting app code:
  - Config concepts: sites, bindings, apps, VirtualDirectories, ApplicationPools, handler mappings, etc
  - Runtime entities: HTTP(.sys) bindings, HTTP(.sys) request queues, user identities, worker processes (w3wp.exe), etc

## Referencee

- <https://docs.microsoft.com/en-us/archive/msdn-magazine/2017/february/azure-inside-the-azure-app-service-architecture>

## Questions

- What is the underlying tech behind the ~1000 VMs per stamp? Are they in physical servers/hosts dedicated to App Service only? How many physical servers?
- What exactly is the sandbox? The `D:\home`? Does the worker process get assigned the low-privileged user identity, and what privileges does it have? Seems that the worker process also has access to DWAS local directory.
