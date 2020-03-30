---
title: Op agent gebaseerde migratie in Azure Migrate Server-migratie
description: Biedt een overzicht van vmware-vm-migratie op basis van agent in Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425851"
---
# <a name="agent-based-migration-architecture"></a>Migratiearchitectuur op basis van een agent

In dit artikel vindt u een overzicht van de architectuur en processen die worden gebruikt voor op agentgebaseerde replicatie van VMware VM's met het hulpprogramma [Azure Migrate: Server Migration.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

Met Azure Migrate: Servermigratie u VMware VM's repliceren met een paar opties:

- Vm's migreren met behulp van op agent gebaseerde replicatie, zoals beschreven in dit artikel.
- VMware VM's migreren met behulp van agentless replicatie. Dit migreert VM's zonder dat er iets op hoeft te worden geïnstalleerd.

Meer informatie over [het selecteren en vergelijken van](server-migrate-overview.md) migratiemethoden voor VMware-VM's. 


## <a name="agent-based-migration"></a>Op agentgebaseerde migratie

Agent-gebaseerde migratie wordt gebruikt om on-premises VMware VM's en fysieke servers naar Azure te migreren. Het kan ook worden gebruikt om andere on-premises gevirtualiseerde servers te migreren, evenals private en public cloud VM's, waaronder AWS-exemplaren en GCP-VM's. Agent-gebaseerde migratie in Azure Migrate maakt gebruik van een backend-functionaliteit van de [Azure Site Recovery-service.](../site-recovery/site-recovery-overview.md)


## <a name="architectural-components"></a>Architectuuronderdelen

Het diagram illustreert de componenten die betrokken zijn bij agentmatige migratie.

![Architectuur](./media/agent-based-replication-architecture/architecture.png)

De tabel geeft een overzicht van de componenten die worden gebruikt voor op agentgebaseerde migratie.

**Component** | **Details** | **Installeren**
--- | --- | ---
**Replicatietoestel** | Het replicatietoestel (configuratieserver/processerver) is een on-premises machine die fungeert als een brug tussen de on-premises omgeving en servermigratie. Het toestel detecteert de on-premises machinevoorraad, zodat servermigratie replicatie en migratie kan orkestreren. Het apparaat bestaat uit twee componenten:<br/><br/> **Configuratieserver:** maakt verbinding met servermigratie en coördineert replicatie.<br/> **Processerver:** verwerkt gegevensreplicatie. De processerver ontvangt machinegegevens, comprimeert en versleutelt deze en verzendt naar Azure. In Azure schrijft Servermigratie de gegevens naar beheerde schijven. | Standaard wordt de processerver samen met de configuratieserver op het replicatietoestel geïnstalleerd.
**Mobiliteitsdienst** | De Mobility-service is een agent die is geïnstalleerd op elke machine die u wilt repliceren en migreren. Het stuurt replicatiegegevens van de machine naar de processerver. | Installatiebestanden voor verschillende versies van de Mobiliteitsservice bevinden zich op het replicatietoestel. U downloadt en installeert de agent die u nodig hebt, in overeenstemming met het besturingssysteem en de versie van de machine die u wilt repliceren.

## <a name="mobility-service-installation"></a>Installatie van de Mobility-service

U de Mobiliteitsservice implementeren met de volgende methoden:

- **Push-installatie**: De Mobility-service wordt door de processerver geïnstalleerd wanneer u een machine inschakelt. 
- **Handmatig installeren:** u de Mobiliteitsservice handmatig installeren op elke machine via de gebruikersinterface of opdrachtprompt.

De Mobiliteitsservice communiceert met het replicatieapparaat en de gerepliceerde machines. Als antivirussoftware wordt uitgevoerd op het replicatietoestel, processervers of machines die worden gerepliceerd, moeten de volgende mappen worden uitgesloten van scannen:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (op Windows-machines met de Mobiliteitsservice geïnstalleerd)

## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie voor een machine inschakelt, begint de eerste replicatie naar Azure.
2. Tijdens de eerste replicatie leest de Mobiliteitsservice gegevens van de machineschijven en stuurt deze naar de processerver.
3. Deze gegevens worden gebruikt om een kopie van de schijf in uw Azure-abonnement te zaaien. 
4. Nadat de eerste replicatie is voltooid, wordt de replicatie van deltawijzigingen in Azure gestart. Replicatie is blokniveau en bijna continu.
4. De dienst van de Mobiliteit onderschept schrijft aan schijfgeheugen, door met het opslagsubsysteem van het werkende systeem te integreren. Met deze methode worden schijf-I/O-bewerkingen op de replicerende machine vermeden voor incrementele replicatie. 
5. Bijgehouden wijzigingen voor een machine worden verzonden naar de processerver op inkomende poort HTTPS 9443. Deze poort kan worden gewijzigd. De processerver comprimeert en versleutelt deze en stuurt deze naar Azure. 

