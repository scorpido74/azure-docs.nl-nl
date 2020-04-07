---
title: De bezinningen van de hemel
description: Beschrijft hoe u omgevingskaarten instelt voor luchtreflecties
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680607"
---
# <a name="sky-reflections"></a>De bezinningen van de hemel

In Azure Remote Rendering wordt een hemelstructuur gebruikt om objecten realistisch te verlichten. Voor augmented reality-toepassingen moet deze textuur lijken op uw echte omgeving, om objecten overtuigend te laten lijken. In dit artikel wordt beschreven hoe u de luchttextuur wijzigen.

> [!NOTE]
> De luchttextuur wordt ook wel een *omgevingskaart*genoemd. Deze termen worden door elkaar gebruikt.

## <a name="object-lighting"></a>Objectverlichting

Azure Remote Rendering maakt gebruik *van fysiek gebaseerde rendering* (PBR) voor realistische lichtberekeningen. Hoewel u [lichtbronnen](lights.md) aan uw scène toevoegen, heeft het gebruik van een goede luchttextuur de grootste impact.

De onderstaande afbeeldingen tonen de resultaten van het licht verschillende oppervlakken alleen met een hemel textuur:

| Ruwheid  | 0                                        | 0,25                                          | 0.5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Niet-metaal  | ![Diëlektrisch0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metalen      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

Zie voor meer informatie over het lichtmodel het [hoofdstuk materialen.](../../concepts/materials.md)

> [!IMPORTANT]
> Azure Remote Rendering gebruikt de hemelstructuur alleen voor verlichtingsmodellen. Het maakt de hemel niet als achtergrond, omdat Augmented Reality-toepassingen al een goede achtergrond hebben - de echte wereld.

## <a name="changing-the-sky-texture"></a>Het veranderen van de hemeltextuur

Als u de omgevingskaart wilt wijzigen, hoeft u alleen maar `SkyReflectionSettings`een structuur te [laden](../../concepts/textures.md) en de sessie te wijzigen:

``` cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

Houd er `LoadTextureFromSASAsync` rekening mee dat de variant hierboven wordt gebruikt omdat een ingebouwde structuur is geladen. In het geval van laden vanuit `LoadTextureAsync` [gekoppelde blob-opslag,](../../how-tos/create-an-account.md#link-storage-accounts)gebruikt u de variant.

## <a name="sky-texture-types"></a>Hemelstructuurtypen

U zowel *[kubuskaarten](https://en.wikipedia.org/wiki/Cube_mapping)* als *2D-texturen* gebruiken als omgevingskaarten.

Alle texturen moeten in een [ondersteunde textuurindeling zijn.](../../concepts/textures.md#supported-texture-formats) U hoeft geen mipmaps te leveren voor hemeltexturen.

### <a name="cube-environment-maps"></a>Kubusomgevingskaarten

Ter referentie, hier is een uitgepakte kubuskaart:

![Een uitgepakte kubuskaart](media/Cubemap-example.png)

Met `AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` `TextureType.CubeMap` behulp van cubemap-texturen laden.

### <a name="sphere-environment-maps"></a>Sfeeromgevingskaarten

Wanneer u een 2D-structuur als omgevingskaart gebruikt, moet de afbeelding zich in [de sferische coördinatenruimte bevinden.](https://en.wikipedia.org/wiki/Spherical_coordinate_system)

![Een hemelbeeld in sferische coördinaten](media/spheremap-example.png)

Gebruik `AzureSession.Actions.LoadTextureAsync` `TextureType.Texture2D` met het laden van sferische omgevingskaarten.

## <a name="built-in-environment-maps"></a>Ingebouwde omgevingskaarten

Azure Remote Rendering biedt een aantal ingebouwde omgevingskaarten die altijd beschikbaar zijn. Alle ingebouwde omgevingskaarten zijn kubuskaarten.

|Id                         | Beschrijving                                              | Illustratie                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Verscheidenheid van streeplichten, heldere binnenbasisverlichting    | ![Autoshop Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | Heldere binnenlichtinstelling, meerdere raamverlichting      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | Afwisselend gekleurde lichten in middelgrote lichte binneninstelling  | ![ColorfulStudio (ColorfulStudio)](media/colorful-studio.png)
|builtin://Hangar                   | Matig helder omgevingszaallicht                     | ![SmallHangar SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Dim binneninstelling met licht-donker contrast              | ![Industriële pijp- en klepklep](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Overdag ambient kamer licht, helder raam gebied licht     | ![Lebombo Lebombo](media/lebombo.png)
|builtin://SataraNight              | Donkere nachthemel en grond met vele omringende lichten   | ![SataraNight SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | Fel zonlicht en schaduwcontrast                      | ![ZonnigVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Helder hemellicht met gematigde grondverlichting            | ![Syferfontein Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Matig wisselende zon en schaduw                         | ![TearsofSteelbridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Het zonsonderganglicht dat schemer nadert                    | ![VenetiëZonsondergang](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Heldere, weelderige-groene, en witte lichttonen, gedimde grond | ![WhippleCreekRegionaalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Overdag met helder omgevingslicht                 | ![WinterRivier (WinterRivier)](media/winter-river.png)
|builtin://DefaultSky               | Hetzelfde als TearsOfSteelBridge                               | ![DefaultSky DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Volgende stappen

* [Lampen](../../overview/features/lights.md)
* [Materialen](../../concepts/materials.md)
* [Texturen](../../concepts/textures.md)
* [Het texconv-opdrachtregelgereedschap](../../resources/tools/tex-conv.md)
