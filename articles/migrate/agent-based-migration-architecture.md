---
title: Migratie architectuur op basis van agents in Azure Migrate server migratie
description: Biedt een overzicht van de virtuele machine migratie op basis van een agent met Azure Migrate server migratie.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: f5ad3aa0fc51f47942750d3745ffef1d6e4a087d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232580"
---
# <a name="agent-based-migration-architecture"></a>Migratiearchitectuur op basis van een agent

Dit artikel bevat een overzicht van de architectuur en processen die worden gebruikt voor replicatie op basis van een agent met het hulp programma voor migratie van Azure Migrate-server.

[Azure migrate](migrate-services-overview.md) biedt een centrale hub voor het bijhouden van de detectie, beoordeling en migratie van uw on-premises apps en werk belastingen en AWS/GCP VM-instanties naar Azure. De hub biedt Azure Migrate-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV'S-aanbiedingen (Independent Software Vendor) van derden.

## <a name="agent-based-replication"></a>Replicatie op basis van een agent

Replicatie op basis van agents in het hulp programma Azure Migrate Server-replicatie wordt gebruikt om on-premises virtuele VMware-machines en fysieke servers naar Azure te migreren. Het kan ook worden gebruikt voor het migreren van andere on-premises gevirtualiseerde servers, evenals persoonlijke en open bare Cloud-Vm's, waaronder AWS-instanties en virtuele GCP-machines.

Het hulp programma voor migratie van Azure Migrate server biedt een aantal opties voor VMware-migratie:

- Migratie met behulp van replicatie op basis van een agent, zoals beschreven in dit artikel.
- Replicatie zonder agent, voor het migreren van Vm's, zodat er niets hoeft te worden geïnstalleerd.

