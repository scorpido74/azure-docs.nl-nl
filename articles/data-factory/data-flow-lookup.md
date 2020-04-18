---
title: Opzoektransformatie in kaartgegevensstroom
description: Referentiegegevens van een andere bron met behulp van de opzoektransformatie in de toewijzingsgegevensstroom.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 24fe11610d2a91fcdb0f09b8e45ea6ff4b81bd70
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606385"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Opzoektransformatie in kaartgegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de opzoektransformatie om gegevens van een andere bron in een gegevensstroomstroom te referentieeren. De opzoektransformatie voegt kolommen toe van gekoppelde gegevens tot uw brongegevens.

Een lookup transformatie is vergelijkbaar met een linker buitenste join. Alle rijen uit de primaire stroom zullen bestaan in de uitvoerstroom met extra kolommen uit de opzoekstream. 

## <a name="configuration"></a>Configuratie

![Opzoektransformatie](media/data-flow/lookup1.png "Opzoeken")

**Primaire stream:** De binnenkomende stroom van gegevens. Deze stroom is gelijk aan de linkerkant van een join.

**Opzoekstroom:** De gegevens die zijn toegevoegd aan de primaire stroom. Welke gegevens worden toegevoegd, wordt bepaald door de opzoekvoorwaarden. Deze stroom is gelijk aan de rechterkant van een join.

**Overeenkomen met meerdere rijen:** Als dit is ingeschakeld, wordt een rij met meerdere overeenkomsten in de primaire stream meerdere rijen teruggekeerd. Anders wordt slechts één rij geretourneerd op basis van de voorwaarde 'Match on'.

**Wedstrijd op:** Alleen zichtbaar als 'Meerdere rijen matchen' is ingeschakeld. Kies of u wilt overeenkomen op een rij, de eerste wedstrijd of de laatste wedstrijd. Elke rij wordt aanbevolen omdat deze het snelst wordt uitgevoerd. Als de eerste rij of de laatste rij is geselecteerd, moet u sorteervoorwaarden opgeven.

**Opzoekvoorwaarden:** Kies op welke kolommen u wilt overeenkomen. Als aan de gelijkheidsvoorwaarde is voldaan, worden de rijen als een overeenkomst beschouwd. Plaats de boventoon en selecteer 'Berekende kolom' om een waarde te extraheren met behulp van de taal van de [gegevensstroomexpressie](data-flow-expression-functions.md).

De opzoektransformatie ondersteunt alleen gelijkheidsovereenkomsten. Als u de opzoekexpressie wilt aanpassen aan andere operatoren, zoals groter dan, wordt aanbevolen om een kruislid te gebruiken [bij de join-transformatie.](data-flow-join.md#custom-cross-join) Een cross join voorkomt eventuele cartesiaanse productfouten bij de uitvoering.

Alle kolommen van beide streams zijn opgenomen in de uitvoergegevens. Als u dubbele of ongewenste kolommen wilt laten vallen, voegt u een [geselecteerde transformatie](data-flow-select.md) toe na uw opzoektransformatie. Kolommen kunnen ook worden verwijderd of hernoemd in een gootsteentransformatie.

## <a name="analyzing-matched-rows"></a>Overeenkomende rijen analyseren

Na uw opzoektransformatie `isMatch()` kan de functie worden gebruikt om te zien of de opzoeking overeenkomt met afzonderlijke rijen.

![Opzoekpatroon](media/data-flow/lookup111.png "Opzoekpatroon")

Een voorbeeld van dit patroon is het gebruik `isMatch()` van de voorwaardelijke gesplitste transformatie om te splitsen op de functie. In het bovenstaande voorbeeld gaan overeenkomende rijen door de bovenste ```NoMatch``` stream en stromen niet-overeenkomende rijen door de stream.

## <a name="testing-lookup-conditions"></a>Zoekomstandigheden testen

Gebruik bij het testen van de opzoektransformatie met gegevensvoorbeeld in de foutopsporingsmodus een kleine set bekende gegevens. Wanneer u rijen uit een grote gegevensset bemonstert, u niet voorspellen welke rijen en sleutels worden gelezen voor het testen. Het resultaat is niet-deterministisch, wat betekent dat uw join voorwaarden mogen geen wedstrijden terug.

## <a name="broadcast-optimization"></a>Broadcast optimalisatie

In Azure Data Factory worden gegevensstromen uitgevoerd in geschaalde Spark-omgevingen. Als uw gegevensset past in de geheugenruimte van het werknemersknooppunt, kunnen uw opzoekprestaties worden geoptimaliseerd door uitzendingen in te schakelen.

![Uitzending Word lid](media/data-flow/broadcast.png "Uitzending Word lid")

Door uitzending in te schakelen wordt de hele gegevensset in het geheugen gepusht. Voor kleinere gegevenssets met slechts een paar duizend rijen kan het uitzenden uw opzoekprestaties sterk verbeteren. Voor grote gegevenssets kan deze optie leiden tot een uitzondering buiten het geheugen.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Voorbeeld

![Opzoektransformatie](media/data-flow/lookup-dsl-example.png "Opzoeken")

Het gegevensstroomscript voor de bovenstaande opzoekconfiguratie bevindt zich in het onderstaande codefragment.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
Volgende stappen

* De [join](data-flow-join.md) en [bestaande](data-flow-exists.md) transformaties nemen beide meerdere streamingangen op
* Een [voorwaardelijke gesplitste transformatie](data-flow-conditional-split.md) gebruiken om ```isMatch()``` rijen te splitsen op overeenkomende en niet-overeenkomende waarden
