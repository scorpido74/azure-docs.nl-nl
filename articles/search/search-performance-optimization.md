---
title: Schalen voor prestaties
titleSuffix: Azure Cognitive Search
description: Leer technieken en aanbevolen procedures voor het afstemmen van Azure Cognitive Search-prestaties en het configureren van optimale schaal.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212388"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Schalen voor prestaties op Azure Cognitive Search

In dit artikel worden aanbevolen procedures beschreven voor geavanceerde scenario's met geavanceerde vereisten voor schaalbaarheid en beschikbaarheid.

## <a name="start-with-baseline-numbers"></a>Begin met basislijnnummers

Voordat u een grotere implementatie-inspanning uitvoert, moet u weten hoe een typische querybelasting eruit ziet. Met de volgende richtlijnen u bij de basislijnquerynummers komen.

1. Kies een doellatentie (of maximale hoeveelheid tijd) die een typische zoekaanvraag moet inbeslagnemen om te voltooien.

1. Maak en test een echte workload met uw zoekservice met een realistische gegevensset om deze latentiesnelheden te meten.

1. Begin met een laag aantal query's per seconde (QPS) en verhoog vervolgens geleidelijk het aantal dat in de test wordt uitgevoerd totdat de querylatentie onder het vooraf gedefinieerde doel zakt. Dit is een belangrijke benchmark om u te helpen plannen voor schaal als uw toepassing groeit in gebruik.

1. Hergebruik HTTP-verbindingen waar mogelijk. Als u de Azure Cognitive Search .NET SDK gebruikt, betekent dit dat u een instantie of [SearchIndexClient-instantie](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) opnieuw moet gebruiken en als u de REST-API gebruikt, moet u één HttpClient opnieuw gebruiken.

1. Varieer de inhoud van queryaanvragen, zodat er wordt gezocht in verschillende delen van uw index. Variatie is belangrijk, want als u voortdurend dezelfde zoekaanvragen uitvoert, zal het incacheren van gegevens ervoor zorgen dat de prestaties er beter uitzien dan met een meer uiteenlopende queryset.

1. Varieer de structuur van queryaanvragen zodat u verschillende soorten query's krijgt. Niet elke zoekopdracht presteert op hetzelfde niveau. Een documentopzoek- of zoeksuggestie is bijvoorbeeld meestal sneller dan een query met een aanzienlijk aantal facetten en filters. Testsamenstelling moet verschillende query's bevatten, in ongeveer dezelfde verhoudingen als u zou verwachten in de productie.  

Tijdens het maken van deze testworkloads zijn er enkele kenmerken van Azure Cognitive Search om in gedachten te houden:

+ Het is mogelijk overbelasting van uw service door te veel zoekopdrachten in een keer te duwen. Wanneer dit gebeurt, ziet u HTTP 503-antwoordcodes. Als u een 503 tijdens het testen wilt vermijden, begint u met verschillende reeksen zoekaanvragen om de verschillen in latentiepercentages te zien terwijl u meer zoekaanvragen toevoegt.

+ Azure Cognitive Search voert geen indexeringstaken uit op de achtergrond. Als uw service query- en indexeringsworkloads gelijktijdig verwerkt, houdt u hier rekening mee door taken te indexeren in uw querytests of door opties te verkennen voor het uitvoeren van indexeringstaken tijdens de daluren.

> [!Tip]
> U een realistische querybelasting simuleren met behulp van load testing-hulpprogramma's. Probeer [het testen van laden met Azure DevOps](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) of gebruik een van deze [alternatieven.](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives)

## <a name="scale-for-high-query-volume"></a>Schalen voor hoog queryvolume

Een service wordt overbelast wanneer query's te lang duren of wanneer de service aanvragen begint te laten vallen. Als dit gebeurt, u het probleem op twee manieren aanpakken:

