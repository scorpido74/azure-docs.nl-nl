---
title: Bestaat transformatie in het toewijzen van gegevensstroom
description: Controleren op bestaande rijen met de bestaande transformatie in azure datafabriek toewijzingsgegevensstroom
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: a303c8fa1e23460fb906232eedb6bfb1930b4bc9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606471"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Bestaat transformatie in het toewijzen van gegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De bestaande transformatie is een rijfiltertransformatie die controleert of uw gegevens in een andere bron of stream bestaan. De uitvoerstroom bevat alle rijen in de linkerstream die wel of niet bestaan in de rechterstroom. De bestaande transformatie ```SQL WHERE EXISTS``` is ```SQL WHERE NOT EXISTS```vergelijkbaar met en .

## <a name="configuration"></a>Configuratie

1. Kies welke gegevensstroom u controleert op bestaan in de **vervolgkeuzelijst Rechts stroom.**
1. Geef op of u op zoek bent naar de gegevens die al dan niet bestaan in de **instelling Bestaan.**
1. Selecteer of u een **aangepaste expressie**wilt .
1. Kies welke belangrijke kolommen u wilt vergelijken als uw bestaande voorwaarden. Standaard zoekt de gegevensstroom naar gelijkheid tussen één kolom in elke stream. Als u wilt vergelijken via een berekende waarde, zweeft u boven de kolomvervolgkeuzelijst en selecteert **u Berekende kolom**.

![Bestaande instellingen](media/data-flow/exists.png "bestaat 1")

### <a name="multiple-exists-conditions"></a>Meerdere bestaande voorwaarden

Als u meerdere kolommen van elke stream wilt vergelijken, voegt u een nieuwe voorwaarde toe door op het pluspictogram naast een bestaande rij te klikken. Elke aanvullende voorwaarde wordt vergezeld door een "en" verklaring. Het vergelijken van twee kolommen is hetzelfde als de volgende expressie:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Aangepaste expressie

Als u een vrije-vormexpressie wilt maken die andere operatoren bevat dan 'en' en 'gelijk aan', selecteert u het veld **Aangepaste expressie.** Voer een aangepaste expressie in via de opbouw van gegevensstromenexpressie door op het blauwe vak te klikken.

![Bestaande aangepaste instellingen](media/data-flow/exists1.png "bestaat op maat")

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Voorbeeld

Het onderstaande voorbeeld is `checkForChanges` een transformatie `NameNorm2` met `TypeConversions`de naam die links stream en rechter stream neemt .  De voorwaarde bestaat: `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` de expressie die `EMPID` `Region` true retourneert als zowel de kolommen als de kolommen in elke stream overeenkomen. Als we controleren op `negate` bestaan, is vals. We zijn niet in staat alle uitzendingen in het tabblad optimaliseren dus `broadcast` heeft waarde `'none'`.

In de UX van de Data Factory lijkt deze transformatie op de onderstaande afbeelding:

![Bestaat bijvoorbeeld](media/data-flow/exists-script.png "Bestaat bijvoorbeeld")

Het gegevensstroomscript voor deze transformatie bevindt zich in het onderstaande fragment:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Volgende stappen

Vergelijkbare transformaties zijn [Lookup](data-flow-lookup.md) en [Join](data-flow-join.md).
