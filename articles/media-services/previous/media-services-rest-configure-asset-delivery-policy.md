---
title: Beleid voor het leveren van activa configureren met behulp van de Media Services REST API | Microsoft Documenten
description: In dit onderwerp ziet u hoe u verschillende beleidsregels voor het leveren van activa configureert met de MEDIA Services REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 09f0371bc189fcf7b25ec3261e2e1f5eaf1892ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194473"
---
# <a name="configuring-asset-delivery-policies"></a>Beleid voor het leveren van activa configureren
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Als u van plan bent dynamisch versleutelde assets te leveren, is een van de stappen in de mediaservices-werkstroom voor het leveren van inhoud het leveringsbeleid voor assets configureren. Het beleid voor het leveren van activa vertelt Media Services hoe u wilt dat uw asset wordt geleverd: in welk streamingprotocol moet uw asset dynamisch worden verpakt (bijvoorbeeld MPEG DASH, HLS, Smooth Streaming of alles), ongeacht of u dynamisch wilt versleutelen uw asset en hoe (envelop of gemeenschappelijke encryptie).

In dit onderwerp wordt besproken waarom en hoe beleid voor het leveren van activa worden gemaakt en geconfigureerd.

> [!NOTE]
> Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 
>
> Ook, om dynamische verpakking en dynamische encryptie te kunnen gebruiken, moet uw asset een set adaptieve bitrate MP4's of adaptieve bitrate Smooth Streaming-bestanden bevatten.

U verschillende beleidsregels toepassen op hetzelfde actief. U bijvoorbeeld PlayReady-versleuteling toepassen op versleuteling voor smooth streaming en AES Envelope op MPEG DASH en HLS. Alle protocollen die niet zijn gedefinieerd in een leveringsbeleid (u voegt bijvoorbeeld één beleid toe waarmee alleen HLS als protocol wordt opgegeven), worden voor streaming geblokkeerd. De uitzondering hierop is als u helemaal geen leveringsbeleid voor assets hebt gedefinieerd. In dat geval is streaming voor alle protocollen toegestaan.

Als u een versleuteld opslagmiddel wilt leveren, moet u het leveringsbeleid van het actief configureren. Voordat uw asset kan worden gestreamd, verwijdert de streamingserver de opslagversleuteling en streamt u uw inhoud met behulp van het opgegeven leveringsbeleid. Als u bijvoorbeeld uw asset versleuteld wilt leveren met een aes-envelopsleutel (Advanced Encryption Standard), stelt u het beleidstype in op **DynamicEnvelopeEncryption.** Als u opslagversleuteling wilt verwijderen en het item wilt streamen in de foutvorm, stelt u het beleidstype in op **NoDynamicEncryption**. Voorbeelden die laten zien hoe u deze beleidstypen configureert, volgen.

Afhankelijk van hoe u het beleid voor het leveren van activa configureert, u de volgende streamingprotocollen dynamisch verpakken, dynamisch versleutelen en streamen: Smooth Streaming, HLS, MPEG DASH-streams.

In de volgende lijst worden de indelingen weergegeven die u gebruikt om Vloeiend, HLS, DASH te streamen.

Vloeiende streaming:

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest

HLS:

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=mpd-time-csf)


Zie [Build a streaming URL](media-services-deliver-streaming-content.md) (Een streaming-URL bouwen) voor instructies over het publiceren van een asset en bouwen van een streaming-URL.

## <a name="considerations"></a>Overwegingen
* U een AssetDeliveryPolicy die aan een asset is gekoppeld, niet verwijderen terwijl er een OnDemand-locator (streaming) bestaat voor dat actief. De aanbeveling is om het beleid uit het actief te verwijderen voordat u het beleid verwijdert.
* Een streaminglocator kan niet worden gemaakt op een opslagversleuteld item wanneer er geen beleid voor het leveren van activa is ingesteld.  Als het item niet versleuteld is opgeslagen, u met het systeem een locator maken en het item in de fout gaan zonder een beleid voor het leveren van activa.
* U meerdere beleid voor het leveren van activa aan één actief hebben, maar u slechts één manier opgeven om een bepaald AssetDeliveryProtocol te verwerken.  Dit betekent dat als u twee leveringsbeleidsregels probeert te koppelen die het AssetDeliveryProtocol.SmoothStreaming-protocol opgeven dat resulteert in een fout omdat het systeem niet weet welke u wilt dat het toepast wanneer een client een Smooth Streaming-verzoek indient.
* Als u een asset hebt met een bestaande streaminglocator, u een nieuw beleid niet koppelen aan het item, een bestaand beleid loskoppelen van het item of een leveringsbeleid bijwerken dat aan het item is gekoppeld.  Je moet eerst de streaminglocator verwijderen, het beleid aanpassen en vervolgens de streaminglocator opnieuw maken.  U dezelfde locatorId gebruiken wanneer u de streaming locator opnieuw maakt, maar u moet ervoor zorgen dat dit geen problemen veroorzaakt voor clients, omdat inhoud kan worden opgeslagen door de oorsprong of een downstream-CDN.

