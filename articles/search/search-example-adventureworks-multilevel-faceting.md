---
title: 'Voorbeeld: facetten op meerdere niveaus'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het bouwen van facetingstructuren voor taxonomies op meerdere niveaus, waardoor een geneste navigatiestructuur wordt gecreatied die u op toepassingspagina's opnemen.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792947"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Voorbeeld: facetten op meerdere niveaus in Azure Cognitive Search

Azure Cognitive Search-schema's ondersteunen niet expliciet taxonomiecategorieën op meerdere niveaus, maar u ze benaderen door inhoud te manipuleren voordat u de indexering doet en vervolgens een speciale verwerking toe te passen op de resultaten. 

## <a name="start-with-the-data"></a>Begin met de gegevens

Het voorbeeld in dit artikel bouwt voort op een vorig voorbeeld, [Modelthe AdventureWorks Inventory database](search-example-adventureworks-modeling.md), om faceting op meerdere niveaus aan te tonen in Azure Cognitive Search.

AdventureWorks heeft een eenvoudige twee-niveau taxonomie met een ouder-kind relatie. Voor taxonomiediepten met een vaste lengte van deze structuur kan een eenvoudige SQL join-query worden gebruikt om de taxonomie te groeperen:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Queryresultaten](./media/search-example-adventureworks/prod-query-results.png "Queryresultaten")

## <a name="indexing-to-a-collection-field"></a>Indexeren naar een verzamelingsveld

Maak in de index met deze structuur een veld **Verzameling (Edm.String)** in het Azure Cognitive Search-schema om deze gegevens op te slaan, zodat veldkenmerken doorzoekbaar, filterbaar, facetable en opvraagbaar zijn.

Nu, bij het indexeren van inhoud die verwijst naar een specifieke taxonomie categorie, dien de taxonomie als een array met tekst van elk niveau van de taxonomie. Voor een entiteit met `ProductCategoryId = 5 (Mountain Bikes)`, dien het veld bijvoorbeeld in als`[ "Bikes", "Bikes|Mountain Bikes"]`

Let op de opname van de oudercategorie "Fietsen" in de kindercategoriewaarde "Mountainbikes". Elke subcategorie moet zijn hele pad insluiten ten opzichte van het hoofdelement. De pipe character separator is willekeurig, maar moet consistent zijn en mag niet in de brontekst worden weergegeven. Het scheidingsteken wordt gebruikt in de toepassingscode om de taxonomieboom te reconstrueren op basis van facetresultaten.

## <a name="construct-the-query"></a>De query construeren

Bij het uitgeven van query's, voeg de volgende facet specificatie (waar taxonomie is uw facetable taxonomie veld):`facet = taxonomy,count:50,sort:value`

De telwaarde moet hoog genoeg zijn om alle mogelijke taxonomiewaarden terug te geven. De AdventureWorks-gegevens bevatten 41 verschillende taxonomiewaarden, dus `count:50` dat is voldoende.

  ![Gefacetteerd filter](./media/search-example-adventureworks/facet-filter.png "Gefacetteerd filter")

## <a name="build-the-structure-in-client-code"></a>De structuur in clientcode bouwen

Reconstrueren in uw clienttoepassingscode de taxonomieboom door elke facetwaarde op het pijpteken te splitsen.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

Het **object categorieën** kan nu worden gebruikt om een inklapbare taxonomieboom met nauwkeurige tellingen weer te geven:

  ![gefacetteerd filter op meerdere niveaus](./media/search-example-adventureworks/multi-level-facet.png "gefacetteerd filter op meerdere niveaus")

 
Elke koppeling in de structuur moet het bijbehorende filter toepassen. Bijvoorbeeld:

+ **taxonomie/eventuele** `(x:x eq 'Accessories')` retourneert alle documenten in de accessoiresbranche
+ **taxonomie/eventuele** `(x:x eq 'Accessories|Bike Racks')` retourneert alleen de documenten met een subcategorie fietsenrekken onder de accessoirestak.

Deze techniek wordt geschaald om complexere scenario's te dekken, `Bike Components|Forks` zoals diepere taxonomiebomen en gedupliceerde subcategorieën die zich voordoen onder verschillende bovenliggende categorieën (bijvoorbeeld en `Camping Equipment|Forks`).

> [!TIP]
> De querysnelheid wordt beïnvloed door het aantal geretourneerde facetten. Als u zeer grote taxonomiesets wilt ondersteunen, u overwegen een veld facetable **Edm.String** toe te voegen om de taxonomiewaarde op het hoogste niveau voor elk document te behouden. Pas vervolgens dezelfde techniek hierboven toe, maar voer alleen de verzamelingsfacetquery uit (gefilterd op het veld roottaxonomie) wanneer de gebruiker een knooppunt op het hoogste niveau uitbreidt. Of, als 100% terugroeping niet nodig is, gewoon het aantal facet te verminderen tot een redelijk aantal, en ervoor te zorgen dat de facet-items worden gesorteerd op telling.

## <a name="see-also"></a>Zie ook

[Voorbeeld: de AdventureWorks Inventory-database modelleren voor Azure Cognitive Search](search-example-adventureworks-modeling.md)