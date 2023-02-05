---
layout: post
author: Lucas
title: Comptia Security+ Certification Notes
subtitle: Note Repository
categories: [cybersecurity]
tags: [comptia, security, certificate, security+, cybersecurity]
---
<!--🔴 🟠 ⚫ ⚪ 🟣 🟢 🟡 🔵-->

## 1.0 Attacks, Threats, and Vulnerabilities

### 1.1 Social engineering techniques

- Credential harvesting: transfer of password files and authentication information.
- Invoice scam: fake invoices.
- Spear phishing: directed attack aiming to obtain private/personal information.
- Watering hole attack: users visit a central website or location.

### 1.2 Types of attack

- Malwares:
  - RAT (Remote Access Trojan): can control a computer using desktop sharing and other admin functions (screenshots, reboot, etc).
  - Trojan: hidden inside seemingly harmless application.
  - Worm: replicates itself between systems without attacker or victim intervention.

### 1.3 Application attacks

- Buffer overflow: attacker attempts to manipulate the contents of the memory by storing information into an area of memory that overflows the boundary of the buffer.
- Cross-site scriptiong: includes a script that would reference another site that was trusted by the browser.
- Directory traversal: attempts to read or access files that are outside the scope of the web server's file directory.
- DLL (Dynamic Link Library) injection: takes advantage of a library (the vector, in this casE) used by an application.
- Race condition: occurs when two processes happen at the same time to unexpected results.
- Resource exhaustion: denial of service attack that can often be done by a single device over low bandwidths, using up the available resources on a device. Eg. zip bomb.

### 1.4 Network attacks

- DDoS (Distributed Denial of Service): good for exploiting memory leaks, where unused memory is not properly released, and eventually all available memory is used and the system crashes.
- DNS:
  - DNS poisoning: can modify a DNS server to modify the IP address provided during the name resolution process. If an attacker modifies the DNS information, they can direct client computers to any destination IP address.
  - Domain hijacking: will modify the primary DNS settings for a domain and will allow an attacker to direct users to any IP address.
- Layer 2 attacks:
  - ARP poisoning: malicious change of records in the ARP table, which maps IP addresses to MAC addresses.
- Wireless:
  - Disassociation: uses specially crafted wireless management frames to disconnect wireless devices from a network.
  - Evil twin: a wireless access point that looks similar to the authentic AP.

### 1.5 Threat actors, vectors, and intelligence sources\

- Actors and threats:
  - Competitors: interest in making other company look bad.
  - Criminal syndicates: usually motivated by money.
  - Hacktivists: often makes a political statement.
  - State actors: highly sophisticated, focus on government information or disruption of governmental operations.

### 1.6 Vulnerability types

- Weak configurations:
  - Weak encryption: Wi-Fi security mode WEP is outdated and uses weak encryption technology.

### 1.7 Security assessment techniques

- SIEM (Security Information and Event Management): system that consolidates data from devices on the network and provides log searching and reporting features.
- Vulnerability scans:
  - False negative: fails to detect an issue when it exists.
  - Credentialed: authenticated to the OS with access to files only authorized users would.

### 1.8 Penetration testing techniques

- Penetration testing:
  - Backdoor testing: directly query systems for installed backdoors.
  - Known environment: the attacker has complete systems and infrastructure details.
  - Partially known environment: detailed information about only specific systems or applications.
  - Unknown environment/black box: no pryor knowledge of the systems under attack.
- Reconnaissance:
  - Footprinting: passive, mainly using OSINT.
  - OS fingerprinting: actively query and receive responses across the network to fingerprint an OS.

## 2.0 Architecture and Design

### 2.1 Security concepts

- Data protection:
  - At-rest: stored in a device.
  - In-transit: moving across a network.
  - In-use: in memory of a device.
- Deception and disruption:
  - Honeypots: simulates a production environment without putting any of the production data at risk. As attackers attempt to exploit the honeypot, their techniques and methods are logged. With these logs, administrators can gain additional insights into the attacks and processes used by the attackers.
- Hashing: commonly used to provide integrity verification. Often coupled with blockchain.

### 2.2 Virtualization and cloud computing concepts

### 2.3 Application development, deployment, and automation concepts

- Automation/scripting:
  - Continuous delivery: automates the testing process, but it requires human intervention for the final deployment to production.
  - Continuous deployment: automates every aspect of deploying software. After the developer creates the code, the testing and deployment process is completely hands-off and does not need any human intervention.
  - Continuous integration: code is constantly written and merged into the central repository many times each day.
