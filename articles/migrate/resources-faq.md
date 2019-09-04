---
title: Veelgestelde vragen over Azure Migrate | Microsoft Docs
description: Hierin worden veelgestelde vragen over Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: 46c6ac52e1afb6c1619b814580a1059fd3dfedda
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279504"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>Azure Migrate: Veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Migrate. Als u na het lezen van dit artikel nog meer query's hebt uitgevoerd, plaatst u deze op het [Azure migrate forum](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Algemeen

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Welke Azure-geografies Azure Migrate ondersteunen?

Zie de [lijst voor VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) en de [lijst voor Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Wat is het verschil tussen Azure Migrate en Azure Site Recovery?

Azure Migrate biedt een gecentraliseerde hub om uw migratie te starten, detectie en evaluatie van machines en workloads uit te voeren en te volgen, en de migratie van machines en workloads naar Azure uit te voeren en te volgen. [Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) is een oplossing voor nood herstel. Azure Migrate server migratie gebruikt Azure Site Recovery op de back-end om migratie scenario's voor de migratie van on-premises machines in te scha kelen.

## <a name="azure-migrate-appliance"></a>Azure Migrate-apparaat

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Hoe maakt het Azure Migrate apparaat verbinding met Azure?

De verbinding kan via internet zijn of u kunt Azure ExpressRoute gebruiken met open bare/micro soft-peering.

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Wat zijn de vereisten voor de netwerk verbinding voor de evaluatie en migratie van Azure Migrate server?

Raadpleeg de [VMware](migrate-support-matrix-vmware.md) [-en Hyper-V-](migrate-support-matrix-hyper-v.md) ondersteunings matrices voor informatie over de url's en poorten die nodig zijn om Azure migrate in staat te stellen te communiceren met Azure.

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>Kan ik de apparaat-VM die ik heb ingesteld met de sjabloon, een harder maken?

U kunt onderdelen (bijvoorbeeld anti virus) aan de sjabloon toevoegen, zolang u de communicatie-en firewall regels die vereist zijn voor de Azure Migrate apparaat als dat niet hebt.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Welke gegevens worden verzameld door het Azure Migrate apparaat?

U kunt details weer geven over de gegevens die worden verzameld door het Azure Migrate apparaat [in de Azure migrate documentatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Is er sprake van invloed op de prestaties van de geanalyseerde VMware-of Hyper-V-omgeving?

Met de Azure Migrate apparaat worden on-premises machines voortdurend op de prestaties van de VM-prestatie gegevens gemeten. Deze profilering heeft bijna geen invloed op de prestaties op de Hyper-V-ESXi-hosts of op vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Waar worden de verzamelde gegevens opgeslagen en hoelang?

De gegevens die worden verzameld door het Azure Migrate apparaat, worden opgeslagen op de Azure-locatie die u kiest tijdens het maken van het migratie project. De gegevens worden veilig opgeslagen in een micro soft-abonnement en worden verwijderd wanneer u het Azure Migrate project verwijdert.

Als u agents op de virtuele machines installeert, worden de gegevens die worden verzameld door de afhankelijkheids agents opgeslagen in de Verenigde Staten, in een Log Analytics werk ruimte die in het Azure-abonnement is gemaakt. Deze gegevens worden verwijderd wanneer u de Log Analytics-werk ruimte in uw abonnement verwijdert. Zie [afhankelijkheids visualisatie](concepts-dependency-visualization.md)voor meer informatie.

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>Welk gegevens volume wordt door het Azure Migrate apparaat geüpload tijdens een doorlopende profile ring?

Het volume van de gegevens die naar Azure Migrate worden verzonden, is afhankelijk van verschillende para meters. Om u een idee te geven van het volume: een Azure Migrate project met 10 machines (elk met één schijf en één NIC) stuurt ongeveer 50 MB per dag. Deze waarde is een benadering. Het wordt gewijzigd op basis van het aantal gegevens punten voor de Nic's en schijven. (De toename van verzonden gegevens is niet-lineair als het aantal machines, Nic's of schijven toeneemt.)

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Zijn de gegevens versleuteld op rest en onderweg?

Ja, beide. De meta gegevens worden via internet veilig verzonden naar de Azure Migrate-service via HTTPS. De meta gegevens worden opgeslagen in een [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) -data base en in [Azure Blob-opslag](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) in een micro soft-abonnement. De meta gegevens worden op rest versleuteld.

De gegevens die door de afhankelijkheids agenten worden verzameld, worden ook versleuteld in transit (beveiligde HTTPS). Deze wordt opgeslagen in een Log Analytics-werk ruimte in uw abonnement. Het is ook versleuteld op rest.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>Hoe communiceert het Azure Migrate apparaat met vCenter Server en de Azure Migrate service?

Het apparaat maakt verbinding met vCenter Server (poort 443) met behulp van de referenties die u hebt opgegeven bij het instellen van het apparaat. Er wordt gebruikgemaakt van VMware PowerCLI om vCenter Server te zoeken naar het verzamelen van meta gegevens over de Vm's die worden beheerd door vCenter Server. Er worden configuratie gegevens verzameld over Vm's (kernen, geheugen, schijven, Nic's enzovoort) en ook de prestatie geschiedenis van elke virtuele machine voor de afgelopen maand. De verzamelde meta gegevens worden vervolgens verzonden naar Azure Migrate server-evaluatie (via internet via HTTPS) voor evaluatie.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Kan ik hetzelfde apparaat verbinden met meerdere exemplaren van vCenter Server?

Nee. Er is een een-op-een-toewijzing tussen een apparaat en vCenter Server. Als u Vm's op meerdere exemplaren van vCenter Server moet detecteren, dient u meerdere apparaten te implementeren.


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>Ik heb mijn computer grootte gewijzigd. Kan ik de evaluatie opnieuw uitvoeren?

Het Azure Migrate apparaat verzamelt voortdurend informatie over de on-premises omgeving. Maar een evaluatie is een tijdgebonden moment opname van on-premises Vm's. Als u de instellingen van een virtuele machine die u wilt beoordelen, wijzigt, gebruikt u de optie opnieuw berekenen om de evaluatie bij te werken met de meest recente wijzigingen.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>Hoe kan ik detectie uitvoeren in een multi tenant omgeving in Azure Migrate server-evaluatie?

Als u voor VMware een omgeving hebt die wordt gedeeld tussen tenants en u niet wilt dat de virtuele machines van één Tenant in het abonnement van een andere Tenant worden gedetecteerd, moet u vCenter Server referenties maken die alleen toegang hebben tot de virtuele machines die u wilt detecteren. Gebruik vervolgens deze referenties wanneer u de detectie start in het Azure Migrate apparaat.

Voor Hyper-V maakt detectie gebruik van referenties voor Hyper-V-hosts. Als Vm's dezelfde Hyper-V-host delen, is er momenteel geen enkele manier om de detectie te scheiden.  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>Hoeveel Vm's kan ik vinden met één migratie apparaat?

U kunt Maxi maal 10.000 VMware-Vm's en Maxi maal 5.000 virtuele Hyper-V-machines detecteren met één migratie apparaat. Als u meer computers in uw on-premises omgeving hebt, leert u hoe u de [Hyper-V-](scale-hyper-v-assessment.md) en [VMware](scale-vmware-assessment.md) -evaluatie kunt schalen.

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Kan ik het Azure Migrate apparaat uit het project verwijderen?
Het verwijderen van het apparaat uit het project wordt momenteel niet ondersteund. De enige manier om het apparaat te verwijderen, is door de resource groep te verwijderen die het Azure Migrate project bevat dat is gekoppeld aan het apparaat, maar die ook andere geregistreerde apparaten, de gedetecteerde inventaris, beoordelingen en alle andere Azure-artefacten verwijdert. gekoppeld aan het project in de resource groep.

## <a name="azure-migrate-server-assessment"></a>Evaluatie van Azure Migrate-server

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Ondersteunt Azure Migrate evaluatie van server evaluatie van fysieke servers?

Nee, Azure Migrate biedt momenteel geen ondersteuning voor de evaluatie van fysieke servers.

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>Heeft Azure Migrate vCenter Server nodig om de detectie uit te voeren in een VMware-omgeving?

Ja, Azure Migrate moet vCenter Server om de detectie uit te voeren in een VMware-omgeving. Het biedt geen ondersteuning voor detectie van ESXi-hosts die niet worden beheerd door vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Wat is het verschil tussen Azure Migrate server evaluatie en de kaart Toolkit?

Azure Migrate server-evaluatie biedt migratie-evaluatie om te helpen bij de voor bereiding op de migratie en de evaluatie van workloads voor migratie naar Azure. [Micro soft Assessment and planning (kaart) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) helpt bij andere taken, zoals migratie planning voor nieuwere versies van Windows-besturings systemen voor clients en servers en het bijhouden van software gebruik. Voor deze scenario's kunt u de kaart Toolkit blijven gebruiken.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Wat is het verschil tussen de evaluatie van Azure Migrate servers en de Azure Site Recovery Deployment Planner?

Azure Migrate server-evaluatie is een hulp programma voor migratie planning. De Azure Site Recovery Deployment Planner is een hulp programma voor nood herstel planning.

**Migratie van VMware/Hyper-V naar Azure**: Als u van plan bent om uw on-premises servers naar Azure te migreren, moet u Azure Migrate server-evaluatie voor migratie planning gebruiken. Het evalueert on-premises workloads en biedt richt lijnen, inzichten en hulpprogram ma's waarmee u uw servers naar Azure kunt migreren. Wanneer u klaar bent met uw migratie plan, kunt u hulpprogram ma's als Azure Migrate server migratie gebruiken om de machines te migreren naar Azure.

**Herstel na nood geval van VMware/Hyper-V naar Azure**: Gebruik voor herstel na nood gevallen naar Azure via Site Recovery de Site Recovery Deployment Planner voor de planning. Site Recovery Deployment Planner een diepe, Site Recovery-specifieke evaluatie van uw on-premises omgeving. Het biedt aanbevelingen die nodig zijn voor Site Recovery voor geslaagde rampen, zoals replicatie en failover van Vm's.

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>Ondersteunt Azure Migrate kosten raming voor het Enterprise Agreement EA-programma?

Azure Migrate biedt momenteel geen ondersteuning voor de kosten raming voor het [Enterprise Agreement programma](https://azure.microsoft.com/offers/enterprise-agreement-support/). De tijdelijke oplossing is het opgeven van **betalen per gebruik** als de **aanbieding** en hand matig het kortings percentage opgeven (van toepassing op het abonnement) in het vak **korting** van de eigenschappen van de beoordeling:

  ![Beoordelingseigenschappen](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Wat is het verschil tussen de on-premises grootte en de grootte van de prestaties?

Bij een on-premises grootte wordt Azure Migrate geen rekening gehouden met de prestatie gegevens van de virtuele machine. Het formaat van de virtuele machines op basis van de on-premises configuratie. De grootte van de prestaties is gebaseerd op de gebruiks gegevens.

Denk bijvoorbeeld aan een on-premises VM met vier kernen en 8 GB aan geheugen om 50% CPU-gebruik en 50% geheugen gebruik. Voor deze VM wordt een Azure VM-SKU aanbevolen met vier kernen en 8 GB aan geheugen. De grootte van de prestaties is gebaseerd op een VM-SKU met twee kernen en 4 GB geheugen, omdat het gebruiks percentage wordt overwogen.

De schijf grootte is ook afhankelijk van twee evaluatie-eigenschappen: grootte criteria en opslag type. Als de grootte criteria op basis van prestaties zijn en het opslag type automatisch is, houdt Azure Migrate rekening met de IOPS-en doorvoer waarden van de schijf wanneer het type doel schijf (Standard of Premium) wordt geïdentificeerd.

Als de grootte criteria op basis van prestaties zijn en het opslag type Premium is, raadt Azure Migrate een Premium-schijf aan. De Premium-schijf-SKU wordt geselecteerd op basis van de grootte van de on-premises schijf. Dezelfde logica wordt gebruikt voor het uitvoeren van de schijf grootte wanneer de grootte criteria zich op de lokale grootte bevinden en het opslag type Standard of Premium is.

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>Welke invloed heeft de prestaties van de prestatie geschiedenis en het gebruik van het percentiel?

Deze eigenschappen zijn alleen van toepassing wanneer de grootte wordt bepaald op basis van prestaties.

Azure Migrate verzamelt de prestatie geschiedenis van on-premises machines en gebruikt deze om de VM-grootte en het schijf type in azure aan te bevelen.

Het apparaat maakt continu gebruik van de on-premises omgeving voor het verzamelen van gegevens in realtime om de 20 seconden. Het apparaat rolt de voor beelden van 20 seconden samen en maakt één gegevens punt voor elke 15 minuten. Voor het maken van het gegevens punt selecteert het apparaat de piek waarde van alle voor beelden van 20 seconden. Het apparaat verzendt dit gegevens punt naar Azure.

Wanneer u een evaluatie maakt in azure (op basis van de waarde voor prestatie duur en prestatie geschiedenis percentiel), berekent Azure Migrate de juiste gebruiks waarde en wordt deze gebruikt voor het aanpassen van de grootte.

Stel bijvoorbeeld dat u de duur van de prestaties instelt op één dag en de percentiel waarde op 95 percentiel. Azure Migrate worden de voorbeeld punten van 15 minuten die zijn verzonden door de collector voor de afgelopen dag in oplopende volg orde gesorteerd en wordt de waarde van het 95e percentiel als effectief gebruik gekozen.

De 95e percentiel waarde zorgt ervoor dat u uitschieters negeert. Uitschieters kunnen worden opgenomen als u het 99e percentiel gebruikt. Als u het piek gebruik voor de periode wilt kiezen en u geen uitbijters wilt missen, selecteert u het 99e percentiel.

### <a name="what-is-dependency-visualization"></a>Wat is de visualisatie van afhankelijkheden?

Met behulp van afhankelijkheids visualisatie kunt u groepen Vm's beoordelen voor migratie met een grotere betrouw baarheid. Er wordt gekeken naar computer afhankelijkheden voordat u een evaluatie uitvoert. Met een afhankelijkheids visualisatie kunt u ervoor zorgen dat er niets achterblijft om onverwachte storingen te voor komen wanneer u naar Azure migreert. Azure Migrate maakt gebruik van de Servicetoewijzing oplossing in Azure Monitor Logboeken om de visualisatie van afhankelijkheden in te scha kelen.

> [!NOTE]
> De visualisatie van de afhankelijkheid is niet beschikbaar in Azure Government.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Moet ik betalen om de visualisatie van de afhankelijkheid te gebruiken?

Nee. Zie [Azure migrate prijzen](https://azure.microsoft.com/pricing/details/azure-migrate/)voor meer informatie.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Moet ik iets installeren voor de visualisatie van afhankelijkheden?

Als u afhankelijkheids visualisatie wilt gebruiken, moet u agents downloaden en installeren op elke on-premises computer die u wilt evalueren.

U moet de volgende agents op elke computer installeren:
- [Micro soft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Afhankelijkheids agent](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Als u machines hebt zonder Internet verbinding, moet u Log Analytics gateway op deze computers downloaden en installeren.

U hebt deze agents niet nodig tenzij u gebruikmaakt van afhankelijkheids visualisatie.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Kan ik een bestaande werk ruimte gebruiken voor de visualisatie van afhankelijkheden?

Ja, u kunt een bestaande werk ruimte koppelen aan het migratie project en deze gebruiken voor de visualisatie van afhankelijkheden. Zie ' Hoe werkt het? ' in het [visualisatie](concepts-dependency-visualization.md#how-does-it-work) -artikel voor meer informatie.

### <a name="can-i-export-the-dependency-visualization-report"></a>Kan ik het visualisatie rapport van de afhankelijkheid exporteren?

Nee, de visualisatie van de afhankelijkheid kan niet worden geëxporteerd. Maar omdat Azure Migrate Servicetoewijzing gebruikt voor de visualisatie van afhankelijkheden, kunt u de [Servicetoewijzing rest API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) gebruiken om de afhankelijkheden in JSON-indeling op te halen.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Hoe kan ik de installatie van micro soft Monitoring Agent (MMA) en de afhankelijkheids agent automatiseren?

Gebruik dit [script om de afhankelijkheids agent te installeren](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Volg deze [instructies voor het installeren van MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) met behulp van de opdracht regel of de automatisering. Voor MMA gebruikt u [Dit script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Naast scripts kunt u ook implementatie hulpprogramma's als System Center Configuration Manager en [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) gebruiken om de agents te implementeren.

### <a name="what-operating-systems-are-supported-by-mma"></a>Welke besturings systemen worden ondersteund door MMA?

- Bekijk de lijst met [Windows-besturings systemen die worden ondersteund door MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Bekijk de lijst met [Linux-besturings systemen die worden ondersteund door MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>Welke besturings systemen worden ondersteund door de afhankelijkheids agent?

Bekijk de lijst met [Windows-en Linux-besturings systemen die Azure monitor voor VM's ondersteunt](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Kan ik afhankelijkheden in Azure Migrate meer dan een uur visualiseren?
Nee. U kunt afhankelijkheden tot een uur visualiseren. U kunt teruggaan naar een bepaalde datum in geschiedenis, tot nu toe per maand, maar de maximale duur voor visualisatie is een uur. U kunt bijvoorbeeld de tijds duur in de afhankelijkheids toewijzing gebruiken om afhankelijkheden voor gisteren weer te geven, maar u kunt deze alleen bekijken voor een venster van één uur. U kunt echter Azure Monitor-Logboeken gebruiken om een langere duur van [afhankelijkheids gegevens](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) op te vragen.

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>Kan ik afhankelijkheids visualisatie gebruiken voor groepen die meer dan 10 Vm's bevatten?
U kunt [afhankelijkheden visualiseren voor groepen](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) die Maxi maal 10 vm's bevatten. Als u een groep hebt met meer dan tien Vm's, raden we u aan de groep te splitsen in kleinere groepen en vervolgens de afhankelijkheden te visualiseren.

## <a name="azure-migrate-server-migration"></a>Migratie van Azure Migrate server

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Wat is het verschil tussen Azure Migrate server migratie en Azure Site Recovery?

Azure Migrate server migratie maakt gebruik van de Site Recovery-replicatie-engine voor de migratie van virtuele VMware-machines op basis van een agent, migratie van virtuele Hyper-V-machines en migratie van fysieke servers naar Azure. De optie voor het migreren van virtuele VMware-machines is standaard ingebouwd in server migratie.

## <a name="next-steps"></a>Volgende stappen
Lees het [Azure migrate overzicht](migrate-services-overview.md).
