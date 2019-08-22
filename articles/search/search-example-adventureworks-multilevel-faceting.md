---
title: 'Voorbeeld: Facetten met meerdere niveaus-Azure Search'
description: Meer informatie over het bouwen van facet structuren voor taxonomieën op meerdere niveaus, het maken van een geneste navigatie structuur die u op toepassings pagina's kunt invoegen.
author: cstone
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: chstone
ms.openlocfilehash: 5a6fda0157f0f3a4ca5861acd4bcbead7839e451
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649937"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Voorbeeld: Facetten met meerdere niveaus in Azure Search

Azure Search-schema's bieden geen expliciete ondersteuning voor taxonomie categorieën met meerdere niveaus, maar u kunt ze benaderen door inhoud te bewerken voordat u de indexeert en vervolgens een speciale verwerking toe te passen op de resultaten. 

## <a name="start-with-the-data"></a>Beginnen met de gegevens

Het voor beeld in dit artikel is gebaseerd op een voor gaande voor beeld, [de AdventureWorks-inventarisatie database model leren](search-example-adventureworks-modeling.md)om te demonstreren op meerdere niveaus in azure Search.

AdventureWorks heeft een eenvoudige taxonomie op twee niveaus met een bovenliggende/onderliggende relatie. Voor taxonomie diepten met een vaste lengte van deze structuur kunt u een eenvoudige SQL-samenvoegings query gebruiken om de taxonomie te groeperen:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Query resultaten](./media/search-example-adventureworks/prod-query-results.png "Query resultaten")

## <a name="indexing-to-a-collection-field"></a>Indexeren naar een verzamelings veld

Maak in de index die deze structuur bevat, een **verzameling (EDM. String)** in het Azure Search schema om deze gegevens op te slaan. Zorg er ook voor dat veld kenmerken kunnen worden doorzocht, gefilterd, facetbaar en ophalen.

Wanneer u inhoud indexeert die verwijst naar een specifieke taxonomie categorie, verzendt u de taxonomie als een matrix met tekst van elk niveau van de taxonomie. Indien bijvoorbeeld voor een entiteit met `ProductCategoryId = 5 (Mountain Bikes)`, het veld verzenden als`[ "Bikes", "Bikes|Mountain Bikes"]`

U ziet dat de bovenliggende categorie ' Bikes ' in de waarde ' Mountain Bikes ' van de onderliggende categorie wordt opgenomen. Elke subcategorie moet het volledige pad insluiten ten opzichte van het hoofd element. Het sluis teken is wille keurig, maar het moet consistent zijn en mag niet in de bron tekst worden weer gegeven. Het scheidings teken wordt gebruikt in de toepassings code om de taxonomie structuur opnieuw te maken op basis van de facet resultaten.

## <a name="construct-the-query"></a>De query maken

Bij het uitvoeren van query's, neemt u de volgende facet specificatie op (waarbij taxonomie het facetel taxonomie veld is):`facet = taxonomy,count:50,sort:value`

De Count-waarde moet hoog genoeg zijn om alle mogelijke taxonomie waarden te retour neren. De AdventureWorks-gegevens bevatten 41 afzonderlijke taxonomie waarden, `count:50` dus voldoende.

  ![Facet filter](./media/search-example-adventureworks/facet-filter.png "Facet filter")

## <a name="build-the-structure-in-client-code"></a>De structuur in de client code bouwen

Maak in uw client toepassings code de taxonomie structuur opnieuw door elke facet waarde op het sluis teken te splitsen.

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

Het object **Categorieën** kan nu worden gebruikt om een samenvouw bare taxonomie structuur met nauw keurig aantal te genereren:

  ![beperkt filter met meerdere niveaus](./media/search-example-adventureworks/multi-level-facet.png "beperkt filter met meerdere niveaus")

 
Voor elke koppeling in de structuur moet het gerelateerde filter worden toegepast. Bijvoorbeeld:

+ **taxonomie/alle** `(x:x eq 'Accessories')` documenten in de vertakking accessoires worden geretourneerd
+ **taxonomie/wille keurig** `(x:x eq 'Accessories|Bike Racks')` retourneert alleen de documenten met een subcategorie fiets racks onder de vertakking accessoires.

Deze techniek wordt geschaald voor complexere scenario's, zoals diepere taxonomie structuren en dubbele subcategorieën die optreden onder verschillende bovenliggende categorieën (bijvoorbeeld `Bike Components|Forks` en `Camping Equipment|Forks`).

> [!TIP]
> De query snelheid wordt beïnvloed door het aantal geretourneerde facetten. Voor het ondersteunen van zeer grote taxonomie sets kunt u een facetable **EDM toevoegen. teken reeks** veld waarin de taxonomie op het hoogste niveau voor elk document wordt opgeslagen. Pas vervolgens dezelfde bovenstaande techniek toe, maar Voer alleen de verzameling-facet query (gefilterd op het basis veld taxonomie) uit wanneer de gebruiker een knoop punt op het hoogste niveau uitbreidt. Of als 100% intrekken niet vereist is, verminder dan gewoon het aantal facetten naar een redelijk getal en zorg ervoor dat de facet vermeldingen worden gesorteerd op aantal.

## <a name="see-also"></a>Zie ook

[Voorbeeld: De AdventureWorks Inventory data base model leren voor Azure Search](search-example-adventureworks-modeling.md)