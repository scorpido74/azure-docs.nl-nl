---
title: Transformatie afvlakken in kaartgegevensstroom
description: Hiërarchische gegevens denormaliseren met de afvlaktransformatie
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413682"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Transformatie afvlakken in kaartgegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de afvlaktransformatie om matrixwaarden in hiërarchische structuren zoals JSON te gebruiken en deze uit te rollen in afzonderlijke rijen. Dit proces staat bekend als denormalisatie.

## <a name="configuration"></a>Configuratie

De afvlaktransformatie bevat de volgende configuratie-instellingen

![Afvlakkeninstellingen](media/data-flow/flatten1.png "Afvlakkeninstellingen")

### <a name="unroll-by"></a>Roll uitrollen door

Selecteer een array die u wilt uitrollen. De uitvoergegevens hebben één rij per item in elke array. Als de uitrollen op matrix in de invoerrij null of leeg is, is er één uitvoerrij met niet-overeenkomende waarden als null.

### <a name="unroll-root"></a>Hoofdrol hoofdrollen uitrollen

Standaard rolt de afvlakkende transformatie een array uit naar de bovenkant van de hiërarchie waarin deze bestaat. U optioneel een array selecteren als uw hoofdrol. De roll-hoofd moet een array van complexe objecten zijn die de roll by array is of bevat. Als een uitrollenshoofd is geselecteerd, bevatten de uitvoergegevens ten minste één rij per items in de hoofdmap uitrollen. Als de invoerrij geen items in de hoofdmap uitrollen heeft, wordt deze uit de uitvoergegevens verwijderd. Als u een uitrollenhoofd kiest, wordt altijd een kleiner of gelijk aantal rijen uitgevoerd dan het standaardgedrag.

### <a name="flatten-mapping"></a>Afvlakken

Net als bij de geselecteerde transformatie kiest u de projectie van de nieuwe structuur uit binnenkomende velden en de gedenormaliseerde array. Als een gedenormaliseerde array in kaart wordt gebracht, is de uitvoerkolom hetzelfde gegevenstype als de array. Als de roll by array een array van complexe objecten is die subarrays bevat, wordt een array in kaart gebracht door een item van die subarry in kaart te brengen.

Raadpleeg het tabblad Inspecteren en het voorbeeld van gegevens om de toewijzingsuitvoer te verifiëren.

## <a name="examples"></a>Voorbeelden

Raadpleeg het volgende JSON-object voor de onderstaande voorbeelden van de afvlaktransformatie

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Geen hoofdmap met tekenreeksarray

| Roll uitrollen door | Hoofdrol hoofdrollen uitrollen | Projectie |
| --------- | ----------- | ---------- |
| goods.customers | Geen | name <br> klant = goods.customer |

#### <a name="output"></a>Uitvoer

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Geen hoofdmap met complexe array

| Roll uitrollen door | Hoofdrol hoofdrollen uitrollen | Projectie |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItems | Geen | name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> locatie = locatie |

#### <a name="output"></a>Uitvoer

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Dezelfde hoofdmap als roll array

| Roll uitrollen door | Hoofdrol hoofdrollen uitrollen | Projectie |
| --------- | ----------- | ---------- |
| goods.orders | goods.orders | name <br> goods.orders.shipped.orderItems.itemName <br> goods.customers <br> location |

#### <a name="output"></a>Uitvoer

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Hoofdrol met complexe array uitrollen

| Roll uitrollen door | Hoofdrol hoofdrollen uitrollen | Projectie |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItem | goods.orders |name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> locatie = locatie |

#### <a name="output"></a>Uitvoer

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Voorbeeld

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Volgende stappen

* Gebruik de [draaitransformatie](data-flow-pivot.md) om rijen naar kolommen te draaien.
* Gebruik de [transformatie Unpivot](data-flow-unpivot.md) om kolommen naar rijen te draaien.
