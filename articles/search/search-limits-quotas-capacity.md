---
title: Service limieten voor lagen en sku's
titleSuffix: Azure Cognitive Search
description: Service limieten die worden gebruikt voor capaciteits planning en maximum aantal limieten voor aanvragen en antwoorden voor Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 6ee339cb709a5d825b39b4accf294761c99ee41a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282976"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Service limieten in azure Cognitive Search

De maximum limieten voor opslag, werk belastingen en hoeveel heden indexen en andere objecten zijn afhankelijk van of u Azure-Cognitive Search in de **gratis**, **basis**, **standaard**of **geoptimaliseerde** prijs categorieën voor opslag [inricht](search-create-service-portal.md) .

+ **Gratis** is een multi tenant-gedeelde service die wordt geleverd bij uw Azure-abonnement. Indexering en query aanvragen worden uitgevoerd op replica's en partities die worden gebruikt door andere tenants.

+ **Basic** biedt specifieke computer bronnen voor productie werkbelastingen op een kleinere schaal, maar deelt een netwerk infrastructuur met andere tenants.

+ **Standaard** wordt uitgevoerd op toegewezen computers met meer opslag-en verwerkings capaciteit op elk niveau. Standard is beschikbaar op vier niveaus: S1, S2, S3 en S3 HD.

+ **Opslag geoptimaliseerd** wordt uitgevoerd op toegewezen computers met meer totale opslag, opslag bandbreedte en geheugen dan **standaard**. Geoptimaliseerde opslag is beschikbaar op twee niveaus: L1 en L2