+ **Replica's toevoegen**  

  Elke replica is een kopie van uw gegevens, waardoor de service saldoaanvragen kan laden tegen meerdere kopieën.  Alle taakverdeling en replicatie van gegevens wordt beheerd door Azure Cognitive Search en u het aantal replica's dat voor uw service is toegewezen op elk gewenst moment wijzigen. U maximaal 12 replica's toewijzen in een standaardzoekservice en 3 replica's in een zoekservice Basic. Replica's kunnen worden aangepast vanuit de [Azure-portal](search-create-service-portal.md) of [PowerShell.](search-manage-powershell.md)

+ **Een nieuwe service op een hoger niveau maken**  

  Azure Cognitive Search wordt geleverd in een [aantal lagen](https://azure.microsoft.com/pricing/details/search/) en elk biedt verschillende niveaus van prestaties. In sommige gevallen u zoveel query's hebben dat de laag waarop u zich bevindt niet voldoende doorlooptijd kan bieden, zelfs niet wanneer replica's zijn uitgeschakeld. In dit geval u overwegen om over te stappen op een hoger presterende laag, zoals de standaards3-laag, die is ontworpen voor scenario's met grote aantallen documenten en extreem hoge queryworkloads.

## <a name="scale-for-slow-individual-queries"></a>Schalen voor langzame afzonderlijke query's

Een andere reden voor hoge latentietarieven is een enkele query die te lang duurt om te voltooien. In dit geval helpt het toevoegen van replica's niet. Twee mogelijke opties die kunnen helpen zijn het volgende:

+ **Partities vergroten**

  Een partitie splitst gegevens over extra computerbronnen. Twee partities splitsen gegevens in de helft, een derde partitie splitst het in derden, enzovoort. Een positief neveneffect is dat langzamere query's soms sneller presteren als gevolg van parallelle computing. We hebben parallellen opgemerkt op query's met een lage selectiviteit, zoals query's die overeenkomen met veel documenten of facetten die betrekking hebben op een groot aantal documenten. Aangezien een significante berekening vereist is om de relevantie van de documenten te scoren of om het aantal documenten te tellen, helpt het toevoegen van extra partities om query's sneller te voltooien.  
   
  Er kunnen maximaal 12 partities zijn in de standaardzoekservice en 1 partitie in de zoekservice Basic. Partities kunnen worden aangepast via de [Azure-portal](search-create-service-portal.md) of [PowerShell.](search-manage-powershell.md)

+ **Hoge kardinaliteitvelden beperken**

  Een veld met hoge kardinaliteit bestaat uit een facetable of filterbaar veld met een aanzienlijk aantal unieke waarden en verbruikt daardoor aanzienlijke bronnen bij het berekenen van resultaten. Als u bijvoorbeeld een product-id of beschrijvingsveld als facetable/filterbaar instelt, telt dit als een hoge kardinaliteit omdat de meeste waarden van document tot document uniek zijn. Beperk waar mogelijk het aantal velden met hoge kardinaliteit.

+ **Zoeklaag vergroten**  

  Door naar een hogere Azure Cognitive Search-laag te gaan, kan dit een andere manier zijn om de prestaties van langzame query's te verbeteren. Elke hogere laag biedt snellere CPU's en meer geheugen, die beide een positieve invloed hebben op de queryprestaties.

## <a name="scale-for-availability"></a>Schaal voor beschikbaarheid

Replica's helpen niet alleen de latentie van query's te verminderen, maar kunnen ook een hoge beschikbaarheid mogelijk maken. Met één replica moet u periodieke downtime verwachten als gevolg van het opnieuw opstarten van de server na software-updates of voor andere onderhoudsgebeurtenissen die zullen plaatsvinden. Daarom is het belangrijk om te overwegen of uw toepassing een hoge beschikbaarheid van zoekopdrachten (query's) en schrijfbewerkingen vereist (indexeren van gebeurtenissen). Azure Cognitive Search biedt SLA-opties voor alle betaalde zoekaanbiedingen met de volgende kenmerken:

+ Twee replica's voor een hoge beschikbaarheid van alleen-lezen workloads (query's)

+ Drie of meer replica's voor een hoge beschikbaarheid van lees-schrijfworkloads (query's en indexering)

