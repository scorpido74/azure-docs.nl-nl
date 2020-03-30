---
title: Gezichten redacten met Azure Media Analytics-walkthrough | Microsoft Documenten
description: In dit onderwerp worden stap voor stap instructies weergegeven over het uitvoeren van een volledige redactiewerkstroom met Azure Media Services Explorer (AMSE) en Azure Media Redactor Visualizer (open source-tool).
services: media-services
documentationcenter: ''
author: Lichard
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: a8db8de6ef062dcf757f3d264379677d6550ea3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69997679"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Gezichten redact met Azure Media Analytics-walkthrough

## <a name="overview"></a>Overzicht

**Azure Media Redactor** is een [Azure Media Analytics](media-services-analytics-overview.md) mediaprocessor (MP) die schaalbare gezichtsredactie biedt in de cloud. Met gezichtsredactie u uw video wijzigen om gezichten van geselecteerde personen te vervagen. U de gezichtsredactieservice gebruiken in scenario's voor openbare veiligheid en nieuwsmedia. Een paar minuten beeldmateriaal dat meerdere gezichten bevat, kan uren duren om handmatig te redten, maar met deze service vereist het gezichtsredactieproces slechts een paar eenvoudige stappen. Voor meer informatie, zie [deze](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Zie het [overzichtsonderwerp Face redaction](media-services-face-redaction.md) voor meer informatie over **Azure Media Redactor.**

In dit onderwerp worden stap voor stap instructies weergegeven over het uitvoeren van een volledige redactiewerkstroom met Azure Media Services Explorer (AMSE) en Azure Media Redactor Visualizer (open source-tool).

Voor meer informatie, zie [deze](https://azure.microsoft.com/blog/redaction-preview-available-globally) blog.

## <a name="azure-media-services-explorer-workflow"></a>Azure Media Services Explorer-werkstroom

De eenvoudigste manier om aan de slag te gaan met Redactor is door de open source AMSE-tool op GitHub te gebruiken. U een vereenvoudigde workflow uitvoeren via **de gecombineerde** modus als u geen toegang nodig hebt tot de annotatiejson of de afbeeldingen van Face Jpg.

### <a name="download-and-setup"></a>Downloaden en instellen

1. Download de AMSE voor AMS v2 tool vanaf [hier](https://aka.ms/amseforv2).
1. Meld u aan bij uw Media Services-account met uw servicesleutel.

    Voor het verkrijgen van de accountnaam en sleutelinformatie gaat u naar de [Azure Portal](https://portal.azure.com/) en selecteert u uw AMS-account. Selecteer vervolgens Instellingen > Toetsen. In het venster Sleutels beheren worden de accountnaam en de primaire en secundaire sleutel weergegeven. Kopieer de waarden van de accountnaam en de primaire sleutel.

![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Eerste pass – analysemodus

1. Upload je mediabestand via Asset -> Upload, of via slepen en neerzetten. 
1. Klik met de rechtermuisknop op en verwerk uw mediabestand met Media Analytics -> Azure Media Redactor - > analysemodus. 


![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

De uitvoer bevat een annotaties json-bestand met gezichtslocatiegegevens, evenals een jpg van elk gedetecteerd gezicht. 

![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Tweede pas – redactmodus

1. Upload uw oorspronkelijke video-asset naar de uitvoer vanaf de eerste pas en stel in als een primaire asset. 

    ![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Optioneel) Upload een bestand 'Dance_idlist.txt' met een nieuwe regel van de id's die u wilt redacten. 

    ![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Optioneel) Bewerkingen aanbrengen in het bestand annotaties.json, zoals het verhogen van de grenzen van het selectiekader. 
4. Klik met de rechtermuisknop op het uitvoerelement vanaf de eerste pass, selecteer de Redactor en voer uit met de **Redact-modus.** 

    ![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Download of deel het uiteindelijke geredigeerde uitvoeritem. 

    ![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Open source-hulpprogramma Azure Media Redactor Visualizer

Een open source [visualizer tool](https://github.com/Microsoft/azure-media-redactor-visualizer) is ontworpen om ontwikkelaars te helpen gewoon te beginnen met de annotaties formaat met ontwijs en het gebruik van de output.

Nadat u de repo kloon, om het project uit te voeren, moet u FFMPEG downloaden van hun [officiële site](https://ffmpeg.org/download.html).

Als u een ontwikkelaar bent die de JSON-annotatiegegevens probeert te ontleden, kijkt u in Models.MetaData naar voorbeeldvoorbeelden van voorbeeldcodes.

### <a name="set-up-the-tool"></a>Het gereedschap instellen

1.  Download en bouw de hele oplossing. 

    ![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Download FFMPEG vanaf [hier](https://ffmpeg.org/download.html). Dit project werd oorspronkelijk ontwikkeld met versie be1d324 (2016-10-04) met statische koppeling. 
3.  Kopieer ffmpeg.exe en ffprobe.exe naar dezelfde uitvoermap als AzureMediaRedactor.exe. 

    ![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. AzureMediaRedactor.exe uitvoeren. 

### <a name="use-the-tool"></a>Het gereedschap gebruiken

1. Verwerk uw video in uw Azure Media Services-account met de Redactor MP in de analysemodus. 
2. Download zowel het originele videobestand als de uitvoer van de taak Redactie - Analyseren. 
3. Voer de visualizer-toepassing uit en kies de bovenstaande bestanden. 

    ![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Bekijk een voorbeeld van uw bestand. Selecteer welke gezichten u wilt vervagen via de zijbalk aan de rechterkant. 
    
    ![Gezichten onherkenbaar maken](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Het onderste tekstveld wordt bijgewerkt met de gezichts-id's. Maak een bestand genaamd "idlist.txt" met deze ID's als een nieuwe lijn afgebakende lijst. 

    >[!NOTE]
    > De idlist.txt moet worden opgeslagen in ANSI. U kladblok gebruiken om op te slaan in ANSI.
    
6.  Upload dit bestand naar het uitvoeritem vanaf stap 1. Upload de originele video ook naar dit item en stel deze in als primaire troef. 
7.  Run Redaction taak op deze asset met "Redact" modus om de laatste geredigeerde video te krijgen. 

## <a name="next-steps"></a>Volgende stappen 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwante koppelingen
[Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md)

[Azure Media Analytics-demo's](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Face Redaction aankondigen voor Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)
