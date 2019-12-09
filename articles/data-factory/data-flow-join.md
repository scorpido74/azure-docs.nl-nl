---
title: Trans formatie koppelen in gegevens stroom toewijzen
description: Gegevens uit twee gegevens bronnen combi neren met behulp van de deelname transformatie in Azure Data Factory gegevens stroom toewijzen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/17/2019
ms.openlocfilehash: 09d2c1d063c542583dc11fab0805a9392661426f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930347"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Trans formatie koppelen in gegevens stroom toewijzen

Gebruik de koppelings transformatie om gegevens uit twee bronnen of streams te combi neren in een toewijzings gegevens stroom. De uitvoer stroom bevat alle kolommen van beide bronnen die overeenkomen op basis van een samenvoegings voorwaarde. 

## <a name="join-types"></a>Koppelings typen

Toewijzing van gegevens stromen ondersteunt momenteel vijf verschillende jointypen.

### <a name="inner-join"></a>Inner join

Inner join uitvoer alleen rijen met overeenkomende waarden in beide tabellen.

### <a name="left-outer"></a>Linker outer join

Links outer join retourneert alle rijen uit de linker stroom en overeenkomende records uit de juiste stroom. Als een rij uit de linker stroom niet overeenkomt, worden de uitvoer kolommen van de juiste stroom ingesteld op NULL. De uitvoer is de rijen die worden geretourneerd door een inner join plus de niet-overeenkomende rijen uit de linker stroom.

### <a name="right-outer"></a>Rechter outer join

De rechter outer join retourneert alle rijen uit de juiste stroom en overeenkomende records uit de linker stroom. Als een rij uit de juiste stroom niet overeenkomt, worden de uitvoer kolommen van de linker stroom ingesteld op NULL. De uitvoer is de rijen die worden geretourneerd door een inner join plus de niet-overeenkomende rijen uit de juiste stroom.

### <a name="full-outer"></a>Volledige outer join

Volledige outer join alle kolommen en rijen van beide zijden met NULL-waarden voor kolommen die niet overeenkomen, worden uitgevoerd.

### <a name="cross-join"></a>Cross join

Met cross-koppeling wordt het cross-product van de twee stromen op basis van een voor waarde uitgevoerd. Als u een voor waarde gebruikt die niet gelijk is aan elkaar, geeft u een aangepaste expressie op als cross-samenvoegings voorwaarde. De uitvoer stroom is alle rijen die voldoen aan de voor waarde voor samen voegen. Als u een Cartesisch product wilt maken dat elke rij-combi natie uitvoert, geeft u `true()` op als uw samenvoegings voorwaarde.

## <a name="configuration"></a>Configuratie

1. Kies in de vervolg keuzelijst **juiste stream** de gegevens stroom waarmee u wilt samen voegen.
1. Het **jointype** selecteren
1. Kies welke sleutel kolommen u wilt vergelijken voor de voor waarde voor samen voegen. De gegevens stroom zoekt standaard naar gelijkheid tussen één kolom in elke stroom. Als u wilt vergelijken via een berekende waarde, houdt u de muis aanwijzer boven de vervolg keuzelijst kolom en selecteert u **berekende kolom**.

![Trans formatie koppelen](media/data-flow/join.png "Koppelen")

## <a name="optimizing-join-performance"></a>Prestaties van deelname optimaliseren

In tegens telling tot merge-koppeling in hulpprogram ma's als SSIS is de koppelings transformatie geen verplichte samenvoeg bewerking samen voegen. De koppelings sleutels hoeven niet te worden gesorteerd. De koppelings bewerking vindt plaats op basis van de optimale koppelings bewerking in Spark, broadcast of toewijzing aan de kaart zijde.

![Trans formatie optimaliseren](media/data-flow/joinoptimize.png "Deelname aan optimalisatie")

Als een of beide gegevens stromen in het geheugen van het worker-knoop punt passen, optimaliseert u de prestaties verder door **broadcast** in te scha kelen op het tabblad Optimize. U kunt uw gegevens ook opnieuw partitioneren op basis van de koppelings bewerking zodat deze beter in het geheugen wordt aangepast per werk nemer.

## <a name="self-join"></a>Self-deelname

Als u een gegevens stroom met zichzelf wilt samen voegen, maakt u een alias voor een bestaande stroom met een SELECT trans formatie. Maak een nieuwe vertakking door te klikken op het plus pictogram naast een trans formatie en **nieuwe vertakking**te selecteren. Voeg een SELECT trans formatie toe om de oorspronkelijke stroom te aliassen. Voeg een koppelings transformatie toe en kies de oorspronkelijke stroom als de **linker stroom** en de Selecteer trans formatie als de **juiste stroom**.

![Self-deelname](media/data-flow/selfjoin.png "Self-deelname")

## <a name="testing-join-conditions"></a>Voor waarden voor samen voegen testen

Wanneer u de trans formaties van de koppeling met de preview-versie van gegevens in de foutopsporingsmodus test, moet u een kleine set bekende gegevens gebruiken. Wanneer u rijen van een grote gegevensset bemonstert, kunt u niet voors pellen welke rijen en sleutels worden gelezen voor testen. Het resultaat is niet-deterministisch, wat betekent dat uw samenvoegings voorwaarden geen overeenkomsten kunnen retour neren.

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

### <a name="inner-join-example"></a>Voor beeld van inner join

Het onderstaande voor beeld is een koppelings transformatie met de naam `JoinMatchedData` die links stream `TripData` en Right stream-`TripFare`gebruikt.  De voor waarde voor samen voegen is de expressie `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` die waar retourneert als de kolommen `hack_license`, `medallion`, `vendor_id`en `pickup_datetime` in elke stroom overeenkomen. De `joinType` is `'inner'`. Het inschakelen van de uitzending in alleen de linker stroom is `broadcast` waarde `'left'`.

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Voor beeld van koppeling](media/data-flow/join-script1.png "Voor beeld van koppeling")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

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

### <a name="cross-join-example"></a>Voor beeld van cross-koppeling

Het onderstaande voor beeld is een koppelings transformatie met de naam `CartesianProduct` die links stream `TripData` en Right stream-`TripFare`gebruikt. Deze trans formatie heeft twee stromen en retourneert een Cartesisch product van de rijen. De voor waarde voor samen voegen is `true()`, omdat er een volledig Cartesisch product wordt uitgevoerd. De `joinType` is `cross`. Het inschakelen van de uitzending in alleen de linker stroom is `broadcast` waarde `'left'`.

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Voor beeld van koppeling](media/data-flow/join-script2.png "Voor beeld van koppeling")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
TripData, TripFare
    join(
        true(),
        joinType:'cross',
        broadcast: 'left'
    )~> CartesianProduct
```

## <a name="next-steps"></a>Volgende stappen

Nadat u gegevens hebt toegevoegd, maakt u een [afgeleide kolom](data-flow-derived-column.md) en [sinkt](data-flow-sink.md) u uw gegevens naar een doel gegevens archief.
