---
title: Materialen overschrijven tijdens modelconversie
description: Legt de materiaaloverheersende workflow uit tijdens de conversietijd
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681478"
---
# <a name="override-materials-during-model-conversion"></a>Materialen overschrijven tijdens modelconversie

Tijdens de conversie worden de materiaalinstellingen in het bronmodel gebruikt om de [PBR-materialen](../../overview/features/pbr-materials.md) te definiëren die door de renderer worden gebruikt.
Soms geeft de [standaardconversie](../../reference/material-mapping.md) niet de gewenste resultaten en moet u wijzigingen aanbrengen.
Wanneer een model wordt geconverteerd voor gebruik in Azure Remote Rendering, u een bestand voor materiaaloverschrijvingen verstrekken om aan te passen hoe materiaalconversie per materiaal wordt uitgevoerd.
De sectie over [het configureren van modelconversie](configure-model-conversion.md) bevat instructies voor het declareren van de bestandsnaam van het materiaal.

## <a name="the-override-file-used-during-conversion"></a>Het override-bestand dat tijdens de conversie wordt gebruikt

Als een eenvoudig voorbeeld, laten we zeggen dat een doos model heeft een enkel materiaal, genaamd "Standaard". De albedo kleur moet worden aangepast voor gebruik in ARR.
In dit geval `box_materials_override.json` kan een bestand als volgt worden gemaakt:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

Het `box_materials_override.json` bestand wordt in de invoercontainer geplaatst en er wordt een `ConversionSettings.json` naast `box.fbx`geplaatst , waarmee de conversie wordt geopend waar het overschrijfbestand moet worden gevonden (zie De [modelconversie configureren):](configure-model-conversion.md)

```json
{
    "material-override" : "box_materials_override.json"
}
```

Wanneer het model wordt geconverteerd, zijn de nieuwe instellingen van toepassing.

### <a name="color-materials"></a>Kleurmaterialen

Het [kleurmateriaalmodel](../../overview/features/color-materials.md) beschrijft een voortdurend gearceerd oppervlak dat onafhankelijk is van verlichting.
Dit is bijvoorbeeld handig voor assets gemaakt door Photogrammetry-algoritmen.
In materiaaloverschrijven bestanden, kan een materiaal worden `unlit` gedeclareerd als een kleur materiaal door in te stellen op `true`.

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Specifieke structuurkaarten negeren

Soms wilt u misschien dat het conversieproces specifieke structuurkaarten negeert. Dit kan het geval zijn wanneer uw model is gegenereerd door een tool die speciale kaarten genereert die niet correct door de renderer worden begrepen. Bijvoorbeeld een "OpacityMap" die wordt gebruikt om iets anders dan dekking te definiëren, of een model waarbij de "NormalMap" wordt opgeslagen als "BumpMap". (In het laatste geval wilt u "NormalMap" negeren, waardoor de converter "BumpMap" als "NormalMap" gebruikt.)

Het principe is eenvoudig. Voeg gewoon een `ignoreTextureMaps` eigenschap toe die wordt aangeroepen en voeg een structuurkaart toe die u wilt negeren:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Zie het JSON-schema hieronder voor de volledige lijst met structuurkaarten die u negeren.

## <a name="json-schema"></a>JSON-schema

Het volledige JSON-schema voor materiaalbestanden wordt hier gegeven. Met uitzondering `unlit` van `ignoreTextureMaps`en , de beschikbare eigenschappen zijn een subset van de eigenschappen beschreven in de secties op het [kleurmateriaal](../../overview/features/color-materials.md) en [PBR materiaal](../../overview/features/pbr-materials.md) modellen.

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Kleurmaterialen](../../overview/features/color-materials.md)
* [PBR-materialen](../../overview/features/pbr-materials.md)