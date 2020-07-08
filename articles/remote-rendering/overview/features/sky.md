---
title: Luchtreflecties
description: Hierin wordt beschreven hoe u omgevings kaarten instelt voor lucht reflecties
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: be3dc2b113cb21c2dfb54a29e7f426e0d925c6d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83759112"
---
# <a name="sky-reflections"></a>Luchtreflecties

In azure rendering op afstand wordt een hemel patroon gebruikt om objecten realistisch te lichten. Voor uitgebreide Reality-toepassingen moet dit patroon eruitzien als uw echte omgeving, om ervoor te zorgen dat objecten overtuigend worden weer gegeven. In dit artikel wordt beschreven hoe u het patroon van de lucht wijzigt.

> [!NOTE]
> Het lucht patroon wordt ook wel een *omgevings kaart*genoemd. Deze voor waarden worden door elkaar gebruikt.

## <a name="object-lighting"></a>Object verlichting

Azure remote rendering maakt gebruik van *fysiek gebaseerde rendering* (PBR) voor realistische belichtings berekeningen. Hoewel u [licht bronnen](lights.md) kunt toevoegen aan uw scène, heeft het gebruik van een goede lucht patroon de grootste impact.

In de onderstaande afbeeldingen ziet u de resultaten van de verlichting van verschillende Opper vlakken alleen met een lucht patroon:

| Ruw  | 0                                        | 0,25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Niet-Metal  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

Zie het hoofd stuk [materialen](../../concepts/materials.md) voor meer informatie over het belichtings model.

> [!IMPORTANT]
> Azure remote rendering maakt alleen gebruik van het lucht patroon voor belichtings modellen. De lucht wordt niet weer gegeven als achtergrond, omdat uitgebreide Reality-toepassingen al een correcte achtergrond hebben: de echte wereld.

## <a name="changing-the-sky-texture"></a>Het hemel patroon wijzigen

Als u de omgevings kaart wilt wijzigen, hoeft u alleen [een structuur te laden](../../concepts/textures.md) en de sessie te wijzigen `SkyReflectionSettings` :

```cs
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

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            ApiHandle<SkyReflectionSettings> settings = *session->Actions()->SkyReflectionSettings();
            settings->SkyReflectionTexture(*res->Result());
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}

```

Houd er rekening mee dat de `LoadTextureFromSASAsync` Variant wordt gebruikt, omdat een ingebouwd bitmappatroon wordt geladen. Gebruik de variant in het geval van het laden vanuit [gekoppelde Blob-opslag](../../how-tos/create-an-account.md#link-storage-accounts) `LoadTextureAsync` .

## <a name="sky-texture-types"></a>Lucht patroon typen

U kunt *[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* -en *2D-structuren* gebruiken als omgevings kaarten.

Alle bitmappatronen moeten een [ondersteunde structuur indeling](../../concepts/textures.md#supported-texture-formats)hebben. U hoeft geen mipmaps op te geven voor het lucht patroon.

### <a name="cube-environment-maps"></a>Kubus omgevings kaarten

Ter referentie is dit een niet-verpakte cubemap:

![Een onverpakte cubemap](media/Cubemap-example.png)

Gebruiken `AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` met `TextureType.CubeMap` voor het laden van cubemap-structuren.

### <a name="sphere-environment-maps"></a>Sphere-omgevings kaarten

Wanneer u een 2D-bitmappatroon als een omgevings kaart gebruikt, moet de afbeelding zich in een [bolvormige coördinaat ruimte](https://en.wikipedia.org/wiki/Spherical_coordinate_system)bevinden.

![Een lucht afbeelding in bolvormige coördinaten](media/spheremap-example.png)

Gebruik `AzureSession.Actions.LoadTextureAsync` met `TextureType.Texture2D` om bolvormige-omgevings kaarten te laden.

## <a name="built-in-environment-maps"></a>Ingebouwde omgevings kaarten

Externe rendering van Azure biedt een aantal ingebouwde omgevings kaarten die altijd beschikbaar zijn. Alle ingebouwde omgevings kaarten zijn cubemaps.

|Id                         | Description                                              | Zien                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Diverse Stripe-lampen, heldere basis verlichting    | ![Autoshoper](media/autoshop.png)
|builtin://BoilerRoom               | Lichte instelling voor licht binnenshuis, meerdere venster verlichting      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | Afwijkende kleur verlichting in de instelling gemiddelde licht binnenshuis  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | Gemiddeld helder omgevings zaal lampje                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | De instelling Dim binnen en licht-donker contrast              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Dagrijlicht-omgevings lampje, helder venster dun     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | Donkergrijze lucht en grond met een groot aantal omringende lichten   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | Helder zonlicht en schaduw contrast                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Scherp licht wissen met een lichte verlichting            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Zon en scha duwen die gemiddeld variëren                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Zons ondergangs lampje bijna Dusk                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Helder, lush-groen en wit licht tonen, lichter gekleurd vlak | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Daytime met helder omgevings licht                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | Hetzelfde als TearsOfSteelBridge                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Volgende stappen

* [Verlichting](../../overview/features/lights.md)
* [Materialen](../../concepts/materials.md)
* [Patronen](../../concepts/textures.md)
* [Het opdracht regel programma TexConv](../../resources/tools/tex-conv.md)