> [!NOTE]
> Vanaf 1 juli zijn alle lagen algemeen beschikbaar, waaronder de laag geoptimaliseerd voor opslag. U kunt alle prijzen vinden op de pagina met [prijs informatie](https://azure.microsoft.com/pricing/details/search/) .

  S3 high density (S3 HD) is ontworpen voor specifieke werk belastingen: [multitenancy](search-modeling-multitenant-saas-applications.md) en grote hoeveel heden kleine indexen (1.000.000 documenten per index, 3000 indexen per service). Deze laag biedt geen [Indexeer functie](search-indexer-overview.md). Op S3 HD moet gegevens opname gebruikmaken van de push-benadering, met behulp van API-aanroepen om gegevens van bron naar index te pushen. 

> [!NOTE]
> Een service is ingericht op een specifieke laag. Als u de capaciteit van de laag wilt halen, moet u een nieuwe service inrichten (er is geen in-place upgrade). Zie [een SKU of laag kiezen](search-sku-tier.md)voor meer informatie. Zie voor meer informatie over het aanpassen van capaciteit in een service die u al hebt ingericht, [resource niveaus schalen voor het uitvoeren van query's en het indexeren van werk belastingen](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Abonnementslimieten
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Opslag limieten
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Index limieten

| Resource | Gratis | Basis&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximale aantal indexen |3 |5 of 15 |50 |200 |200 |1000 per partitie of 3000 per service |10 |10 |
| Maximum aantal eenvoudige velden per index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximum aantal complexe verzamelings velden per index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximum aantal elementen in alle complexe verzamelingen per document&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximale diepte van complexe velden |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximum aantal [suggesties](https://docs.microsoft.com/rest/api/searchservice/suggesters) per index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximum aantal [Score profielen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximum aantal functies per profiel |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Basic-services die zijn gemaakt vóór december 2017 hebben lagere limieten (5 in plaats van 15) op indices. Basic-laag is de enige SKU met een ondergrens van 100 velden per index.

<sup>2</sup> met een zeer groot aantal elementen in complexe verzamelingen per document wordt op dit moment veel opslag verbruikt. Dit is een bekend probleem. Ondertussen is een limiet van 3000 een veilige bovengrens voor alle service lagen. Deze limiet wordt alleen afgedwongen voor indexerings bewerkingen die gebruikmaken van de vroegst beschik bare API-versie (GA) die gebruikmaakt van complexe type velden (`2019-05-06`). Om te voor komen dat clients die eerdere preview-API-versies gebruiken (die ondersteuning bieden voor velden van het type complex), wordt deze limiet niet afgedwongen voor indexerings bewerkingen die gebruikmaken van deze preview-API-versies. Houd er rekening mee dat preview-API-versies niet bedoeld zijn om te worden gebruikt voor productie scenario's en wij raden klanten ten zeerste aan om over te stappen op de nieuwste GA API-versie.

<a name="document-limits"></a>

## <a name="document-limits"></a>Document limieten 

Vanaf oktober 2018 zijn er geen document limieten meer voor nieuwe services die zijn gemaakt in een wille keurige laag (Basic, S1, S2, S3, S3 HD) in een wille keurige regio. Hoewel de meeste regio's een onbeperkt aantal documenten hebben gehad sinds november/december 2017, waren er enkele regio's die na die datum nog steeds document limieten opleggen. Afhankelijk van wanneer en waar u een zoek service hebt gemaakt, kunt u een service uitvoeren die nog steeds aan de document limieten is onderworpen.

Als u wilt bepalen of uw service document limieten heeft, gebruikt u de [rest API service statistieken ophalen](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). De document limieten worden weer gegeven in de reactie, met `null` geen limieten.

> [!NOTE]
> Hoewel er geen SKU-specifieke document limieten zijn, is voor elke index nog steeds een maximale kluis limiet van toepassing om de stabiliteit van de service te garanderen. Deze limiet is afkomstig van Lucene. Elk Azure Cognitive Search-document wordt intern geïndexeerd als een of meer lucene-documenten. Het aantal lucene-documenten per Zoek document is afhankelijk van het totale aantal elementen in complexe verzamelings velden. Elk element wordt als een afzonderlijk lucene-document geïndexeerd. Een document met 3 elementen in een veld met een complexe verzameling wordt bijvoorbeeld geïndexeerd als 4 lucene-documenten-1 voor het document zelf en 3 voor de elementen. Het maximum aantal lucene-documenten is ongeveer 25.000.000.000 per index.

### <a name="regions-previously-having-document-limits"></a>Regio's die eerder document limieten hebben

Als de portal een document limiet aangeeft, is uw service gemaakt vóór de eind versie van 2017 of is deze gemaakt in een Data Center met clusters met lagere capaciteit voor het hosten van Azure Cognitive Search Services:

+ Australië - oost
+ Azië - oost
+ India - centraal
+ Japan - west
+ VS - west-centraal

Voor services die aan de document limieten voldoen, gelden de volgende maximum limieten:

|  Gratis | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10.000 |1&nbsp;miljoen |15 miljoen per partitie of 180 miljoen per service |60 miljoen per partitie of 720 miljoen per service |120 miljoen per partitie of 1,4 miljard per service |1 miljoen per index of 200 miljoen per partitie |

Als uw service limieten heeft die u blokkeert, maakt u een nieuwe service en publiceert u vervolgens alle inhoud opnieuw naar die service. Er is geen mechanisme voor het naadloos opnieuw inrichten van uw service op nieuwe hardware achter de schermen.

> [!Note] 
> Voor de S3-Services voor hoge dichtheid die zijn gemaakt na te laat 2017, is het 200.000.000-document per partitie verwijderd, maar blijft de limiet van 1.000.000 documenten per index.


### <a name="document-size-limits-per-api-call"></a>Maximale document grootte per API-aanroep

De maximale document grootte bij het aanroepen van een index-API is ongeveer 16 MB.

De document grootte is in feite een limiet voor de grootte van de aanvraag tekst van de index-API. Omdat u een batch van meerdere documenten tegelijk kunt door geven aan de index-API, is de grootte van het aantal documenten in de batch realistisch. Voor een batch met één document is de maximale document grootte 16 MB JSON.

Als u de document grootte wilt behouden, moet u niet-bevraagde gegevens uit de aanvraag uitsluiten. Afbeeldingen en andere binaire gegevens kunnen niet rechtstreeks worden doorzocht en mogen niet worden opgeslagen in de index. Als u niet-query bare gegevens wilt integreren in Zoek resultaten, definieert u een niet-doorzoekbaar veld waarin een URL-verwijzing naar de resource wordt opgeslagen.

## <a name="indexer-limits"></a>Limieten voor Indexeer functie

Er zijn maximale uitvoerings tijden beschikbaar om het saldo en de stabiliteit van de service als geheel te bieden, maar grotere gegevens sets hebben mogelijk meer indexerings tijd nodig dan het maximum aantal toestaat. Als een indexerings taak niet kan worden voltooid binnen de toegestane maximum tijd, voert u deze uit volgens een schema. De scheduler houdt de indexerings status bij. Als een geplande indexerings taak om welke reden dan ook wordt onderbroken, kan de Indexeer functie ophalen waar deze voor het laatst bij de volgende geplande uitvoering was gebleven.


| Resource | Gratis&nbsp;<sup>1</sup> | Basis&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximale aantal indexeerfuncties |3 |5 of 15|50 |200 |200 |N.v.t. |10 |10 |
| Maximale aantal gegevensbronnen |3 |5 of 15 |50 |200 |200 |N.v.t. |10 |10 |
| Maximum vaardig heden <sup>4</sup> |3 |5 of 15 |50 |200 |200 |N.v.t. |10 |10 |
| Maximale indexerings belasting per aanroep |10.000 documenten |Alleen beperkt door de maximale hoeveelheid documenten |Alleen beperkt door de maximale hoeveelheid documenten |Alleen beperkt door de maximale hoeveelheid documenten |Alleen beperkt door de maximale hoeveelheid documenten |N.v.t. |Geen limiet |Geen limiet |
| Minimale planning | 5 minuten |5 minuten |5 minuten |5 minuten |5 minuten |5 minuten |5 minuten | 5 minuten |
| Maximale uitvoerings tijd <sup>5</sup> | 1-3 minuten |24 uur |24 uur |24 uur |24 uur |N.v.t.  |24 uur |24 uur |
| Maximale uitvoerings tijd voor cognitieve Zoek vaardig heden of BLOB-indexering met afbeeldings analyse <sup>5</sup> | 3-10 minuten |2 uur |2 uur |2 uur |2 uur |N.v.t.  |2 uur |2 uur |
| BLOB-Indexer: maximale grootte van BLOB, MB |16 |16 |128 |256 |256 |N.v.t.  |256 |256 |
| BLOB-Indexeer functie: maximum aantal tekens inhoud geëxtraheerd uit een BLOB |32,000 |64,000 |4&nbsp;miljoen |8&nbsp;miljoen |16&nbsp;miljoen |N.v.t. |4&nbsp;miljoen |4&nbsp;miljoen |

<sup>1</sup> gratis services hebben de maximale uitvoerings tijd van de Indexeer functie van 3 minuten voor BLOB-bronnen en 1 minuut voor alle andere gegevens bronnen. Voor AI-indexering die in Cognitive Services aanroept, zijn gratis services beperkt tot 20 gratis trans acties per dag, waarbij een trans actie wordt gedefinieerd als een document dat door de verrijkings pijplijn is door gegeven.

<sup>2</sup> basis services die zijn gemaakt vóór december 2017 hebben lagere limieten (5 in plaats van 15) voor Indexeer functies, gegevens bronnen en vaardig heden.

<sup>3</sup> S3 HD services bevatten geen ondersteuning voor Indexeer functie.

<sup>4</sup> Maxi maal 30 vaardig heden per vaardig heden.

<sup>5</sup> cognitieve werk belastingen en afbeeldings analyse in Azure Blob-indexering hebben minder uitvoerings tijden dan gewone tekst indexering. De verwerking van afbeeldings-en natuurlijke taal wordt reken intensief uitgevoerd en verbruikt onevenredige hoeveel heden beschik bare verwerkings kracht. De uitvoerings tijd is gereduceerd om andere taken in de wachtrij te geven die een kans om te worden uitgevoerd.  

> [!NOTE]
> Zoals vermeld in de [index limieten](#index-limits), worden ook de bovengrens van 3000 elementen voor alle complexe verzamelingen per document afgedwongen, te beginnen met de nieuwste versie van de Ga-API die ondersteuning biedt voor complexe typen (`2019-05-06`). Dit betekent dat als u een Indexeer functie hebt gemaakt met een eerdere API-versie, u deze limiet niet onderhevig hebt. Voor het behoud van de maximale compatibiliteit wordt een Indexeer functie die is gemaakt met een eerdere API-versie en vervolgens bijgewerkt met een API-versie `2019-05-06` of hoger, nog steeds **uitgesloten** van de limieten. Klanten moeten op de hoogte zijn van de nadelige gevolgen van het hebben van zeer grote complexe verzamelingen (zoals eerder aangegeven). we raden u aan om nieuwe Indexeer functies te maken met de nieuwste versie van de GA-API.

## <a name="synonym-limits"></a>Synoniemen limieten

Het Maxi maal toegestane aantal synoniemen is afhankelijk van de prijs categorie. Elke regel kan Maxi maal 20 uitbrei dingen hebben, waarbij een uitbrei ding een gelijkwaardige term is. Als bijvoorbeeld ' kat ', ' Association ' met ' Kitty ', ' Feline ' en ' Felis ' (het genus voor katten) als drie uitbrei dingen worden beschouwd.

| Resource | Gratis | Basic | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maximum aantal synoniemen |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maximum aantal regels per kaart |5000 |20.000|20.000 |20.000 |20.000 |20.000 | 20.000 | 20.000  |

## <a name="queries-per-second-qps"></a>Query's per seconde (QPS)

QPS-schattingen moeten onafhankelijk van elke klant worden ontwikkeld. De grootte en complexiteit van de index, de grootte en complexiteit van query's en de hoeveelheid verkeer zijn primaire determinanten van QPS. Er is geen manier om zinvolle schattingen te bieden wanneer dergelijke factoren onbekend zijn.

Schattingen zijn meer voorspelbaar wanneer ze worden berekend op Services die worden uitgevoerd op speciale resources (Basic-en Standard-lagen). U kunt de QPS nauw keuriger schatten omdat u meer controle hebt over de para meters. Zie [Azure Cognitive Search prestaties en optimalisatie](search-performance-optimization.md)voor meer informatie over het aanpaken van de schatting.

Voor de lagen geoptimaliseerd voor opslag moet u een lagere query door Voer en een hogere latentie hebben dan de standaard lagen.  De methodologie voor het schatten van de query prestaties die u krijgt, is hetzelfde als de standaard lagen.

## <a name="data-limits-ai-enrichment"></a>Gegevens limieten (AI-verrijking)

Een [AI-verrijkings pijplijn](cognitive-search-concept-intro.md) die aanroepen naar een Text Analytics bron voor [entiteit herkenning](cognitive-search-skill-entity-recognition.md), [extractie van sleutel zinnen](cognitive-search-skill-keyphrases.md), [sentiment analyse](cognitive-search-skill-sentiment.md), [taal detectie](cognitive-search-skill-language-detection.md)en [PII-detectie](cognitive-search-skill-pii-detection.md) is onderhevig aan gegevens limieten. De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Als u uw gegevens wilt opsplitsen voordat u deze naar de sentiment Analyzer verzendt, gebruikt u de functie [tekst splitsen](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Beperkings limieten

Zoek query's en indexerings aanvragen worden beperkt naarmate het systeem de piek capaciteit benadert. Bandbreedte beperking werkt anders voor verschillende Api's. Query-Api's (zoeken/suggesties/automatisch volt ooien) en het indexeren van Api's worden dynamisch beperkt op basis van de belasting van de service. Index-Api's hebben limieten voor statisch aantal aanvragen. 

Limieten voor statische frequentie aanvragen voor bewerkingen met betrekking tot een index:

+ Lijst indexen (GET/Indexes): 5 per seconde per Zoek eenheid
+ Index ophalen (GET/Indexes/MyIndex): 10 per seconde per Zoek eenheid
+ Create Index (POST/Indexes): 12 per minuut per Zoek eenheid
+ Index maken of bijwerken (PUT/Indexes/MyIndex): 6 per seconde per Zoek eenheid
+ Index verwijderen (/Indexes/MyIndex verwijderen): 12 per minuut per Zoek eenheid 

## <a name="api-request-limits"></a>API-aanvraag limieten
* Maxi maal 16 MB per aanvraag <sup>1</sup>
* Maxi maal 8 KB URL-lengte
* Maxi maal 1000 documenten per batch met index-uploads, samen voegingen of verwijderingen
* Maxi maal 32 velden in de $orderby-component
* De maximale grootte van de zoek term is 32.766 bytes (32 KB min 2 bytes) van tekst UTF-8-code ring

<sup>1</sup> In Azure Cognitive Search is de hoofd tekst van een aanvraag onderhevig aan een bovengrens van 16 MB, waardoor een praktische limiet wordt opgelegd aan de inhoud van afzonderlijke velden of verzamelingen die niet anderszins beperkt zijn door theoretische limieten (Zie [ondersteunde gegevens typen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) voor meer informatie over veld samenstelling en beperkingen).

## <a name="api-response-limits"></a>API-antwoord limieten
* Maxi maal 1000 documenten geretourneerd per pagina met zoek resultaten
* Maxi maal 100 suggesties die worden geretourneerd per API-aanvraag Voorst Ellen

## <a name="api-key-limits"></a>API-sleutel limieten
API-sleutels worden gebruikt voor service verificatie. Er zijn twee typen. Beheerders sleutels worden opgegeven in de aanvraag header en krijgen volledige lees-/schrijftoegang tot de service. Query sleutels zijn alleen-lezen, opgegeven voor de URL en worden meestal gedistribueerd naar client toepassingen.

* Maxi maal 2 beheer sleutels per service
* Maxi maal 50 query sleutels per service