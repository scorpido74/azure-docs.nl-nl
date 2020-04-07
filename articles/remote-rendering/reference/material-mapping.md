---
title: Materiaaltoewijzing voor modelindelingen
description: Beschrijft de standaardconversie van modelbronindelingen naar PBR-materiaal
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: ce287ed94066aac4b900d2ddb02579a54b8550f6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680386"
---
# <a name="material-mapping-for-model-formats"></a>Materiaaltoewijzing voor modelindelingen

Wanneer een bronasset als model wordt [geconverteerd,](../how-tos/conversion/model-conversion.md)maakt de converter [materialen](../concepts/materials.md) voor elk [net](../concepts/meshes.md). De manier waarop materialen worden gemaakt kan worden [overschreven.](../how-tos/conversion/override-materials.md) De conversie creëert echter standaard [PBR-materialen.](../overview/features/pbr-materials.md) Aangezien elke bronbestandsindeling, zoals FBX, zijn eigen conventies gebruikt om materialen te definiëren, moeten deze conventies worden toegewezen aan de PBR-materiaalparameters van Azure Remote Rendering. 

In dit artikel worden de exacte toewijzingen weergegeven die worden gebruikt om materialen om te zetten van bronelementen naar runtime-materialen.

## <a name="gltf"></a>glTF glTF

Bijna alles van de glTF 2.0 spec wordt ondersteund in Azure Remote Rendering, behalve *EmissiveFactor* en *EmissiveTexture*.

In de volgende tabel ziet u de toewijzing:

| glTF glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor (baseColorFactor)   |   albedoColor              |
|   baseColorTexture (baseColorTexture)  |   albedoMap                |
|   metallicFactor    |   metaalzijn                |
|   metallicTextuur   |   metalnessMap             |
|   ruwheidFactor   |   Ruwheid                |
|   ruwheidTextuur  |   roughnessMap             |
|   occlusionFactor   |   Occlusie                |
|   occlusionTextuur  |   occlusionMap             |
|   normalTexture normalTexture normalTexture normalTexture     |   Normalmap                |
|   alfacutoff       |   alphaClipThreshold       |
|   alfaMode.OPAQUE  |   alphaClipEnabled = false, isTransparent = false |
|   alfaMode.MASK    |   alphaClipEnabled = true, isTransparent = false  |
|   alphaMode.BLEND   |   isTransparant = waar     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

Elke structuur in glTF `texCoord` kan een waarde hebben, die ook wordt ondersteund in de Azure Remote Rendering-materialen.

### <a name="embedded-textures"></a>Ingesloten texturen

Structuren die zijn ingesloten in * \*.bin-* of * \*.glb-bestanden* worden ondersteund.

### <a name="supported-gltf-extension"></a>Ondersteunde glTF-extensie

Azure Remote Rendering ondersteunt bovendien de volgende glTF-extensies voor de basisfunctieset:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [MSFT_texture_dds](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_texture_dds/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): Komt overeen met [kleurmaterialen](../overview/features/color-materials.md). Voor *emissive* materialen is het raadzaam om deze extensie te gebruiken.
* [KHR_materials_pbrSpecularGlossiness:](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md)In plaats van metallic-ruwheid texturen, u diffuus-spiegelende glans texturen. De azure remote rendering-implementatie volgt rechtstreeks de conversieformules van de extensie.

## <a name="fbx"></a>Fbx

Het FBX-formaat is closed-source en FBX-materialen zijn niet compatibel met PBR-materialen in het algemeen. FBX maakt gebruik van een complexe beschrijving van oppervlakken met veel unieke parameters en eigenschappen en **niet alle worden gebruikt door de Azure Remote Rendering-pijplijn.**

> [!IMPORTANT]
> De Azure Remote Rendering-modelconversiepijplijn ondersteunt alleen **FBX 2011 en hoger.**

De FBX-formaat definieert een conservatieve benadering voor materialen, zijn er slechts twee soorten in de officiële FBX specificatie:

