---
title: Trans formatie van gegevens stroom Azure Data Factory koppelen
description: Trans formatie van gegevens stroom Azure Data Factory koppelen
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 28fedf96ec178aae0615129421bdae7721a66105
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029942"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Trans formatie Azure Data Factory draai tabel



Gebruik UNPIVOT in ADF-toewijzings gegevens stroom als een manier om een ongebruikelijke gegevensset in te scha kelen in een meer genormaliseerde versie door waarden uit meerdere kolommen in één record uit te breiden naar meerdere records met dezelfde waarden in één kolom.

![Draai tabel transformatie]ongedraaid,(media/data-flow/unpivot1.png "opties 1")

## <a name="ungroup-by"></a>Groep opheffen op

![Draai tabel transformatie]ongedraaid,(media/data-flow/unpivot5.png "opties 2")

Stel eerst de kolommen in waarop u wilt groeperen voor de aggregatie van de draai tabel. Stel een of meer kolommen in voor het opheffen van de groep met het plus teken naast de kolom lijst.

## <a name="unpivot-key"></a>Depivot-sleutel

![Draai tabel transformatie]uitdraaien(media/data-flow/unpivot6.png "opties 3")

De Pivot-toets is de kolom die ADF van rij naar kolom draait. Standaard wordt elke unieke waarde in de gegevensset voor dit veld naar een kolom gedraaid. U kunt eventueel ook de waarden opgeven uit de gegevensset die u wilt draaien naar kolom waarden.

## <a name="unpivoted-columns"></a>Niet-gedraaide kolommen

![Draai tabel transformatie](media/data-flow//unpivot7.png "UNPIVOT-opties 4")

Kies ten slotte de aggregatie die u wilt gebruiken voor de gedraaide waarden en hoe u wilt dat de kolommen worden weer gegeven in de nieuwe uitvoer projectie van de trans formatie.

Beschrijving U kunt een naamgevings patroon met een voor voegsel, Midden en achtervoegsel instellen dat moet worden toegevoegd aan elke nieuwe kolom naam uit de rijwaarden.

Bijvoorbeeld: het draaien van ' verkoop ' op ' regio ' zou u alleen nieuwe kolom waarden van elke verkoop waarde geven. Bijvoorbeeld: "25", "50", "1000",... Als u echter de waarde voor voor voegsel van ' verkoop ' instelt, wordt ' omzet ' voorafgegaan door de waarden.

<img src="media/data-flow/unpivot3.png" width="400">

Als u de rang schikking van de kolom instelt op normaal, worden alle gedraaide kolommen gegroepeerd met de geaggregeerde waarden. Als u de rang schikking van de kolommen instelt op ' zijdelings ', wordt er een alternatief voor de kolom en waarde.

![Draai tabel transformatie]ondraaibaar(media/data-flow//unpivot7.png "Opties 5")

De laatste niet-gedraaide gegevens resultaatset bevat de kolom totalen die nu in afzonderlijke rijwaarden zijn weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Gebruik de [draai transformatie](data-flow-pivot.md) om rijen naar kolommen te draaien.
