---
title: Kolom patronen voor het toewijzen van gegevens stromen Azure Data Factory
description: Gegeneraliseerde gegevens transformatie patronen maken met behulp van Azure Data Factory kolom patronen in de toewijzing van gegevens stromen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 4df831c1329ab13f19e6ecf979e404d4a90e5f72
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219759"
---
# <a name="mapping-data-flows-column-patterns"></a>Kolom patronen voor gegevens stromen toewijzen

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Verschillende Azure Data Factory gegevensstroom transformaties ondersteunen het idee van ' column patronen ', zodat u sjabloon kolommen kunt maken op basis van patronen in plaats van in code vastgelegde kolom namen. U kunt deze functie in de opbouw functie voor expressies gebruiken om patronen te definiëren die overeenkomen met kolommen voor trans formatie in plaats van exacte, specifieke veld namen te vereisen. Patronen zijn nuttig als inkomende bron velden vaak veranderen, met name in het geval van het wijzigen van kolommen in tekst bestanden of NoSQL-data bases. Dit probleem wordt ook wel ' schema drift ' genoemd.

De verwerking van het flexibele schema is momenteel gevonden in de afgeleide kolom en de geaggregeerde trans formaties en de trans formaties Select en Sink als toewijzing op basis van een regel.

![kolom patronen](media/data-flow/columnpattern2.png "Kolom patronen")

## <a name="column-patterns"></a>Kolompatronen
Kolom patronen zijn handig voor het afhandelen van zowel schema-en algemene scenario's. Het is een goed idee om de kolom naam niet volledig te kennen. U kunt overeenkomen met de kolom naam en kolom gegevens type en een expressie maken voor trans formatie waarmee deze bewerking wordt uitgevoerd voor elk veld in de gegevens stroom dat `name` overeenkomt met uw  &  `type` patronen.

Wanneer u een expressie toevoegt aan een trans formatie die patronen accepteert, kiest u kolom patroon toevoegen. Kolom patronen kunnen schema-drift kolom matching-patronen.

Bij het bouwen van sjabloon kolom patronen `$$` gebruikt u in de expressie om een verwijzing naar elk overeenkomend veld van de invoer gegevens stroom weer te geven.

Als u een van de regex-functies van Expression Builder wilt gebruiken, kunt u vervolgens $1, $2, $3 gebruiken... om te verwijzen naar de Subpatronen die overeenkomen met uw regex-expressie.

Een voor beeld van een scenario met een kolom patroon is het gebruik van SUM met een reeks inkomende velden. De cumulatieve som-berekeningen bevinden zich in de geaggregeerde trans formatie. U kunt vervolgens SUM gebruiken bij elke overeenkomst met veld typen die ' Integer ' overeenkomen en vervolgens $ $ gebruiken om te verwijzen naar elke overeenkomst in uw expressie.

## <a name="match-columns"></a>Kolommen vergelijken
![type kolom patroon](media/data-flow/pattern2.png "Patroon typen")

Als u patronen wilt bouwen op basis van kolommen, kunt u overeenkomen met de kolom naam, het type, de stroom of de positie en een combi natie hiervan gebruiken met expressie functies en reguliere expressies.

![kolom positie](media/data-flow/position.png "Kolom positie")

## <a name="rule-based-mapping"></a>Toewijzing op basis van een regel
Bij het toewijzen van kolommen in de bron en het selecteren van trans formaties, hebt u de mogelijkheid om "vaste toewijzing" of "op regels gebaseerde toewijzing" te kiezen. Wanneer u het schema van uw gegevens kent en wilt verwachten dat er specifieke kolommen van de bron-gegevensset worden gevonden die altijd overeenkomen met specifieke statische namen, kunt u vaste toewijzing gebruiken. Maar wanneer u met flexibele schema's werkt, gebruikt u toewijzing op basis van een regel. U kunt een patroon overeenkomst samen stellen met de regels die hierboven worden beschreven.

![toewijzing op basis van een regel](media/data-flow/rule2.png "Toewijzing op basis van een regel")

Bouw uw regels met de opbouw functie voor expressies. Uw expressies retour neren een Booleaanse waarde naar match columns (true) of kolommen uitsluiten (false).

## <a name="pattern-matching-special-columns"></a>Patroon overeenkomende speciale kolommen

* `$$`wordt omgezet naar de naam van elk match tijdens de ontwerp fase in de foutopsporingsmodus en tijdens de uitvoering.
* `name`vertegenwoordigt de naam van elke binnenkomende kolom
* `type`vertegenwoordigt het gegevens type van elke binnenkomende kolom
* `stream`vertegenwoordigt de naam die is gekoppeld aan elke stroom of trans formatie in uw stroom
* `position`is het Volg nummer van de kolommen in uw gegevens stroom

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [taal](https://aka.ms/dataflowexpressions) van de ADF-toewijzings gegevens voor gegevens transformaties
* Kolom patronen in de [sink-trans formatie](data-flow-sink.md) gebruiken en [trans formatie selecteren](data-flow-select.md) met toewijzing op basis van een regel
