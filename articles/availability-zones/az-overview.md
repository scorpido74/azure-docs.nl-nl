---
title: Regio's en Beschikbaarheidszones in azure
description: Meer informatie over regio's en Beschikbaarheidszones in azure om te voldoen aan uw technische en wettelijke vereisten.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 04/28/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 9014d446b29b9a81a807c002cd7f83a2c3bdaa51
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231336"
---
# <a name="regions-and-availability-zones-in-azure"></a>Regio's en Beschikbaarheidszones in azure

Microsoft Azure Services zijn wereld wijd beschikbaar om uw Cloud bewerkingen op een optimaal niveau te stimuleren. U kunt de beste regio voor uw behoeften kiezen op basis van technische en reglementaire overwegingen: service mogelijkheden, gegevens locatie, nalevings vereisten en latentie.

## <a name="terminology"></a>Terminologie

Als u meer wilt weten over regio's en Beschikbaarheidszones in azure, kunt u de belangrijkste termen of concepten begrijpen.

| Term of concept | Beschrijving |
| --- | --- |
| regio | Een reeks data centers die zijn geïmplementeerd binnen een latentie definitie en verbonden zijn via een toegewezen regionaal netwerk met lage latentie. |
| Geografie | Een gebied van de wereld met ten minste één Azure-regio. Met geografi wordt een discrete markt gedefinieerd die de grenzen van gegevens locatie en-naleving behoudt. Klanten met specifieke behoeften ten aanzien van gegevenslocatie en naleving, kunnen met geografische gebieden hun gegevens en toepassingen in de buurt houden. Geografi zijn fout tolerante problemen met betrekking tot de volledige regio fout via de verbinding met onze specifieke netwerk infrastructuur met hoge capaciteit. |
| Beschikbaarheidszone | Unieke fysieke locaties binnen een regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. |
| Aanbevolen regio | Een regio die het breedste scala aan service mogelijkheden biedt en is ontworpen om Beschikbaarheidszones nu of in de toekomst te ondersteunen. Deze worden in de Azure Portal zoals **Aanbevolen**aangeduid. |
| alternatieve regio (overige) | Een regio die de footprint van Azure uitbreidt binnen een gegevens locatie grens waar een aanbevolen regio ook zich bevindt. Alternatieve regio's helpen latentie te optimaliseren en bieden een tweede regio voor nood herstel behoeften. Ze zijn niet ontworpen ter ondersteuning van Beschikbaarheidszones (hoewel Azure een regel matige evaluatie van deze regio's uitvoert om te bepalen of ze aanbevolen regio's moeten worden). Deze worden in de Azure Portal aangeduid als **andere**. |
| Foundational service | Een kern service van Azure die beschikbaar is in alle regio's wanneer de regio algemeen beschikbaar is. |
| mainstream service | Een Azure-service die beschikbaar is in alle aanbevolen regio's binnen 12 maanden na de algemene Beschik baarheid van de regio/service of de beschik baarheid op basis van de vraag in alternatieve regio's. |
| gespecialiseerde service | Een Azure-service die Beschik baarheid via de vraag heeft over regio's die worden ondersteund door aangepaste/gespecialiseerde hardware. |
| regionale service | Een Azure-service die regionaal wordt geïmplementeerd en de klant in staat stelt de regio op te geven waarin de service wordt geïmplementeerd. Zie voor een volledige lijst [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| niet-regionale service | Een Azure-service waarvoor geen afhankelijkheid geldt voor een specifieke Azure-regio. Niet-regionale services worden geïmplementeerd in twee of meer regio's en als er sprake is van een regionale fout, blijft het exemplaar van de service in een andere regio het onderhoud van klanten voort. Zie voor een volledige lijst [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regio's

Een regio is een set data centers die worden geïmplementeerd binnen een latentie definitie en verbonden zijn via een toegewezen regionaal netwerk met lage latentie. Azure biedt u de flexibiliteit om toepassingen te implementeren waar u behoefte aan hebt, met inbegrip van meerdere regio's voor het leveren van flexibele tolerantie. Zie [overzicht van de tolerantie pijler](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)voor meer informatie.

## <a name="availability-zones"></a>Beschikbaarheidszones

Een beschikbaarheids zone is een aanbieding met hoge Beschik baarheid die uw toepassingen en gegevens beveiligt tegen Data Center-fouten. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen op zoneniveau. Zone-redundante Services repliceren uw toepassingen en gegevens op Beschikbaarheidszones om te beschermen tegen enkele punten van een storing. Met beschikbaarheidszones biedt Azure de beste uptime SLA voor VM’s van de branche, van 99,99%. In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

Een beschikbaarheidszone in een Azure-regio is een combinatie van een foutdomein en een updatedomein. Als u bijvoorbeeld drie of meer virtuele machines in drie zones in een Azure-regio maakt, worden uw virtuele machines effectief over drie foutdomeinen en drie updatedomeinen verdeeld. Het Azure-platform herkent deze verdeling over updatedomeinen om ervoor te zorgen dat virtuele machines in verschillende zones niet op hetzelfde moment worden bijgewerkt.

Bouw hoge Beschik baarheid in uw toepassings architectuur door uw berekenings-, opslag-, netwerk-en gegevens bronnen in een zone te plaatsen en te repliceren in andere zones. Azure-services die ondersteuning bieden voor beschikbaarheidszones worden onderverdeeld in twee categorieën:

- **Zonegebonden Services** : waarbij een bron wordt vastgemaakt aan een specifieke zone (bijvoorbeeld virtuele machines, beheerde schijven, standaard-IP-adressen) of
- **Zone-redundante Services** : wanneer het Azure-platform automatisch repliceert in zones (bijvoorbeeld zone-redundante opslag, SQL database).

Als u een uitgebreide bedrijfs continuïteit wilt bereiken op Azure, bouwt u uw toepassings architectuur met de combi natie van Beschikbaarheidszones met Azure-regio paren. U kunt uw toepassingen en gegevens synchroon repliceren met Beschikbaarheidszones binnen een Azure-regio voor hoge Beschik baarheid en asynchroon repliceren in azure-regio's voor nood herstel beveiliging.
 
![Conceptueel overzicht van één zone die in een regio wordt weer gegeven](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> De id's van de beschikbaarheids zone (de getallen 1, 2 en 3 in de bovenstaande afbeelding) worden logisch toegewezen aan de werkelijke fysieke zones voor elk abonnement, onafhankelijk van elkaar. Dit betekent dat Beschik baarheid Zone 1 in een bepaald abonnement kan verwijzen naar een andere fysieke zone dan de beschik baarheid Zone 1 in een ander abonnement. Als gevolg hiervan wordt het aanbevolen om niet te vertrouwen op de beschikbaarheids zone-Id's tussen verschillende abonnementen voor plaatsing van virtuele machines.

## <a name="region-and-service-categories"></a>Regio-en service Categorieën

De benadering van Azure voor de beschik baarheid van Azure-Services in verschillende regio's wordt het beste beschreven door de services die beschikbaar zijn in Aanbevolen regio's en alternatieve regio's.

- **Aanbevolen regio** : een regio die het breedste scala aan service mogelijkheden biedt en is ontworpen om Beschikbaarheidszones nu of in de toekomst te ondersteunen. Deze worden in de Azure Portal zoals **Aanbevolen**aangeduid.
- **Alternatieve (andere) regio** : een regio die de footprint van Azure uitbreidt binnen een Data locatie-grens waarbij een aanbevolen regio ook bestaat. Alternatieve regio's helpen latentie te optimaliseren en bieden een tweede regio voor nood herstel behoeften. Ze zijn niet ontworpen ter ondersteuning van Beschikbaarheidszones (hoewel Azure een regel matige evaluatie van deze regio's uitvoert om te bepalen of ze aanbevolen regio's moeten worden). Deze worden in de Azure Portal aangeduid als **andere**.

### <a name="comparing-region-types"></a>Regio typen vergelijken

Azure-Services zijn onderverdeeld in drie categorieën: Foundational, mainstream en gespecialiseerde services. Het algemene beleid van Azure voor het implementeren van services in een bepaalde regio wordt voornamelijk aangestuurd door regio type, service categorieën en vraag van de klant:

- **Basis** : beschikbaar in alle aanbevolen en alternatieve regio's wanneer de regio algemeen beschikbaar is, of binnen een periode van 12 maanden nadat een nieuwe Foundational service algemeen beschikbaar wordt.
- **Mainstream** : beschikbaar in alle aanbevolen regio's binnen 12 maanden na de algemene Beschik baarheid van de regio/service; Demand-aangedreven in alternatieve regio's (veel zijn al geïmplementeerd in een grote subset van alternatieve regio's).
- **Gespecialiseerde** , gerichte service aanbiedingen, vaak in de branche gericht of ondersteund door aangepaste/gespecialiseerde hardware. Beschik baarheid via de vraag in verschillende regio's (veel worden al in een grote subset aanbevolen regio's geïmplementeerd).

Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=all)om te zien welke services worden geïmplementeerd in een bepaalde regio, evenals het toekomstige schema voor preview of algemene Beschik baarheid van services in een regio.

Als een service aanbieding niet beschikbaar is in een specifieke regio, kunt u uw interesse delen door contact op te nemen met uw micro soft-verkoop medewerker.

| Gebieds type | Niet-regionale | Fundamentele | Meest | Gespecialiseerd | Beschikbaarheidszones | Gegevenslocatie |
| --- | --- | --- | --- | --- | --- | --- |
| Aanbevolen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Op aanvraag gebaseerd | :heavy_check_mark: | :heavy_check_mark: |
| Alternatief | :heavy_check_mark: | :heavy_check_mark: | Op aanvraag gebaseerd | Op aanvraag gebaseerd | N.v.t. | :heavy_check_mark: |

### <a name="services-by-category"></a>Services per categorie

Zoals eerder vermeld, classificeert Azure Services in drie categorieën: basis, mainstream en gespecialiseerd. Service categorieën worden toegewezen aan de algemene Beschik baarheid. Vaak starten Services hun levens cyclus als een gespecialiseerde service en kunnen de benodigde verhogingen worden bevorderd tot mainstream of basis. De volgende tabel bevat de categorie voor services als Foundational, mainstream of gespecialiseerd. Let op het volgende over de tabel:

- Sommige services zijn niet regionaal. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/)voor meer informatie en een lijst met niet-regionale Services.
- Virtuele machines van de vorige generatie worden niet weer gegeven. Raadpleeg voor meer informatie de documentatie bij [eerdere generaties van de grootte van virtuele machines](../virtual-machines/sizes-previous-gen.md).