## <a name="ports"></a>Poorten

**Apparaat** | **Verbinding**
--- | --- 
**Replicerende machines** | De Mobiliteitsservice die op VM's wordt uitgevoerd, communiceert met het on-premises replicatietoestel op poort HTTPS 443 inbound, voor replicatiebeheer.<br/><br/> Machines verzenden replicatiegegevens naar de processerver op poort HTTPS 9443 binnenkomend. Deze poort kan worden gewijzigd.
**Replicatietoestel** | Het replicatietoestel orkestreert replicatie met Azure via poort HTTPS 443 outbound.
**Processerver** | De processerver ontvangt replicatiegegevens, optimaliseert en versleutelt deze en stuurt deze naar Azure-opslag via poort 443-uitgaand.


## <a name="performance-and-scaling"></a>Prestaties en schalen

Standaard implementeert u één replicatietoestel dat zowel de configuratieserver als de processerver uitvoert. Als u slechts een paar machines repliceert, is deze implementatie voldoende. Als u echter honderden machines repliceert en migreert, kan één processerver mogelijk niet al het replicatieverkeer verwerken. In dit geval u extra, scale-out processervers implementeren.

### <a name="plan-vmware-deployment"></a>VMware-implementatie plannen

Als u Vm's van VMware repliceert, u de [Site Recovery Deployment Planner voor VMware](../site-recovery/site-recovery-deployment-planner.md)gebruiken om de prestatievereisten te bepalen, inclusief de dagelijkse gegevenswijzigingssnelheid en de processervers die u nodig hebt.

### <a name="replication-appliance-capacity"></a>Capaciteit van replicatietoestel

Gebruik de waarden in deze tabel om erachter te komen of u een extra processerver nodig hebt bij uw implementatie.

- Als uw dagelijkse wijzigingssnelheid (churn rate) meer dan 2 TB bedraagt, implementeert u een extra processerver.
- Als u meer dan 200 machines repliceert, implementeert u een extra replicatietoestel.

**Cpu** | **Geheugen** | **Caching voor vrije ruimtegegevens** | **Churn rate** | **Replicatielimieten**
--- | --- | --- | --- | ---
8 vCPU's (2 sockets * 4 cores \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB of minder | < 100 machines 
12 vCPU's (2 sockets * 6 cores \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB tot 1 TB | 100-150 machines.
16 vCPU's (2 sockets * 8 cores \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB tot 2 TB | 151-200 machines.

### <a name="sizing-scale-out-process-servers"></a>Schaalgrootte van processervers

Als u een scale-outprocesserver moet implementeren, gebruikt u deze tabel om de grootte van de server te achterhalen.

**Processerver** | **Vrije ruimte voor het caching van gegevens** | **Churn rate** | **Replicatielimieten**
--- | --- | --- | --- 
4 vCPU's (2 sockets * 2 cores \@ 2,5 GHz), 8 GB geheugen | 300 GB | 250 GB of minder | Maximaal 85 machines 
8 vCPU's (2 sockets * 4 cores \@ 2,5 GHz), 12 GB geheugen | 600 GB | 251 GB tot 1 TB    | 86-150 machines.
12 vCPU's (2 sockets * 6 cores \@ 2,5 GHz), 24 GB geheugen | 1 TB | 1-2 TB | 151-225 machines.

## <a name="throttle-upload-bandwidth"></a>Throttle uploadbandbreedte.

VMware-verkeer dat wordt gerepliceerd naar Azure gaat via een specifieke processerver. U de uploaddoorvoer beperken door bandbreedte te beperken op de machines die als processervers worden uitgevoerd. U bandbreedte beïnvloeden met behulp van deze registersleutel:

- De HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM-registerwaarde geeft het aantal threads op dat wordt gebruikt voor gegevensoverdracht (initiële of deltareplicatie) van een schijf. Een hogere waarde verhoogt de netwerkbandbreedte die wordt gebruikt voor replicatie. De standaardwaarde is vier. De maximale waarde is 32. Houd het verkeer in de gaten om de waarde te optimaliseren.
- Bovendien u de bandbreedte op de processervermachine als volgt beperken:

    1. Open de module Azure Backup MMC op de processerver. Er is een snelkoppeling op het bureaublad of in de map C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. Selecteer Eigenschappen **wijzigen**in de module .
    3. Selecteer in **Beperking**de optie **Beperking van internetbandbreedtegebruik inschakelen voor back-upbewerkingen**. Stel de limieten in voor werk- en niet-werkuren. Geldige bereiken zijn 512 Kbps tot 1.023 Mbps.


## <a name="next-steps"></a>Volgende stappen

Probeer [op agentgebaseerde migratie](tutorial-migrate-vmware-agent.md) voor [VMware](tutorial-migrate-vmware-agent.md) of [fysieke servers](tutorial-migrate-physical-virtual-machines.md)uit.
