---
title: 'Snelstart: een zoekservice maken in de portal'
titleSuffix: Azure Cognitive Search
description: Lees in deze snelstart van deze portal hoe u een Azure Cognitive Search-bron in de Azure-portal instelt. Resourcegroepen, regio's en de SKU of prijscategorie kiezen.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3bc3edcd0e75d8f6e3e4d6f9b200032909318040
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77209355"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Snelstart: een Azure Cognitive Search-service maken in de portal

Azure Cognitive Search is een zelfstandige bron die wordt gebruikt om een zoekervaring aan te sluiten op aangepaste apps. Azure Cognitive Search integreert eenvoudig met andere Azure-services, met apps op netwerkservers of met software die op andere cloudplatforms wordt uitgevoerd.

In dit artikel leest u hoe u een resource maakt in de [Azure-portal.](https://portal.azure.com/)

[![Gif](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Hebt u liever PowerShell? Gebruik de [servicesjabloon](https://azure.microsoft.com/resources/templates/101-azure-search-create/) van Azure Resource Manager. Zie [Azure Cognitive Search beheren met PowerShell](search-manage-powershell.md)voor hulp bij het aan de slag gaan.

## <a name="subscribe-free-or-paid"></a>Abonneren (gratis of betaald)

[Maak een gratis Azure-account](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) en gebruik gratis tegoed om betaalde Azure-services uit te proberen. Nadat u het tegoed hebt opgemaakt, kunt u het account houden en de gratis Azure-services, zoals Websites, blijven gebruiken. Er wordt nooit geld van uw creditcard afgeschreven, tenzij u uw instellingen zelf wijzigt en expliciet aangeeft dat u wilt betalen.

U kunt ook [voordelen voor MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Als u een MSDN-abonnement hebt, ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken. 

## <a name="find-azure-cognitive-search"></a>Azure Cognitive Search zoeken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik op het plusteken (+ Resource maken) in de linkerbovenhoek.
3. Gebruik de zoekbalk om 'Azure Cognitive Search' te zoeken of naar de bron te navigeren via **Web** > **Azure Cognitive Search**.

![Een resource maken in de portal](./media/search-create-service-portal/find-search3.png "Een resource maken in de portal")

## <a name="choose-a-subscription"></a>Een abonnement kiezen

Als u meer dan één abonnement hebt, kiest u er een voor uw zoekservice.

## <a name="set-a-resource-group"></a>Een resourcegroep instellen

Een resourcegroep is een container met gerelateerde resources voor uw Azure-oplossing. Het is vereist voor de zoekservice. Het is ook nuttig voor het beheer van middelen all-up, inclusief kosten. Een resourcegroep kan bestaan uit één service of meerdere services die samen worden gebruikt. Als u bijvoorbeeld Azure Cognitive Search gebruikt om een Azure Cosmos DB-database te indexeren, u beide services onderdeel maken van dezelfde brongroep voor beheerdoeleinden. 

Als u resources niet in één groep combineert of als bestaande resourcegroepen zijn gevuld met resources die worden gebruikt in niet-gerelateerde oplossingen, maakt u een nieuwe brongroep alleen voor uw Azure Cognitive Search-bron. 

![Een nieuwe resourcegroep maken](./media/search-create-service-portal/new-resource-group.png "Een nieuwe resourcegroep maken")

Na verloop van tijd u de huidige en verwachte kosten all-up bijhouden of u de kosten voor afzonderlijke resources bekijken. De volgende schermafbeelding toont de kosteninformatie die u verwachten te zien wanneer u meerdere bronnen in één groep combineert.

![Kosten beheren op het niveau van de resourcegroep](./media/search-create-service-portal/resource-group-cost-management.png "Kosten beheren op het niveau van de resourcegroep")

> [!TIP]
> Resourcegroepen vereenvoudigen het opschonen omdat het verwijderen van een groep alle services binnen de groep verwijdert. Bij prototypeprojecten die gebruikmaken van meerdere services, wordt het opschonen na afloop van het project gemakkelijker als die services zich allemaal in dezelfde resourcegroep bevinden.

## <a name="name-the-service"></a>De service een naam geven

Geef in instantiedetails een servicenaam op in het **veld URL.** De naam maakt deel uit van het URL-eindpunt waartegen API-aanroepen worden uitgegeven: `https://your-service-name.search.windows.net`. Als u bijvoorbeeld wilt dat het eindpunt `https://myservice.search.windows.net` wordt, voert u `myservice` in.

Vereisten voor servicenaam:

* De naam moet uniek zijn binnen de naamruimte search.windows.net
* Het moet tussen de 2 en 60 tekens in lengte
* U moet kleine letters, cijfers of streepjes gebruiken ("-")
* Gebruik geen streepjes ("-") in de eerste 2 tekens of als het laatste teken
* U mag nergens opeenvolgende streepjes ("--") gebruiken

> [!TIP]
> Als u denkt dat u meerdere services gebruikt, raden we u aan de regio (of locatie) op te nemen in de servicenaam als naamgevingsconventie. Services binnen dezelfde regio kunnen gratis gegevens uitwisselen, dus als Azure Cognitive Search zich in West-VS `mysearchservice-westus` bevindt en u ook andere services in West-VS hebt, kan een naam als u een reis naar de eigenschappenpagina besparen wanneer u beslist hoe u resources wilt combineren of koppelen.

## <a name="choose-a-location"></a>Een locatie kiezen

Als Azure-service kan Azure Cognitive Search worden gehost in datacenters over de hele wereld. De lijst met ondersteunde regio's is te vinden op de [prijspagina.](https://azure.microsoft.com/pricing/details/search/) 

U bandbreedtekosten minimaliseren of vermijden door dezelfde locatie voor meerdere services te kiezen. Als u bijvoorbeeld gegevens indexeert die worden geleverd door een andere Azure-service (Azure-opslag, Azure Cosmos DB, Azure SQL Database), voorkomt het maken van uw Azure Cognitive Search-service in dezelfde regio bandbreedtekosten (er zijn geen kosten verbonden aan uitgaande gegevens wanneer er geen kosten in rekening worden gebracht diensten zich in dezelfde regio bevinden).

Als u AI-verrijking gebruikt, maakt u uw zoekservice in dezelfde regio als Cognitive Services. *Colocatie van Azure Cognitive Search and Cognitive Services in dezelfde regio is een vereiste voor AI-verrijking.*

> [!Note]
> Centraal-India is momenteel niet beschikbaar voor nieuwe services. Voor services die al in Centraal-India zijn, u zonder beperkingen opschalen en uw service wordt volledig ondersteund in die regio. De beperking op deze regio is tijdelijk en beperkt tot nieuwe diensten. We verwijderen deze notitie wanneer de beperking niet langer van toepassing is.

## <a name="choose-a-pricing-tier-sku"></a>Kies een prijscategorie (SKU)

[Azure Cognitive Search wordt momenteel aangeboden in meerdere prijsniveaus:](https://azure.microsoft.com/pricing/details/search/)Gratis, Basic of Standard. Elke categorie heeft eigen [capaciteiten en limieten](search-limits-quotas-capacity.md). Raadpleeg [Choose a pricing tier or SKU](search-sku-tier.md) (Een prijscategorie of SKU kiezen) voor hulp.

Basic en Standard zijn de meest voorkomende keuzes voor productieworkloads, maar de meeste klanten beginnen met de gratis service. Belangrijkste verschillen tussen lagen zijn partitiegrootte en -snelheid en beperkingen op het aantal objecten dat u maken.

Houd er rekening mee dat een prijscategorie niet kan worden gewijzigd zodra de service is gemaakt. Als u een hogere of lagere laag nodig hebt, moet u de service opnieuw maken.

## <a name="create-your-service"></a>Uw service maken

Nadat u de benodigde ingangen hebt opgegeven, gaat u verder en maakt u de service. 

![De service bekijken en maken](./media/search-create-service-portal/new-service3.png "De service bekijken en maken")

Uw service wordt binnen enkele minuten geïmplementeerd. U de voortgang controleren via Azure-meldingen. Overweeg om de service vast te maken aan uw dashboard voor eenvoudige toegang in de toekomst.

![De service controleren en vastmaken](./media/search-create-service-portal/monitor-notifications.png "De service controleren en vastmaken")

## <a name="get-a-key-and-url-endpoint"></a>Een sleutel en het URL-eindpunt ophalen

Tenzij u de portal gebruikt, vereist programmatische toegang tot uw nieuwe service dat u het URL-eindpunt en een api-toets voor verificatie verstrekt.

1. Zoek en kopieer op de pagina **Overzicht** het URL-eindpunt aan de rechterkant van de pagina.

2. Kopieer **op** de pagina Sleutels een van de beheerderssleutels (deze zijn gelijkwaardig). API-beheersleutels voor beheerders zijn vereist voor het maken, bijwerken en verwijderen van objecten in uw service. Querytoetsen bieden daarentegen leestoegang tot indexinhoud.

   ![Pagina met serviceoverzicht met URL-eindpunt](./media/search-create-service-portal/get-url-key.png "URL-eindpunt en andere servicegegevens")

Een eindpunt en sleutel zijn niet nodig voor op de portal gebaseerde taken. De portal is al gekoppeld aan uw Azure Cognitive Search-bron met beheerdersrechten. Voor een portal-walkthrough begint u met [Quickstart: Een Azure Cognitive Search-index maken in de portal.](search-get-started-portal.md)

## <a name="scale-your-service"></a>Uw service schalen

Nadat uw service is geconfigureerd, kunt u deze schalen, zodat de service aan uw behoeften voldoet. Als u de standaardlaag voor uw Azure Cognitive Search-service hebt gekozen, u uw service schalen in twee dimensies: replica's en partities. Als u had gekozen voor de categorie Basic, had u alleen replica's kunnen toevoegen. Als u had gekozen voor de gratis service, had u niet kunnen schalen.

***Partities*** zorgen ervoor dat uw service meer documenten bewaart en meer documenten doorzoekt.

***Replica's*** zorgen ervoor dat uw services meer zoekquery's kunnen verwerken.

Als u resources toevoegt, wordt uw maandfactuur hoger. Met de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) krijgt u inzicht in het effect van het toevoegen van resources op uw facturering. Onthoud dat u resources kunt aanpassen aan de benodigde capaciteit. Zo kunt u eerst resources toevoegen voor het maken van een volledige eerste index en later het aantal resources weer verlagen tot een niveau dat geschikt is voor incrementele indexering.

> [!Important]
> Een service moet [twee replica's hebben voor een alleen-lezen-SLA en drie replica's voor een lezen/schrijven-SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Ga naar de pagina van uw zoekservice in Azure Portal.
2. Selecteer > **Instellingenschaal**in het **Settings**linkernavigatiedeelvenster .
3. Gebruik de schuifbalk om resources van een bepaald type toe te voegen.

![Capaciteit toevoegen](./media/search-create-service-portal/settings-scale.png "Capaciteit toevoegen via replica's en partities")

> [!Note]
> Opslag per partitie en de snelheid neemt toe op hogere niveaus. Zie [capaciteit en limieten](search-limits-quotas-capacity.md)voor meer informatie.

## <a name="when-to-add-a-second-service"></a>Wanneer u een tweede service moet toevoegen

De meeste klanten gebruiken maar één service die geconfigureerd is voor de categorie die de [juiste balans voor resources](search-sku-tier.md) biedt. Eén service kan meerdere indexen hosten, afhankelijk van de [maximale limieten van de door u geselecteerde categorie](search-capacity-planning.md), en alle indexen zijn van elkaar geïsoleerd. In Azure Cognitive Search kunnen aanvragen alleen naar één index worden geleid, waardoor de kans op het ophalen van onbedoelde of opzettelijke gegevens uit andere indexen in dezelfde service wordt geminimaliseerd.

Hoewel de meeste klanten slechts één service gebruiken, kan serviceredundantie nodig zijn als de volgende operationele vereisten gelden:

* Herstel na noodgevallen (onderbreking datacentrum). Azure Cognitive Search biedt geen directe failover in het geval van een storing. Raadpleeg voor aanbevelingen en richtlijnen [Servicebeheer](search-manage.md).
* Uit uw onderzoek naar multitenancymodellering blijkt dat een ontwerp met meerdere services optimaal is. Raadpleeg voor meer informatie [Ontwerp voor multitenancy](search-modeling-multitenant-saas-applications.md).
* Voor wereldwijd geïmplementeerde toepassingen hebt u mogelijk een exemplaar van Azure Cognitive Search in meerdere regio's nodig om de latentie van het internationale verkeer van uw toepassing te minimaliseren.

> [!NOTE]
> In Azure Cognitive Search u indexerings- en querybewerkingen niet scheiden. u zou dus nooit meerdere services maken voor gescheiden workloads. Een query wordt altijd uitgevoerd op de service waarin de index is gemaakt (u kunt niet een index in een service maken en vervolgens naar een andere service kopiëren).

Een tweede service is niet vereist voor hoge beschikbaarheid. Hoge beschikbaarheid voor query's wordt bereikt wanneer u twee of meer replica's in dezelfde service gebruikt. Replica-updates zijn sequentiële, wat betekent dat ten minste een operationeel is wanneer een service-update wordt uitgerold. Zie [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/)voor meer informatie over uptime.

## <a name="next-steps"></a>Volgende stappen

Nadat u een service hebt ingericht, u doorgaan in de portal om uw eerste index te maken.

> [!div class="nextstepaction"]
> [Snelstart: een Azure Cognitive Search-index maken in de portal](search-get-started-portal.md)
