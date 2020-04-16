---
title: Voorwaardelijke gesplitste transformatie in kaartgegevensstroom
description: Gegevens splitsen in verschillende stromen met behulp van de voorwaardelijke gesplitste transformatie in azure datastroom voor het toewijzen van gegevens
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 20def8ca51f21d914e7090999e8284244c5f3ec7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416494"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Voorwaardelijke gesplitste transformatie in kaartgegevensstroom

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

De voorwaardelijke gesplitste transformatie leidt gegevensrijen naar verschillende streams op basis van overeenkomende voorwaarden. De voorwaardelijke gesplitste transformatie is vergelijkbaar met een CASE-beslissingsstructuur in een programmeertaal. De transformatie evalueert expressies en leidt de gegevensrij op basis van de resultaten naar de opgegeven stroom.

## <a name="configuration"></a>Configuratie

Met **de instelling Splitsen wordt** bepaald of de rij gegevens naar de eerste overeenkomende stream of elke stream loopt die overeenkomt met deze.

Gebruik de opbouw van gegevensstromenexpressie om een expressie voor de gesplitste voorwaarde in te voeren. Als u een nieuwe voorwaarde wilt toevoegen, klikt u op het pluspictogram in een bestaande rij. Er kan ook een standaardstream worden toegevoegd voor rijen die niet overeenkomen met een voorwaarde.

![voorwaardelijke splitsing](media/data-flow/conditionalsplit1.png "opties voor voorwaardelijke splitsing")

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Voorbeeld

Het onderstaande voorbeeld is `SplitByYear` een voorwaardelijke gesplitste `CleanData`transformatie met de naam die inkomende stream inneemt . Deze transformatie heeft `year < 1960` twee `year > 1980`gesplitste voorwaarden en . `disjoint`is vals omdat de gegevens naar de eerste overeenkomende voorwaarde gaan. Elke rij die overeenkomt met `moviesBefore1960`de eerste voorwaarde gaat naar uitvoerstroom. Alle resterende rijen die overeenkomen met `moviesAFter1980`de tweede voorwaarde gaan naar uitvoerstroom . Alle andere rijen stromen `AllOtherMovies`door de standaardstroom .

In de UX van de Data Factory lijkt deze transformatie op de onderstaande afbeelding:

![voorwaardelijke splitsing](media/data-flow/conditionalsplit1.png "opties voor voorwaardelijke splitsing")

Het gegevensstroomscript voor deze transformatie bevindt zich in het onderstaande fragment:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Volgende stappen

Algemene gegevensstroomtransformaties die worden gebruikt met voorwaardelijke splitsing zijn de [jointransformatie,](data-flow-join.md) [opzoektransformatie](data-flow-lookup.md)en de [geselecteerde transformatie](data-flow-select.md)
