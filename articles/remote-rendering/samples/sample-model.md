---
title: Voorbeeldmodellen
description: Hiermee worden bronnen voor voorbeeldmodellen weergegeven.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679483"
---
# <a name="sample-models"></a>Voorbeeldmodellen

In dit artikel worden enkele bronnen voor voorbeeldgegevens weergegeven die kunnen worden gebruikt voor het testen van de Azure Remote Rendering-service.

## <a name="built-in-sample-model"></a>Ingebouwd voorbeeldmodel

We bieden een ingebouwd voorbeeldmodel dat altijd kan worden geladen met behulp van de **URL-builtin://Engine**

![Voorbeeldmodel](./media/sample-model.png "Voorbeeldmodel")

Modelstatistieken:

| Name | Waarde |
|-----------|:-----------|
| [Vereiste VM-grootte](../how-tos/session-rest-api.md#create-a-session) | Standaard |
| Aantal driehoeken | 18,7 miljoen |
| Aantal beweegbare delen | 2073 |
| Aantal materialen | 94 |

## <a name="third-party-data"></a>Gegevens van derden

De Khronos Group onderhoudt een set glTF-monstermodellen voor het testen. ARR ondersteunt de glTF-indeling, zowel in tekst (*.gltf*) als in binaire (*.glb*) vorm. We raden u aan de PBR-modellen te gebruiken voor de beste visuele resultaten:

* [glTF-voorbeeldmodellen](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: een model weergeven met Unity](../quickstarts/render-model.md)
* [Snelstart: een model converteren voor rendering](../quickstarts/convert-model.md)
