---
title: Voorbeeldmodellen
description: Hier vindt u bronnen voor voorbeeldmodellen.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507532"
---
# <a name="sample-models"></a>Voorbeeldmodellen

In dit artikel vindt u enkele bronnen voor voorbeeldgegevens die kunnen worden gebruikt voor het testen van de Azure Remote Rendering-service.

## <a name="built-in-sample-model"></a>Ingebouwd voorbeeldmodel

We bieden een ingebouwd voorbeeldmodel dat altijd kan worden geladen met behulp van de URL **builtin://Engine**

![Voorbeeldmodel](./media/sample-model.png "Voorbeeldmodel")

Modelstatistieken:

| Naam | Waarde |
|-----------|:-----------|
| [Vereiste servergrootte](../how-tos/session-rest-api.md#create-a-session) | Standard |
| Aantal driehoeken | 18,7 miljoen |
| Aantal bewegende delen | 2073 |
| Aantal materialen | 94 |

## <a name="third-party-data"></a>Gegevens van derden

De Khronos Group onderhoudt een reeks glTF-voorbeeldmodellen voor testdoeleinden. ARR ondersteunt de glTF-indeling in zowel tekstindeling ( *. gltf*) als in een binair bestand ( *.glb*). We raden aan om de PBR-modellen te gebruiken voor de beste visuele resultaten:

* [glTF-voorbeeldmodellen](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een model weergeven met Unity](../quickstarts/render-model.md)
* [Snelstart: een model converteren voor rendering](../quickstarts/convert-model.md)
