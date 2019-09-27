---
title: Service limieten voor lagen en sku's-Azure Search
description: Service limieten die worden gebruikt voor capaciteits planning en maximum aantal limieten voor aanvragen en antwoorden voor Azure Search.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.openlocfilehash: fd65bb134d9057246a1b8c5cc2986e979713d20b
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327168"
---
# <a name="service-limits-in-azure-search"></a>Service limieten in Azure Search
De maximum limieten voor opslag, werk belastingen en hoeveel heden indexen, documenten en andere objecten zijn afhankelijk van het feit of uAzure Search hebt [ingericht](search-create-service-portal.md) , **basis**, **standaard**of voor **opslag geoptimaliseerde** prijs categorieën.

+ **Gratis** is een multi tenant-gedeelde service die wordt geleverd bij uw Azure-abonnement. Indexering en query aanvragen worden uitgevoerd op replica's en partities die worden gebruikt door andere tenants.

+ **Basic** biedt specifieke computer bronnen voor productie werkbelastingen op een kleinere schaal, maar deelt een netwerk infrastructuur met andere tenants.

+ **Standaard** wordt uitgevoerd op toegewezen computers met meer opslag-en verwerkings capaciteit op elk niveau. De standaard is beschikbaar op vier niveaus: S1, S2, S3 en S3 HD.

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

