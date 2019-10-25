---
title: 'Snelstartgids: een Azure Cognitive Search-service maken in de portal'
titleSuffix: Azure Cognitive Search
description: Een AzureCognitive-Zoek resource inrichten in de Azure Portal. Resourcegroepen, regio's en de SKU of prijscategorie kiezen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 21f55805e0486d987922a1aa160f2938f3a50155
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792442"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Snelstartgids: een Azure Cognitive Search-service maken in de portal

Azure Cognitive Search is een zelfstandige resource die wordt gebruikt om een zoek ervaring in aangepaste apps te koppelen. Hoewel Azure Cognitive Search eenvoudig kan worden geïntegreerd met andere Azure-Services, kunt u het ook gebruiken als zelfstandig onderdeel of het integreren met apps op netwerk servers, of met software die wordt uitgevoerd op andere Cloud platforms.

In dit artikel leert u hoe u een Azure Cognitive Search-resource maakt in de [Azure Portal](https://portal.azure.com/).

[![GIF-animatie](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Hebt u liever PowerShell? Gebruik de [servicesjabloon](https://azure.microsoft.com/resources/templates/101-azure-search-create/) van Azure Resource Manager. Zie [Azure Cognitive Search beheren met Power shell](search-manage-powershell.md)voor hulp bij het aan de slag.

## <a name="subscribe-free-or-paid"></a>Abonneren (gratis of betaald)

[Maak een gratis Azure-account](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) en gebruik gratis tegoed om betaalde Azure-services uit te proberen. Nadat u het tegoed hebt opgemaakt, kunt u het account houden en de gratis Azure-services, zoals Websites, blijven gebruiken. Er worden nooit kosten in rekening gebracht bij uw creditcard tenzij u de instellingen expliciet wijzigt en aangeeft dat u wilt betalen.

U kunt ook [voordelen voor MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Als u een MSDN-abonnement hebt, ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken. 

## <a name="find-azure-cognitive-search"></a>Azure Cognitive Search zoeken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik op het plusteken (+ Resource maken) in de linkerbovenhoek.
3. Gebruik de zoek balk om ' Azure Cognitive Search ' te vinden of navigeer naar de resource via **Web** > **Azure Cognitive Search**.

![Navigeer naar een Azure Cognitive Search-resource](./media/search-create-service-portal/find-search3.png "Het navigatiepad naar Azure Cognitive Search")

## <a name="select-a-subscription"></a>Een abonnement selecteren

Als u meer dan één abonnement hebt, moet u er één kiezen dat ook services voor gegevensopslag of File Storage-services biedt. Azure Cognitive Search kunnen Azure Table-en Blob-opslag, SQL Database en Azure Cosmos DB voor indexering automatisch detecteren via [*Indexeer functies*](search-indexer-overview.md), maar alleen voor services onder hetzelfde abonnement.

## <a name="set-a-resource-group"></a>Een resource groep instellen

Een resource groep is vereist en is handig voor het beheren van resources, met inbegrip van kosten beheer. Een resource groep kan bestaan uit één service of meerdere services die samen worden gebruikt. Als u bijvoorbeeld Azure Cognitive Search gebruikt voor het indexeren van een Azure Cosmos DB-Data Base, kunt u beide services onderdeel maken van dezelfde resource groep voor beheer doeleinden. 

Als u resources niet in één groep combineert of als bestaande resource groepen zijn gevuld met resources die worden gebruikt in niet-gerelateerde oplossingen, maakt u een nieuwe resource groep voor uw Azure Cognitive Search-resource. 

Wanneer u de service gebruikt, kunt u de huidige en geschatte kosten bijhouden (zoals weer gegeven in de scherm opname) of omlaag schuiven om de kosten voor afzonderlijke resources weer te geven.

![Kosten beheren op het niveau van de resource groep](./media/search-create-service-portal/resource-group-cost-management.png "Kosten beheren op het niveau van de resource groep")

> [!TIP]
> Als u een resourcegroep verwijdert, worden de services binnen die resourcegroep ook verwijderd. Bij prototypeprojecten die gebruikmaken van meerdere services, wordt het opschonen na afloop van het project gemakkelijker als die services zich allemaal in dezelfde resourcegroep bevinden.

## <a name="name-the-service"></a>Naam van de service

Geef in de details van het exemplaar een service naam op in het veld **URL** . De naam maakt deel uit van het URL-eind punt waarmee API-aanroepen worden uitgegeven: `https://your-service-name.search.windows.net`. Als u bijvoorbeeld wilt dat het eindpunt `https://myservice.search.windows.net` wordt, voert u `myservice` in.

Vereisten voor servicenaam:

* De naam moet uniek zijn binnen de naamruimte search.windows.net
* tussen de 2 en 60 tekens lang zijn
* Gebruik kleine letters, cijfers of streepjes (-)
* Vermijd streepjes (-) bij de eerste 2 tekens of als het allerlaatste teken
* Zorg ervoor dat er nergens twee opeenvolgende streepjes (--) staan

> [!TIP]
> Als u denkt dat u meerdere services gaat gebruiken, is het raadzaam om de regio (of locatie) in de service naam op te nemen als een naamgevings Conventie. Services binnen dezelfde regio kunnen zonder kosten worden uitgewisseld. als Azure Cognitive Search zich in VS West bevindt en u andere services ook in VS West hebt, kunt u met een naam zoals `mysearchservice-westus` u een reis naar de pagina eigenschappen besparen wanneer u bepaalt hoe u resources wilt combi neren of koppelen.

## <a name="choose-a-location"></a>Een locatie kiezen

Als Azure-service kunnen Azure Cognitive Search worden gehost in data centers over de hele wereld. De lijst met ondersteunde regio's kunt u vinden op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/). 

U kunt de bandbreedte kosten minimaliseren of vermijden door dezelfde locatie voor meerdere services te kiezen. Als u bijvoorbeeld gegevens indexeert die door een andere Azure-service zijn verschaft (Azure Storage, Azure Cosmos DB, Azure SQL Database), voor komt u dat de Azure Cognitive Search-service in dezelfde regio bandbreedte kosten (er worden geen kosten in rekening gebracht voor uitgaande gegevens wanneer Services bevinden zich in dezelfde regio).

Daarnaast kunt u, als u verrijkingen van cognitieve Zoek opdrachten AI gebruikt, uw service maken in dezelfde regio als uw Cognitive Services-resource. *De co-locatie van Azure Cognitive Search en Cognitive Services in dezelfde regio is een vereiste voor AI-verrijking*.

> [!Note]
> Centraal-India is momenteel niet beschikbaar voor nieuwe services. Voor services die zich al in Centraal-India bevinden, kunt u opschalen zonder beperkingen en wordt uw service volledig ondersteund in die regio. De beperking voor deze regio is tijdelijk en beperkt tot alleen nieuwe services. Deze notitie wordt verwijderd wanneer de beperking niet meer van toepassing is.

## <a name="choose-a-pricing-tier-sku"></a>Een prijs categorie kiezen (SKU)

[Azure Cognitive Search wordt momenteel aangeboden in meerdere prijs categorieën](https://azure.microsoft.com/pricing/details/search/): gratis, basis en standaard. Elke categorie heeft eigen [capaciteiten en limieten](search-limits-quotas-capacity.md). Raadpleeg [Choose a pricing tier or SKU](search-sku-tier.md) (Een prijscategorie of SKU kiezen) voor hulp.

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

   ![Service overzicht pagina met URL-eind punt](./media/search-create-service-portal/get-url-key.png "URL-eind punt en andere service Details")

Een eindpunt en sleutel zijn niet nodig voor op de portal gebaseerde taken. De portal is al gekoppeld aan uw Azure Cognitive Search-resource met beheerders rechten. Voor een portal-overzicht begint u met [Quick Start: een Azure Cognitive search-index maken in de portal](search-get-started-portal.md).

## <a name="scale-your-service"></a>Uw service schalen

Nadat uw service is geconfigureerd, kunt u deze schalen, zodat de service aan uw behoeften voldoet. Als u de Standard-laag voor uw Azure Cognitive Search-service hebt gekozen, kunt u uw service in twee dimensies schalen: replica's en partities. Als u had gekozen voor de categorie Basic, had u alleen replica's kunnen toevoegen. Als u had gekozen voor de gratis service, had u niet kunnen schalen.

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

De meeste klanten gebruiken maar één service die geconfigureerd is voor de categorie die de [juiste balans voor resources](search-sku-tier.md) biedt. Eén service kan meerdere indexen hosten, afhankelijk van de [maximale limieten van de door u geselecteerde categorie](search-capacity-planning.md), en alle indexen zijn van elkaar geïsoleerd. In azure Cognitive Search kunnen aanvragen alleen worden omgeleid naar één index, zodat de kans op onbedoelde of opzettelijke gegevens uit andere indexen in dezelfde service wordt geminimaliseerd.

Hoewel de meeste klanten slechts één service gebruiken, kan serviceredundantie nodig zijn als de volgende operationele vereisten gelden:

* Herstel na noodgevallen (onderbreking datacentrum). Azure Cognitive Search biedt geen directe failover in het geval van een storing. Raadpleeg voor aanbevelingen en richtlijnen [Servicebeheer](search-manage.md).
* Uit uw onderzoek naar multitenancymodellering blijkt dat een ontwerp met meerdere services optimaal is. Raadpleeg voor meer informatie [Ontwerp voor multitenancy](search-modeling-multitenant-saas-applications.md).
* Voor globaal geïmplementeerde toepassingen kunt u een exemplaar van Azure Cognitive Search in meerdere regio's vereisen om de latentie van het internationale verkeer van uw toepassing te minimaliseren.

> [!NOTE]
> In azure Cognitive Search kunt u indexering en query bewerkingen niet scheiden. Daarom zou u nooit meerdere services voor gescheiden werk belastingen maken. Een query wordt altijd uitgevoerd op de service waarin de index is gemaakt (u kunt niet een index in een service maken en vervolgens naar een andere service kopiëren).

Een tweede service is niet vereist voor hoge beschikbaarheid. Hoge beschikbaarheid voor query's wordt bereikt wanneer u twee of meer replica's in dezelfde service gebruikt. Replica-updates zijn opeenvolgend, wat betekent dat er ten minste één operationeel is wanneer een service-update wordt geïmplementeerd. Zie [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/)voor meer informatie over uptime.

## <a name="next-steps"></a>Volgende stappen

Nadat u een Azure Cognitive Search-service hebt ingericht, kunt u door gaan in de portal om uw eerste index te maken.

> [!div class="nextstepaction"]
> [Snelstartgids: een Azure Cognitive Search-index maken in de portal](search-get-started-portal.md)
