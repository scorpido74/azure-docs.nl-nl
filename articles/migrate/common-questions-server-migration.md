---
title: Veelgestelde vragen over Azure Migrate server migratie
description: Antwoorden vinden op veelgestelde vragen over het migreren van computers met Azure Migrate server migratie
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425830"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migratie van Azure Migrate server: veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Migrate: hulp programma voor server migratie. Als u meer vragen hebt na het lezen van dit artikel, raadpleegt u de volgende artikelen:

- [Algemene vragen](resources-faq.md) over Azure Migrate.
- [Vragen](common-questions-appliance.md) over het Azure migrate apparaat.
- [Vragen](common-questions-discovery-assessment.md) over de visualisatie van detectie, beoordeling en afhankelijkheid.
- Vraag het Azure Migrate- [forum](https://aka.ms/AzureMigrateForum)om vragen te plaatsen.


## <a name="how-does-agentless-vmware-replication-work"></a>Hoe werkt VMware-replicatie zonder agent?

De replicatie methode zonder agent voor VMware maakt gebruik van VMware-moment opnamen en het gewijzigde VMware-blok tracering (CBT).

1. Wanneer u de replicatie start, wordt een initiële replicatie cyclus gepland. In de eerste cyclus wordt een moment opname van de virtuele machine gemaakt. Deze moment opname wordt gebruikt om de Vm's Vmdk's (schijven) te repliceren. 
2. Nadat de eerste replicatie cyclus is voltooid, worden de Delta replicatie cycli periodiek gepland.
    - Tijdens de replicatie van verschillen wordt een moment opname gemaakt en gegevens blokken die zijn gewijzigd sinds de vorige replicatie cyclus worden gerepliceerd.
    - VMware CBT wordt gebruikt om te bepalen welke blokken zijn gewijzigd sinds de laatste cyclus.
    - De frequentie van de periodieke replicatie cycli wordt automatisch beheerd door de Azure Migrate, afhankelijk van het aantal andere Vm's/schijven die gelijktijdig worden gerepliceerd vanuit dezelfde Data Store. In de ideale omstandigheden convergeert replicatie uiteindelijk tot één cyclus per uur voor elke virtuele machine.

Wanneer u migreert, wordt een replicatie cyclus op aanvraag voor de machine gepland om alle resterende gegevens vast te leggen. U kunt ervoor kiezen om de machine tijdens de migratie uit te scha kelen om ervoor te zorgen dat er geen gegevens verloren gaan en de consistentie van de toepassing.

## <a name="why-isnt-resynchronization-exposed"></a>Waarom wordt de weer gegeven niet opnieuw gesynchroniseerd?

Tijdens migratie zonder agents wordt in elke Delta cyclus het verschil tussen de huidige moment opname en de eerder gebruikte moment opname geschreven. Omdat het altijd het verschil is tussen moment opnamen, vouwt u de gegevens in. Als een bepaalde sector N keer tussen moment opnamen wordt geschreven, moet alleen de laatste schrijf bewerking worden overgedragen, omdat alleen de laatste synchronisatie is betrokken. Dit verschilt van replicatie op basis van een agent, waarbij wij elke schrijf bewerking bijhouden en Toep assen. Dit betekent dat elke Delta cyclus een hersynchronisatie is. Daarom wordt er geen hersynchronisaties optie weer gegeven. Als de schijven niet worden gesynchroniseerd vanwege een fout, wordt deze in de volgende cyclus opgelost. 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>Wat is de invloed van het verloop van de replicatie zonder agent?

Sinds replicatie van zonder agents is de datum het verloop patroon belang rijker dan het verloop frequentie. Wanneer een bestand opnieuw wordt geschreven, heeft de frequentie geen veel invloed meer. Een patroon waarbij elke andere sector wordt geschreven, veroorzaakt echter een hoog verloop in de volgende cyclus. Omdat we de hoeveelheid gegevens die we overdragen minimaliseert, kunnen we de gegevens zo veel mogelijk vouwen voordat de volgende cyclus wordt gepland.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Hoe vaak is een replicatie cyclus gepland?

De formule voor het plannen van de volgende replicatie cyclus: (vorige cyclus tijd/2) of 1 uur, afhankelijk van wat hoger is.

Als een virtuele machine bijvoorbeeld vier uur duurt voor een Delta cyclus, wordt de volgende cyclus gepland in twee uur en niet in het volgende uur. Dit wordt direct na de initiële replicatie afwijkend, waarbij de eerste Delta cyclus onmiddellijk wordt gepland.

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>Hoe heeft replicatie van agents invloed op VMware-servers?

Er is een invloed op de prestaties van vCenter Server-ESXi-hosts. Omdat replicatie zonder agent gebruikmaakt van moment opnamen, verbruikt de server IOPs op opslag en is er een aantal IOPS-opslag bandbreedte nodig. Het wordt niet aangeraden replicatie zonder agent te gebruiken als er beperkingen gelden voor opslag/IOPs in uw omgeving.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Kan ik zonder agent migratie van UEFI-Vm's naar Azure gen 2?

Nee. Gebruik Azure Site Recovery om deze Vm's te migreren naar Gen 2 Azure Vm's. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Kan ik virtuele machines vastmaken aan Azure-beschikbaarheidszones wanneer ik migreer?

Nee, Azure-beschikbaarheidszones worden niet ondersteund.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Welk transport protocol wordt door Azure Migrate gebruikt tijdens de replicatie?

Azure Migrate maakt gebruik van het protocol voor netwerk blok apparaten (NBD) met SSL-versleuteling.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Wat is de minimale vCenter Server versie die vereist is voor de migratie?

U moet ten minste vCenter Server 5,5 en VMware vSphere ESXi host versie 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Kunnen klanten hun Vm's migreren naar niet-beheerde schijven?

Nee. Azure Migrate ondersteunt alleen migratie naar Managed disks (standaard HDD, Premium SSD).

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>Hoeveel Vm's kan ik samen repliceren met migratie zonder agent?

Op dit moment kunt u 100 Vm's per vCenter Server tegelijk migreren. Migreer in batches van tien Vm's.
 



