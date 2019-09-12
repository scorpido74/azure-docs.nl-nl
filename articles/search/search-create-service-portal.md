---
title: 'Quickstart: Een Azure Search-service maken in de portal - Azure Search'
description: Een Azure Search-resource inrichten in de Microsoft Azure-portal-portal. Resourcegroepen, regio's en de SKU of prijscategorie kiezen.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 09/10/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 483810f89ea4bbb3a68e616929bd7d752c4d509f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883849"
---
# <a name="quickstart-create-an-azure-search-service-in-the-portal"></a>Quickstart: Een Azure Search-service maken in de portal

Azure Search is een zelfstandige resource die wordt gebruikt voor het toevoegen van een zoekervaring aan aangepaste apps. Hoewel Azure Search eenvoudig integreert met andere Azure-Services, kunt u het ook gebruiken als zelfstandig onderdeel, of het integreren met apps op netwerk servers, of met software die wordt uitgevoerd op andere Cloud platforms.

Dit artikel bevat meer informatie over hoe u een Azure Search-resource in [Azure Portal](https://portal.azure.com/) kunt maken.

[![GIF-animatie](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Hebt u liever PowerShell? Gebruik de [servicesjabloon](https://azure.microsoft.com/resources/templates/101-azure-search-create/) van Azure Resource Manager. Zie [Azure Search beheren met PowerShell](search-manage-powershell.md) voor hulp om aan de slag te gaan.

## <a name="subscribe-free-or-paid"></a>Abonneren (gratis of betaald)

[Maak een gratis Azure-account](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) en gebruik gratis tegoed om betaalde Azure-services uit te proberen. Nadat u het tegoed hebt opgemaakt, kunt u het account houden en de gratis Azure-services, zoals Websites, blijven gebruiken. Er wordt nooit geld van uw creditcard afgeschreven, tenzij u uw instellingen zelf wijzigt en expliciet aangeeft dat u wilt betalen.

U kunt ook [voordelen voor MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Als u een MSDN-abonnement hebt, ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken. 

## <a name="find-azure-search"></a>Azure Search vinden

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik op het plusteken (+ Resource maken) in de linkerbovenhoek.
3. Gebruik de zoekbalk om te zoeken naar 'Azure Search' of ga naar de resource via **Web** > **Azure Search**.

![Naar een Azure Search-resource navigeren](./media/search-create-service-portal/find-search3.png "Navigatiepad naar Azure Search")

## <a name="select-a-subscription"></a>Selecteer een abonnement

Als u meer dan één abonnement hebt, moet u er één kiezen dat ook services voor gegevensopslag of File Storage-services biedt. Azure Search kunnen Azure Table-en Blob-opslag, SQL Database en Azure Cosmos DB voor indexering automatisch detecteren via [*Indexeer functies*](search-indexer-overview.md), maar alleen voor services onder hetzelfde abonnement.

## <a name="set-a-resource-group"></a>Een resource groep instellen

Een resource groep is vereist en is handig voor het beheren van resources, met inbegrip van kosten beheer. Een resource groep kan bestaan uit één service of meerdere services die samen worden gebruikt. Als u bijvoorbeeld Azure Search gebruikt voor het indexeren van een Azure Cosmos DB-Data Base, kunt u beide services onderdeel maken van dezelfde resource groep voor beheer doeleinden. 

Als u resources niet in één groep combineert of als bestaande resourcegroepen resources bevatten die worden gebruikt in oplossingen waartussen geen verband bestaat, maakt u een nieuwe resourcegroep voor uw Azure Search-resource. 

Wanneer u de service gebruikt, kunt u de huidige en geschatte kosten bijhouden (zoals weer gegeven in de scherm opname) of omlaag schuiven om de kosten voor afzonderlijke resources weer te geven.

![Kosten beheren op het niveau van de resource groep](./media/search-create-service-portal/resource-group-cost-management.png "Kosten beheren op het niveau van de resource groep")

> [!TIP]
> Als u een resourcegroep verwijdert, worden de services binnen die resourcegroep ook verwijderd. Bij prototypeprojecten die gebruikmaken van meerdere services, wordt het opschonen na afloop van het project gemakkelijker als die services zich allemaal in dezelfde resourcegroep bevinden.

## <a name="name-the-service"></a>Naam van de service

Geef in de details van het exemplaar een service naam op in het veld **URL** . De naam maakt deel uit van het URL-eind punt waartegen API-aanroepen worden uitgegeven: `https://your-service-name.search.windows.net`. Als u bijvoorbeeld wilt dat het eindpunt `https://myservice.search.windows.net` wordt, voert u `myservice` in.

Vereisten voor servicenaam:

* De naam moet uniek zijn binnen de naamruimte search.windows.net
* tussen de 2 en 60 tekens lang zijn
* Gebruik kleine letters, cijfers of streepjes (-)
* Vermijd streepjes (-) bij de eerste 2 tekens of als het allerlaatste teken
* Zorg ervoor dat er nergens twee opeenvolgende streepjes (--) staan

> [!TIP]
> Als u denkt dat u meerdere services gaat gebruiken, is het raadzaam om de regio (of locatie) in de service naam op te nemen als een naamgevings Conventie. Services binnen dezelfde regio kunnen zonder kosten worden uitgewisseld, dus als Azure Search zich in VS West bevindt en u andere services hebt in het VS-West, `mysearchservice-westus` kunt u met een naam op de pagina eigenschappen een reis opslaan in een bestand met de informatie over het combi neren of koppelen van resources.

## <a name="choose-a-location"></a>Locatie kiezen

Azure Search kan als Azure-service worden gehost in datacenters over de hele wereld. De lijst met ondersteunde regio's kunt u vinden op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/). 

U kunt de bandbreedte kosten minimaliseren of vermijden door dezelfde locatie voor meerdere services te kiezen. Als u bijvoorbeeld gegevens indexeert die door een andere Azure-service zijn verschaft (Azure Storage, Azure Cosmos DB, Azure SQL Database), voor komt u dat de Azure Search service in dezelfde regio bandbreedte kosten (er worden geen kosten in rekening gebracht voor uitgaande gegevens wanneer de services zich bevinden). dezelfde regio).

Daarnaast kunt u, als u verrijkingen van cognitieve Zoek opdrachten AI gebruikt, uw service maken in dezelfde regio als uw Cognitive Services-resource. *De co-locatie van Azure Search en Cognitive Services in dezelfde regio is een vereiste voor AI-verrijking*.

> [!Note]
> Centraal-India is momenteel niet beschikbaar voor nieuwe services. Voor services die zich al in Centraal-India bevinden, kunt u opschalen zonder beperkingen en wordt uw service volledig ondersteund in die regio. De beperking voor deze regio is tijdelijk en beperkt tot alleen nieuwe services. Deze notitie wordt verwijderd wanneer de beperking niet meer van toepassing is.

## <a name="choose-a-pricing-tier-sku"></a>Een prijs categorie kiezen (SKU)

[Azure Search wordt momenteel aangeboden in verschillende prijscategorieën](https://azure.microsoft.com/pricing/details/search/): Gratis, Basic of Standard. Elke categorie heeft eigen [capaciteiten en limieten](search-limits-quotas-capacity.md). Raadpleeg [Choose a pricing tier or SKU](search-sku-tier.md) (Een prijscategorie of SKU kiezen) voor hulp.

Basic en Standard zijn de meest voorkomende opties voor productie werkbelastingen, maar de meeste klanten beginnen met de gratis service. De belangrijkste verschillen tussen lagen zijn de partitie grootte en de snelheid, en limieten voor het aantal objecten dat u kunt maken.

Houd er rekening mee dat een prijs categorie niet kan worden gewijzigd nadat de service is gemaakt. Als u een hogere of lagere categorie nodig hebt, moet u de service opnieuw maken.

## <a name="create-your-service"></a>Uw service maken

Nadat u de benodigde invoer hebt gemaakt, gaat u verder met het maken van de service. 

![De service controleren en maken](./media/search-create-service-portal/new-service3.png "De service controleren en maken")

Uw service wordt binnen enkele minuten geïmplementeerd, die u via meldingen van Azure kunt bewaken. U kunt de service vastmaken aan uw dash board voor een snelle toegang in de toekomst.

![De service controleren en vastmaken](./media/search-create-service-portal/monitor-notifications.png "De service controleren en vastmaken")

## <a name="get-a-key-and-url-endpoint"></a>Een sleutel en het URL-eindpunt ophalen

Tenzij u de portal gebruikt, moet u op programmeer niveau toegang tot uw nieuwe service het URL-eind punt en een verificatie-API-sleutel opgeven.

1. Zoek en kopieer op de overzichtspagina van de service het URL-eindpunt aan de rechterkant van de pagina.

2. Selecteer **Sleutels** in het navigatiedeelvenster links en kopieer een van de beheersleutels (deze zijn equivalent). API-beheersleutels voor beheerders zijn vereist voor het maken, bijwerken en verwijderen van objecten in uw service.

   ![Overzichtspagina met URL-eindpunt](./media/search-create-service-portal/get-url-key.png "URL-eindpunt en andere details van de service")

Een eindpunt en sleutel zijn niet nodig voor op de portal gebaseerde taken. De portal is al gekoppeld aan uw Azure Search-resource met beheerdersrechten. Begin met [Quick start voor een portal-overzicht: Een Azure Search-index maken in de](search-get-started-portal.md)Portal.

## <a name="scale-your-service"></a>Uw service schalen

Nadat uw service is geconfigureerd, kunt u deze schalen, zodat de service aan uw behoeften voldoet. Als u de Standard-laag voor uw Azure Search-service hebt gekozen, kunt u uw service in twee dimensies schalen: replica's en partities. Als u had gekozen voor de categorie Basic, had u alleen replica's kunnen toevoegen. Als u had gekozen voor de gratis service, had u niet kunnen schalen.

***Partities*** zorgen ervoor dat uw service meer documenten bewaart en meer documenten doorzoekt.

***Replica's*** zorgen ervoor dat uw services meer zoekquery's kunnen verwerken.

Als u resources toevoegt, wordt uw maandfactuur hoger. Met de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) krijgt u inzicht in het effect van het toevoegen van resources op uw facturering. Onthoud dat u resources kunt aanpassen aan de benodigde capaciteit. Zo kunt u eerst resources toevoegen voor het maken van een volledige eerste index en later het aantal resources weer verlagen tot een niveau dat geschikt is voor incrementele indexering.

> [!Important]
> Een service moet [twee replica's hebben voor een alleen-lezen-SLA en drie replica's voor een lezen/schrijven-SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Ga naar de pagina van uw zoekservice in Azure Portal.
2. Selecteer in het navigatiedeelvenster aan de linkerkant **Instellingen** > **Schalen**.
3. Gebruik de schuifbalk om resources van een bepaald type toe te voegen.

![Capaciteit toevoegen](./media/search-create-service-portal/settings-scale.png "Capaciteit toevoegen via replica's en partities")

> [!Note]
> Opslag per partitie en snelheid neemt toe bij hogere lagen. Zie [capaciteit en limieten](search-limits-quotas-capacity.md)voor meer informatie.

## <a name="when-to-add-a-second-service"></a>Wanneer u een tweede service moet toevoegen

De meeste klanten gebruiken maar één service die geconfigureerd is voor de categorie die de [juiste balans voor resources](search-sku-tier.md) biedt. Eén service kan meerdere indexen hosten, afhankelijk van de [maximale limieten van de door u geselecteerde categorie](search-capacity-planning.md), en alle indexen zijn van elkaar geïsoleerd. In Azure Search kunnen aanvragen alleen worden gericht aan één index, waardoor de kans dat er per ongeluk of opzettelijk gegevens worden opgehaald uit andere indexen in dezelfde service, wordt geminimaliseerd.

Hoewel de meeste klanten slechts één service gebruiken, kan serviceredundantie nodig zijn als de volgende operationele vereisten gelden:

* Herstel na noodgevallen (onderbreking datacentrum). Azure Search biedt geen onmiddellijke failover in geval van een storing. Raadpleeg voor aanbevelingen en richtlijnen [Servicebeheer](search-manage.md).
* Uit uw onderzoek naar multitenancymodellering blijkt dat een ontwerp met meerdere services optimaal is. Raadpleeg voor meer informatie [Ontwerp voor multitenancy](search-modeling-multitenant-saas-applications.md).
* Voor globaal geïmplementeerde toepassingen kan het nodig zijn dat u een exemplaar van Azure Search in meerdere regio's plaatst om de latentie van het internationale verkeer in uw toepassing te minimaliseren.

> [!NOTE]
> In Azure Search kunt u indexering en query bewerkingen niet scheiden. Daarom zou u nooit meerdere services voor gescheiden werk belastingen maken. Een query wordt altijd uitgevoerd op de service waarin de index is gemaakt (u kunt niet een index in een service maken en vervolgens naar een andere service kopiëren).

Een tweede service is niet vereist voor hoge beschikbaarheid. Hoge beschikbaarheid voor query's wordt bereikt wanneer u twee of meer replica's in dezelfde service gebruikt. Replica-updates zijn sequentieel, wat betekent dat er ten minste één operationeel is wanneer er een service-update wordt geïmplementeerd. Voor meer informatie over uptime raadpleegt u [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Volgende stappen

Nadat u een Azure Search-service hebt ingericht, kunt u in de portal verdergaan met het maken van uw eerste index.

> [!div class="nextstepaction"]
> [Snelstart: Een Azure Search-index maken in de portal](search-get-started-portal.md)
