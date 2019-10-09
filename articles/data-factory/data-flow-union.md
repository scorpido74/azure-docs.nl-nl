---
title: Nieuwe vertakkings transformatie Azure Data Factory toewijzings gegevens stroom
description: Nieuwe vertakkings transformatie Azure Data Factory toewijzings gegevens stroom
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 35d5b2250cb5f2f5bd5b3a0073dc2e3c655ceccb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029915"
---
# <a name="mapping-data-flow-union-transformation"></a>Data flow-vakbonds transformatie toewijzen



Samen voegen meerdere gegevens stromen met elkaar samen, met de SQL-Unie van deze stromen als de nieuwe uitvoer van de samenvoeg transformatie. Alle schema's van elke invoer stroom worden gecombineerd in uw gegevens stroom, zonder dat hiervoor een koppelings sleutel nodig is.

U kunt n-aantal streams combi neren in de instellingen tabel door het pictogram ' + ' te selecteren naast elke geconfigureerde rij, inclusief zowel bron gegevens als stromen van bestaande trans formaties in uw gegevens stroom.

Vakbond ![Union-trans formatie](media/data-flow/union.png "")

In dit geval kunt u ongelijksoortige meta gegevens van meerdere bronnen combi neren (in dit voor beeld drie verschillende bron bestanden) en deze combi neren in één stroom:

![Overzicht van Union-trans formatie](media/data-flow/union111.png "Union 1")

U kunt dit doen door extra rijen toe te voegen aan de vakbonds instellingen door alle bron toe te voegen die u wilt toevoegen. Er is geen algemene zoek-of koppelings sleutel nodig:

(media/data-flow/unionsettings.png "Vakbonds instellingen") voor ![instellingen Union-trans formatie]

Als u een SELECT-trans formatie hebt ingesteld na de samen voeging, kunt u de namen van overlappende velden of velden die niet zijn benoemd vanuit bronnen zonder koptekst. Klik op controleren om de kolommen gecombineerde meta gegevens met 132 totaal in dit voor beeld uit drie verschillende bronnen weer te geven:

![Vakbonds transformatie laatste](media/data-flow/union333.png "vakbond 3")

## <a name="name-and-position"></a>Naam en positie

Wanneer u "Union op naam" kiest, wordt elke kolom waarde uit de bijbehorende kolom van elke bron verwijderd, met een nieuw samengevoegd meta gegevens schema.

Als u vereniging op positie kiest, wordt elke kolom waarde uit de oorspronkelijke positie van elke overeenkomende bron verwijderd, wat resulteert in een nieuwe gecombineerde stroom van gegevens, waarbij de gegevens van elke bron aan dezelfde stroom worden toegevoegd:

![](media/data-flow/unionoutput.png "Uitvoer") Union-uitvoer Union

## <a name="next-steps"></a>Volgende stappen

Verken vergelijk bare trans formaties, waaronder [samen voegen](data-flow-join.md) en [bestaan](data-flow-exists.md).
