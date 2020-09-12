---
title: Contourweergave
description: Uitleg over het weer geven van selectie overzichten
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 5dd6d682c9db044763cad64eec420c1974d4ac03
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613696"
---
# <a name="outline-rendering"></a>Contourweergave

Geselecteerde objecten kunnen visueel worden gemarkeerd door een overzichts weergave toe te voegen via het [onderdeel hiërarchische status onderdrukking](../../overview/features/override-hierarchical-state.md). In dit hoofd stuk wordt uitgelegd hoe globale para meters voor overzichts rendering worden gewijzigd via de client-API.

Overzichts eigenschappen zijn een globale instelling. Alle objecten die gebruikmaken van de overzichts weergave, gebruiken dezelfde instelling. het is niet mogelijk om een contour kleur per object te gebruiken.

## <a name="parameters-for-outlinesettings"></a>Para meters voor `OutlineSettings`

Class `OutlineSettings` bevat de instellingen met betrekking tot algemene overzichts eigenschappen. De volgende leden worden beschikbaar gemaakt:

| Parameter      | Type    | Beschrijving                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | De kleur die wordt gebruikt voor het tekenen van het overzicht. Het gedeelte alpha wordt genegeerd.         |
| `PulseRateHz`    | float   | De snelheid waarmee de overzichts trillingen per seconde|
| `PulseIntensity` | float   | De intensiteit van het contour Pulse-effect. Moet tussen 0,0 en geen Pulse en 1,0 voor Full Pulse zijn. Met intensiteit wordt impliciet de minimale dekking van het overzicht ingesteld als `MinOpacity = 1.0 - PulseIntensity` . |

![Een object wordt drie keer gerenderd met verschillende overzichts parameters ](./media/outlines.png) het effect van het wijzigen van de `color` para meter van geel (links) naar magenta (midden) en `pulseIntensity` van 0 tot 0,8 (rechts).

## <a name="example"></a>Voorbeeld

De volgende code toont een voor beeld van het instellen van overzichts parameters via de API:

```cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<AzureSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = session->Actions()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>Prestaties

Het weer geven van overzichten kan een grote invloed hebben op de weergave prestaties. Deze impact is afhankelijk van de ruimte-relatie tussen geselecteerde en niet-geselecteerde objecten voor een bepaald frame.

## <a name="api-documentation"></a>API-documentatie

* [C# RemoteManager. OutlineSettings eigenschap](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.outlinesettings)
* [C++ RemoteManager:: OutlineSettings ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#outlinesettings)

## <a name="next-steps"></a>Volgende stappen

* [Overschrijvings onderdeel hiërarchische status](../../overview/features/override-hierarchical-state.md)
