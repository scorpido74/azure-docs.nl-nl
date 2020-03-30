---
title: FROM-clausule in Azure Cosmos DB
description: Meer informatie over de SQL-syntaxis en bijvoorbeeld voor FROM-component voor Azure Cosmos DB. In dit artikel worden ook voorbeelden weergegeven voor de bereikresultaten en subitems ophalen met behulp van de FROM-component.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587682"
---
# <a name="from-clause-in-azure-cosmos-db"></a>FROM-clausule in Azure Cosmos DB

De clausule`FROM <from_specification>`VAN ( ) is optioneel, tenzij de bron wordt gefilterd of later in de query wordt geprojecteerd. Een query `SELECT * FROM Families` zoals opsomt `Families` over de hele container. U ook de speciale id ROOT voor de container gebruiken in plaats van de containernaam te gebruiken.

De FROM-clausule handhaaft de volgende regels per query:

* Voor de container kunt u een alias gebruiken, zoals `SELECT f.id FROM Families AS f` of gewoon `SELECT f.id FROM Families f`. Hier `f` is de `Families`alias voor . AS is een optioneel zoekwoord om de id te [aliasen.](sql-query-aliasing.md)  

* Zodra de oorspronkelijke bronnaam is gealiasd, kan deze niet meer worden gekoppeld. Is bijvoorbeeld `SELECT Families.id FROM Families f` syntactisch ongeldig omdat `Families` de id is aliased en niet meer kan worden opgelost.  

* Alle naar verwezen eigenschappen moeten volledig gekwalificeerd zijn, om dubbelzinnige bindingen te voorkomen bij afwezigheid van strikte schema-naleving. Is bijvoorbeeld `SELECT id FROM Families f` syntactisch ongeldig omdat `id` de eigenschap niet gebonden is.

## <a name="syntax"></a>Syntaxis
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argumenten
  
- `<from_source>`  
  
  Hiermee geeft u een gegevensbron op, met of zonder alias. Als alias niet is opgegeven, wordt `<container_expression>` deze afgeleid van de volgende regels:  
  
  -  Als de expressie een container_name is, wordt container_name als alias gebruikt.  
  
  -  Als de `<container_expression>`expressie is, dan property_name, dan zal property_name worden gebruikt als een alias. Als de expressie een container_name is, wordt container_name als alias gebruikt.  
  
- Als`input_alias`  
  
  Hiermee geeft `input_alias` u op dat het een set waarden is die wordt geretourneerd door de onderliggende containerexpressie.  
 
- `input_alias`Inch  
  
  Hiermee geeft `input_alias` u op dat de set waarden moet worden weergegeven die zijn verkregen door alle arrayelementen van elke array te herhalen die door de onderliggende containerexpressie worden geretourneerd. Elke waarde die wordt geretourneerd door onderliggende containerexpressie die geen array is, wordt genegeerd.  
  
- `<container_expression>`  
  
  Hiermee geeft u de containerexpressie op die moet worden gebruikt om de documenten op te halen.  
  
- `ROOT`  
  
  Hiermee geeft u op dat document moet worden opgehaald uit de standaard, momenteel verbonden container.  
  
- `container_name`  
  
  Hiermee geeft u op dat document uit de meegeleverde container moet worden opgehaald. De naam van de container moet overeenkomen met de naam van de container die momenteel is verbonden.  
  
- `input_alias`  
  
  Hiermee geeft u op dat document moet worden opgehaald uit de andere bron die wordt gedefinieerd door de meegeleverde alias.  
  
- `<container_expression> '.' property_name`  
  
  Hiermee geeft u op dat document `property_name` moet worden opgehaald door toegang te krijgen tot de eigenschap.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Hiermee geeft u op dat document `property_name` moet worden opgehaald door toegang te krijgen tot de eigenschap of array_index arrayelement voor alle documenten die zijn opgehaald met een opgegeven containerexpressie.  
  
## <a name="remarks"></a>Opmerkingen
  
Alle aliassen die in `<from_source>(`de s worden verstrekt of afgeleid, moeten uniek zijn. De `<container_expression>.`syntaxisproperty_name is `<container_expression>' ['"property_name"']'`hetzelfde als . De laatste syntaxis kan echter worden gebruikt als een eigenschapsnaam een niet-id-teken bevat.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Ontbrekende eigenschappen, ontbrekende matrixelementen en niet-gedefinieerde waarden verwerken
  
Als een containerexpressie toegang heeft tot eigenschappen of matrixelementen en die waarde niet bestaat, wordt die waarde genegeerd en niet verder verwerkt.  
  
### <a name="container-expression-context-scoping"></a>Contextscoping containerexpressie  
  
Een containerexpressie kan containerruimte of documentscoped zijn:  
  
-   Een expressie is containerbereik, als de onderliggende bron van `container_name`de containerexpressie ROOT of . Een dergelijke expressie vertegenwoordigt een set documenten die rechtstreeks uit de container zijn opgehaald en is niet afhankelijk van de verwerking van andere containerexpressies.  
  
-   Een expressie is documentbereik als de onderliggende bron `input_alias` van de containerexpressie eerder in de query wordt geïntroduceerd. Een dergelijke expressie vertegenwoordigt een reeks documenten die zijn verkregen door de containerexpressie te evalueren in het toepassingsgebied van elk document dat behoort tot de verzameling die is gekoppeld aan de aliascontainer.  De resulterende set is een unie van sets die zijn verkregen door de containerexpressie voor elk van de documenten in de onderliggende set te evalueren. 

## <a name="examples"></a>Voorbeelden

### <a name="get-subitems-by-using-the-from-clause"></a>Subitems ophalen met behulp van de FROM-component

De FROM-component kan de bron reduceren tot een kleinere subset. Als u in elk item alleen een substructuur wilt opsommen, kan de subroot de bron worden, zoals in het volgende voorbeeld wordt weergegeven:

```sql
    SELECT *
    FROM Families.children
```

U ziet deze uitvoer:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

De vorige query gebruikte een array als bron, maar u een object ook als bron gebruiken. De query houdt rekening met een geldige, gedefinieerde JSON-waarde in de bron voor opname in het resultaat. In het volgende `Families` voorbeeld wordt uitgesloten `address.state` dat deze geen waarde hebben.

```sql
    SELECT *
    FROM Families.address.state
```

U ziet deze uitvoer:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Volgende stappen

- [Slag](sql-query-getting-started.md)
- [SELECT-component](sql-query-select.md)
- [WHERE-clausule](sql-query-where.md)
