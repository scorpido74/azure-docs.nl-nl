---
title: Hoe werkt Hyper-V-migratie in Azure Migreren?
description: Meer informatie over Hyper-V-migratie met Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185879"
---
# <a name="how-does-hyper-v-replication-work"></a>Hoe werkt Hyper-V-replicatie?

In dit artikel vindt u een overzicht van de architectuur en processen die worden gebruikt wanneer u Hyper-V VM's migreert met het hulpprogramma Voor migratie van Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) biedt een centrale hub om detectie, beoordeling en migratie van uw on-premises apps en workloads en private/public cloud VM's naar Azure bij te houden. De hub biedt Azure Migrate-tools voor beoordeling en migratie, evenals isv-aanbiedingen (onafhankelijke softwareleveranciers) van derden.

## <a name="agentless-migration"></a>Agentloze migratie

Het hulpprogramma Azure Migrate Server Migration biedt agentloze replicatie voor on-premises Hyper-V VM's, met behulp van een migratieworkflow die is geoptimaliseerd voor Hyper-V. U installeert een softwareagent alleen op Hyper-V-hosts of clusterknooppunten. Er hoeft niets te worden geïnstalleerd op Hyper-V VM's.

## <a name="server-migration-and-azure-site-recovery"></a>Servermigratie en Azure-siteherstel

Azure Migrate Server Migration is een hulpmiddel voor het migreren van on-premises workloads en vm's in de cloud naar Azure. Site Recovery is een hulpmiddel voor noodherstel. De hulpprogramma's delen een aantal algemene technologiecomponenten die worden gebruikt voor gegevensreplicatie, maar dienen verschillende doeleinden. 


## <a name="architectural-components"></a>Architectuuronderdelen

![Architectuur](./media/hyper-v-replication-architecture/architecture.png)



**Component** | **Implementatie** | 
--- | --- 
**Replicatieprovider** | De Microsoft Azure Site Recovery-provider is geïnstalleerd op Hyper-V-hosts en is geregistreerd bij Azure Migration Server Migration.<br/> De provider orkestreert replicatie voor Hyper-V VM's.
**Recovery Services-agent** | De Microsoft Azure Recovery Service-agent verwerkt gegevensreplicatie. Het werkt samen met de provider om gegevens van Hyper-V VM's naar Azure te repliceren.<br/> De gerepliceerde gegevens worden geüpload naar een opslagaccount in uw Azure-abonnement. Met het hulpprogramma Servermigratie verwerkt u de gerepliceerde gegevens en past deze toe op replicaschijven in het abonnement. De replicaschijven worden gebruikt om de Azure VM's te maken wanneer u migreert.

- Onderdelen worden geïnstalleerd door één installatiebestand, gedownload van Azure Migrate Server Migration in de portal.
- De provider en het toestel gebruiken uitgaande HTTPS-poort 443-verbindingen om te communiceren met Azure Migrate Server Migration.
- Communicatie van de provider en agent is veilig en versleuteld.


## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie inschakelt voor een Hyper-V VM, begint de eerste replicatie.
2. Er wordt een Hyper-V VM-momentopname gemaakt.
3. VHD's op de VM worden één voor één gerepliceerd, totdat ze allemaal naar Azure worden gekopieerd. De initiële replicatietijd is afhankelijk van de VM-grootte en de netwerkbandbreedte.
4. Schijfwijzigingen die optreden tijdens de eerste replicatie worden bijgehouden met Hyper-V Replica en opgeslagen in logboekbestanden (hrl-bestanden).
    - Logbestanden bevinden zich in dezelfde map als de schijven.
    - Elke schijf heeft een bijbehorend hrl-bestand dat naar secundaire opslag wordt verzonden.
    - De momentopname- en logboekbestanden nemen schijfbronnen in beslag terwijl de eerste replicatie wordt uitgevoerd.
4. Nadat de eerste replicatie is voltooid, wordt de VM-momentopname verwijderd en wordt deltareplicatie gestart.
5. Incrementele schijfwijzigingen worden bijgehouden in hrl-bestanden. Replicatielogboeken worden periodiek geüpload naar een Azure-opslagaccount door de medewerker Herstelservices.


## <a name="performance-and-scaling"></a>Prestaties en schalen

Replicatieprestaties voor Hyper-V worden beïnvloed door factoren zoals de VM-grootte, de gegevenswijzigingssnelheid (churn) van de VM's, beschikbare ruimte op de Hyper-V-host voor opslag van logboekbestanden, uploadbandbreedte voor replicatiegegevens en doelopslag in Azure.

- Als u meerdere machines tegelijk repliceert, gebruikt u de [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) voor Hyper-V om replicatie te optimaliseren.
- Plan uw Hyper-V-replicatie en distribueer replicatie over Azure-opslagaccounts, in overeenstemming met de capaciteit.

### <a name="control-upload-throughput"></a>Uploaddoorvoer beheren

U de hoeveelheid bandbreedte beperken die wordt gebruikt om gegevens naar Azure te uploaden op elke Hyper-V-host. Wees voorzichtig. Als u de waarden te laag instelt, heeft dit een negatieve invloed op replicatie en vertraagt dit de migratie.


1. Meld u aan bij de Hyper-V-host of het clusterknooppunt.
2. Voer **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**uit om de Module Windows Azure Backup MMC te openen.
3. Selecteer Eigenschappen **wijzigen**in de module .
4. Selecteer in **Beperking**de optie **Beperking van internetbandbreedtegebruik inschakelen voor back-upbewerkingen**. Stel de limieten in voor werk- en niet-werkuren. Geldige bereiken zijn 512 Kbps tot 1.023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Beïnvloed de uploadefficiëntie

Als u extra bandbreedte hebt voor replicatie en het aantal uploads wilt verhogen, u het aantal threads dat voor de uploadtaak is toegewezen, als volgt verhogen:

1. Open het register met Regedit.
2. Navigeer naar de belangrijkste HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Verhoog de waarde voor het aantal threads dat wordt gebruikt voor het uploaden van gegevens voor elke replicerende VM. De standaardwaarde is 4 en de maximale waarde 32. 




## <a name="next-steps"></a>Volgende stappen

Probeer [Hyper-V-migratie](tutorial-migrate-hyper-v.md) uit met Azure Migrate Server Migration.