| Resource | Free | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximale aantal indexen |3 |5 of 15 |50 |200 |200 |1000 per partitie of 3000 per service |10 |10 |
| Maximum aantal eenvoudige velden per index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximum aantal complexe verzamelings velden per index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximum aantal elementen in alle complexe verzamelingen per document |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximale diepte van complexe velden |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximum aantal [suggesties](https://docs.microsoft.com/rest/api/searchservice/suggesters) per index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximum aantal [Score profielen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximum aantal functies per profiel |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Basic-services die zijn gemaakt vóór december 2017 hebben lagere limieten (5 in plaats van 15) op indices. Basic-laag is de enige SKU met een ondergrens van 100 velden per index.

<a name="document-limits"></a>

## <a name="document-limits"></a>Document limieten 

Vanaf oktober 2018 zijn er geen document limieten meer<sup>1</sup> voor nieuwe services die zijn gemaakt in een wille keurige laag (Basic, S1, S2, S3, S3 HD) in een wille keurige regio. Hoewel de meeste regio's een onbeperkt aantal documenten hebben gehad sinds november/december 2017, waren er vijf regio's die de document limieten bleven oplopen. Afhankelijk van wanneer en waar u een zoek service hebt gemaakt, kunt u een service uitvoeren die nog steeds aan de document limieten is onderworpen.

Als u wilt bepalen of uw service document limieten heeft, controleert u de tegel gebruik op de pagina overzicht van uw service. Document tellingen zijn onbeperkt of zijn onderworpen aan een limiet op basis van de laag.

  ![Gebruikstegel](media/search-limits-quotas-capacity/portal-usage-tile.png)

<sup>1</sup> hoewel er geen SKU-specifieke document limieten zijn, is voor elke index nog steeds een maximale kluis limiet van toepassing om de stabiliteit van de service te garanderen. Deze limiet is afkomstig van Lucene. Elk Azure Search-document wordt intern geïndexeerd als een of meer lucene-documenten. Het aantal lucene-documenten per Azure-Zoek document is afhankelijk van het totale aantal elementen in complexe verzamelings velden. Elk element wordt als een afzonderlijk lucene-document geïndexeerd. Een document met 3 elementen in een veld met een complexe verzameling wordt bijvoorbeeld geïndexeerd als 4 lucene-documenten-1 voor het document zelf en 3 voor de elementen. Het maximum aantal lucene-documenten is ongeveer 25.000.000.000 per index.

### <a name="regions-previously-having-document-limits"></a>Regio's die eerder document limieten hebben

Als de portal een document limiet aangeeft, is de service gemaakt vóór de eind versie van 2017 of is deze gemaakt in een Data Center met clusters met lagere capaciteit voor het hosten van Azure Search Services:

+ Australië - oost
+ Azië - oost
+ India - centraal
+ Japan - west
+ US - west-centraal

Voor services die aan de document limieten voldoen, gelden de volgende maximum limieten:

|  Free | Basic | S1 | S2 | S3 | S3&nbsp;HD |
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


| Resource | Gratis&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximale aantal indexeerfuncties |3 |5 of 15|50 |200 |200 |N/A |10 |10 |
| Maximale aantal gegevensbronnen |3 |5 of 15 |50 |200 |200 |N/A |10 |10 |
| Maximum vaardig heden <sup>4</sup> |3 |5 of 15 |50 |200 |200 |N/A |10 |10 |
| Maximale indexerings belasting per aanroep |10.000 documenten |Alleen beperkt door de maximale hoeveelheid documenten |Alleen beperkt door de maximale hoeveelheid documenten |Alleen beperkt door de maximale hoeveelheid documenten |Alleen beperkt door de maximale hoeveelheid documenten |N/A |Geen limiet |Geen limiet |
| Minimale planning | 5 minuten |5 minuten |5 minuten |5 minuten |5 minuten |5 minuten |5 minuten | 5 minuten |
| Maximale uitvoerings tijd <sup>5</sup> | 1-3 minuten |24 uur |24 uur |24 uur |24 uur |N/A  |24 uur |24 uur |
| Maximale uitvoerings tijd voor cognitieve Zoek vaardig heden of BLOB-indexering met afbeeldings analyse <sup>5</sup> | 3-10 minuten |2 uur |2 uur |2 uur |2 uur |N/A  |2 uur |2 uur |
| BLOB-Indexer: maximale grootte van BLOB, MB |16 |16 |128 |256 |256 |N/A  |256 |256 |
| BLOB-Indexeer functie: maximum aantal tekens inhoud geëxtraheerd uit een BLOB |32,000 |64,000 |4&nbsp;miljoen |4&nbsp;miljoen |4&nbsp;miljoen |N/A |4&nbsp;miljoen |4&nbsp;miljoen |

<sup>1</sup> gratis services hebben de maximale uitvoerings tijd van de Indexeer functie van 3 minuten voor BLOB-bronnen en 1 minuut voor alle andere gegevens bronnen. Voor AI-indexering die in Cognitive Services aanroept, zijn gratis services beperkt tot 20 gratis trans acties per dag, waarbij een trans actie wordt gedefinieerd als een document dat door de verrijkings pijplijn is door gegeven.

<sup>2</sup> basis services die zijn gemaakt vóór december 2017 hebben lagere limieten (5 in plaats van 15) voor Indexeer functies, gegevens bronnen en vaardig heden.

<sup>3</sup> S3 HD services bevatten geen ondersteuning voor Indexeer functie.

<sup>4</sup> Maxi maal 30 vaardig heden per vaardig heden.

<sup>5</sup> cognitieve werk belastingen en afbeeldings analyse in Azure Blob-indexering hebben minder uitvoerings tijden dan gewone tekst indexering. De verwerking van afbeeldings-en natuurlijke taal wordt reken intensief uitgevoerd en verbruikt onevenredige hoeveel heden beschik bare verwerkings kracht. De uitvoerings tijd is gereduceerd om andere taken in de wachtrij te geven die een kans om te worden uitgevoerd.  

## <a name="synonym-limits"></a>Synoniemen limieten

Het Maxi maal toegestane aantal synoniemen is afhankelijk van de prijs categorie. Elke regel kan Maxi maal 20 uitbrei dingen hebben, waarbij een uitbrei ding een equivalvent term is. Als bijvoorbeeld ' kat ', ' Association ' met ' Kitty ', ' Feline ' en ' Felis ' (het genus voor katten) als drie uitbrei dingen worden beschouwd.

| Resource | Free | Basic | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maximum aantal synoniemen |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maximum aantal regels per kaart |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Query's per seconde (QPS)

QPS-schattingen moeten onafhankelijk van elke klant worden ontwikkeld. De grootte en complexiteit van de index, de grootte en complexiteit van query's en de hoeveelheid verkeer zijn primaire determinanten van QPS. Er is geen manier om zinvolle schattingen te bieden wanneer dergelijke factoren onbekend zijn.

Schattingen zijn meer voorspelbaar wanneer ze worden berekend op Services die worden uitgevoerd op speciale resources (Basic-en Standard-lagen). U kunt de QPS nauw keuriger schatten omdat u meer controle hebt over de para meters. Zie [Azure Search prestaties en optimalisatie](search-performance-optimization.md)voor meer informatie over het aanpaken van de schatting.

Voor de lagen geoptimaliseerd voor opslag moet u een lagere query door Voer en een hogere latentie hebben dan de standaard lagen.  De methodologie voor het schatten van de query prestaties die u krijgt, is hetzelfde als de standaard lagen.

## <a name="data-limits-cognitive-search"></a>Gegevens limieten (cognitieve zoek opdracht)

Een [cognitieve Zoek pijplijn](cognitive-search-concept-intro.md) die aanroepen naar een Text Analytics resource voor [entiteit herkenning](cognitive-search-skill-entity-recognition.md), [extractie van sleutel zinnen](cognitive-search-skill-keyphrases.md), [sentiment analyse](cognitive-search-skill-sentiment.md)en [taal detectie](cognitive-search-skill-language-detection.md) onderhevig is aan gegevens limieten. De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)door. Als u uw gegevens wilt opsplitsen voordat u deze naar de sentiment Analyzer verzendt, gebruikt u de functie [tekst splitsen](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>API-aanvraag limieten
* Maxi maal 16 MB per aanvraag <sup>1</sup>
* Maxi maal 8 KB URL-lengte
* Maxi maal 1000 documenten per batch met index-uploads, samen voegingen of verwijderingen
* Maxi maal 32 velden in de $orderby-component
* De maximale grootte van de zoek term is 32.766 bytes (32 KB min 2 bytes) van tekst UTF-8-code ring

<sup>1</sup> in azure Search is de hoofd tekst van een aanvraag onderhevig aan een bovengrens van 16 MB, waardoor een praktische limiet wordt opgelegd aan de inhoud van afzonderlijke velden of verzamelingen die niet anderszins beperkt zijn door theoretische limieten (Zie [ondersteunde gegevens typen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) voor meer informatie informatie over de samen stelling van velden en beperkingen).

## <a name="api-response-limits"></a>API-antwoord limieten
* Maxi maal 1000 documenten geretourneerd per pagina met zoek resultaten
* Maxi maal 100 suggesties die worden geretourneerd per API-aanvraag Voorst Ellen

## <a name="api-key-limits"></a>API-sleutel limieten
API-sleutels worden gebruikt voor service verificatie. Er zijn twee typen. Beheerders sleutels worden opgegeven in de aanvraag header en krijgen volledige lees-/schrijftoegang tot de service. Query sleutels zijn alleen-lezen, opgegeven voor de URL en worden meestal gedistribueerd naar client toepassingen.

* Maxi maal 2 beheer sleutels per service
* Maxi maal 50 query sleutels per service
