---
title: Veelgestelde vragen over Azure-toestel migreren
description: Antwoorden op veelgestelde vragen over het Azure Migrate-toestel.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2ef7de3b31cb11a71ec9379232fc5ff1022cf666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336976"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate appliance: veelgestelde vragen

In dit artikel worden veelgestelde vragen beantwoord over het Azure Migrate-toestel. Als u andere vragen hebt, controleert u de volgende bronnen:

- [Algemene vragen](resources-faq.md) over Azure Migreren
- Vragen over [detectie-, beoordelings- en afhankelijkheidsvisualisatie](common-questions-discovery-assessment.md)
- Vragen over [servermigratie](common-questions-server-migration.md)
- Vragen beantwoorden in het [Azure Migrate-forum](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Wat is het Azure Migrate-toestel?

Het Azure Migrate-toestel is een lichtgewicht toestel dat het Azure Migrate: Server Assessment-hulpprogramma gebruikt om on-premises servers te detecteren en te beoordelen. Het hulpprogramma Azure Migreren: Servermigratie gebruikt het toestel ook voor agentloze migratie van on-premises Vm's vMware.

Hier vindt u meer informatie over het Azure Migrate-toestel:

- Het apparaat wordt on-premises geïmplementeerd als vm of fysieke machine.
- Het toestel detecteert on-premises machines en stuurt voortdurend machinemetadata en prestatiegegevens naar Azure Migrate.
- Toesteldetectie is agentloos. Er is niets geïnstalleerd op ontdekte machines.

[Meer informatie](migrate-appliance.md) over het apparaat.

## <a name="how-does-the-appliance-connect-to-azure"></a>Hoe maakt het toestel verbinding met Azure?

Het toestel kan verbinding maken via internet of via Azure ExpressRoute met public/Microsoft-peering.

## <a name="does-appliance-analysis-affect-performance"></a>Heeft toestelanalyse invloed op de prestaties?

De on-premises machines van Azure Migrate-toestel worden continu gemigreerd om prestatiegegevens te meten. Deze profilering heeft bijna geen impact op de prestaties van geprofileerde machines.

## <a name="can-i-harden-the-appliance-vm"></a>Kan ik het apparaat VM verharden?

Wanneer u de gedownloade sjabloon gebruikt om de toestel-VM te maken, u onderdelen (bijvoorbeeld antivirus) aan de sjabloon toevoegen als u de communicatie- en firewallregels die nodig zijn voor het Azure Migrate-toestel op zijn plaats laat staan.

## <a name="what-network-connectivity-is-required"></a>Welke netwerkconnectiviteit is vereist?

Zie de volgende artikelen voor informatie over netwerkconnectiviteitsvereisten voor het Azure Migrate-toestel:

- **VMware-beoordeling**: [TOEGANG tot URL](migrate-appliance.md#url-access) en [poorttoegang](migrate-support-matrix-vmware.md#port-access)
- **VMware agentless migratie**: [URL-toegang](migrate-appliance.md#url-access) en [poorttoegang](migrate-support-matrix-vmware-migration.md#agentless-ports)
- **Hyper-V-beoordeling**: [TOEGANG tot URL](migrate-appliance.md#url-access) en [poorttoegang](migrate-support-matrix-hyper-v.md#port-access)

## <a name="what-data-does-the-appliance-collect"></a>Welke gegevens verzamelt het toestel?

Zie de volgende artikelen voor informatie over gegevens die het Azure Migrate-toestel verzamelt op VM's:

- **VMware VM**: [Bekijk](migrate-appliance.md#collected-data---vmware) verzamelde gegevens. [
- **Hyper-V VM**: [Bekijk](migrate-appliance.md#collected-data---hyper-v) verzamelde gegevens.

## <a name="how-is-data-stored"></a>Hoe worden gegevens opgeslagen?

Gegevens die worden verzameld door het Azure Migrate-toestel, worden opgeslagen op de Azure-locatie waar u het Azure Migrate-project hebt gemaakt.

Hier vindt u meer informatie over hoe gegevens worden opgeslagen:

- De verzamelde gegevens worden veilig opgeslagen in CosmosDB in een Microsoft-abonnement. De gegevens worden verwijderd wanneer u het Azure Migrate-project verwijdert. Opslag wordt afgehandeld door Azure Migrate. U niet specifiek een opslagaccount kiezen voor verzamelde gegevens.
- Als u [afhankelijkheidsvisualisatie](concepts-dependency-visualization.md)gebruikt, worden de verzamelde gegevens in de Verenigde Staten opgeslagen in een Azure Log Analytics-werkruimte die is gemaakt in uw Azure-abonnement. De gegevens worden verwijderd wanneer u de werkruimte Log Analytics in uw abonnement verwijdert.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Hoeveel gegevens worden geüpload tijdens continue profilering?

De hoeveelheid gegevens die naar Azure Migreren wordt verzonden, is afhankelijk van meerdere parameters. Een Azure Migrate-project met 10 machines (elk met één schijf en één NIC) verzendt bijvoorbeeld ongeveer 50 MB aan gegevens per dag. Deze waarde is bij benadering; de werkelijke waarde is afhankelijk van het aantal gegevenspunten voor de schijven en NIC's. Als het aantal machines, schijven of NIC's toeneemt, is de toename van de verzonden gegevens niet-lineair.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Zijn gegevens versleuteld in rust en onderweg?

Ja, voor beide:

- Metagegevens worden via HTTPS veilig naar de Azure Migrate-service verzonden via HTTPS.
- Metagegevens worden opgeslagen in een [Azure Cosmos-database](../cosmos-db/database-encryption-at-rest.md) en in [Azure Blob-opslag](../storage/common/storage-service-encryption.md) in een Microsoft-abonnement. De metagegevens worden in rust versleuteld voor opslag.
- De gegevens voor afhankelijkheidsanalyse worden ook versleuteld tijdens het transport (via beveiligde HTTPS). Het wordt opgeslagen in een werkruimte Log Analytics in uw abonnement. De gegevens worden in rust versleuteld voor afhankelijkheidsanalyse.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hoe maakt het toestel verbinding met vCenter Server?

In deze stappen wordt beschreven hoe het toestel verbinding maakt met VMware vCenter Server:

1. Het toestel maakt verbinding met vCenter Server (poort 443) met behulp van de referenties die u hebt opgegeven bij het instellen van het toestel.
2. Het toestel gebruikt VMware PowerCLI om vCenter Server op te vragen om metagegevens te verzamelen over de VM's die worden beheerd door vCenter Server.
3. Het toestel verzamelt configuratiegegevens over VM's (cores, geheugen, schijven, NIC's) en de prestatiegeschiedenis van elke VM van de afgelopen maand.
4. De verzamelde metagegevens worden verzonden naar de Azure Migrate: Server Assessment tool (via het internet via HTTPS) voor beoordeling.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Kan het Azure Migrate-toestel verbinding maken met meerdere vCenter-servers?

Nee. Er is een één-op-één toewijzing tussen een [Azure Migrate-toestel](migrate-appliance.md) en vCenter Server. Als u VM's wilt ontdekken op meerdere vCenter Server-exemplaren, moet u meerdere apparaten implementeren. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Kan een Azure Migrate-project meerdere apparaten hebben?
Aan een project kunnen meerdere apparaten zijn gekoppeld. Een toestel kan echter slechts aan één project worden gekoppeld. 


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Hoeveel VM's of servers kan ik ontdekken met een toestel?

U maximaal 10.000 VMware VM's, maximaal 5.000 Hyper-V VM's en maximaal 250 fysieke servers met één toestel ontdekken. Als u meer machines in uw on-premises omgeving hebt, leest u over [het schalen van een Hyper-V-beoordeling,](scale-hyper-v-assessment.md)het schalen van een [VMware-beoordeling](scale-vmware-assessment.md)en [het schalen van een fysieke serverbeoordeling.](scale-physical-assessment.md)

## <a name="can-i-delete-an-appliance"></a>Kan ik een toestel verwijderen?

Momenteel wordt het verwijderen van een toestel uit het project niet ondersteund.

De enige manier om het toestel te verwijderen, is door de brongroep te verwijderen die het Azure Migrate-project bevat dat aan het toestel is gekoppeld.

Als u echter de brongroep verwijdert, worden echter ook andere geregistreerde apparaten, de gedetecteerde voorraad, beoordelingen en alle andere Azure-componenten in de brongroep die aan het project zijn gekoppeld, verwijderd.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Kan ik het toestel gebruiken met een ander abonnement of project?

Nadat u het toestel hebt gebruikt om detectie te starten, u het toestel niet opnieuw configureren voor gebruik met een ander Azure-abonnement en u het niet gebruiken in een ander Azure Migrate-project. U ook geen VM's ontdekken op een ander exemplaar van vCenter Server. Stel een nieuw toestel in voor deze taken.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Kan ik het toestel instellen op een Azure VM?

Nee. Momenteel wordt deze optie niet ondersteund. 

## <a name="can-i-discover-on-an-esxi-host"></a>Kan ik het ontdekken op een ESXi-host?

Nee. Als u VMware VM's wilt ontdekken, moet u vCenter Server hebben.

## <a name="how-do-i-update-the-appliance"></a>Hoe werk ik het toestel bij?

Standaard worden het toestel en de geïnstalleerde agents automatisch bijgewerkt. Het toestel controleert elke 24 uur op updates. Updates die mislukken, worden opnieuw geprobeerd. 

Alleen het toestel en de apparaatagenten worden bijgewerkt door deze automatische updates. Het besturingssysteem wordt niet bijgewerkt door automatische updates voor Azure Migrate. Gebruik Windows Updates om het besturingssysteem up-to-date te houden.

## <a name="can-i-check-agent-health"></a>Kan ik de gezondheid van agenten controleren?

Ja. Ga in de portal naar de statuspagina **van agent** voor het Azure-migreren: serverbeoordeling of Azure-migreren: servermigratietool. Daar u de verbindingsstatus tussen Azure en de detectie- en beoordelingsagents op het toestel controleren.

## <a name="next-steps"></a>Volgende stappen

Lees het [overzicht Azure Migreren](migrate-services-overview.md).
