---
title: Kolompatronen in de toewijzingsgegevensstroom van Azure Data Factory
description: Algemene gegevenstransformatiepatronen maken met kolompatronen in toewijzingsgegevensstromen van Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: bfb4eeef482c4944e75b7805642bc93c23195208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065522"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Kolompatronen gebruiken in de gegevensstroom van toewijzingen

Met verschillende transformaties van de toewijzingsgegevensstroom u sjabloonkolommen verwijzen op basis van patronen in plaats van hardgecodeerde kolomnamen. Deze matching staat bekend als *kolompatronen.* U patronen definiëren die overeenkomen met kolommen op basis van naam, gegevenstype, stream of positie in plaats van exacte veldnamen te vereisen. Er zijn twee scenario's waarin kolompatronen nuttig zijn:

* Als binnenkomende bronvelden vaak veranderen, zoals het geval van het wijzigen van kolommen in tekstbestanden of NoSQL-databases. Dit scenario staat bekend als [schemadrift](concepts-data-flow-schema-drift.md).
* Als u een veelgebruikte bewerking wilt uitvoeren op een grote groep kolommen. Bijvoorbeeld, willen elke kolom die 'totaal' in de kolom naam heeft cast in een dubbele.

Kolompatronen zijn momenteel beschikbaar in de afgeleide kolom-, aggregaat-, selectie- en sinktransformaties.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Kolompatronen in afgeleide kolom en aggregaat

Als u een kolompatroon wilt toevoegen in een afgeleide kolom of het tabblad Aggregaten van een samengevoegde transformatie, klikt u op het pluspictogram rechts van een bestaande kolom. Selecteer **Kolompatroon toevoegen**. 

![kolompatronen](media/data-flow/columnpattern.png "Kolompatronen")

Gebruik de [opbouwfunctie voor expressies](concepts-data-flow-expression-builder.md) om de wedstrijdvoorwaarde in te voeren. Maak een booleaanse expressie die `name` `type`overeenkomt `stream`met `position` kolommen op basis van de kolom , en van de kolom. Het patroon heeft invloed op elke kolom, gedreven of gedefinieerd, waarbij de voorwaarde true retourneert.

In de twee expressievakken onder de overeenkomstvoorwaarde worden de nieuwe namen en waarden van de betreffende kolommen opgegeven. Gebruiken `$$` om te verwijzen naar de bestaande waarde van het overeenkomende veld. In het vak met de linkerexpressie wordt de naam gedefinieerd en het rechterexpressievak definieert de waarde.

![kolompatronen](media/data-flow/columnpattern2.png "Kolompatronen")

Het bovenstaande kolompatroon komt overeen met elke kolom van het type dubbel en maakt één aggregaatkolom per wedstrijd. De naam van de nieuwe kolom is de naam van de overeenkomende kolom die is gekoppeld aan '_total'. De waarde van de nieuwe kolom is de afgeronde, geaggregeerde som van de bestaande dubbele waarde.

Als u wilt controleren of de gegevensstatus correct zijn, u het uitvoerschema van gedefinieerde kolommen valideren op het tabblad **Inspecteren** of een momentopname van de gegevens krijgen op het tabblad **Gegevensvoorbeeld.** 

![kolompatronen](media/data-flow/columnpattern3.png "Kolompatronen")

## <a name="rule-based-mapping-in-select-and-sink"></a>Toewijzing op basis van regels in selecteren en zinken

Wanneer u kolommen in bron toewijzingen en transformaties selecteert, u vaste toewijzingen of op regels gebaseerde toewijzingen toevoegen. Overeenkomen op `name`basis `type` `stream`van `position` de , , , en van kolommen. U elke combinatie van vaste en op regels gebaseerde toewijzingen hebben. Standaard worden alle projecties met meer dan 50 kolommen standaard gekoppeld aan een op regels gebaseerde toewijzing die overeenkomt met elke kolom en de ingevoerde naam wordt uitgevoerd. 

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

### <a name="rule-based-hierarchies"></a>Hiërarchieën op basis van regels

Als uw gedefinieerde projectie een hiërarchie heeft, u regeltoewijzing gebruiken om de subkolommen van de hiërarchieën in kaart te brengen. Geef een overeenkomende voorwaarde op en de complexe kolom waarvan u de subkolommen wilt toewijzen. Elke overeenkomende subkolom wordt uitgevoerd met de regel 'Name as', die aan de rechterkant is opgegeven.

![op regels gebaseerde toewijzing](media/data-flow/rule-based-hierarchy.png "Toewijzing op basis van regels")

Het bovenstaande voorbeeld komt overeen `a`op alle subkolommen van complexe kolom . `a`bevat twee `b` subkolommen en `c`. Het uitvoerschema bevat `b` twee `c` kolommen en als de `$$`voorwaarde 'Naam als' is .

## <a name="pattern-matching-expression-values"></a>Patroon overeenkomende expressiewaarden.

* `$$`vertaalt naar de naam of waarde van elke wedstrijd tijdens de looptijd
* `name`vertegenwoordigt de naam van elke binnenkomende kolom
* `type`vertegenwoordigt het gegevenstype van elke binnenkomende kolom
* `stream`vertegenwoordigt de naam die aan elke stream is gekoppeld of transformatie in uw stroom
* `position`is de ordinale positie van kolommen in uw gegevensstroom

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de taal van de [gegevensstroomexpressie](data-flow-expression-functions.md) voor gegevenstransformaties
* Kolompatronen gebruiken in de [sinktransformatie](data-flow-sink.md) en [transformatie selecteren](data-flow-select.md) met op regels gebaseerde toewijzing