- Integrity measurement: checks a deployment for the secure baseline of firewall settings, patch levels, OS versions, and any other security components associated with the application.
- Secure coding techniques:
  - Stored procedures: SQL queries that execute on the server side instead of the client application. The client application calls the stored procedure on the server, and this prevents the client from making any changes to the actual SQL queries.

### 2.4 Authentication and authorization design concepts

- Authentication methods:
  - Attestation: confirms the identity of the device connecting to the network.
  - Directory services: central database that stores usernames, passwords, computers, printers, and other devices that might be connected to the network. This database is distributed across multiple devices, and those databases will communicate to each other, and send replication data so that every database is always up to date with the latest information. The users only need to remember this single authentication method, and that single method generally gives you access to all of the resources you need on that network.
  - Federation: a way that you can allow someone to authenticate to your network, using credentials that are stored with a third party. First you must connect with the third party entity, then be sure to use the same authentication type.
  - Technologies:
    - TOTP (Time-based one-time password): algorithm that provides a pseudo-random number as an authentication factor.
    - HOTP (HMAC-based One-Time Password): algorithm that provides an authentication factor based on a one-time password.
- Biometrics:
  - FAR (False Acceptance Rate): the likelihood that an unauthorized user will be accepted. The FAR should be kept as close to zero as possible.
  - FRR (False Rejection Rate): the likelihood that an authorized user will be rejected. If the FRR is incrementing, then authorized users will not gain access to the data center.

### 2.5 Cybersecurity resilience

- Backup types:
  - Differential: will copy all files that have been changed since the last full backup.
  - Incremental: will archive all of the files that have changed since the last full or incremental backup.
  - Snapshot: backup feature that can capture both a full backup of the virtual system and incremental changes that occur over time. It’s common to take a snapshot of a VM for backup purposes and before making any significant changes to the VM. If the changes need to be rolled back, a previous snapshot can be selected and instantly applied to the VM.
- Redundancy:
  - Disk:
    - Raid 0: striping without parity, high performance but no fault tolerance.
    - Raid 1: mirroring, requires twice the disk space but no extra performance.
    - Raid 5: stripping with parity, boost in performance + an additional disk for redundancy.

### 2.6 Embedded and specialized systems

- Embedded systems: hardware + software designed for a specific function or task, usually optimized for size or cost.
  - FPGA (Field Programmable Gate Array): integrated circuit that can be configured after manufactoring/shipping. Doesn't require hardware replacement, as it can be reprogrammed. Eg. routers and switches.
- SCADA (Supervisory Control and Data Acquisition System): large scale, multi-site industrial control system. Requires extensive segmentation, no access from the outside.
- IoT: sensors, smart devices, wearable tech, etc.
- MFP (Multifunction Print): scanner + printer + fax.
- RTOS (Real-Time OS): OS with deterministic processing schedule. It can instantly react to input without any significant delays or queuing in the operating system. Operating systems used by the military, automobile manufacturers, and industrial equipment developers often use RTOS to ensure that certain transactions can be processed without any significant delays.
- Specialized systems: medical systems, smart meters, etc.
- SoC (System on a Chip): multiple components running on a single chip. Eg. Raspberry Pi.

### 2.7 Physical security controls

- Access control vestibules: controls flow of people through a particular area. Unlocking one door of the vestibule usually restricts the other door. Common in data centers.
- USB data blocker: allow power but prevent data.
- Secure data destruction:
  - Degaussing: removes the electromagnetic field of storage media and electronics.
  - Pulping: places papers in large tank to remove ink, then paper is broken down into pulp and recycled.

### 2.8 Cryptographic concepts

- AES (Advanced Encryption Standard): common encryption protocol/cipher.
- Asymmetric encryption: different keys for encryption and decryption. Diffie-Hellman process is a good example to securely derive a session key.
- Block cipher: used to encrypt fixed-length groups of data.
- Blockchain: a ledger distributed across multiple devices, which allows for checks and balances. Useful to keep track of events. The ledger is a shared document with a list of all blockchain transactions. The ledger is shared among everyone in the blockchain, and all transactions are available to view on this central ledger.
- Diffie-Hellman: a key-agreement protocol that can combine public and private keys to derive the same session keys on both sides of a conversation without sending that session key across the network. Does not provide encryption or authentication itself.
- Digital signature: cryptographic method to check integrity, authentication, and non-repudiation of a message. A certificate authority (CA) will sign a cert in order to give it trust. If the CA is trusted, the cert is trusted.
- ECC (Elliptic-Curve Cryptography): uses small keys and has smaller storage and transmission requirements.
- Perfect forward secrecy: key exchange method that creates new and unique set of session keys for each session.
- Quantum computing: uses quantum theory to perform high-speed calculations.
- Stenography: process of hiding information within another document. Eg. embed data or documents within image files.

