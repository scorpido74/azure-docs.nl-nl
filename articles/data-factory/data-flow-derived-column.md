---
title: Afgeleide kolom transformatie in toewijzings gegevens stroom-Azure Data Factory | Microsoft Docs
description: Meer informatie over het transformeren van gegevens op schaal in Azure Data Factory met de toewijzing gegevens stroom die is afgeleide kolom transformatie.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aacd6f1799f1813e168bd04e78f18cf60ad5243f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026850"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Afgeleide kolom transformatie bij toewijzing van gegevens stroom

Gebruik de afgeleide kolom transformatie om nieuwe kolommen in uw gegevens stroom te genereren of om bestaande velden te wijzigen.

## <a name="derived-column-settings"></a>Afgeleide kolom instellingen

Als u een bestaande kolom wilt overschrijven, selecteert u deze via de vervolg keuzelijst kolom. Als dat niet het geval is, gebruikt u het veld kolom selectie als tekstvak en typt u de naam van de nieuwe kolom. Als u de expressie van de afgeleide kolom wilt maken, klikt u op het vak expressie invoeren om de [opbouw functie voor de data flow-expressie](concepts-data-flow-expression-builder.md)te openen.

Afgeleide(media/data-flow/dc1.png "kolom instellingen voor") ![afgeleide kolom instellingen]

Als u extra afgeleide kolommen wilt toevoegen, houdt u de muis aanwijzer over een bestaande afgeleide kolom en klikt u op +. Vervolgens kiest u kolom toevoegen of patroon van kolom toevoegen. Kolom patronen kunnen handig zijn als uw kolom namen van uw bronnen variabel zijn. Zie [kolom patronen](concepts-data-flow-column-pattern.md)voor meer informatie.

![Nieuwe afgeleide kolom selectie](media/data-flow/columnpattern.png "nieuwe afgeleide kolom selectie")

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [taal van de toewijzings gegevens stroom](data-flow-expression-functions.md).
