---
title: Draaitransformatie in kaartgegevensstroom
description: Gegevens draaien van rijen naar kolommen met azure data factory-toewijzing van gegevensstroomdraaitransformatie
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686428"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Draaitransformatie in kaartgegevensstroom


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de draaitransformatie om meerdere kolommen te maken op basis van de unieke rijwaarden van één kolom. Pivot is een aggregatietransformatie waarbij u groep op kolommen selecteert en draaikolommen genereert met behulp van [aggregaatfuncties](data-flow-expression-functions.md#aggregate-functions).

## <a name="configuration"></a>Configuratie

De draaitransformatie vereist drie verschillende ingangen: groeperen op kolommen, de draaitoets en het genereren van de gedraaide kolommen

### <a name="group-by"></a>Groeperen op

![Groeperen op opties](media/data-flow/pivot2.png "[Groeperen op opties")

Selecteer welke kolommen de gedraaide kolommen wilt samenvoegen. De uitvoergegevens groeperen alle rijen met dezelfde groep op waarden in één rij. De aggregatie in de draaikolom vindt plaats in elke groep.

Deze sectie is optioneel. Als er geen groep per kolom is geselecteerd, wordt de volledige gegevensstroom samengevoegd en wordt slechts één rij uitgevoerd.

### <a name="pivot-key"></a>Draaitoets

![Draaitoets](media/data-flow/pivot3.png "Draaitoets")

De draaitoets is de kolom waarvan de rijwaarden in nieuwe kolommen worden gedraaid. Standaard maakt de draaitransformatie een nieuwe kolom voor elke unieke rijwaarde.

In de sectie met het label **Waarde**u specifieke rijwaarden invoeren die moeten worden gedraaid. Alleen de rijwaarden die in deze sectie zijn ingevoerd, worden gedraaid. Als **u null-waarde** inschakelt, wordt een draaikolom gemaakt voor de null-waarden in de kolom.

### <a name="pivoted-columns"></a>Gedraaide kolommen

![Gedraaide kolommen](media/data-flow/pivot4.png "Gedraaide kolommen")

Geneer voor elke unieke draaisleutelwaarde die een kolom wordt, een geaggregeerde rijwaarde voor elke groep. U meerdere kolommen per draaitoets maken. Elke draaikolom moet ten minste één [aggregaatfunctie](data-flow-expression-functions.md#aggregate-functions)bevatten .

**Kolomnaampatroon:** Selecteer hoe u de kolomnaam van elke draaikolom opmaakt. De uitvoerde kolomnaam is een combinatie van de waarde van de draaitoets, kolomvoorvoegsel en optioneel voorvoegsel, volstaan, middelste tekens. 

**Kolomregeling:** Als u meer dan één draaikolom per draaitoets genereert, kiest u hoe u de kolommen wilt bestellen. 

**Kolomvoorvoegsel:** Als u meer dan één draaikolom per draaisleutel genereert, voert u voor elke kolom een kolomvoorvoegsel in. Deze instelling is optioneel als u slechts één draaikolom hebt.

## <a name="help-graphic"></a>Help-afbeelding

De onderstaande help-afbeelding laat zien hoe de verschillende draaicomponenten met elkaar omgaan

![Help-afbeeldingen van Pivot](media/data-flow/pivot5.png "Knop Pivot Help")

## <a name="pivot-metadata"></a>Pivot-metagegevens

Als er geen waarden zijn opgegeven in de configuratie van de draaitoets, worden de gedraaide kolommen dynamisch gegenereerd tijdens uitvoering. Het aantal draaikolommen is gelijk aan het aantal unieke draaisleutelwaarden vermenigvuldigd met het aantal draaikolommen. Aangezien dit een veranderend getal kan zijn, geeft de UX de kolommetagegevens niet weer op het tabblad **Inspect** en is er geen kolompropagatie. Als u deze kolommen wilt transformeren, gebruikt u de mogelijkheden voor [kolompatroon](concepts-data-flow-column-pattern.md) van het toewijzen van gegevensstroom. 

Als specifieke draaisleutelwaarden zijn ingesteld, worden de draaipuntenkolommen weergegeven in de metagegevens.e kolomnamen zijn voor u beschikbaar in de toewijzing van Inspect en Sink.

### <a name="generate-metadata-from-drifted-columns"></a>Metagegevens genereren uit zwevende kolommen

Pivot genereert dynamisch nieuwe kolomnamen op basis van rijwaarden. U deze nieuwe kolommen toevoegen aan de metagegevens waarnaar later in uw gegevensstroom kan worden verwezen. Gebruik hiervoor de [snelle](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) actie van de kaart in het voorbeeld van gegevens. 

![Kolommen draaien](media/data-flow/newpivot1.png "Zwevende draaikolommen toewijzen")

### <a name="sinking-pivoted-columns"></a>Dalende draaikolommen

Hoewel draaikolommen dynamisch zijn, kunnen ze nog steeds worden geschreven in uw doelgegevensarchief. Schemadrift **toestaan in** de sink-instellingen inschakelen. Hiermee u kolommen schrijven die niet zijn opgenomen in metagegevens. uw kolommetagegevens, maar met de optie schemadrift u de gegevens landen.

### <a name="rejoin-original-fields"></a>Opnieuw deelnemen aan oorspronkelijke velden

De draaitransformatie projecteert de groep alleen op en wordt gepivoteerd. Als u wilt dat uw uitvoergegevens andere invoerkolommen bevatten, gebruikt u een [zelfjoinpatroon.](data-flow-join.md#self-join)

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Voorbeeld

De schermen in de configuratiesectie hebben het volgende script voor gegevensstromen:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Volgende stappen

Probeer de [draaibare transformatie om](data-flow-unpivot.md) kolomwaarden om te zetten in rijwaarden. 