> [!div class="mx-tableFixed"]
> | Fundamentele | Meest | Gespecialiseerd |
> | --- | --- | --- |
> | Account opslag | API Management | Azure-API voor FHIR |
> | Application Gateway | App Configuration | Azure Blockchain-service |
> | Azure Backup | App Service | Azure Blueprints |
> | Azure Cosmos DB | Automation | Azure Database for MariaDB |
> | Azure Data Lake Storage Gen2 | Azure Active Directory Domain Services | Azure toegewezen HSM |
> | Azure ExpressRoute | Azure Analysis Services | Azure Dev Spaces |
> | Azure SQL Database | Azure Bastion | Azure Digital Twins |
> | Cloud Services | Azure Cache voor Redis | Azure Lab-Services |
> | Cloud Services: Av2-serie | Azure Cognitive Search | Azure NetApp Files |
> | Cloud Services: dv2-serie | Azure Data Explorer | Azure-Quantum |
> | Cloud Services: Dv3-serie | Azure Data Share | Azure Time Series Insights |
> | Cloud Services: Ev3-serie | Azure Database for MySQL | Azure VMware Solution by CloudSimple |
> | Cloud Services: Ip's op exemplaar niveau | Azure Database for PostgreSQL | Cloud Services: A8-A11 (reken intensief) |
> | Cloud Services: Gereserveerd IP | Azure Database Migration Service | Cloud Services: G-serie |
> | Disk Storage | Azure Databricks | Cloud Services: H-serie |
> | Event Hubs | Azure DDoS Protection | Cognitive Services: anomalie detectie |
> | Key Vault | Azure DevTest Labs | Cognitive Services: Custom Vision |
> | Load balancer | Azure Firewall Manager | Cognitive Services: Speaker Recognition |
> | Service Bus | Azure Firewall | Data Box Heavy |
> | Service Fabric | Azure Functions | Data Catalog |
> | Virtual Machine Scale Sets | Azure HPC Cache | Data Factory: Data Factory v1 |
> | Virtuele machines | Azure IoT Hub | Data Lake Analytics |
> | Virtual Machines: Av2-serie | Azure Kubernetes Service (AKS) | Machine Learning Studio |
> | Virtual Machines: BS-serie | Azure Machine Learning | Microsoft Genomics |
> | Virtual Machines: DSv2-serie | Azure Private Link | Remote Rendering |
> | Virtual Machines: DSv3-serie | Azure Red Hat OpenShift | Spatial Anchors |
> | Virtual Machines: dv2-serie | Azure Site Recovery | StorSimple |
> | Virtual Machines: Dv3-serie | Azure lente-Cloud service | Video Indexer |
> | Virtual Machines: ESv3-serie | Azure Stack hub | Virtual Machines: A8-A11 (reken intensief) |
> | Virtual Machines: Ev3-serie | Azure Stream Analytics | Virtual Machines: DASv4-serie |
> | Virtual Machines: F-serie | Azure Synapse Analytics | Virtual Machines: DAv4-serie |
> | Virtual Machines: FS-serie | Azure SignalR Service | Virtual Machines: DCsv2-serie |
> | Virtual Machines: Ip's op exemplaar niveau | Batch | Virtual Machines: EASv4-serie |
> | Virtual Machines: Gereserveerd IP | Cloud Services: M-serie | Virtual Machines: EAv4-serie |
> | Virtual Network | Cognitive Services | Virtual Machines: G-serie |
> | VPN Gateway | Cognitive Services: Computer Vision | Virtual Machines: GS-serie |
> |  | Cognitive Services: Content Moderator | Virtual Machines: HBv1-serie |
> |  | Cognitive Services: Face | Virtual Machines: HBv2-serie |
> |  | Cognitive Services: Language Understanding | Virtual Machines: HCv1-serie |
> |  | Cognitive Services: spraak Services | Virtual Machines: H-serie |
> |  | Cognitive Services: QnA Maker | Virtual Machines: LS-serie |
> |  | Container Instances | Virtual Machines: LSv2-serie |
> |  | Container Registry | Virtual Machines: Mv2-serie |
> |  | Data Factory | Virtual Machines: NC-serie |
> |  | Event Grid | Virtual Machines: NCv2-serie |
> |  | HDInsight | Virtual Machines: NCv3-serie |
> |  | Logic Apps | Virtual Machines: NDs-serie |
> |  | Media Services | Virtual Machines: NDv2-serie |
> |  | Network Watcher | Virtual Machines: NV-serie |
> |  | Notification Hubs | Virtual Machines: NVv3-serie |
> |  | Power BI Embedded | Virtual Machines: NVv4-serie |
> |  | Premium-Blob Storage | Virtual Machines: SAP HANA on Azure Large Instances |
> |  | Premium files-opslag | Visual Studio App Center |
> |  | Opslag: Archive Storage |  |
> |  | Ultra Disk Storage |  |
> |  | Virtual Machines: Fsv2-serie |  |
> |  | Virtual Machines: M-serie |  |
> |  | Virtuele WAN |  |