* *Lambert* - Niet vaak gebruikt voor geruime tijd al, maar het wordt nog steeds ondersteund door het omzetten naar Phong bij conversie tijd.
* *Phong* - Bijna alle materialen en de meeste content tools gebruiken dit type.

Het Phong-model is nauwkeuriger en wordt gebruikt als het *enige* model voor FBX-materialen. Hieronder zal worden aangeduid als de *FBX Material*.

> Maya gebruikt twee aangepaste extensies voor FBX door aangepaste eigenschappen te definiëren voor PBR- en Stingray-typen van een materiaal. Deze details zijn niet opgenomen in de FBX-specificatie, dus het wordt momenteel niet ondersteund door Azure Remote Rendering.

FBX-materialen gebruiken het diffuus-specular-SpecularLevel-concept, dus om van een diffuse textuur naar een albedo-kaart om te zetten, moeten we de andere parameters berekenen om ze van diffuus af te trekken.

> Alle kleuren en texturen in FBX bevinden zich in de sRGB-ruimte (ook wel Gamma-ruimte genoemd), maar Azure Remote Rendering werkt met lineaire ruimte tijdens visualisatie en aan het einde van het frame wordt alles teruggezet naar sRGB-ruimte. De Azure Remote Rendering asset pipeline converteert alles naar lineaire ruimte om deze als voorbereide gegevens naar de renderer te sturen.

In deze tabel ziet u hoe texturen worden toegewezen van FBX-materialen tot Azure Remote Rendering-materialen. Sommige ervan worden niet direct gebruikt, maar in combinatie met andere texturen die deelnemen aan de formules (bijvoorbeeld de diffuse textuur):

| Fbx | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | Occlusiekaart   |
| Diffuuskleur | *gebruikt voor Albedo, Metalness* |
| TransparentColor (TransparentColor) | *gebruikt voor alfakanaal van Albedo* |
| TransparantieFactor | *gebruikt voor alfakanaal van Albedo* |
| Dekking | *gebruikt voor alfakanaal van Albedo* |
| Spiegelkleur | *gebruikt voor Albedo, Metalness, Roughness* |
| SpecularFactor| *gebruikt voor Albedo, Metalness, Roughness* |
| ShininessExponent Shininess | *gebruikt voor Albedo, Metalness, Roughness* |
| Normalmap | Normalmap |
| Bump | *geconverteerd naar NormalMap* |
| EmissiveKleur | - |
| EmissiveFactor | - |
| ReflectieKleur | - |
| VerplaatsingKleur | - |

De mapping hierboven is het meest complexe deel van de materiaalconversie, vanwege de vele aannames die moeten worden gemaakt. We bespreken deze veronderstellingen hieronder.

Enkele definities die hieronder worden gebruikt:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Rood ∙ 0,2125 + `Specular`. Groen ∙ 0,7154 + `Specular`. Blauw ∙ 0,0721
* `DiffuseBrightness`= 0,299 `Diffuse`* . Rood<sup>2</sup> + 0,587 * `Diffuse`. Groen<sup>2</sup> + 0,114 * `Diffuse`. Blauw<sup>2</sup>
* `SpecularBrightness`= 0,299 `Specular`* . Rood<sup>2</sup> + 0,587 * `Specular`. Groen<sup>2</sup> + 0,114 * `Specular`. Blauw<sup>2</sup>
* `SpecularStrength`= max.`Specular` Rood, `Specular`. Groen, `Specular`. Blauw)

