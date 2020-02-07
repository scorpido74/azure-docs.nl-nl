---
title: Veelgestelde vragen over het Azure Migrate apparaat
description: Antwoorden vinden op veelgestelde vragen over het Azure Migrate apparaat
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: de9583b7983dda904e06ea258ecb0d202644586b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064696"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate apparaat: veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over het Azure Migrate apparaat. Als u na het lezen van dit artikel nog meer query's hebt uitgevoerd, plaatst u deze op het [Azure migrate forum](https://aka.ms/AzureMigrateForum). Als u andere vragen hebt, raadpleegt u de volgende artikelen:

- [Algemene vragen](resources-faq.md) over Azure Migrate.
- [Vragen](common-questions-discovery-assessment.md) over de visualisatie van detectie, beoordeling en afhankelijkheid.
- [Vragen](common-questions-server-migration.md) over server migratie.


## <a name="what-is-the-azure-migrate-appliance"></a>Wat is het Azure Migrate apparaat?

Het Azure Migrate apparaat is een licht gewicht dat wordt gebruikt door het Azure Migrate: Server Assessment hulp programma voor het detecteren en evalueren van on-premises servers en wordt gebruikt door de Azure Migrate: hulp programma voor server migratie voor agentloze migratie van on-premises VMware-Vm's. 

Het apparaat wordt on-premises geïmplementeerd als een VM of fysieke machine. Het apparaat detecteert on-premises machines en stuurt voortdurend meta gegevens en prestatie gegevens van de machine naar Azure Migrate. De detectie van het apparaat is zonder agent. Er wordt niets geïnstalleerd op gedetecteerde computers. Meer [informatie](migrate-appliance.md) over het apparaat.

## <a name="how-does-the-appliance-connect-to-azure"></a>Hoe maakt het apparaat verbinding met Azure?

De verbinding kan via internet zijn of Azure ExpressRoute gebruiken met open bare/micro soft-peering.

## <a name="does-appliance-analysis-impact-performance"></a>Beïnvloeden de prestaties van het toestel analyse?

Met de Azure Migrate apparaat worden on-premises machines voortdurend op de prestaties van de VM-prestatie gegevens gemeten. Deze profilering heeft bijna geen invloed op de prestaties op de Hyper-V-ESXi-hosts of op VMware vCenter Server.

### <a name="can-i-harden-the-appliance-vm"></a>Kan ik de VM van het apparaat harder?

Wanneer u de apparaat-VM met behulp van de gedownloade sjabloon maakt, kunt u onderdelen (bijvoorbeeld een anti virus) aan de sjabloon toevoegen, zolang u de communicatie-en firewall regels die vereist zijn voor de Azure Migrate apparaat in-of uitschakelen.


## <a name="what-network-connectivity-is-needed"></a>Welke netwerk verbinding is nodig?

