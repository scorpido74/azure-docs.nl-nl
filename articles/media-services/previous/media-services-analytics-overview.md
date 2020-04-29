---
title: Media Analytics op het Media Services-platform | Microsoft Docs
description: Overzicht van de open bare preview van Media Analytics, een verzameling spraak-en computer vision-Services op ENTER prise Scale, compliance, Security en Global REACH
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.reviewer: milanga; johndeu
ms.openlocfilehash: ab1eba3de474d9ff985e62f491c24fa63be0fa63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77069622"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics op het Media Services platform 

## <a name="retirement-plans"></a>Pensioen plannen

> [!IMPORTANT]
> Sommige media processors worden buiten gebruik gesteld. Zie het onderwerp [oudere onderdelen](legacy-components.md) voor de pensioen datums en meer informatie. 

## <a name="overview"></a>Overzicht

Meer organisaties gebruiken video als voorkeurs medium om hun werk nemers te trainen, hun klanten te laten deel nemen en zakelijke functies te documenteren. Cloud Computing biedt een manier om deze grote media bestanden op te slaan, te streamen en te openen. Maar als de bibliotheek met video-inhoud van een bedrijf groeit, heeft IT een even efficiënte manier nodig om inzichten uit de inhoud te halen. 

Azure Media Services biedt Azure Media Analytics om deze groeiende behoefte aan te pakken. Media Analytics is een verzameling spraakonderdelen en visuele onderdelen waarmee organisaties en bedrijven gemakkelijker inzichten kunnen verkrijgen op basis van hun video's, waarvoor een actie kan worden uitgevoerd. Media Analytics kan worden gemaakt met de kern onderdelen van Media Services platform en de media verwerking op de juiste schaal op elke dag kan verwerken.

Met Media Analytics kunnen ontwikkel aars snel geavanceerde video functionaliteit in toepassingen zetten. Het biedt bedrijfs omgevingen met de volledige schaal, naleving, beveiliging en wereld wijd bereik dat is vereist voor grote organisaties.

Het volgende diagram toont Media Analytics en andere belang rijke onderdelen van het Media Services platform. 

