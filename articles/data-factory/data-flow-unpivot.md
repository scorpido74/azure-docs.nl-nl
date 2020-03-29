---
title: Gegevensstroom ondraaiende transformatie toewijzen
description: Azure Data Factory mapping dataflow Unpivot Transformation Azure Data Factory mapping dataflow Unpivot Transformation Azure Data Factory mapping dataflow Unpivot Transformation Azure Data
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: b207012335e68d389a07b54408e840dbb305a30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930141"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory Onpivot Transformatie



Gebruik Unpivot in ADF-toewijzingsgegevensstroom als een manier om een ongenormaliseerde gegevensset om te zetten in een meer genormaliseerde versie door waarden uit meerdere kolommen in één record uit te vouwen in meerdere records met dezelfde waarden in één kolom.

![Draaibare transformatie ongedaan maken](media/data-flow/unpivot1.png "Draaiopties ongedaan maken 1")

## <a name="ungroup-by"></a>Groepeer de groep uit

![Draaibare transformatie ongedaan maken](media/data-flow/unpivot5.png "Draaiopties 2 ongedaan maken")

Stel eerst de kolommen in die u wilt groeperen voor uw spilaggregatie. Stel een of meer kolommen in voor het opheffen van de groepering met het teken + naast de kolomlijst.

## <a name="unpivot-key"></a>Draaibare toets de spil punt ontdraaien

![Draaibare transformatie ongedaan maken](media/data-flow/unpivot6.png "Draaiopties ongedaan maken 3")

De draaisleutel is de kolom die ADF van rij naar kolom draait. Standaard draait elke unieke waarde in de gegevensset voor dit veld naar een kolom. U echter optioneel de waarden uit de gegevensset invoeren die u wilt draaien naar kolomwaarden.

## <a name="unpivoted-columns"></a>Niet-gedraaide kolommen

![Draaibare transformatie ongedaan maken](media/data-flow//unpivot7.png "Draaiopties ongedaan maken 4")

Kies ten slotte de aggregatie die u wilt gebruiken voor de draaibare waarden en hoe u de kolommen wilt weergeven in de nieuwe uitvoerprojectie van de transformatie.

(Optioneel) U een naamgevingspatroon instellen met een voorvoegsel, midden en achtervoegsel dat moet worden toegevoegd aan elke nieuwe kolomnaam van de rijwaarden.

Als u bijvoorbeeld 'Verkoop' door 'Regio' draait, u eenvoudig nieuwe kolomwaarden van elke verkoopwaarde genereren. Bijvoorbeeld: "25", "50", "1000", ... Als u echter een voorvoegselwaarde van 'Verkoop' instelt, wordt 'Verkoop' vooraf aan de waarden bevestigd.

<img src="media/data-flow/unpivot3.png" width="400">

Als u de kolomregeling instelt op 'Normaal' worden alle gedraaide kolommen gegroepeerd met hun samengevoegde waarden. Als u de kolommenregeling instelt op 'Lateral' wordt afwisselend kolom en waarde.

![Draaibare transformatie ongedaan maken](media/data-flow//unpivot7.png "Draaiopties ongedaan maken 5")

De uiteindelijke niet-gedraaide gegevensresultatenset toont de kolomtotalen die nu niet zijn gedraaid in afzonderlijke rijwaarden.

## <a name="next-steps"></a>Volgende stappen

Gebruik de [draaitransformatie](data-flow-pivot.md) om rijen naar kolommen te draaien.