Controleer het volgende:
- VMware-evaluatie met behulp van het Azure Migrate-apparaat: vereisten voor toegang tot de [URL](migrate-appliance.md#url-access) en [poort](migrate-support-matrix-vmware.md#port-access) .
- VMware-migratie zonder agents met behulp van het Azure Migrate apparaat: vereisten voor de toegang tot de [URL](migrate-appliance.md#url-access) en [poort](migrate-support-matrix-vmware-migration.md#agentless-ports) .
- Hyper-V-evaluatie met behulp van het Azure Migrate apparaat: vereisten voor toegang tot de [URL](migrate-appliance.md#url-access) en [poort](migrate-support-matrix-hyper-v.md#port-access) .


## <a name="what-data-does-the-appliance-collect"></a>Welke gegevens worden door het apparaat verzameld?

Controleer de verzamelde gegevens:

- [Prestatie gegevens](migrate-appliance.md#collected-performance-data-vmware) en [meta gegevens](migrate-appliance.md#collected-metadata-vmware)voor VMware-vm's.
- [Prestatie gegevens](migrate-appliance.md#collected-performance-data-hyper-v) en [META](migrate-appliance.md#collected-metadata-hyper-v)gegevens van de virtuele machine van Hyper-V.


## <a name="how-is-data-stored"></a>Hoe worden gegevens opgeslagen?

Gegevens die worden verzameld door het Azure Migrate apparaat, worden opgeslagen op de Azure-locatie die u kiest tijdens het maken van het migratie project. 

- De gegevens worden veilig opgeslagen in een micro soft-abonnement en worden verwijderd wanneer u het Azure Migrate project verwijdert.
- Als u [afhankelijkheids visualisatie](concepts-dependency-visualization.md)gebruikt, worden de verzamelde gegevens opgeslagen in het Verenigde Staten, in een log Analytics werk ruimte die in het Azure-abonnement is gemaakt. Deze gegevens worden verwijderd wanneer u de Log Analytics-werk ruimte in uw abonnement verwijdert.

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>Hoeveel gegevens worden er geüpload in een doorlopende Profiler?

Het volume van de gegevens die naar Azure Migrate worden verzonden, is afhankelijk van een aantal para meters. Om u een idee te geven van het volume, stuurt een Azure Migrate project met 10 machines (elk met één schijf en één NIC) ongeveer 50 MB per dag. Deze waarde is van benadering en verandert op basis van het aantal gegevens punten voor de Nic's en schijven. De toename van gegevens die worden verzonden, is niet-lineair als er een verhoging is van het aantal machines, Nic's of schijven.

## <a name="is-data-encrypted-at-restin-transit"></a>Zijn gegevens versleuteld op rest/in-transit?

Ja, voor beide.

- Meta gegevens worden via internet veilig verzonden naar de Azure Migrate-service via HTTPS.
- Meta gegevens worden opgeslagen in een [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -data base en in [Azure Blob-opslag](../storage/common/storage-service-encryption.md), in een micro soft-abonnement. De meta gegevens worden op rest versleuteld.
- De gegevens voor afhankelijkheids analyse worden ook in-transit (beveiligde HTTPS) versleuteld. Deze wordt opgeslagen in een Log Analytics-werk ruimte in uw abonnement. Het is ook versleuteld op rest.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hoe maakt het apparaat verbinding met vCenter Server?

1. Het apparaat maakt verbinding met vCenter Server (poort 443) met behulp van de referenties die u hebt opgegeven bij het instellen van het apparaat.
2. Het apparaat gebruikt VMware PowerCLI om vCenter Server te zoeken, om meta gegevens te verzamelen over de Vm's die worden beheerd door vCenter Server.
3. Het apparaat verzamelt configuratie gegevens over Vm's (kernen, geheugen, schijven, Nic's) en de prestatie geschiedenis van elke virtuele machine in de afgelopen maand.
4. De verzamelde meta gegevens zijn voor Azure Migrate: Server evaluatie (via internet via HTTPS) voor evaluatie.

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>Kan ik het apparaat aansluiten op meerdere vCenter-servers?

Nee. Er is een een-op-een-toewijzing tussen een apparaat en vCenter Server. Als u Vm's op meerdere exemplaren van vCenter Server wilt detecteren, moet u meerdere apparaten implementeren.

### <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Hoeveel Vm's of servers kan ik vinden met een apparaat?

U kunt Maxi maal 10.000 VMware-Vm's detecteren, tot 5.000 Hyper-V-Vm's en Maxi maal 250 servers met één apparaat. Als u meer computers in uw on-premises omgeving hebt, leest u over het schalen van [Hyper-V](scale-hyper-v-assessment.md), [VMware](scale-vmware-assessment.md) en [fysieke](scale-physical-assessment.md) evaluatie.

## <a name="can-i-delete-an-appliance"></a>Kan ik een apparaat verwijderen?

Het verwijderen van het apparaat uit het project wordt momenteel niet ondersteund.

- De enige manier om het apparaat te verwijderen, is door de resource groep te verwijderen die het Azure Migrate project bevat dat aan het apparaat is gekoppeld.
- Als u echter de resource groep verwijdert, worden ook andere geregistreerde apparaten, de gedetecteerde inventaris, beoordelingen en alle andere Azure-onderdelen die zijn gekoppeld aan het project in de resource groep, verwijderd.


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>Kan ik het apparaat met een ander abonnement/project gebruiken?

Nadat u het apparaat hebt gebruikt om de detectie te initiëren, kunt u het niet opnieuw configureren met een ander Azure-abonnement of in een ander Azure Migrate project. U kunt ook geen Vm's detecteren op een andere vCenter Server. Stel een nieuw apparaat in voor deze taken.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Kan ik het apparaat instellen op een virtuele Azure-machine?
Momenteel niet ondersteund. 

## <a name="can-i-discover-on-an-esxi-host"></a>Kan ik op een ESXi-host ontdekken?
U hebt geen vCenter Server nodig om virtuele VMware-machines te detecteren.

## <a name="how-do-i-update-the-appliance"></a>Het apparaat Hoe kan ik bijwerken?

Standaard worden het apparaat en de geïnstalleerde agents automatisch bijgewerkt. Het apparaat controleert elke 24 uur of er updates zijn. Als er fouten optreden tijdens het update proces, wordt het proces opnieuw geprobeerd. Automatische updates werkt alleen de apparaat-en toestel agent bij. Het besturings systeem is niet bijgewerkt. Gebruik micro soft-updates om het besturings systeem up-to-date te houden.

## <a name="can-i-check-agent-health"></a>Kan ik de agent status controleren?

Ga in de portal naar de pagina **agent status** in de server Assessment of het hulp programma voor server migratie. Daar kunt u de verbindings status controleren tussen de detectie-en evaluatie agenten op het apparaat en Azure.

## <a name="next-steps"></a>Volgende stappen
Lees het [Azure migrate overzicht](migrate-services-overview.md).
