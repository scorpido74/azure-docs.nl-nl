---
title: Kolom patronen in de gegevens stroom van Azure Data Factory toewijzing
description: Gegeneraliseerde gegevens transformatie patronen maken met behulp van kolom patronen in Azure Data Factory gegevens stromen toewijzen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0c9a3c2ef05f4a11933ca7fc81c7c0f87a612293
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789849"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Kolom patronen gebruiken bij het toewijzen van gegevens stroom

Met verschillende toewijzings gegevensstroom transformaties kunt u verwijzen naar sjabloon kolommen op basis van patronen in plaats van in code vastgelegde kolom namen. Deze overeenkomst wordt ook wel *kolom patronen*genoemd. U kunt patronen definiëren die overeenkomen met kolommen op basis van naam, gegevens type, stroom of positie, in plaats van exacte veld namen te vereisen. Er zijn twee scenario's waarin kolom patronen handig zijn:

* Als binnenkomende bron velden vaak veranderen, zoals het wijzigen van kolommen in tekst bestanden of NoSQL-data bases. Dit scenario wordt schema- [drift](concepts-data-flow-schema-drift.md)genoemd.
* Als u een gemeen schappelijke bewerking wilt uitvoeren op een grote groep kolommen. Bijvoorbeeld wilt dat elke kolom met ' Total ' in een dubbele waarde wordt omgezet in de kolom naam.

Kolom patronen zijn momenteel beschikbaar in de afgeleide kolom, aggregatie, selecteren en Sink-trans formaties.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Kolom patronen in afgeleide kolom en aggregatie

Als u een kolom patroon wilt toevoegen in een afgeleide kolom of het tabblad aggregaties van een statistische trans formatie, klikt u op het plus pictogram rechts van een bestaande kolom. Selecteer **kolom patroon toevoegen**. 

![kolom patronen](media/data-flow/columnpattern.png "Kolompatronen")

Gebruik de [expressie Builder](concepts-data-flow-expression-builder.md) om de match-voor waarde in te voeren. Maak een Boole-expressie die overeenkomt met kolommen op basis van de `name`, `type`, `stream`en `position` van de kolom. Het patroon is van invloed op een kolom, die is geplaatste of gedefinieerd, waarbij de voor waarde waar retourneert.

De twee expressie vakken onder de voor waarde match geven de nieuwe namen en waarden van de betrokken kolommen op. Gebruik `$$` om te verwijzen naar de bestaande waarde van het overeenkomende veld. In het vak linker expressie definieert u de naam en de rechter expressie Box definieert de waarde.

![kolom patronen](media/data-flow/columnpattern2.png "Kolompatronen")

Het bovenstaande kolom patroon komt overeen met elke kolom van het type Double en maakt één geaggregeerde kolom per overeenkomst. De naam van de nieuwe kolom is de naam van de overeenkomende kolom die wordt samengevoegd met _Totaal. De waarde van de nieuwe kolom is de afgeronde, geaggregeerde som van de bestaande dubbele waarde.

Als u wilt controleren of uw overeenkomende voor waarde juist is, kunt u het uitvoer schema van gedefinieerde kolommen valideren in het tabblad **controleren** of een moment opname van de gegevens op het tabblad **voor het voor beeld van gegevens** ophalen. 

![kolom patronen](media/data-flow/columnpattern3.png "Kolompatronen")

## <a name="rule-based-mapping-in-select-and-sink"></a>Toewijzing op basis van een regel in SELECT en Sink

Bij het toewijzen van kolommen in de bron en het selecteren van trans formaties, kunt u een vaste toewijzing of op regels gebaseerde toewijzingen toevoegen. Als u het schema van uw gegevens kent en wilt verwachten dat specifieke kolommen van de bron-gegevensset altijd overeenkomen met specifieke statische namen, gebruikt u vaste toewijzing. Als u werkt met flexibele schema's, gebruikt u toewijzing op basis van regels om een patroon overeenkomst te maken op basis van de `name`, `type`, `stream`en `position` kolommen. U kunt een combi natie van vaste en op regels gebaseerde toewijzingen hebben. 

Als u een op een regel gebaseerde toewijzing wilt toevoegen, klikt u op **toewijzing toevoegen** en selecteert u **toewijzing op basis van een regel**.

![toewijzing op basis van een regel](media/data-flow/rule2.png "Toewijzing op basis van een regel")

Voer in het vak linker expressie de voor waarde Boole match in. Geef in het vak rechter expressie op aan welke overeenkomende kolom wordt toegewezen. Gebruik `$$` om te verwijzen naar de bestaande naam van het overeenkomende veld.

Als u op het pictogram met de pijl omlaag klikt, kunt u een regex-toewijzings voorwaarde opgeven.

Klik op het bril pictogram naast een op een regel gebaseerde toewijzing om te zien welke gedefinieerde kolommen worden vergeleken en waaraan ze zijn toegewezen.

![toewijzing op basis van een regel](media/data-flow/rule1.png "Toewijzing op basis van een regel")

In het bovenstaande voor beeld worden twee op regels gebaseerde toewijzingen gemaakt. In de eerste plaats worden alle kolommen met de naam ' Movie ' gebruikt en worden ze toegewezen aan hun bestaande waarden. De tweede regel gebruikt regex om alle kolommen die beginnen met ' Movie ' te vergelijken en ze toe te wijzen aan kolom ' movieId '.

Als uw regel resulteert in meerdere identieke toewijzingen, schakelt u **dubbele invoer overs Laan** of **dubbele uitvoer overs Laan** om duplicaten te voor komen.

## <a name="pattern-matching-expression-values"></a>Patroon waarden die overeenkomen met de expressie.

* `$$` wordt omgezet naar de naam of waarde van elk match tijdens runtime
* `name` staat voor de naam van elke binnenkomende kolom
* `type` staat voor het gegevens type van elke binnenkomende kolom
* `stream` staat voor de naam die is gekoppeld aan elke stroom of trans formatie in uw stroom
* `position` is de volg orde van de kolommen in uw gegevens stroom

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de data flow- [expressie](data-flow-expression-functions.md) van de toewijzings gegevens voor gegevens transformaties
* Kolom patronen in de [sink-trans formatie](data-flow-sink.md) gebruiken en [trans formatie selecteren](data-flow-select.md) met toewijzing op basis van een regel
