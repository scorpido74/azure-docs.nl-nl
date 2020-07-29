---
title: Veelgestelde vragen over Azure Migrate server migratie
description: Krijg antwoorden op veelgestelde vragen over het gebruik van Azure Migrate server migratie om machines te migreren.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: af40aecaa1614542074cf87ce95eb81492233bdc
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321221"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migratie van Azure Migrate server: veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Migrate: hulp programma voor server migratie. Als u andere vragen hebt, raadpleegt u deze bronnen:

- [Algemene vragen](resources-faq.md) over Azure migrate
- Vragen over het [Azure migrate apparaat](common-questions-appliance.md)
- Vragen over de [visualisatie van detectie, beoordeling en afhankelijkheid](common-questions-discovery-assessment.md)
- Beantwoorde vragen in het [Azure migrate-forum](https://aka.ms/AzureMigrateForum) ontvangen

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Welke geografische gebieden worden ondersteund voor migratie met Azure Migrate?

Bekijk de ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-does-agentless-vmware-replication-work"></a>Hoe werkt VMware-replicatie zonder agent?

De replicatie methode zonder agent voor VMware maakt gebruik van VMware-moment opnamen en VMware changed Block tracking (CBT).

Dit is het proces:

1. Wanneer u de replicatie start, wordt een initiële replicatie cyclus gepland. In de eerste cyclus wordt een moment opname van de virtuele machine gemaakt. De moment opname wordt gebruikt om de Vm's Vmdk's (schijven) te repliceren. 
2. Nadat de eerste replicatie cyclus is voltooid, worden de Delta replicatie cycli periodiek gepland.
    - Tijdens de replicatie van verschillen wordt een moment opname gemaakt en gegevens blokken die zijn gewijzigd sinds de vorige replicatie cyclus worden gerepliceerd.
    - VMware CBT wordt gebruikt om te bepalen welke blokken zijn gewijzigd sinds de laatste cyclus.
    - De frequentie van de periodieke replicatie cycli wordt automatisch beheerd door Azure Migrate en is afhankelijk van het aantal andere Vm's en schijven die gelijktijdig worden gerepliceerd vanuit dezelfde gegevens opslag. In de ideale omstandigheden convergeert replicatie uiteindelijk tot één cyclus per uur voor elke virtuele machine.

Wanneer u migreert, wordt een replicatie cyclus op aanvraag gepland voor de machine om eventuele resterende gegevens vast te leggen. Om ervoor te zorgen dat er geen gegevens verloren gaan en de consistentie van de toepassing is, kunt u ervoor kiezen de machine af te sluiten tijdens de migratie.

## <a name="why-isnt-resynchronization-exposed"></a>Waarom wordt de weer gegeven niet opnieuw gesynchroniseerd?

Tijdens migratie zonder agents wordt in elke Delta cyclus het verschil tussen de huidige moment opname en de eerder gebruikte moment opname geschreven. Het is altijd het verschil tussen moment opnamen, het vouwen van gegevens in. Als een specifieke sector *N* keer wordt geschreven tussen moment opnamen, moet alleen de laatste schrijf bewerking worden overgedragen omdat we alleen geïnteresseerd zijn in de laatste synchronisatie. Het proces wijkt af van de replicatie op basis van een agent, waarbij we elke schrijf bijhouden en Toep assen. In dit proces is elke Delta cyclus een hersynchronisatie. De optie voor opnieuw synchroniseren wordt dus niet weer gegeven. Als de schijven ooit niet worden gesynchroniseerd vanwege een fout, wordt deze in de volgende cyclus opgelost. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Wat is de invloed van het verloop tempo op replicatie zonder agent?

Het *verloop patroon* is belang rijker dan het *verloop tempo*, omdat replicatie van zonder agents in gegevens wordt gevouwen. Wanneer een bestand opnieuw wordt geschreven, heeft de frequentie geen veel invloed meer. Een patroon waarbij elke andere sector wordt geschreven, veroorzaakt echter een hoog verloop in de volgende cyclus. Omdat we de hoeveelheid gegevens die we overdragen minimaliseert, kunnen we de gegevens zo veel mogelijk vouwen voordat we de volgende cyclus plannen.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Hoe vaak is een replicatie cyclus gepland?

De formule voor het plannen van de volgende replicatie cyclus is (vorige cyclus tijd/2) of één uur, afhankelijk van wat hoger is.

Als een virtuele machine bijvoorbeeld vier uur duurt voor een Delta cyclus, wordt de volgende cyclus gepland in twee uur en niet in het volgende uur. Het proces wijkt direct na de initiële replicatie af wanneer de eerste Delta cyclus onmiddellijk wordt gepland.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Hoe heeft replicatie zonder agent invloed op VMware-servers?

Replicatie zonder agent resulteert in een invloed op de prestaties van VMware vCenter Server en VMware ESXi hosts. Omdat replicatie zonder agent gebruik maakt van moment opnamen, verbruikt de service IOPS op opslag, zodat er een aantal IOPS-opslag bandbreedte vereist is. Het wordt niet aangeraden replicatie zonder agent te gebruiken als u beperkingen hebt voor opslag of IOPs in uw omgeving.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Kan ik zonder agent migratie van UEFI-Vm's naar Azure gen 2?

Nee. Gebruik Azure Site Recovery om deze Vm's te migreren naar Gen 2 Azure Vm's. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Kan ik virtuele machines vastmaken aan Azure-beschikbaarheidszones wanneer ik migreer?

Nee. Azure-beschikbaarheidszones worden niet ondersteund voor Azure Migrate migratie.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Welk transport protocol gebruikt Azure Migrate tijdens de replicatie?

Azure Migrate maakt gebruik van het protocol voor netwerk blok apparaten (NBD) met TLS-code ring.

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Hoe worden de gegevens verzonden vanuit een on-premises omgeving naar Azure? Versleuteld vóór verzen ding? 
Het Azure Migrate apparaat in de replicatie zonder agent zorgt ervoor dat gegevens worden gecomprimeerd en versleuteld voordat ze worden geüpload. Gegevens worden verzonden via een beveiligd communicatie kanaal via https en maakt gebruik van TLS 1,2 of hoger. Daarnaast worden uw gegevens automatisch door Azure Storage versleuteld wanneer deze persistent worden gemaakt in de Cloud (versleuteling-at-rest).  

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Wat is de minimale vCenter Server versie die vereist is voor de migratie?

U moet ten minste beschikken over vCenter Server 5,5 en vSphere ESXi host versie 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Kunnen klanten hun Vm's migreren naar niet-beheerde schijven?

Nee. Azure Migrate ondersteunt alleen migratie naar Managed disks (Standard-HDD Premium-SSD).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Hoeveel Vm's kan ik op één keer repliceren met behulp van migratie zonder agent?

Op dit moment kunt u 300 Vm's per exemplaar van vCenter Server tegelijk migreren. Migreer in batches van tien Vm's.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Hoe kan ik beperking van de replicatie in het gebruik van Azure Migrate apparaat voor VMware-replicatie zonder agent?  

U kunt dit beperken met behulp van NetQosPolicy. Bijvoorbeeld:

De AppNamePrefix die in de NetQosPolicy moet worden gebruikt, is "GatewayWindowsService.exe". U kunt een beleid maken op het Azure Migrate apparaat om het replicatie verkeer van het apparaat te beperken door een beleid te maken, bijvoorbeeld:
 
New-NetQosPolicy-name "ThrottleReplication"-AppPathNameMatchCondition GatewayWindowsService.exe "-ThrottleRateActionBitsPerSecond 1MB

## <a name="can-i-migrate-vms-that-are-already-being-replicated-to-azure"></a>Kan ik virtuele machines migreren die al naar Azure worden gerepliceerd? 

Als Vm's al een andere manier worden gerepliceerd naar Azure, kunt u deze machines niet migreren als Vm's met Azure Migrate server migratie. Als tijdelijke oplossing kunt u de Vm's als fysieke servers behandelen en deze migreren in overeenstemming met de [ondersteunde fysieke server migratie](migrate-support-matrix-physical-migration.md).

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Wanneer kan ik machines migreren als fysieke servers?

Machines migreren door ze te behandelen als fysieke servers is handig in een aantal scenario's:

- Wanneer u on-premises fysieke servers migreert.
- Als u virtuele machines migreert die zijn gevirtualiseerd door platforms zoals xen, KVM.
- Als u Hyper-V-of VMware-Vm's wilt migreren, als u om een of andere reden het standaard migratie proces voor [hyper-v](tutorial-migrate-hyper-v.md)of [VMware](server-migrate-overview.md) -migratie niet kunt gebruiken. Als u bijvoorbeeld niet met VMware vCenter werkt en alleen ESXi-hosts gebruikt.
- Vm's die momenteel worden uitgevoerd in persoonlijke Clouds migreren naar Azure
- Als u Vm's wilt migreren die worden uitgevoerd in open bare Clouds, zoals Amazon Web Services (AWS) of Google Cloud Platform (GCP), naar Azure.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>Ik heb twee (of meer) apparaten geïmplementeerd om Vm's in mijn vCenter Server te detecteren. Als ik de Vm's echter probeer te migreren, zie ik alleen Vm's die overeenkomen met een van de apparaten.

Als er meerdere toestellen zijn ingesteld, is het vereist dat er geen overlap is tussen de virtuele machines op de opgegeven vCenter-accounts. Een detectie met een dergelijke overlap ping is een niet-ondersteund scenario.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Heb ik VMware vCenter nodig voor het migreren van virtuele VMware-machines?
Als u [virtuele VMware-machines wilt migreren](server-migrate-overview.md) met behulp van een VMware-agent of een agentloze migratie, moeten ESXi-hosts waarop vm's zich bevinden, worden beheerd door vCenter Server. Als u niet beschikt over vCenter Server, kunt u virtuele VMware-machines migreren door ze te migreren als fysieke servers. [Meer informatie](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Volgende stappen

Lees het [Azure migrate overzicht](migrate-services-overview.md).
