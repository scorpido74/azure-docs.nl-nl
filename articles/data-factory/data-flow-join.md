---
title: Deelnemen aan transformatie in het toewijzen van gegevensstroom
description: Gegevens uit twee gegevensbronnen combineren met de jointransformatie in azure datafabriek
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 5c388dd2b3e4f40fbf2ed75cf3f1b8ab31aee394
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606408"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Deelnemen aan transformatie in het toewijzen van gegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de join-transformatie om gegevens uit twee bronnen of stromen te combineren in een kaartgegevensstroom. De uitvoerstroom bevat alle kolommen van beide bronnen die overeenkomen op basis van een joinvoorwaarde. 

## <a name="join-types"></a>Jointypen

Het toewijzen van gegevensstromen ondersteunt momenteel vijf verschillende jointypen.

### <a name="inner-join"></a>Inner Join

Inner join-expressie levert alleen rijen op met overeenkomende waarden in beide tabellen.

### <a name="left-outer"></a>Linkerbuitenkant

Linker buitenste join retourneert alle rijen van de linkerstream en overeenkomende records van de rechterstream. Als een rij van de linkerstream geen overeenkomst heeft, worden de uitvoerkolommen van de rechterstream ingesteld op NULL. De uitvoer zal de rijen zijn die door een binnenjoin plus de ongeëvenaarde rijen van de linkerstroom worden teruggegeven.

> [!NOTE]
> De Spark-engine die door gegevensstromen wordt gebruikt, zal af en toe uitvallen als gevolg van mogelijke cartesiaanse producten in uw join-omstandigheden. Als dit gebeurt, u overschakelen naar een aangepaste cross join en handmatig voer uw join conditie. Dit kan resulteren in tragere prestaties in uw gegevensstromen, omdat de uitvoeringsengine mogelijk alle rijen van beide zijden van de relatie moet berekenen en vervolgens rijen moet filteren.

### <a name="right-outer"></a>Rechter buiten

Rechts naaste join retourneert alle rijen van de rechterstream en overeenkomende records van de linkerstream. Als een rij van de rechterstream niet overeenkomt, worden de uitvoerkolommen van de linkerstream ingesteld op NULL. De uitvoer zal de rijen zijn die door een binnenjoin plus de ongeëvenaarde rijen van de juiste stroom worden teruggegeven.

### <a name="full-outer"></a>Volledige buitenkant

Volledige buitenste join-uitvoer maakt alle kolommen en rijen van beide zijden met NULL-waarden voor kolommen die niet overeenkomen.

### <a name="custom-cross-join"></a>Aangepaste cross join

Cross join uitgangen het kruisproduct van de twee stromen op basis van een aandoening. Als u een voorwaarde gebruikt die geen gelijkheid is, geeft u een aangepaste expressie op als voorwaarde voor cross-join. De uitvoerstroom zijn alle rijen die voldoen aan de joinvoorwaarde.

U dit jointype gebruiken voor ```OR``` non-equi joins en voorwaarden.

Als u expliciet een volledig cartesiaans product wilt produceren, gebruikt u de transformatie Afgeleide kolom in elk van de twee onafhankelijke streams voordat de join wordt samengevoegd om een synthetische sleutel te maken waarop u wilt overeenkomen. Maak bijvoorbeeld een nieuwe kolom in Afgeleide ```SyntheticKey``` kolom in elke ```1```stream die wordt aangeroepen en stel deze in op die gelijk is aan . Gebruik ```a.SyntheticKey == b.SyntheticKey``` dan als uw aangepaste join expressie.

> [!NOTE]
> Zorg ervoor dat u ten minste één kolom van elke kant van uw linker- en rechterrelatie opneemt in een aangepaste cross join. Het uitvoeren van kruisjoins met statische waarden in plaats van kolommen van elke kant resulteert in volledige scans van de gehele gegevensset, waardoor uw gegevensstroom slecht presteert.

## <a name="configuration"></a>Configuratie