> [!NOTE]
> 
> Wanneer u toegang krijgt tot entiteiten in Media Services, moet u specifieke koptekstvelden en -waarden instellen in uw HTTP-aanvragen. Zie [Setup for Media Services REST API Development voor](media-services-rest-how-to-use.md)meer informatie.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [Toegang tot de Azure Media Services API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor informatie over hoe u verbinding maken met de AMS-API. 

## <a name="clear-asset-delivery-policy"></a>Duidelijk beleid voor het leveren van activa
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>Beleid voor het leveren van activa maken
Met de volgende HTTP-aanvraag wordt een beleid voor het leveren van activa aangeschreven dat aangeeft geen dynamische versleuteling toe te passen en de stream te leveren in een van de volgende protocollen: MPEG DASH-, HLS- en Smooth Streaming-protocollen. 

Zie de sectie Typen die worden gebruikt bij het definiëren van de sectie [AssetDeliveryPolicy](#types) voor informatie over welke waarden u opgeven bij het maken van een AssetDeliveryPolicy.   

Aanvraag:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net

    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Reactie:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>Asset koppelen aan beleid voor het leveren van activa
Met de volgende HTTP-aanvraag wordt het opgegeven actief gekoppeld aan het beleid voor de levering van activa aan.

Aanvraag:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Reactie:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption asset delivery policy DynamicEnvelopeEncryption asset delivery policy DynamicEnvelopeEncryption asset delivery policy DynamicEnvelope
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Inhoudssleutel van het type EnvelopeEncryption maken en deze koppelen aan het item
Wanneer u het leveringsbeleid van DynamicEnvelopeEncryption opgeeft, moet u ervoor zorgen dat uw asset wordt gekoppeld aan een inhoudssleutel van het type EnvelopeEncryption. Zie voor meer informatie: [Een inhoudstoets maken](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>Url van levering ophalen
Download de weergave-URL voor de opgegeven weergavemethode van de inhoudssleutel die in de vorige stap is gemaakt. Een client gebruikt de geretourneerde URL om een AES-toets of een PlayReady-licentie aan te vragen om de beveiligde inhoud af te spelen.

Geef het type URL op dat in de hoofdtekst van de HTTP-aanvraag moet komen. Als u uw inhoud beschermt met PlayReady, vraagt u een URL voor het aanschaffen van een Media Services PlayReady-licentie aan, waarbij u 1 gebruikt voor de keyDeliveryType: {"keyDeliveryType":1}. Als u uw inhoud beschermt met de envelopversleuteling, vraagt u een URL voor sleutelverwerving aan door 2 op te geven voor keyDeliveryType: {"keyDeliveryType":2}.

Aanvraag:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21

    {"keyDeliveryType":2}

Reactie:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


### <a name="create-asset-delivery-policy"></a>Beleid voor het leveren van activa maken
Met de volgende **HTTP-aanvraag** wordt het AssetDeliveryPolicy geconfigureerd dat is geconfigureerd om dynamische envelope encryption **(DynamicEnvelopeEncryption)** toe te passen op het **HLS-protocol** (in dit voorbeeld worden andere protocollen geblokkeerd voor streaming). 

Zie de sectie Typen die worden gebruikt bij het definiëren van de sectie [AssetDeliveryPolicy](#types) voor informatie over welke waarden u opgeven bij het maken van een AssetDeliveryPolicy.   

Aanvraag:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}


Reactie:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


### <a name="link-asset-with-asset-delivery-policy"></a>Asset koppelen aan beleid voor het leveren van activa
Zie [Asset koppelen aan beleid voor het leveren van activa](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption asset delivery policy
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Inhoudssleutel van het type CommonEncryption maken en deze koppelen aan het item
Wanneer u het leveringsbeleid van DynamicCommonEncryption opgeeft, moet u ervoor zorgen dat uw asset wordt gekoppeld aan een inhoudssleutel van het commonencryption-type. Zie voor meer informatie: [Een inhoudstoets maken](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Url van levering ophalen
Download de weergave-URL voor de PlayReady-weergavemethode van de inhoudssleutel die in de vorige stap is gemaakt. Een client gebruikt de geretourneerde URL om een PlayReady-licentie aan te vragen om de beveiligde inhoud af te spelen. Zie [URL van bezorging ophalen](#get_delivery_url)voor meer informatie .

### <a name="create-asset-delivery-policy"></a>Beleid voor het leveren van activa maken
Met de volgende **HTTP-aanvraag** wordt het AssetDeliveryPolicy geconfigureerd dat is geconfigureerd om dynamische algemene versleuteling **(DynamicCommonEncryption)** toe te passen op het **Smooth Streaming-protocol** (in dit voorbeeld worden andere protocollen geblokkeerd voor streaming). 

Zie de sectie Typen die worden gebruikt bij het definiëren van de sectie [AssetDeliveryPolicy](#types) voor informatie over welke waarden u opgeven bij het maken van een AssetDeliveryPolicy.   

Aanvraag:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Als u uw inhoud wilt beschermen met Widevine DRM, werkt u de assetdeliveryconfiguratiewaarden bij om WidevineLicenseAcquisitionUrl (met de waarde van 7) te gebruiken en geeft u de URL van een licentieleveringsservice op. U de volgende AMS-partners gebruiken om u te helpen widevine-licenties te leveren: [Axinom,](https://www.axinom.com) [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Bijvoorbeeld: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> Bij het versleutelen met Widevine u alleen leveren met DASH. Zorg ervoor dat dash (2) wordt opgegeven in het protocol voor het leveren van activa.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Asset koppelen aan beleid voor het leveren van activa
Zie [Asset koppelen aan beleid voor het leveren van activa](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typen die worden gebruikt bij het definiëren van AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

In het volgende enum worden waarden beschreven die u instellen voor het asset delivery protocol.

    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

In het volgende enum worden waarden beschreven die u instellen voor het type beleid voor het leveren van activa.  

    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

In het volgende enum worden waarden beschreven die u gebruiken om de leveringsmethode van de inhoudssleutel voor de client te configureren.
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquisition protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquisition protocol
        ///
        </summary>
        Widevine = 3

    }


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

In het volgende enum worden waarden beschreven die u instellen om sleutels te configureren die worden gebruikt om een specifieke configuratie voor een beleid voor het leveren van activa te krijgen.

    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

