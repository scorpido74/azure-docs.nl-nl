---
title: Informatie over een geconverteerd model
description: Beschrijving van alle modelconversieparameters
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681517"
---
# <a name="get-information-about-a-converted-model"></a>Informatie over een geconverteerd model

Het arrAsset-bestand dat door de conversieservice wordt geproduceerd, is uitsluitend bedoeld voor gebruik door de renderingservice. Het kan echter soms zijn dat u toegang wilt tot informatie over een model zonder een renderingsessie te starten. Daarom plaatst de conversieservice een JSON-bestand naast het arrAsset-bestand in de uitvoercontainer. Als een bestand `buggy.gltf` bijvoorbeeld wordt geconverteerd, bevat `buggy.info.json` de uitvoercontainer `buggy.arrAsset`een bestand dat naast het geconverteerde element wordt genoemd. Het bevat informatie over het bronmodel, het geconverteerde model en over de conversie zelf.

## <a name="example-info-file"></a>*Voorbeeldinfobestand*

Hier volgt een voorbeeld *infobestand* dat is `buggy.gltf`geproduceerd door het converteren van een bestand genaamd:

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Informatie in het infobestand

### <a name="the-files-section"></a>De *sectie bestanden*

Deze sectie bevat de opgegeven bestandsnamen.

* `input`: De naam van het bronbestand.
* `output`: De naam van het uitvoerbestand, wanneer de gebruiker een niet-standaardnaam heeft opgegeven.

### <a name="the-conversionsettings-section"></a>De sectie *conversieInstellingen*

In deze sectie wordt een kopie van de [conversieinstellingen](configure-model-conversion.md#settings-file) bevat die zijn opgegeven toen het model werd geconverteerd.

### <a name="the-inputinfo-section"></a>De *sectie inputInfo*

In deze sectie worden informatie over de bronbestandsindeling opgenomen.

* `sourceAssetExtension`: De bestandsextensie van het bronbestand.
* `sourceAssetFormat`: Een beschrijving van de bronbestandsindeling.
* `sourceAssetFormatVersion`: De versie van de bronbestandsindeling.
* `sourceAssetGenerator`: De naam van het gereedschap dat het bronbestand heeft gegenereerd, indien beschikbaar.

### <a name="the-inputstatistics-section"></a>De sectie *inputStatistieken*

In deze sectie vindt u informatie over de bronscène. Er zullen vaak verschillen zijn tussen de waarden in deze sectie en de equivalente waarden in het gereedschap dat het bronmodel heeft gemaakt. Dergelijke verschillen worden verwacht, omdat het model wordt gewijzigd tijdens de export- en conversiestappen.

* `numMeshes`: Het aantal maasdelen, waarbij elk onderdeel naar één enkel materiaal kan verwijzen.
* `numFaces`: Het totale aantal _driehoeken_ in het hele model. Houd er rekening mee dat het gaas tijdens de conversie trianguleerd is.
* `numVertices`: Het totale aantal vertices in het hele model.
* `numMaterial`: Het totale aantal materialen in het hele model.
* `numFacesSmallestMesh`: Het aantal driehoeken in het kleinste gaas van het model.
* `numFacesBiggestMesh`: Het aantal driehoeken in de grootste mazen van het model.
* `numNodes`: Het aantal knooppunten in de scènegrafiek van het model.
* `numMeshUsagesInScene`: Het aantal keren dat knooppunten verwijzen naar mazen. Meer dan één knooppunt kan naar hetzelfde net verwijzen.
* `maxNodeDepth`: De maximale diepte van de knooppunten in de scènegrafiek.

### <a name="the-outputinfo-section"></a>De sectie *outputInfo*

In deze sectie worden algemene informatie over de gegenereerde uitvoer bijeen.

* `conversionToolVersion`: Versie van de modelconverter.
* `conversionHash`: Een hash van de gegevens binnen de arrAsset die kan bijdragen aan rendering. Kan worden gebruikt om te begrijpen of de conversieservice een ander resultaat heeft opgeleverd bij het opnieuw uitvoeren van hetzelfde bestand.

### <a name="the-outputstatistics-section"></a>De sectie *uitvoerstatistieken*

In deze sectie worden gegevens opdie zijn berekend op basis van het geconverteerde actief.

* `numMeshPartsCreated`: Het aantal mazen in de arrAsset. Het kan `numMeshes` verschillen `inputStatistics` van in de sectie, omdat instancing wordt beïnvloed door het conversieproces.
* `numMeshPartsInstanced`: Het aantal mazen dat wordt hergebruikt in de arrAsset.
* `recenteringOffset`: Wanneer `recenterToOrigin` de optie in de [conversieinstellingen](configure-model-conversion.md) is ingeschakeld, is deze waarde de vertaling die het geconverteerde model terug naar de oorspronkelijke positie zou verplaatsen.
* `boundingBox`: De grenzen van het model.

## <a name="next-steps"></a>Volgende stappen

* [Modelconversie](model-conversion.md)
* [De modelconversie configureren](configure-model-conversion.md)
