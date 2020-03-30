---
title: Analyseer uw media met behulp van de Azure-portal | Microsoft Documenten
description: In dit onderwerp wordt besproken hoe u uw media verwerken met Media Analytics-mediaprocessors (Mp's) via de Azure-portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0e49e90209c7337081458b7c214d27b37d3b4da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74462611"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Uw media analyseren met behulp van de Azure-portal 

> [!IMPORTANT]
> Bekijk de [pensioenplannen](media-services-analytics-overview.md#retirement-plans) van sommige mediaverwerkers.

## <a name="overview"></a>Overzicht
Azure Media Services Analytics is een verzameling spraak- en visiecomponenten (op bedrijfsschaal, compliance, beveiliging en wereldwijd bereik) die het voor organisaties en ondernemingen gemakkelijker maken om bruikbare inzichten uit hun videobestanden te halen. Zie [dit](media-services-analytics-overview.md) onderwerp voor een gedetailleerder overzicht van Azure Media Services Analytics. 

In dit onderwerp wordt besproken hoe u uw media verwerken met Media Analytics-mediaprocessors (Mp's) via de Azure-portal. Media Analytics-parlementsleden produceren MP4-bestanden of JSON-bestanden. Als een mediaprocessor een MP4-bestand heeft geproduceerd, downloadt u het bestand geleidelijk. Als een mediaprocessor een JSON-bestand heeft geproduceerd, downloadt u het bestand uit de Azure blob-opslag. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Kies een asset die u wilt analyseren
1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer in het venster **Instellingen** de optie **Assets**.  
   
    ![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Selecteer het element dat u wilt analyseren en druk op de knop **Analyseren.**
   
    ![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Selecteer de processor in het **media-item Proces met Media Analytics.** 
   
    De rest van het artikel legt uit waarom en hoe elke processor te gebruiken. 
5. Druk op **Maken** om een taak te starten.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Met de **mediaprocessor Azure Media Indexer** u mediabestanden en inhoud doorzoekbaar maken en ondertitelingstracks genereren. In deze sectie vindt u enkele details over de opties die u voor dit MP opgeeft.

![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Taal
De natuurlijke taal die moet worden herkend in het multimediabestand. Bijvoorbeeld Engels of Spaans. 

### <a name="captions"></a>Bijschriften
U een bijschriftindeling kiezen die wordt gegenereerd uit uw inhoud. Een indexeringstaak kan bestanden met ondertiteling genereren in de volgende indelingen:  
 
* **TTML TTML**
* **WebvTT**

Cc-bestanden (Closed Caption) in deze indelingen kunnen worden gebruikt om audio- en videobestanden toegankelijk te maken voor mensen met een gehoorhandicap.

### <a name="keywords"></a>Trefwoorden
Selecteer deze optie als u een XML-bestand met trefwoorden wilt genereren. Dit bestand bevat trefwoorden uit de spraakinhoud, met frequentie- en offsetinformatie.

### <a name="job-name"></a>Taaknaam
Een vriendelijke naam waarmee u de taak identificeren. [In](media-services-portal-check-job-progress.md) dit artikel wordt beschreven hoe u de voortgang van een taak volgen. 

### <a name="output-file"></a>Uitvoerbestand
Een vriendelijke naam waarmee u de uitvoerinhoud identificeren. 

### <a name="speed"></a>Snelheid
Geef de snelheid op waarmee u de invoervideo versnellen. De uitvoer is een gestabiliseerde en tijdverzakte weergave van de invoervideo.

### <a name="job-name"></a>Taaknaam
Een vriendelijke naam waarmee u de taak identificeren. [In](media-services-portal-check-job-progress.md) dit artikel wordt beschreven hoe u de voortgang van een taak volgen. 

### <a name="output-file"></a>Uitvoerbestand
Een vriendelijke naam waarmee u de uitvoerinhoud identificeren. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
Met **de Azure Media Face Detector-mediaprocessor** (MP) u bewegingen tellen, bijhouden en zelfs de deelname en reactie van het publiek meten via gezichtsuitdrukkingen. Deze service bevat twee functies: 

* **Gezichtsherkenning**
  
    Gezichtsherkenning vindt en volgt menselijke gezichten in een video. Meerdere gezichten kunnen worden gedetecteerd en vervolgens worden bijgehouden terwijl ze bewegen, waarbij de tijd- en locatiemetagegevens worden geretourneerd in een JSON-bestand. Tijdens het volgen, zal het proberen om een consistente ID te geven aan hetzelfde gezicht, terwijl de persoon beweegt rond op het scherm, zelfs als ze worden belemmerd of kort het frame te verlaten.
  
  > [!NOTE]
  > Deze diensten voeren geen gezichtsherkenning uit. Een persoon die het frame verlaat of te lang wordt belemmerd, krijgt een nieuwe ID wanneer hij terugkeert.
  > 
  > 
* **Emotiedetectie**
  
    Emotion Detection is een optioneel onderdeel van de Face Detection Media Processor die de analyse van meerdere emotionele kenmerken van de gedetecteerde gezichten retourneert, waaronder geluk, verdriet, angst, woede en meer. 

![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Detectiemodus
Een van de volgende modi kan door de processor worden gebruikt:

* gezichtsherkenning
* per gezichtsemotiedetectie
* geaggregeerde emotiedetectie

### <a name="job-name"></a>Taaknaam
Een vriendelijke naam waarmee u de taak identificeren. [In](media-services-portal-check-job-progress.md) dit artikel wordt beschreven hoe u de voortgang van een taak volgen. 

### <a name="output-file"></a>Uitvoerbestand
Een vriendelijke naam waarmee u de uitvoerinhoud identificeren. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
Met **de Azure Media Motion Detector-mediaprocessor** (MP) u delen van belang efficiënt identificeren binnen een anders lange en rustige video. Bewegingsdetectie kan worden gebruikt op statische camerabeelden om delen van de video te identificeren waar beweging plaatsvindt. Het genereert een JSON-bestand met een metagegevens met tijdstempels en het grensgebied waar de gebeurtenis heeft plaatsgevonden.

Gericht op beveiligingsvideofeeds, is deze technologie in staat om beweging te categoriseren in relevante gebeurtenissen en false positives zoals schaduwen en lichtveranderingen. Hiermee u beveiligingswaarschuwingen genereren van camerafeeds zonder te worden gespamd met eindeloze irrelevante gebeurtenissen, terwijl u momenten van interesse extraheren uit extreem lange bewakingsvideo's.

![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Deze processor kan u helpen bij het maken van samenvattingen van lange video's door automatisch interessante fragmenten uit de bronvideo te selecteren. Dit is handig als u een snel overzicht wilt geven van wat u verwachten in een lange video. Zie Azure Media Video [Miniaturen gebruiken om een videosoming te maken voor](media-services-video-summarization.md) gedetailleerde informatie en voorbeelden.

![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Taaknaam
Een vriendelijke naam waarmee u de taak identificeren. [In](media-services-portal-check-job-progress.md) dit artikel wordt beschreven hoe u de voortgang van een taak volgen. 

### <a name="output-file"></a>Uitvoerbestand
Een vriendelijke naam waarmee u de uitvoerinhoud identificeren. 

## <a name="azure-media-content-moderator"></a>Azure Media-inhoudsmoderator
Deze processor helpt je potentiële inhoud voor volwassenen en racy in video's te detecteren. De processor detecteert automatisch opnamen en hoofdframes in de video. Het scoort de hoofdframes voor mogelijke inhoud voor volwassenen of racy en stelt beoordelingen voor op basis van standaarddrempels. Zie Azure Media Content [Moderator gebruiken om video's te modereren voor](media-services-content-moderation.md) gedetailleerde informatie en voorbeelden.

![Matige video's](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Versie 
Gebruik "2.0".

### <a name="mode"></a>Modus
De 2.0-versie `Mode` negeert de instelling.

## <a name="next-steps"></a>Volgende stappen
Leerpaden van Media Services weergeven.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
