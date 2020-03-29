---
title: Gegevensstroom in kaart brengen Surrogaatsleuteltransformatie
description: De mapping dataflow surrogate key transformation van Azure Data Factory gebruiken om sequentiële sleutelwaarden te genereren
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930206"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Gegevensstroom in kaart brengen Surrogaatsleuteltransformatie



Gebruik de surrogaatsleuteltransformatie om een willekeurige waarde van niet-zakelijke sleutels toe te voegen aan uw rijset gegevensstroom. Dit is handig bij het ontwerpen van dimensietabellen in een analytisch gegevensmodel voor een sterschema waarbij elk lid in uw dimensietabellen een unieke sleutel moet hebben die een niet-zakelijke sleutel is, onderdeel van de Kimball DW-methodologie.

![Surrogaat sleutel transformeren](media/data-flow/surrogate.png "Surrogaat sleutel transformatie")

"Key Column" is de naam die u zal geven aan uw nieuwe surrogaat sleutel kolom.

'Startwaarde' is het beginpunt van de incrementele waarde.

## <a name="increment-keys-from-existing-sources"></a>Toetsen uit bestaande bronnen verhogen

Als u uw reeks wilt starten vanaf een waarde die in een bron bestaat, u een afgeleide kolomtransformatie gebruiken onmiddellijk na de transformatie van uw Surrogaatsleutel en de twee waarden bij elkaar voegen:

![SK voeg Max toe](media/data-flow/sk006.png "Surrogaat sleutel transformatie voeg Max")

Om de belangrijkste waarde met de vorige max te zaaien, zijn er twee technieken die u gebruiken:

### <a name="database-sources"></a>Databasebronnen

Gebruik de optie 'Query' om MAX() uit uw bron te selecteren met de brontransformatie:

![Surrogaatsleutelquery](media/data-flow/sk002.png "Surrogaatsleuteltransformatiequery")

### <a name="file-sources"></a>Bestandsbronnen

Als uw vorige maximumwaarde zich in een bestand bevindt, u de brontransformatie samen met een gesplitste transformatie gebruiken en de expressiefunctie MAX() gebruiken om de vorige maximale waarde te krijgen:

![Surrogaatsleutelbestand](media/data-flow/sk008.png "Surrogaatsleutelbestand")

In beide gevallen moet u deelnemen aan uw binnenkomende nieuwe gegevens samen met uw bron die de vorige maximale waarde bevat:

![Surrogaatsleutel Join](media/data-flow/sk004.png "Surrogaatsleutel Join")

## <a name="next-steps"></a>Volgende stappen

In deze voorbeelden worden de transformaties [Join en](data-flow-join.md) [Afgeleide Kolom](data-flow-derived-column.md) gebruikt.
