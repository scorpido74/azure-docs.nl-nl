---
title: Trans formatie filteren in Azure Data Factory gegevens stroom toewijzen
description: Rijen filteren met behulp van de filter transformatie in Azure Data Factory gegevens stroom toewijzen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 77587d007d9bfcdc1461201f676c79880bd89d6b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676177"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Trans formatie filteren in gegevens stroom toewijzen

Met filter transformaties kunt u filteren op rijen op basis van een voor waarde. De uitvoer stroom bevat alle rijen die overeenkomen met de filter voorwaarde. De filter transformatie is vergelijkbaar met een WHERE-component in SQL.

## <a name="configuration"></a>Configuratie

Gebruik de opbouw functie voor de data flow-expressie om een expressie voor de filter voorwaarde in te voeren. Klik op het blauwe vak om de opbouw functie voor expressies te openen. De filter voorwaarde moet van het type Boolean zijn. Zie de documentatie over [Expression Builder](concepts-data-flow-expression-builder.md) voor meer informatie over het maken van een expressie.

![Filter transformatie](media/data-flow/filter1.png "Filter transformatie")

## <a name="data-flow-script"></a>Gegevens stroom script

### <a name="syntax"></a>Syntaxis

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Voorbeeld

Het onderstaande voor beeld is een Conditional Split-trans formatie met de naam `FilterBefore1960` die in de binnenkomende stream `CleanData`neemt. De filter voorwaarde is de expressie `year <= 1960`.

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Filter transformatie](media/data-flow/filter1.png "Filter transformatie")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Volgende stappen

Kolommen filteren met de [geselecteerde trans formatie](data-flow-select.md)
