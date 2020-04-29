---
title: Voorbeeldmodellen
description: Geeft een lijst van bronnen voor voorbeeld modellen.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679483"
---
# <a name="sample-models"></a>Voorbeeldmodellen

In dit artikel vindt u enkele bronnen voor voorbeeld gegevens die kunnen worden gebruikt voor het testen van de Azure remote rendering-service.

## <a name="built-in-sample-model"></a>Ingebouwd voorbeeld model

We bieden een ingebouwd voorbeeld model dat altijd kan worden geladen met behulp van de URL **Builtin://Engine**

![Voorbeeld model](./media/sample-model.png "Voorbeeld model")

Model statistieken:

| Naam | Waarde |
|-----------|:-----------|
| [Vereiste VM-grootte](../how-tos/session-rest-api.md#create-a-session) | standaard |
| Aantal drie hoeken | 18.700.000 |
| Aantal beweeg bare onderdelen | 2073 |
| Aantal materialen | 94 |

## <a name="third-party-data"></a>Gegevens van derden

De groep Khronos houdt een reeks glTF-voorbeeld modellen bij die moeten worden getest. ARR ondersteunt de glTF-indeling in tekst (*. glTF*) en in een binair (*. GLB*)-formulier. We raden aan om de PBR-modellen te gebruiken voor de beste visuele resultaten:

* [Voorbeeld modellen voor glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Volgende stappen

* [Quick Start: een model met Unit renderen](../quickstarts/render-model.md)
* [Snelstartgids: een model voor rendering converteren](../quickstarts/convert-model.md)
