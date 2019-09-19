---
title: Voorwaardelijke Splits transformatie Azure Data Factory toewijzings gegevens stroom
description: Voorwaardelijke Splits-trans formatie van gegevens stroom Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd51cc2d5f95598154b76b5da8e3fc9e4801100d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104951"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Gegevens stroom toewijzen Conditional Split trans formatie

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![werkset Conditional Split](media/data-flow/conditionalsplit2.png "werkset Conditional Split")

De voorwaardelijke Splits transformatie kan gegevens rijen naar verschillende stromen routeren, afhankelijk van de inhoud van de gegevens. De implementatie van de voorwaardelijke Splits transformatie is vergelijkbaar met een beslissings structuur voor de situatie in een programmeer taal. De trans formatie evalueert expressies en op basis van de resultaten, stuurt de gegevensrij door naar de opgegeven stroom. Deze trans formatie biedt ook een standaard uitvoer, zodat als een rij overeenkomt met geen expressie, deze wordt omgeleid naar de standaard uitvoer.

![Conditional Split](media/data-flow/conditionalsplit1.png "Conditional Split opties")

## <a name="multiple-paths"></a>Meerdere paden

Als u aanvullende voor waarden wilt toevoegen, selecteert u ' stream toevoegen ' in het onderste configuratie venster en klikt u in het tekstvak opbouw functie voor expressies om uw expressie te bouwen.

![Conditional Split multi](media/data-flow/conditionalsplit3.png "Conditional Split multi")

## <a name="next-steps"></a>Volgende stappen

Gemeen schappelijke gegevensstroom transformaties die worden gebruikt met Conditional Split: [Deelname aan trans formatie](data-flow-join.md), [opzoek transformatie](data-flow-lookup.md), [Selecteer trans formatie](data-flow-select.md)