De formule SpecularIntensity wordt [hier](https://en.wikipedia.org/wiki/Luma_(video))vandaan verkregen.
De helderheidsformule wordt beschreven in deze [specificatie](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Ruwheid

`Roughness`wordt berekend `Specular` `ShininessExponent` op basis van en met behulp van [deze formule](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). De formule is een benadering van ruwheid van de Phong spiegelexponent:

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metaalheid

`Metalness`wordt berekend `Diffuse` `Specular` op basis van en met behulp van deze [formule uit de glTF-specificatie](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

Het idee hier is dat we de vergelijking oplossen: Ax<sup>2</sup> + Bx + C = 0.
Kortom, diëlektrische oppervlakken reflecteren ongeveer 4% van het licht op een spiegelende manier, en de rest is diffuus. Metalen oppervlakken reflecteren geen licht op een diffuse manier, maar allemaal op een spiegelende manier.
Deze formule heeft een paar nadelen, want er is geen manier om onderscheid te maken tussen glanzend plastic en glanzende metalen oppervlakken. We gaan ervan uit dat het oppervlak meestal metalen eigenschappen heeft, en daardoor zien glanzende plastic/rubberoppervlakken er misschien niet zoals verwacht uit.
```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo`wordt berekend `Diffuse`op `Specular`basis `Metalness`van , en .

Zoals beschreven in de sectie Metalness reflecteren diëlektrische oppervlakken ongeveer 4% van het licht.  
Het idee hier is om lineair `Dielectric` `Metal` interpoleren tussen en kleuren met behulp van `Metalness` waarde als een factor. Als de `0.0`metaalheid is, dan afhankelijk van spiegelende zal het ofwel een donkere kleur (als spiegelende is hoog) of diffuus zal niet veranderen (als er geen spiegelbaar aanwezig is). Als metaalheid is een grote waarde, dan is de diffuse kleur zal verdwijnen in het voordeel van spiegelende kleur.

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`is berekend door de bovenstaande formule, maar het alfakanaal vereist extra berekeningen. Het FBX-formaat is vaag over transparantie en heeft vele manieren om het te definiëren. Verschillende inhoudstools gebruiken verschillende methoden. Het idee hier is om ze te verenigen in een formule. Het maakt sommige activa onjuist weergegeven als transparant, hoewel, als ze niet zijn gemaakt op een gemeenschappelijke manier.

Dit wordt berekend `TransparentColor` `TransparencyFactor`aan `Opacity`de basis van :

als `Opacity` wordt gedefinieerd, gebruik het `AlbedoAlpha`  =  `Opacity` dan direct: anders  
als `TransparencyColor` wordt gedefinieerd, `AlbedoAlpha` dan = 1,0 - ((`TransparentColor`. Rood `TransparentColor`+ . Groen `TransparentColor`+ . Blauw) / 3.0) anders  
als `TransparencyFactor`, `AlbedoAlpha` dan = 1,0 -`TransparencyFactor`

De `Albedo` uiteindelijke kleur heeft vier `AlbedoRGB` kanalen, het combineren van de met de `AlbedoAlpha`.

### <a name="summary"></a>Samenvatting

Om hier `Albedo` samen te vatten, `Diffuse`zal `Specular` zeer dicht bij het origineel, als is dicht bij nul. Anders zal het oppervlak eruit zien als een metalen oppervlak en verliest de diffuse kleur. Het oppervlak ziet er meer `ShininessExponent` gepolijst en `Specular` reflecterend als groot genoeg is en is helder. Anders zal het oppervlak er ruw uitzien en nauwelijks de omgeving weerspiegelen.

### <a name="known-issues"></a>Bekende problemen

* De huidige formule werkt niet goed voor eenvoudige gekleurde geometrie. Als `Specular` is helder genoeg, dan zijn alle geometrieën worden reflecterende metalen oppervlakken zonder enige kleur. De tijdelijke oplossing hier `Specular` is om te verlagen tot 30% van het origineel of om de conversie-instelling [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)te gebruiken.
* PBR-materialen zijn `Maya` onlangs `3DS Max` toegevoegd aan en content creatie tools. Ze maken gebruik van aangepaste door de gebruiker gedefinieerde black-box eigenschappen om het door te geven aan FBX. Azure Remote Rendering leest deze extra eigenschappen niet omdat ze niet zijn gedocumenteerd en de indeling closed-source is.

## <a name="next-steps"></a>Volgende stappen

* [Modelconversie](../how-tos/conversion/model-conversion.md)
* [Overheersende materialen tijdens modelconversie](../how-tos/conversion/override-materials.md)
