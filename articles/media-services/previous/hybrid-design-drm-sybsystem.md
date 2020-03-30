---
title: Hybride ontwerp van DRM-subsysteem(en) met Azure Media Services | Microsoft Documenten
description: In dit onderwerp wordt gesproken over hybride ontwerp van DRM-subsysteem(s) met Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: d2f4ddfbff791fbfeb2eb006a628c0fdeb4fdce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975190"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hybride ontwerp van DRM-subsystemen 

In dit onderwerp wordt gesproken over hybride ontwerp van DRM-subsysteem(s) met Azure Media Services.

## <a name="overview"></a>Overzicht

Azure Media Services biedt ondersteuning voor het volgende drie DRM-systeem:

* PlayReady
* Widevine (modulair)
* FairPlay

De DRM-ondersteuning omvat DRM-versleuteling (dynamische versleuteling) en licentielevering, waarbij Azure Media Player alle 3 DRM's ondersteunt als een SDK voor browserspelers.

Voor een gedetailleerde behandeling van drm/CENC subsysteem ontwerp en implementatie, zie het document getiteld [CENC met Multi-DRM en Access Control](media-services-cenc-with-multidrm-access-control.md).

Hoewel we volledige ondersteuning bieden voor drie DRM-systemen, moeten klanten soms verschillende delen van hun eigen infrastructuur/subsystemen gebruiken naast Azure Media Services om een hybride DRM-subsysteem te bouwen.

Hieronder vindt u enkele veelgestelde vragen van klanten:

* "Kan ik mijn eigen DRM-licentieservers gebruiken?" (In dit geval hebben klanten geïnvesteerd in DRM-licentieserverfarm met ingesloten bedrijfslogica).
* "Kan ik alleen uw DRM-licentielevering gebruiken in Azure Media Services zonder inhoud in AMS te hosten?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modulariteit van het AMS DRM-platform

Als onderdeel van een uitgebreid cloudvideoplatform heeft Azure Media Services DRM een ontwerp met flexibiliteit en modulariteit in gedachten. U Azure Media Services gebruiken met een van de volgende verschillende combinaties die in de onderstaande tabel worden beschreven (een uitleg van de notatie die in de tabel wordt gebruikt volgt). 

|**Contenthosting & herkomst**|**Inhoudsversleuteling**|**Levering van DRM-licentie**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Derde partij|
|AMS|Derde partij|AMS|
|AMS|Derde partij|Derde partij|
|Derde partij|Derde partij|AMS|

### <a name="content-hosting--origin"></a>Contenthosting & herkomst

* AMS: video asset wordt gehost in AMS en streaming is via AMS streaming endpoints (maar niet noodzakelijkerwijs dynamische verpakking).
* Third-party: video wordt gehost en geleverd op een streamingplatform van derden buiten AMS.

### <a name="content-encryption"></a>Inhoudsversleuteling

* AMS: content encryptie wordt dynamisch/on-demand uitgevoerd door AMS dynamic encryption.
* Third-party: content encryptie wordt uitgevoerd buiten AMS met behulp van een pre-processing workflow.

### <a name="drm-license-delivery"></a>Levering van DRM-licentie

* AMS: DRM-licentie wordt geleverd door AMS-licentieleveringsservice.
* Third-party: DRM-licentie wordt geleverd door een DRM-licentieserver van derden buiten AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Configureren op basis van uw hybride scenario

### <a name="content-key"></a>Inhoudstoets

Door de configuratie van een inhoudssleutel u de volgende kenmerken van zowel AMS-dynamische versleuteling als AMS-licentieleveringsservice beheren:

* De inhoudssleutel die wordt gebruikt voor dynamische DRM-versleuteling.
* DRM-licentie-inhoud die moet worden geleverd door licentieservices: rechten, inhoudssleutel en beperkingen.
* Beleidsbeperking voor **inhoudssleutelautorisatie:** beperking van openen, IP of token.
* Als de beleidsbeperking voor **tokentype** **van inhoudssleutelautorisatie wordt gebruikt,** moet aan de beleidsbeperking voor autorisatie van **inhoudssleutel** worden voldaan voordat een licentie wordt uitgegeven.

### <a name="asset-delivery-policy"></a>Beleid voor het leveren van activa

Door het configureren van een asset delivery policy u de volgende kenmerken beheren die worden gebruikt door AMS dynamic packager en dynamische versleuteling van een AMS-streaming eindpunt:

* Streaming protocol en DRM encryptie combinatie, zoals DASH onder CENC (PlayReady en Widevine), vloeiende streaming onder PlayReady, HLS onder Widevine of PlayReady.
* De standaard/ingesloten URL's voor het leveren van licenties voor elk van de betrokken DRM's.
* Of licentieverwerving-URL's (LA_URLs) in DASH MPD- of HLS-afspeellijsten respectievelijk queryreeksen van sleutel-ID (KID) bevatten voor Widevine en FairPlay.

## <a name="scenarios-and-samples"></a>Scenario's en monsters

Op basis van de uitleg in de vorige sectie worden in de volgende vijf hybride scenario's respectievelijke configuratiecombinaties van **inhoudssleutel**-**assetleveringsbeleid** gebruikt (de voorbeelden in de laatste kolom volgen de tabel):

|**Contenthosting & herkomst**|**DRM-versleuteling**|**Levering van DRM-licentie**|**Inhoudssleutel configureren**|**Beleid voor het leveren van activa configureren**|**Monster**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Ja|Ja|Voorbeeld 1|
|AMS|AMS|Derde partij|Ja|Ja|Voorbeeld 2|
|AMS|Derde partij|AMS|Ja|Nee|Voorbeeld 3|
|AMS|Derde partij|Buiten|Nee|Nee|Voorbeeld 4|
|Derde partij|Derde partij|AMS|Ja|Nee|    

In de samples werkt PlayReady-beveiliging voor zowel DASH als vloeiende streaming. De onderstaande video-URL's zijn vloeiende streaming URL's. Als u de bijbehorende DASH-URL's wilt krijgen, moet u gewoon "(format=mpd-time-csf)" toevoegen. U de [azure media testspeler](https://aka.ms/amtest) gebruiken om te testen in een browser. Hiermee u configureren welk streamingprotocol u wilt gebruiken, waaronder technologie. IE11 en Microsoft Edge op Windows 10 ondersteunen PlayReady via EME. Zie meer [informatie over het testgereedschap](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/)voor meer informatie.

### <a name="sample-1"></a>Voorbeeld 1

* URL van bron (basis):https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady-LA_URL (DASH & soepel):https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (DASH):https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS):https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Voorbeeld 2

* URL van bron (basis):https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady-LA_URL (DASH & soepel):http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Voorbeeld 3

* Bron-URL:https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady-LA_URL (DASH & soepel):https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Voorbeeld 4

* Bron-URL:https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady-LA_URL (DASH & soepel):https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="summary"></a>Samenvatting

Samengevat zijn DRM-componenten van Azure Media Services flexibel, u ze gebruiken in een hybride scenario door het beleid voor het leveren van inhoudssleutels en activa correct te configureren, zoals beschreven in dit onderwerp.

## <a name="next-steps"></a>Volgende stappen
Leerpaden van Media Services weergeven.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

