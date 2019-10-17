---
title: Gegevens stroom van surrogaat sleutel transformatie Azure Data Factory koppelen
description: De trans formatie van de gegevens stroom surrogaat van Azure Data Factory gebruiken om sequentiële sleutel waarden te genereren
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 144d6298a13d35d94a68b35c443a3a47cefcfc2a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387163"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Gegevens stroom van surrogaat sleutel transformatie toewijzen



Gebruik de trans formatie surrogaat sleutel voor het toevoegen van een wille keurige sleutel waarde die niet aan het bedrijf is toe te voegen aan uw gegevens stroom rijenset. Dit is handig bij het ontwerpen van dimensie tabellen in een ster schema analytisch gegevens model, waarbij elk lid in de dimensie tabellen een unieke sleutel moet hebben die een niet-bedrijfs sleutel is, onderdeel van de Kimball DW-methodologie.

![Surrogaat sleutel transformatie](media/data-flow/surrogate.png "Surrogaat sleutel transformatie")

' Sleutel kolom ' is de naam die u voor de nieuwe kolom met surrogaat sleutels moet opgeven.

"Begin waarde" is het begin punt van de incrementele waarde.

## <a name="increment-keys-from-existing-sources"></a>Sleutels van bestaande bronnen stapsgewijs verhogen

Als u de volg orde wilt starten vanuit een waarde die voor komt in een bron, kunt u een afgeleide kolom transformatie direct volgen op uw surrogaat sleutel transformatie en de twee waarden samen voegen:

![SK toevoegen Max](media/data-flow/sk006.png "Aantal trans formatie van surrogaat sleutel maximum toevoegen")

Als u de sleutel waarde wilt seeden met het vorige maximum, zijn er twee technieken die u kunt gebruiken:

### <a name="database-sources"></a>Database bronnen

Gebruik de optie ' query ' om MAX () te selecteren in uw bron met behulp van de bron transformatie:

![Surrogaat sleutel query](media/data-flow/sk002.png "Query voor surrogaat sleutel transformatie")

### <a name="file-sources"></a>Bestands bronnen

Als uw vorige maximum waarde zich in een bestand bevindt, kunt u uw bron transformatie samen met een geaggregeerde trans formatie gebruiken en de expressie functie MAX () gebruiken om de vorige maximum waarde op te halen:

![Surrogaat sleutel bestand](media/data-flow/sk008.png "Surrogaat sleutel bestand")

In beide gevallen moet u uw inkomende nieuwe gegevens samen voegen met de bron die de vorige maximum waarde bevat:

![Surrogaat sleutel toevoegen](media/data-flow/sk004.png "Surrogaat sleutel toevoegen")

## <a name="next-steps"></a>Volgende stappen

In deze voor beelden [worden](data-flow-join.md) de combi neren en [afgeleide kolom](data-flow-derived-column.md) transformaties gebruikt.
