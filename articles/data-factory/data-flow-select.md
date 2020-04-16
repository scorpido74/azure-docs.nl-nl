---
title: Gegevensstroom toewijzen Selecteer transformatie
description: Azure Data Factory mapping data flow Select Transformation
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: 498ce053e8a52897d9087091b92fa51a04e195a4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413257"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Transformatie selecteren in het toewijzen van gegevensstroom

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Gebruik de optietransformatie om kolommen opnieuw te benoemen, neer te zetten of opnieuw te ordenen. Deze transformatie wijzigt geen rijgegevens, maar kiest welke kolommen stroomafwaarts worden gepropageerd. 

In een geselecteerde transformatie kunnen gebruikers vaste toewijzingen opgeven, patronen gebruiken om op regels gebaseerde toewijzing uit te voeren of automatische toewijzing inschakelen. Vaste en op regels gebaseerde toewijzingen kunnen beide worden gebruikt binnen dezelfde select-transformatie. Als een kolom niet overeenkomt met een van de gedefinieerde toewijzingen, wordt deze verwijderd.

## <a name="fixed-mapping"></a>Vaste toewijzing

Als er minder dan 50 kolommen in uw projectie zijn gedefinieerd, hebben alle gedefinieerde kolommen standaard een vaste toewijzing. Een vaste toewijzing neemt een gedefinieerde, binnenkomende kolom en brengt deze een exacte naam toe.

![Vaste toewijzing](media/data-flow/fixedmapping.png "Vaste toewijzing")

> [!NOTE]
> U een zwevende kolom niet toewijzen of de naam wijzigen met een vaste toewijzing

### <a name="mapping-hierarchical-columns"></a>Hiërarchische kolommen toewijzen

Vaste toewijzingen kunnen worden gebruikt om een subkolom van een hiërarchische kolom in kaart te brengen naar een kolom op het hoogste niveau. Als u een gedefinieerde hiërarchie hebt, gebruikt u de kolomvervolgom een subkolom te selecteren. Met de selecte transformatie wordt een nieuwe kolom gemaakt met de waarde en het gegevenstype van de subkolom.

![hiërarchische toewijzing](media/data-flow/select-hierarchy.png "hiërarchische toewijzing")

## <a name="rule-based-mapping"></a>Toewijzing op basis van regels

Als u veel kolommen tegelijk in kaart wilt brengen of zwevende kolommen stroomafwaarts wilt doorgeven, gebruikt u regelgebaseerde toewijzing om uw toewijzingen te definiëren met behulp van kolompatronen. Overeenkomen op `name`basis `type` `stream`van `position` de , , , en van kolommen. U elke combinatie van vaste en op regels gebaseerde toewijzingen hebben. Standaard worden alle projecties met meer dan 50 kolommen standaard gekoppeld aan een op regels gebaseerde toewijzing die overeenkomt met elke kolom en de ingevoerde naam wordt uitgevoerd. 

Als u een toewijzing op basis van regels wilt toevoegen, klikt u op **Toewijzing toevoegen** en selecteert u Toewijzing op basis **van regels**.

![op regels gebaseerde toewijzing](media/data-flow/rule2.png "Toewijzing op basis van regels")

Elke regelgebaseerde toewijzing vereist twee ingangen: de voorwaarde waarop deze moet overeenkomen en wat elke toegewezen kolom moet worden genoemd. Beide waarden worden ingevoerd via de [expressiebouwer](concepts-data-flow-expression-builder.md). Voer in het linkerexpressievak de voorwaarde van de booleaanse wedstrijd in. Geef in het juiste expressievak op aan welke overeenkomende kolom wordt toegewezen.

![op regels gebaseerde toewijzing](media/data-flow/rule-based-mapping.png "Toewijzing op basis van regels")

Gebruik `$$` syntaxis om te verwijzen naar de invoernaam van een overeenkomende kolom. Stel dat een gebruiker de bovenstaande afbeelding als voorbeeld gebruikt, deze wil overeenkomen met alle tekenreekskolommen waarvan de namen korter zijn dan zes tekens. Als er een inkomende kolom is benoemd, `test`wordt de naam van de kolom door de expressie `$$ + '_short'` gewijzigd. `test_short` Als dat de enige toewijzing is die bestaat, worden alle kolommen die niet aan de voorwaarde voldoen, uit de geümeerde gegevens verwijderd.

Patronen komen overeen met zowel zwevende als gedefinieerde kolommen. Als u wilt zien welke gedefinieerde kolommen door een regel in kaart zijn gebracht, klikt u op het pictogram van de bril naast de regel. Controleer uw uitvoer met behulp van een voorbeeld van gegevens.

### <a name="regex-mapping"></a>Regex-toewijzing

Als u op het pictogram neerwaartse chevron klikt, u een regex-toewijzingsvoorwaarde opgeven. Een regex-toewijzingsvoorwaarde komt overeen met alle kolomnamen die overeenkomen met de opgegeven regexvoorwaarde. Dit kan worden gebruikt in combinatie met standaard regelgebaseerde toewijzingen.