Meer informatie over [het selecteren van een migratie methode voor VMware](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Server migratie en Azure Site Recovery

Azure Migrate server migratie is een hulp programma voor het migreren van on-premises en open bare Cloud werkbelastingen naar Azure. Deze is geoptimaliseerd voor migratie. Site Recovery is een hulp programma voor nood herstel. Migratie van Azure server en Site Recovery delen enkele veelvoorkomende technologie onderdelen die worden gebruikt voor gegevens replicatie, maar hebben verschillende doel einden.

## <a name="architectural-components"></a>Architectuuronderdelen

![Architectuur](./media/agent-based-replication-architecture/architecture.png)

De tabel bevat een overzicht van de onderdelen die worden gebruikt voor migratie op basis van een agent.

**Onderdeel** | **Details** | **Installatie**
--- | --- | ---
**Replicatie apparaat** | Het replicatie apparaat (configuratie server) is een on-premises computer die fungeert als een brug tussen de on-premises omgeving en het hulp programma voor migratie van Azure Migrate-server. Het apparaat detecteert de on-premises VM-inventaris, zodat de migratie van Azure Server replicatie en migratie kan organiseren. Het apparaat heeft twee onderdelen:<br/><br/> **Configuratie server**: Maakt verbinding met Azure Migrate server migratie en coördineert replicatie.<br/> **Processerver**: Hiermee wordt de gegevens replicatie verwerkt. Er worden VM-gegevens ontvangen, gecomprimeerd en versleuteld en verzonden naar het Azure-abonnement. Daar schrijft de server migratie de gegevens naar Managed disks. | Standaard wordt de proces server samen met de configuratie server op het replicatie apparaat geïnstalleerd.
**Mobility-service** | De Mobility-service is een agent die is geïnstalleerd op elke computer die u wilt repliceren en migreren. Het verzendt replicatie gegevens van de machine naar de proces server. Er zijn een aantal verschillende agents voor Mobility-services beschikbaar. | Installatie bestanden voor de Mobility-service bevinden zich op het replicatie-apparaat. U downloadt en installeert de agent die u nodig hebt, in overeenstemming met het besturings systeem en de versie van de computer die u wilt repliceren.

### <a name="mobility-service-installation"></a>Installatie van de Mobility-service

U kunt de Mobility-service implementeren met behulp van de volgende methoden:

- **Push-installatie**: De Mobility-service wordt geïnstalleerd door de proces server wanneer u de beveiliging voor een machine inschakelt. 
- **Hand matig installeren**: U kunt de Mobility-service hand matig op elke computer installeren via de gebruikers interface of de opdracht prompt.

De Mobility-service communiceert met het replicatie apparaat en de gerepliceerde machines. Als u antivirus software hebt uitgevoerd op het replicatie apparaat, de proces servers of de computers die worden gerepliceerd, moeten de volgende mappen worden uitgesloten van scannen:


- C:\Program Files\Microsoft Azure Recovery Services-agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (op Windows-computers waarop de Mobility-service is geïnstalleerd)

## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie voor een virtuele machine inschakelt, begint de eerste replicatie naar Azure.
2. Tijdens de eerste replicatie leest de Mobility-service gegevens van de machine schijven en verzendt deze naar de proces server.
3. Deze gegevens worden gebruikt voor het seeden van een kopie van de schijf in uw Azure-abonnement. 
4. Wanneer de initiële replicatie is voltooid, begint de replicatie van Delta wijzigingen naar Azure. Replicatie is op blok niveau en bijna doorlopend.
4. De Mobility-service onderschept de schrijf bewerkingen naar het schijf geheugen van de virtuele machine door te integreren met het opslag subsysteem van het besturings systeem. Deze methode voor komt dat I/O-bewerkingen op de replicerende computer worden uitgevoerd voor incrementele replicatie. 
5. Bijgehouden wijzigingen voor een machine worden verzonden naar de proces server op poort HTTPS 9443 binnenkomend. Deze poort kan worden gewijzigd. De proces server comprimeert en versleutelt deze en verzendt deze naar Azure. 

## <a name="ports"></a>Poorten

**Apparaatconfiguratie** | **verbinding**
--- | --- 
VM's | De Mobility-service die wordt uitgevoerd op Vm's communiceert met het on-premises replicatie apparaat op poort HTTPS 443 inkomend voor replicatie beheer.<br/><br/> Vm's verzenden de replicatie gegevens naar de proces server (standaard actief op het replicatie apparaat) op poort HTTPS 9443 binnenkomend. Deze poort kan worden gewijzigd.
Replicatie apparaat | Het replicatie apparaat organiseert de replicatie met Azure via poort HTTPS 443 uitgaand.
Processerver | De proces server ontvangt replicatie gegevens, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage via poort 443 uitgaand.


## <a name="performance-and-scaling"></a>Prestaties en schalen

Standaard implementeert u één replicatie apparaat waarop zowel de configuratie server als de proces server worden uitgevoerd. Als u slechts enkele computers repliceert, is deze implementatie voldoende. Als u echter honderden computers repliceert en migreert, kan één proces server mogelijk niet alle replicatie verkeer afhandelen. In dit geval kunt u extra, scale-out proces servers implementeren.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Site Recovery Deployment Planner voor VMware

Als u virtuele VMware-machines repliceert, kunt u de [Site Recovery Deployment planner](../site-recovery/site-recovery-deployment-planner.md) voor VMware gebruiken om te helpen bij het bepalen van de prestatie vereisten, inclusief de dagelijkse wijzigings snelheid van gegevens en de proces servers die u nodig hebt.

### <a name="replication-appliance-capacity"></a>Capaciteit van replicatie apparaat

De waarden in deze tabel kunnen worden gebruikt om erachter te komen of u een extra proces server nodig hebt in uw implementatie.

- Als uw dagelijkse wijzigings frequentie (verloop frequentie) groter is dan 2 TB, implementeert u een extra proces server.
- Als u meer dan 200 computers repliceert, implementeert u een extra replicatie apparaat.

**CPU** | **Geheugenmetabase** | **Vrije ruimte voor het opslaan van gegevens in de cache** | **Verloop frequentie** | **Replicatie limieten**
--- | --- | --- | --- | ---
8 vcpu's (2 sockets * 4 kernen \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB of minder | < 100-machines 
12 vcpu's (2 sockets * 6 kernen \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB tot 1 TB | 100-150 machines.
16 vcpu's (2 sockets * 8 kernen \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB tot 2 TB | 151-200 machines.

### <a name="scale-out-process-server-sizing"></a>Grootte van scale-out proces server

Als u een scale-out proces server moet implementeren, kan deze tabel u helpen de server grootte te bepalen.

**Processerver** | **Vrije ruimte voor het opslaan van gegevens in de cache** | **Verloop frequentie** | **Replicatie limieten**
--- | --- | --- | --- 
4 vcpu's (2 sockets * 2 kernen \@ 2,5 GHz), 8 GB geheugen | 300 GB | 250 GB of minder | Maxi maal 85 computers 
8 vcpu's (2 sockets * 4 kernen \@ 2,5 GHz), 12 GB geheugen | 600 GB | 251 GB tot 1 TB    | 86-150 machines.
12 vcpu's (2 sockets * 6 kernen \@ 2,5 GHz), 24 GB geheugen | 1 TB | 1-2 TB | 151-225 machines.

## <a name="control-upload-throughput"></a>Upload doorvoer beheren


 VMware-verkeer dat wordt gerepliceerd naar Azure, loopt via een specifieke proces server. U kunt de door Voer van een upload beperken door de band breedte op de computers die worden uitgevoerd als proces servers te beperken. U kunt de band breedte beïnvloeden met behulp van de volgende register sleutel:

- Met de waarde voor HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM-REGI ster wordt het aantal threads aangegeven dat wordt gebruikt voor gegevens overdracht (initiële of Delta replicatie) van een schijf. Een hogere waarde verhoogt de netwerk bandbreedte die wordt gebruikt voor replicatie. De standaard waarde is vier. De maximum waarde is 32. Houd het verkeer in de gaten om de waarde te optimaliseren.
- Daarnaast kunt u de band breedte op de proces Server computer als volgt beperken:

    1. Open de Azure Backup MMC-module op de computer met de proces server. Er is een snelkoppeling op het bureau blad of in de map C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. Selecteer in de module **Eigenschappen wijzigen**.
    3. Selecteer in **beperking**de optie **beperking van Internet bandbreedte gebruik inschakelen voor back-** upbewerkingen. Stel de limieten voor werk-en niet-werk uren in. Geldige bereiken zijn van 512 Kbps tot 1.023 Mbps.


## <a name="next-steps"></a>Volgende stappen

Probeer de migratie van een [VMware-VM](tutorial-migrate-vmware-agent.md) op basis van een agent uit te proberen met Azure migrate server migratie.
