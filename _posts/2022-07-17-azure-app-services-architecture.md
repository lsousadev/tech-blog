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

- Basic components:
  - **DWAS**: Windows service (DWASSVC) that runs in all web worker virtual machines. It is in charge of provisioning the websites and maintaining the health of the VM, websites, and processes.
    - When provisioning a website for the first time in the VM, it associates a sandbox to that app.

## Referencee

- <https://docs.microsoft.com/en-us/archive/msdn-magazine/2017/february/azure-inside-the-azure-app-service-architecture>

## Questions

- What is the underlying tech behind the ~1000 VMs per stamp? Are they in physical servers/hosts dedicated to App Service only? How many physical servers?