## 3.0 Implementation

### 3.1 Secure protocols

- Protocols:
  - DNSSEC (Domain Name System Security Extensions): adds origin authentication and data integrity check to the old DNS protocol.
  - FTPS (File Transfer Protocol Secure): uses SSL.
  - HTTPS: HTTP over TLS.
  - IPsec (Internet Protocol Security): authentication and encryption for every packet. Two core protocols:
    - AH (Authentication Headers): contains a hash (usually HMAC hashing algorithm) of the IPsec packet to provide integrity protection of the data. The AH does not encrypt data.
    - ESP (Encapsulation Security Payload): encrypts the data in the IP packet.
  - LDAPS (Lightweight Directory Access Protocol Secure): LDAP over SSL. Standard for accessing a network directory.
  - S/MIME (Secure Multipurpose Internet Mail Extensions): provides security for the content of an email message. Public/private key encryption mechanism and digital signature provider at a client level (for most modern email clients). It encrypts all email information from client to client, regardless of the communication used between email servers.
  - Secure POP and Secure IMAP: adds SSL to both protocols at a server level.
  - SFTP (SSH File Transfer Protocol): uses SSH, provides extra functionality.
  - SNMPv3 (Simple Network Management Protocol v3): encryption, data integrity, authentication.
  - SRTP (Secure Real-Time Transport Protocol): adds security features (authentication, encryption, integrity, replay protection) to RTP (voice and video).
  - SSH (Secure Shell): secure terminal communication and file transfer features. Does not use TLS.

### 3.2 Host and application security solutions

- Application security:
  - Code signing: code that has been digitally signed by the application developer can be evaluated to ensure that nothing has changed with the application code since it was published.
- Boot integrity:
  1. UEFI BIOS:
     - Holds manufacturer public key.
     - Digital signature verified during BIOS update.
     - Prevents unauthorized writes to flash.
     - Secure boot: checks bootloader's digital signature for a trusted or manually approved cert.
  2. Trusted boot: after secure boot, OS kernel's digital signature is checked, then other startup components such as boot drivers, startup files, etc.
  3. Boot attestation: measure if any change has occurred in the OS. UEFI stores in TPM hash of firmware, boot drivers, and everything loaded during previous boot processes. The measured boot process performs remote attestation using the TPM.
- Disk encryption:
  - FDE (Full Disk Encryption): encrypts everything on the drive. Eg. BitLocker, FileVault.
  - SED (Self-Encrypting Drive): hardware-based FDE, no software needed.
- Endpoint protection:
  - HIDS (Host-based Intrusion Detection System)
  - HIPS (Host-based Intrusion Prevention System)
- TPM (Trusted Platform Module): hardware that is part of a computer's motherboard, specifically used to assist and protect with cryptographic functions (random nunmber gen, key gen, storage, etc). It includes built-in protections against brute force and dictionary attacks, as well as burned-in keys, which are helpful for full-disk encryption.

### 3.3 Secure network designs

- Network appliances:
  - Firewalls:
    - UTM (Unified Threat Management): advanced firewall device that can include many functionalities, such as routing, switching, URL filtering, content inspection, IDS, VPN endpoints, bandwidth shaping, etc.
    - WAF (Web Application Firewall): examines user input to a browser-based
application and allows or denies traffic based on the expected input. This is
commonly used to prevent SQL injections, cross-site scripting, or similar
input-related security concerns.
  - HSM (Hardware Security Module): high-end cryptographic appliance to securely store keys and certs for multiple devices. Similar to TPM, but meant for data centers rather than a single system.
  - IPS (Intrusion Prevention System): monitors network traffic for exploit attempts such as buffer overflows, cross-site scripting, SQL injections, or other known exploits. If an exploit attempt is identified in the traffic flow, the IPS will block the traffic and prevent the attack.
  - NIDS/NIPS (Network Intrusion Detection/Prevention System): can be signature-based, check for anomalous behavior, etc.
