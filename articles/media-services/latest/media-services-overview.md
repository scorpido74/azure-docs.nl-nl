---
title: Overzicht van Azure Media Services v3
titleSuffix: Azure Media Services
description: Een overzicht op hoog niveau van Azure Media Services v3 met koppelingen naar quickstarts, tutorials en codevoorbeelden.
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
ms.date: 03/09/2020
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: bd3890757377525cf9c178866a2a2fbc0791b9de
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79461007"
---
# <a name="azure-media-services-v3-overview"></a>Overzicht van Azure Media Services v3

Azure Media Services is een cloudgebaseerd platform waarmee u oplossingen bouwt voor het creëren van videostreaming van broadcast-kwaliteit, het verbeteren van toegankelijkheid en distributie, het analyseren van inhoud en nog veel meer. Of u nu een app-ontwikkelaar, een callcenter, een overheidsinstantie of een entertainmentbedrijf bent, Media Services helpt u apps te maken die media-ervaringen van uitstekende kwaliteit leveren aan een groot publiek op de populairste mobiele apparaten en browsers van vandaag.

De Media Services v3 SDKs zijn gebaseerd op [Media Services v3 OpenAPI Specification (Swagger).](https://aka.ms/ams-v3-rest-sdk)

> [!NOTE]
> Momenteel u de [Azure-portal](https://portal.azure.com/) gebruiken om: Media Services v3 [Live Events](live-events-outputs-concept.md)beheren, [v3-elementen](assets-concept.md)weergeven (niet beheren), [informatie krijgen over toegang tot API's](access-api-portal.md). Voor alle andere beheertaken (bijvoorbeeld [Transformaties en Taken](transforms-jobs-concept.md) en [Contentbescherming),](content-protection-overview.md)gebruikt u de [REST API](https://docs.microsoft.com/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Compliance, privacy en beveiliging

Als een belangrijke herinnering moet u voldoen aan alle toepasselijke wetten bij uw gebruik van Azure Media Services en mag u Media Services of een Azure-service niet gebruiken op een manier die de rechten van anderen schendt of die schadelijk kan zijn voor anderen.

Voordat u een video/afbeelding uploadt naar Media Services, moet u over alle juiste rechten beschikken om de video/afbeelding te gebruiken, inclusief, indien vereist door de wet, alle benodigde toestemmingen van personen (indien aanwezig) in de video/afbeelding, voor het gebruik, de verwerking en de opslag van hun gegevens in Media Services en Azure. Sommige rechtsgebieden kunnen speciale wettelijke vereisten opleggen voor het verzamelen, online verwerken en opslaan van bepaalde categorieën gegevens, zoals biometrische gegevens. Voordat u Media Services en Azure gebruikt voor de verwerking en opslag van gegevens die aan speciale wettelijke vereisten voldoen, moet u ervoor zorgen dat dergelijke wettelijke vereisten op u worden nageleefd.

Ga naar het Microsoft [Trust Center](https://www.microsoft.com/trust-center/?rtc=1)voor meer informatie over compliance, privacy en beveiliging in Media Services. Voor de privacyverplichtingen van Microsoft, gegevensverwerking en bewaarpraktijken, waaronder het verwijderen van uw gegevens, raadpleegt u de [privacyverklaring](https://privacy.microsoft.com/PrivacyStatement)van Microsoft, de [voorwaarden voor onlineservices](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) (OST) en [het addendum voor gegevensverwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA"). Door mediaservices te gebruiken, stemt u ermee in gebonden te zijn door de OST, DPA en de Privacyverklaring.
 
## <a name="what-can-i-do-with-media-services"></a>Wat kan ik doen met Media Services?

Met Media Services u verschillende mediaworkflows in de cloud bouwen. Enkele voorbeelden van wat u doen met Media Services zijn:

* Video's in verschillende indelingen aanbieden, zodat deze kunnen worden afgespeeld in een groot aantal browsers en op diverse apparaten. Voor zowel on-demand als live streaming levering aan verschillende clients (mobiele apparaten, tv, pc, enzovoort), moet de video- en audio-inhoud op de juiste manier worden gecodeerd en verpakt. Zie [Snelstartgids: Videobestanden streamen - .NET](stream-files-dotnet-quickstart.md) om te lezen hoe u dergelijke inhoud kunt leveren en streamen.
* Stream live sportevenementen voor een groot online publiek, zoals voetbal, honkbal, school- en middelbare schoolsporten en meer.
* Publieke vergaderingen en evenementen uitzenden, zoals gemeentehuizen, gemeenteraadsvergaderingen en wetgevende organen.
* Opgenomen videobeelden of audio-inhoud analyseren. Zo kunnen organisaties bijvoorbeeld de klanttevredenheid verbeteren door spraak-naar-tekst te extraheren en zoekindexen en dashboards samen te stellen. Vervolgens kunnen ze informatie over veelvoorkomende klachten, bronnen van klachten en andere relevante gegevens verzamelen.
* Een videoservice op abonnementsbasis maken en met DRM beveiligde inhoud streamen wanneer een klant (bijvoorbeeld een filmstudio) de toegang tot en het gebruik van bedrijfseigen, auteursrechtelijk beschermd werk moet beperken.
* Offline inhoud aanbieden voor afspelen in vliegtuigen, treinen en auto's. Klanten willen bijvoorbeeld inhoud downloaden naar hun telefoon of tablet, omdat ze weten dat ze later geen toegang hebben tot het netwerk en dan toch deze inhoud kunnen afspelen.
* Implementeer een educatief e-learning videoplatform met Azure Media Services en [Azure Cognitive Services API's](https://docs.microsoft.com/azure/?pivot=products&panel=ai) voor spraak-naar-tekst bijschriften, vertalen naar meerdere talen, enzovoort.
* Gebruik Azure Media Services in combinatie met [Azure Cognitive Services API's](https://docs.microsoft.com/azure/?pivot=products&panel=ai) om ondertitels en bijschriften toe te voegen aan video's om een breder publiek te bereiken (bijvoorbeeld mensen met een gehoorbeperking of personen die de video in een andere taal willen volgen).
* Schakel Azure CDN in om grote schaalbaarheid te bieden, zodat piekbelastingen beter kunnen worden verwerkt (bijvoorbeeld bij een productlancering).

## <a name="how-can-i-get-started-with-v3"></a>Hoe ga ik aan de slag met v3? 

Meer informatie over het coderen en verpakken van inhoud, het streamen van video's op aanvraag, live uitzenden en analyseren van je video's met Media Services v3. Zelfstudies, API-verwijzingen en andere documentatie laten zien hoe u veilig on-demand of live video- of audiostromen levert aan miljoenen gebruikers.

> [!TIP]
> Voordat u begint met ontwikkelen, bekijkt u:<br/>* [Fundamentele concepten](concepts-overview.md) (incudes belangrijke concepten, zoals verpakking, codering, en bescherming)<br/>* [Ontwikkelen met Media Services v3 API's](media-services-apis-overview.md) (inclusief informatie over toegang tot API's, naamgevingsconventies, enzovoort)

### <a name="sdks"></a>SDK's

Begin met het ontwikkelen met [Azure Media Services v3-client SDKs.](media-services-apis-overview.md#sdks)

### <a name="quickstarts"></a>Snelstartgidsen  

De quickstarts tonen fundamentele dag-1 instructies voor nieuwe klanten om mediaservices snel uit te proberen.

* [Videobestanden streamen - .NET](stream-files-dotnet-quickstart.md)
* [Videobestanden streamen - CLI](stream-files-cli-quickstart.md)
* [Videobestanden streamen - Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Zelfstudies

De zelfstudies tonen scenariogebaseerde procedures voor enkele van de belangrijkste Media Services-taken.

* [Externe bestanden coderen en video streamen - REST](stream-files-tutorial-with-rest.md)
* [Externe bestanden coderen en video streamen - .NET](stream-files-tutorial-with-api.md)
* [Live streamen - .NET](stream-live-tutorial-with-api.md)
* [Uw video analyseren - .NET](analyze-videos-tutorial-with-api.md)
* [Dynamische AES-128-versleuteling - .NET](protect-with-aes128.md)

### <a name="samples"></a>Voorbeelden

Gebruik [deze voorbeeldbrowser](https://docs.microsoft.com/samples/browse/?products=azure-media-services) om door codevoorbeelden van Azure Media Services te bladeren.

### <a name="how-to-guides"></a>Handleidingen

Handleidingen bevatten codevoorbeelden die aantonen hoe u een taak voltooien. In deze sectie vindt u vele voorbeelden. Hier zijn een paar van hen:

* [Een account maken - CLI](create-account-cli-how-to.md)
* [Toegang tot API's - CLI](access-api-cli-how-to.md)
* [Coderen met HTTPS als taakinvoer - .NET](job-input-from-http-how-to.md)  
* [Gebeurtenissen bewaken - Portal](monitor-events-portal-how-to.md)
* [Dynamisch versleutelen met multi-DRM - .NET](protect-with-drm.md) 
* [Hoe te coderen met een aangepaste transformatie - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over fundamentele concepten](concepts-overview.md)
