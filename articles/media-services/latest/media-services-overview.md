---
title: Overzicht van Azure Media Services v3 | Microsoft Docs
description: Dit artikel biedt een algemeen overzicht van Media Services en bevat koppelingen naar artikelen voor meer informatie.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, uitzenden, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 09/17/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: a752d7759386828e916f68e5903115220812f3e0
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123045"
---
# <a name="azure-media-services-v3-overview"></a>Overzicht van Azure Media Services v3

Azure Media Services is een cloudgebaseerd platform waarmee u oplossingen bouwt die videostreaming van broadcast-kwaliteit mogelijk maken, de toegankelijkheid en distributie verbeteren, inhoud analyseren en nog veel meer. Ongeacht of u een ontwikkelaar van toepassingen, een callcenter, een overheidsinstelling of een entertainmentbedrijf bent, is Media Services het perfecte hulpmiddel bij het maken van toepassingen die media-ervaringen van uitzonderlijke kwaliteit willen aanbieden aan grote doelgroepen, op alle populaire mobiele apparaten en in de meestgebruikte browsers van dit moment. 

> [!NOTE]
> U kunt momenteel geen gebruik maken van de Azure-portal om v3-resources te beheren. Gebruik de [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).

## <a name="what-can-i-do-with-media-services"></a>Wat kan ik doen met Media Services?

Met Media Services kunt u diverse mediawerkstromen bouwen in de cloud. Hier volgen enkele voorbeelden van wat u kunt doen met Media Services.  

* Video's in verschillende indelingen aanbieden, zodat deze kunnen worden afgespeeld in een groot aantal browsers en op diverse apparaten. Voor zowel on demand als live streaming naar verschillende clients (mobiele apparaten, tv, pc, enzovoort) geldt dat de video- en audio-inhoud op de juiste manier moet worden gecodeerd en verpakt. Zie [Snelstart: Bestanden coderen en streamen](stream-files-dotnet-quickstart.md) om te lezen hoe u dergelijke inhoud kunt leveren en streamen.
* Live sportevenementen streamen naar een groot online publiek, zoals voetbalwedstrijden, schaatswedstrijden en grote kampioenschappen. 
* Openbare vergaderingen en evenementen uitzenden, zoals gemeenteraadsvergaderingen, debatten of verkiezingen.
* Opgenomen videobeelden of audio-inhoud analyseren. Zo kunnen organisaties bijvoorbeeld de klanttevredenheid verbeteren door spraak-naar-tekst te extraheren en zoekindexen en dashboards samen te stellen. Vervolgens kunnen ze informatie over veelvoorkomende klachten, bronnen van klachten en andere relevante gegevens verzamelen.
* Een videoservice op abonnementsbasis maken en met DRM beveiligde inhoud streamen wanneer een klant (bijvoorbeeld een filmstudio) de toegang tot en het gebruik van bedrijfseigen, auteursrechtelijk beschermd werk moet beperken.
* Offline inhoud aanbieden voor afspelen in vliegtuigen, treinen en auto's. Klanten willen bijvoorbeeld inhoud downloaden naar hun telefoon of tablet, omdat ze weten dat ze later geen toegang hebben tot het netwerk en dan toch deze inhoud kunnen afspelen.
* Een videoplatform voor e-learning implementeren met Azure Media Services en [API's van Azure Cognitive Servicess](https://docs.microsoft.com/azure/#pivot=products&panel=ai) voor spraak-naar-tekst ondertiteling, vertalen naar meerdere talen, enzovoort. 
* Gebruik Azure Media Services in combinatie met [Azure Cognitive Services API's](https://docs.microsoft.com/azure/#pivot=products&panel=ai) om ondertitels en bijschriften toe te voegen aan video's om een breder publiek te bereiken (bijvoorbeeld mensen met een gehoorbeperking of personen die de video in een andere taal willen volgen).
* Schakel Azure CDN in om grote schaalbaarheid te bieden, zodat piekbelastingen beter kunnen worden verwerkt (bijvoorbeeld bij een productlancering). 

## <a name="how-can-i-get-started-with-v3"></a>Hoe ga ik aan de slag met v3? 

Informatie over coderen en verpakken van content, video-streaming op aanvraag, live uitzenden en het analyseren van uw video's met Media Services v3. Zelfstudies, API-verwijzingen en andere documentatie laten zien hoe u veilig on-demand of live video- of audiostromen levert aan miljoenen gebruikers.

> [!TIP]
> Controleer het volgende voordat u begint met ontwikkelen:<br/>* [Basis concepten](concepts-overview.md) (incudes belang rijke concepten: verpakking, code ring, beveiliging, enz.)<br/>* [Ontwikkelen met Media Services v3-api's](media-services-apis-overview.md) (bevat informatie over het openen van Api's, naam conventies, enzovoort)

### <a name="quickstarts"></a>Snelstartgidsen  

In de Quick starts worden de belangrijkste dag-1 instructies voor nieuwe klanten weer gegeven om Media Services snel uit te proberen.

* [Video bestanden streamen-.NET](stream-files-dotnet-quickstart.md)
* [Video bestanden streamen-CLI](stream-files-cli-quickstart.md)
* [Video bestanden streamen: node. js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Zelfstudies 

De zelf studies tonen op scenario's gebaseerde procedures voor een aantal van de belangrijkste Media Services taken.

* [Video van extern bestand en stream coderen – REST](stream-files-tutorial-with-rest.md)
* [Geüpload bestand en stream video coderen-.NET](stream-files-tutorial-with-api.md)
* [Live-.NET streamen](stream-live-tutorial-with-api.md)
* [Uw video analyseren-.NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dynamische versleuteling-.NET](protect-with-aes128.md)
    
### <a name="samples"></a>Voorbeelden

Gebruik [deze voorbeeld browser](https://docs.microsoft.com/samples/browse/?products=azure-media-services) om te bladeren door Azure Media Services code voorbeelden.

### <a name="how-to-guides"></a>Handleidingen

Artikelen bevatten code voorbeelden die laten zien hoe u een taak kunt uitvoeren. In deze sectie vindt u een groot aantal voor beelden, maar hier zijn er slechts enkele:

* [Een account maken - CLI](create-account-cli-how-to.md)
* [Toegang tot API's - CLI](access-api-cli-how-to.md)
* [Coderen met HTTPS als taak invoer-.NET](job-input-from-http-how-to.md)  
* [Gebeurtenissen bewaken-Portal](monitor-events-portal-how-to.md)
* [Dynamisch versleutelen met multi-DRM-.NET](protect-with-drm.md) 
* [Coderen met een aangepaste transform-CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over basis concepten](concepts-overview.md)

