---
title: Materialen
description: Materiaalbeschrijving en materiaaleigenschappen weergeven
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681842"
---
# <a name="materials"></a>Materialen

Materialen zijn [gedeelde bronnen](../concepts/lifetime.md) die bepalen hoe [mazen](meshes.md) worden weergegeven. Materialen worden gebruikt om aan te geven welke [texturen](textures.md) moeten worden toegepast, of objecten transparant moeten worden gemaakt en hoe de verlichting wordt berekend.

Materialen worden automatisch gemaakt tijdens [de modelconversie](../how-tos/conversion/model-conversion.md) en zijn toegankelijk tijdens runtime. U ook aangepaste materialen maken op code en bestaande materialen vervangen. Dit scenario is vooral zinvol als u hetzelfde materiaal over vele mazen wilt delen. Aangezien wijzigingen van een materiaal zichtbaar zijn op elk gaas dat ernaar verwijst, kan deze methode worden gebruikt om eenvoudig wijzigingen toe te passen.

> [!NOTE]
> Sommige use cases, zoals het markeren van een gekozen object, kunnen worden gedaan door materialen te wijzigen, maar zijn veel gemakkelijker te bereiken via de [HierarchicalStateOverrideComponent.](../overview/features/override-hierarchical-state.md)

## <a name="material-types"></a>Materiaalsoorten

Azure Remote Rendering heeft twee verschillende materiaaltypen:

* [PBR-materialen](../overview/features/pbr-materials.md) worden gebruikt voor oppervlakken die zo fysiek correct mogelijk moeten worden weergegeven. Realistische verlichting wordt berekend voor deze materialen met behulp van *fysiek gebaseerde rendering* (PBR). Om het meeste uit dit materiaaltype te halen, is het belangrijk om hoogwaardige invoergegevens te verstrekken, zoals ruwheid en normale kaarten.

* [Kleurmaterialen](../overview/features/color-materials.md) worden gebruikt voor gevallen waar geen extra verlichting gewenst is. Deze materialen zijn altijd volledig helder en zijn gemakkelijker in te stellen. Kleurmaterialen worden gebruikt voor gegevens die helemaal geen verlichting moeten hebben, of al statische verlichting bevatten, zoals modellen die zijn verkregen door [middel van fotogrammetrie.](https://en.wikipedia.org/wiki/Photogrammetry)

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Toewijzing van mesh vs. MeshComponent-materiaal

[Mazen](meshes.md) hebben een of meer submeshes. Elke submesh verwijst naar één materiaal. U het materiaal wijzigen om rechtstreeks op het gaas te gebruiken, of u overschrijven welk materiaal u moet gebruiken voor een submesh op een [MeshComponent.](meshes.md#meshcomponent)

Wanneer u een materiaal rechtstreeks op de mesh-bron wijzigt, is deze wijziging van invloed op alle exemplaren van dat net. Het wijzigen van deze op de MeshComponent heeft echter alleen invloed op die ene mesh-instantie. Welke methode geschikter is, is afhankelijk van het gewenste gedrag, maar het wijzigen van een MeshComponent is de meest voorkomende aanpak.

## <a name="material-classes"></a>Materiaalklassen

Alle materialen die door de API `Material`worden geleverd, zijn afkomstig van de basisklasse . Hun type kan worden `Material.MaterialSubType` opgevraagd door of door ze rechtstreeks te casten:

``` cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if( pbrMat!= null )
    {
        PbrMaterial pbrMaterial = material.PbrMaterial.Value;
        pbrMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

## <a name="next-steps"></a>Volgende stappen

* [PBR-materialen](../overview/features/pbr-materials.md)
* [Kleurmaterialen](../overview/features/color-materials.md)
