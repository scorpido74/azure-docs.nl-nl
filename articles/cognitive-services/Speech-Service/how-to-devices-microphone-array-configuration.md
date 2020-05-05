---
title: Een microfoon matrix configureren-spraak service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het configureren van een microfoon matrix, zodat de SDK voor spraak apparaten deze kan gebruiken.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: a2652bed6c8e7dec0a6fe8f9471793c3873646bf
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82781755"
---
# <a name="how-to-configure-a-microphone-array"></a>Een microfoon matrix configureren

In dit artikel leert u hoe u een [microfoon matrix](https://aka.ms/sdsdk-microphone)kunt configureren. Het bevat het instellen van de werk hoek en het selecteren van de microfoon die wordt gebruikt voor de speech-apparaten SDK.

De SDK voor spraak apparaten werkt het beste met een microfoon matrix die is ontworpen volgens [onze richt lijnen](https://aka.ms/sdsdk-microphone). De configuratie van de microfoon matrix kan worden geleverd door het besturings systeem of worden geleverd via een van de volgende methoden.

De SDK voor spraak apparaten heeft eerst ondersteunde microfoon matrices door te selecteren uit een vaste set configuraties.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

In Windows wordt de configuratie van de microfoon matrix geleverd door het audio stuur programma.

De SDK voor spraak apparaten van v 1.11.0 ondersteunt ook de configuratie van een [JSON-bestand](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
In Windows wordt de geometrie gegevens van de microfoon matrix automatisch opgehaald uit het audio stuur programma. Dit betekent dat de `DeviceGeometry`eigenschappen `SelectedGeometry`, en `MicArrayGeometryConfigFile` optioneel zijn. We gebruiken het [JSON file](https://aka.ms/sdsdk-micarray-json) door `MicArrayGeometryConfigFile` gegeven JSON-bestand alleen voor het ophalen van het beamforming-bereik.

Als er een microfoon matrix wordt opgegeven `AudioConfig::FromMicrophoneInput`met, gebruiken we de opgegeven microfoon. Als een microfoon niet is opgegeven of `AudioConfig::FromDefaultMicrophoneInput` wordt aangeroepen, gebruiken we de standaard microfoon, die is opgegeven in geluids instellingen in Windows.
De micro soft audio-stack in de speech-apparaten SDK ondersteunt alleen de sampling voor voorbeeld frequenties die integraal veelvouden van 16 KHz zijn.

## <a name="linux"></a>Linux
In Linux moet de informatie over de geometrie van de microfoon worden verstrekt. Het gebruik van `DeviceGeometry` en `SelectedGeometry` blijft ondersteund. Het kan ook worden verschaft via het JSON-bestand met `MicArrayGeometryConfigFile` behulp van de eigenschap. Net als bij Windows kan het beamforming-bereik worden verstrekt door het JSON-bestand.

Als er een microfoon matrix wordt opgegeven `AudioConfig::FromMicrophoneInput`met, gebruiken we de opgegeven microfoon. Als een microfoon niet is opgegeven of `AudioConfig::FromDefaultMicrophoneInput` wordt aangeroepen, neemt u een record op van het alsa-apparaat met de naam *standaard*. Standaard verwijst *standaard* altijd naar kaart 0 apparaat 0, maar gebruikers kunnen dit wijzigen in het `asound.conf` bestand. 

De micro soft audio-stack in de speech-apparaten SDK ondersteunt alleen downsamplen voor voorbeeld frequenties die integraal veelvouden van 16 KHz zijn. Daarnaast worden de volgende indelingen ondersteund: 32-bits IEEE little endian float, 32-bits little endian ondertekende int, 24-bits little endian ondertekende int, 16-bits little endian ondertekende int en 8-bits ondertekende int.

## <a name="android"></a>Android
Momenteel wordt alleen [roobo v1](speech-devices-sdk-android-quickstart.md) ondersteund door de speech-apparaten SDK. Gedrag is hetzelfde als eerdere versies, met uitzonde ring van de eigenschap now `MicArrayGeometryConfigFile` kan worden gebruikt om een JSON-bestand met beamforming-bereik op te geven.

## <a name="microphone-array-configuration-json"></a>Configuratie van de microfoon matrix JSON

Het JSON-bestand voor de geometrie configuratie van de microfoon matrix volgt het [JSON-schema](https://aka.ms/sdsdk-micarray-json). Hieronder vindt u enkele voor beelden die volgen op het schema.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


of


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

of

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw spraak apparaat kiezen](get-speech-devices-sdk.md)
