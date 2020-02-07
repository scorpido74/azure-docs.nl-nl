---
title: Veelgestelde vragen over Azure Migrate server migratie
description: Antwoorden vinden op veelgestelde vragen over Azure Migrate server migratie
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067381"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migratie van Azure Migrate server: veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Migrate: Server migratie. Als u na het lezen van dit artikel nog meer query's hebt uitgevoerd, plaatst u deze op het [Azure migrate forum](https://aka.ms/AzureMigrateForum). Als u andere vragen hebt, raadpleegt u de volgende artikelen:

- [Algemene vragen](resources-faq.md) over Azure Migrate.
- [Vragen](common-questions-appliance.md) over het Azure migrate apparaat.
- [Vragen](common-questions-discovery-assessment.md) over de visualisatie van detectie, beoordeling en afhankelijkheid.


## <a name="how-does-agentless-vmware-replication-work"></a>Hoe werkt VMware-replicatie zonder agent?

De replicatie methode zonder agent voor VMware maakt gebruik van VMware-moment opnamen en het gewijzigde VMware-blok tracering (CBT). Er wordt een initiële replicatie cyclus gepland wanneer de gebruiker de replicatie start. In de eerste replicatie cyclus wordt een moment opname van de virtuele machine gemaakt en wordt deze moment opname gebruikt voor het repliceren van de virtuele machines Vmdk's (schijven). Nadat de eerste replicatie cyclus is voltooid, worden de Delta replicatie cycli periodiek gepland. In de Delta replicatie cyclus wordt een moment opname gemaakt en gegevens blokken die zijn gewijzigd sinds de vorige replicatie cyclus worden gerepliceerd. VMware gewijzigde blok tracering wordt gebruikt om te bepalen welke blokken zijn gewijzigd sinds de laatste cyclus.
De frequentie van de periodieke replicatie cycli wordt automatisch beheerd door de service, afhankelijk van het aantal andere virtuele machines/schijven die gelijktijdig worden gerepliceerd van hetzelfde gegevens archief en de ideale omstandigheden zullen uiteindelijk convergeren op 1 cyclus per uur per VM.

Wanneer u migreert, wordt een replicatie cyclus op aanvraag gepland voor de virtuele machine om eventuele resterende gegevens vast te leggen. U kunt ervoor kiezen om de virtuele machine af te sluiten als onderdeel van de migratie om ervoor te zorgen dat er geen gegevens verloren gaan en de consistentie van de toepassing.

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>Waarom is de optie voor opnieuw synchroniseren niet beschikbaar in stack zonder agents?

In stack zonder agents wordt in elke Delta cyclus het verschil overgedragen tussen de huidige moment opname en de vorige moment opname die we hebben ondernomen. Omdat het altijd een verschil is tussen moment opnamen, biedt dit het voor deel van het vouwen van de gegevens (dat wil zeggen, als een bepaalde sector n keer tussen de moment opnamen is geschreven), hoeft alleen de laatste schrijf overdracht te worden overgedragen, omdat alleen de laatste synchronisatie van belang is. Dit wijkt af van de op agent gebaseerde stack, waarmee we elke schrijf bewerking bijhouden en Toep assen. Dit betekent dat elke Delta cyclus een hersynchronisatie is. Daarom wordt er geen hersynchronisaties optie weer gegeven. 

Als de schijven niet synchroon lopen vanwege een storing, wordt deze in de volgende cyclus opgelost. 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>Wat is de impact van het verloop tempo als ik replicatie zonder agent gebruikt?

Omdat de stack afhankelijk is van de gegevens die worden gevouwen, is het verloop patroon gelijk aan die in deze stack. Een patroon waarin een bestand opnieuw wordt geschreven en dat opnieuw geen veel invloed heeft. Een patroon waarbij elke andere sector wordt geschreven, zal echter een hoog verloop veroorzaken in de volgende cyclus. Omdat we de hoeveelheid gegevens die we overdragen minimaliseert, kunnen we de gegevens zo veel mogelijk vouwen voordat de volgende cyclus wordt gepland.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Hoe vaak is een replicatie cyclus gepland?

De formule voor het plannen van de volgende replicatie cyclus is: (vorige cyclus tijd/2) of 1 uur, afhankelijk van wat er hoger is. Als een VM bijvoorbeeld vier uur heeft geduurd voor een Delta cyclus, plannen we de volgende cyclus in twee uur en niet in het volgende uur. Dit wijkt af van het moment dat de cyclus direct na IR ligt, waarbij de eerste Delta cyclus direct wordt gepland.

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>Wat is de invloed op de prestaties van vCenter Server-of ESXi-host tijdens het gebruik van replicatie zonder agent?

Aangezien replicatie zonder agent gebruik maakt van moment opnamen, worden er IOPs-verbruikt voor opslag en hebben klanten een aantal IOPs-ruimte nodig voor de opslag. Het wordt niet aanbevolen deze stack te gebruiken voor beperkte opslag/IOPs-omgeving.

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>Biedt migratie stack zonder agent migratie van UEFI-Vm's naar virtuele machines van Azure generatie 2?

Nee, u moet Azure Site Recovery gebruiken om deze Vm's te migreren naar Gen 2 Azure Vm's. 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>Kan ik mijn Vm's vastmaken aan Azure-beschikbaarheidszones wanneer ik migreer?

Nee, ondersteuning voor Azure-beschikbaarheidszones is niet aanwezig.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Welk transport protocol wordt door Azure Migrate gebruikt tijdens de replicatie?

Azure Migrate gebruikt een protocol voor netwerk blok apparaten (NBD) met SSL-versleuteling.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Wat is de minimale vCenter Server versie die vereist is voor de migratie?

U moet ten minste vCenter Server 5,5 en VMware vSphere ESXi host versie 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Kunnen klanten hun Vm's migreren naar niet-beheerde schijven?

Nee. Azure Migrate ondersteunt alleen migratie naar Managed disks (standaard HDD, Premium SSD).

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>Hoeveel Vm's kunnen gelijktijdig worden gerepliceerd met VMware-stack zonder agent?

Op dit moment kunnen klanten 100 Vm's per vCenter Server tegelijk migreren. Dit kan worden gedaan in batches van tien Vm's.




