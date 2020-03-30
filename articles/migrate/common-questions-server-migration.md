---
title: Veelgestelde vragen over migratie van Azure Migrate Server
description: Antwoorden op veelgestelde vragen over het gebruik van Azure Migrate Server Migration om machines te migreren.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 507cc8088bf54b1a4f4483673ec5332efcdd36c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127807"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migratie van Azure Migrate Server: veelgestelde vragen

In dit artikel worden veelgestelde vragen beantwoord over het hulpprogramma Azure Migreren: Servermigratie. Als u andere vragen hebt, controleert u de volgende bronnen:

- [Algemene vragen](resources-faq.md) over Azure Migreren
- Vragen over het [Azure Migrate-toestel](common-questions-appliance.md)
- Vragen over [detectie-, beoordelings- en afhankelijkheidsvisualisatie](common-questions-discovery-assessment.md)
- Vragen beantwoorden in het [Azure Migrate-forum](https://aka.ms/AzureMigrateForum)

## <a name="how-does-agentless-vmware-replication-work"></a>Hoe werkt agentless VMware-replicatie?

De agentless replicatiemethode voor VMware maakt gebruik van VMware-snapshots en VMware Changed Block Tracking (CBT).

Dit is het proces:

1. Wanneer u de replicatie start, wordt een eerste replicatiecyclus gepland. In de eerste cyclus wordt een momentopname van de VM gemaakt. De momentopname wordt gebruikt om de VMVM's (schijven) te repliceren. 
2. Nadat de eerste replicatiecyclus is voltooid, worden deltareplicatiecycli periodiek gepland.
    - Tijdens deltareplicatie wordt een momentopname gemaakt en gegevensblokken die zijn gewijzigd sinds de vorige replicatiecyclus zijn gerepliceerd.
    - VMware CBT wordt gebruikt om blokken te bepalen die sinds de laatste cyclus zijn gewijzigd.
    - De frequentie van de periodieke replicatiecycli wordt automatisch beheerd door Azure Migrate en is afhankelijk van het aantal andere VM's en schijven dat gelijktijdig wordt gerepliceerd vanuit hetzelfde gegevensarchief. In ideale omstandigheden komt replicatie uiteindelijk samen tot één cyclus per uur voor elke VM.

Wanneer u migreert, wordt een on-demand replicatiecyclus gepland voor de machine om de resterende gegevens vast te leggen. Als u wilt zorgen voor nul gegevensverlies en consistentie van de toepassing, u ervoor kiezen om de machine tijdens de migratie uit te schakelen.

## <a name="why-isnt-resynchronization-exposed"></a>Waarom wordt resynchronisatie niet blootgesteld?

Tijdens agentloze migratie wordt in elke deltacyclus het verschil tussen de huidige momentopname en de eerder gemaakte momentopname geschreven. Het is altijd het verschil tussen snapshots, het invouwen van gegevens. Als een specifieke sector *N-tijden* tussen momentopnamen wordt geschreven, hoeft alleen de laatste schrijftekst te worden overgedragen omdat we alleen geïnteresseerd zijn in de laatste synchronisatie. Het proces is anders dan op agent gebaseerde replicatie, waarbij we elke schrijfperiode bijhouden en toepassen. In dit proces is elke deltacyclus een resynchronisatie. Dus, geen resynchronisatie optie blootgesteld. Als de schijven ooit niet worden gesynchroniseerd vanwege een storing, wordt deze in de volgende cyclus opgelost. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Welke invloed heeft churn rate op agentless replicatie?

Omdat agentless replicatie in gegevens wordt gevouwen, is het *verlooppatroon* belangrijker dan de *churn rate*. Wanneer een bestand steeds opnieuw wordt geschreven, heeft de snelheid niet veel impact. Echter, een patroon waarin elke andere sector is geschreven veroorzaakt hoge churn in de volgende cyclus. Omdat we de hoeveelheid gegevens die we overdragen minimaliseren, laten we de gegevens zoveel mogelijk vouwen voordat we de volgende cyclus plannen.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Hoe vaak is een replicatiecyclus gepland?

De formule voor het plannen van de volgende replicatiecyclus is (vorige cyclustijd / 2) of een uur, welke hoger is.

Als een VM bijvoorbeeld vier uur duurt voor een deltacyclus, wordt de volgende cyclus binnen twee uur gepland en niet in het volgende uur. Het proces is onmiddellijk na de eerste replicatie anders, wanneer de eerste deltacyclus onmiddellijk is gepland.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Hoe beïnvloedt agentless replicatie VMware-servers?

Agentless replicatie resulteert in enige prestatie-impact op VMware vCenter Server en VMware ESXi hosts. Omdat agentless replicatie snapshots gebruikt, verbruikt het IOPS op opslag, dus sommige IOPS-opslagbandbreedte is vereist. We raden u af om agentless replicatie te gebruiken als u beperkingen hebt op opslag of IPP's in uw omgeving.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Kan ik gebruikmaken van agentless migratie van UEFI VM's naar Azure Gen 2?

Nee. Gebruik Azure Site Recovery om deze VM's te migreren naar Gen 2 Azure VM's. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Kan ik VM's vastmaken aan Azure Availability Zones wanneer ik migreer?

Nee. Azure Availability Zones worden niet ondersteund voor Azure Migrate-migratie.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Welk transportprotocol gebruikt Azure Migrate tijdens de replicatie?

Azure Migrate maakt gebruik van het NBD-protocol (Network Block Device) met SSL-versleuteling.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Wat is de minimale vCenter Server-versie die nodig is voor migratie?

U moet ten minste vCenter Server 5.5 en vSphere ESXi-hostversie 5.5 hebben.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Kunnen klanten hun VM's migreren naar onbeheerde schijven?

Nee. Azure Migrate ondersteunt migratie alleen naar beheerde schijven (Standard HDD, Premium SSD).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Hoeveel VM's kan ik in één keer repliceren met behulp van agentloze migratie?

Momenteel u tegelijkertijd 100 VM's per exemplaar van vCenter Server migreren. Migreren in batches van 10 VM's.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Hoe kan ik de replicatie beperken bij het gebruik van Azure Migrate appliance voor agentless VMware-replicatie?  

U gas geven met NetQosPolicy. Bijvoorbeeld:

Het AppNamePrefix dat u in het NetQosPolicy wilt gebruiken, is "GatewayWindowsService.exe". U een beleid maken voor het Azure Migrate-toestel om het replicatieverkeer van het toestel te beperken door een beleid als dit te maken:
 
Nieuw-NetQosPolicy -Naam "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottlerateActionBitsPerSeconde 1 MB

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Wanneer migreer ik machines als fysieke servers?

Het migreren van machines door ze als fysieke servers te behandelen is nuttig in een aantal scenario's:

- Wanneer u on-premises fysieke servers migreert.
- Als u VM's migreert die worden gevirtualiseerd door platforms zoals Xen, KVM.
- Als u Hyper-V- of VMware-VM's wilt migreren, u het standaardmigratieproces niet gebruiken voor [Hyper-V-](tutorial-migrate-hyper-v.md)of [VMware-migratie.](server-migrate-overview.md) Als u bijvoorbeeld geen VMware vCenter uitvoert en alleen ESXi-hosts gebruikt.
- VM's migreren die momenteel in privéclouds worden uitgevoerd naar Azure
- Als u VM's die worden uitgevoerd in openbare clouds zoals Amazon Web Services (AWS) of Google Cloud Platform (GCP), wilt migreren naar Azure.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Heb ik VMware vCenter nodig om VMware VM's te migreren?
Als [u VMware VM's wilt migreren](server-migrate-overview.md) met VMware-agentgebaseerde of agentloze migratie, moeten ESXi-hosts waarop VM's zich bevinden, worden beheerd door vCenter Server. Als u geen vCenterServer hebt, u VMware VM's migreren door ze te migreren als fysieke servers. [Meer informatie](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Volgende stappen

Lees het [overzicht Azure Migreren](migrate-services-overview.md).
