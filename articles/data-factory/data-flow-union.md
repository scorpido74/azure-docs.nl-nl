---
title: Transformatie van de unie van gegevensstromen in kaart brengen
description: Azure Data Factory mapping dataflow Nieuwe Branch Transformatie
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 35f6b74f3b6cce5a0af812d4613f9da2f9fbd552
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606269"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Azure Data Factory brengt transformatie van gegevensstroomunie in kaart

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Union zal meerdere gegevensstromen combineren tot één, met de SQL Union van die stromen als de nieuwe output van de transformatie van de Unie. Alle schema's van elke invoerstroom worden binnen uw gegevensstroom gecombineerd, zonder dat u een join-toets hoeft te hebben.

U het n-aantal streams in de instellingentabel combineren door het pictogram '+' naast elke geconfigureerde rij te selecteren, inclusief zowel brongegevens als streams van bestaande transformaties in uw gegevensstroom.

![Transformatie van de Unie](media/data-flow/union.png "Union")

In dit geval u verschillende metagegevens uit meerdere bronnen combineren (in dit voorbeeld drie verschillende bronbestanden) en deze combineren tot één stream:

![Overzicht van de transformatie van de Unie](media/data-flow/union111.png "Unie 1")

Om dit te bereiken, voegt u extra rijen toe in de unieinstellingen door alle bronnen op te nemen die u wilt toevoegen. Er is geen behoefte aan een gemeenschappelijke lookup of join key:

![Instellingen voor unietransformatie](media/data-flow/unionsettings.png "Vakbondsinstellingen")

Als u een Select-transformatie naar uw Unie instelt, u de naam van overlappende velden of velden die niet zijn genoemd uit bronnen zonder koptekst wijzigen. Klik op 'Inspect' om de metagegevens combineren met 132 kolommen in totaal uit drie verschillende bronnen te bekijken:

![Unie transformatie definitief](media/data-flow/union333.png "Unie 3")

## <a name="name-and-position"></a>Naam en positie

Wanneer u 'union by name' kiest, valt elke kolomwaarde in de bijbehorende kolom van elke bron, met een nieuw gekoppeld metagegevensschema.

Als u 'unie per positie' kiest, valt elke kolomwaarde vanuit elke overeenkomende bron in de oorspronkelijke positie, wat resulteert in een nieuwe gecombineerde stroom van gegevens waarbij de gegevens van elke bron aan dezelfde stroom worden toegevoegd:

![Output van de Unie](media/data-flow/unionoutput.png "Uitvoer van de Unie")

## <a name="next-steps"></a>Volgende stappen

Ontdek vergelijkbare transformaties, waaronder [Deelnemen](data-flow-join.md) en [bestaan.](data-flow-exists.md)
