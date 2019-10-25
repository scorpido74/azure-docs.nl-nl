---
title: Zoek opdracht Verkenner gebruiken voor het opvragen van gegevens in Azure Portal
titleSuffix: Azure Cognitive Search
description: Search Explorer is ingebouwd in de Azure Portal, handig voor het verkennen van inhoud en het valideren van query's in azure Cognitive Search. Voer teken reeksen in voor de term of woordgroepen zoeken, of volledig gekwalificeerde Zoek expressies met een geavanceerde syntaxis.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0bb50bd50c58b85f1d5de100c34d00d114a9ed77
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792968"
---
# <a name="use-search-explorer-in-the-azure-portal-for-querying-documents-in-azure-cognitive-search"></a>Zoek Verkenner gebruiken in de Azure Portal voor het opvragen van documenten in azure Cognitive Search 

Dit artikel laat u zien hoe u een query kunt uitvoeren op een bestaande Azure Cognitive Search-index met behulp van **Search Explorer** in de Azure Portal. U kunt Search Explorer starten vanaf de opdracht balk om eenvoudige of volledige lucene-query-expressies naar een wille keurige bestaande index in uw service te verzenden. 

   ![Zoek opdracht Verkenner in de portal](./media/search-explorer/search-explorer-cmd2.png "Zoek opdracht Verkenner in de portal")

## <a name="basic-search-strings"></a>Eenvoudige Zoek reeksen

In de volgende voor beelden wordt ervan uitgegaan dat de ingebouwde voor beeld-index van onroerend goed is. U kunt deze index maken met behulp van de wizard gegevens importeren in de portal en voor **beelden** kiezen als de gegevens bron.

### <a name="example-1---empty-search"></a>Voor beeld 1: lege zoek opdracht

Voer een lege zoek opdracht uit als u de inhoud voor het eerst wilt bekijken en klik op **zoeken** zonder voor waarden. Een lege zoek opdracht is handig als eerste query omdat hiermee hele documenten worden geretourneerd, zodat u de document samenstelling kunt controleren. Bij een lege zoek opdracht zijn er geen zoek volgorde en documenten worden geretourneerd in een wille keurige volg orde (`"@search.score": 1` voor alle documenten). Standaard worden 50-documenten geretourneerd in een zoek opdracht.

Een gelijkwaardige syntaxis voor een lege zoek opdracht is `*` of `search=*`.

   ```Input
   search=*
   ```

   **Results**
   
   ![Voor beeld van lege query](./media/search-explorer/search-explorer-example-empty.png "Voor beeld van niet-gekwalificeerde of lege query")

### <a name="example-2---free-text-search"></a>Voor beeld 2: zoeken in vrije tekst

Vrije-vorm query's, met of zonder Opera Tors, zijn handig voor het simuleren van door de gebruiker gedefinieerde query's die vanuit een aangepaste app naar Azure Cognitive Search worden verzonden. Wanneer u query termen of-expressies opgeeft, komt de zoek prioriteit in afspeel. In het volgende voor beeld ziet u een zoek opdracht met vrije tekst.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   U kunt CTRL-F gebruiken om binnen de resultaten te zoeken naar specifieke gebruiks voorwaarden.

   ![Voor beeld van een vrije-tekst query](./media/search-explorer/search-explorer-example-freetext.png "Voor beeld van een vrije-tekst query")

### <a name="example-3---count-of-matching-documents"></a>Voor beeld 3: aantal overeenkomende documenten 

Voeg **$Count** toe om het aantal overeenkomsten op te halen dat in een index is gevonden. Bij een lege zoek opdracht is Count het totale aantal documenten in de index. Bij een gekwalificeerde zoek opdracht is het het aantal documenten dat overeenkomt met de invoer van de query.

   ```Input1
   $count=true
   ```
   **Results**

   ![Voor beeld van aantal documenten](./media/search-explorer/search-explorer-example-count.png "Aantal overeenkomende documenten in de index")

### <a name="example-4---restrict-fields-in-search-results"></a>Voor beeld 4: velden in Zoek resultaten beperken

Voeg **$Select** toe om de resultaten te beperken tot de expliciet benoemde velden voor meer Lees bare uitvoer in **Search Explorer**. Voor het behoud van de zoek teken reeks en **$Count = True**, voorvoegsel argumenten met **&** . 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Voor beeld van limiet velden](./media/search-explorer/search-explorer-example-selectfield.png "Velden in Zoek resultaten beperken")

### <a name="example-5---return-next-batch-of-results"></a>Voor beeld 5: volgende batch met resultaten retour neren

