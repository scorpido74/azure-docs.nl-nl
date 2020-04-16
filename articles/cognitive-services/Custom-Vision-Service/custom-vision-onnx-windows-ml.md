---
title: 'Zelfstudie: Een ONNX-model gebruiken met Windows ML: Custom Vision Service'
titleSuffix: Azure Cognitive Services
description: Lees hoe u een Windows UWP-app maakt die gebruikmaakt van een ONNX-model dat uit Azure Cognitive Services is geëxporteerd.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0b59321bf04a8230342be706b88cd208c19d76ea
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404174"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Zelfstudie: Een ONNX-model uit Custom Vision gebruiken met Windows ML (preview)

Lees hoe u een ONNX-model gebruikt dat uit de Custom Vision Service is geëxporteerd met Windows ML (preview).

De informatie in dit document laat zien hoe u een ONNX-bestand gebruikt dat is geëxporteerd uit de Custom Vision Service met Windows ML. Er wordt een voorbeeld gegeven van een Windows UWP-toepassing. Het voorbeeld bevat een getraind model dat kan herkennen. Ook worden er stappen weergegeven om uw eigen model bij dit voorbeeld te gebruiken.

> [!div class="checklist"]
> * Over de voorbeeld-app
> * De voorbeeldcode halen
> * Het voorbeeld uitvoeren
> * Uw eigen model gebruiken

## <a name="prerequisites"></a>Vereisten

* Windows 10 versie 1809 of hoger

* Windows SDK voor build 17763 of hoger

* Visual Studio 2017 versie 15.7 of later waarbij de __ontwikkelworkload van Universal Windows Platform__ is ingeschakeld.

* Ontwikkelmodus ingeschakeld. Zie het document [Ontwikkeling voor uw apparaat inschakelen](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) voor meer informatie.

## <a name="about-the-example-app"></a>Over de voorbeeld-app

De toepassing is een generieke Windows UWP-toepassing. Hiermee kunt u een afbeelding selecteren vanaf uw computer, die vervolgens naar het model wordt verzonden. De tags en scores die door het model worden geretourneerd, worden weergegeven naast de afbeelding.

## <a name="get-the-example-code"></a>De voorbeeldcode halen

De voorbeeldtoepassing is [https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample)beschikbaar op .

## <a name="run-the-example"></a>Het voorbeeld uitvoeren

1. Gebruik de `F5`-sleutel om de toepassing te starten vanuit Visual Studio. U wordt mogelijk gevraagd de Ontwikkelmodus in te schakelen. Zie het document [Ontwikkeling voor uw apparaat inschakelen](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) voor meer informatie.

1. Wanneer de toepassing wordt gestart, moet u de knop gebruiken om een installatiekopie voor het scoren te selecteren.

## <a name="use-your-own-model"></a>Uw eigen model gebruiken

Als u uw eigen model wilt gebruiken, volgt u de volgende stappen:

1. [Maak en train](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) een classificatie met de Custom Vision Service. Om het model te exporteren, selecteert u een __compact__ domein, zoals **Algemeen (compact)**. Om een bestaande classificatie te exporteren, converteert u het domein naar compact door het tandwielpictogram in de rechterbovenhoek te selecteren. Kies in __Instellingen__ een compact model, sla op en train uw project.  

1. [Exporteer uw model](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) door naar het tabblad Prestaties te gaan. Selecteer een iteratie die is getraind met een compact domein en er verschijnt een knop 'Exporteren'. Selecteer *Exporteren*, *ONNX*en vervolgens *Exporteren*. Zodra het bestand gereed is, selecteert u de knop *Downloaden*.

1. Zet het bestand ONNX neer in de map __Assets__ van uw project. 

1. Klik in Solution Explorer met de rechtermuisknop op de map Assets en selecteer __Bestaand item toevoegen__. Selecteer het ONNX-bestand.

1. Selecteer in Solution Explorer het ONNX-bestand in de map Assets. Wijzig de volgende eigenschappen voor het bestand:

    * __Actie-inhoud__ -> __maken__
    * __Kopiëren naar uitvoermapkopiëren__ -> __als nieuwere__

1. Wijzig de variabele `_onnxFileNames` in de naam van het ONNX-bestand. Wijzig ook `ClassLabel` in het aantal labels dat het model bevat.

1. Bouw het model en voer het uit.

1. Klik op de knop om de afbeelding die u wilt evalueren te selecteren.

## <a name="next-steps"></a>Volgende stappen

Als u andere manieren wilt ontdekken om een Custom Vision-model te exporteren en te gebruiken, raadpleegt u de volgende documenten:

* [Uw model exporteren](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Geëxporteerd Tensorflow-model in een Android-toepassing gebruiken](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Geëxporteerd CoreML-model in een Swift iOS-toepassing gebruiken](https://go.microsoft.com/fwlink/?linkid=857726)
* [Geëxporteerd CoreML-model in een iOS-toepassing gebruiken met Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Voor meer informatie over het gebruik van ONNX-modellen met Windows ML raadpleegt u het document [Een model in uw app integreren met Windows ML](/windows/ai/windows-ml/integrate-model).
