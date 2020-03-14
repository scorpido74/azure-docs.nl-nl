---
title: De Speech Devices SDK ophalen
titleSuffix: Azure Cognitive Services
description: De speech-service werkt met een groot aantal verschillende apparaten en audio bronnen. Nu kunt u uw spraaktoepassingen-naar een hoger niveau met overeenkomende hardware en software uitvoeren. In dit artikel leert u hoe u toegang krijgt tot de speech-apparaten SDK en hoe u kunt ontwikkelen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f8c1500dbbd9135a850e145199de8fea68cc4630
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220532"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>De Cognitive Services spraak apparaten SDK ophalen

De SDK voor spraak apparaten is een vooraf geclusterde bibliotheek die is ontworpen om te werken met doel ontwikkelde ontwikkel kits en verschillende matrix configuraties van microfoons.

## <a name="choose-a-development-kit"></a>Een Development Kit kiezen

|Apparaten|Specificatie|Beschrijving|Scenario 's|
|--|--|--|--|
|[Roobo slimme audio dev kit](https://ddk.roobo.com)<br>[Setup](speech-devices-sdk-roobo-v1.md) / [Quick](speech-devices-sdk-android-quickstart.md) start![roobo Smart audio dev kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7-Mic-matrix, ARM SOC, WIFI, audio out, IO. <br>[Android](speech-devices-sdk-android-quickstart.md)|De eerste speech-apparaten SDK voor het aanpassen van micro soft Mic-matrix en front processing SDK, voor het ontwikkelen van transcriptie-en spraak scenario's van hoge kwaliteit|Conversation transcriptie, Smart spreker, Voice agent, wearable|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br> / [Snelstartgids](speech-devices-sdk-windows-quickstart.md) [instellen](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7-microfoon matrix RGB en diepte camera's. <br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Een Developer Kit met geavanceerde kunst matige intelligentie (AI) Sens oren voor het bouwen van geavanceerde computer vision-en spraak modellen. Het combineert een ' best-in-class ' ruimtelijke microfoon matrix en diepte camera met een video camera en afdruk stand-sensor, allemaal op één klein apparaat met meerdere modi, opties en Sdk's om een bereik van reken typen te bieden.|Conversation transcriptie, Robotics, slim bouwen|
|Roobo slimme audio dev kit 2<br>[Instellen](speech-devices-sdk-roobo-v2.md)<br>![roobo Smart audio dev kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7-Mic-matrix, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|De tweede generatie speech-apparaten SDK die alternatieve besturings systemen en meer functies biedt in een kosteneffectd referentie ontwerp.|Conversation transcriptie, Smart spreker, Voice agent, wearable|
|URbetter T11 Development Board![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7-microfoon matrix, ARM SOC, WIFI, Ethernet, HDMI, USB-camera. <br>Linux|De SDK voor spraak apparaten op branche niveau die micro soft Mic-matrix aanpast en ondersteuning biedt voor uitgebreide I/O, zoals HDMI/Ethernet en meer USB-rand apparatuur|Transcriptie, onderwijs, zieken huis, robots, OTT box, Voice agent, drive t/m|

## <a name="download-the-speech-devices-sdk"></a>De spraak-apparaten SDK downloaden

Down load de [SDK voor spraak apparaten](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met de SDK voor spraak apparaten](https://aka.ms/sdsdk-quickstart)
