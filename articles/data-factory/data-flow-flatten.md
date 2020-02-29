---
title: Data flow-afvlakkings transformatie toewijzen
description: Afvlakkings transformatie van gegevens stroom Azure Data Factory toewijzen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 415a093fd8a8fbe27e1d240b061548e18f2ca6b6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164726"
---
# <a name="azure-data-factory-flatten-transformation"></a>Azure Data Factory Plate trans formatie

De vlakke trans formatie kan worden gebruikt om matrix waarden binnen een hiërarchische structuur naar nieuwe rijen te draaien, waarbij uw gegevens in feite worden genormaliseerd.

![Werkset voor trans formatie](media/data-flow/flatten5.png "Werkset voor trans formatie")

![Trans formatie 1 afvlakken](media/data-flow/flatten7.png "Trans formatie 1 afvlakken")

## <a name="unroll-by"></a>Uitrollen

Kies eerst de matrix kolom die u wilt uitrollen en draaien.

![Instellingen voor trans formatie afvlakken](media/data-flow/flatten1.png "Instellingen voor trans formatie afvlakken")

## <a name="unroll-root"></a>Hoofdmap uitrollen

Standaard wordt de structuur door ADF afgevlakt bij de niet-gedraaide matrix die u hierboven hebt gekozen. U kunt ook een ander deel van de hiërarchie kiezen om uit te vouwen. ' Hoofdmap uitrollen ' is een optionele instelling.

## <a name="input-columns"></a>Invoer kolommen

Kies ten slotte de projectie van uw nieuwe structuur op basis van de binnenkomende velden, evenals de genormaliseerde kolom die u hebt uitgedraaid.

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
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>Volgende stappen

* Gebruik de [draai transformatie](data-flow-pivot.md) om rijen naar kolommen te draaien.
* Gebruik de [draai tabel transformatie](data-flow-unpivot.md) om kolommen naar rijen te draaien.
