---
title: Afgeleide kolom transformatie in Azure Data Factory gegevens stroom toewijzen | Microsoft Docs
description: Meer informatie over het transformeren van gegevens op schaal in Azure Data Factory met de toewijzing gegevens stroom die is afgeleide kolom transformatie.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 60451fa6152590ed0fde51be436c867f39906acf
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514811"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Afgeleide kolom transformatie bij toewijzing van gegevens stroom

Gebruik de afgeleide kolom transformatie om nieuwe kolommen in uw gegevens stroom te genereren of om bestaande velden te wijzigen.

## <a name="derived-column-settings"></a>Afgeleide kolom instellingen

Als u een bestaande kolom wilt overschrijven, selecteert u deze via de vervolg keuzelijst kolom. Als dat niet het geval is, gebruikt u het veld kolom selectie als tekstvak en typt u de naam van de nieuwe kolom. Als u de expressie van de afgeleide kolom wilt maken, klikt u op het vak expressie invoeren om de [opbouw functie voor de data flow-expressie](concepts-data-flow-expression-builder.md)te openen.

![Afgeleide kolom instellingen](media/data-flow/dc1.png "Afgeleide kolom instellingen")

Beweeg de muis aanwijzer over een bestaande afgeleide kolom en klik op het plus pictogram om extra afgeleide kolommen toe te voegen. Kies **kolom toevoegen** of een **kolom patroon toevoegen**. Kolom patronen kunnen handig zijn als uw kolom namen van uw bronnen variabel zijn. Zie [kolom patronen](concepts-data-flow-column-pattern.md)voor meer informatie.

![Nieuwe afgeleide kolom selectie](media/data-flow/columnpattern.png "Nieuwe afgeleide kolom selectie")

## <a name="data-flow-script"></a>Gegevens stroom script

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

Het onderstaande voor beeld is een afgeleide kolom met de naam `CleanData` die een binnenkomende stream `MoviesYear` gebruikt en twee afgeleide kolommen maakt. De eerste afgeleide kolom vervangt kolom `Rating` met waarde als geheel getal. De tweede afgeleide kolom is een patroon dat overeenkomt met elke kolom waarvan de naam begint met ' films '. Voor elke overeenkomende kolom maakt een kolom `movie` die gelijk is aan de waarde van de overeenkomende kolom, voorafgegaan door ' movie_ '. 

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
