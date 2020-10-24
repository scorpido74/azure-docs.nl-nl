---
title: Draai tabel transformatie bij toewijzing van gegevens stroom
description: Trans formatie van gegevens stroom Azure Data Factory koppelen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ea8881adf39a315df7746dbce14dedcbee18ccf6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521047"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Draai tabel transformatie bij toewijzing van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik UNPIVOT in ADF-toewijzings gegevens stroom als een manier om een ongebruikelijke gegevensset in te scha kelen in een meer genormaliseerde versie door waarden uit meerdere kolommen in één record uit te breiden naar meerdere records met dezelfde waarden in één kolom.

![Draai tabel transformatie](media/data-flow/unpivot1.png "Draai tabel opties 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Groep opheffen op

![Draai tabel transformatie](media/data-flow/unpivot5.png "Draai tabel opties 2")

Stel eerst de kolommen in waarop u de groepering wilt opheffen voor uw aggregatie van de draai tabel. Stel een of meer kolommen in voor het opheffen van de groep met het plus teken naast de kolom lijst.

## <a name="unpivot-key"></a>Depivot-sleutel

![Draai tabel transformatie](media/data-flow/unpivot6.png "Draai tabel opties 3")

De depivot-sleutel is de kolom die ADF van kolom naar rij wordt gedraaid. Standaard wordt elke unieke waarde in de gegevensset voor dit veld naar een rij gedraaid. U kunt eventueel ook de waarden opgeven uit de gegevensset die u wilt draaien naar rijgegevens.

## <a name="unpivoted-columns"></a>Niet-gedraaide kolommen

![Draai tabel transformatie](media/data-flow//unpivot7.png "Draai tabel opties 4")

Kies ten slotte de kolom naam voor het opslaan van de waarden voor niet-gedraaide kolommen die worden omgezet in rijen.

Beschrijving U kunt rijen met Null-waarden verwijderen.

Bijvoorbeeld: SumCost is de kolom naam die u hebt gekozen in het voor beeld dat hierboven is gedeeld.

![Afbeelding van de kolommen io, Vendor en fruit voor en na een UniPivot-trans formatie met de kolom fruit als de UniPivot-sleutel.](media/data-flow/unpivot3.png)

Als u de rang schikking van de kolom instelt op ' normaal ', worden alle nieuwe niet-gedraaide kolommen van één waarde gegroepeerd. Als u de rang schikking van de kolommen instelt op ' zijdelings ', worden nieuwe niet-gedraaide kolommen gegroepeerd die zijn gegenereerd op basis van een bestaande kolom.

![Draai tabel transformatie](media/data-flow//unpivot7.png "Draai tabel opties 5")

De laatste niet-gedraaide gegevens resultaatset bevat de kolom totalen die nu in afzonderlijke rijwaarden zijn weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Gebruik de [draai transformatie](data-flow-pivot.md) om rijen naar kolommen te draaien.
