---
title: Queryhulpprogramma voor zoekverkenners in Azure-portal
titleSuffix: Azure Cognitive Search
description: Gebruik in deze Snelstart van Azure-portal Zoekverkenner om querysyntaxis, test queryexpressies of inspecteer een zoekdocument. Zoekexplorer-query's indexen in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369666"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Snelstart: zoekverkenner gebruiken om query's uit te voeren in de portal

**Search explorer** is een ingebouwd queryhulpprogramma dat wordt gebruikt voor het uitvoeren van query's tegen een zoekindex in Azure Cognitive Search. Met dit hulpprogramma u eenvoudig de syntaxis van query's leren, een query of filterexpressie testen of de resultaten van een indexvernieuwing bevestigen door te controleren of er nieuwere inhoud bestaat.

Deze quickstart maakt gebruik **van realestate-us-sample-index** om Search explorer aan te tonen. Aanvragen worden geformuleerd met behulp van de [Search REST API,](https://docs.microsoft.com/rest/api/searchservice/)met antwoorden geretourneerd als JSON-documenten.

## <a name="prerequisites"></a>Vereisten

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U gebruik maken van een gratis service voor deze quickstart.

+ **realestate-us-sample-index** wordt gebruikt voor deze quickstart. Stap door de wizard [**Gegevens importeren**](search-import-data-portal.md) om de index te genereren uit de ingebouwde gegevensbron van voorbeelden.

## <a name="start-search-explorer"></a>Zoekverkenner starten

1. Open in de [Azure-portal](https://portal.azure.com)de pagina met zoekservice vanuit het dashboard of [zoek uw service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Open Zoekverkenner vanaf de opdrachtbalk:

   ![Opdracht Verkenner zoeken in portal](./media/search-explorer/search-explorer-cmd2.png "Opdracht Verkenner zoeken in portal")

    Of gebruik het ingesloten tabblad **Zoekverkenner** op een geopende index:

   ![Tabblad Verkenner](./media/search-explorer/search-explorer-tab.png "Tabblad Verkenner")

## <a name="unspecified-query"></a>Niet-gespecificeerde query

Voer voor een eerste blik op de inhoud een lege zoekopdracht uit door op **Zoeken** te klikken zonder dat er termen zijn opgegeven. Een lege zoekopdracht is handig als eerste query omdat volledige documenten worden geretourneerd, zodat u de documentsamenstelling controleren. Bij een lege zoekopdracht is er geen zoekrang en`"@search.score": 1` worden documenten in willekeurige volgorde geretourneerd (voor alle documenten). Standaard worden 50 documenten geretourneerd in een zoekverzoek.

Gelijkwaardige syntaxis voor `*` een `search=*`lege zoekopdracht is of .
   
   ```http
   search=*
   ```

   **Resultaten**
   
   ![Voorbeeld van lege query](./media/search-explorer/search-explorer-example-empty.png "Voorbeeld van ongekwalificeerde of lege query")

## <a name="free-text-search"></a>Gratis tekst zoeken

Query's in vrije vorm, met of zonder operatoren, zijn handig voor het simuleren van door de gebruiker gedefinieerde query's die vanuit een aangepaste app naar Azure Cognitive Search worden verzonden. Alleen de velden die worden toegeschreven als **Doorzoekbaar** in de indexdefinitie worden gescand op overeenkomsten. 

Wanneer u zoekcriteria opgeeft, zoals querytermen of expressies, wordt de zoekrang in het spel gebracht. In het volgende voorbeeld wordt een gratis tekstzoekopdracht weergegeven.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultaten**

   U Ctrl-F gebruiken om in de resultaten te zoeken naar specifieke termen die interessant zijn.

   ![Voorbeeld van gratis tekstquery](./media/search-explorer/search-explorer-example-freetext.png "Voorbeeld van gratis tekstquery")

## <a name="count-of-matching-documents"></a>Aantal overeenkomende documenten 

Voeg **$count=true toe** om het aantal overeenkomsten in een index te krijgen. Bij een lege zoekopdracht is het aantal het totale aantal documenten in de index. Bij een gekwalificeerde zoekopdracht is dit het aantal documenten dat overeenkomt met de queryinvoer.

   ```http
   $count=true
   ```

   **Resultaten**

   ![Aantal documenten voorbeeld](./media/search-explorer/search-explorer-example-count.png "Aantal overeenkomende documenten in index")

## <a name="limit-fields-in-search-results"></a>Velden in zoekresultaten beperken

Voeg [**$select**](search-query-odata-select.md) toe om resultaten te beperken tot de expliciet benoemde velden voor meer leesbare uitvoer in **Zoekverkenner**. Als u de zoektekenreeks en **$count=true wilt**behouden, u argumenten vooraf weergeven met **&**. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultaten**

   ![Voorbeeld van velden beperken](./media/search-explorer/search-explorer-example-selectfield.png "Velden in zoekresultaten beperken")

## <a name="return-next-batch-of-results"></a>Volgende batch resultaten retourneren

Azure Cognitive Search retourneert de top 50-overeenkomsten op basis van de zoekpositie. Als u de volgende set overeenkomende documenten wilt krijgen, wordt **$top=100.&$skip=50** toegevoegd om het resultaat dat is ingesteld op 100 documenten te verhogen (standaard is 50, maximaal 1000), waarbij de eerste 50 documenten worden overgeslagen. Bedenk dat u zoekcriteria, zoals een queryterm of -expressie, moet verstrekken om gerangschikte resultaten te krijgen. Merk op dat zoekscores afnemen hoe dieper je in de zoekresultaten.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultaten**

   ![Zoekresultaten voor batch's](./media/search-explorer/search-explorer-example-topskip.png "Volgende batch zoekresultaten retourneren")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filterexpressies (groter dan, minder dan, gelijk aan)

Gebruik de [**parameter $filter**](search-query-odata-filter.md) wanneer u precieze criteria wilt opgeven in plaats van het zoeken naar vrije tekst. Het veld moet worden toegeschreven als **Filterbaar** in de index. In dit voorbeeld wordt gezocht naar slaapkamers groter dan 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultaten**

   ![Filterexpressie](./media/search-explorer/search-explorer-example-filter.png "Filteren op criteria")

## <a name="order-by-expressions"></a>Volgorde-door-expressies

Voeg [**$orderby**](search-query-odata-orderby.md) toe om resultaten te sorteren op een ander veld naast de zoekscore. Het veld moet worden toegeschreven als **Sorteerbaar** in de index. Een voorbeeldexpressie die u gebruiken om dit uit te testen, is:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultaten**

   ![Volgordeperexpressie](./media/search-explorer/search-explorer-example-ordery.png "De sorteervolgorde wijzigen")

Zowel **$filter** als **$orderby** expressies zijn OData-constructies. Zie [OData-syntaxis filteren](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) voor meer informatie.

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Opgedane kennis

In deze snelstart hebt u **Search explorer** gebruikt om een index op te vragen met behulp van de REST API.

+ Resultaten worden geretourneerd als uitgebreide JSON-documenten, zodat u de documentconstructie en -inhoud in zijn geheel bekijken. U queryexpressies gebruiken, weergegeven in de voorbeelden, om te beperken welke velden worden geretourneerd.

+ Documenten bestaan uit alle velden die zijn gemarkeerd als **Opvraagbaar** in de index. Als u indexkenmerken in de portal wilt weergeven, klikt u op *het voorbeeld van onroerend goed en ons* in de lijst **Indexen** op de pagina met zoekresultaten.

+ Query's in vrije vorm, vergelijkbaar met wat u in een commerciële webbrowser invoeren, zijn handig voor het testen van een gebruikerservaring. Bijvoorbeeld, uitgaande van de ingebouwde onroerend goed monster index, u in te voeren Seattle appartementen lake Washington, en dan u ctrl-F gebruiken om termen te vinden in de zoekresultaten. 

+ Query- en filterexpressies worden gearticuleerd in een syntaxis die wordt ondersteund door Azure Cognitive Search. De standaardis een [eenvoudige syntaxis,](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)maar u optioneel [volledige Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) gebruiken voor krachtigere query's. [Filterexpressies](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) zijn een syntaxis van OData.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling **Alle resources** of **Resourcegroepen** in het linkernavigatiedeelvenster.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u beperkt bent tot drie indexen, indexeerders en gegevensbronnen. U afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over querystructuren en syntaxis gebruikt u Postman of een gelijkwaardig hulpprogramma om queryexpressies te maken die meer onderdelen van de API gebruiken. De [Search REST API](https://docs.microsoft.com/rest/api/searchservice/) is vooral handig voor leren en verkennen.

> [!div class="nextstepaction"]
> [Een basisquery maken in Postman](search-query-simple-examples.md)