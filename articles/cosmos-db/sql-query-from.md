---
title: De component FROM in Azure Cosmos DB
description: Meer informatie over de SQL-syntaxis en voor beeld voor de component FROM voor Azure Cosmos DB. In dit artikel ziet u ook voor beelden van bereik resultaten en het ophalen van subitems met behulp van de component FROM.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77587682"
---
# <a name="from-clause-in-azure-cosmos-db"></a>De component FROM in Azure Cosmos DB

De component FROM`FROM <from_specification>`() is optioneel, tenzij de bron wordt gefilterd of later in de query wordt geprojecteerd. Een query zoals `SELECT * FROM Families` een inventarisatie van de `Families` gehele container. U kunt ook de speciale id-HOOFDMAP voor de container gebruiken in plaats van de container naam te gebruiken.

De component FROM dwingt de volgende regels per query af:

* Voor de container kunt u een alias gebruiken, zoals `SELECT f.id FROM Families AS f` of gewoon `SELECT f.id FROM Families f`. Dit `f` is de alias voor `Families`. Net als een optioneel sleutel woord voor het [aliassen](sql-query-aliasing.md) van de id.  

* Als de alias is gewijzigd, kan de oorspronkelijke bron naam niet worden gebonden. De syntaxis `SELECT Families.id FROM Families f` is bijvoorbeeld ongeldig omdat de id `Families` van de alias is en kan niet meer worden omgezet.  

* Alle eigenschappen waarnaar wordt verwezen, moeten volledig gekwalificeerd zijn om ondubbelzinnige bindingen te voor komen zonder strikte schema's. De syntaxis `SELECT id FROM Families f` is bijvoorbeeld ongeldig omdat de eigenschap `id` niet is gekoppeld.

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
  
  Hiermee geeft u een gegevens bron, met of zonder een alias. Als er geen alias is opgegeven, wordt deze afgeleid van de `<container_expression>` volgende regels:  
  
  -  Als de expressie een container_name is, wordt container_name als alias gebruikt.  
  
  -  Als de expressie is `<container_expression>`, dan property_name, wordt property_name als alias gebruikt. Als de expressie een container_name is, wordt container_name als alias gebruikt.  
  
- ALSOOK`input_alias`  
  
  Hiermee geeft u `input_alias` op dat de is een set waarden die wordt geretourneerd door de onderliggende container expressie.  
 
- `input_alias`Naast  
  
  Specificeert dat `input_alias` de moet de set waarden vertegenwoordigen die worden verkregen door te herhalen over alle matrix elementen van elke matrix die wordt geretourneerd door de onderliggende container expressie. Elke waarde die wordt geretourneerd door de onderliggende container expressie die geen matrix is, wordt genegeerd.  
  
- `<container_expression>`  
  
  Hiermee geeft u de container expressie op die moet worden gebruikt om de documenten op te halen.  
  
- `ROOT`  
  
  Hiermee wordt aangegeven dat het document moet worden opgehaald uit de standaard, momenteel verbonden container.  
  
- `container_name`  
  
  Hiermee geeft u op dat het document moet worden opgehaald uit de opgegeven container. De naam van de container moet overeenkomen met de naam van de container die momenteel is verbonden met.  
  
- `input_alias`  
  
  Hiermee geeft u op dat het document moet worden opgehaald uit de andere bron die door de opgegeven alias is gedefinieerd.  
  
- `<container_expression> '.' property_name`  
  
  Hiermee wordt aangegeven dat het document moet worden opgehaald door `property_name` toegang te krijgen tot de eigenschap.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Hiermee wordt aangegeven dat het document moet worden opgehaald door `property_name` toegang te krijgen tot het element van de eigenschap of het array_index matrix voor alle documenten die worden opgehaald door de opgegeven container expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
Alle aliassen die in de `<from_source>(`s zijn opgenomen of uitgesteld, moeten uniek zijn. De syntaxis `<container_expression>.`property_name is hetzelfde als `<container_expression>' ['"property_name"']'`. De laatste syntaxis kan echter worden gebruikt als de naam van een eigenschap een niet-id-teken bevat.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Ontbrekende eigenschappen, ontbrekende matrix elementen en niet-gedefinieerde waarden verwerken
  
Als een container expressie toegang heeft tot eigenschappen of matrix elementen en die waarde niet bestaat, wordt die waarde genegeerd en wordt deze niet verder verwerkt.  
  
### <a name="container-expression-context-scoping"></a>Context bereik van container expressie  
  
Een container expressie kan container-scoped of document-Scoped zijn:  
  
-   Een expressie is container-scoped als de onderliggende bron van de container expressie ROOT of `container_name`ofwel is. Een dergelijke expressie vertegenwoordigt een set documenten die rechtstreeks uit de container is opgehaald en is niet afhankelijk van de verwerking van andere container expressies.  
  
-   Een expressie is een document bereik, als de onderliggende bron van de container expressie eerder in `input_alias` de query is geïntroduceerd. Een dergelijke expressie vertegenwoordigt een reeks documenten die zijn verkregen door de container expressie te evalueren in het bereik van elk document dat deel uitmaakt van de set die is gekoppeld aan de container met alias.  De resulterende set is een samen voeging van sets die zijn verkregen door de container expressie te evalueren voor elk van de documenten in de onderliggende set. 

## <a name="examples"></a>Voorbeelden

### <a name="get-subitems-by-using-the-from-clause"></a>Subitems ophalen met behulp van de component FROM

De component FROM kan de bron naar een kleinere subset beperken. Als u alleen een substructuur in elk item wilt inventariseren, kan de subhoofdmap de bron worden, zoals wordt weer gegeven in het volgende voor beeld:

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

De voor gaande query heeft een matrix als bron gebruikt, maar u kunt ook een object als bron gebruiken. De query beschouwt een geldige, gedefinieerde JSON-waarde in de bron voor opname in het resultaat. Het volgende voor beeld zou `Families` uitsluiten dat geen `address.state` waarde heeft.

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

- [Aan de slag](sql-query-getting-started.md)
- [SELECT-component](sql-query-select.md)
- [WHERE-component](sql-query-where.md)