###  <a name="services-resiliency"></a>Tolerantie van services

Alle Azure Management-Services zijn ontworpen om te worden flexibeler dan storingen op regio niveau. In het spectrum van fouten, hebben een of meer fouten in de beschikbaarheids zone binnen een regio een kleinere fout RADIUS vergeleken met een volledige regio fout. Azure kan worden hersteld van een storing op zone niveau voor beheer services binnen de regio of vanuit een andere Azure-regio. Azure voert een kritieke onderhouds bewerking uit binnen een regio, om te voor komen dat er fouten optreden die van invloed zijn op klant resources die zijn geïmplementeerd op Beschikbaarheidszones binnen een regio.

### <a name="pricing-for-vms-in-availability-zones"></a>Prijzen voor Vm's in Beschikbaarheidszones

Er zijn geen extra kosten verbonden aan het implementeren van virtuele machines in een beschikbaarheids zone. SLA voor de VM-uptime van 99,99% wordt aangeboden wanneer er twee of meer virtuele machines zijn geïmplementeerd in twee of meer Beschikbaarheidszones binnen een Azure-regio. Er zijn extra kosten voor de gegevens overdracht van VM-naar-VM-zone met een andere Beschik baarheid. Bekijk de pagina met [bandbreedte prijzen](https://azure.microsoft.com/pricing/details/bandwidth/) voor meer informatie.

### <a name="get-started-with-availability-zones"></a>Aan de slag met Beschikbaarheidszones

- [Een virtuele machine maken](../virtual-machines/windows/create-portal-availability-zone.md)
- [Een beheerde schijf toevoegen met Power shell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Een door een zone redundante schaalset voor virtuele machines maken](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Taak verdeling van virtuele machines in zones met behulp van een Standard Load Balancer met een zone-redundante front-end](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Taak verdeling van Vm's binnen een zone met behulp van een Standard Load Balancer met een zonegebonden-front-end](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zone-redundante opslag](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Geo-noodherstel Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geo-noodherstel Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Een zone-redundante virtuele netwerkgateway maken](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Zone redundante regio voor Azure Cosmos DB toevoegen](../cosmos-db/high-availability.md#availability-zone-support)
- [Aan de slag met Azure cache voor redis Beschikbaarheidszones](https://aka.ms/redis/az/getstarted)
- [Een Azure Active Directory Domain Services-instantie maken](../active-directory-domain-services/tutorial-create-instance.md)
- [Een AKS-cluster (Azure Kubernetes service) maken dat gebruikmaakt van Beschikbaarheidszones](../aks/availability-zones.md)

## <a name="next-steps"></a>Volgende stappen

- [Regio's die ondersteuning bieden voor Beschikbaarheidszones in azure](az-region.md)
- [Snelstartsjablonen](https://aka.ms/azqs)
