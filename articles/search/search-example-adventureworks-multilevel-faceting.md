---
title: 'Voor beeld: facetten met meerdere niveaus'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het bouwen van facet structuren voor taxonomieën op meerdere niveaus, het maken van een geneste navigatie structuur die u op toepassings pagina's kunt invoegen.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792947"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Voor beeld: facetten met meerdere niveaus in azure Cognitive Search

Azure Cognitive Search-schema's bieden geen expliciete ondersteuning voor taxonomie categorieën met meerdere niveaus, maar u kunt deze wel benaderen door inhoud te bewerken voordat u de indexeert en vervolgens een speciale verwerking toe te passen op de resultaten. 

## <a name="start-with-the-data"></a>Beginnen met de gegevens

Het voor beeld in dit artikel is gebaseerd op een voor gaande voor beeld, [de AdventureWorks-inventarisatie database model leren](search-example-adventureworks-modeling.md)om te demonstreren op meerdere niveaus in azure Cognitive Search.

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

  ![Query resultaten](./media/search-example-adventureworks/prod-query-results.png "Queryresultaten")

## <a name="indexing-to-a-collection-field"></a>Indexeren naar een verzamelings veld

Maak in de index die deze structuur bevat, een **verzameling (EDM. String)** in het Azure Cognitive Search-schema voor het opslaan van deze gegevens en zorg ervoor dat veld kenmerken doorzoekbaar, filterbaar, facetable en ophalen kunnen bevatten.

Wanneer u inhoud indexeert die verwijst naar een specifieke taxonomie categorie, verzendt u de taxonomie als een matrix met tekst van elk niveau van de taxonomie. Indien een entiteit bijvoorbeeld `ProductCategoryId = 5 (Mountain Bikes)`, dient u het veld in te dienen als `[ "Bikes", "Bikes|Mountain Bikes"]`

U ziet dat de bovenliggende categorie ' Bikes ' in de waarde ' Mountain Bikes ' van de onderliggende categorie wordt opgenomen. Elke subcategorie moet het volledige pad insluiten ten opzichte van het hoofd element. Het sluis teken is wille keurig, maar het moet consistent zijn en mag niet in de bron tekst worden weer gegeven. Het scheidings teken wordt gebruikt in de toepassings code om de taxonomie structuur opnieuw te maken op basis van de facet resultaten.

## <a name="construct-the-query"></a>De query maken

Bij het uitvoeren van query's, neemt u de volgende facet specificatie op (waarbij taxonomie het facetel taxonomie veld is): `facet = taxonomy,count:50,sort:value`

De Count-waarde moet hoog genoeg zijn om alle mogelijke taxonomie waarden te retour neren. De AdventureWorks-gegevens bevatten 41 afzonderlijke taxonomie waarden, dus `count:50` is voldoende.

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

+ **taxonomie/alle** `(x:x eq 'Accessories')` retourneert alle documenten in de vertakking accessoires
+ **taxonomie/elke** `(x:x eq 'Accessories|Bike Racks')` retourneert alleen de documenten met een subcategorie fiets racks onder de vertakking accessoires.

Deze techniek wordt geschaald voor complexere scenario's, zoals diepere taxonomie structuren en dubbele subcategorieën die optreden onder verschillende bovenliggende categorieën (bijvoorbeeld `Bike Components|Forks` en `Camping Equipment|Forks`).

> [!TIP]
> De query snelheid wordt beïnvloed door het aantal geretourneerde facetten. Voor het ondersteunen van zeer grote taxonomie sets kunt u een facetable **EDM toevoegen. teken reeks** veld waarin de taxonomie op het hoogste niveau voor elk document wordt opgeslagen. Pas vervolgens dezelfde bovenstaande techniek toe, maar Voer alleen de verzameling-facet query (gefilterd op het basis veld taxonomie) uit wanneer de gebruiker een knoop punt op het hoogste niveau uitbreidt. Of als 100% intrekken niet vereist is, verminder dan gewoon het aantal facetten naar een redelijk getal en zorg ervoor dat de facet vermeldingen worden gesorteerd op aantal.

## <a name="see-also"></a>Zie ook

[Voor beeld: de AdventureWorks-inventarisatie database voor Azure Cognitive Search model leren](search-example-adventureworks-modeling.md)