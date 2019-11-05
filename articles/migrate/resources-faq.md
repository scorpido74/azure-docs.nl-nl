---
title: Veelgestelde vragen over Azure Migrate
description: Veelvoorkomende en veelgestelde vragen over Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: snehaa
ms.openlocfilehash: 1663e1d418610bcfa2ada28770944bfc8cbf908f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480065"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: algemene vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Migrate. Als u na het lezen van dit artikel nog meer query's hebt uitgevoerd, plaatst u deze op het [Azure migrate forum](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Algemeen

### <a name="which-azure-geographies-are-supported"></a>Welke Azure-geografische gebieden worden ondersteund?

Bekijk de Azure Migrate ondersteunde geographs voor [VMware VM](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) en voor [virtuele Hyper-V-machines](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Wat is het verschil tussen Azure Migrate en Site Recovery?

Azure Migrate biedt een gecentraliseerde hub voor het beheren en bijhouden van de detectie, beoordeling en migratie van on-premises machines en workloads naar Azure. [Azure site Recovery](../site-recovery/site-recovery-overview.md) is een oplossing voor nood herstel. Azure Migrate: het hulp programma voor server migratie maakt gebruik van een back-end-Site Recovery functionaliteit voor het migreren van een of meer on-premises machines.

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Hoe kan ik een Azure Migrate project verwijderen?

Volg [deze instructies](how-to-delete-project.md) voor het verwijderen van een project. [Bekijk de resources](how-to-delete-project.md#created-resources) die zijn gemaakt bij het detecteren, evalueren en migreren van machines en workloads in een Azure migrate-project.


## <a name="azure-migrate-appliance"></a>Azure Migrate-apparaat

### <a name="how-does-the-appliance-connect-to-azure"></a>Hoe maakt het apparaat verbinding met Azure?

De verbinding kan via internet zijn of Azure ExpressRoute gebruiken met open bare/micro soft-peering.

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Welke netwerk verbinding is nodig voor de evaluatie en migratie van Azure Migrate server?

Bekijk de [VMware](migrate-support-matrix-vmware.md) -en [Hyper-V-](migrate-support-matrix-hyper-v.md) ondersteunings matrices voor informatie over de url's en poorten die nodig zijn om Azure migrate te communiceren met Azure.

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>Kan ik de apparaat-VM die is gemaakt met de sjabloon, een harder maken?

U kunt onderdelen (bijvoorbeeld anti virus) aan de sjabloon toevoegen, zolang u de communicatie-en firewall regels die vereist zijn voor de Azure Migrate apparaat als dat niet hebt.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Welke gegevens worden verzameld door het Azure Migrate apparaat?

Controleer als volgt de gegevens die door het apparaat worden verzameld:
- [Prestatie gegevens](migrate-appliance.md#collected-performance-data-vmware) en [meta gegevens](migrate-appliance.md#collected-metadata-vmware) voor virtuele VMware-machines.
- [Prestatie gegevens](migrate-appliance.md#collected-performance-data-hyper-v) en [meta gegevens](migrate-appliance.md#collected-metadata-hyper-v) voor virtuele Hyper-V-machines.


### <a name="does-appliance-analysis-impact-performance"></a>Beïnvloeden de prestaties van het toestel analyse?

Met de Azure Migrate apparaat worden on-premises machines voortdurend op de prestaties van de VM-prestatie gegevens gemeten. Deze profilering heeft bijna geen invloed op de prestaties op de Hyper-V-ESXi-hosts of op vCenter Server.

### <a name="where-and-how-long-is-collected-data-stored"></a>Waar en hoe lang worden verzamelde gegevens opgeslagen?

De gegevens die worden verzameld door het Azure Migrate apparaat, worden opgeslagen op de Azure-locatie die u kiest tijdens het maken van het migratie project. De gegevens worden veilig opgeslagen in een micro soft-abonnement en worden verwijderd wanneer u het Azure Migrate project verwijdert.

Voor visualisatie van afhankelijkheden worden de verzamelde gegevens opgeslagen in het Verenigde Staten, in een Log Analytics werk ruimte die in het Azure-abonnement is gemaakt. Deze gegevens worden verwijderd wanneer u de Log Analytics-werk ruimte in uw abonnement verwijdert. Meer [informatie](concepts-dependency-visualization.md) over de visualisatie van afhankelijkheden.

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>Welk gegevens volume wordt geüpload tijdens een doorlopende Profiler?

Het volume van de gegevens die naar Azure Migrate worden verzonden, is afhankelijk van verschillende para meters. Om u een idee te geven van het volume, stuurt een Azure Migrate project met 10 machines (elk met één schijf en één NIC) ongeveer 50 MB per dag. Deze waarde is van benadering en verandert op basis van het aantal gegevens punten voor de Nic's en schijven. De toename van gegevens die worden verzonden, is niet-lineair als het aantal machines, Nic's of schijven toeneemt.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Zijn de gegevens versleuteld op rust en onderweg?

Ja, beide.
- Meta gegevens worden via internet veilig verzonden naar de Azure Migrate-service via HTTPS.
- De meta gegevens worden opgeslagen in een [Azure Cosmos-data base](../cosmos-db/database-encryption-at-rest.md) en in [Azure Blob-opslag](../storage/common/storage-service-encryption.md) in een micro soft-abonnement. De meta gegevens worden op rest versleuteld.
- De gegevens voor afhankelijkheids analyse worden ook in-transit (beveiligde HTTPS) versleuteld. Deze wordt opgeslagen in een Log Analytics-werk ruimte in uw abonnement. Het is ook versleuteld op rest.

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>Hoe communiceert het apparaat met vCenter Server en Azure Migrate?

1. Het apparaat maakt verbinding met vCenter Server (poort 443) met behulp van de referenties die u hebt opgegeven bij het instellen van het apparaat.
2. Het apparaat gebruikt VMware PowerCLI om vCenter Server te zoeken, om meta gegevens te verzamelen over de Vm's die worden beheerd door vCenter Server. Er worden configuratie gegevens verzameld over Vm's (kernen, geheugen, schijven, Nic's) en de prestatie geschiedenis van elke virtuele machine in de afgelopen maand.
3. De verzamelde meta gegevens worden vervolgens verzonden naar Azure Migrate: Server evaluatie (via internet via HTTPS) voor evaluatie.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Kan ik hetzelfde apparaat verbinden met meerdere exemplaren van vCenter Server?

Nee. Er is een een-op-een-toewijzing tussen een apparaat en vCenter Server. Als u Vm's op meerdere exemplaren van vCenter Server wilt detecteren, moet u meerdere apparaten implementeren.


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>De computer grootte is gewijzigd. Kan ik de evaluatie opnieuw uitvoeren?

Het Azure Migrate apparaat verzamelt voortdurend informatie over de on-premises omgeving. Maar een evaluatie is een tijdgebonden moment opname van on-premises Vm's. Als u de instellingen van een virtuele machine die u wilt beoordelen, wijzigt, gebruikt u de optie opnieuw berekenen om de evaluatie bij te werken met de meest recente wijzigingen.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>Hoe kan ik detectie uitvoeren in een multi tenant omgeving?

- Als uw omgeving wordt gedeeld tussen tenants en u niet wilt dat de Vm's van één Tenant in het abonnement van een andere Tenant worden gedetecteerd, moet u voor VMware vCenter Server referenties maken die alleen toegang hebben tot de Vm's die u wilt detecteren. Gebruik vervolgens deze referenties wanneer u de detectie start in het Azure Migrate apparaat.
- Voor Hyper-V maakt detectie gebruik van referenties voor Hyper-V-hosts. Als Vm's dezelfde Hyper-V-host delen, is er momenteel geen enkele manier om de detectie te scheiden.  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>Hoeveel Vm's kan ik vinden met één apparaat?

U kunt Maxi maal 10.000 VMware-Vm's en Maxi maal 5.000 virtuele Hyper-V-machines detecteren met één migratie apparaat. Als u meer computers in uw on-premises omgeving hebt, leert u hoe u de [Hyper-V-](scale-hyper-v-assessment.md) en [VMware](scale-vmware-assessment.md) -evaluatie kunt schalen.

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Kan ik het Azure Migrate apparaat uit het project verwijderen?

Het verwijderen van het apparaat uit het project wordt momenteel niet ondersteund.

- De enige manier om het apparaat te verwijderen, is door de resource groep te verwijderen die het Azure Migrate project bevat dat aan het apparaat is gekoppeld.
- Als u echter de resource groep verwijdert, worden ook andere geregistreerde apparaten, de gedetecteerde inventaris, beoordelingen en alle andere Azure-onderdelen die zijn gekoppeld aan het project in de resource groep, verwijderd.

## <a name="azure-migrate-server-assessment"></a>Evaluatie van Azure Migrate-server


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>Heeft Azure Migrate vCenter Server nodig voor de detectie van virtuele VMWare-machines?

Ja, Azure Migrate moet vCenter Server om de detectie uit te voeren in een VMware-omgeving. Het biedt geen ondersteuning voor detectie van ESXi-hosts die niet worden beheerd door vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Wat is het verschil tussen Azure Migrate server evaluatie en de kaart Toolkit?

Server evaluatie biedt een evaluatie van de voor bereiding op de migratie en de evaluatie van werk belastingen voor migratie naar Azure. [Micro soft Assessment and planning (kaart) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) helpt bij andere taken, waaronder migratie planning voor nieuwere versies van Windows-besturings systemen voor clients en servers, en het bijhouden van software gebruik. Voor deze scenario's kunt u de kaart Toolkit blijven gebruiken.

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Wat is het verschil tussen server evaluatie en het Site Recovery Deployment Planner?

Server evaluatie is een hulp programma voor migratie planning. De Site Recovery Deployment Planner is een hulp programma voor herstel na nood gevallen. 

- **On-premises migratie naar Azure plannen**: als u van plan bent om uw on-premises servers naar Azure te migreren, gebruikt u Server evaluatie voor migratie planning. Het evalueert on-premises workloads en biedt hulp en hulpprogram ma's waarmee u kunt migreren. Nadat het migratie plan is geïmplementeerd, kunt u hulpprogram ma's, waaronder Azure Migrate server migratie, gebruiken om de machines te migreren naar Azure.
- **Herstel na nood geval naar Azure plannen**: als u van plan bent om herstel na nood gevallen van on-premises naar azure met site Recovery in te stellen, gebruikt u de site Recovery Deployment planner. De Deployment Planner biedt een diep gaande Site Recovery-specifieke evaluatie van uw on-premises omgeving voor nood herstel. Het biedt aanbevelingen voor herstel na nood gevallen, zoals replicatie en failover.

### <a name="does-azure-migrate-estimate-costs-for-the-enterprise-agreement-ea-program"></a>Maakt Azure Migrate kosten voor het Enterprise Agreement EA-programma?

Azure Migrate server analyse ondersteunt momenteel geen kosten ramingen voor het [Enterprise Agreement programma](https://azure.microsoft.com/offers/enterprise-agreement-support/). Als tijdelijke oplossing, wanneer u een evaluatie maakt, kunt u **betalen per gebruik** als **aanbieding**opgeven en het kortings percentage (van toepassing op het abonnement) hand matig toevoegen als de **korting** in de eigenschappen van de beoordeling:

  ![Eigenschappen van beoordeling](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>Wat is het verschil tussen as-on-premises en op prestaties gebaseerde grootte?

Bij een on-premises grootte wordt Azure Migrate geen rekening gehouden met de prestatie gegevens van de virtuele machine voor evaluatie. De VM-grootten worden geëvalueerd op basis van de on-premises configuratie. De grootte van de prestaties is gebaseerd op de gebruiks gegevens.

- Als een on-premises VM bijvoorbeeld 4 kernen en 8 GB aan geheugen heeft met een CPU-gebruik van 50% en 50% geheugen gebruik, gebeurt het volgende:
    - Bij een on-premises grootte wordt een Azure VM-SKU aanbevolen met vier kernen en 8 GB aan geheugen.
    - Op basis van de prestaties wordt een VM-SKU aanbevolen met twee kernen en 4 GB geheugen, omdat het gebruiks percentage wordt overwogen.

- De schijf grootte is ook afhankelijk van twee evaluatie-eigenschappen: grootte criteria en opslag type.
    - Als de grootte criteria op basis van prestaties zijn en het opslag type automatisch is, neemt Azure Migrate de IOPS-en doorvoer waarden van de schijf in rekening wanneer het type doel schijf (Standard of Premium) wordt geïdentificeerd.
    - Als de grootte criteria gebaseerd zijn op prestaties en het opslag type Premium is, raadt Azure Migrate een SKU voor Premium-schijven aan op basis van de grootte van de on-premises schijf. Dezelfde logica wordt toegepast op schijf grootte, wanneer de grootte is ingesteld op on-premises en het opslag type Standard of Premium is.

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>Worden aanbevelingen voor de prestaties van de prestatie geschiedenis en het gebruik van percentiel grootte?

Deze eigenschappen zijn alleen van toepassing wanneer de grootte wordt bepaald op basis van prestaties.

- Azure Migrate verzamelt de prestatie geschiedenis van on-premises machines en gebruikt deze om de VM-grootte en het schijf type in azure aan te bevelen.
- Het apparaat maakt continu gebruik van de on-premises omgeving, zodat gegevens in realtime kunnen worden verzameld om de 20 seconden.
- Het apparaat rolt de voor beelden van 20 seconden samen en maakt één gegevens punt om de 15 minuten.
- Voor het maken van het gegevens punt selecteert het apparaat de piek waarde van alle voor beelden van 20 seconden.
- Het apparaat verzendt dit gegevens punt naar Azure.

Bij het maken van een evaluatie in azure, op basis van de waarde voor prestatie duur en het percentiel van de prestatie geschiedenis, berekent Azure Migrate de juiste gebruiks waarde en wordt deze gebruikt voor het aanpassen van de grootte.

- Als u bijvoorbeeld de duur van de prestaties instelt op één dag en de percentiel waarde op 95 percentiel, sorteert Azure Migrate de voorbeeld punten van 15 minuten die zijn verzonden door de collector voor de afgelopen dag in oplopende volg orde, en wordt de waarde van het 95e percentiel als effectief gekozen gebruik.
- Het gebruik van de 95e percentiel waarde zorgt ervoor dat uitbijters worden genegeerd. Uitschieters kunnen worden opgenomen als u het 99e percentiel gebruikt. Als u het piek gebruik voor de periode wilt kiezen zonder uitbijters te missen, selecteert u het 99e percentiel.

## <a name="server-assessment---dependency-visualization"></a>Server evaluatie-afhankelijkheids visualisatie


### <a name="what-is-dependency-visualization"></a>Wat is de visualisatie van afhankelijkheden?

Gebruik de visualisatie van afhankelijkheden om groepen Vm's te beoordelen op migratie met een grotere betrouw baarheid. Afhankelijkheids visualisatie: meerdere computer afhankelijkheden controleren voordat u een evaluatie uitvoert. Zo kunt u er zeker van zijn dat er niets achter blijft, waardoor onverwachte storingen worden voor komen wanneer u naar Azure migreert. Azure Migrate maakt gebruik van de Servicetoewijzing oplossing in Azure Monitor om de visualisatie van afhankelijkheden in te scha kelen. [meer informatie](concepts-dependency-visualization.md).

> [!NOTE]
> Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Moet ik betalen om de visualisatie van de afhankelijkheid te gebruiken?
Nee. Meer informatie over prijzen voor Azure Migrate vindt u [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Moet ik iets installeren voor de visualisatie van afhankelijkheden?

Als u afhankelijkheids visualisatie wilt gebruiken, moet u agents downloaden en installeren op elke on-premises computer die u wilt evalueren.

U moet de volgende agents op elke computer installeren:
- [Micro soft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Afhankelijkheids agent](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Als u machines hebt zonder Internet verbinding, moet u Log Analytics gateway op deze computers downloaden en installeren.

U hebt deze agents niet nodig tenzij u gebruikmaakt van afhankelijkheids visualisatie.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Kan ik een bestaande werk ruimte gebruiken voor de visualisatie van afhankelijkheden?

Ja, u kunt een bestaande werk ruimte koppelen aan het migratie project en deze gebruiken voor de visualisatie van afhankelijkheden. [Meer informatie](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Kan ik het visualisatie rapport van de afhankelijkheid exporteren?

Nee, de visualisatie van de afhankelijkheid kan niet worden geëxporteerd. Azure Migrate maakt echter gebruik van Servicetoewijzing, en u kunt de [Servicetoewijzing rest API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) gebruiken om de afhankelijkheden in JSON-indeling op te halen.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Hoe kan ik de installatie van micro soft Monitoring Agent (MMA) en de afhankelijkheids agent automatiseren?

Gebruik dit [script om de afhankelijkheids agent te installeren](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Volg deze [instructies voor het installeren van MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) met behulp van de opdracht regel of de automatisering. Voor MMA gebruikt u [Dit script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Naast scripts kunt u ook implementatie hulpprogramma's als System Center Configuration Manager en [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) gebruiken om de agents te implementeren.


### <a name="what-operating-systems-are-supported-by-mma"></a>Welke besturings systemen worden ondersteund door MMA?

- Bekijk de lijst met [Windows-besturings systemen die worden ondersteund door MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Bekijk de lijst met [Linux-besturings systemen die worden ondersteund door MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Kan ik afhankelijkheden langer dan een uur visualiseren?
Nee. U kunt afhankelijkheden tot een uur visualiseren. U kunt teruggaan naar een bepaalde datum in geschiedenis, tot nu toe per maand, maar de maximale duur voor visualisatie is een uur. U kunt bijvoorbeeld de tijds duur in de afhankelijkheids toewijzing gebruiken om afhankelijkheden voor gisteren weer te geven, maar u kunt afhankelijkheden alleen bekijken voor een venster van één uur. U kunt echter Azure Monitor-Logboeken gebruiken om een langere duur van [afhankelijkheids gegevens](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) op te vragen.

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>Kan ik afhankelijkheids visualisatie gebruiken voor groepen van meer dan tien Vm's?
U kunt [afhankelijkheden](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) voor groepen met Maxi maal tien vm's visualiseren. Als u een groep hebt met meer dan tien Vm's, raden we u aan de groep te splitsen in kleinere groepen en vervolgens de afhankelijkheden te visualiseren.

## <a name="azure-migrate-server-migration"></a>Migratie van Azure Migrate server

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Wat is het verschil tussen Azure Migrate server migratie en Site Recovery?

- Migratie van on-premises VMware-Vm's zonder agent is standaard binnen Azure Migrate server migratie.
- Voor migratie van Hyper-V-Vm's, fysieke servers en virtuele VMware-machines op basis van een agent maakt Azure Migrate server migratie gebruik van de Azure Site Recovery-replicatie-engine.


## <a name="next-steps"></a>Volgende stappen
Lees het [Azure migrate overzicht](migrate-services-overview.md).
