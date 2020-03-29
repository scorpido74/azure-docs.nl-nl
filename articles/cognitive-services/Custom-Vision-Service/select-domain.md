---
title: Selecteer een domein voor een Custom Vision-project - Computer Vision
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u een domein voor uw project selecteren in de Aangepaste Vision-service.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1569b6081adad4cae0855f9adfb4e14e910bf819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899449"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Een domein selecteren voor een Custom Vision-project

In het instellingenblad voor uw Custom Vision-project u een domein voor uw project selecteren. Kies het domein dat het dichtst bij uw scenario ligt.

## <a name="image-classification"></a>Classificatie van afbeeldingen

|Domain|Doel|
|---|---|
|__Generieke__| Geoptimaliseerd voor een breed scala aan taken voor beeldclassificatie. Als geen van de andere domeinen geschikt is of als u niet zeker weet welk domein u moet kiezen, selecteert u het algemene domein.|
|__Voedsel__|Geoptimaliseerd voor foto's van gerechten zoals je ze zou zien op een restaurant menu. Als u foto's van individuele groenten of fruit wilt classificeren, gebruikt u het domein Voedsel.|
|__Bezienswaardigheden__|Geoptimaliseerd voor herkenbare oriëntatiepunten, zowel natuurlijk als kunstmatig. Dit domein werkt het beste wanneer het oriëntatiepunt duidelijk zichtbaar is op de foto. Dit domein werkt zelfs als het oriëntatiepunt lichtjes door mensen voor het wordt belemmerd.|
|__Retail__|Geoptimaliseerd voor afbeeldingen die worden gevonden in een winkelcatalogus of winkelwebsite. Als u wilt hoge precisie classificeren tussen jurken, broeken en shirts, gebruik dan dit domein.|
|__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van real-time classificatie op edge-apparaten.|

## <a name="object-detection"></a>Objectdetectie

|Domain|Doel|
|---|---|
|__Algemeen__| Geoptimaliseerd voor een breed scala aan objectdetectietaken. Als geen van de andere domeinen geschikt is of als u niet zeker weet welk domein u moet kiezen, selecteert u het algemene domein.|
|__Logo__|Geoptimaliseerd voor het vinden van merklogo's in afbeeldingen.|
|__Producten in de schappen__|Geoptimaliseerd voor het detecteren en classificeren van producten in de schappen.|
|__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van real-time objectdetectie op randapparaten.|

## <a name="compact-domains"></a>Compacte domeinen

De modellen die worden gegenereerd door compacte domeinen kunnen worden geëxporteerd om lokaal te worden uitgevoerd. De prestaties van het model variëren per geselecteerd domein. In de onderstaande tabel rapporteren we de modelgrootte en de gevolgtrekkingstijd op Intel Desktop CPU en NVidia GPU \[1\]. 

> [!NOTE]
> Deze nummers bevatten geen voorbewerkings- en nabewerkingstijd.

|Taak|Domain|Modelgrootte|CPU-gevolgtrekkingstijd|GPU-gevolgtrekkingstijd|
|---|---|---|---|---|
|Classificatie|Algemeen (compact)|5 MB|13 ms|5 ms|
|Objectdetectie|Algemeen (compact)|45 MB|35 ms|5 ms|
|Objectdetectie|Algemeen (compact) [S1]|14 MB|27 ms|7 ms|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision AI Dev Kit)

Wanneer een compact domein wordt geselecteerd, wordt een extra optie "Exportmogelijkheden" geboden, zodat onderscheid kan worden gemaakt tussen "Basisplatforms" en "Vision AI Dev Kit".

Onder _Exportmogelijkheden_ zijn de twee opties:

- Basisplatforms (Tensorflow, CoreML, ONNX, enz.)
- Visie AI Dev Kit.

Wanneer _Vision AI Dev Kit_ is geselecteerd, zijn de _algemene_, _oriëntatiepunten_en _detailhandelsdomeinen,_ maar niet de _compacte domeinen van Levensmiddelen_ beschikbaar voor beeldclassificatie, terwijl zowel algemene _(compacte)_ als _algemene (compacte) [S1]_ beschikbaar zijn voor objectdetectie.

>[!NOTE]
>__Algemeen (compact)__ domein voor Objectdetectie vereist speciale nabewerkingslogica. Zie voor de details een voorbeeldscript in het geëxporteerde zip-pakket. Als u een model zonder de nabewerkingslogica nodig hebt, gebruikt u __Algemeen (compact) [S1]__.

>[!IMPORTANT]
>Er is geen garantie dat de geëxporteerde modellen precies hetzelfde resultaat geven als de voorspellings-API in de cloud. Een klein verschil in het loopplatform of de voorbewerkingsimplementatie kan leiden tot een groter verschil in de modeluitvoer. Zie [dit document](python-tutorial.md)voor de details van de voorbewerkingslogica.

\[1\] Intel Xeon E5-2690 CPU en NVIDIA Tesla M60