- VPN:
  - Full tunnel: all traffic is tunneled.
  - IPsec: commonly used for site-to-site VPN.
  - Split tunnel: allows portion of the traffic (eg. non-work related traffic) to use non-tunneled option.

### 3.4 Wireless security settings

- Authentication protocols:
  - EAP-FAST (Extensible Authentication Protocol - Flexible Authentication via Secure Tunneling): updated LEAP after WEP was replaced by WPA.
  - EAP-TLS: performs mutual authentication before creating a TLS tunnel, so all devices require digital certificates.
  - EAP-TTLS (EAP Tunneled TLS): only requires a single digital certificate on the authentication server in order to create a TLS tunnel.
  - IEEE 802.1x: port based NAC (Network Access Control), used in conjunction with a method (eg. one of the EAP methods) and an auth DB in the backend (eg. RADIUS, LDAP, TACACS+). It uses a centralized authentication server which users can use normal credentials for.
  - PEAP (Protected EAP): authentication protocol that encapsulates EAP in a TLS tunnel to ensure a protected authentication process.
  - RADIUS: common method of centralizing authentication for users.
- Cryptographic protocols:
  - WPA2 (Wi-Fi Protected Access v2)
  - WPA3: enhanced PSK authentication process that privately derives session keys instead of sending key hashes across the network. Can be configured with a shared key, or with 802.1X to integrate with an existing authentication database.
- Methods:
  - Enterprise: uses IEEE 802.1X, which offers enterprise-grade authentication.
  - PSK (Pre-shared Keys): designed for home use, common in home wireless routers.
  - WPS (Wi-Fi Protected Setup): standard method of connecting devices to a wireless network without requiring a PSK or passphrase.

### 3.5 Secure mobile solutions

- Deployment models:
  - BYOD: employee purchases mobile platform.
  - COPE (Corporate-Owned Personally Enabled): device purchased by company for corporate and personal use.
  - CYOD: like COPE, but chosen by the employee.
  - Corporate-owned: device purchased by company for corporate use only.
  - VDI (Virtual Desktop Infrastructure): field teams access their applications from any supported device without MDM or concern of corporate data stored in the devices.
- MDM (Mobile Device Management): provides a centralized management system for all mobile devices. From this central console, security administrators can set policies for many different types of mobile devices.

### 3.6 Cloud cybersecurity solutions

- Solutions:
  - CASB (Cloud Access Security Broker) can be used to apply security policies to cloud-based implementations. Its common functions are to:
    - Provide visibility into application.
    - Provide visibility into data security policy use.
    - Verify compliance with formal standards.
    - Monitor and identify threats.

### 3.7 Identity and account management controls

### 3.8 Authentication and authorization solutions

- Access control schemes:
  - ABAC: attribute-based or rule-based, combines different parameters to determine access.
  - DAC: discretionary, owner of resource controls access. Eg. user creates a spreadsheet and gives editor access to a coworker.
  - MAC: mandatory, based on security level assigned to resource. Users are sassigned a security level and only have access to files of the same level or below.
  - Role-based AC: rights and permissions assigned based on role of a user. Eg. manager permissions vs. accountant permissions.
  - Rule-based AC: based on series of system-enforced rules. Eg. use only a particular browser, or at a specific time of day.
- Authentication/authorization:
  - EAP: authentication framework for providing the transport and usage of material and parameters generated by EAP methods, not a specific authentication mechanism.
  - IEEE 802.1X: port based NAC (Network Access Control), used in conjunction with a method (eg. one of the EAP methods) and an auth DB in the backend (eg. RADIUS, LDAP, TACACS+). It uses a centralized authentication server which users can use normal credentials for.
  - Kerberos: network authentication protocol that provides SSO and mutual authentication using cryptographic tickets behind the scenes.
  - LDAP (Lightweight Directory Access Protocol): network authentication protocol.
  - MSCHAP (Microsoft Challenge-Handshake Authentication Protocol): older implementation no longer commonly used. Preferred methods are L2TP, IPsec, 802.1X, etc.
  - PAP (Password Authentication Protocol): validates username and password.
  - RADIUS (Remote Authentication Dial-in User Service): authentication protocol.
  - TACACS+: flexible remote authentication protocol, does not provide SSO capabilities.

### 3.9 Public key infrastructure

