---
title: Materialen
description: Materiaal beschrijving en materiaal eigenschappen weer geven
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 2bc356060bacd1c04ecb3d3dd10b8322ae40b8ba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83758670"
---
# <a name="materials"></a>Materialen

Materialen zijn [gedeelde resources](../concepts/lifetime.md) die bepalen hoe [mazen](meshes.md) worden weer gegeven. Materialen worden gebruikt om op te geven welke [bitmappatronen](textures.md) moeten worden toegepast, of u objecten transparant wilt maken en hoe belichting moet worden berekend.

Materialen worden automatisch gemaakt tijdens de [model conversie](../how-tos/conversion/model-conversion.md) en zijn toegankelijk tijdens runtime. U kunt ook aangepaste materialen maken op basis van code en deze vervangen. Dit scenario is vooral handig als u hetzelfde materiaal wilt delen in veel netten. Omdat de wijzigingen van een materiaal zichtbaar zijn op elke mesh die ernaar verwijst, kan deze methode worden gebruikt om eenvoudig wijzigingen toe te passen.

> [!NOTE]
> Sommige use-cases, zoals het markeren van een gepickt object, kunnen worden uitgevoerd door materialen te wijzigen, maar zijn veel eenvoudiger te bereiken via de [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Materiaal typen

De externe rendering van Azure heeft twee verschillende materiaal typen:

* [PBR-materialen](../overview/features/pbr-materials.md) worden gebruikt voor Opper vlakken die zo fysiek mogelijk moeten worden gerenderd. Realistische verlichting wordt berekend op basis van deze materialen met behulp van *fysieke rendering* (PBR). Om optimaal gebruik te kunnen maken van dit type materiaal, is het belang rijk om invoer gegevens van hoge kwaliteit op te geven, zoals Grove en normale kaarten.

* [Kleur materialen](../overview/features/color-materials.md) worden gebruikt voor gevallen waarin geen extra verlichting gewenst is. Deze materialen zijn altijd volledig helder en zijn eenvoudiger te installeren. Kleur materialen worden gebruikt voor gegevens die helemaal geen verlichting moeten hebben of die al een statische verlichting hebben, zoals modellen die zijn verkregen via [Photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Net versus MeshComponent-materiaal toewijzing

[Mazen](meshes.md) hebben een of meer subnetten. Elke submesh verwijst naar één materiaal. U kunt het materiaal wijzigen zodat het rechtstreeks op het net wordt gebruikt, of u kunt overschrijven welk materiaal moet worden gebruikt voor een subnet in een [MeshComponent](meshes.md#meshcomponent).

Wanneer u een materiaal rechtstreeks op de netresource wijzigt, geldt deze wijziging voor alle exemplaren van dat net. Het wijzigen van de waarde op de MeshComponent heeft echter alleen invloed op die ene mesh-instantie. Welke methode het meest geschikt is, is afhankelijk van het gewenste gedrag, maar het wijzigen van een MeshComponent is de meest voorkomende benadering.

## <a name="material-de-duplication"></a>Materiaal van de duplicatie

Tijdens de conversie worden meerdere materialen met dezelfde eigenschappen en bitmappatronen automatisch gedupliceerd in één enkel materiaal. U kunt deze functie uitschakelen in de [conversie-instellingen](../how-tos/conversion/configure-model-conversion.md), maar we raden u aan deze in te scha kelen voor de beste prestaties.

## <a name="material-classes"></a>Materiaal klassen

Alle materialen die door de API worden verschaft, zijn afgeleid van de basis klasse `Material` . Hun type kan worden opgevraagd `Material.MaterialSubType` of door ze rechtstreeks te converteren:

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (*material->MaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->AlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (*material->MaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->AlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```


## <a name="next-steps"></a>Volgende stappen

* [PBR-materialen](../overview/features/pbr-materials.md)
* [Kleurmaterialen](../overview/features/color-materials.md)
