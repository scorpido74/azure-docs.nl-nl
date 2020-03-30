---
title: Geavanceerde coderingswerkstromen maken met Workflow Designer | Microsoft Documenten
description: Meer informatie over het maken van geavanceerde coderingswerkstromen met Workflow Designer.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 4dceb558532305c6d2e84563e25ab05508423090
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72801952"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Geavanceerde encoding-werkstromen maken met Workflow Designer  
## <a name="overview"></a>Overzicht
De **Workflow Designer** is een Windows-bureaubladtool die wordt gebruikt voor het ontwerpen en bouwen van aangepaste werkstromen voor codering met Media **Encoder Premium Workflow.**
Door gebruik te maken van de kracht van de workflow designer tool, u complexe workflows ontwerpen en maken die worden uitgevoerd in **Media Encoder Premium.**  

Werkstromen kunnen de logica van klantbeslissingen en vertakkingen bevatten op basis van de eigenschappen van het invoerbronbestand. U werkstromen maken met overridable eigenschappen en dynamische waarden om zelfs de meest complexe coderingstaken eenvoudig te herhalen en aan te passen in de cloud.

Voorbeeldwerkstromen die u maken, zijn onder andere:

* Op beslissingen gebaseerde workflows die de broninhoud inspecteren op oplossing en alleen de gewenste uitvoertracks coderen.  Dit is handig door het elimineren van de verspilde tracks die zouden worden gegenereerd door het opschalen van de broninhoud per ongeluk.
* Meerdere invoerbestanden kunnen worden gebruikt om bijschriften, overlays en stiksels samen inhoud te ondersteunen. 

Deze tool kan ook worden gebruikt om een van onze [gepubliceerde workflows](media-services-workflow-designer.md#existing_workflows)te wijzigen. 

> [!NOTE]
> Neem contact op met mepd@microsoft.comvoor uw exemplaar van de tool Workflow Designer .

Zodra een werkstroombestand is gemaakt, kan het worden geüpload als een asset en vervolgens worden gebruikt voor het coderen van mediabestanden. Zie [Geavanceerde codering met Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md)voor informatie over het coderen met Media **Encoder Premium Workflow.** **.NET**

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Bestaande werkstromen wijzigen
De standaard [gepubliceerde werkstromen](media-services-workflow-designer.md#existing_workflows) kunnen worden gewijzigd met behulp van het ontwerpgereedschap. U de standaardwerkstroombestanden [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)krijgen. De map bevat ook de beschrijving van deze bestanden.

De volgende video's laten zien hoe de ontwerper te gebruiken.

### <a name="day-1--getting-started"></a>Dag 1 – Aan de slag
Dag 1 video covers:

* Overzicht van ontwerpers
* Basisworkflows – "Hello World"
* Meerdere uitvoerMP4-bestanden maken voor gebruik met Streaming Azure Media Services

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dag 2
Dag 2 video covers:

* Verschillende scenario's voor bronbestanden – het verwerken van audio
* Werkstromen met geavanceerde logica
* Grafiekfasen

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dag 3
Dag 3 video covers:

* Scripting in werkstromen/blauwdrukken
* Beperkingen met de huidige Encoder
* Q&A

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Hebt u hulp nodig?

U een ondersteuningsticket openen door te navigeren naar [nieuw ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Volgende stap
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Trainingsvideo's van Azure Premium Encoder Workflow Designer](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

