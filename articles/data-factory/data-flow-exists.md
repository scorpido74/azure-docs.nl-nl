---
title: Er is een trans formatie Azure Data Factory toewijzings gegevens stroom
description: Controleren op bestaande rijen met data factory toewijzing van gegevens stromen met exists-trans formatie
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6048a6d30d37b9d2b46c3105c5f8eac0a9ca41c0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387838"
---
# <a name="mapping-data-flow-exists-transformation"></a>Er is een trans formatie voor de toewijzing van gegevens stroom



De exists-trans formatie is een filter transformatie voor rijen waarmee rijen in uw gegevens kunnen worden getransporteerd. De exists-trans formatie is vergelijkbaar met ```SQL WHERE EXISTS``` en ```SQL WHERE NOT EXISTS```. Na de exists-trans formatie bevat de resulterende rijen uit de gegevens stroom alle rijen waarin kolom waarden uit bron 1 bestaan in bron 2 of die niet voor komen in bron 2.

![Instellingen voor exists](media/data-flow/exists.png "bestaat 1")

Kies de tweede bron voor uw bestaande, zodat de gegevens stroom waarden van stream 1 kan vergelijken met Stream 2.

Selecteer de kolom van bron 1 en van bron 2 waarvan u de waarden wilt controleren voor bestaat of niet bestaat.

## <a name="multiple-exists-conditions"></a>Meerdere voor waarden voor bestaan

Naast elke rij in de kolom voorwaarden voor bestaat, wordt er een plus teken weer beschikbaar wanneer u de muis aanwijzer boven de bereiken bereikt. Hiermee kunt u meerdere rijen toevoegen voor voor waarden. Elke aanvullende voor waarde is een ' en '.

## <a name="custom-expression"></a>Aangepaste expressie

![Aangepaste instellingen bestaan](media/data-flow/exists1.png "bestaat aangepast")

U kunt op ' aangepaste expressie ' klikken om in plaats daarvan een vrije-vorm expressie te maken met de voor waarde al of niet aanwezig. Als u dit selectie vakje inschakelt, kunt u uw eigen expressie als voor waarde invoeren.

## <a name="next-steps"></a>Volgende stappen

Vergelijk bare trans formaties zijn [Opzoeken](data-flow-lookup.md) en [samen voegen](data-flow-join.md).
