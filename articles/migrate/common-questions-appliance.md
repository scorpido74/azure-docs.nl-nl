---
title: Veelgestelde vragen over Azure Migrate apparaat
description: Krijg antwoorden op veelgestelde vragen over het Azure Migrate-apparaat.
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 14ce04e992cdbf121bcb1518b6a95c09ec421872
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324416"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate apparaat: veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over het Azure Migrate apparaat. Als u andere vragen hebt, raadpleegt u deze bronnen:

- [Algemene vragen](resources-faq.md) over Azure migrate
- Vragen over de [visualisatie van detectie, beoordeling en afhankelijkheid](common-questions-discovery-assessment.md)
- Vragen over [server migratie](common-questions-server-migration.md)
- Beantwoorde vragen in het [Azure migrate-forum](https://aka.ms/AzureMigrateForum) ontvangen

## <a name="what-is-the-azure-migrate-appliance"></a>Wat is het Azure Migrate apparaat?

Het Azure Migrate apparaat is een licht gewicht apparaat dat het Azure Migrate: Server Assessment Tool gebruikt om on-premises servers te detecteren en te evalueren. De Azure Migrate: het hulp programma voor server migratie gebruikt ook het apparaat voor de migratie van on-premises VMware-Vm's zonder agent.

Hier vindt u meer informatie over het Azure Migrate-apparaat:

- Het apparaat wordt on-premises geïmplementeerd als een VM of fysieke machine.
- Het apparaat detecteert on-premises machines en verstuurt continu de meta gegevens en prestatie gegevens van de computer naar Azure Migrate.
- De detectie van het apparaat is zonder agent. Er wordt niets geïnstalleerd op gedetecteerde computers.

Meer [informatie](migrate-appliance.md) over het apparaat.

## <a name="how-can-i-deploy-the-appliance"></a>Hoe kan ik het apparaat implementeren?

Het apparaat kan als volgt worden geïmplementeerd:

- Een sjabloon gebruiken voor VMware-Vm's en Hyper-V-Vm's (eicellen-sjabloon voor VMware of VHD voor Hyper-V).
- Als u geen sjabloon wilt gebruiken of als u zich in Azure Government bevindt, kunt u het apparaat voor VMware of Hyper-V implementeren met behulp van een Power shell-script.
- Voor fysieke servers implementeert u het apparaat altijd met behulp van een script.


## <a name="how-does-the-appliance-connect-to-azure"></a>Hoe maakt het apparaat verbinding met Azure?

Het apparaat kan verbinding maken via internet of met behulp van Azure ExpressRoute met open bare/micro soft-peering.

## <a name="does-appliance-analysis-affect-performance"></a>Is de invloed van de apparatuur op de prestaties?

Met de Azure Migrate apparaat worden on-premises machines continu op de prestaties van de prestatie gegevens gemeten. Deze profilering heeft bijna geen invloed op de prestaties van gefileeerde machines.

## <a name="can-i-harden-the-appliance-vm"></a>Kan ik de VM van het apparaat harder?

Wanneer u de gedownloade sjabloon gebruikt om de apparaat-VM te maken, kunt u onderdelen (bijvoorbeeld anti virus) aan de sjabloon toevoegen als u de communicatie-en firewall regels die vereist zijn voor het Azure Migrate apparaat hebt ingesteld.

## <a name="what-network-connectivity-is-required"></a>Welke netwerk verbinding is vereist?


Het apparaat moet toegang hebben tot Azure-Url's. [Controleer](migrate-appliance.md#url-access) de lijst met url's.

## <a name="what-data-does-the-appliance-collect"></a>Welke gegevens worden door het apparaat verzameld?

Raadpleeg de volgende artikelen voor informatie over gegevens die het Azure Migrate apparaat verzamelt op Vm's:

- **VMware-VM**: [Controleer](migrate-appliance.md#collected-data---vmware) de verzamelde gegevens. [
- **Hyper-V VM**: [Controleer](migrate-appliance.md#collected-data---hyper-v) de verzamelde gegevens.

## <a name="how-is-data-stored"></a>Hoe worden gegevens opgeslagen?

Gegevens die worden verzameld door het Azure Migrate apparaat, worden opgeslagen op de Azure-locatie waar u het Azure Migrate project hebt gemaakt.

Hier vindt u meer informatie over de manier waarop gegevens worden opgeslagen:

- De verzamelde gegevens worden veilig opgeslagen in CosmosDB in een micro soft-abonnement. De gegevens worden verwijderd wanneer u het Azure Migrate project verwijdert. Opslag wordt verwerkt door Azure Migrate. U kunt niet specifiek een opslag account voor verzamelde gegevens kiezen.
- Als u [afhankelijkheids visualisatie](concepts-dependency-visualization.md)gebruikt, worden de verzamelde gegevens opgeslagen in de Verenigde Staten in een Azure log Analytics-werk ruimte die in uw Azure-abonnement is gemaakt. De gegevens worden verwijderd wanneer u de Log Analytics-werk ruimte in uw abonnement verwijdert.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Hoeveel gegevens worden er geüpload tijdens een doorlopende Profiler?

Het gegevens volume dat naar Azure Migrate wordt verzonden, is afhankelijk van meerdere para meters. Een voor beeld: een Azure Migrate-project met tien machines (elk met één schijf en één NIC) verzendt ongeveer 50 MB aan gegevens per dag. Deze waarde is bij benadering; de werkelijke waarde varieert, afhankelijk van het aantal gegevens punten voor de schijven en Nic's. Als het aantal machines, schijven of Nic's toeneemt, is de toename van verzonden gegevens niet lineair.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Worden gegevens versleuteld op rest en onderweg?

Ja, voor beide:

- Meta gegevens worden via internet veilig verzonden naar de Azure Migrate-service via HTTPS.
- Meta gegevens worden opgeslagen in een [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -data base en in [Azure Blob-opslag](../storage/common/storage-service-encryption.md) in een micro soft-abonnement. De meta gegevens zijn versleuteld op rest voor opslag.
- De gegevens voor afhankelijkheids analyse zijn ook versleuteld tijdens de overdracht (door middel van beveiligde HTTPS). Deze wordt opgeslagen in een Log Analytics-werk ruimte in uw abonnement. De gegevens worden versleuteld op rest voor afhankelijkheids analyse.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hoe maakt het apparaat verbinding met vCenter Server?

In deze stappen wordt beschreven hoe het apparaat verbinding maakt met VMware vCenter Server:

1. Het apparaat maakt verbinding met vCenter Server (poort 443) met behulp van de referenties die u hebt opgegeven bij het instellen van het apparaat.
2. Het apparaat gebruikt VMware PowerCLI om vCenter Server voor het verzamelen van meta gegevens over de virtuele machines die worden beheerd door vCenter Server.
3. Het apparaat verzamelt configuratie gegevens over Vm's (kernen, geheugen, schijven, Nic's) en de prestatie geschiedenis van elke virtuele machine in de afgelopen maand.
4. De verzamelde meta gegevens worden verzonden naar de Azure Migrate: Server Assessment Tool (via internet via HTTPS) voor evaluatie.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Kan het Azure Migrate apparaat verbinding maken met meerdere vCenter-servers?

Nee. Er is een een-op-een-toewijzing tussen een [Azure migrate apparaat](migrate-appliance.md) en vCenter Server. Als u Vm's op meerdere exemplaren van vCenter Server wilt detecteren, moet u meerdere toestellen implementeren. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Kan een Azure Migrate project meerdere toestellen hebben?
Aan een project kunnen meerdere apparaten zijn gekoppeld. Een apparaat kan echter slechts aan één project worden gekoppeld. 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Kan de Azure Migrate apparaat/replicatie apparaat verbinding maken met dezelfde vCenter?
Ja. U kunt zowel het Azure Migrate apparaat (gebruikt voor de distributie en de VMware-migratie zonder agent) toevoegen aan dezelfde vCenter-Server (gebruikt voor de migratie van virtuele VMware-machines op basis van een agent).


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Hoeveel Vm's of servers kan ik vinden met een apparaat?

U kunt Maxi maal 10.000 VMware-Vm's, Maxi maal 5.000 virtuele Hyper-V-machines en Maxi maal 250 fysieke servers op één apparaat detecteren. Als u meer computers in uw on-premises omgeving hebt, leest u over [het schalen van een Hyper-V-beoordeling](scale-hyper-v-assessment.md), [het schalen van een VMware-evaluatie](scale-vmware-assessment.md)en [het schalen van een fysieke server beoordeling](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Kan ik een apparaat verwijderen?

Het verwijderen van een apparaat uit het project wordt momenteel niet ondersteund.

De enige manier om het apparaat te verwijderen, is door de resource groep te verwijderen die het Azure Migrate project bevat dat is gekoppeld aan het apparaat.

Als u echter de resource groep verwijdert, worden ook andere geregistreerde apparaten, de gedetecteerde inventaris, beoordelingen en alle andere Azure-onderdelen in de resource groep die aan het project zijn gekoppeld, verwijderd.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Kan ik het apparaat met een ander abonnement of project gebruiken?

Nadat u het apparaat hebt gebruikt om de detectie te initiëren, kunt u het apparaat niet opnieuw configureren voor gebruik met een ander Azure-abonnement en kunt u dit niet gebruiken in een ander Azure Migrate project. U kunt ook geen Vm's detecteren op een ander exemplaar van vCenter Server. Stel een nieuw apparaat in voor deze taken.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Kan ik het apparaat instellen op een virtuele Azure-machine?

Nee. Deze optie wordt momenteel niet ondersteund. 

## <a name="can-i-discover-on-an-esxi-host"></a>Kan ik op een ESXi-host ontdekken?

Nee. U moet beschikken over vCenter Server om virtuele VMware-machines te detecteren.

## <a name="how-do-i-update-the-appliance"></a>Het apparaat Hoe kan ik bijwerken?

Standaard worden het apparaat en de geïnstalleerde agents automatisch bijgewerkt. Het apparaat controleert elke 24 uur op updates. Updates die niet kunnen worden uitgevoerd. 

Alleen het apparaat en de agents van het apparaat worden bijgewerkt door deze automatische updates. Het besturings systeem wordt niet bijgewerkt door Azure Migrate automatische updates. Windows-updates gebruiken om het besturings systeem up-to-date te houden.

## <a name="can-i-check-agent-health"></a>Kan ik de agent status controleren?

Ja. Ga in de portal naar de pagina **status van agent** voor de Azure migrate: Server evaluatie of Azure migrate: hulp programma voor server migratie. Daar kunt u de verbindings status controleren tussen Azure en de detectie-en evaluatie agenten op het apparaat.

## <a name="next-steps"></a>Volgende stappen

Lees het [Azure migrate overzicht](migrate-services-overview.md).
