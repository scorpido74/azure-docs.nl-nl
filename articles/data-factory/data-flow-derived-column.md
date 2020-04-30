---
title: Afgeleide kolom transformatie bij toewijzing van gegevens stroom
description: Meer informatie over het transformeren van gegevens op schaal in Azure Data Factory met de toewijzing gegevens stroom die is afgeleide kolom transformatie.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 38ec2d4619f47bf9fc4d1815cb6e9990cef72dcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606503"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Afgeleide kolom transformatie bij toewijzing van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de afgeleide kolom transformatie om nieuwe kolommen in uw gegevens stroom te genereren of om bestaande velden te wijzigen.

## <a name="derived-column-settings"></a>Afgeleide kolom instellingen

Als u een bestaande kolom wilt overschrijven, selecteert u deze via de vervolg keuzelijst kolom. Als dat niet het geval is, gebruikt u het veld kolom selectie als tekstvak en typt u de naam van de nieuwe kolom. Als u de expressie van de afgeleide kolom wilt maken, klikt u op het vak expressie invoeren om de [opbouw functie voor de data flow-expressie](concepts-data-flow-expression-builder.md)te openen.

![Afgeleide kolom instellingen](media/data-flow/dc1.png "Afgeleide kolom instellingen")

Beweeg de muis aanwijzer over een bestaande afgeleide kolom en klik op het plus pictogram om extra afgeleide kolommen toe te voegen. Kies **kolom toevoegen** of een **kolom patroon toevoegen**. Kolom patronen kunnen handig zijn als uw kolom namen van uw bronnen variabel zijn. Zie [kolom patronen](concepts-data-flow-column-pattern.md)voor meer informatie.

![Nieuwe afgeleide kolom selectie](media/data-flow/columnpattern.png "Nieuwe afgeleide kolom selectie")

## <a name="build-schemas-in-output-schema-pane"></a>Schema's maken in het deel venster uitvoer schema

De kolommen die u wilt wijzigen en toevoegen aan uw schema worden weer gegeven in het deel venster uitvoer schema. U kunt hier interactief eenvoudige en complexe gegevens structuren bouwen. Selecteer **kolom toevoegen**om extra velden toe te voegen. Selecteer **subkolom toevoegen**om hiërarchieën te bouwen.

![Subkolom toevoegen](media/data-flow/addsubcolumn.png "Subkolom toevoegen")

Zie [JSON handling in mapping data flow](format-json.md#mapping-data-flow-properties)(Engelstalig) voor meer informatie over het verwerken van complexe typen in de gegevens stroom.

![Complexe kolom toevoegen](media/data-flow/complexcolumn.png "Kolommen toevoegen")

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Voorbeeld

Het onderstaande voor beeld is een afgeleide kolom met de `CleanData` naam `MoviesYear` die een inkomende stroom gebruikt en twee afgeleide kolommen maakt. De eerste afgeleide kolom `Rating` vervangt de kolom met de classificatie waarde als een geheel getal. De tweede afgeleide kolom is een patroon dat overeenkomt met elke kolom waarvan de naam begint met ' films '. Voor elke overeenkomende kolom wordt een kolom `movie` gemaakt die gelijk is aan de waarde van de overeenkomende kolom, voorafgegaan door ' movie_ '. 

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Voor beeld afleiden](media/data-flow/derive-script1.png "Voor beeld afleiden")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [taal van de toewijzings gegevens stroom](data-flow-expression-functions.md).
