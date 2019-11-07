---
title: Er bestaat een trans formatie in Azure Data Factory toewijzing van gegevens stroom
description: Controleren op bestaande rijen met de trans formatie exists in Azure Data Factory gegevens stroom toewijzen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: a477eba246c4ebcfbd32e92f1fd30c301ea1cc5b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676735"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Er bestaat een trans formatie in de toewijzing van gegevens stroom

De exists-trans formatie is een filter transformatie voor rijen waarmee wordt gecontroleerd of uw gegevens zich in een andere bron of stroom bevindt. De uitvoer stroom bevat alle rijen in de linker stroom die ofwel bestaan of niet voor komen in de juiste stroom. De exists-trans formatie is vergelijkbaar met ```SQL WHERE EXISTS``` en ```SQL WHERE NOT EXISTS```.

## <a name="configuration"></a>Configuratie

1. Kies welke gegevens stroom u in de vervolg keuzelijst naar de **juiste stroom** wilt controleren.
1. Geef op of u wilt dat de gegevens bestaan of niet aanwezig zijn in de instelling voor het **bestaande type** .
1. Selecteer of u een **aangepaste expressie**wilt of niet.
1. Kies welke sleutel kolommen u als voor waarden wilt vergelijken. De gegevens stroom zoekt standaard naar gelijkheid tussen één kolom in elke stroom. Als u wilt vergelijken via een berekende waarde, houdt u de muis aanwijzer boven de vervolg keuzelijst kolom en selecteert u **berekende kolom**.

![Instellingen voor exists](media/data-flow/exists.png "bestaat 1")

### <a name="multiple-exists-conditions"></a>Meerdere voor waarden voor bestaan

Als u meerdere kolommen van elke stroom wilt vergelijken, voegt u een nieuwe exists-voor waarde toe door op het plus pictogram naast een bestaande rij te klikken. Elke aanvullende voor waarde wordt gekoppeld door een and-instructie. Vergelijken van twee kolommen is hetzelfde als de volgende expressie:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Aangepaste expressie

Als u een vrije-vorm expressie wilt maken die andere opera tors dan ' en ' en ' is gelijk aan ' bevat, selecteert u het veld **aangepaste expressie** . Voer een aangepaste expressie in via de opbouw functie voor de data flow-expressie door te klikken op het blauwe vak.

![Aangepaste instellingen bestaan](media/data-flow/exists1.png "bestaat aangepast")

## <a name="data-flow-script"></a>Gegevens stroom script

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

Het onderstaande voor beeld is een bestaande trans formatie met de naam `checkForChanges` die links stream `NameNorm2` en Right stream-`TypeConversions`gebruikt.  De exists-voor waarde is de expressie `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` die waar retourneert als de `EMPID` en `Region` kolommen in elke stroom overeenkomen. Wanneer we controleren op bestaan, is `negate` onwaar. Er wordt geen uitzending ingeschakeld op het tabblad Optimize, dus `broadcast` heeft waarde `'none'`.

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Voor beeld van exist](media/data-flow/exists-script.png "Voor beeld van exist")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Volgende stappen

Vergelijk bare trans formaties zijn [Opzoeken](data-flow-lookup.md) en [samen voegen](data-flow-join.md).
