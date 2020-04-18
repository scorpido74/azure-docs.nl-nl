---
title: Filtertransformatie in kaartgegevensstroom
description: Rijen filteren met de filtertransformatie in de toewijzingsgegevensstroom van Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 32a40f3d4da93318c6d2ec25295c89a7b53141e4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606427"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filtertransformatie in kaartgegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de filtertransformaties kan rijfilteren op basis van een voorwaarde. De uitvoerstroom bevat alle rijen die overeenkomen met de filtervoorwaarde. De filtertransformatie is vergelijkbaar met een WHERE-component in SQL.

## <a name="configuration"></a>Configuratie

Gebruik de opbouw van gegevensstromenexpressie om een expressie voor de filtervoorwaarde in te voeren. Als u de opbouwfunctie voor expressies wilt openen, klikt u op het blauwe vak. De filterconditie moet van het type booleaan zijn. Zie de documentatie van de [expressiebouwer](concepts-data-flow-expression-builder.md) voor meer informatie over het maken van een expressie.

![Filtertransformatie](media/data-flow/filter1.png "Filtertransformatie")

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Voorbeeld

Het onderstaande voorbeeld is `FilterBefore1960` een filtertransformatie `CleanData`met de naam die inkomende stroom inneemt. De filtervoorwaarde is `year <= 1960`de expressie .

In de UX van de Data Factory lijkt deze transformatie op de onderstaande afbeelding:

![Filtertransformatie](media/data-flow/filter1.png "Filtertransformatie")

Het gegevensstroomscript voor deze transformatie bevindt zich in het onderstaande fragment:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Volgende stappen

Kolommen filteren met de [geselecteerde transformatie](data-flow-select.md)
