---
title: Een zoekservice maken in de portal
titleSuffix: Azure Cognitive Search
description: In deze portal-quickstart leert u hoe u een Azure Cognitive Search-resource kunt instellen in de Azure-portal. Resourcegroepen, regio's en de SKU of prijscategorie kiezen.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 18204777a8e61b577b257b67cdd12bed1a5534fa
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529621"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Quickstart: Een Azure Cognitive Search-service maken in de portal

Azure Cognitive Search is een zelfstandige resource die wordt gebruikt voor het toevoegen van een zoekervaring aan aangepaste apps. Cognitive Search kan eenvoudig worden geïntegreerd met andere Azure-services, met apps op netwerkservers of met software die op andere cloudplatforms wordt uitgevoerd.

Dit artikel bevat meer informatie over hoe u een resource in de [Azure-portal](https://portal.azure.com/) kunt maken.

[![GIF-animatie](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Hebt u liever PowerShell? Gebruik de [servicesjabloon](https://azure.microsoft.com/resources/templates/101-azure-search-create/) van Azure Resource Manager. Zie [Azure Cognitive Search beheren met PowerShell](search-manage-powershell.md) voor hulp om aan de slag te gaan.

## <a name="before-you-start"></a>Voordat u begint

De volgende service-eigenschappen staan gedurende de levensduur van de service vast. Als een of meerdere wilt wijzigen, is er een nieuwe service vereist. Aangezien ze vast staan, is het belangrijk om na te denken over de implicaties voor het gebruik bij het invullen van de eigenschappen:

* De servicenaam wordt onderdeel van het URL-eindpunt ([tips](#name-the-service) voor handige servicenamen).
* De servicelaag is van [invloed op de facturering](search-sku-tier.md) en zorgt voor een opwaartse limiet op de capaciteit.
* De serviceregio kan de beschikbaarheid van bepaalde scenario's bepalen. Als u geïnteresseerd bent in kennisanalyse en AI-verrijking, hebt u Cognitive Services en mogelijk ook Azure Machine Learning in dezelfde regio als Azure Cognitive Search nodig.

## <a name="subscribe-free-or-paid"></a>Abonneren (gratis of betaald)

[Maak een gratis Azure-account](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) en gebruik gratis tegoed om betaalde Azure-services uit te proberen. Nadat u het tegoed hebt opgemaakt, kunt u het account houden en de gratis Azure-services, zoals Websites, blijven gebruiken. Er wordt nooit geld van uw creditcard afgeschreven, tenzij u uw instellingen zelf wijzigt en expliciet aangeeft dat u wilt betalen.

U kunt ook [voordelen voor MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Als u een MSDN-abonnement hebt, ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken. 

## <a name="find-azure-cognitive-search"></a>Azure Cognitive Search zoeken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Klik op het plusteken (+ Resource maken) in de linkerbovenhoek.

1. Gebruik de zoekbalk om te zoeken naar 'Azure Cognitive Search' of ga naar de resource via **Web** > **Azure Cognitive Search**.

![Een resource maken in de portal](./media/search-create-service-portal/find-search3.png "Een resource maken in de portal")

## <a name="choose-a-subscription"></a>Kies een abonnement

Als u meerdere abonnementen hebt, kiest u er één voor uw zoekservice.

## <a name="set-a-resource-group"></a>Stel een resourcegroep in

Een resourcegroep is een container met gerelateerde resources voor uw Azure-oplossing. Deze is vereist voor de zoekservice. Het is ook handig voor het beheren van ‘all-up’ resources, waaronder kosten. Een resourcegroep kan bestaan uit één service, of uit meerdere services die samen worden gebruikt. Als u Azure Cognitive Search bijvoorbeeld gebruikt om een Azure Cosmos DB-database te indexeren, kunt u beide services onderbrengen in dezelfde resourcegroep voor beheerdoeleinden. 

Als u resources niet in één groep combineert of als bestaande resourcegroepen resources bevatten die worden gebruikt in oplossingen waartussen geen verband bestaat, maakt u een nieuwe resourcegroep voor uw Azure Cognitive Search-resource. 

![Een nieuwe resourcegroep maken](./media/search-create-service-portal/new-resource-group.png "Een nieuwe resourcegroep maken")

U kunt in de loop van de tijd huidige en geschatte ‘all-up’ kosten bijhouden of kosten voor afzonderlijke resources bekijken. De volgende schermopname toont het soort kostengegevens dat u kunt verwachten te zien wanneer u meerdere resources in één groep combineert.

![Kosten beheren op het niveau van de resourcegroep](./media/search-create-service-portal/resource-group-cost-management.png "Kosten beheren op het niveau van de resourcegroep")

> [!TIP]
> Resourcegroepen vereenvoudigen opschoning, omdat met de verwijdering van een groep ook alle services daarin worden verwijderd. Bij prototypeprojecten die gebruikmaken van meerdere services, wordt het opschonen na afloop van het project gemakkelijker als die services zich allemaal in dezelfde resourcegroep bevinden.

## <a name="name-the-service"></a>De service een naam geven

Geef bij Exemplaardetails een servicenaam op in het veld **URL**. De naam is onderdeel van het URL-eindpunt waaraan API-aanroepen worden geadresseerd: `https://your-service-name.search.windows.net`. Als u bijvoorbeeld wilt dat het eindpunt `https://myservice.search.windows.net` wordt, voert u `myservice` in.

Vereisten voor servicenaam:

* De naam moet uniek zijn binnen de naamruimte search.windows.net
* De naam moet tussen de 2 en 60 tekens lang zijn
* U moet kleine letters, cijfers of streepjes (-) gebruiken
* Gebruik geen streepjes (-) bij de eerste 2 tekens of als het allerlaatste teken
* U mag nergens twee opeenvolgende streepjes (--) gebruiken

> [!TIP]
> Als u denkt dat u meerdere services zult gebruiken, is het raadzaam om als naamconventie de regio (of locatie) in de servicenaam op te nemen. Services in dezelfde regio kunnen gegevens gratis uitwisselen, dus als Azure Cognitive Search zich in VS - west bevindt en u ook andere services in VS - west hebt, kan een naam zoals `mysearchservice-westus` u een bezoek aan de eigenschappenpagina besparen wanneer u beslist hoe u resources combineert of bijvoegt.

## <a name="choose-a-location"></a>Een locatie kiezen

Azure Cognitive Search is beschikbaar in de meeste regio’s. De lijst met ondersteunde regio’s kunt u vinden op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/).

> [!Note]
> India - centraal en VAE - noord zijn momenteel niet beschikbaar voor nieuwe services. Voor services die al beschikbaar zijn in die regio’s, kunt u zonder beperkingen opschalen en wordt uw service volledig ondersteund in die regio. De beperkingen zijn tijdelijk en alleen beperkt tot nieuwe services. We zullen deze opmerking verwijderen wanneer de beperkingen niet meer van toepassing zijn.

### <a name="requirements"></a>Vereisten

 Als u AI-verrijking gebruikt, maakt u uw zoekservice in dezelfde regio als Cognitive Services. *Co-locatie van Azure Cognitive Search en Cognitive Services in dezelfde regio is een vereiste voor AI-verrijking*.

 Klanten met BCDR-vereisten (bedrijfscontinuïteit en herstel na noodgevallen) moeten hun services in [regioparen](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#azure-regional-pairs) maken. Als u bijvoorbeeld werkzaam bent in Noord-Amerika, kiest u wellicht VS - oost en VS - west, of VS - noord-centraal en VS - zuid-centraal, voor elke service.

### <a name="recommendations"></a>Aanbevelingen

Als u meerdere Azure-services gebruikt, kiest u een regio die ook uw gegevens- of toepassingsservice host. Zo minimaliseert of vermijdt u bandbreedtekosten voor uitgaande gegevens (er zijn geen kosten voor uitgaande gegevens wanneer services zich in dezelfde regio bevinden).

## <a name="choose-a-pricing-tier-sku"></a>Kies een prijscategorie (SKU)

[Azure Cognitive Search wordt momenteel aangeboden in verschillende prijscategorieën](https://azure.microsoft.com/pricing/details/search/): Gratis, Basic of Standard. Elke categorie heeft eigen [capaciteiten en limieten](search-limits-quotas-capacity.md). Raadpleeg [Choose a pricing tier or SKU](search-sku-tier.md) (Een prijscategorie of SKU kiezen) voor hulp.

Basic en Standard zijn de gebruikelijkste keuzes voor productieworkloads, maar de meeste klanten beginnen met de gratis service. De belangrijkste verschillen tussen de categorieën zijn partitiegrootte en snelheid, en limieten voor het aantal objecten dat u kunt maken.

Vergeet niet dat een prijscategorie niet kan worden gewijzigd nadat de service is gemaakt. Als u een hogere of lagere categorie nodig hebt, moet u de service opnieuw maken.

## <a name="create-your-service"></a>Uw service maken

Nadat u de benodigde invoer hebt opgegeven, kunt u de service gaan maken. 

![De service controleren en maken](./media/search-create-service-portal/new-service3.png "De service controleren en maken")

Uw service wordt binnen slechts enkele minuten geïmplementeerd. U kunt de voortgang controleren via Azure-meldingen. Overweeg de service aan uw dashboard vast te maken voor gemakkelijke toegang in de toekomst.

![De service bewaken en vastmaken](./media/search-create-service-portal/monitor-notifications.png "De service bewaken en vastmaken")

## <a name="get-a-key-and-url-endpoint"></a>Een sleutel en het URL-eindpunt ophalen

Tenzij u de portal gebruikt, moet u voor programmatische toegang tot uw nieuwe service het URL-eindpunt en een verificatie-API-sleutel opgeven.

1. Zoek aan de rechterkant van de pagina **Overzicht** het URL-eindpunt en kopieer het.

2. Kopieer op de pagina **Sleutels** een van de beheersleutels (deze zijn equivalent). API-beheersleutels voor beheerders zijn vereist voor het maken, bijwerken en verwijderen van objecten in uw service. Querysleutels bieden daarentegen alleen leestoegang tot indexinhoud.

   ![Pagina Overzicht van de service met URL-eindpunt](./media/search-create-service-portal/get-url-key.png "URL-eindpunt en andere servicedetails")

Een eindpunt en sleutel zijn niet nodig voor op de portal gebaseerde taken. De portal is al gekoppeld aan uw Azure Cognitive Search-resource met beheerdersrechten. Voor een portaloverzicht begint u met [Quickstart: Een Azure Cognitive Search-index maken in de portal](search-get-started-portal.md).

## <a name="scale-your-service"></a>Uw service schalen

Nadat uw service is geconfigureerd, kunt u deze schalen, zodat de service aan uw behoeften voldoet. Als u voor uw Azure Cognitive Search-service kiest voor de categorie Standard, kunt u uw service in twee dimensies schalen: replica's en partities. Als u had gekozen voor de categorie Basic, had u alleen replica's kunnen toevoegen. Als u had gekozen voor de gratis service, had u niet kunnen schalen.

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
> Hoe hoger de categorie, hoe hoger de opslag per partitie en de snelheid. Zie [Capaciteit en limieten](search-limits-quotas-capacity.md) voor meer informatie.

## <a name="when-to-add-a-second-service"></a>Wanneer u een tweede service moet toevoegen

De meeste klanten gebruiken maar één service die geconfigureerd is voor de categorie die de [juiste balans voor resources](search-sku-tier.md) biedt. Eén service kan meerdere indexen hosten, afhankelijk van de [maximale limieten van de door u geselecteerde categorie](search-capacity-planning.md), en alle indexen zijn van elkaar geïsoleerd. In Azure Cognitive Search kunnen aanvragen alleen worden gericht aan één index, waardoor de kans dat er per ongeluk of opzettelijk gegevens worden opgehaald uit andere indexen in dezelfde service, wordt geminimaliseerd.

Hoewel de meeste klanten slechts één service gebruiken, kan serviceredundantie nodig zijn als de volgende operationele vereisten gelden:

+ [Bedrijfscontinuïteit en herstel na noodgevallen (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Azure Cognitive Search biedt geen onmiddellijke failover in geval van een storing.

+ Voor [multitenant-architecturen](search-modeling-multitenant-saas-applications.md) zijn soms twee of meer services nodig.

+ Voor globaal geïmplementeerde toepassingen kunnen zoekservices in elke geografie nodig zijn om latentie te minimaliseren.

> [!NOTE]
> In Azure Cognitive Search kunt u indexerings- en querybewerkingen niet scheiden; u zou dus nooit meerdere services voor gescheiden werkbelastingen maken. Een query wordt altijd uitgevoerd op de service waarin de index is gemaakt (u kunt niet een index in een service maken en vervolgens naar een andere service kopiëren).

Een tweede service is niet vereist voor hoge beschikbaarheid. Hoge beschikbaarheid voor query's wordt bereikt wanneer u twee of meer replica's in dezelfde service gebruikt. Replica-updates zijn sequentieel, wat betekent dat er ten minste één operationeel is wanneer er een service-update wordt geïmplementeerd. Voor meer informatie over uptime raadpleegt u [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Volgende stappen

Nadat u een service hebt ingericht, kunt u in de portal verdergaan met het maken van uw eerste index.

> [!div class="nextstepaction"]
> [Snelstart: Een Azure Cognitive Search-index maken in de portal](search-get-started-portal.md)
