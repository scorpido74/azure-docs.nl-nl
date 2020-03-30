---
title: bestand opnemen
description: bestand opnemen
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 1aaec104e9130eeef723c6505e04e3317271566b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234188"
---
**Vereisten voor configuratie- en processervers**


## <a name="hardware-requirements"></a>Hardwarevereisten

**Component** | **Vereiste** 
--- | ---
CPU-kernen | 8 
RAM | 16 GB
Aantal schijven | 3, inclusief de osschijf, de servercacheschijf en het bewaarstation voor failback 
Vrije schijfruimte (processervercache) | 600 GB
Vrije schijfruimte (bewaarschijf) | 600 GB
 | 

## <a name="software-requirements"></a>Softwarevereisten

**Component** | **Vereiste** 
--- | ---
Besturingssysteem | Windows Server 2012 R2 <br> Windows Server 2016
Landinstelling van het besturingssysteem | Engels (en-*)
Windows Server-functies | Schakel deze rollen niet in: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Groepsbeleid | Schakel dit groepsbeleid niet in: <br> - Voorkom toegang tot de opdrachtprompt. <br> - Voorkom toegang tot registerbewerkingstools. <br> - Logica vertrouwen voor bestandsbijlagen. <br> - Scriptuitvoering inschakelen. <br> [Meer informatie](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Geen reeds bestaande standaardwebsite <br> - Geen reeds bestaande website/applicatie luisteren op poort 443 <br>- [Anonieme verificatie](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) inschakelen <br> - [FastCGI-instelling](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inschakelen 
FIPS (Federal Information Processing Standards) | Fips-modus niet inschakelen
|

## <a name="network-requirements"></a>Netwerkvereisten

**Component** | **Vereiste** 
--- | --- 
Type IP-adres | Statisch 
Poorten | 443 (Orchestration-besturingselement)<br>9443 (Gegevenstransport) 
NIC-type | VMXNET3 (als de configuratieserver een VMware VM is)
 |
**Internettoegang** (de server heeft toegang nodig tot de volgende URL's, rechtstreeks of via proxy):|
\*.backup.windowsazure.com | Gebruikt voor gerepliceerde gegevensoverdracht en -coördinatie
\*.store.core.windows.net | Gebruikt voor gerepliceerde gegevensoverdracht en -coördinatie
\*.blob.core.windows.net | Wordt gebruikt om toegang te krijgen tot opslagaccount dat gerepliceerde gegevens opslaat
\*.hypervrecoverymanager.windowsazure.com | Wordt gebruikt voor replicatiebeheerbewerkingen en -coördinatie
https:\//management.azure.com | Wordt gebruikt voor replicatiebeheerbewerkingen en -coördinatie 
*.services.visualstudio.com | Gebruikt voor telemetriedoeleinden (optioneel)
time.nist.gov | Wordt gebruikt om tijdsynchronisatie tussen systeem- en globale tijd te controleren
time.windows.com | Wordt gebruikt om tijdsynchronisatie tussen systeem- en globale tijd te controleren
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF-instellingen hebben toegang nodig tot deze URL's. Ze worden gebruikt voor toegangscontrole en identiteitsbeheer door Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Om MySQL-download te voltooien. </br> In een paar regio's kan de download worden doorgestuurd naar de CDN-URL. Zorg ervoor dat de CDN-URL ook op de witte lijst staat, indien nodig.
|

## <a name="required-software"></a>Vereiste software

**Component** | **Vereiste** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI-versie 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) moet worden geïnstalleerd als de configuratieserver op een Vm van VMware wordt uitgevoerd.
Mysql | MySQL moet worden geïnstalleerd. U handmatig installeren of Site Recovery kan het installeren. (Raadpleeg [instellingen configureren](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) voor meer informatie)
|

## <a name="sizing-and-capacity-requirements"></a>Dimensionerings- en capaciteitsvereisten

In de volgende tabel worden capaciteitsvereisten voor de configuratieserver samengevat. Als u meerdere Vm's voor VMware repliceert, controleert u de [overwegingen voor capaciteitsplanning](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) en voert u het [hulpprogramma Azure Site Recovery Deployment Planner](../articles/site-recovery/site-recovery-deployment-planner.md)uit.


**Cpu** | **Geheugen** | **Cacheschijf** | **Gegevenswijzigingssnelheid** | **Gerepliceerde machines**
--- | --- | --- | --- | ---
8 vCPU's<br/><br/> 2 sockets * \@ 4 cores 2,5 GHz | 16 GB | 300 GB | 500 GB of minder | < 100 machines
12 vCPU's<br/><br/> 2 sokken * \@ 6 cores 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 tot 150 machines
16 vCPU's<br/><br/> 2 sokken * \@ 8 cores 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 machines
|

