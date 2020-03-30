---
title: Servicelimieten voor lagen en skus
titleSuffix: Azure Cognitive Search
description: Servicelimieten die worden gebruikt voor capaciteitsplanning en maximale limieten voor aanvragen en antwoorden voor Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 6ee339cb709a5d825b39b4accf294761c99ee41a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282976"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Servicelimieten in Azure Cognitive Search

Maximale limieten voor opslag, workloads en hoeveelheden indexen en andere objecten zijn afhankelijk van de vraag of u [Azure Cognitive Search](search-create-service-portal.md) at **Free,** **Basic,** **Standard**of **Storage Optimized** pricing tiers indient.

+ **Gratis** is een gedeelde service met meerdere tenantn die wordt geleverd met uw Azure-abonnement. Indexerings- en queryaanvragen worden uitgevoerd op replica's en partities die door andere tenants worden gebruikt.

+ **Basic** biedt speciale computerbronnen voor productieworkloads op kleinere schaal, maar deelt sommige netwerkinfrastructuur met andere tenants.

+ **Standaard** draait op speciale machines met meer opslag- en verwerkingscapaciteit op elk niveau. Standaard komt in vier niveaus: S1, S2, S3, en S3 HD.

+ **Storage Optimized** wordt uitgevoerd op speciale machines met meer totale opslag, opslagbandbreedte en geheugen dan **Standard.** Storage Optimized is verkrijgbaar in twee niveaus: L1 en L2

