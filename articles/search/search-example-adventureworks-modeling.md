---
title: 'Voorbeeld: De AdventureWorks Inventory-database modelleren'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het modelleren van relationele gegevens en het omzetten ervan in een afgevlakte gegevensset, voor indexering en zoeken in volledige tekst in Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792995"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Voorbeeld: de AdventureWorks Inventory-database modelleren voor Azure Cognitive Search

Azure Cognitive Search accepteert een afgevlakte rijset als ingangen voor de [indexeringspijplijn (data-opname).](search-what-is-an-index.md) Als uw brongegevens afkomstig zijn van een SQL Server relationele database, toont dit artikel één benadering voor het maken van een afgevlakte rijset voorafgaand aan het indexeren, waarbij de AdventureWorks-voorbeelddatabase als voorbeeld wordt gebruikt.

## <a name="about-adventureworks"></a>Over AdventureWorks

Als u een SQL Server-exemplaar hebt, bent u mogelijk bekend met de [AdventureWorks-voorbeelddatabase.](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017) Onder de tabellen in deze database zijn vijf tabellen die productinformatie blootleggen.

+ **ProductModel**: naam
+ **Product**: naam, kleur, kosten, grootte, gewicht, afbeelding, categorie (elke rij wordt lid van een specifiek productmodel)
+ **ProductBeschrijving**: beschrijving
+ **ProductModelProductDescription**: landine (elke rij voegt een ProductModel toe aan een specifieke productbeschrijving voor een specifieke taal)
+ **Productcategorie**: naam, bovenliggende categorie

Het combineren van al deze gegevens in een afgevlakte rijset die kan worden ingenomen in een zoekindex is het doel van dit voorbeeld. 

## <a name="considering-our-options"></a>Gezien onze opties

De naïeve benadering zou zijn om alle rijen uit de producttabel te indexeren (waar nodig samengevoegd), omdat de producttabel de meest specifieke informatie heeft. Deze benadering zou de zoekindex echter blootstellen aan waargenomen duplicaten in een resultset. Het Road-650-model is bijvoorbeeld verkrijgbaar in twee kleuren en zes maten. Een query voor "racefietsen" zou dan worden gedomineerd door twaalf exemplaren van hetzelfde model, alleen gedifferentieerd naar grootte en kleur. De andere zes wegspecifieke modellen zouden allemaal worden gedegradeerd tot de onderwereld van de zoekwereld: pagina twee.

  ![Productenlijst](./media/search-example-adventureworks/products-list.png "Productenlijst")
 
Merk op dat de Road-650 model heeft twaalf opties. Een-op-veel entiteitsrijen worden het best weergegeven als velden met meerdere waarden of vooraf samengevoegde waardevelden in de zoekindex.

Het oplossen van dit probleem is niet zo eenvoudig als het verplaatsen van de doelindex naar de tabel ProductModel. Als u dit doet, worden de belangrijke gegevens in de producttabel genegeerd die nog steeds in zoekresultaten moeten worden weergegeven.

## <a name="use-a-collection-data-type"></a>Een gegevenstype Verzameling gebruiken

De "juiste benadering" is het gebruik van een zoekschemafunctie die geen directe parallel heeft in het databasemodel: **Collection(Edm.String)**. Deze constructie wordt gedefinieerd in het Azure Cognitive Search-indexschema. Een gegevenstype Verzameling wordt gebruikt wanneer u een lijst met afzonderlijke tekenreeksen moet weergeven in plaats van een zeer lange (enkele) tekenreeks. Als u tags of trefwoorden hebt, gebruikt u een gegevenstype Verzameling voor dit veld.

Door indexvelden met meerdere waarden van **Verzameling(Edm.String)** te definiëren voor 'kleur', 'grootte' en 'afbeelding' wordt de aanvullende informatie bewaard voor faceting en filtering zonder de index te vervuilen met dubbele vermeldingen. Pas ook geaggregeerde functies toe op de numerieke productvelden en indexeert **minListPrice** in plaats van elke **productcatalogusPrijs**.

Gezien een index met deze structuren, zou een zoektocht naar "mountainbikes" tonen discrete fiets modellen, met behoud van belangrijke metadata zoals kleur, grootte, en laagste prijs. De volgende screenshot geeft een illustratie.

  ![Voorbeeld van het zoeken van de mountainbike](./media/search-example-adventureworks/mountain-bikes-visual.png "Voorbeeld van het zoeken van de mountainbike")

## <a name="use-script-for-data-manipulation"></a>Script gebruiken voor gegevensmanipulatie

Helaas kan dit type modellering niet gemakkelijk worden bereikt door sql-instructies alleen. Gebruik in plaats daarvan een eenvoudig NodeJS-script om de gegevens te laden en vervolgens in kaart te brengen in zoekvriendelijke JSON-entiteiten.

De uiteindelijke database-search mapping ziet er als volgt uit:

+ model (Edm.String: doorzoekbaar, filterbaar, opvraagbaar) van "ProductModel.Name"
+ description_en (Edm.String: doorzoekbaar) van "ProductDescription" voor het model waar culture='en'
+ kleur (Collectie(Edm.String): doorzoekbaar, filterbaar, facetable, opvraagbaar): unieke waarden uit 'Product.Color' voor het model
+ grootte (Collectie(Edm.String): doorzoekbaar, filterbaar, facetable, opvraagbaar): unieke waarden uit 'Product.Size' voor het model
+ afbeelding (Collection(Edm.String): opvraagbaar): unieke waarden uit 'Product.ThumbnailPhoto' voor het model
+ minStandardCost (Edm.Double: filterbaar, facetable, sorteerbaar, opvraagbaar): totaalminimum van alle "Product.StandardCost" voor het model
+ minListPrice (Edm.Double: filterbaar, facetable, sorteerbaar, opvraagbaar): totaalminimum van alle "Product.ListPrice" voor het model
+ minWeight (Edm.Double: filterbaar, facetable, sorteerbaar, opvraagbaar): totaalminimum van alle "Product.Weight" voor het model
+ producten (Collection(Edm.String): doorzoekbaar, filterbaar, opvraagbaar): unieke waarden uit 'Product.Name' voor het model

Nadat u lid bent gegaan van de productmodeltabel met product en productbeschrijving, gebruikt u [lodash](https://lodash.com/) (of Linq in C#) om de resultaatset snel te transformeren:

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

De resulterende JSON ziet er als volgt uit:

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Tot slot is hier de SQL-query om de eerste recordset terug te sturen. Ik gebruikte de [mssql](https://www.npmjs.com/package/mssql) npm-module om de gegevens in mijn NodeJS-app te laden.

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Voorbeeld: Facettaxonomies op meerdere niveaus in Azure Cognitive Search](search-example-adventureworks-multilevel-faceting.md)