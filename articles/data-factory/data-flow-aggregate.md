---
title: Statistische trans formatie in toewijzing van gegevens stroom
description: Meer informatie over het samen stellen van gegevens op schaal in Azure Data Factory met de statistische trans formatie toewijzings gegevens stroom.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 74b96bf2cac0de7c57e496c637f2e3ef549eb61f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930458"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Statistische trans formatie in toewijzing van gegevens stroom 

De cumulatieve trans formatie definieert aggregaties van kolommen in uw gegevens stromen. Met de opbouw functie voor expressies kunt u verschillende typen aggregaties definiëren, zoals som, MIN, maximum en aantal, gegroepeerd op bestaande of berekende kolommen.

## <a name="group-by"></a>Groeperen op

Selecteer een bestaande kolom of maak een nieuwe berekende kolom die u wilt gebruiken als een component Group by voor uw aggregatie. Als u een bestaande kolom wilt gebruiken, selecteert u deze in de vervolg keuzelijst. Als u een nieuwe berekende kolom wilt maken, houdt u de muis aanwijzer boven de component en klikt u op **berekende kolom**. Hiermee opent u de [opbouw functie voor de data flow-expressie](concepts-data-flow-expression-builder.md). Wanneer u de berekende kolom maakt, voert u de naam van de uitvoer kolom onder het veld **naam als in** . Als u een component Group by wilt toevoegen, houdt u de muis aanwijzer over een bestaande component en klikt u op het pictogram met het plus teken.

![Statistische transformatie groep op instellingen](media/data-flow/agg.png "Statistische transformatie groep op instellingen")

Een component Group by is optioneel in een geaggregeerde trans formatie.

## <a name="aggregate-column"></a>Statistische kolom 

Ga naar het tabblad **aggregaties** om aggregatie-expressies samen te stellen. U kunt een bestaande kolom overschrijven met een aggregatie of een nieuw veld maken met een nieuwe naam. De expressie aggregatie wordt ingevoerd in het rechter vak naast de kolom naam kiezer. Als u de expressie wilt bewerken, klikt u op het tekstvak om de opbouw functie voor expressies te openen. Als u extra aggregaties wilt toevoegen, houdt u de muis aanwijzer over een bestaande expressie en klikt u op het pictogram plus om een nieuwe aggregatie kolom of [kolom patroon](concepts-data-flow-column-pattern.md)te maken.

Elke aggregatie-expressie moet ten minste één statistische functie bevatten.

![Statistische instellingen voor trans formatie aggregatie](media/data-flow/agg2.png "Statistische instellingen voor trans formatie aggregatie")


> [!NOTE]
> In de foutopsporingsmodus kan de opbouw functie voor expressies geen gegevens previews maken met statistische functies. Voor het weer geven van voor beelden van gegevens voor samengevoegde trans formaties, sluit u de opbouw functie voor expressies en bekijkt u de gegevens via het tabblad gegevens voorbeeld.

## <a name="reconnect-rows-and-columns"></a>Rijen en kolommen opnieuw verbinden

Samengestelde trans formaties zijn vergelijkbaar met SQL-query's voor aggregatie selectie. Kolommen die niet zijn opgenomen in de component Group by of statistische functies, stromen niet naar de uitvoer van uw geaggregeerde trans formatie. Als u andere kolommen in de cumulatieve uitvoer wilt opnemen, voert u een van de volgende methoden uit:

* Gebruik een statistische functie zoals `last()` of `first()` om de extra kolom op te nemen.
* De kolommen opnieuw samen voegen met de uitvoer stroom met het [Self-deelname patroon](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Voorbeeld

In het onderstaande voor beeld wordt een inkomende stroom `MoviesYear` en worden rijen gegroepeerd op kolom `year`. De trans formatie maakt een statistische kolom `avgrating` die het gemiddelde van kolom `Rating`evalueert. Deze cumulatieve trans formatie heet `AvgComedyRatingsByYear`.

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Groeperen op voor beeld](media/data-flow/agg-script1.png "Groeperen op voor beeld")

![Voor beeld van geaggregeerde](media/data-flow/agg-script2.png "Voor beeld van geaggregeerde")

Het gegevens stroom script voor deze trans formatie bevindt zich in het onderstaande fragment.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Volgende stappen

* Aggregatie op basis van een venster definiëren met behulp van de [venster transformatie](data-flow-window.md)
