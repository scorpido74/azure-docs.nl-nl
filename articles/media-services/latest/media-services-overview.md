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
ms.date: 03/09/2020
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 661b4dd5364e9c823f9a77ac175c9a8eca03b8a5
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968974"
---
# <a name="azure-media-services-v3-overview"></a>Overzicht van Azure Media Services v3

Azure Media Services is een cloudgebaseerd platform waarmee u oplossingen bouwt die videostreaming van broadcast-kwaliteit mogelijk maken, de toegankelijkheid en distributie verbeteren, inhoud analyseren en nog veel meer. Of u nu een app-ontwikkelaar, een oproep centrum, een overheids instantie of een entertainment bedrijf bent, Media Services helpt u bij het maken van apps die media-ervaringen van een uitstekende kwaliteit leveren aan grote doel groepen op de meest populaire mobiele apparaten en browsers.

De Media Services v3 Sdk's zijn gebaseerd op [Media Services v3 OpenAPI Specification (Swagger)](https://aka.ms/ams-v3-rest-sdk).

> [!NOTE]
> U kunt de [Azure Portal](https://portal.azure.com/) gebruiken om v3 [Live-gebeurtenissen](live-events-outputs-concept.md)te beheren, v3- [assets](assets-concept.md)weer te geven, informatie over het openen van api's op te halen. Gebruik voor alle andere beheer taken (bijvoorbeeld trans formaties en taken) de [rest API](https://aka.ms/ams-v3-rest-ref), [cli](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [sdk's](media-services-apis-overview.md#sdks).

## <a name="compliance-privacy-and-security"></a>Compliance, privacy en beveiliging

Als belang rijke herinnering moet u zich houden aan alle toepasselijke wetgeving bij het gebruik van Azure Media Services en mag u Media Services of een Azure-service niet gebruiken op een manier die de rechten van anderen schendt of die schadelijk voor anderen kunnen zijn.

Voordat u een video/afbeelding naar Media Services uploadt, moet u over alle juiste rechten beschikken voor het gebruik van de video/afbeelding, inclusief, indien vereist door de wet, alle vereiste mede werkers (indien van toepassing) in de video/afbeelding, voor gebruik, verwerking en opslag van hun gegevens in Media Services en Azure. Sommige jurisdicties kunnen speciale wettelijke vereisten opleggen voor het verzamelen, online verwerken en opslaan van bepaalde gegevens categorieën, zoals biometrische gegevens. Voordat u Media Services en Azure gebruikt voor het verwerken en opslaan van gegevens die onder bijzondere wettelijke vereisten vallen, moet u ervoor zorgen dat u voldoet aan de wettelijke vereisten die voor u van toepassing kunnen zijn.

Ga naar het [vertrouwens centrum](https://www.microsoft.com/trust-center/?rtc=1)van micro soft voor meer informatie over naleving, privacy en beveiliging in Media Services. Raadpleeg de privacyverklaring van micro soft voor de privacy van micro soft, het verwerken en bewaren van gegevens, inclusief de manier waarop u uw gegevens kunt verwijderen, de [Privacy verklaring](https://privacy.microsoft.com/PrivacyStatement), de [voor waarden voor Online Services](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("Ost") en de [gegevens verwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Door Media Services te gebruiken, gaat u ermee akkoord dat u bent gebonden aan de OST, DPA en de privacyverklaring.
 
## <a name="what-can-i-do-with-media-services"></a>Wat kan ik doen met Media Services?

Met Media Services kunt u diverse media werk stromen in de Cloud bouwen. Hieronder vindt u enkele voor beelden van wat u kunt doen met Media Services:

* Video's in verschillende indelingen aanbieden, zodat deze kunnen worden afgespeeld in een groot aantal browsers en op diverse apparaten. Voor zowel on-demand als live streaming-levering aan verschillende clients (mobiele apparaten, TV, PC, enzovoort) moet de video-en audio-inhoud worden gecodeerd en op de juiste manier worden verpakt. Zie [Snelstartgids: Videobestanden streamen - .NET](stream-files-dotnet-quickstart.md) om te lezen hoe u dergelijke inhoud kunt leveren en streamen.
* U kunt live sport gebeurtenissen streamen naar een grote online publiek, zoals voetbal, voetbal, College, school sport en meer.
* Open bare vergaderingen en gebeurtenissen, zoals stads huizen, steden vergaderingen en wetgevende instanties.
* Opgenomen videobeelden of audio-inhoud analyseren. Zo kunnen organisaties bijvoorbeeld de klanttevredenheid verbeteren door spraak-naar-tekst te extraheren en zoekindexen en dashboards samen te stellen. Vervolgens kunnen ze informatie over veelvoorkomende klachten, bronnen van klachten en andere relevante gegevens verzamelen.
* Een videoservice op abonnementsbasis maken en met DRM beveiligde inhoud streamen wanneer een klant (bijvoorbeeld een filmstudio) de toegang tot en het gebruik van bedrijfseigen, auteursrechtelijk beschermd werk moet beperken.
* Offline inhoud aanbieden voor afspelen in vliegtuigen, treinen en auto's. Klanten willen bijvoorbeeld inhoud downloaden naar hun telefoon of tablet, omdat ze weten dat ze later geen toegang hebben tot het netwerk en dan toch deze inhoud kunnen afspelen.
* Implementeer een educatief e-learning video platform met Azure Media Services en [Azure Cognitive Services-API's](https://docs.microsoft.com/azure/?pivot=products&panel=ai) voor ondertiteling van spraak naar tekst, vertalen naar meerdere talen, enzovoort.
* Gebruik Azure Media Services in combinatie met [Azure Cognitive Services API's](https://docs.microsoft.com/azure/?pivot=products&panel=ai) om ondertitels en bijschriften toe te voegen aan video's om een breder publiek te bereiken (bijvoorbeeld mensen met een gehoorbeperking of personen die de video in een andere taal willen volgen).
* Schakel Azure CDN in om grote schaalbaarheid te bieden, zodat piekbelastingen beter kunnen worden verwerkt (bijvoorbeeld bij een productlancering).

## <a name="how-can-i-get-started-with-v3"></a>Hoe ga ik aan de slag met v3? 

Meer informatie over het coderen en inpakken van inhoud, het streamen van Video's op aanvraag, live uitzenden en het analyseren van uw Video's met Media Services v3. Zelfstudies, API-verwijzingen en andere documentatie laten zien hoe u veilig on-demand of live video- of audiostromen levert aan miljoenen gebruikers.

> [!TIP]
> Controleer het volgende voordat u begint met ontwikkelen:<br/>* [basis concepten](concepts-overview.md) (incudes belang rijke concepten, zoals verpakking, code ring en bescherming)<br/>* [ontwikkelen met Media Services v3-api's](media-services-apis-overview.md) (bevat informatie over het openen van api's, naam conventies, enzovoort)

### <a name="sdks"></a>SDK's

Begin met ontwikkelen met [Azure Media Services v3 client-sdk's](media-services-apis-overview.md#sdks).

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

### <a name="how-to-guides"></a>Instructiegidsen

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