- Concepts:
  - Chaining: Intermediate certificates are often listed between a web server’s SSL certificate and the root certificate. This list of intermediate certificates is called a “chain.” It’s important to configure web servers with the proper chain, or the end user may receive an error in their browser that the server can’t be trusted.
  - Key escrow: use a trusted third party to store a copy of the decryption keys.
  - If a custom CA is used for signing a certificate for a server, the client must have that custom CA's certificate installed in order to successfully verify the certificate's validity.
  - OCSP stapling: with OCSP, it is challenging to keep checking back with the CA to see if a certificate is still valid. OSCP stapling solves that by storing certificate status information locally, so local validation can be performed instead of having to communicate with the CA every time. It is a method that has the certificate holder verify their own certificate status. The OCSP status is commonly “stapled” into the SSL handshake process. Since the status information is digitally signed by the CA, its validity can be trusted.
  - Offline CA: common way to prevent the exploitation of a root authority.
  - Pinning: embeds a service's certificate inside of an application. When the application contacts the service, the service certificate will be compared to the pinned certificate. If the certificates match, the application knows that it can trust the service.
- PKI: describes the processes and procedures associated with maintaining digital certificates.
  - CA (Certificate Authority): the administrative control for any public key infrastructure deployment. Deploys and manages certificates.
  - CSR (Certificate Signing Request): used during key creation process. Public key is sent to CA to be signed as part of CSR.
  - CRL (Certificate Revocation List): a file containing a list of the revoked certificates. This list is maintained by the associated certificate authority.
  - Hierarchical CA: design that includes intermediate CAs to distribute certificate management load.
  - OCSP (Online Certificate Status Protocol): protocol used by the browser to check the revocation status of a certificate.
  - RA (Registration Authority): works with the certificate authority to identify and authenticate the certificate requester. Verifies the entity requesting the certificate.
  - x.509: defines the structure of digital certificates. It makes it easy for everyone to view the contents of a certificate.

## 4.0 Operations and Incident Response

### 4.1 Organizational security

- Forensics:
  - dd: create an image of a partition or disk. If a server is infected with malware, an image of the server storage drive can be created with `dd`.
  - memdump: used to make a copy of everything stored in local system memory.
- Network reconnaissance and discovery:
  - arp: `arp -a` will show local ARP cache. Using `ping` to ping a device in the local network will add that device's IP and MAC addresses to the local ARP table.
  - Nessus: scanning tool to identify and help resolve vulnerabilities. It can view inbound and outbound statistics for all connections to a device.
  - net view: Windows command used to list the available file shares on a Windows computer.
  - netcat: can read or write information to the network. It can also be used to create an open connection on a device or to access a connection on a remote machine.
  - netstat: displays connectivity information about a device, including port listeners.
  - nmap: scans for all open network ports, can also perform a service scan to determine more information about the specific services running.
  - Wireshark: protocol analyzer that can provide information about every frame that traverses the network.

### 4.2 Policies, processes, and procedures for incident response

- Attack frameworks:
  - Diamond model: created as a way to standardize attack reporting and intrusion analysis. A way to summarize the aftermath of an intrusion. Documentation for this model includes information about the adversary, capability, infrastructure, and the victim.
  - MITRE ATT&CK: MITRE provides the ATT&CK framework as a knowledge base of attack types, techniques, and mitigation options.
- IR exercises:
  - Simulation: a useful test of disaster recovery processes, but it often requires a change to the existing systems to properly test the simulated disaster.
  - Tabletop: a walk-through exercise where the disaster recovery process can be discussed in a conference room without making any changes to the existing systems.
- IR plans:
  - Communication plan: predefined list of contacts and processes to inform key members of organization.
  - Continuity of operations plan: plan to ensure business will continue to operate when any type of outage or issue happens.
  - Stakeholder management: ongoing relationship between IT team and business customer.
- IR process:
  - Preparation: have documentation, policies, and procedures (eg. communication plan).
  - Identification: detection and analysis. IPS reports, anti-virus alerts, configuration change notifications, and other indicators.
  - Containment/Isolation: separate infected resource from rest of network.
  - Eradication: removes the cause of the event and restoring the systems back to their non-compromised state (remove malware, fix vulnerabilities, fix access, etc).
  - Recovery/Reconstitution: can be a long, phased approach.
  - Lessons learned: revisit the process to learn and improve next time.

### 4.3 Investigation support

