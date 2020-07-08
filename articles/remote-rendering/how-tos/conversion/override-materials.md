---
title: Materialen overschrijven tijden modelconversie
description: Geeft uitleg over de manier waarop de werk stroom op de conversie tijd wordt overschreven
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681478"
---
# <a name="override-materials-during-model-conversion"></a>Materialen overschrijven tijden modelconversie

Tijdens de conversie worden de materiaal instellingen in het bron model gebruikt voor het definiëren van de [PBR-materialen](../../overview/features/pbr-materials.md) die worden gebruikt door de renderer.
Soms geeft de [standaard conversie](../../reference/material-mapping.md) niet de gewenste resultaten en u moet wijzigingen aanbrengen.
Wanneer een model wordt geconverteerd voor gebruik in azure-rendering op afstand, kunt u een bestand voor het overschrijven van materialen opgeven om te bepalen hoe de conversie van materialen per materiaal wordt uitgevoerd.
De sectie over het configureren van de [model conversie](configure-model-conversion.md) bevat instructies voor het declareren van de bestands naam voor het overschrijven van materiaal.

## <a name="the-override-file-used-during-conversion"></a>Het overschrijvings bestand dat wordt gebruikt tijdens de conversie

Stel dat een box-model een enkel materiaal heeft met de naam standaard. De kleur van de albedo moet worden aangepast voor gebruik in ARR.
In dit geval kan een `box_materials_override.json` bestand als volgt worden gemaakt:

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

Het `box_materials_override.json` bestand wordt in de invoer container geplaatst en er `ConversionSettings.json` wordt een toegevoegd naast `box.fbx` , waarmee wordt aangegeven dat het overschrijvings bestand moet worden gevonden (Zie [de model conversie configureren](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

Wanneer het model wordt geconverteerd, worden de nieuwe instellingen toegepast.

### <a name="color-materials"></a>Kleurmaterialen

Het [kleuren materiaal](../../overview/features/color-materials.md) model beschrijft een voortdurend gearceerd Opper vlak dat onafhankelijk is van de belichting.
Dit is handig voor assets die zijn gemaakt door Photogrammetry-algoritmen, bijvoorbeeld.
In materiaal onderdrukking bestanden kan een materiaal worden gedeclareerd als een kleur materiaal door in te stellen `unlit` op `true` .

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

### <a name="ignore-specific-texture-maps"></a>Specifieke patroon kaarten negeren

Soms wilt u het conversie proces specifieke structuur toewijzingen negeren. Dit kan het geval zijn wanneer het model is gegenereerd door een hulp programma waarmee speciale kaarten worden gegenereerd die niet goed zijn begrepen door de renderer. Bijvoorbeeld een ' OpacityMap ' die wordt gebruikt voor het definiëren van iets anders dan dekking, of een model waarin ' NormalMap ' is opgeslagen als ' BumpMap '. (In het laatste geval kunt u "NormalMap" negeren. Dit zorgt ervoor dat het conversie programma "BumpMap" als "NormalMap" gebruikt.)

Het principe is eenvoudig. Voeg een eigenschap toe met de naam `ignoreTextureMaps` en voeg een structuur toewijzing toe die u wilt negeren:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Zie het onderstaande JSON-schema voor een volledige lijst met patroon toewijzingen die u kunt negeren.

## <a name="json-schema"></a>JSON-schema

Hier wordt het volledige JSON-schema voor materiaal bestanden gegeven. Met uitzonde ring van `unlit` en `ignoreTextureMaps` , zijn de beschik bare eigenschappen een subset van de eigenschappen die worden beschreven in de secties op het [kleuren](../../overview/features/color-materials.md) -en [PBR-materiaal](../../overview/features/pbr-materials.md) model.

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