> [!NOTE]
> Vanaf 1 juli zijn alle lagen algemeen beschikbaar, inclusief de laag Storage Optimized. Alle prijzen zijn te vinden op de pagina [Prijsdetails.](https://azure.microsoft.com/pricing/details/search/)

  S3 High Density (S3 HD) is ontworpen voor specifieke workloads: [multi-tenancy](search-modeling-multitenant-saas-applications.md) en grote hoeveelheden kleine indexen (een miljoen documenten per index, drieduizend indexen per service). Deze laag biedt niet de [indexerfunctie](search-indexer-overview.md). Op S3 HD moet gegevensopname gebruikmaken van de push-benadering, waarbij API-aanroepen worden gebruikt om gegevens van bron naar index te pushen. 

> [!NOTE]
> Een service wordt op een bepaald niveau ingericht. Springen tiers om capaciteit te winnen omvat het inrichten van een nieuwe dienst (er is geen in-place upgrade). Zie [Een SKU of -laag kiezen](search-sku-tier.md)voor meer informatie. Zie Resourceniveaus schalen [voor query's en indexering van workloads voor](search-capacity-planning.md)meer informatie over het aanpassen van de capaciteit binnen een service die u al hebt ingericht.
>

## <a name="subscription-limits"></a>Abonnementslimieten
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Opslaglimieten
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Indexlimieten

| Resource | Gratis | Basis&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximale aantal indexen |3 |5 of 15 |50 |200 |200 |1000 per partitie of 3000 per service |10 |10 |
| Maximale eenvoudige velden per index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximale complexe verzamelingsvelden per index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximale elementen in alle&nbsp;complexe verzamelingen per document<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximale diepte van complexe velden |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximale [suggesties](https://docs.microsoft.com/rest/api/searchservice/suggesters) per index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximale [scoreprofielen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximale functies per profiel |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Basisdiensten die vóór december 2017 zijn gemaakt, hebben lagere limieten (5 in plaats van 15) op indexen. Basislaag is de enige SKU met een ondergrens van 100 velden per index.

<sup>2</sup> Het hebben van een zeer groot aantal elementen in complexe verzamelingen per document veroorzaakt momenteel een hoog opslaggebruik. Dit is een bekend probleem. In de tussentijd is een limiet van 3000 een veilige bovengrens voor alle servicelagen. Deze limiet wordt alleen afgedwongen voor indexeringsbewerkingen die gebruikmaken van de vroegste`2019-05-06`algemeen beschikbare (GA)-versie die complexe typevelden ( ) ondersteunt. Als we clients die mogelijk eerdere API-versies van preview gebruiken (die complexe typevelden ondersteunen) niet wilt verbreken, zullen we deze limiet niet afdwingen voor indexeringsbewerkingen die deze preview-API-versies gebruiken. Houd er rekening mee dat preview-API-versies niet bedoeld zijn om te worden gebruikt voor productiescenario's en we raden klanten ten zeerste aan om over te stappen op de nieuwste GA API-versie.

<a name="document-limits"></a>

## <a name="document-limits"></a>Documentlimieten 

Vanaf oktober 2018 zijn er in geen enkele andere service die op een factuurbare niveau (Basic, S1, S2, S3, S3 HD) is gemaakt, in elke regio. Hoewel de meeste regio's sinds november/december 2017 onbeperkte tellingen van documenten hebben gehad, waren er enkele regio's die na die datum documentlimieten bleven opleggen. Afhankelijk van waar en wanneer u een zoekservice hebt gemaakt, voert u mogelijk een service uit waarvoor nog steeds documentlimieten gelden.

Als u wilt bepalen of uw service documentlimieten heeft, gebruikt u de [API VOOR HERSTELServicestatistieken .](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics) Documentlimieten worden weergegeven in `null` het antwoord, waarbij geen limieten worden aangegeven.

> [!NOTE]
> Hoewel er geen SKU-specifieke documentlimieten zijn, is elke index nog steeds onderworpen aan een maximale veilige limiet om de stabiliteit van de service te garanderen. Deze limiet komt van Lucene. Elk Azure Cognitive Search-document wordt intern geïndexeerd als een of meer Lucene-documenten. Het aantal Lucene-documenten per zoekdocument is afhankelijk van het totale aantal elementen in complexe verzamelingsvelden. Elk element wordt geïndexeerd als een afzonderlijk Lucene-document. Een document met 3 elementen in een complex verzamelveld wordt bijvoorbeeld geïndexeerd als 4 Lucene-documenten - 1 voor het document zelf en 3 voor de elementen. Het maximum aantal Lucene-documenten is ongeveer 25 miljard per index.

### <a name="regions-previously-having-document-limits"></a>Regio's met documentlimieten

Als de portal een documentlimiet aangeeft, is uw service vóór eind 2017 gemaakt of is deze gemaakt op een datacenter met clusters met een lagere capaciteit voor het hosten van Azure Cognitive Search-services:

+ Australië - oost
+ Azië - oost
+ India - centraal
+ Japan - west
+ VS - west-centraal

Voor diensten waarvoor documentlimieten gelden, gelden de volgende maximumlimieten:

|  Gratis | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10.000 |1&nbsp;miljoen |15 miljoen per partitie of 180 miljoen per service |60 miljoen per partitie of 720 miljoen per service |120 miljoen per partitie of 1,4 miljard per service |1 miljoen per index of 200 miljoen per partitie |

Als uw service limieten heeft die u blokkeren, maakt u een nieuwe service en publiceert u alle inhoud opnieuw naar die service. Er is geen mechanisme voor het naadloos opnieuw inrichten van uw service op nieuwe hardware achter de schermen.

> [!Note] 
> Voor S3 High Density-services die na eind 2017 zijn gemaakt, is het document van 200 miljoen per partitie verwijderd, maar blijft de limiet van 1 miljoen documenten per index behouden.


### <a name="document-size-limits-per-api-call"></a>Limieten voor documentgrootte per API-aanroep

De maximale documentgrootte bij het aanroepen van een index-API is ongeveer 16 megabyte.

Documentgrootte is eigenlijk een limiet op de grootte van de instantie voor de aanvraag van de Index API. Aangezien u een batch van meerdere documenten tegelijk doorgeven aan de Index-API, is de groottelimiet realistisch afhankelijk van het aantal documenten dat zich in de batch bevindt. Voor een batch met één document is de maximale documentgrootte 16 MB JSON.

Als u de grootte van het document laag wilt houden, moet u niet-opvraagbare gegevens uit de aanvraag uitsluiten. Afbeeldingen en andere binaire gegevens zijn niet direct opvraagbaar en mogen niet worden opgeslagen in de index. Als u niet-opvraagbare gegevens wilt integreren in zoekresultaten, definieert u een niet-doorzoekbaar veld dat een URL-verwijzing naar de bron opslaat.

## <a name="indexer-limits"></a>Indexerlimieten

Er bestaan maximale gebruikstijden om balans en stabiliteit te bieden aan de service als geheel, maar grotere gegevenssets hebben mogelijk meer indexeringstijd nodig dan het maximum toestaat. Als een indexeringstaak niet binnen de maximaal toegestane tijd kan worden voltooid, probeert u deze volgens een planning uit te voeren. De planner houdt de indexeringsstatus bij. Als een geplande indexeringstaak om welke reden dan ook wordt onderbroken, kan de indexer bij de volgende geplande uitvoering verdergaan waar deze voor het laatst was gebleven.


| Resource | Gratis&nbsp;<sup>1</sup> | Basis&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;&nbsp;HD<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximale aantal indexeerfuncties |3 |5 of 15|50 |200 |200 |N.v.t. |10 |10 |
| Maximale aantal gegevensbronnen |3 |5 of 15 |50 |200 |200 |N.v.t. |10 |10 |
| Maximale skillsets <sup>4</sup> |3 |5 of 15 |50 |200 |200 |N.v.t. |10 |10 |
| Maximale indexeringsbelasting per aanroep |10.000 documenten |Alleen beperkt door maximale documenten |Alleen beperkt door maximale documenten |Alleen beperkt door maximale documenten |Alleen beperkt door maximale documenten |N.v.t. |Geen limiet |Geen limiet |
| Minimumschema | 5 minuten |5 minuten |5 minuten |5 minuten |5 minuten |5 minuten |5 minuten | 5 minuten |
| Maximale looptijd <sup>5</sup> | 1-3 minuten |24 uur |24 uur |24 uur |24 uur |N.v.t.  |24 uur |24 uur |
| Maximale looptijd voor cognitieve zoekvaardigheden of blobindexering met beeldanalyse <sup>5</sup> | 3-10 minuten |2 uur |2 uur |2 uur |2 uur |N.v.t.  |2 uur |2 uur |
| Blob-indexer: maximale blobgrootte, MB |16 |16 |128 |256 |256 |N.v.t.  |256 |256 |
| Blob-indexer: maximale tekens van inhoud die uit een blob is geëxtraheerd |32,000 |64,000 |4&nbsp;miljoen |8&nbsp;miljoen |16&nbsp;miljoen |N.v.t. |4&nbsp;miljoen |4&nbsp;miljoen |

<sup>1</sup> Gratis services hebben een maximale uitvoeringstijd van 3 minuten voor blobbronnen en 1 minuut voor alle andere gegevensbronnen. Voor AI-indexering die wordt aangeroepen in Cognitive Services, zijn gratis services beperkt tot 20 gratis transacties per dag, waarbij een transactie wordt gedefinieerd als een document dat met succes door de verrijkingspijplijn gaat.

<sup>2</sup> Basisservices die vóór december 2017 zijn gemaakt, hebben lagere limieten (5 in plaats van 15) voor indexers, gegevensbronnen en skillsets.

<sup>3</sup> S3 HD-services bevatten geen ondersteuning voor indexeren.

<sup>4</sup> Maximaal 30 vaardigheden per skillset.

<sup>5</sup> Cognitieve zoekworkloads en beeldanalyse in Azure blob-indexering hebben kortere looptijden dan gewone tekstindexering. Beeldanalyse en natuurlijke taalverwerking zijn rekenintensief en verbruiken onevenredigveel beschikbare verwerkingskracht. De looptijd is verkort om andere taken in de wachtrij een kans te geven om uit te voeren.  

> [!NOTE]
> Zoals vermeld in de [indexlimieten,](#index-limits)zullen indexers ook de bovengrens van 3000 elementen afdwingen voor alle`2019-05-06`complexe verzamelingen per document, te beginnen met de nieuwste GA API-versie die complexe typen ( ) ondersteunt. Dit betekent dat als u uw indexer met een eerdere API-versie hebt gemaakt, u niet aan deze limiet onderworpen bent. Om maximale compatibiliteit te behouden, wordt een indexer die is gemaakt `2019-05-06` met een eerdere API-versie en vervolgens bijgewerkt met een API-versie of later, nog steeds **uitgesloten** van de limieten. Klanten moeten zich bewust zijn van de negatieve impact van het hebben van zeer grote complexe collecties (zoals eerder vermeld) en we raden ten zeerste aan om nieuwe indexers te maken met de nieuwste GA API-versie.

## <a name="synonym-limits"></a>Synoniemlimieten

Het maximaal toegestane aantal synoniemenkaarten verschilt per prijscategorie. Elke regel kan maximaal 20 uitbreidingen hebben, waarbij een uitbreiding een gelijkwaardige term is. Bijvoorbeeld, gezien "kat", associatie met "kitty", "katachtige", en "felis" (het geslacht voor katten) zou tellen als 3 uitbreidingen.

| Resource | Gratis | Basic | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maximale synoniemkaarten |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maximum aantal regels per kaart |5000 |20.000|20.000 |20.000 |20.000 |20.000 | 20.000 | 20.000  |

## <a name="queries-per-second-qps"></a>Query's per seconde (QPS)

QPS schattingen moeten onafhankelijk worden ontwikkeld door elke klant. Indexgrootte en complexiteit, querygrootte en complexiteit en de hoeveelheid verkeer zijn primaire determinanten van QPS. Er is geen manier om zinvolle schattingen aan te bieden wanneer dergelijke factoren onbekend zijn.

Schattingen zijn voorspelbaarder wanneer ze worden berekend op services die worden uitgevoerd op speciale resources (basis- en standaardlagen). U QPS beter schatten omdat u controle hebt over meer parameters. Zie [Azure Cognitive Search-prestaties en -optimalisatie](search-performance-optimization.md)voor richtlijnen voor het benaderen van schattingen.

Voor de niveaus Opslag geoptimaliseerd, moet u verwachten dat een lagere query doorvoer en een hogere latentie dan de standaard lagen.  De methodologie voor het schatten van de queryprestaties die u zult ervaren, is dezelfde als de standaardlagen.

## <a name="data-limits-ai-enrichment"></a>Gegevenslimieten (AI-verrijking)

Een [AI-verrijkingspijplijn](cognitive-search-concept-intro.md) die oproepen naar een Text Analytics-bron maakt voor [entiteitsherkenning,](cognitive-search-skill-entity-recognition.md) [sleutelzinextractie,](cognitive-search-skill-keyphrases.md) [sentimentanalyse,](cognitive-search-skill-sentiment.md) [taaldetectie](cognitive-search-skill-language-detection.md)en [PII-detectie,](cognitive-search-skill-pii-detection.md) is onderworpen aan gegevenslimieten. De maximale grootte van een record moet 50.000 tekens zijn, gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Als u uw gegevens moet opsplitsen voordat u deze naar de sentimentanalyzer stuurt, gebruikt u de [vaardigheid Text Split](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Beperkingslimieten

Zoekquery- en indexeringsaanvragen worden beperkt naarmate het systeem de piekcapaciteit nadert. Throttling gedraagt zich anders voor verschillende API's. Query-API's (Zoeken/voorstellen/Automatisch aanvullen) en het indexeren van API's worden dynamisch beperkt op basis van de belasting van de service. Index-API's hebben statische limieten voor de aanvraagsnelheid. 

Statische limieten voor tariefaanvragen voor bewerkingen die verband houden met een index:

+ Lijstindexen (GET /indexen): 5 per seconde per zoekeenheid
+ Index krijgen (GET /indexen/myindex): 10 per seconde per zoekeenheid
+ Index maken (POST /indexen): 12 per minuut per zoekeenheid
+ Index maken of bijwerken (PUT /indexen/myindex): 6 per seconde per zoekeenheid
+ Index verwijderen (verwijderen /indexen/myindex): 12 per minuut per zoekeenheid 

## <a name="api-request-limits"></a>API-aanvraaglimieten
* Maximaal 16 MB per aanvraag <sup>1</sup>
* Maximale URL-lengte van 8 KB
* Maximaal 1000 documenten per batch index uploadt, voegt of verwijdert
* Maximaal 32 velden in $orderby clausule
* Maximale zoekterm grootte is 32.766 bytes (32 KB minus 2 bytes) van UTF-8 gecodeerde tekst

<sup>1</sup> In Azure Cognitive Search is de hoofdtekst van een aanvraag onderworpen aan een bovengrens van 16 MB, waarbij een praktische limiet wordt opgelegd aan de inhoud van afzonderlijke velden of verzamelingen die op een andere manier niet worden beperkt door theoretische limieten (zie [Ondersteunde gegevenstypen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) voor meer informatie over veldsamenstelling en beperkingen).

## <a name="api-response-limits"></a>API-responslimieten
* Maximaal 1000 documenten geretourneerd per pagina met zoekresultaten
* Maximaal 100 suggesties geretourneerd per API-aanvraag voorstellen

## <a name="api-key-limits"></a>API-sleutellimieten
API-sleutels worden gebruikt voor serviceverificatie. Er zijn twee typen. Beheerderssleutels worden opgegeven in de aanvraagkoptekst en verlenen volledige lees-schrijftoegang tot de service. Querysleutels zijn alleen-lezen, opgegeven op de URL en worden doorgaans gedistribueerd naar clienttoepassingen.

* Maximaal 2 beheerderssleutels per service
* Maximaal 50 querysleutels per service