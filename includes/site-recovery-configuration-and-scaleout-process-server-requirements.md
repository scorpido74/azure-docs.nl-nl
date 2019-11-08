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
ms.openlocfilehash: d77269c1e965d5bca1e32b756ef26e2c694e5c81
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747841"
---
**Vereisten voor configuratie en proces server**


## <a name="hardware-requirements"></a>Hardwarevereisten

**Onderdeel** | **Vereiste** 
--- | ---
CPU-kernen | 8 
RAM | 16 GB
Aantal schijven | 3, met inbegrip van de besturingssysteem schijf, de cache schijf van de proces server en het Bewaar station voor failback 
Vrije schijf ruimte (cache van proces server) | 600 GB
Vrije schijf ruimte (Bewaar schijf) | 600 GB
 | 

## <a name="software-requirements"></a>Softwarevereisten

**Onderdeel** | **Vereiste** 
--- | ---
Besturingssysteem | Windows Server 2012 R2 <br> Windows Server 2016
Landinstelling van het besturingssysteem | Engels (en-us)
Windows Server-functies | Deze rollen niet inschakelen: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Groeps beleid | Dit groeps beleid niet inschakelen: <br> -Toegang tot de opdracht prompt voor komen. <br> -Toegang tot register bewerkings Programma's verhinderen. <br> -Logica vertrouwen voor bestands bijlagen. <br> -Schakel uitvoering van script in. <br> [Meer informatie](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Geen vooraf bestaande standaard website. <br> -Geen vooraf bestaande website of toepassing die luistert op poort 443. <br>- [Anonieme verificatie](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx)inschakelen. <br> -De [fastcgi](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -instelling inschakelen.
| 

## <a name="network-requirements"></a>Netwerkvereisten

**Onderdeel** | **Vereiste** 
--- | --- 
Type IP-adres | Statisch 
Poorten | 443 (Orchestration-besturingselement)<br>9443 (Gegevenstransport) 
NIC-type | VMXNET3 (als de configuratie server een VMware-VM is)
 |
**Internet toegang** (de server moet toegang hebben tot de volgende url's, rechtstreeks of via proxy):|
\*.backup.windowsazure.com | Gebruikt voor gerepliceerde gegevens overdracht en coördinatie
\*.store.core.windows.net | Gebruikt voor gerepliceerde gegevens overdracht en coördinatie
\*.blob.core.windows.net | Wordt gebruikt voor toegang tot het opslag account waarin gerepliceerde gegevens worden opgeslagen
\*.hypervrecoverymanager.windowsazure.com | Gebruikt voor replicatie beheer bewerkingen en coördinatie
https:\//management.azure.com | Gebruikt voor replicatie beheer bewerkingen en coördinatie 
*.services.visualstudio.com | Gebruikt voor telemetrie-doel einden (optioneel)
time.nist.gov | Wordt gebruikt om de tijd synchronisatie tussen het systeem en de algemene tijd te controleren
time.windows.com | Wordt gebruikt om de tijd synchronisatie tussen het systeem en de algemene tijd te controleren
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\/-login.live.com </li><li> https:\/-graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\/-www.live.com </li><li> https:\/-www.microsoft.com </li></ul> | OVF Setup heeft toegang tot deze Url's nodig. Ze worden gebruikt voor toegangs beheer en identiteits beheer door Azure Active Directory.
https:\/-dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Om de MySQL-down load te volt ooien. </br> In een paar regio's wordt de down load mogelijk omgeleid naar de CDN-URL. Zorg ervoor dat de CDN-URL ook white list is, indien nodig.
|

## <a name="required-software"></a>Vereiste software

**Onderdeel** | **Vereiste** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI versie 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) moet worden geïnstalleerd als de configuratie server wordt uitgevoerd op een virtuele VMWare-machine.
MySQL | MySQL moet worden geïnstalleerd. U kunt hand matig installeren of Azure Site Recovery het installeren. (Zie [instellingen configureren](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings)voor meer informatie.)

## <a name="sizing-and-capacity-requirements"></a>Grootte-en capaciteits vereisten

De volgende tabel bevat een overzicht van de capaciteits vereisten voor de configuratie server. Als u meerdere virtuele VMware-machines repliceert, raadpleegt u de [overwegingen voor capaciteits planning](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) en voert u het [Azure site Recovery Deployment planner-hulp programma](../articles/site-recovery/site-recovery-deployment-planner.md)uit.


**VERBRUIK** | **Geheugenmetabase** | **Cache schijf** | **Gegevens wijzigings frequentie** | **Gerepliceerde machines**
--- | --- | --- | --- | ---
8 Vcpu's<br/><br/> 2 sockets * 4 kern geheugens \@ 2,5 GHz | 16 GB | 300 GB | 500 GB of minder | < 100 machines
12 Vcpu's<br/><br/> 2 SOCKS * 6 kernen \@ 2,5 GHz | 18 GB | 600 GB | 500 GB tot 1 TB | 100 tot 150 computers
16 Vcpu's<br/><br/> 2 SOCKS * 8 kern geheugens \@ 2,5 GHz | 32 GB | 1 TB | 1 tot 2 TB | 150 tot 200 computers

