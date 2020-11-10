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
ms.openlocfilehash: 87b9e4a3ca7151b3666928b00add175eddeea050
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409379"
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

De modellen die door compacte domeinen worden gegenereerd, kunnen worden geëxporteerd om lokaal te worden uitgevoerd. In de open bare preview-API van Custom Vision 3,4 kunt u een lijst met de Exporteer bare platforms voor compacte domeinen verkrijgen door de GetDomains-API aan te roepen.

Model prestaties verschillen per geselecteerd domein. In de onderstaande tabel rapporteren we de model grootte en de tijd voor het afnemen van de Intel-Desktop-CPU en NVidia GPU \[ 1 \] . Deze getallen bevatten geen voorverwerkende en postprocessing tijd.

|Taak|Domain|Modelgrootte|Time-outtijd van CPU|Time-outtijd GPU|
|---|---|---|---|---|
|Classificatie|Algemeen (compact)|5 MB|13 MS|5 MS|
|Objectdetectie|Algemeen (compact)|45 MB|35 MS|5 MS|
|Objectdetectie|Algemeen (compact) [S1]|14 MB|27 MS|7 MS|

>[!NOTE]
>__Algemeen (compact)__ domein voor object detectie vereist speciale postprocessing-logica. Raadpleeg voor meer informatie een voorbeeld script in het geëxporteerde zip-pakket. Als u een model nodig hebt zonder de postprocessing Logic, gebruikt u __Algemeen (compact) [S1]__.

>[!IMPORTANT]
>Er is geen garantie dat de geëxporteerde modellen precies hetzelfde resultaat hebben als de Voorspellings-API in de Cloud. Een enigszins verschil in het actieve platform of de voor verwerking van de implementatie kan een groter verschil veroorzaken in de model uitvoer. Zie [dit document](quickstarts/image-classification.md)voor meer informatie over de logica voor voor verwerking.

\[1 \] Intel Xeon E5-2690 CPU en Nvidia Tesla M60
