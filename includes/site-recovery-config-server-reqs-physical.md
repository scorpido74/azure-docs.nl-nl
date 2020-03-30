---
title: bestand opnemen
description: bestand opnemen
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: afeae4af9b41bf434b26833a3bd927118a4697ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176261"
---
**Configuratie-/processerververeisten voor fysieke serverreplicatie**

**Component** | **Vereiste** 
--- | ---
**HARDWARE-INSTELLINGEN** | 
CPU-kernen | 8 
RAM | 16 GB
Aantal schijven | 3, inclusief de osschijf, de servercacheschijf en het bewaarstation voor failback 
Vrije schijfruimte (processervercache) | 600 GB
Vrije schijfruimte (bewaarschijf) | 600 GB
 | 
**SOFTWARE-INSTELLINGEN** | 
Besturingssysteem | Windows Server 2012 R2 <br> Windows Server 2016
Landinstelling van het besturingssysteem | Engels (en-us)
Windows Server-functies | Schakel deze rollen niet in: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Groepsbeleid | Schakel dit groepsbeleid niet in: <br> - Voorkom toegang tot de opdrachtprompt. <br> - Voorkom toegang tot registerbewerkingstools. <br> - Logica vertrouwen voor bestandsbijlagen. <br> - Scriptuitvoering inschakelen. <br> [Meer informatie](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Geen reeds bestaande standaardwebsite <br> - Geen reeds bestaande website/applicatie luisteren op poort 443 <br>- [Anonieme verificatie](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) inschakelen <br> - Schakel [de instelling van FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) in.
Type IP-adres | Statisch 
| 
**TOEGANGSINSTELLINGEN** | 
Mysql | MySQL moet worden geïnstalleerd op de configuratieserver. U handmatig installeren of Site Recovery kan het installeren tijdens de implementatie. Controleer op het installeren van siteherstel http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msiof de machine kan worden geïnstalleerd .
 URL's | De configuratieserver heeft toegang nodig tot deze URL's (rechtstreeks of via proxy):<br/><br/> Azure AD: `login.microsoftonline.com`; `login.microsoftonline.us`;`*.accesscontrol.windows.net`<br/><br/> Overdracht van `*.backup.windowsazure.com`replicatiegegevens: ;`*.backup.windowsazure.us`<br/><br/> Replicatiebeheer: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`;`*.services.visualstudio.com`<br/><br/> Toegang tot `*.blob.core.windows.net`de opslag: ;`*.blob.core.usgovcloudapi.net`<br/><br/> Tijdsynchronisatie: `time.nist.gov`;`time.windows.com`<br/><br/> Telemetrie (optioneel):`dc.services.visualstudio.com`
Firewall | Firewallregels op basis van IP-adres moeten communicatie met Azure-URL's mogelijk maken. Om de IP-bereiken te vereenvoudigen en te beperken, raden we u aan URL-filtering te gebruiken.<br/><br/>**Voor commerciële IP's:**<br/><br/>- De [IP-bereiken van Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653)en de HTTPS-poort (443) toestaan.<br/><br/> - Sta IP-adresbereiken toe voor de West-VS (gebruikt voor toegangscontrole en identiteitsbeheer).<br/><br/> - Sta IP-adresbereiken toe voor het Azure-gebied van uw abonnement om de URL's te ondersteunen die nodig zijn voor Azure Active Directory, back-up, replicatie en opslag.<br/><br/> **Voor overheids-IP's:**<br/><br/> - Sta de IP-bereiken van Azure Government Datacenter en de HTTPS-poort (443) toe.<br/><br/> - Sta IP-adresbereiken toe voor alle Gov-regio's in de VS (Virginia, Texas, Arizona en Iowa) ter ondersteuning van de URL's die nodig zijn voor Azure Active Directory, back-up, replicatie en opslag.
Poorten | 443 toestaan (Orkestratie van het kanaal van het controlekanaal)<br/><br/> 9443 toestaan (gegevenstransport) 


**Vereisten voor het aanpassen van configuratie-/processervers**

**Cpu** | **Geheugen** | **Cacheschijf** | **Gegevenswijzigingssnelheid** | **Gerepliceerde machines**
--- | --- | --- | --- | ---
8 vCPU's<br/><br/> 2 sockets * \@ 4 cores 2,5 GHz | 16 GB | 300 GB | 500 GB of minder | < 100 machines
12 vCPU's<br/><br/> 2 sokken * \@ 6 cores 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 tot 150 machines
16 vCPU's<br/><br/> 2 sokken * \@ 8 cores 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 machines

