---
title: JSON-concepten Azure Data Factory toewijzing van gegevens stromen
description: Data Factory gegevens stroom voor toewijzing heeft ingebouwde mogelijkheden voor het verwerken van JSON-documenten met hiërarchieën
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 69dce46052c18eec7c3f1fa2082907ed62b367d6
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703331"
---
# <a name="mapping-data-flow-json-handling"></a>JSON-verwerking van gegevens stroom toewijzen

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="creating-json-structures-in-expression-editor"></a>JSON-structuren maken in de expressie-editor
### <a name="derived-column-transformation"></a>Afgeleide kolom transformatie
Het is eenvoudiger om een complexe kolom toe te voegen aan uw gegevens stroom via de afgeleide kolom expressie-editor. Nadat u een nieuwe kolom hebt toegevoegd en de editor hebt geopend, zijn er twee opties: Voer de JSON-structuur hand matig in of gebruik de gebruikers interface om interactief subkolomlen toe te voegen.

#### <a name="interactive-ui-json-design"></a>Interactief JSON-ontwerp voor gebruikers interface
In het deel venster uitvoer schema aan de zijkant kunnen nieuwe subkolomen worden toegevoegd met behulp van het menu `+`: ![Subkolom toevoegen]Voeg(media/data-flow/addsubcolumn.png "subkolom toe")

Van daaruit kunnen nieuwe kolommen en subkolomen op dezelfde manier worden toegevoegd. Voor elk niet-complex veld kan een expressie in de expressie-editor aan de rechter kant worden toegevoegd.

Complexe ![kolom](media/data-flow/complexcolumn.png "complex")

#### <a name="manual-json-design"></a>Hand matig JSON-ontwerp
Als u hand matig een JSON-structuur wilt toevoegen, voegt u een nieuwe kolom toe en voert u de expressie in de editor in. De expressie volgt de volgende algemene notatie:
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
Als deze expressie is ingevoerd voor een kolom met de naam ' complexColumn ', wordt deze als de volgende JSON naar de Sink geschreven:
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Voor beeld hand matig script voor de volledige hiërarchische definitie
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>Opties voor bron indeling
### <a name="default"></a>Standaard
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Eén document
* Optie één
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* Optie twee
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>Niet-geciteerde kolom namen
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Bevat opmerkingen
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Enkel aanhalings teken
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Back slash met escape-teken
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Functies met hogere volg orde
## <a name="filter"></a>filter
Hiermee worden elementen van de matrix gefilterd die niet voldoen aan het gegeven predicaat. Het filter verwacht een verwijzing naar één element in de predicaat functie als #item.

### <a name="examples"></a>Voorbeelden
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>map
Wijst elk element van de matrix toe aan een nieuw element met behulp van de gegeven expressie. De toewijzing verwacht een verwijzing naar één element in de expressie functie als #item.

### <a name="examples"></a>Voorbeelden
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>besparen
Verzamelt elementen in een matrix. Reductie verwacht een verwijzing naar een accumulator en één element in de eerste expressie functie als #acc en #item en verwacht de resulterende waarde als #result die in de tweede expressie functie moet worden gebruikt.

### <a name="examples"></a>Voorbeelden
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>Acties
Sorteert de matrix met behulp van de meegeleverde predicaat functie. Bij het sorteren wordt een verwijzing verwacht naar twee opeenvolgende elementen in de expressie-functie als #item1 en #item2.

### <a name="examples"></a>Voorbeelden
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>bevat
Retourneert waar als een element in de geleverde matrix als waar wordt geëvalueerd in het geleverde predicaat. Contains verwacht een verwijzing naar één element in de predicaat functie als #item.

### <a name="examples"></a>Voorbeelden
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Volgende stappen

* [De afgeleide kolom transformatie gebruiken om uw hiërarchische structuren te bouwen](data-flow-derived-column.md)
