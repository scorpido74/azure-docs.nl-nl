---
title: Het queryprogramma Search Explorer in Azure Portal
titleSuffix: Azure Cognitive Search
description: Gebruik in deze Azure Portal-quickstart Search Explorer om kennis op te doen van de querysyntaxis, query-expressies te testen of een zoekdocument te controleren. Search Explorer voert query's uit op indexen in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 09/25/2020
ms.openlocfilehash: adcd07ad370ad9f1301caa41c494a33958743dd8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91398405"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Quickstart: Gebruik Search Explorer om query's uit te voeren in de portal

**Search Explorer** is een ingebouwd queryprogramma dat wordt gebruikt voor het uitvoeren van query's op een zoekindex in Azure Cognitive Search. Met dit hulpprogramma kunt u eenvoudig kennis opdoen van querysyntaxis, een query- of filterexpressie testen of het vernieuwen van gegevens controleren door na te gaan of er nieuwe inhoud in de index voorkomt.

In deze quickstart wordt een bestaande index gebruikt om Search Explorer te demonstreren. Aanvragen worden geformuleerd met behulp van de [Search REST API](/rest/api/searchservice/), waarbij antwoorden worden geretourneerd als JSON-documenten.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

+ Een Azure Cognitive Search-service. [Maak een service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in uw huidige abonnement. U kunt een gratis service voor deze quickstart gebruiken. 

+ De *realestate-us-sample-index* wordt voor deze quickstart gebruikt. Gebruik de wizard [**Gegevens importeren**](search-import-data-portal.md) om de index te maken. In de eerste stap kiest u **Voorbeelden** wanneer u wordt gevraagd om de gegevensbron en selecteert u vervolgens de gegevensbron **realestate-us-sample**. Accepteer alle standaardinstellingen van de wizard voor het maken van de index.

## <a name="start-search-explorer"></a>Start Search Explorer

1. Open in [Azure Portal](https://portal.azure.com) de pagina met de zoekservice vanuit het dashboard of [zoek uw service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Open Search Explorer vanuit de opdrachtbalk:

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Search Explorer-opdracht in portal" border="false":::

    Of gebruik het ingesloten tabblad **Search Explorer** in een geopende index:

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Search Explorer-opdracht in portal" border="false":::

## <a name="unspecified-query"></a>Query zonder specificaties

Voer om de inhoud eerst in z'n algemeen te bekijken een lege zoekopdracht uit door op **Zoeken** te klikken zonder dat u zoektermen opgeeft. Een lege zoekopdracht is handig als een eerste query omdat hiermee hele documenten worden geretourneerd, zodat u de documentsamenstelling kunt bekijken. Bij een lege zoekopdracht is er geen zoekrangschikking en worden documenten geretourneerd in een willekeurige volgorde (`"@search.score": 1` voor alle documenten). Standaard worden 50 documenten geretourneerd in een zoekopdracht.

De syntaxis voor een lege zoekopdracht is `*` of `search=*`.
   
   ```http
   search=*
   ```

   **Results**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Search Explorer-opdracht in portal" border="false":::

## <a name="free-text-search"></a>Vrij zoeken in tekst

Query's in de natuurlijke taal, met of zonder operators, zijn handig voor het simuleren van door de gebruiker gedefinieerde query's die vanuit een aangepaste app naar Azure Cognitive Search worden verzonden. Alleen de velden die in de indexdefinitie zijn aangegeven als **Doorzoekbaar**, worden gescand op overeenkomsten. 

Wanneer u zoekcriteria opgeeft, zoals zoektermen of expressies, is er sprake van zoekrangschikking. Hieronder ziet u een voorbeeld van vrij zoeken in tekst.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   U kunt Ctrl-F gebruiken om in de resultaten te zoeken naar specifieke termen.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Search Explorer-opdracht in portal" border="false":::

## <a name="count-of-matching-documents"></a>Aantal overeenkomende documenten 

Voeg **$count=true** toe om het aantal matches dat in een index is gevonden op te halen. Bij een lege zoekopdracht is count het totale aantal documenten in de index. Bij een gekwalificeerde zoekopdracht staat het voor het aantal documenten dat overeenkomt met de query-invoer.

   ```http
   $count=true
   ```

   **Results**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="Search Explorer-opdracht in portal" border="false":::

## <a name="limit-fields-in-search-results"></a>De velden in de zoekresultaten beperken

Voeg [ **$select**](search-query-odata-select.md) toe om de resultaten te beperken tot de expliciet benoemde velden voor een beter leesbare uitvoer in **Search Explorer**. Als u de zoektekenreeks en **$count=true** wilt behouden, voegt u aan argumenten het voorvoegsel **&** toe. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Search Explorer-opdracht in portal" border="false":::

## <a name="return-next-batch-of-results"></a>Volgende batch met resultaten retourneren

Azure Cognitive Search retourneert de beste 50 matches op basis van de zoekrangschikking. Als u de volgende reeks overeenkomende documenten wilt ophalen, voegt u **$top=100,&$skip=50** toe om de resultatenset uit te breiden naar 100 documenten (standaardinstelling is 50, het maximumaantal is 1000), waarbij de eerste 50 documenten worden overgeslagen. Vergeet niet dat u zoekcriteria moet opgeven, zoals een queryterm of -expressie, om gerangschikte resultaten te verkrijgen. Naarmate u verder vordert in de zoekresultaten, nemen de zoekscores trouwens af.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Results**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Search Explorer-opdracht in portal" border="false":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filterexpressies (groter dan, kleiner dan, gelijk aan)

Gebruik de parameter [ **$filter**](search-query-odata-filter.md) wanneer u precieze criteria wilt opgeven in plaats van vrij zoeken in tekst. Het veld moet in de index worden aangegeven als **Filterbaar**. In dit voorbeeld wordt gezocht naar slaapkamers met meer dan drie bedden:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Results**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Search Explorer-opdracht in portal" border="false":::

## <a name="order-by-expressions"></a>Orderby-expressies

Voeg [ **$orderby**](search-query-odata-orderby.md) toe om resultaten niet alleen op zoekscore, maar ook op basis van een ander veld te sorteren. Het veld moet in de index worden aangegeven als **Sorteerbaar**. Een voorbeeldexpressie waarmee u dit kunt uitproberen, is:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Results**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="Search Explorer-opdracht in portal" border="false":::

De expressie **$filter** en de expressie **$orderby** zijn beide een OData-constructie. Zie [OData-syntaxis filteren](/rest/api/searchservice/odata-expression-syntax-for-azure-search) voor meer informatie.

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Opgedane kennis

In deze quickstart hebt u **Search Explorer** gebruikt om een query uit te voeren op een index met behulp van de REST API.

+ Resultaten worden geretourneerd als uitgebreide JSON-documenten, zodat u de documentsamenstelling en -inhoud in z'n geheel kunt bekijken. U kunt query-expressies, zoals in de voorbeelden, gebruiken om de velden die worden geretourneerd te beperken.

+ Documenten bestaan uit alle velden die in de index zijn aangegeven als **Ophaalbaar**. Als u in de portal indexkenmerken wilt bekijken, klikt u op de overzichtspagina voor zoeken in de lijst **Indexen** op *realestate-us-sample*.

+ Query's in de natuurlijke taal, vergelijkbaar met wat u in een commerciële webbrowser invoert, zijn handig voor het testen van gebruikerstoepassingen. U kunt bijvoorbeeld in het geval van de ingebouwde realestate-voorbeeldindex Seattle appartementen lake washington invoeren en vervolgens Ctrl-F gebruiken om termen in de zoekresultaten te vinden. 

+ Query- en filterexpressies worden opgesteld in een syntaxis die door Azure Cognitive Search wordt ondersteund. De standaardinstelling is een [eenvoudige syntaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search), maar u kunt desgewenst [volledige Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) gebruiken voor krachtigere query's. [Filterexpressies](/rest/api/searchservice/odata-expression-syntax-for-azure-search) hebben een OData-syntaxis.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over querystructuren en -syntaxis, gebruikt u Postman of een soortgelijk hulpprogramma om query-expressies te maken die meer onderdelen van de API benutten. De [Search REST API](/rest/api/searchservice/) is vooral nuttig voor leer- en verkenningsdoeleinden.

> [!div class="nextstepaction"]
> [Een eenvoudige query maken in Postman](search-query-simple-examples.md)