---
title: Afgeleide kolomtransformatie in kaartgegevensstroom
description: Meer informatie over het transformeren van gegevens op schaal in Azure Data Factory met de toewijzingsgegevensstroom Afgeleide kolomtransformatie.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 38ec2d4619f47bf9fc4d1815cb6e9990cef72dcf
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606503"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Afgeleide kolomtransformatie in kaartgegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de afgeleide kolomtransformatie om nieuwe kolommen in uw gegevensstroom te genereren of om bestaande velden te wijzigen.

## <a name="derived-column-settings"></a>Afgeleide kolominstellingen

Als u een bestaande kolom wilt overschrijven, selecteert u deze via de vervolgkeuzelijst kolom. Gebruik anders het kolomselectieveld als tekstvak en typ de naam van de nieuwe kolom. Als u de expressie van de afgeleide kolom wilt maken, klikt u op het vak Expressie invoeren om de opbouwvan de [opbouwvan gegevensstroomexpressies](concepts-data-flow-expression-builder.md)te openen.

![Afgeleide kolominstellingen](media/data-flow/dc1.png "Afgeleide kolominstellingen")

Als u extra afgeleide kolommen wilt toevoegen, houdt u de muisaanwijzer op een bestaande afgeleide kolom en klikt u op het pluspictogram. Kies **Kolom toevoegen** of **Kolompatroon toevoegen**. Kolompatronen kunnen handig zijn als uw kolomnamen variabel zijn uit uw bronnen. Zie [Kolompatronen](concepts-data-flow-column-pattern.md)voor meer informatie .

![Nieuwe afgeleide kolomselectie](media/data-flow/columnpattern.png "Nieuwe afgeleide kolomselectie")

## <a name="build-schemas-in-output-schema-pane"></a>Schema's bouwen in deelvenster Uitvoerschema

De kolommen die u wijzigt en aan uw schema toevoegt, worden weergegeven in het deelvenster Uitvoerschema.The columns you're modifying and add to your schema are listed in the Output Schema pane,. U hier interactief eenvoudige en complexe gegevensstructuren bouwen. Als u extra velden wilt toevoegen, selecteert u **Kolom toevoegen**. Als u hiërarchieën wilt opbouwen, selecteert u **Subkolom toevoegen**.

![Subkolom toevoegen](media/data-flow/addsubcolumn.png "Subkolom toevoegen")

Zie [JSON handling in mapping data flow](format-json.md#mapping-data-flow-properties)voor meer informatie over het verwerken van complexe typen in de gegevensstroom.

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

Het onderstaande voorbeeld is `CleanData` een afgeleide kolom `MoviesYear` met de naam die een binnenkomende stroom neemt en twee afgeleide kolommen maakt. De eerste afgeleide `Rating` kolom vervangt kolom met de waarde van de classificatie als een geheel getaltype. De tweede afgeleide kolom is een patroon dat overeenkomt met elke kolom waarvan de naam begint met 'films'. Voor elke overeenkomende kolom wordt `movie` een kolom gemaakt die gelijk is aan de waarde van de overeenkomende kolom die vooraf is gekoppeld aan 'movie_'. 

In de UX van de Data Factory lijkt deze transformatie op de onderstaande afbeelding:

![Voorbeeld afleiden](media/data-flow/derive-script1.png "Voorbeeld afleiden")

Het gegevensstroomscript voor deze transformatie bevindt zich in het onderstaande fragment:

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

- Meer informatie over de [expressietaal Gegevensstroom toewijzen](data-flow-expression-functions.md).
