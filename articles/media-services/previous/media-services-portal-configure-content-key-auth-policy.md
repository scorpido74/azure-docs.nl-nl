---
title: Een autorisatiebeleid voor inhoudssleutel configureren met behulp van de Azure-portal | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u een autorisatiebeleid voor een inhoudssleutel configureert.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 8580bafd4d68ef6567b09fefcaa01c682ae2cafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968787"
---
# <a name="configure-a-content-key-authorization-policy"></a>Een autorisatiebeleid voor inhoudssleutel configureren
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Overzicht
 U Azure Media Services gebruiken om MPEG-DASH-, Smooth Streaming- en HTTP Live Streaming (HLS)-streams (HLS) te leveren die zijn beveiligd met Advanced Encryption Standard (AES) met behulp van 128-bits encryptiesleutels of [PlayReady digital rights management (DRM).](https://www.microsoft.com/playready/overview/) Met Media Services u dashstreams ook versleuteld leveren met Widevine DRM. Zowel PlayReady als Widevine zijn versleuteld volgens de specificatie Common Encryption (ISO/IEC 23001-7 CENC).

Media Services biedt ook een key/license delivery Service van waaruit clients AES-toetsen of PlayReady/Widevine-licenties kunnen verkrijgen om de versleutelde inhoud af te spelen.

In dit artikel ziet u hoe u de Azure-portal gebruiken om het autorisatiebeleid voor inhoudssleutel te configureren. De sleutel kan later worden gebruikt om uw inhoud dynamisch te versleutelen. Momenteel u HLS-, MPEG-DASH- en Smooth Streaming-indelingen versleutelen. U progressieve downloads niet versleutelen.

Wanneer een speler een stream aanvraagt die dynamisch is versleuteld, gebruikt Media Services de geconfigureerde sleutel om uw inhoud dynamisch te versleutelen met Behulp van AES- of DRM-versleuteling. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. Als u wilt bepalen of de gebruiker gemachtigd is om de sleutel te krijgen, evalueert de service het autorisatiebeleid dat u voor de sleutel hebt opgegeven.

Als u van plan bent meerdere inhoudssleutels te hebben of een andere URL voor de levering van een sleutel-/licentieservice wilt opgeven dan de mediaservices-sleutelleveringsservice, gebruikt u de MEDIA Services .NET SDK- of REST-API's. Zie voor meer informatie:

* [Een autorisatiebeleid voor inhoudssleutel configureren met behulp van de Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Een autorisatiebeleid voor inhoudssleutel configureren met de API Voor de rest van Media Services](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Sommige overwegingen zijn van toepassing
* Wanneer uw Media Services-account is gemaakt, wordt er een standaard streaming-eindpunt met de status Gestopt aan uw account toegevoegd. Om te beginnen met het streamen van uw inhoud en te profiteren van dynamische verpakking en dynamische versleuteling, moet uw streaming-eindpunt zich in de status 'Lopend' bevindt. 
* Uw asset moet een set adaptieve bitrate MP4's of adaptieve bitrate Smooth Streaming-bestanden bevatten. Zie [Een actief coderen voor](media-services-encode-asset.md)meer informatie .
* De key delivery service caches ContentKeyAuthorizationPolicy en de bijbehorende objecten (beleidsopties en beperkingen) gedurende 15 minuten. U een ContentKeyAuthorizationPolicy maken en opgeven om een tokenbeperking te gebruiken, te testen en het beleid vervolgens bij te werken naar de open beperking. Dit proces duurt ongeveer 15 minuten voordat het beleid overschakelt naar de geopende versie.
* Een streamingeindpunt voor Media Services stelt de waarde van de CORS Access-Control-Allow-Origin-header in preflight-respons in als de wildcard "\*". Deze waarde werkt goed bij de meeste spelers, waaronder Azure Media Player, Roku en JWPlayer, en anderen. Sommige spelers die dash.js gebruiken, werken echter niet omdat xmlHttpRequest in hun dash.js niet is toegestaan\*als de waarde van Access-Control-Allow-Origin. Als tijdelijke oplossing voor deze beperking in dash.js kan Media Services dat domein opgeven in de preflight-antwoordkop als u uw client vanuit één domein host. Open voor hulp een ondersteuningsticket via de Azure-portal.

## <a name="configure-the-key-authorization-policy"></a>Het sleutelautorisatiebeleid configureren
Als u het sleutelautorisatiebeleid wilt configureren, selecteert u de pagina **INHOUDSBESCHERMING.**

Media Services ondersteunt meerdere manieren om gebruikers te verifiëren die belangrijke verzoeken indienen. Het autorisatiebeleid voor inhoudssleutel kan open-, token- of IP-autorisatiebeperkingen bevatten. (IP kan worden geconfigureerd met REST of de .NET SDK.)

### <a name="open-restriction"></a>Open beperking
De open beperking betekent dat het systeem de sleutel levert aan iedereen die een sleutelverzoek doet. Deze beperking kan nuttig zijn voor testdoeleinden.

![OpenBeleid][open_policy]

### <a name="token-restriction"></a>Tokenbeperking
Als u het beleid met tokenbeperking wilt kiezen, selecteert u de knop **TOKEN.**

Het beleid met tokenbeperkte nummers moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in de eenvoudige webtoken[(SWT)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)en JSON Web Token (JWT) formaten. Zie [JWT-verificatie](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)voor meer informatie.

Media Services biedt geen STS. U een aangepaste STS maken om tokens uit te geven. De STS moet zijn geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel- en uitgifteclaims die u hebt opgegeven in de configuratie van tokenbeperkingen. Als het token geldig is en de claims in het token overeenkomen met de claims die zijn geconfigureerd voor de inhoudssleutel, retourneert de service voor het leveren van de mediaservicesdesleutel de versleutelingssleutel naar de client.

Wanneer u het beleid met een tokenbeperking configureert, moet u de primaire verificatiesleutel, de uitgever en de doelgroepparameters opgeven. De primaire verificatiesleutel bevat de sleutel waarmee het token is ondertekend. De uitgever is de STS die het token uitgeeft. De doelgroep (ook wel scope genoemd) beschrijft de intentie van het token of de bron waartoe het token toegang geeft. De service voor het leveren van de sleutel van Media Services valideert dat deze waarden in het token overeenkomen met de waarden in de sjabloon.

### <a name="playready"></a>PlayReady
Wanneer u uw inhoud beschermt met PlayReady, is een van de dingen die u in uw autorisatiebeleid moet opgeven een XML-tekenreeks die de PlayReady-licentiesjabloon definieert. Standaard is het volgende beleid ingesteld:

    <PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
          <LicenseTemplates>
            <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
              <ContentKey i:type="ContentEncryptionKeyFromHeader" />
              <LicenseType>Nonpersistent</LicenseType>
              <PlayRight>
                <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
              </PlayRight>
            </PlayReadyLicenseTemplate>
          </LicenseTemplates>
        </PlayReadyLicenseResponseTemplate>

U de **xml-knop voor importbeleid** selecteren en een andere XML bieden die voldoet aan het XML-schema dat is gedefinieerd in het overzicht van de [mediaservices PlayReady-licentiesjabloon.](media-services-playready-license-template-overview.md)

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

