---
title: 'Voor beeld: de AdventureWorks-inventarisatie database model leren'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het model leren van relationele gegevens, het transformeren ervan naar een platte gegevensset, voor het indexeren en zoeken in volledige tekst in azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792995"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Voor beeld: de AdventureWorks-inventarisatie database voor Azure Cognitive Search model leren

Azure Cognitive Search accepteert een samengevoegde rijenset als invoer voor de [pijp lijn voor het indexeren (gegevens opname)](search-what-is-an-index.md). Als uw bron gegevens afkomstig zijn uit een relationele data base van SQL Server, ziet u in dit artikel één benadering voor het maken van een samengevoegde rijenset vóór het indexeren, met behulp van de AdventureWorks-voorbeeld database als voor beeld.

## <a name="about-adventureworks"></a>Over AdventureWorks

Als u een SQL Server-exemplaar hebt, is het mogelijk dat u bekend bent met de [AdventureWorks-voorbeeld database](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). De tabellen die in deze data base zijn opgenomen, zijn vijf tabellen die product informatie beschikbaar maken.

+ **Product model**: naam
+ **Product**: naam, kleur, kosten, grootte, gewicht, afbeelding, categorie (elke rij wordt toegevoegd aan een specifiek product model)
+ **ProductDescription**: beschrijving
+ **ProductModelProductDescription**: de land instelling (elke rij wordt toegevoegd aan een product model aan een specifieke ProductDescription voor een specifieke taal)
+ **ProductCategory**: naam, bovenliggende categorie

Als al deze gegevens worden gecombineerd in een samengevoegde rijenset die in een zoek index kan worden opgenomen, is het doel van dit voor beeld. 

## <a name="considering-our-options"></a>Overweeg onze opties

De Naïve benadering is het indexeren van alle rijen uit de tabel product (indien van toepassing), omdat de tabel product de meest specifieke informatie bevat. Deze benadering zou de zoek index echter beschikbaar maken voor dubbele waarden in een resultatenset. Het model weg-650 is bijvoorbeeld beschikbaar in twee kleuren en zes grootten. Er wordt dan een query voor ' Road Bikes ' in plaats van twaalf instanties van hetzelfde model, die alleen worden onderscheiden door de grootte en de kleur. De andere zes weg-specifieke modellen worden allemaal relegated naar de Nether wereld van Search: pagina twee.

  ![Lijst met producten](./media/search-example-adventureworks/products-list.png "Lijst met producten")
 
U ziet dat het model Road-650 twaalf opties heeft. Een-op-veel-entiteits rijen worden het beste weer gegeven als velden met meerdere waarden of velden met vooraf samengestelde waarden in de zoek index.

Het oplossen van dit probleem is niet zo eenvoudig als het verplaatsen van de doel index naar de tabel product model. Als u dit doet, worden de belang rijke gegevens in de product tabel genegeerd die nog steeds in de zoek resultaten moeten worden weer gegeven.

## <a name="use-a-collection-data-type"></a>Een verzamelings gegevens type gebruiken

De "juiste aanpak" is het gebruik van een zoek schema-functie die geen directe parallelle in het database model: **verzameling (EDM. String)** heeft. Deze construct wordt gedefinieerd in het Azure Cognitive Search-index schema. Een verzamelings gegevens type wordt gebruikt wanneer u een lijst met afzonderlijke teken reeksen moet vertegenwoordigen in plaats van een zeer lange teken reeks (één). Als u Tags of tref woorden hebt, gebruikt u een verzamelings gegevens type voor dit veld.

Door index velden met meerdere waarden te definiëren voor de **verzameling (EDM. String)** voor "kleur", "grootte" en "afbeelding", wordt de aanvullende informatie bewaard voor facetten en filters zonder de index te vervuilen met dubbele vermeldingen. Op dezelfde manier kunt u statistische functies Toep assen op de numerieke product velden, waarbij u **minListPrice** in plaats van elke product- **listPrice**indexeert.

Op basis van een index met deze structuren worden met een zoek opdracht naar ' Mountain Bikes ' afzonderlijke Bicycle-modellen weer gegeven, waarbij belang rijke meta gegevens, zoals kleur, grootte en laagste prijs behouden blijven. De volgende scherm afbeelding bevat een illustratie.

  ![Zoek voorbeeld van Mountain Bike](./media/search-example-adventureworks/mountain-bikes-visual.png "Zoek voorbeeld van Mountain Bike")

## <a name="use-script-for-data-manipulation"></a>Script gebruiken voor het bewerken van gegevens

Helaas kan dit type model niet eenvoudig worden bereikt via SQL-instructies. Gebruik in plaats daarvan een eenvoudig NodeJS-script om de gegevens te laden en vervolgens toe te wijzen aan Zoek vriendelijke JSON-entiteiten.

De uiteindelijke data base-Zoek toewijzing ziet er als volgt uit:

+ model (EDM. String: doorzoekbaar, filterbaar, opgehaald) van ' ProductModel.Name '
+ description_en (EDM. String: Doorzoek bare) van ' ProductDescription ' voor het model waarbij Culture = ' en '
+ Color (verzameling (EDM. String): Doorzoek bare, filterbaar, facetbaar, ophalend): unieke waarden van ' product. Color ' voor het model
+ grootte (verzameling (EDM. String): Doorzoek bare, filterbaar, facetbaar, ophalend): unieke waarden van ' product. size ' voor het model
+ afbeelding (verzameling (EDM. String): kan worden opgehaald): unieke waarden van ' product. ThumbnailPhoto ' voor het model
+ minStandardCost (EDM. Double: filterbaar, facetable, sorteerbaar, ophaalbaar): het minimum aantal van ' product. StandardCost ' voor het model verzamelen
+ minListPrice (EDM. Double: filterbaar, facetable, sorteerbaar, ophaalbaar): het minimum aantal van ' product. ListPrice ' voor het model verzamelen
+ minWeight (EDM. Double: filterbaar, facetable, sorteerbaar, ophaalbaar): cumulatief minimum van alle "product. Weight" voor het model
+ Products (verzameling (EDM. String): Doorzoek bare, filterbaar, ophalend): unieke waarden van ' Product.Name ' voor het model

Nadat u de tabel Modeler hebt toegevoegd aan het product en [](https://lodash.com/) ProductDescription, gebruikt u lodash C#(of LINQ in) om snel de resultatenset te transformeren:

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

Ten slotte ziet u hier de SQL-query om de initiële recordset te retour neren. Ik heb de [MSSQL](https://www.npmjs.com/package/mssql) NPM-module gebruikt om de gegevens in mijn NodeJS-app te laden.

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
> [Voor beeld: facet-taxonomieën op meerdere niveaus in azure Cognitive Search](search-example-adventureworks-multilevel-faceting.md)