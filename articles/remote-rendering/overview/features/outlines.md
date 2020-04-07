---
title: Overzichtsweergave
description: Legt uit hoe u het renderen van selectiecontouren maken
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680828"
---
# <a name="outline-rendering"></a>Overzichtsweergave

Geselecteerde objecten kunnen visueel worden gemarkeerd door overzichtsweergave toe te voegen via de [component Hiërarchische statusoverschrijving](../../overview/features/override-hierarchical-state.md). In dit hoofdstuk wordt uitgelegd hoe globale parameters voor overzichtsweergave worden gewijzigd via de client-API.

Overzichtseigenschappen zijn een globale instelling. Alle objecten die overzichtsweergave gebruiken, gebruiken dezelfde instelling - het is niet mogelijk om een omtrekkleur per object te gebruiken.

## <a name="parameters-for-outlinesettings"></a>Parameters voor`OutlineSettings`

Klasse `OutlineSettings` bevat de instellingen met betrekking tot globale overzichtseigenschappen. Het stelt de volgende leden bloot:

| Parameter      | Type    | Beschrijving                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub (kleur4Ub) | De kleur die wordt gebruikt voor het tekenen van de omtrek. Het alfagedeelte wordt genegeerd.         |
| `PulseRateHz`    | float   | De snelheid waarmee de omtrek per seconde oscilleert|
| `PulseIntensity` | float   | De intensiteit van het pulseffect. Moet tussen 0,0 voor geen pulseren de 1.0 voor volledige pulseren. Intensiteit stelt impliciet de minimale dekking van `MinOpacity = 1.0 - PulseIntensity`de omtrek in als . |

![Overzichten](./media/outlines.png) Het effect `color` van het wijzigen van de parameter `pulseIntensity` van geel (links) naar magenta (midden) en van 0 naar 0,8 (rechts).

## <a name="example"></a>Voorbeeld

De volgende code toont een voorbeeld voor het instellen van overzichtsparameters via de API:

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>Prestaties

Overzichtsweergave kan een aanzienlijke invloed hebben op de renderingprestaties. Deze impact varieert op basis van de ruimtelijke relatie tussen geselecteerde en niet-geselecteerde objecten voor een bepaald frame.

## <a name="next-steps"></a>Volgende stappen

* [Component hiërarchische status overschrijven](../../overview/features/override-hierarchical-state.md)