Azure Cognitive Search retourneert de beste 50 overeenkomsten op basis van de zoek positie. Als u de volgende set overeenkomende documenten wilt ophalen, voegt u **$Top = 100 toe, & $Skip = 50** om de resultatenset te verhogen naar 100-documenten (de standaard waarde is 50, maximum is 1000), waarbij de eerste 50 documenten worden overgeslagen. U moet zoek criteria opgeven, zoals een query term of expressie, om gerangschikte resultaten te krijgen. U ziet dat zoek scores de diep gaande Zoek resultaten verlagen.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Results**

   ![Zoek resultaten batch](./media/search-explorer/search-explorer-example-topskip.png "Volgende batch met zoek resultaten retour neren")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filter expressies (groter dan, kleiner dan, gelijk aan)

Gebruik de para meter **$filter** als u nauw keurige criteria wilt opgeven in plaats van zoeken met vrije tekst. In dit voor beeld wordt gezocht naar slaap kamers groter dan 3:

   ```Input
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Results**

   ![Filter expressie](./media/search-explorer/search-explorer-example-filter.png "Filteren op criteria")

## <a name="order-by-expressions"></a>Order-by-expressies

Voeg **$OrderBy** toe om resultaten te sorteren op een ander veld behalve een zoek Score. Een voorbeeld expressie die u kunt gebruiken om dit uit te testen is:

   ```Input
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Results**

   ![OrderBy-expressie](./media/search-explorer/search-explorer-example-ordery.png "De sorteer volgorde wijzigen")

Zowel **$filter** -als **$OrderBy** -expressies zijn OData-constructies. Zie [OData-syntaxis filteren](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) voor meer informatie.

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Zoeken Verkenner starten

1. Open in de [Azure Portal](https://portal.azure.com)de pagina zoek service in het dash board of [Zoek uw service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in de lijst met Services.

2. Klik op de pagina overzicht van services op **Search Explorer**.

   ![Zoek opdracht Verkenner in de portal](./media/search-explorer/search-explorer-cmd2.png "Zoek opdracht Verkenner in de portal")

3. Selecteer de index die u wilt opvragen.

   ![Selecteer de index die u wilt doorzoeken](./media/search-explorer/search-explorer-changeindex-se2.png "De index selecteren")

4. U kunt desgewenst de API-versie instellen. De huidige algemeen beschik bare API-versie is standaard geselecteerd, maar u kunt een voor beeld of een oudere API kiezen als de syntaxis die u wilt gebruiken, versie-specifiek is.

5. Zodra de index-en API-versie is geselecteerd, voert u zoek termen of Fully Qualified query expressies in de zoek balk in en klikt u op **zoeken** om uit te voeren.

   ![Voer zoek termen in en klik op zoeken](./media/search-explorer/search-explorer-query-string-example.png "Voer zoek termen in en klik op zoeken")

Tips voor zoeken in **Search Explorer**:

+ Resultaten worden geretourneerd als uitgebreide JSON-documenten, zodat u de document constructie en-inhoud volledig kunt weer geven. U kunt query-expressies die in de voor beelden worden weer gegeven, gebruiken om te beperken welke velden worden geretourneerd.

+ Documenten bestaan uit alle velden die in de index zijn gemarkeerd als **ophalen** . Als u index kenmerken wilt weer geven in de portal, klikt u in de lijst **indexen** op de pagina overzicht van zoek opdrachten op *realestate-VS-voor beeld* .

+ Vrije-vorm query's, vergelijkbaar met wat u in een commerciële webbrowser kunt invoeren, zijn handig voor het testen van de ervaring van de eind gebruiker. Als u bijvoorbeeld de ingebouwde realestate-voor beeld-index gebruikt, kunt u ' Seattle appartementen Lake Washington ' invoeren en vervolgens CTRL-F gebruiken om voor waarden te vinden in de zoek resultaten. 

+ Query-en filter expressies moeten worden gegeleeerd in een syntaxis die wordt ondersteund door Azure Cognitive Search. De standaard instelling is een [eenvoudige syntaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), maar u kunt eventueel [volledige lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) gebruiken voor krachtiger query's. [Filter expressies](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) zijn een OData-syntaxis.


## <a name="next-steps"></a>Volgende stappen

De volgende resources bieden extra informatie over querysyntaxis en voorbeelden.

 + [Vereenvoudigde querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene-querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene-queryvoorbeelden](search-query-lucene-examples.md) 
 + [OData-filterexpressiesyntaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
