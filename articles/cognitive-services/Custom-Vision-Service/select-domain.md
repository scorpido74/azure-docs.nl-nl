---
title: Selecteer een domein voor een Custom Vision project-Computer Vision
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u in de Custom Vision Service een domein selecteert voor uw project.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1fb30cc0634224213dc9a188a16902e07d379904
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82127780"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Een domein voor een Custom Vision project selecteren

Op de Blade instellingen voor uw Custom Vision project kunt u een domein voor uw project selecteren. Kies het domein dat zich het dichtst bij uw scenario bevindt.

## <a name="image-classification"></a>Classificatie van afbeeldingen

|Domain|Doel|
|---|---|
|__Algemeen__| Geoptimaliseerd voor een breed scala aan afbeeldingsclassificatietaken. Als geen van de andere domeinen geschikt is, of als u niet zeker weet welk domein u moet kiezen, selecteert u het domein Algemeen.|
|__Voedsel__|Geoptimaliseerd voor foto's van gerechten zoals op de menukaart van een restaurant. Gebruik het domein Voedsel als u foto's van afzonderlijke soorten fruit of groenten wilt classificeren.|
|__Oriëntatiepunten__|Geoptimaliseerd voor herkenbare oriëntatiepunten, zowel natuurlijke als kunstmatige. Dit domein werkt het beste wanneer het oriëntatiepunt duidelijk te zien is in de foto. Dit domein werkt ook als het oriëntatiepunt slechts gedeeltelijk zichtbaar is omdat er mensen voor staan.|
|__Retail__|Geoptimaliseerd voor afbeeldingen zoals die te vinden zijn in de catalogus of op de website van een winkel. Gebruik dit domein voor een nauwkeurige classificatie van kleding, zoals jurken, broeken en shirts.|
|__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van real-time classificatie op edge-apparaten.|

## <a name="object-detection"></a>Objectdetectie

|Domain|Doel|
|---|---|
|__Algemeen__| Geoptimaliseerd voor een breed scala aan objectdetectietaken. Als geen van de andere domeinen geschikt is of als u niet zeker weet welk domein u moet kiezen, selecteert u het domein Algemeen.|
|__Logo__|Geoptimaliseerd voor het vinden van merklogo's in afbeeldingen.|
|__Producten op schappen__|Geoptimaliseerd voor het detecteren en classificeren van producten op schappen.|
|__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van real-time object detectie op edge-apparaten.|

## <a name="compact-domains"></a>Compacte domeinen

De modellen die door compacte domeinen worden gegenereerd, kunnen worden geëxporteerd om lokaal te worden uitgevoerd. Model prestaties verschillen per geselecteerd domein. In de onderstaande tabel rapporteren we de model grootte en de tijd voor het afnemen van de Intel-Desktop-CPU en NVidia GPU \[ 1 \] . 

> [!NOTE]
> Deze getallen bevatten geen voorverwerkende en postprocessing tijd.

|Taak|Domain|Model grootte|Time-outtijd van CPU|Time-outtijd GPU|
|---|---|---|---|---|
|Classificatie|Algemeen (compact)|5 MB|13 MS|5 MS|
|Objectdetectie|Algemeen (compact)|45 MB|35 MS|5 MS|
|Objectdetectie|Algemeen (compact) [S1]|14 MB|27 MS|7 MS|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision AI dev kit)

Wanneer een compact domein is geselecteerd, wordt er een extra optie export mogelijkheden geboden, waardoor onderscheid kan worden gemaakt tussen ' Basic platforms ' en ' Vision AI dev kit '.

Onder _export mogelijkheden_ zijn de volgende twee opties:

- Basis platforms (tensor flow, CoreML, ONNX, etc.)
- Vision AI dev kit.

Als _Vision AI dev kit_ is geselecteerd, zijn de _algemene_, _bezienswaardigheden_en _detail handel_ , maar niet de _voedsel_ -compacte domeinen beschikbaar voor de classificatie van installatie kopieën, terwijl zowel _Algemeen (compact)_ als _Algemeen (compact) [S1]_ beschikbaar zijn voor object detectie.

>[!NOTE]
>__Algemeen (compact)__ domein voor object detectie vereist speciale postprocessing-logica. Raadpleeg voor meer informatie een voorbeeld script in het geëxporteerde zip-pakket. Als u een model nodig hebt zonder de postprocessing Logic, gebruikt u __Algemeen (compact) [S1]__.

>[!IMPORTANT]
>Er is geen garantie dat de geëxporteerde modellen precies hetzelfde resultaat hebben als de Voorspellings-API in de Cloud. Een enigszins verschil in het actieve platform of de voor verwerking van de implementatie kan een groter verschil veroorzaken in de model uitvoer. Zie [dit document](quickstarts/image-classification.md)voor meer informatie over de logica voor voor verwerking.

\[1 \] Intel Xeon E5-2690 CPU en Nvidia Tesla M60
