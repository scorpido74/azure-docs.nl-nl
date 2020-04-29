---
title: Voorwaardelijke Splits Transform in toewijzing van gegevens stroom
description: Gegevens in verschillende stromen splitsen met behulp van de Conditional Split trans formatie in Azure Data Factory toewijzing van gegevens stroom
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: bd9241e526d7cf42f0697afb8635c085a08c80d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606489"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Voorwaardelijke Splits Transform in toewijzing van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de Conditional Split trans formatie worden gegevens rijen naar verschillende stromen gerouteerd op basis van overeenkomende voor waarden. De Conditional Split trans formatie is vergelijkbaar met een beslissings structuur voor de situatie in een programmeer taal. De trans formatie evalueert expressies en op basis van de resultaten, stuurt de gegevensrij door naar de opgegeven stroom.

## <a name="configuration"></a>Configuratie

De instelling **splitsen op** bepaalt of de rij van de gegevens naar de eerste overeenkomende stream of elke stream die overeenkomt met moet stromen.

Gebruik de opbouw functie voor de data flow-expressie om een expressie voor de Splits voorwaarde in te voeren. Klik op het plus pictogram in een bestaande rij om een nieuwe voor waarde toe te voegen. Er kan ook een standaard stroom worden toegevoegd voor rijen die niet overeenkomen met een voor waarde.

![Conditional Split](media/data-flow/conditionalsplit1.png "Conditional Split opties")

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

Het onderstaande voor beeld is een Conditional Split trans `SplitByYear` formatie met de naam die `CleanData`in de inkomende stroom neemt. Deze trans formatie heeft twee gesplitste voor waarden `year < 1960` en. `year > 1980` `disjoint`is onwaar, omdat de gegevens worden verplaatst naar de eerste overeenkomende voor waarde. Elke rij die overeenkomt met de eerste voor waarde `moviesBefore1960`, gaat naar uitvoer stroom. Alle resterende rijen die overeenkomen met de tweede voor waarde `moviesAFter1980`, gaan naar uitvoer stroom. Alle andere rijen stromen via de standaard stroom `AllOtherMovies`.

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Conditional Split](media/data-flow/conditionalsplit1.png "Conditional Split opties")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Volgende stappen

Gemeen schappelijke gegevensstroom transformaties die worden gebruikt met Conditional Split zijn de trans [formatie voor samen voegen](data-flow-join.md), de [Zoek transformatie](data-flow-lookup.md)en de [selectie transformatie](data-flow-select.md)
