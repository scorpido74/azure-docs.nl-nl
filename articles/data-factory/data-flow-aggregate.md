---
title: Geaggregeerde transformatie in kaartgegevensstroom
description: Meer informatie over het samenvoegen van gegevens op schaal in Azure Data Factory met de totale transformatie van de toewijzingsgegevensstroom.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 050fcc33f0e3d65ca8e94b3be143c5b1ae77608d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416560"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Geaggregeerde transformatie in kaartgegevensstroom

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

De samengevoegde transformatie definieert aggregaties van kolommen in uw gegevensstromen. Met de opbouwfunctie voor expressies u verschillende typen aggregaties definiëren, zoals SOM, MIN, MAX en COUNT gegroepeerd op bestaande of berekende kolommen.

## <a name="group-by"></a>Groeperen op

Selecteer een bestaande kolom of maak een nieuwe berekende kolom die u als groep per component voor uw aggregatie wilt gebruiken. Als u een bestaande kolom wilt gebruiken, selecteert u deze in de vervolgkeuzelijst. Als u een nieuwe berekende kolom wilt maken, houdt u de muisaanwijzer op de component en klikt u op **Berekende kolom**. Hiermee wordt de [opbouw van gegevensstromenexpressie geopend](concepts-data-flow-expression-builder.md). Zodra u de berekende kolom hebt gemaakt, voert u de naam van de uitvoerkolom in onder het veld **Naam als.** Als u een extra groep per clausule wilt toevoegen, houdt u de muisaanwijzer op een bestaande clausule en klikt u op het pluspictogram.

![Transformatiegroep samenvoegen op instellingen](media/data-flow/agg.png "Transformatiegroep samenvoegen op instellingen")

Een groep op clausule is optioneel in een aggregaattransformatie.

## <a name="aggregate-column"></a>Kolom Aggregaat 

Ga naar het tabblad **Aggregaten** om aggregatieexpressies te maken. U een bestaande kolom overschrijven met een aggregatie of een nieuw veld maken met een nieuwe naam. De aggregatie-expressie wordt ingevoerd in het rechtervak naast de kolomnaamkiezer. Als u de expressie wilt bewerken, klikt u op het tekstvak om de opbouw van de expressie te openen. Als u extra aggregaties wilt toevoegen, houdt u de muisaanwijzer boven een bestaande expressie en klikt u op pluspictogram om een nieuwe aggregatiekolom of [kolompatroon](concepts-data-flow-column-pattern.md)te maken .

Elke aggregatie-expressie moet ten minste één geaggregeerde functie bevatten.

![Geaggregeerde transformatie-aggregaatinstellingen](media/data-flow/agg2.png "Geaggregeerde transformatie-aggregaatinstellingen")


> [!NOTE]
> In de foutopsporingsmodus kan de opbouw van expressies geen gegevensvoorbeelden met geaggregeerde functies produceren. Als u gegevensvoorbeelden voor geaggregeerde transformaties wilt weergeven, sluit u de opbouwvan expressies en bekijkt u de gegevens via het tabblad 'Gegevensvoorbeeld'.

## <a name="reconnect-rows-and-columns"></a>Rijen en kolommen opnieuw verbinden

Geaggregeerde transformaties zijn vergelijkbaar met SQL-verzamelselectiequery's. Kolommen die niet per component in uw groep zijn opgenomen of geaggregeerde functies, worden niet doorgestroomd naar de uitvoer van uw totale transformatie. Als u andere kolommen in uw geaggregeerde uitvoer wilt opnemen, doet u een van de volgende methoden:

* Gebruik een samengevoegde `last()` `first()` functie zoals of om die extra kolom op te nemen.
* Ga opnieuw naar de kolommen met uw uitvoerstroom met behulp van het [zelfjoinpatroon](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Dubbele rijen verwijderen

Een veelvoorkomend gebruik van de geaggregeerde transformatie is het verwijderen of identificeren van dubbele vermeldingen in brongegevens. Dit proces staat bekend als ontdubbeling. Op basis van een set van groep door toetsen, gebruik maken van een heuristische van uw keuze om te bepalen welke dubbele rij te houden. Gemeenschappelijke heuristieken `max()`zijn `min()` `first()`, `last()`, en . Gebruik [kolompatronen](concepts-data-flow-column-pattern.md) om de regel toe te passen op elke kolom, behalve op de groep op kolommen.

![Ontdubbeling](media/data-flow/agg-dedupe.png "Ontdubbeling")

In het bovenstaande `ProductID` voorbeeld `Name` worden kolommen en worden gebruikt voor het groeperen. Als twee rijen dezelfde waarden hebben voor deze twee kolommen, worden ze beschouwd als duplicaten. In deze geaggregeerde transformatie worden de waarden van de eerste rij gelijk gehouden en worden alle andere waarden verwijderd. Met behulp van de syntaxis van `ProductID` `Name` het kolompatroon worden alle kolommen waarvan de namen niet zijn en zijn toegewezen aan hun bestaande kolomnaam en met de waarde van de eerste overeenkomende rijen. Het uitvoerschema is hetzelfde als het invoerschema.

Voor scenario's `count()` voor gegevensvalidatie kan de functie worden gebruikt om te tellen hoeveel duplicaten er zijn.

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

In het onderstaande voorbeeld `MoviesYear` wordt een `year`binnenkomende stream genomen en wordt rijen per kolom groepeert. Met de transformatie `avgrating` wordt een geaggregeerde kolom `Rating`gemaakt die wordt geëvalueerd op het gemiddelde van de kolom . Deze geaggregeerde `AvgComedyRatingsByYear`transformatie wordt genoemd .

In de UX van de Data Factory lijkt deze transformatie op de onderstaande afbeelding:

![Groep e.a.](media/data-flow/agg-script1.png "Groep e.a.")

![Voorbeeld van aggregaat](media/data-flow/agg-script2.png "Voorbeeld van aggregaat")

Het gegevensstroomscript voor deze transformatie bevindt zich in het onderstaande fragment.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Script voor gegevensstroom samenvoegen](media/data-flow/aggdfs1.png "Script voor gegevensstroom samenvoegen")

```MoviesYear```: Afgeleide kolomdefiniërende jaar- en titelkolommen ```AvgComedyRatingByYear```: Geaggregeerde transformatie ```avgrating```voor gemiddelde beoordeling van komedies gegroepeerd per jaar : Naam van nieuwe kolom die wordt gemaakt om de geaggregeerde waarde vast te houden

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Volgende stappen

* Op venster gebaseerde aggregatie definiëren met behulp van de [venstertransformatie](data-flow-window.md)
