---
title: De component FROM in Azure Cosmos DB
description: Meer informatie over de SQL-syntaxis en voor beeld voor de component FROM voor Azure Cosmos DB. In dit artikel ziet u ook voor beelden van bereik resultaten en het ophalen van subitems met behulp van de component FROM.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587682"
---
# <a name="from-clause-in-azure-cosmos-db"></a>De component FROM in Azure Cosmos DB

De component FROM (`FROM <from_specification>`) is optioneel, tenzij de bron wordt gefilterd of later in de query wordt geprojecteerd. Een query zoals `SELECT * FROM Families` op de hele `Families` container wordt opgesomd. U kunt ook de speciale id-HOOFDMAP voor de container gebruiken in plaats van de container naam te gebruiken.

De component FROM dwingt de volgende regels per query af:

* Voor de container kunt u een alias gebruiken, zoals `SELECT f.id FROM Families AS f` of gewoon `SELECT f.id FROM Families f`. Hier `f` de alias voor `Families`. Net als een optioneel sleutel woord voor het [aliassen](sql-query-aliasing.md) van de id.  

* Als de alias is gewijzigd, kan de oorspronkelijke bron naam niet worden gebonden. `SELECT Families.id FROM Families f` is bijvoorbeeld syntactisch ongeldig omdat de id `Families` een alias heeft en niet meer kan worden opgelost.  

* Alle eigenschappen waarnaar wordt verwezen, moeten volledig gekwalificeerd zijn om ondubbelzinnige bindingen te voor komen zonder strikte schema's. `SELECT id FROM Families f` is bijvoorbeeld syntactisch ongeldig omdat de eigenschap `id` niet is gekoppeld.

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
  
  Hiermee geeft u een gegevensbron, met of zonder een alias. Als er geen alias is opgegeven, wordt deze afgeleid van de `<container_expression>` met de volgende regels:  
  
  -  De expressie is een container_name, zal container_name worden gebruikt als een alias.  
  
  -  Als de expressie is `<container_expression>`, dan property_name, wordt property_name als alias gebruikt. De expressie is een container_name, zal container_name worden gebruikt als een alias.  
  
- Als `input_alias`  
  
  Geeft aan dat de `input_alias` een set waarden is die door de onderliggende container expressie wordt geretourneerd.  
 
- `input_alias` IN  
  
  Hiermee geeft u op dat de `input_alias` de set waarden moet vertegenwoordigen die worden verkregen door te herhalen over alle matrix elementen van elke matrix die wordt geretourneerd door de onderliggende container expressie. Een waarde die is geretourneerd door onderliggende container-expressie die is geen matrix wordt genegeerd.  
  
- `<container_expression>`  
  
  Hiermee geeft u de container-expressie moet worden gebruikt om de documenten te halen.  
  
- `ROOT`  
  
  Hiermee geeft u op dat document moet worden opgehaald van de standaardwaarde, momenteel verbonden container.  
  
- `container_name`  
  
  Hiermee geeft u op dat document moet worden opgehaald uit de opgegeven container. De naam van de container moet overeenkomen met de naam van de container momenteel verbonden.  
  
- `input_alias`  
  
  Hiermee geeft u op dat document moet worden opgehaald uit de andere bron die wordt gedefinieerd door de opgegeven alias.  
  
- `<container_expression> '.' property_name`  
  
  Hiermee wordt aangegeven dat het document moet worden opgehaald door toegang te krijgen tot de eigenschap `property_name`.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Hiermee wordt aangegeven dat het document moet worden opgehaald door de `property_name` eigenschap of het element array_index matrix te openen voor alle documenten die zijn opgehaald met de opgegeven container expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
Alle aliassen die in de `<from_source>(`s zijn opgenomen of uitgesteld, moeten uniek zijn. De syntaxis `<container_expression>.`property_name is hetzelfde als `<container_expression>' ['"property_name"']'`. De syntaxis van de laatste kan echter worden gebruikt als een eigenschapsnaam een niet-id-teken bevat.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>afhandeling van ontbrekende eigenschappen, ontbrekende matrixelementen en niet-gedefinieerde waarden
  
Als een expressie voor een container toegang heeft tot de eigenschappen of matrixelementen en waarde niet bestaat, wordt die waarde genegeerd en niet verder wordt verwerkt.  
  
### <a name="container-expression-context-scoping"></a>Container expressie context scoping  
  
Een expressie voor een container zijn binnen het bereik van container of binnen het bereik van document:  
  
-   Een expressie is container-scoped als de onderliggende bron van de container expressie ROOT of `container_name`is. Deze expressie vertegenwoordigt een set documenten die zijn opgehaald uit de container rechtstreeks en is niet afhankelijk van de verwerking van andere expressies container.  
  
-   Een expressie is een document bereik, als de onderliggende bron van de container expressie `input_alias` eerder in de query is geïntroduceerd. Deze expressie vertegenwoordigt een set documenten die zijn verkregen door het evalueren van de container-expressie in het bereik van elk document die behoren tot de verzameling die zijn gekoppeld aan de alias-container.  De resulterende set is een samenvoeging van die worden verkregen door het evalueren van de container-expressie voor elk van de documenten in de onderliggende verzameling. 

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

De voor gaande query heeft een matrix als bron gebruikt, maar u kunt ook een object als bron gebruiken. De query beschouwt een geldige, gedefinieerde JSON-waarde in de bron voor opname in het resultaat. In het volgende voor beeld worden `Families` uitgesloten die geen `address.state`-waarde hebben.

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
