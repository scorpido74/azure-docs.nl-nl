---
title: 'Quick Start: een zoek service maken in de portal'
titleSuffix: Azure Cognitive Search
description: In deze snelstartgids vindt u informatie over het instellen van een Azure Cognitive Search-resource in de Azure Portal. Resourcegroepen, regio's en de SKU of prijscategorie kiezen.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3bc3edcd0e75d8f6e3e4d6f9b200032909318040
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209355"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Snelstartgids: een Azure Cognitive Search-service maken in de portal

Azure Cognitive Search is een zelfstandige resource die wordt gebruikt om een zoek ervaring te koppelen aan aangepaste apps. Azure Cognitive Search kan eenvoudig worden geïntegreerd met andere Azure-Services, met apps op netwerk servers of met software die wordt uitgevoerd op andere Cloud platforms.

In dit artikel leert u hoe u een resource maakt in de [Azure Portal](https://portal.azure.com/).

[![GIF-animatie](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Hebt u liever PowerShell? Gebruik de [servicesjabloon](https://azure.microsoft.com/resources/templates/101-azure-search-create/) van Azure Resource Manager. Zie [Azure Cognitive Search beheren met Power shell](search-manage-powershell.md)voor hulp bij het aan de slag.

## <a name="subscribe-free-or-paid"></a>Abonneren (gratis of betaald)

[Maak een gratis Azure-account](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) en gebruik gratis tegoed om betaalde Azure-services uit te proberen. Nadat u het tegoed hebt opgemaakt, kunt u het account houden en de gratis Azure-services, zoals Websites, blijven gebruiken. Er worden nooit kosten in rekening gebracht bij uw creditcard tenzij u de instellingen expliciet wijzigt en aangeeft dat u wilt betalen.

U kunt ook [voordelen voor MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Als u een MSDN-abonnement hebt, ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken. 

## <a name="find-azure-cognitive-search"></a>Azure Cognitive Search zoeken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik op het plusteken (+ Resource maken) in de linkerbovenhoek.
3. Gebruik de zoek balk om ' Azure Cognitive Search ' te vinden of navigeer naar de resource via **Web** > **Azure Cognitive Search**.

![Een resource maken in de portal](./media/search-create-service-portal/find-search3.png "Een resource maken in de portal")

## <a name="choose-a-subscription"></a>Een abonnement kiezen

Als u meer dan één abonnement hebt, kiest u er een voor uw zoek service.

## <a name="set-a-resource-group"></a>Een resource groep instellen

Een resource groep is een container met gerelateerde resources voor uw Azure-oplossing. Dit is vereist voor de Search-service. Het is ook handig voor het beheren van resources, inclusief de kosten. Een resource groep kan bestaan uit één service of meerdere services die samen worden gebruikt. Als u bijvoorbeeld Azure Cognitive Search gebruikt voor het indexeren van een Azure Cosmos DB-Data Base, kunt u beide services onderdeel maken van dezelfde resource groep voor beheer doeleinden. 

Als u resources niet in één groep combineert of als bestaande resource groepen zijn gevuld met resources die worden gebruikt in niet-gerelateerde oplossingen, maakt u een nieuwe resource groep voor uw Azure Cognitive Search-resource. 

![Een nieuwe resource groep maken](./media/search-create-service-portal/new-resource-group.png "Een nieuwe resourcegroep maken")

In de loop van de tijd kunt u de huidige en geraamde kosten in de gaten houden of kunt u kosten voor afzonderlijke resources weer geven. De volgende scherm afbeelding toont het soort kosten gegevens dat u kunt verwachten wanneer u meerdere resources in één groep combineert.

![Kosten beheren op het niveau van de resource groep](./media/search-create-service-portal/resource-group-cost-management.png "Kosten beheren op het niveau van de resource groep")

> [!TIP]
> Resource groepen vereenvoudigen opschonen omdat het verwijderen van een groep alle services erin verwijdert. Bij prototypeprojecten die gebruikmaken van meerdere services, wordt het opschonen na afloop van het project gemakkelijker als die services zich allemaal in dezelfde resourcegroep bevinden.

## <a name="name-the-service"></a>Naam van de service

Geef in de details van het exemplaar een service naam op in het veld **URL** . De naam maakt deel uit van het URL-eind punt waarmee API-aanroepen worden uitgegeven: `https://your-service-name.search.windows.net`. Als u bijvoorbeeld wilt dat het eindpunt `https://myservice.search.windows.net` wordt, voert u `myservice` in.

Vereisten voor servicenaam:

* De naam moet uniek zijn binnen de naamruimte search.windows.net
* De waarde moet tussen 2 en 60 tekens lang zijn
* U moet kleine letters, cijfers of streepjes (-) gebruiken
* Gebruik geen streepjes (-) in de eerste twee tekens of als het laatste teken
* U mag geen opeenvolgende streepjes gebruiken ('--') overal

> [!TIP]
> Als u denkt dat u meerdere services gaat gebruiken, is het raadzaam om de regio (of locatie) in de service naam op te nemen als een naamgevings Conventie. Services binnen dezelfde regio kunnen zonder kosten worden uitgewisseld. als Azure Cognitive Search zich in VS West bevindt en u andere services ook in VS West hebt, kunt u met een naam zoals `mysearchservice-westus` u een reis naar de pagina eigenschappen besparen wanneer u bepaalt hoe u resources wilt combi neren of koppelen.

## <a name="choose-a-location"></a>Een locatie kiezen

Als Azure-service kunnen Azure Cognitive Search worden gehost in data centers over de hele wereld. De lijst met ondersteunde regio's kunt u vinden op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/). 

U kunt de bandbreedte kosten minimaliseren of vermijden door dezelfde locatie voor meerdere services te kiezen. Als u bijvoorbeeld gegevens indexeert die door een andere Azure-service zijn verschaft (Azure Storage, Azure Cosmos DB, Azure SQL Database), voor komt u dat de Azure Cognitive Search-service in dezelfde regio bandbreedte kosten (er worden geen kosten in rekening gebracht voor uitgaande gegevens wanneer Services bevinden zich in dezelfde regio).

Als u AI-verrijking gebruikt, maakt u uw zoek service in dezelfde regio als Cognitive Services. *De co-locatie van Azure Cognitive Search en Cognitive Services in dezelfde regio is een vereiste voor AI-verrijking*.

> [!Note]
> Centraal-India is momenteel niet beschikbaar voor nieuwe services. Voor services die zich al in Centraal-India bevinden, kunt u opschalen zonder beperkingen en wordt uw service volledig ondersteund in die regio. De beperking voor deze regio is tijdelijk en beperkt tot alleen nieuwe services. Deze notitie wordt verwijderd wanneer de beperking niet meer van toepassing is.

## <a name="choose-a-pricing-tier-sku"></a>Een prijs categorie kiezen (SKU)

[Azure Cognitive Search wordt momenteel aangeboden in meerdere prijs categorieën](https://azure.microsoft.com/pricing/details/search/): gratis, basis en standaard. Elke categorie heeft eigen [capaciteiten en limieten](search-limits-quotas-capacity.md). Raadpleeg [Choose a pricing tier or SKU](search-sku-tier.md) (Een prijscategorie of SKU kiezen) voor hulp.

Basic en Standard zijn de meest voorkomende opties voor productie werkbelastingen, maar de meeste klanten beginnen met de gratis service. De belangrijkste verschillen tussen lagen zijn de partitie grootte en de snelheid, en limieten voor het aantal objecten dat u kunt maken.

Houd er rekening mee dat een prijs categorie niet kan worden gewijzigd nadat de service is gemaakt. Als u een hogere of lagere laag nodig hebt, moet u de service opnieuw maken.

## <a name="create-your-service"></a>Uw service maken

Nadat u de benodigde invoer hebt gemaakt, gaat u verder met het maken van de service. 

![De service controleren en maken](./media/search-create-service-portal/new-service3.png "De service controleren en maken")

Uw service wordt binnen enkele minuten geïmplementeerd. U kunt de voortgang bewaken via Azure-meldingen. U kunt de service vastmaken aan uw dash board voor een snelle toegang in de toekomst.

![De service controleren en vastmaken](./media/search-create-service-portal/monitor-notifications.png "De service controleren en vastmaken")

## <a name="get-a-key-and-url-endpoint"></a>Een sleutel en het URL-eindpunt ophalen

Tenzij u de portal gebruikt, moet u op programmeer niveau toegang tot uw nieuwe service het URL-eind punt en een verificatie-API-sleutel opgeven.

1. Op de pagina **overzicht** zoekt en kopieert u het URL-eind punt aan de rechter kant van de pagina.

2. Kopieer op de pagina **sleutels** een van de beheer sleutels (deze zijn gelijk aan). API-beheersleutels voor beheerders zijn vereist voor het maken, bijwerken en verwijderen van objecten in uw service. Query sleutels bieden daarentegen Lees toegang voor het indexeren van inhoud.

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

Nadat u een service hebt ingericht, kunt u door gaan in de portal om uw eerste index te maken.

> [!div class="nextstepaction"]
> [Snelstartgids: een Azure Cognitive Search-index maken in de portal](search-get-started-portal.md)
