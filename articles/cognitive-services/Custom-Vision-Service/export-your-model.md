---
title: Uw model exporteren naar mobiel - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u uw model exporteren voor het maken van mobiele toepassingen of lokaal worden uitgevoerd voor realtime classificatie.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718950"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Uw model exporteren voor gebruik met mobiele apparaten

Met de aangepaste Vision-service kunnen classificaties worden geëxporteerd om offline te worden uitgevoerd. U uw geëxporteerde classificatie insluiten in een toepassing en deze lokaal uitvoeren op een apparaat voor realtime classificatie.

## <a name="export-options"></a>Exportopties

Custom Vision Service ondersteunt de volgende export:

* __Tensorflow__ voor __Android__.
* __CoreML__ voor __iOS11__.
* __ONNX__ voor __Windows ML__.
* __[Vision AI Developer Kit](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* Een __Docker-container__ voor Windows-, Linux- of ARM-architectuur. De container bevat een Tensorflow-model en servicecode om de Custom Vision API te gebruiken.

> [!IMPORTANT]
> Custom Vision Service exporteert alleen __compacte__ domeinen. De modellen die worden gegenereerd door compacte domeinen zijn geoptimaliseerd voor de beperkingen van real-time classificatie op mobiele apparaten. Classifiers gebouwd met een compact domein kan iets minder nauwkeurig dan een standaard domein met dezelfde hoeveelheid trainingsgegevens.
>
> Zie Het document [Beter uw classificatie](getting-started-improving-your-classifier.md) document verbeteren voor informatie over het verbeteren van uw classificatie.

## <a name="convert-to-a-compact-domain"></a>Converteren naar een compact domein

> [!NOTE]
> De stappen in deze sectie zijn alleen van toepassing als u een bestaand model hebt dat niet is ingesteld op compact domein.

Als u het domein van een bestaand model wilt converteren, neemt u de volgende stappen:

1. Selecteer op de [website Van Aangepaste visie](https://customvision.ai)het pictogram __Start__ om een lijst met uw projecten weer te geven.

    ![Afbeelding van het startpictogram en de lijst met projecten](./media/export-your-model/projects-list.png)

1. Selecteer een project en selecteer __het__ tandwielpictogram rechtsboven op de pagina.

    ![Afbeelding van het tandwielpictogram](./media/export-your-model/gear-icon.png)

1. Selecteer __in__ de sectie Domeinen een van de __compacte__ domeinen. Selecteer __Wijzigingen opslaan__ om de wijzigingen op te slaan. 

    > [!NOTE]
    > Voor Vision AI Dev Kit moet het project worden gemaakt met het domein __Algemeen (Compact)__ en moet u de optie **Vision AI Dev Kit** opgeven onder de sectie **Exportmogelijkheden.**

    ![Afbeelding van selectie domeinen](./media/export-your-model/domains.png)

1. Selecteer boven aan de pagina __Trainen__ om te trainen met het nieuwe domein.

## <a name="export-your-model"></a>Uw model exporteren

Als u het model na omscholing wilt exporteren, gebruikt u de volgende stappen:

1. Ga naar het tabblad **Prestaties** en selecteer __Exporteren__. 

    ![Afbeelding van het exportpictogram](./media/export-your-model/export.png)

    > [!TIP]
    > Als de __exportvermelding__ niet beschikbaar is, wordt in de geselecteerde iteratie geen compact domein gebruikt. Gebruik de sectie __Iteraties__ van deze pagina om een iteratie te selecteren die een compact domein gebruikt en selecteer __Vervolgens Exporteren__.

1. Selecteer de gewenste exportindeling en selecteer __Exporteren__ om het model te downloaden.

## <a name="next-steps"></a>Volgende stappen

Integreer uw geëxporteerde model in een toepassing door een van de volgende artikelen of voorbeelden te verkennen:

* [Gebruik uw Tensorflow-model met Python](export-model-python.md)
* [Uw ONNX-model gebruiken met Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Bekijk het voorbeeld voor [CoreML-model in een iOS-toepassing](https://go.microsoft.com/fwlink/?linkid=857726) voor real-time beeldclassificatie met Swift.
* Bekijk het voorbeeld voor [Tensorflow-model in een Android-toepassing](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) voor real-time beeldclassificatie op Android.
* Bekijk het voorbeeld voor [CoreML-model met Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) voor real-time beeldclassificatie in een Xamarin iOS-app.