- NetFlow: its logs can provide summary of network traffic, application usage, and details of network converesations from all devices in the network.

### 4.4 Mitigation techniques and environment security controls

- SOAR (Security Orchestration, Automation, and Response): designed to make security teams more efficient by automating processes and integration third-party tools. Usually has runbooks for tasks and playbooks for sequences of tasks.

### 4.5 Digital forensics

- Documentation/evidence:
  - Chain of custody: documents the interactions of every person who comes into contact with the evidence.
  - Legal hold: describes what type of data needs to be preserved for later use and for how long. Often requested by legal counsel and received by a security professional.
- Non-repudiation: ensures that the author of a document cannot be disputed.

## 5.0 Governance, Risk, and Compliance

### 5.1 Control types

- Category:
  - Managerial: controls how people act. Eg: security policies, standard operating procedures.
  - Operational: managed by people. Eg: security guards, awareness program.
  - Technical: systems preventing security events from occurring. Eg: AV, firewall.
- Type (each example can be more than one type):
  - Compensating: compensates when an attack occurs. Eg: Restoring server from a backup.
  - Corrective: mitigate damage. Eg: IPS blocking further malicious traffic.
  - Detective: detect and record intrusion attempts. Eg: IDS.
  - Deterrent: can't stop but tries to deter attacks. Eg: Camera or dog warning signs, lights around building.
  - Physical: physically block access. Eg: Door lock, security guard.
  - Preventive: access prevention. Eg: security guards, firewalls.

### 5.2 Organizational security posture

- ISO standards:
  - ISO 27001: foundational standard for ISMS (Information Security Management Systems), it is a framework designed to enhance privacy controls towards adding or improving a Privacy Information Management System (PIMS).
  - ISO 27002: focuses on information security controls.
  - ISO 27701: extends the previous two b adding detailed management of PII and data privacy.
  - ISO 31000: sets international standards for risk management practices.
- Key frameworks:
  - CCM (Cloud Control Matrix): provides security information and guidelines for cloud-based applications.
- Regulations, standards, and legislation:
  - GDPR (General Data Protection Regulation): regulation that governs data protection and privacy for individuals in the EU.
  - PCI DSS (Payment Card Industry Data Security Standard): minimum security requirements for storing and protecting credit card information.

### 5.3 Organizational security policies

### 5.4 Risk management processes and concepts

- Business impact analysis:
  - MTBF: mean time between failures, for repairable systems.
  - MTTF: mean time to failure, for non-repairable systems.
  - MTTR: mean time to restore.
  - RTO: recovery time objectives, how long after failure to get back up and running to a particular service level.
  - RPO: recovery point objectives, how much data loss is acceptable.
- Risk analysis:
  - ALE (Annualized Loss Expectancy) monetary. ALE = ARO x SLE.
  - ARO (Annualized Rate of Ocurrence): number of instances an event will occur.
  - Risk matrix/heat map: often presented as a graphical chart comparing the likelihood of risk with the consequence.
  - SLE (Single Loss Expectancy): monetary.
- Risk management strategies:
  - Acceptance: owner understands the risks and accepts the potential results.
  - Mitigation: strategy that decreases the threat level. Commonly done through use of additional security systems and monitoring.
  - Risk-avoidance: owner decides to stop participating in a high-risk activity.
  - Transference: moves the risk from one party to another.

### 5.5 Privacy and sensitive data concepts

- Privacy enhancing technologies:
  - Anonymization: changes data to remove or replace identifiable information.
  - Masking: hides some of the original data to protect sensitive information from view. While hidden, this data cannot be used for any functional purpose.
  - Minimization: limits the amount of collected information that can be gathered. Inherent to regulations such as HIPAA and GDPR.
  - Tokenization: replaces sensitive data with non-sensitive placeholder (token), and this token can be used as a functional placeholder for the original data. Tokenization is commonly used with credit card processing and mobile devices using NFC.
- Roles and responsibilities:
  - Data owner: usually a higher level executive that makes business decisions regarding the data.
  - Data controller: responsible for purposes and means by which the data is processed. Eg. Payroll department.
  - Data processor: manages operational use of data. Eg. Payroll company.
  - Data custodian/steward: manages access rights and sets security controls to data.
  - Data protection/privacy officer (DPO): sets privacy policies and implements privacy processes.

## Reference

- [Professor Messer’s SY0-601 CompTIA Security+ Course](https://www.professormesser.com/)
