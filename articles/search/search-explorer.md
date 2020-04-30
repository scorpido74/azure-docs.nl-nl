---
title: Query hulp programma voor zoeken in Verkenner in Azure Portal
titleSuffix: Azure Cognitive Search
description: In deze Azure Portal Snelstartgids gebruikt u Search Explorer om query syntaxis te leren, query-expressies te testen of een zoek document te controleren. Zoek opdrachten van Search Explorer-indexen in azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80369666"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Snelstartgids: Search Explorer gebruiken om query's uit te voeren in de portal

**Search Explorer** is een ingebouwd query programma dat wordt gebruikt voor het uitvoeren van query's op een zoek index in azure Cognitive Search. Met dit hulp programma kunt u eenvoudig query syntaxis leren, een query of filter expressie testen of de resultaten van een index vernieuwen bevestigen door nieuwe inhoud te controleren.

Deze Snelstartgids maakt gebruik van **realestate-US-sample-index** om Search Explorer te demonstreren. Aanvragen worden geformuleerd met behulp van de [zoek rest API](https://docs.microsoft.com/rest/api/searchservice/), waarbij reacties worden geretourneerd als JSON-documenten.

## <a name="prerequisites"></a>Vereisten

+ [Een Azure Cognitive Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze Quick Start gebruiken.

+ **realestate-US-sample-index** wordt gebruikt voor deze Quick Start. U kunt de wizard [**gegevens importeren**](search-import-data-portal.md) stapsgewijs door lopen en de index genereren op basis van de ingebouwde voor beelden van gegevens bronnen.

## <a name="start-search-explorer"></a>Zoek Verkenner starten

1. Open in de [Azure Portal](https://portal.azure.com)de pagina zoek service in het dash board of [Zoek uw service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Open Search Explorer via de opdracht balk:

   ![Zoek opdracht Verkenner in de portal](./media/search-explorer/search-explorer-cmd2.png "Zoek opdracht Verkenner in de portal")

    Of gebruik het Inge sloten tabblad **Zoek Verkenner** van een geopende index:

   ![Tabblad Zoeken in Verkenner](./media/search-explorer/search-explorer-tab.png "Tabblad Zoeken in Verkenner")

## <a name="unspecified-query"></a>Niet-opgegeven query

Voer een lege zoek opdracht uit als u de inhoud voor het eerst wilt bekijken en klik op **zoeken** zonder voor waarden. Een lege zoek opdracht is handig als eerste query omdat hiermee hele documenten worden geretourneerd, zodat u de document samenstelling kunt controleren. Bij een lege zoek opdracht zijn er geen zoek volgorde en documenten worden in wille keurige`"@search.score": 1` volg orde geretourneerd (voor alle documenten). Standaard worden 50-documenten geretourneerd in een zoek opdracht.

Een gelijkwaardige syntaxis voor een lege `*` Zoek `search=*`opdracht is of.
   
   ```http
   search=*
   ```

   **Resultaten**
   
   ![Voor beeld van lege query](./media/search-explorer/search-explorer-example-empty.png "Voor beeld van niet-gekwalificeerde of lege query")

## <a name="free-text-search"></a>Vrije tekst zoeken

Vrije-vorm query's, met of zonder Opera Tors, zijn handig voor het simuleren van door de gebruiker gedefinieerde query's die vanuit een aangepaste app naar Azure Cognitive Search worden verzonden. Alleen de velden die in de index definitie kunnen worden **doorzocht** , worden gescand op overeenkomsten. 

U ziet dat wanneer u zoek criteria opgeeft, zoals query termen of expressies, de zoek positie in Play is. In het volgende voor beeld ziet u een zoek opdracht met vrije tekst.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultaten**

   U kunt CTRL-F gebruiken om binnen de resultaten te zoeken naar specifieke gebruiks voorwaarden.

   ![Voor beeld van een vrije-tekst query](./media/search-explorer/search-explorer-example-freetext.png "Voor beeld van een vrije-tekst query")

## <a name="count-of-matching-documents"></a>Aantal overeenkomende documenten 

Voeg **$Count = True** toe om het aantal overeenkomsten op te halen dat in een index is gevonden. Bij een lege zoek opdracht is Count het totale aantal documenten in de index. Bij een gekwalificeerde zoek opdracht is het het aantal documenten dat overeenkomt met de invoer van de query.

   ```http
   $count=true
   ```

   **Resultaten**

   ![Voor beeld van aantal documenten](./media/search-explorer/search-explorer-example-count.png "Aantal overeenkomende documenten in de index")

## <a name="limit-fields-in-search-results"></a>De velden in de zoek resultaten beperken

Voeg [**$Select**](search-query-odata-select.md) toe om de resultaten te beperken tot de expliciet benoemde velden voor meer Lees bare uitvoer in **Search Explorer**. Als u de zoek teken reeks en **$Count = True**, voorvoegsel argumenten **&** met wilt blijven gebruiken. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultaten**

   ![Voor beeld van limiet velden](./media/search-explorer/search-explorer-example-selectfield.png "Velden in Zoek resultaten beperken")

## <a name="return-next-batch-of-results"></a>Volgende batch met resultaten retour neren

Azure Cognitive Search retourneert de beste 50 overeenkomsten op basis van de zoek positie. Als u de volgende set overeenkomende documenten wilt ophalen, voegt u **$Top = 100, &$Skip = 50** toe om de resultatenset te verhogen naar 100 documenten (de standaard waarde is 50, maximum is 1000), waarbij de eerste 50 documenten worden overgeslagen. U moet zoek criteria opgeven, zoals een query term of expressie, om gerangschikte resultaten te krijgen. U ziet dat zoek scores de diep gaande Zoek resultaten verlagen.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultaten**

   ![Zoek resultaten batch](./media/search-explorer/search-explorer-example-topskip.png "Volgende batch met zoek resultaten retour neren")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filter expressies (groter dan, kleiner dan, gelijk aan)

Gebruik de para meter [**$filter**](search-query-odata-filter.md) als u nauw keurige criteria wilt opgeven in plaats van zoeken met vrije tekst. Het veld moet worden toegekend als **filterbaar** in de index. In dit voor beeld wordt gezocht naar slaap kamers groter dan 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultaten**

   ![Filter expressie](./media/search-explorer/search-explorer-example-filter.png "Filteren op criteria")

## <a name="order-by-expressions"></a>Order-by-expressies

Voeg [**$OrderBy**](search-query-odata-orderby.md) toe om resultaten te sorteren op een ander veld behalve een zoek Score. Het veld moet worden geschreven als **sorteerbaar** in de index. Een voorbeeld expressie die u kunt gebruiken om dit uit te testen is:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultaten**

   ![OrderBy-expressie](./media/search-explorer/search-explorer-example-ordery.png "De sorteervolgorde wijzigen")

Zowel **$filter** -als **$OrderBy** -expressies zijn OData-constructies. Zie [OData-syntaxis filteren](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) voor meer informatie.

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Opgedane kennis

In deze Quick Start hebt u **Search Explorer** gebruikt om een index op te vragen met behulp van de rest API.

+ Resultaten worden geretourneerd als uitgebreide JSON-documenten, zodat u de document constructie en-inhoud volledig kunt weer geven. U kunt query-expressies die in de voor beelden worden weer gegeven, gebruiken om te beperken welke velden worden geretourneerd.

+ Documenten bestaan uit alle velden die in de index zijn gemarkeerd als **ophalen** . Als u index kenmerken wilt weer geven in de portal, klikt u in de lijst **indexen** op de pagina overzicht van zoek opdrachten op *realestate-VS-voor beeld* .

+ Vrije-vorm query's, vergelijkbaar met wat u in een commerciële webbrowser kunt invoeren, zijn handig voor het testen van de ervaring van de eind gebruiker. Als u bijvoorbeeld de ingebouwde realestate-voor beeld-index gebruikt, kunt u ' Seattle appartementen Lake Washington ' invoeren en vervolgens CTRL-F gebruiken om voor waarden te vinden in de zoek resultaten. 

+ Query-en filter expressies worden gegeleeerd in een syntaxis die wordt ondersteund door Azure Cognitive Search. De standaard instelling is een [eenvoudige syntaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), maar u kunt eventueel [volledige lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) gebruiken voor krachtiger query's. [Filter expressies](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) zijn een OData-syntaxis.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over query structuren en syntaxis gebruikt u postman of een gelijkwaardig hulp programma om query-expressies te maken die meer delen van de API gebruiken. De [zoek rest API](https://docs.microsoft.com/rest/api/searchservice/) is met name handig voor leren en verkennen.

> [!div class="nextstepaction"]
> [Een eenvoudige query maken in postman](search-query-simple-examples.md)