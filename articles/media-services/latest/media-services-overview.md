---
title: Overzicht van Azure Media Services v3
titleSuffix: Azure Media Services
description: Een overzicht op hoog niveau van Azure Media Services V3 met koppelingen naar Quick starts, zelf studies en code voorbeelden.
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
ms.openlocfilehash: 61ffd4857ca9a330a4cb0eeace89791fc0973f70
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575096"
---
# <a name="azure-media-services-v3-overview"></a>Overzicht van Azure Media Services v3

Azure Media Services is een cloudgebaseerd platform waarmee u oplossingen bouwt die videostreaming van broadcast-kwaliteit mogelijk maken, de toegankelijkheid en distributie verbeteren, inhoud analyseren en nog veel meer. Of u nu een app-ontwikkelaar, een oproep centrum, een overheids instantie of een entertainment bedrijf bent, Media Services helpt u bij het maken van apps die media-ervaringen van een uitstekende kwaliteit leveren aan grote doel groepen op de meest populaire mobiele apparaten en browsers.

De Media Services v3 Sdk's zijn gebaseerd op [Media Services v3 OpenAPI Specification (Swagger)](https://aka.ms/ams-v3-rest-sdk).

> [!NOTE]
> Op dit moment kunt u de Azure Portal niet gebruiken voor het beheren van v3-resources. Gebruik de [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).

## <a name="what-can-i-do-with-media-services"></a>Wat kan ik doen met Media Services?

Met Media Services kunt u diverse media werk stromen in de Cloud bouwen. Hieronder vindt u enkele voor beelden van wat u kunt doen met Media Services:

* Video's in verschillende indelingen aanbieden, zodat deze kunnen worden afgespeeld in een groot aantal browsers en op diverse apparaten. Voor zowel on-demand als live streaming-levering aan verschillende clients (mobiele apparaten, TV, PC, enzovoort) moet de video-en audio-inhoud worden gecodeerd en op de juiste manier worden verpakt. Zie [Snelstartgids: Videobestanden streamen - .NET](stream-files-dotnet-quickstart.md) om te lezen hoe u dergelijke inhoud kunt leveren en streamen.
* U kunt live sport gebeurtenissen streamen naar een grote online publiek, zoals voetbal, voetbal, College, school sport en meer.
* Open bare vergaderingen en gebeurtenissen, zoals stads huizen, steden vergaderingen en wetgevende instanties.
* Opgenomen videobeelden of audio-inhoud analyseren. Zo kunnen organisaties bijvoorbeeld de klanttevredenheid verbeteren door spraak-naar-tekst te extraheren en zoekindexen en dashboards samen te stellen. Vervolgens kunnen ze informatie over veelvoorkomende klachten, bronnen van klachten en andere relevante gegevens verzamelen.
* Een videoservice op abonnementsbasis maken en met DRM beveiligde inhoud streamen wanneer een klant (bijvoorbeeld een filmstudio) de toegang tot en het gebruik van bedrijfseigen, auteursrechtelijk beschermd werk moet beperken.
* Offline inhoud aanbieden voor afspelen in vliegtuigen, treinen en auto's. Klanten willen bijvoorbeeld inhoud downloaden naar hun telefoon of tablet, omdat ze weten dat ze later geen toegang hebben tot het netwerk en dan toch deze inhoud kunnen afspelen.
* Implementeer een educatief e-learning video platform met Azure Media Services en [Azure Cognitive Services-API's](https://docs.microsoft.com/azure/#pivot=products&panel=ai) voor ondertiteling van spraak naar tekst, vertalen naar meerdere talen, enzovoort.
* Gebruik Azure Media Services in combinatie met [Azure Cognitive Services API's](https://docs.microsoft.com/azure/#pivot=products&panel=ai) om ondertitels en bijschriften toe te voegen aan video's om een breder publiek te bereiken (bijvoorbeeld mensen met een gehoorbeperking of personen die de video in een andere taal willen volgen).
* Schakel Azure CDN in om grote schaalbaarheid te bieden, zodat piekbelastingen beter kunnen worden verwerkt (bijvoorbeeld bij een productlancering).

## <a name="how-can-i-get-started-with-v3"></a>Hoe ga ik aan de slag met v3? 

Meer informatie over het coderen en inpakken van inhoud, het streamen van Video's op aanvraag, live uitzenden en het analyseren van uw Video's met Media Services v3. Zelfstudies, API-verwijzingen en andere documentatie laten zien hoe u veilig on-demand of live video- of audiostromen levert aan miljoenen gebruikers.

> [!TIP]
> Controleer het volgende voordat u begint met ontwikkelen:<br/>* [basis concepten](concepts-overview.md) (incudes belang rijke concepten, zoals verpakking, code ring en bescherming)<br/>* [ontwikkelen met Media Services v3-api's](media-services-apis-overview.md) (bevat informatie over het openen van api's, naam conventies, enzovoort)

### <a name="quickstarts"></a>Snelstartgidsen  

In de Quick starts worden de belangrijkste dag-1 instructies voor nieuwe klanten weer gegeven om Media Services snel uit te proberen.

* [Video bestanden streamen-.NET](stream-files-dotnet-quickstart.md)
* [Video bestanden streamen-CLI](stream-files-cli-quickstart.md)
* [Video bestanden streamen: node. js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Zelfstudie

De zelf studies tonen op scenario's gebaseerde procedures voor een aantal van de belangrijkste Media Services taken.

* [Video van extern bestand en stream coderen – REST](stream-files-tutorial-with-rest.md)
* [Geüpload bestand en stream video coderen-.NET](stream-files-tutorial-with-api.md)
* [Live-.NET streamen](stream-live-tutorial-with-api.md)
* [Uw video analyseren-.NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dynamische versleuteling-.NET](protect-with-aes128.md)

### <a name="samples"></a>Voorbeelden

Gebruik [deze voorbeeld browser](https://docs.microsoft.com/samples/browse/?products=azure-media-services) om te bladeren door Azure Media Services code voorbeelden.

### <a name="how-to-guides"></a>Handleidingen

Instructies voor het uitvoeren van een taak zijn voorbeeld codes. In deze sectie vindt u veel voor beelden. Hier volgen enkele hiervan:

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