![op regels gebaseerde toewijzing](media/data-flow/regex-matching.png "Toewijzing op basis van regels")

Het bovenstaande voorbeeld komt `(r)` overeen met het regex-patroon of een kolomnaam die een kleine letters r bevat. Net als bij standaard regelgebaseerde toewijzing worden alle overeenkomende kolommen `$$` gewijzigd door de voorwaarde aan de rechterkant met behulp van syntaxis.

Als u meerdere regex-overeenkomsten in uw kolomnaam hebt, u verwijzen naar specifieke overeenkomsten met de plaats `$n` waar 'n' naar welke overeenkomst verwijst. '$2' verwijst bijvoorbeeld naar de tweede overeenkomst binnen een kolomnaam.

### <a name="rule-based-hierarchies"></a>Hiërarchieën op basis van regels

Als uw gedefinieerde projectie een hiërarchie heeft, u regeltoewijzing gebruiken om de subkolommen van de hiërarchieën in kaart te brengen. Geef een overeenkomende voorwaarde op en de complexe kolom waarvan u de subkolommen wilt toewijzen. Elke overeenkomende subkolom wordt uitgevoerd met de regel 'Name as', die aan de rechterkant is opgegeven.

![op regels gebaseerde toewijzing](media/data-flow/rule-based-hierarchy.png "Toewijzing op basis van regels")

Het bovenstaande voorbeeld komt overeen `a`op alle subkolommen van complexe kolom . `a`bevat twee `b` subkolommen en `c`. Het uitvoerschema bevat `b` twee `c` kolommen en als de `$$`voorwaarde 'Naam als' is .

### <a name="parameterization"></a>Parameterisatie

U kolomnamen parameteriseren met behulp van regelgebaseerde toewijzing. Gebruik het ```name``` trefwoord om inkomende kolomnamen af te koppelen aan een parameter. Als u bijvoorbeeld een parameter ```mycolumn```voor gegevensstromen hebt, u een regel ```mycolumn```maken die overeenkomt met een kolomnaam die gelijk is aan. U de naam van de overeenkomende kolom wijzigen in een hard-coded tekenreeks zoals 'business key' en deze expliciet verwijzen. In dit voorbeeld is ```name == $mycolumn``` de bijpassende voorwaarde en is de naamvoorwaarde 'business key'. 

## <a name="auto-mapping"></a>Automatisch toewijzen

Wanneer u een geselecteerde transformatie toevoegt, kan **Automatisch toewijzen** worden ingeschakeld door de schuifregelaar Automatisch toewijzen te schakelen. Met automatische toewijzing worden in de geselecteerde transformatie alle binnenkomende kolommen toegewezen, met uitzondering van duplicaten, met dezelfde naam als hun invoer. Dit omvat zwevende kolommen, wat betekent dat de uitvoergegevens kolommen kunnen bevatten die niet in uw schema zijn gedefinieerd. Zie [schemadrift voor](concepts-data-flow-schema-drift.md)meer informatie over afgedreven kolommen.

![Automatisch toewijzen](media/data-flow/automap.png "Automatisch toewijzen")

Met automatische toewijzing aan, zal de selecte transformatie de dubbele instellingen overslaan en een nieuwe alias voor de bestaande kolommen bieden. Aliasing is handig bij het doen van meerdere joins of lookups op dezelfde stream en in zelf-join scenario's. 

## <a name="duplicate-columns"></a>Kolommen dupliceren

Standaard worden bij de geselecteerde transformatie dubbele kolommen in zowel de invoer- als uitvoerprojectie neergedaald. Dubbele invoerkolommen komen vaak van join- en opzoektransformaties waarbij kolomnamen aan elke kant van de join worden gedupliceerd. Dubbele uitvoerkolommen kunnen optreden als u twee verschillende invoerkolommen aan dezelfde naam toegeeft. Kies of u dubbele kolommen wilt neerzetten of doorgeven door het selectievakje te schakelen.

![Duplicaten overslaan](media/data-flow/select-skip-dup.png "Duplicaten overslaan")

## <a name="ordering-of-columns"></a>Bestellen van kolommen

De volgorde van toewijzingen bepaalt de volgorde van de uitvoerkolommen. Als een invoerkolom meerdere keren in kaart wordt gebracht, wordt alleen de eerste toewijzing gehonoreerd. Voor het vallen van dubbele kolommen wordt de eerste wedstrijd bewaard.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Voorbeeld

Hieronder vindt u een voorbeeld van een select mapping en het gegevensstroomscript:

![Scriptvoorbeeld selecteren](media/data-flow/select-script-example.png "Scriptvoorbeeld selecteren")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Volgende stappen
* Nadat u Select hebt gebruikt om de naam, de volgorde en aliaskolommen te wijzigen, gebruikt u de [transformatie Sink](data-flow-sink.md) om uw gegevens in een gegevensarchief te plaatsen.