1. Kies met welke gegevensstroom je lid wordt in de **vervolgkeuzelijst Rechtsstroom.**
1. Selecteer het **type Join**
1. Kies op welke belangrijke kolommen u wilt overeenkomen voor de voorwaarde. Standaard zoekt de gegevensstroom naar gelijkheid tussen één kolom in elke stream. Als u wilt vergelijken via een berekende waarde, zweeft u boven de kolomvervolgkeuzelijst en selecteert **u Berekende kolom**.

![Deelnemen aan transformatie](media/data-flow/join.png "Koppelen")

## <a name="optimizing-join-performance"></a>De prestaties van de join optimaliseren

In tegenstelling tot samenvoegen in tools zoals SSIS, is de join-transformatie geen verplichte samenvoegbewerking. De jointoetsen hoeven niet te worden gesorteerd. De join-bewerking vindt plaats op basis van de optimale join-bewerking in Spark, uitzending of map-side join.

![Join Transformatie optimaliseren](media/data-flow/joinoptimize.png "Deelnemen aan optimalisatie")

Als een of beide gegevensstromen passen in het geheugen van het werknemersknooppunt, u uw prestaties verder optimaliseren door **Broadcast** in te schakelen op het tabblad Optimaliseren. U uw gegevens ook opnieuw verdelen over de join-bewerking, zodat deze beter in het geheugen per werknemer passen.

## <a name="self-join"></a>Zelf lid worden

Als u een gegevensstroom met zichzelf wilt aansluiten, alias een bestaande stroom met een geselecteerde transformatie. Maak een nieuwe vertakking door op het pluspictogram naast een transformatie te klikken en **Nieuwe vertakking te**selecteren. Voeg een selecte transformatie toe aan de alias van de oorspronkelijke stream. Voeg een jointransformatie toe en kies de oorspronkelijke stream als de **linkerstream** en de selectietransformatie als de **rechterstream**.

![Zelf lid worden](media/data-flow/selfjoin.png "Zelf lid worden")

## <a name="testing-join-conditions"></a>Testen join voorwaarden

Gebruik bij het testen van de join-transformaties met gegevensvoorbeeld in de foutopsporingsmodus een kleine set bekende gegevens. Wanneer u rijen uit een grote gegevensset bemonstert, u niet voorspellen welke rijen en sleutels worden gelezen voor het testen. Het resultaat is niet-deterministisch, wat betekent dat uw join voorwaarden mogen geen wedstrijden terug.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Voorbeeld van Inner join

Het onderstaande voorbeeld is `JoinMatchedData` een join `TripData` transformatie `TripFare`met de naam die links stream en rechter stream neemt .  De aan/s-voorwaarde is `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` de expressie die true retourneert als `hack_license`de , `medallion`, en `vendor_id` `pickup_datetime` kolommen in elke streamovereenkomst. De `joinType` `'inner'`is. We zijn het inschakelen van uitzendingen `broadcast` in `'left'`alleen de linker stream dus heeft waarde .

In de UX van de Data Factory lijkt deze transformatie op de onderstaande afbeelding:

![Voorbeeld van deelnemen](media/data-flow/join-script1.png "Voorbeeld van deelnemen")

Het gegevensstroomscript voor deze transformatie bevindt zich in het onderstaande fragment:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>Voorbeeld van aangepaste cross-join

Het onderstaande voorbeeld is `JoiningColumns` een join `LeftStream` transformatie `RightStream`met de naam die links stream en rechter stream neemt . Deze transformatie neemt twee streams in en `leftstreamcolumn` voegt alle `rightstreamcolumn`rijen samen waar de kolom groter is dan kolom . De `joinType` `cross`is. Broadcasting is niet `broadcast` ingeschakeld `'none'`heeft waarde .

In de UX van de Data Factory lijkt deze transformatie op de onderstaande afbeelding:

![Voorbeeld van deelnemen](media/data-flow/join-script2.png "Voorbeeld van deelnemen")

Het gegevensstroomscript voor deze transformatie bevindt zich in het onderstaande fragment:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Volgende stappen

Nadat u gegevens hebt samengebracht, maakt u een [afgeleide kolom](data-flow-derived-column.md) en [laat u](data-flow-sink.md) uw gegevens zinken naar een doelgegevensarchief.
