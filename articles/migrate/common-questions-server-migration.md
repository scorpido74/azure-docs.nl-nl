---
title: Veelgestelde vragen over de migratie van Azure Migrate server
description: Krijg antwoorden op veelgestelde vragen over het gebruik van Azure Migrate server migratie om machines te migreren.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 273ef746e685afcf9f3654963dd9c6bd5b855b24
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927521"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migratie van Azure Migrate server: veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Migrate: hulp programma voor server migratie. Als u andere vragen hebt, raadpleegt u deze bronnen:

- [Algemene vragen](resources-faq.md) over Azure migrate
- Vragen over het [Azure migrate apparaat](common-questions-appliance.md)
- Vragen over de [visualisatie van detectie, beoordeling en afhankelijkheid](common-questions-discovery-assessment.md)
- Beantwoorde vragen in het [Azure migrate-forum](https://aka.ms/AzureMigrateForum) ontvangen

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

Azure Migrate maakt gebruik van het protocol voor netwerk blok apparaten (NBD) met SSL-versleuteling.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Wat is de minimale vCenter Server versie die vereist is voor de migratie?

U moet ten minste beschikken over vCenter Server 5,5 en vSphere ESXi host versie 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Kunnen klanten hun Vm's migreren naar niet-beheerde schijven?

Nee. Azure Migrate ondersteunt alleen migratie naar Managed disks (Standard-HDD Premium-SSD).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Hoeveel Vm's kan ik op één keer repliceren met behulp van migratie zonder agent?

Op dit moment kunt u 100 Vm's per exemplaar van vCenter Server tegelijk migreren. Migreer in batches van tien Vm's.
 
## <a name="next-steps"></a>Volgende stappen

Lees het [Azure migrate overzicht](migrate-services-overview.md).
