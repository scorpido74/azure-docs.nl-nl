---
title: De component FROM in Azure Cosmos DB
description: Meer informatie over de component SQL FROM voor Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 79bb17277a041f71c095ed724737012f9501f16f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326996"
---
# <a name="from-clause-in-azure-cosmos-db"></a>De component FROM in Azure Cosmos DB

De component FROM (`FROM <from_specification>`) is optioneel, tenzij de bron wordt gefilterd of later in de query wordt geprojecteerd. Een query zoals `SELECT * FROM Families` inventariseert de hele `Families`-container. U kunt ook de speciale id-HOOFDMAP voor de container gebruiken in plaats van de container naam te gebruiken.

De component FROM dwingt de volgende regels per query af:

* Voor de container kunt u een alias gebruiken, zoals `SELECT f.id FROM Families AS f` of gewoon `SELECT f.id FROM Families f`. Hier `f` is de alias voor `Families`. Net als een optioneel sleutel woord voor het [aliassen](sql-query-aliasing.md) van de id.  

* Als de alias is gewijzigd, kan de oorspronkelijke bron naam niet worden gebonden. @No__t-0 is bijvoorbeeld syntactisch ongeldig omdat de id `Families` is gealiasd en niet meer kan worden opgelost.  

* Alle eigenschappen waarnaar wordt verwezen, moeten volledig gekwalificeerd zijn om ondubbelzinnige bindingen te voor komen zonder strikte schema's. @No__t-0 is bijvoorbeeld syntactisch ongeldig omdat de eigenschap `id` niet is gekoppeld.

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
  
  Hiermee geeft u een gegevens bron, met of zonder een alias. Als er geen alias is opgegeven, wordt deze afgeleid van de `<container_expression>`, met behulp van de volgende regels:  
  
  -  Als de expressie een container_name is, wordt container_name gebruikt als een alias.  
  
  -  Als de expressie `<container_expression>` en vervolgens property_name, wordt property_name gebruikt als alias. Als de expressie een container_name is, wordt container_name gebruikt als een alias.  
  
- Als `input_alias`  
  
  Hiermee geeft u op dat de `input_alias` een set waarden is die wordt geretourneerd door de onderliggende container expressie.  
 
- `input_alias` IN  
  
  Hiermee geeft u op dat de `input_alias` moet de set waarden vertegenwoordigen die worden verkregen door te herhalen over alle matrix elementen van elke matrix die wordt geretourneerd door de onderliggende container expressie. Elke waarde die wordt geretourneerd door de onderliggende container expressie die geen matrix is, wordt genegeerd.  
  
- `<container_expression>`  
  
  Hiermee geeft u de container expressie op die moet worden gebruikt om de documenten op te halen.  
  
- `ROOT`  
  
  Hiermee wordt aangegeven dat het document moet worden opgehaald uit de standaard, momenteel verbonden container.  
  
- `container_name`  
  
  Hiermee geeft u op dat het document moet worden opgehaald uit de opgegeven container. De naam van de container moet overeenkomen met de naam van de container die momenteel is verbonden met.  
  
- `input_alias`  
  
  Hiermee geeft u op dat het document moet worden opgehaald uit de andere bron die door de opgegeven alias is gedefinieerd.  
  
- `<container_expression> '.' property_`  
  
  Hiermee wordt aangegeven dat het document moet worden opgehaald door toegang te krijgen tot de eigenschap `property_name` of het matrix element array_index voor alle documenten die worden opgehaald met de opgegeven container expressie.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Hiermee wordt aangegeven dat het document moet worden opgehaald door toegang te krijgen tot de eigenschap `property_name` of het matrix element array_index voor alle documenten die worden opgehaald met de opgegeven container expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
Alle aliassen die in de `<from_source>(`s zijn opgenomen of uitgesteld, moeten uniek zijn. De syntaxis `<container_expression>.`property_name is hetzelfde als `<container_expression>' ['"property_name"']'`. De laatste syntaxis kan echter worden gebruikt als de naam van een eigenschap een niet-id-teken bevat.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Ontbrekende eigenschappen, ontbrekende matrix elementen en niet-gedefinieerde waarden verwerken
  
Als een container expressie toegang heeft tot eigenschappen of matrix elementen en die waarde niet bestaat, wordt die waarde genegeerd en wordt deze niet verder verwerkt.  
  
### <a name="container-expression-context-scoping"></a>Context bereik van container expressie  
  
Een container expressie kan container-scoped of document-Scoped zijn:  
  
-   Een expressie is container-scoped als de onderliggende bron van de container expressie ROOT of `container_name` is. Een dergelijke expressie vertegenwoordigt een set documenten die rechtstreeks uit de container is opgehaald en is niet afhankelijk van de verwerking van andere container expressies.  
  
-   Een expressie is een document bereik, als de onderliggende bron van de container expressie `input_alias` eerder in de query is geïntroduceerd. Een dergelijke expressie vertegenwoordigt een reeks documenten die zijn verkregen door de container expressie te evalueren in het bereik van elk document dat deel uitmaakt van de set die is gekoppeld aan de container met alias.  De resulterende set is een samen voeging van sets die zijn verkregen door de container expressie te evalueren voor elk van de documenten in de onderliggende set. 

## <a name="examples"></a>Voorbeelden

### <a name="get-subitems-by-using-the-from-clause"></a>Subitems ophalen met behulp van de component FROM

De component FROM kan de bron naar een kleinere subset beperken. Als u alleen een substructuur in elk item wilt inventariseren, kan de subhoofdmap de bron worden, zoals wordt weer gegeven in het volgende voor beeld:

```sql
    SELECT *
    FROM Families.children
```

De resultaten zijn:

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

De voor gaande query heeft een matrix als bron gebruikt, maar u kunt ook een object als bron gebruiken. De query beschouwt een geldige, gedefinieerde JSON-waarde in de bron voor opname in het resultaat. In het volgende voor beeld wordt `Families` uitgesloten die geen `address.state`-waarde hebben.

```sql
    SELECT *
    FROM Families.address.state
```

De resultaten zijn:

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