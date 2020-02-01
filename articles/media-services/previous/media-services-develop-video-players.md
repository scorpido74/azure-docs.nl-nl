---
title: Videospelertoepassingen ontwikkelen
description: Het onderwerp bevat koppelingen naar de Windows Media Player-frameworks en-invoeg toepassingen die u kunt gebruiken om uw eigen client toepassingen te ontwikkelen die gebruikmaken van streaming media van Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 39459267919747ed49e9fa3f05746294eaf741dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906837"
---
# <a name="develop-video-player-applications"></a>Videospelertoepassingen ontwikkelen
## <a name="overview"></a>Overzicht
Azure Media Services biedt de hulpprogramma's die u nodig hebt om geavanceerde, dynamische clientspelertoepassingen te maken voor de meeste platforms, waaronder: iOS-apparaten, Android-apparaten, Windows, Windows Phone, Xbox en settopboxen. Dit onderwerp bevat ook koppelingen naar Sdk's en Player frameworks die u kunt gebruiken om uw eigen client toepassingen te ontwikkelen die gebruikmaken van streaming media van Azure Media Services.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](https://aka.ms/ampinfo) is een webvideo speler die is ontworpen om media-inhoud af te spelen van Microsoft Azure Media Services op een groot aantal verschillende browsers en apparaten. Azure Media Player maakt gebruik van industrie standaarden, zoals HTML5, media source Extensions (MSE) en versleutelde Media-extensies (EME) om een geavanceerde adaptieve streaming-ervaring te bieden. Wanneer deze standaarden niet beschikbaar zijn op een apparaat of in een browser, Azure Media Player gebruikt Flash en Silverlight als terugval technologie. Ontwikkel aars hebben altijd toegang tot Api's via een geïntegreerde Java script-interface, ongeacht de afspeel technologie die wordt gebruikt. Dit maakt het mogelijk dat inhoud die wordt aangeboden door Azure Media Services over een breed scala aan apparaten en browsers zonder extra inspanning kan worden gespeeld.

Met Microsoft Azure Media Services kan inhoud met STREEPJES, Smooth Streaming en HLS worden geleverd om inhoud af te spelen. Azure Media Player houdt rekening met deze verschillende indelingen en speelt automatisch de beste koppeling op basis van de mogelijkheden van platform/browser. Microsoft Azure Media Services biedt ook dynamische versleuteling van assets met PlayReady-versleuteling of AES-128 bits-envelop versleuteling. Azure Media Player staat het ontsleutelen van PlayReady-en AES-128 bits versleutelde inhoud toe wanneer deze op de juiste wijze zijn geconfigureerd. 

Voor meer informatie:

* [Azure Media Player](https://aka.ms/ampinfo)
* [Documentatie over Azure Media Player](https://aka.ms/ampdocs) 
* [Azure Media Player aan de slag-blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Meld u aan om up-to-date te blijven met de nieuwste van Azure Media Player](https://aka.ms/ampsignup)
* [Nieuwe functie aanvragen, ideeën, feedback toevoegen](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Andere Hulpprogram Ma's voor het maken van Player-toepassingen
U kunt ook een van de volgende Sdk's gebruiken:

* [Smooth Streaming client-SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Smooth Streaming Windows Store-app](media-services-build-smooth-streaming-apps.md)
* [Micro soft media platform: Player Framework](https://playerframework.codeplex.com/) 
* [Documentatie voor HTML5-Player Framework](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Micro soft Smooth Streaming-invoeg toepassing voor OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licenties voor micro soft® Smooth Streaming client Porting kit](https://aka.ms/sspk) 
* [Ontwikkeling van XBOX-video toepassingen](https://www.xbox.com/en-US/developers) 

## <a name="advertising"></a>Reclame
Azure Media Services biedt ondersteuning voor AD-invoeging via het Windows Media-platform: Player frameworks. Er zijn Windows 8-, Silverlight-, Windows Phone 8-en iOS-apparaten beschikbaar voor de service kaders met AD-ondersteuning. Elk Framework van de speler bevat voorbeeld code die laat zien hoe u een speler-toepassing implementeert. Er zijn drie verschillende soorten advertenties die u kunt invoegen in uw media:

Lineair: volledige frame advertenties die de hoofd video onderbreken

Niet-lineaire, overlay-advertenties die worden weer gegeven als de hoofd video wordt afgespeeld, meestal een logo of een andere statische afbeelding die in de speler is geplaatst

Companion: advertenties die buiten de speler worden weer gegeven

Advertenties kunnen op elk punt in de tijd lijn van de hoofd video worden geplaatst. U moet de speler vertellen wanneer u de AD wilt afspelen en welke advertenties moeten worden afgespeeld. Dit wordt gedaan met behulp van een set standaard XML-bestanden: video ad service Temp late (VAST), Digital Video multiple AD Play List (VMAP), media abstract sequencing Temp late (MAST) en Digital Video Player AD Interface definition (VPAID). Met grote bestanden kunt u opgeven welke advertenties moeten worden weer gegeven. VMAP-bestanden geven aan wanneer verschillende advertenties moeten worden afgespeeld en die grote XML bevatten. MAST-bestanden zijn een andere manier om advertenties aan te bieden die ook grote XML kunnen bevatten. VPAID-bestanden definiëren een interface tussen de video speler en de AD-of ad-server. Zie [advertenties invoegen](https://msdn.microsoft.com/library/dn387398.aspx)voor meer informatie.

Voor informatie over ondertiteling en ADS-ondersteuning in live streaming Video's, Zie [ondersteunde ondertiteling en normen voor AD-invoeging](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Een adaptieve MPEG-DASH-videostream insluiten in een HTML5-toepassing met DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[GitHub streepje. js-opslag plaats](https://github.com/Dash-Industry-Forum/dash.js)