![VoD-werkstroom](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Media Analytics-mediaprocessoren produceren MP4- of JSON-bestanden. Als een media processor een MP4-bestand produceert, kunt u het bestand progressief downloaden. Als een media processor een JSON-bestand produceert, kunt u het bestand downloaden van Azure Blob-opslag. 

## <a name="media-analytics-services"></a>Media Analytics Services

### <a name="indexer"></a>Indexeerfunctie
Met Azure Media Indexer kunt u inhoud doorzoekbaar maken en ondertitelings tracks genereren. Zie [Media bestanden indexeren met Azure media indexer](media-services-index-content.md)voor gedetailleerde informatie en voor beelden.

### <a name="motion-detector"></a>Bewegingsherkenning
U kunt bewegings detector gebruiken om bewegingen in een video te detecteren met behulp van stationaire achtergronden. Dit maakt het mogelijk om te controleren op fout-positieven op bewegings gebeurtenissen die zijn gedetecteerd door bewakings camera's. Zie [bewegings detectie voor Azure Media Analytics](media-services-motion-detection.md)voor gedetailleerde informatie en voor beelden.

### <a name="face-detector"></a>Gezichtsherkenning
Door gebruik te maken van Face detector kunt u de gezichten van mensen en hun emoties, met inbegrip van blij, verdriet en verrassing, ontdekken. Dit heeft verschillende bruikbare toepassingen, die later worden beschreven, inclusief het samen voegen en analyseren van reacties van personen die een evenement bijwonen. Zie [gezichts-en Emotion detectie voor Azure Media Analytics](media-services-face-and-emotion-detection.md)voor meer informatie en voor beelden.

### <a name="video-summarization"></a>Video samenvatting
Video overzicht kan u helpen bij het maken van samen vattingen van lange Video's door automatisch interessante fragmenten te selecteren uit de bron video. Deze mogelijkheid is handig als u een kort overzicht wilt geven van wat u in een lange video kunt verwachten. Zie [Azure Media video thumbnails gebruiken om video samenvatting te maken](media-services-video-summarization.md)voor meer informatie en voor beelden.
### <a name="optical-character-recognition"></a>Optische tekenherkenning
Met Azure media OCR (optische teken herkenning) kunt u tekst inhoud in video bestanden converteren naar bewerkbaar, Doorzoek bare digitale tekst. U kunt vervolgens het uitpakken van zinvolle meta gegevens automatiseren vanuit het video signaal van uw media.
### <a name="scalable-face-redaction"></a>Schaal bare gezichts redactie
Azure Media Redactor is een Media Analytics media processor die schaal bare gezichts redactie in de Cloud biedt. Door gebruik te maken van gezichts redactie kunt u uw video aanpassen om gezichten van geselecteerde personen te vervagen. Mogelijk wilt u de gezichts redactie service gebruiken in nieuws media of wanneer de open bare veiligheid betrokken is. Een paar minuten van beeld materiaal dat meerdere gezichten bevat, kan uren duren om hand matig te worden geredigeerd, maar bij deze service nemen gezichts redactie slechts enkele eenvoudige stappen in beslag. Zie voor meer informatie de [reredactie-gezichten met Azure Media Analytics](media-services-face-redaction.md) -artikel.

### <a name="content-moderation"></a>Inhouds toezicht
Met Azure Content Moderator kunt u op uw Video's machine-ondersteunde toezicht gebruiken. Stel, u wilt mogelijk erotische en racistische inhoud detecteren in video's en de gemarkeerde inhoud laten controleren door uw team. Het hand matig toezicht op Video's voor ongewenste inhoud is een tijdrovende en dure taak. Met deze service en de bijbehorende beoordelings tools combineert u door de machine ondersteunde toezicht met Human-in-the-loop-mogelijkheden voor de beste resultaten efficiënt en rendabel. Zie het artikel [uw Video's verwerken met Azure content moderator](media-services-content-moderation.md) voor meer informatie.

## <a name="common-scenarios"></a>Algemene scenario's
Met Media Analytics kunnen organisaties en ondernemingen nieuwe inzichten van video beschikken en de grote hoeveel heden video-inhoud effectiever beheren. Hier volgen enkele scenario's:

* **Call centers**. Zelfs met de komst van sociale media, vereenvoudigt de klant oproep centrums een groot percentage klant-service transacties. De code ring in deze audio gegevens is een grote hoeveelheid klant gegevens die kan worden geanalyseerd om een hogere klant tevredenheid te krijgen. Met behulp van Media Indexer kunnen organisaties tekst extra heren en zoek indexen en dash boards bouwen. Ze kunnen vervolgens informatie ophalen over algemene klachten, klachten bronnen en andere relevante gegevens.
* Door de **gebruiker gegenereerde toezicht op inhoud**. Van nieuws media-uitgangen tot politie-afdelingen hebben veel organisaties open bare portals die door de gebruiker gegenereerde media, zoals Video's en installatie kopieën, accepteren. Het volume van de inhoud kan wegens onverwachte gebeurtenissen ontstaan. In deze scenario's is het moeilijk om effectief hand matige revisie van inhoud uit te voeren op de juiste manier. Klanten kunnen afhankelijk zijn van de service Content-moderator om te focussen op inhoud die van toepassing is.

## <a name="media-analytics-media-processors"></a>Media processors Media Analytics
In deze sectie vindt u een overzicht van de Media Analytics-media processors en wordt uitgelegd hoe u .NET of REST kunt gebruiken om een media processor (MP)-object te verkrijgen.

### <a name="mp-names"></a>MP-namen

* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
Met de volgende functie wordt een van de opgegeven MP-namen gebruikt en wordt een MP-object geretourneerd.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
Aanvraag:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.19
    Host: media.windows.net

Reactie:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Demo's
Zie [Azure Media Analytics demo's](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Verwante artikelen:
Zie [Media Services Analytics-aankondiging](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
