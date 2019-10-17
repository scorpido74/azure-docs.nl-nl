---
title: JSON gebruiken in de toewijzing van gegevens stroom in Azure Data Factory
description: Azure Data Factory gegevens stroom voor toewijzing heeft ingebouwde mogelijkheden voor het verwerken van JSON-documenten met hiërarchieën
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: fe412e9e682fb55f1664c546e6b6c5a347527adb
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387348"
---
# <a name="mapping-data-flow-json-handling"></a>JSON-verwerking van gegevens stroom toewijzen

## <a name="creating-json-structures-in-derived-column"></a>JSON-structuren maken in afgeleide kolom

U kunt een complexe kolom toevoegen aan uw gegevens stroom via de afgeleide column Expression Builder. Voeg in de afgeleide kolom transformatie een nieuwe kolom toe en open de opbouw functie voor expressies door te klikken op het blauwe vak. Als u een kolom complex wilt maken, kunt u de JSON-structuur hand matig invoeren of de UX gebruiken om subkoloms interactief toe te voegen.

### <a name="using-the-expression-builder-ux"></a>De Expression Builder UX gebruiken

Beweeg de muis aanwijzer over een kolom in het deel venster uitvoer schema en klik op het plus pictogram. Selecteer **subkolom toevoegen** om de kolom een complex type te maken.

![Subkolom toevoegen](media/data-flow/addsubcolumn.png "Subkolom toevoegen")

U kunt op dezelfde manier extra kolommen en subkolomlen toevoegen. Voor elk niet-complex veld kan een expressie in de expressie-editor aan de rechter kant worden toegevoegd.

![Complexe kolom](media/data-flow/complexcolumn.png "Complexe kolom")

### <a name="entering-the-json-structure-manually"></a>De JSON-structuur hand matig invoeren

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

U kunt met behulp van een JSON-gegevensset als bron in uw gegevens stroom vijf extra instellingen instellen. Deze instellingen kunt u vinden onder de accordeon van de **JSON-instellingen** op het tabblad **bron opties** .  

![JSON-instellingen](media/data-flow/json-settings.png "JSON-instellingen")

### <a name="default"></a>Standaard

JSON-gegevens worden standaard in de volgende indeling gelezen.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Eén document

Als er **één document** is geselecteerd, stroomt de toewijzing van gegevens een JSON-document uit elk bestand. 

``` json
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

Als **kolom namen die geen aanhalings tekens** zijn geselecteerd, worden door het toewijzen van gegevens stromen JSON-kolommen gelezen die niet tussen aanhalings tekens staan. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Bevat opmerkingen

Selecteer **opmerkingen bevat** als de JSON-gegevens C of C++ stijl opmerkingen hebben.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Enkel aanhalings teken

**Eén aanhalings** teken selecteren als de JSON-velden en-waarden enkele aanhalings tekens gebruiken in plaats van dubbele aanhalings tekens.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Back slash met escape-teken

**Eén aanhalings** teken selecteren als backslashes worden gebruikt om tekens in de JSON-gegevens te escapepen.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Functies van een hogere orde

Een functie met een hogere volg orde is een functie die in een of meer functies als argument wordt gebruikt. Hieronder vindt u een lijst met functies met hogere volg orde die worden ondersteund bij het toewijzen van gegevens stromen waarmee matrix bewerkingen kunnen worden uitgevoerd.

### <a name="filter"></a>Filterwebonderdelen
Hiermee worden elementen van de matrix gefilterd die niet voldoen aan het gegeven predicaat. Het filter verwacht een verwijzing naar één element in de predicaat functie als #item.

#### <a name="examples"></a>Voorbeelden
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
Wijst elk element van de matrix toe aan een nieuw element met behulp van de gegeven expressie. De toewijzing verwacht een verwijzing naar één element in de expressie functie als #item.

#### <a name="examples"></a>Voorbeelden
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>besparen
Verzamelt elementen in een matrix. Reductie verwacht een verwijzing naar een accumulator en één element in de eerste expressie functie als #acc en #item en verwacht de resulterende waarde als #result die in de tweede expressie functie moet worden gebruikt.

#### <a name="examples"></a>Voorbeelden
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>Acties
Sorteert de matrix met behulp van de meegeleverde predicaat functie. Bij het sorteren wordt een verwijzing verwacht naar twee opeenvolgende elementen in de expressie-functie als #item1 en #item2.

#### <a name="examples"></a>Voorbeelden
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>daarin
Retourneert waar als een element in de geleverde matrix als waar wordt geëvalueerd in het geleverde predicaat. Contains verwacht een verwijzing naar één element in de predicaat functie als #item.

#### <a name="examples"></a>Voorbeelden
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Volgende stappen

* [De afgeleide kolom transformatie gebruiken om uw hiërarchische structuren te bouwen](data-flow-derived-column.md)