Ga voor meer informatie hierover naar de [Azure Cognitive Search Service Level Agreement](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Aangezien replica's kopieën van uw gegevens zijn, kan Azure Cognitive Search met meerdere replica's opnieuw opstarten en onderhoud uitvoeren tegen één replica, terwijl de uitvoering van query's wordt voortgezet op andere replica's. Omgekeerd, als u replica's wegte nemen, zult u queryprestatiesdegradatie oplopen, ervan uitgaande dat deze replica's een onderbenutte bron waren.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Schaal voor geo-gedistribueerde workloads en georedundantie

Voor geo-gedistribueerde workloads hebben gebruikers die zich ver van het hostdatacenter bevinden, hogere latentiesnelheden. Een beperking is het leveren van meerdere zoekservices in regio's die dichter bij deze gebruikers liggen.

Azure Cognitive Search biedt momenteel geen geautomatiseerde methode voor georeplicerende Azure Cognitive Search-indexen in verschillende regio's, maar er zijn enkele technieken die kunnen worden gebruikt die dit proces eenvoudig kunnen implementeren en beheren. Deze worden beschreven in de volgende paar secties.

Het doel van een geogedistribueerde set zoekservices is om twee of meer indexen beschikbaar te hebben in twee of meer regio's, waarbij een gebruiker wordt doorgestuurd naar de Azure Cognitive Search-service die de laagste latentie biedt zoals in dit voorbeeld wordt gezien:

   ![Kruisoverzicht van services per regio][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Gegevens gesynchroniseerd houden voor meerdere services

Er zijn twee opties om uw gedistribueerde zoekservices gesynchroniseerd te houden, die bestaan uit het gebruik van de [Azure Cognitive Search Indexer](search-indexer-overview.md) of de Push API (ook wel de [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/)genoemd).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Indexers gebruiken voor het bijwerken van inhoud op meerdere services

Als u al indexeren op één service gebruikt, u een tweede indexer op een tweede service configureren om hetzelfde gegevensbronobject te gebruiken en gegevens van dezelfde locatie te halen. Elke service in elke regio heeft zijn eigen indexeren en een doelindex (uw zoekindex wordt niet gedeeld, wat betekent dat gegevens worden gedupliceerd), maar elke indexer verwijst naar dezelfde gegevensbron.

Hier is een high-level visuele van hoe die architectuur eruit zou zien.

   ![Enkele gegevensbron met gedistribueerde indexer- en servicecombinaties][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>REST API's gebruiken voor het pushen van inhoudsupdates op meerdere services

Als u de Azure Cognitive Search REST API gebruikt om [inhoud in uw Azure Cognitive Search-index te pushen,](https://docs.microsoft.com/rest/api/searchservice/update-index)u uw verschillende zoekservices synchroon houden door wijzigingen in alle zoekservices te pushen wanneer een update nodig is. Zorg er in uw code voor dat u gevallen verwerkt waarin een update naar één zoekservice mislukt, maar slaagt voor andere zoekservices.

## <a name="leverage-azure-traffic-manager"></a>Azure Traffic Manager gebruiken

[Met Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) u aanvragen doorsturen naar meerdere geo-gelokaliseerde websites die vervolgens worden ondersteund door meerdere zoekservices. Een voordeel van traffic manager is dat het Azure Cognitive Search kan onderzoeken om ervoor te zorgen dat het beschikbaar is en gebruikers te leiden naar alternatieve zoekservices in het geval van downtime. Als u bovendien zoekaanvragen routert via Azure-websites, u met Azure Traffic Manager balansaanvragen laden waarin de website is up, maar niet Azure Cognitive Search. Hier is een voorbeeld van wat de architectuur die Traffic Manager gebruikt.

   ![Cross-tab van services per regio, met centrale Verkeersmanager][3]

## <a name="next-steps"></a>Volgende stappen

Zie [Servicelimieten](search-limits-quotas-capacity.md)voor meer informatie over de prijsniveaus en servicelimieten voor elk niveau. Zie [Plannen voor capaciteit](search-capacity-planning.md) voor meer informatie over partitie- en replicacombinaties.

Voor een discussie over de prestaties en demonstraties van de technieken besproken in dit artikel, bekijk de volgende video:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
