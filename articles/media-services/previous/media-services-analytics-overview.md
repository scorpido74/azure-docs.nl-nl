---
title: Media Analytics op het Media Services-platform | Microsoft Documenten
description: Overzicht van een openbare preview van Media Analytics, een verzameling spraak- en computervision-services op bedrijfsschaal, compliance, beveiliging en wereldwijd bereik
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069622"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics op het Media Services-platform 

## <a name="retirement-plans"></a>Pensioenplannen

> [!IMPORTANT]
> Sommige mediaprocessors worden buiten gebruik gesteld. Zie het onderwerp [legacy-onderdelen](legacy-components.md) voor de pensioendata en meer informatie. 

## <a name="overview"></a>Overzicht

Meer organisaties gebruiken video als het voorkeursmedium om hun werknemers te trainen, hun klanten te betrekken en zakelijke functies te documenteren. Cloud computing biedt een manier om deze grote mediabestanden op te slaan, te streamen en te openen. Maar naarmate de bibliotheek van video-inhoud van een bedrijf groeit, heeft het een even effectief middel nodig om inzichten uit de inhoud te halen. 

Om aan deze groeiende behoefte tegemoet te komen, biedt Azure Media Services Azure Media Analytics. Media Analytics is een verzameling spraakonderdelen en visuele onderdelen waarmee organisaties en bedrijven gemakkelijker inzichten kunnen verkrijgen op basis van hun video's, waarvoor een actie kan worden uitgevoerd. Media Analytics is gebouwd met behulp van de belangrijkste mediaservices-platformcomponenten en kan op dag één op schaal mediaverwerking verwerken.

Met Media Analytics kunnen ontwikkelaars snel geavanceerde videofunctionaliteit in toepassingen brengen. Het biedt bedrijfsomgevingen met het volledige schaal, naleving, beveiliging en wereldwijd bereik dat grote organisaties nodig hebben.

Het volgende diagram toont Media Analytics en andere belangrijke onderdelen van het Media Services-platform. 

![VoD-werkstroom](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Media Analytics-mediaprocessoren produceren MP4- of JSON-bestanden. Als een mediaprocessor een MP4-bestand produceert, u het bestand geleidelijk downloaden. Als een mediaprocessor een JSON-bestand produceert, u het bestand downloaden uit Azure Blob-opslag. 

## <a name="media-analytics-services"></a>Media Analytics-services

### <a name="indexer"></a>Indexeerfunctie
Met Azure Media Indexer u inhoud doorzoekbaar maken en tracks met ondertiteling genereren. Zie [Mediabestanden indexeren met Azure Media Indexer](media-services-index-content.md)voor gedetailleerde informatie en voorbeelden.

### <a name="motion-detector"></a>Bewegingsherkenning
U Bewegingsdetector gebruiken om beweging in een video met stationaire achtergronden te detecteren. Dit maakt het mogelijk om te controleren op valse positieven op motion gebeurtenissen gedetecteerd door bewakingscamera's. Zie [Bewegingsdetectie voor Azure Media Analytics voor](media-services-motion-detection.md)gedetailleerde informatie en voorbeelden.

### <a name="face-detector"></a>Gezichtsherkenning
Door Face Detector te gebruiken, u de gezichten van mensen en hun emoties detecteren, inclusief geluk, verdriet en verrassing. Dit heeft verschillende nuttige toepassingen in de industrie, later beschreven, waaronder het aggregeren en analyseren van reacties van mensen die een evenement bijwonen. Zie [Gezichts- en emotiedetectie voor Azure Media Analytics voor](media-services-face-and-emotion-detection.md)gedetailleerde informatie en voorbeelden.

### <a name="video-summarization"></a>Videosummarisatie
Videosummarisatie kan u helpen bij het maken van samenvattingen van lange video's door automatisch interessante fragmenten uit de bronvideo te selecteren. Deze mogelijkheid is handig wanneer u een snel overzicht wilt geven van wat u verwachten in een lange video. Zie Azure Media Video [Miniaturen gebruiken om videosoming te maken voor](media-services-video-summarization.md)gedetailleerde informatie en voorbeelden.
### <a name="optical-character-recognition"></a>Optische tekenherkenning
Met Azure Media OCR (optische tekenherkenning) u tekstinhoud in videobestanden converteren naar bewerkbare, doorzoekbare digitale tekst. U vervolgens de extractie van zinvolle metadata van het videosignaal van uw media automatiseren.
### <a name="scalable-face-redaction"></a>Schaalbare gezichtsredactie
Azure Media Redactor is een Media Analytics-mediaprocessor die schaalbare gezichtsredactie biedt in de cloud. Door gezichtsredactie te gebruiken, u uw video wijzigen om gezichten van geselecteerde personen te vervagen. U de gezichtsredactieservice gebruiken in nieuwsmedia of wanneer de openbare veiligheid erbij betrokken is. Een paar minuten beeldmateriaal dat meerdere gezichten bevat, kan uren duren om handmatig te redten, maar met deze service neemt gezichtsredactie slechts een paar eenvoudige stappen. Zie het artikel [Redact-gezichten met Azure Media Analytics](media-services-face-redaction.md) voor meer informatie.

### <a name="content-moderation"></a>Inhoudsmatiging
Azure Content Moderator stelt u in staat om machine-assisted moderatie te gebruiken voor uw video's. Stel, u wilt mogelijk erotische en racistische inhoud detecteren in video's en de gemarkeerde inhoud laten controleren door uw team. Het handmatig modereren van video's voor ongewenste inhoud is een tijdrovende en dure taak. Met deze service en bijbehorende beoordelingstools combineert u machineondersteunde moderatie met human-in-the-loop-mogelijkheden voor de beste resultaten efficiënt en kosteneffectief. Zie het artikel [Uw video's verwerken met Azure Content Moderator](media-services-content-moderation.md) voor meer informatie.

## <a name="common-scenarios"></a>Algemene scenario's
Media Analytics kan organisaties en bedrijven helpen nieuwe inzichten uit video te halen en grote hoeveelheden videocontent effectiever te beheren. Hier volgen verschillende scenario's:

* **Call centers**. Zelfs met de komst van sociale media faciliteren callcenters nog steeds een groot percentage van de klantenservicetransacties. Gecodeerd in deze audiogegevens is een grote hoeveelheid klantinformatie die kan worden geanalyseerd om een hogere klanttevredenheid te bereiken. Met Behulp van Media Indexer kunnen organisaties tekst extraheren en zoekindexen en dashboards maken. Dan kunnen ze informatie verzamelen over veelvoorkomende klachten, bronnen van klachten en andere relevante gegevens.
* **Door gebruikers gegenereerde inhoudsmoderatie**. Van nieuwsmedia tot politiediensten, veel organisaties hebben publieke portals die door gebruikers gegenereerde media zoals video's en afbeeldingen accepteren. Het volume van de inhoud kan pieken als gevolg van onverwachte gebeurtenissen. In deze scenario's is het moeilijk om effectieve handmatige beoordelingen van inhoud uit te voeren op geschiktheid. Klanten kunnen vertrouwen op de content-moderatieservice om zich te concentreren op de inhoud die geschikt is.

## <a name="media-analytics-media-processors"></a>Media Analytics-mediaprocessors
In deze sectie worden de mediaprocessors van Media Analytics weergegeven en wordt weergegeven hoe u .NET of REST gebruiken om een MP-object (Media Processor) te krijgen.

### <a name="mp-names"></a>MP-namen

* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media-inhoudsmoderator

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

## <a name="demos"></a>Demos
Zie [Demo's van Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Verwante artikelen:
Zie [Aankondiging Media Services Analytics](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
