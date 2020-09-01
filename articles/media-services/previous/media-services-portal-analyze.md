---
title: Analyseer uw media met behulp van de Azure Portal | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u uw media met behulp van de Azure Portal met Media Analytics media-processors (MPs) kunt verwerken.
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
ms.openlocfilehash: 0bc8233110c02775e208470591c3e0c6eb619294
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260965"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analyseer uw media met behulp van de Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Bekijk de [pensioen plannen](media-services-analytics-overview.md#retirement-plans) van sommige media processors.

## <a name="overview"></a>Overzicht
Azure Media Services Analytics is een verzameling spraak-en visie onderdelen (op ENTER prise-schaal, naleving, beveiliging en wereld wijd bereik) waarmee organisaties en ondernemingen eenvoudig inzicht kunnen verkrijgen uit hun video bestanden. Zie [Dit](media-services-analytics-overview.md) onderwerp voor meer gedetailleerd overzicht van Azure Media Services Analytics. 

In dit onderwerp wordt beschreven hoe u uw media met behulp van de Azure Portal met Media Analytics media-processors (MPs) kunt verwerken. Media Analytics MPs produceert MP4-bestanden of JSON-bestanden. Als een media processor een MP4-bestand produceert, kunt u het bestand progressief downloaden. Als een media processor een JSON-bestand produceert, kunt u het bestand downloaden uit de Azure Blob-opslag. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Kies een Asset die u wilt analyseren
1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer in het venster **Instellingen** de optie **Assets**.  
   
    ![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Selecteer de activa die u wilt analyseren en druk op de knop **analyseren** .
   
    ![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Selecteer de processor in het venster **Media-activum verwerken met Media Analytics** . 
   
    In de rest van het artikel wordt uitgelegd waarom en hoe u elke processor gebruikt. 
5. Druk op **maken** om de taak te starten.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Met de **Azure media indexer** -media processor kunt u media bestanden en inhoud doorzoekbaar maken, evenals ondertitelings tracks genereren. In deze sectie vindt u enkele informatie over de opties die u voor dit Management Pack opgeeft.

![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Taal
De natuurlijke taal die moet worden herkend in het multimedia bestand. Bijvoorbeeld Engels of Spaans. 

### <a name="captions"></a>Bijschriften
U kunt een titel indeling kiezen die wordt gegenereerd op basis van uw inhoud. Een indexerings taak kan ondertitelings bestanden genereren in de volgende indelingen:  
 
* **TTML**
* **WebVTT**

Ondertitelings bestanden (CC) in deze indelingen kunnen worden gebruikt om audio-en video bestanden toegankelijk te maken voor mensen met een gehoor handicap.

### <a name="keywords"></a>Trefwoorden
Selecteer deze optie als u een XML-bestand met tref woorden wilt genereren. Dit bestand bevat tref woorden die zijn geëxtraheerd uit de spraak inhoud, met frequentie en verschuivings informatie.

### <a name="job-name"></a>Taaknaam
Een beschrijvende naam waarmee u de taak kunt identificeren. In [Dit](media-services-portal-check-job-progress.md) artikel wordt beschreven hoe u de voortgang van een taak kunt bewaken. 

### <a name="output-file"></a>Uitvoerbestand
Een beschrijvende naam waarmee u de uitvoer inhoud kunt identificeren. 

### <a name="speed"></a>Snelheid
Geef de snelheid op waarmee de invoer video wordt versneld. De uitvoer is een gestabiliseerde en tijdgebonden vertoning van de invoer video.

### <a name="job-name"></a>Taaknaam
Een beschrijvende naam waarmee u de taak kunt identificeren. In [Dit](media-services-portal-check-job-progress.md) artikel wordt beschreven hoe u de voortgang van een taak kunt bewaken. 

### <a name="output-file"></a>Uitvoerbestand
Een beschrijvende naam waarmee u de uitvoer inhoud kunt identificeren. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
Met de **Azure media face detector** media processor (MP) kunt u het aantal bewegingen en de inschakeling van de publieks-en reactie activiteiten via gezichts uitdrukkingen tellen, bijhouden en meten. Deze service bevat twee functies: 

* **Gezichtsdetectie**
  
    Met gezichts detectie wordt geconstateerd en getraceerd voor menselijke gezichten in een video. Er kunnen meerdere gezichten worden gedetecteerd en vervolgens worden gevolgd wanneer ze worden verplaatst, met de tijd en locatie-meta gegevens die zijn geretourneerd in een JSON-bestand. Tijdens het bijhouden wordt geprobeerd een consistente ID te geven aan hetzelfde gezicht, terwijl de persoon op het scherm gaat, zelfs als ze niet meer of korter zijn dan het frame.
  
  > [!NOTE]
  > Deze services voeren geen gezichts herkenning uit. Een persoon die het frame verlaat of gedurende te lang wordt belemmerd, krijgt een nieuwe ID wanneer deze wordt geretourneerd.
  > 
  > 
* **Emotion-detectie**
  
    Emotion Detection is een optioneel onderdeel van de Gezichtsdetectie media processor dat analyse op meerdere emotioneel-kenmerken van de gedetecteerde gezichten retourneert, waaronder blij, verdriet, vrezen, boosheid en meer. 

![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Detectie modus
Een van de volgende modi kan door de processor worden gebruikt:

* gezichts detectie
* Emotion detectie per gezicht
* cumulatieve Emotion-detectie

### <a name="job-name"></a>Taaknaam
Een beschrijvende naam waarmee u de taak kunt identificeren. In [Dit](media-services-portal-check-job-progress.md) artikel wordt beschreven hoe u de voortgang van een taak kunt bewaken. 

### <a name="output-file"></a>Uitvoerbestand
Een beschrijvende naam waarmee u de uitvoer inhoud kunt identificeren. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
Met de **Azure Media Motion detector** media processor (MP) kunt u op efficiënte wijze gedeelten van interesses identificeren binnen een andere, lange en niet-gebeurtenisloze video. Bewegings detectie kan worden gebruikt voor statische camera beelden om secties van de video te identificeren waar beweging optreedt. Er wordt een JSON-bestand gegenereerd met een meta gegevens met tijds tempels en het grens gebied waarin de gebeurtenis plaatsvond.

Deze technologie is gericht op beveiligings videofeeds en kan bewegingen in relevante gebeurtenissen categoriseren en fout-positieven, zoals scha duwen en belichtings veranderingen. Op deze manier kunt u beveiligings waarschuwingen genereren van camera feeds zonder spam met oneindig irrelevante gebeurtenissen, terwijl u de momenten van interesse kunt ophalen van extreem lange bewakings Video's.

![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Deze processor kan u helpen samen vattingen van lange Video's te maken door automatisch interessante fragmenten uit de bron video te selecteren. Dit is handig als u een kort overzicht wilt geven van wat u in een lange video kunt verwachten. Zie [Azure Media video thumbnails gebruiken om een video samenvatting te maken](media-services-video-summarization.md) voor meer informatie en voor beelden

![Video's analyseren](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Taaknaam
Een beschrijvende naam waarmee u de taak kunt identificeren. In [Dit](media-services-portal-check-job-progress.md) artikel wordt beschreven hoe u de voortgang van een taak kunt bewaken. 

### <a name="output-file"></a>Uitvoerbestand
Een beschrijvende naam waarmee u de uitvoer inhoud kunt identificeren. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
Deze processor helpt u potentiële inhoud van de volwassene en ongepaste in Video's te detecteren. De processor detecteert automatisch opnamen en keyframes in de video. Hiermee worden de keyframes voor mogelijke inhoud voor volwassenen of ongepaste beoordeeld en worden beoordelingen voorgesteld op basis van de standaard drempel waarden. Voor gedetailleerde informatie en voor beelden raadpleegt [u Azure Media Content moderator gebruiken voor gemiddeld aantal Video's](media-services-content-moderation.md)

![Gemiddeld Video's](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Versie 
Gebruik ' 2,0 '.

### <a name="mode"></a>Modus
De 2,0-versie negeren de `Mode` instelling.

## <a name="next-steps"></a>Volgende stappen
Media Services leer trajecten weer geven.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
