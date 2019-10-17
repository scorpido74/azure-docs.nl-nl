---
title: Voorwaardelijke Splits transformatie Azure Data Factory toewijzings gegevens stroom
description: Voorwaardelijke Splits-trans formatie van gegevens stroom Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d06b5b86737d0940930a3ccea3b6d65be0a802f9
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387893"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Gegevens stroom toewijzen Conditional Split trans formatie



![werkset Conditional Split](media/data-flow/conditionalsplit2.png "werkset Conditional Split")

De voorwaardelijke Splits transformatie kan gegevens rijen naar verschillende stromen routeren, afhankelijk van de inhoud van de gegevens. De implementatie van de voorwaardelijke Splits transformatie is vergelijkbaar met een beslissings structuur voor de situatie in een programmeer taal. De trans formatie evalueert expressies en op basis van de resultaten, stuurt de gegevensrij door naar de opgegeven stroom. Deze trans formatie biedt ook een standaard uitvoer, zodat als een rij overeenkomt met geen expressie, deze wordt omgeleid naar de standaard uitvoer.

![Conditional Split](media/data-flow/conditionalsplit1.png "Conditional Split opties")

## <a name="multiple-paths"></a>Meerdere paden

Als u aanvullende voor waarden wilt toevoegen, selecteert u ' stream toevoegen ' in het onderste configuratie venster en klikt u in het tekstvak opbouw functie voor expressies om uw expressie te bouwen.

![Conditional Split multi](media/data-flow/conditionalsplit3.png "Conditional Split multi")

## <a name="next-steps"></a>Volgende stappen

Gemeen schappelijke gegevensstroom transformaties die worden gebruikt met Conditional Split: [koppelings transformatie](data-flow-join.md), [Zoek transformatie](data-flow-lookup.md), [Selecteer trans formatie](data-flow-select.md)
