---
title: Trans formatie van de draai tabel van de gegevens stroom koppelen
description: Trans formatie van gegevens stroom Azure Data Factory koppelen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: e7c0a4cd6e44994c4b002fcc2e5fde441cf22283
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541648"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Trans formatie Azure Data Factory draai tabel

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik UNPIVOT in ADF-toewijzings gegevens stroom als een manier om een ongebruikelijke gegevensset in te scha kelen in een meer genormaliseerde versie door waarden uit meerdere kolommen in één record uit te breiden naar meerdere records met dezelfde waarden in één kolom.

![Draai tabel transformatie](media/data-flow/unpivot1.png "Draai tabel opties 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Groep opheffen op

![Draai tabel transformatie](media/data-flow/unpivot5.png "Draai tabel opties 2")

Stel eerst de kolommen in waarop u wilt groeperen voor de aggregatie van de draai tabel. Stel een of meer kolommen in voor het opheffen van de groep met het plus teken naast de kolom lijst.

## <a name="unpivot-key"></a>Depivot-sleutel

![Draai tabel transformatie](media/data-flow/unpivot6.png "Draai tabel opties 3")

De Pivot-toets is de kolom die ADF van rij naar kolom draait. Standaard wordt elke unieke waarde in de gegevensset voor dit veld naar een kolom gedraaid. U kunt eventueel ook de waarden opgeven uit de gegevensset die u wilt draaien naar kolom waarden.

## <a name="unpivoted-columns"></a>Niet-gedraaide kolommen

![Draai tabel transformatie](media/data-flow//unpivot7.png "Draai tabel opties 4")

Kies ten slotte de aggregatie die u wilt gebruiken voor de gedraaide waarden en hoe u wilt dat de kolommen worden weer gegeven in de nieuwe uitvoer projectie van de trans formatie.

Beschrijving U kunt een naamgevings patroon met een voor voegsel, Midden en achtervoegsel instellen dat moet worden toegevoegd aan elke nieuwe kolom naam uit de rijwaarden.

Bijvoorbeeld: het draaien van ' verkoop ' op ' regio ' zou u alleen nieuwe kolom waarden van elke verkoop waarde geven. Bijvoorbeeld: ' 25 ', ' 50 ', ' 1000 ',... Als u echter de waarde voor voor voegsel van ' verkoop ' instelt, wordt ' omzet ' voorafgegaan door de waarden.

![Afbeelding van de kolommen io, Vendor en fruit voor en na een UniPivot-trans formatie met de kolom fruit als de UniPivot-sleutel.](media/data-flow/unpivot3.png)

Als u de rang schikking van de kolom instelt op normaal, worden alle gedraaide kolommen gegroepeerd met de geaggregeerde waarden. Als u de rang schikking van de kolommen instelt op ' zijdelings ', wordt er een alternatief voor de kolom en waarde.

![Draai tabel transformatie](media/data-flow//unpivot7.png "Draai tabel opties 5")

De laatste niet-gedraaide gegevens resultaatset bevat de kolom totalen die nu in afzonderlijke rijwaarden zijn weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Gebruik de [draai transformatie](data-flow-pivot.md) om rijen naar kolommen te draaien.
