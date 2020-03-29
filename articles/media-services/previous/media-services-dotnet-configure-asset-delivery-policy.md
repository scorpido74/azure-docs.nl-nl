---
title: Beleid voor het leveren van activa configureren met .NET SDK | Microsoft Documenten
description: In dit onderwerp ziet u hoe u verschillende beleidsregels voor het leveren van activa configureert met Azure Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ab3c40ee408498453bb137c63c440d980b0b7255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974509"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Beleid voor het leveren van activa configureren met .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Overzicht
Als u van plan bent versleutelde assets te leveren, configureert een van de stappen in de contentdeliveryworkflow van Media Services het leveringsbeleid voor assets. Het beleid voor het leveren van activa vertelt Media Services hoe u wilt dat uw asset wordt geleverd: in welk streamingprotocol moet uw asset dynamisch worden verpakt (bijvoorbeeld MPEG DASH, HLS, Smooth Streaming of alles), ongeacht of u dynamisch wilt versleutelen uw asset en hoe (envelop of gemeenschappelijke encryptie).

In dit artikel wordt besproken waarom en hoe beleid voor het leveren van activa worden gemaakt en geconfigureerd.

>[!NOTE]
>Wanneer uw AMS-account wordt gemaakt, wordt een **standaard** streamingeindpunt toegevoegd aan uw account in de **status Gestopt.** Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 
>
>Ook, om dynamische verpakking en dynamische encryptie te kunnen gebruiken, moet uw asset een set adaptieve bitrate MP4's of adaptieve bitrate Smooth Streaming-bestanden bevatten.

U verschillende beleidsregels toepassen op hetzelfde actief. U bijvoorbeeld PlayReady-versleuteling toepassen op versleuteling voor smooth streaming en AES Envelope op MPEG DASH en HLS. Alle protocollen die niet zijn gedefinieerd in een leveringsbeleid (u voegt bijvoorbeeld één beleid toe waarmee alleen HLS als protocol wordt opgegeven), worden voor streaming geblokkeerd. De uitzondering is als u helemaal geen leveringsbeleid voor assets hebt gedefinieerd. In dat geval is streaming voor alle protocollen toegestaan.

Als u een versleuteld opslagmiddel wilt leveren, moet u het leveringsbeleid van het actief configureren. Voordat uw asset kan worden gestreamd, verwijdert de streamingserver de opslagversleuteling en streamt u uw inhoud met behulp van het opgegeven leveringsbeleid. Als u bijvoorbeeld uw asset versleuteld wilt leveren met een aes-envelopsleutel (Advanced Encryption Standard), stelt u het beleidstype in op **DynamicEnvelopeEncryption.** Als u opslagversleuteling wilt verwijderen en het item wilt streamen in de foutvorm, stelt u het beleidstype in op **NoDynamicEncryption**. Voorbeelden die laten zien hoe u deze beleidstypen configureert, volgen.

Afhankelijk van hoe u het beleid voor het leveren van activa configureert, u de volgende streamingprotocollen dynamisch verpakken, versleutelen en streamen: Smooth Streaming, HLS en MPEG DASH.

In de volgende lijst worden de indelingen weergegeven die u gebruikt om Vloeiend, HLS en DASH te streamen.

Vloeiende streaming:

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest

HLS:

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Overwegingen
* Voordat u het AssetDeliveryPolicy verwijdert, moet u alle streaminglocators verwijderen die aan het item zijn gekoppeld. U later nieuwe streaming locators maken, indien gewenst, met een nieuwe AssetDeliveryPolicy.
* Een streaminglocator kan niet worden gemaakt op een opslagversleuteld item wanneer er geen beleid voor het leveren van activa is ingesteld.  Als het item niet versleuteld is opgeslagen, u met het systeem een locator maken en het item in de fout gaan zonder een beleid voor het leveren van activa.
* U meerdere beleid voor het leveren van activa aan één actief hebben, maar u slechts één manier opgeven om een bepaald AssetDeliveryProtocol te verwerken.  Dit betekent dat als u twee leveringsbeleidsregels probeert te koppelen die het AssetDeliveryProtocol.SmoothStreaming-protocol opgeven dat resulteert in een fout omdat het systeem niet weet welke u wilt dat het toepast wanneer een client een Smooth Streaming-verzoek indient.
* Als u een asset hebt met een bestaande streaminglocator, u een nieuw beleid niet koppelen aan het item (u een bestaand beleid loskoppelen van het item of een leveringsbeleid bijwerken dat aan het item is gekoppeld).  Je moet eerst de streaminglocator verwijderen, het beleid aanpassen en vervolgens de streaminglocator opnieuw maken.  U dezelfde locatorId gebruiken wanneer u de streaming locator opnieuw maakt, maar u moet ervoor zorgen dat dit geen problemen veroorzaakt voor clients, omdat inhoud kan worden opgeslagen door de oorsprong of een downstream-CDN.

## <a name="clear-asset-delivery-policy"></a>Duidelijk beleid voor het leveren van activa

De volgende **methode ConfigureClearAssetDeliveryPolicy** geeft aan geen dynamische versleuteling toe te passen en de stream te leveren in een van de volgende protocollen: MPEG DASH, HLS en Smooth Streaming-protocollen. Misschien wilt u dit beleid toepassen op uw opslagversleutelde assets.

Zie de sectie Typen die worden gebruikt bij het definiëren van de sectie [AssetDeliveryPolicy](#types) voor informatie over welke waarden u opgeven bij het maken van een AssetDeliveryPolicy.

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption asset delivery policy

Met de volgende **createassetdeliverybeleid-methode** wordt het **AssetDeliveryPolicy** gemaakt dat is geconfigureerd om dynamische algemene versleuteling **(DynamicCommonEncryption)** toe te passen op een vloeiend streamingprotocol (andere protocollen worden geblokkeerd voor streaming). De methode heeft twee parameters: **Asset** (het actief waarop u het leveringsbeleid wilt toepassen) en **IContentKey** (de inhoudssleutel van het **commonencryption-type,** voor meer informatie, zie: [Een inhoudssleutel maken).](media-services-dotnet-create-contentkey.md#common_contentkey)

Zie de sectie Typen die worden gebruikt bij het definiëren van de sectie [AssetDeliveryPolicy](#types) voor informatie over welke waarden u opgeven bij het maken van een AssetDeliveryPolicy.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Azure Media Services stelt u ook in staat om Widevine-versleuteling toe te voegen. In het volgende voorbeeld wordt aangetoond dat zowel PlayReady als Widevine worden toegevoegd aan het beleid voor het leveren van activa.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> Bij het versleutelen met Widevine u alleen leveren met DASH. Zorg ervoor dat dash wordt opgegeven in het protocol voor de levering van activa.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption asset delivery policy DynamicEnvelopeEncryption asset delivery policy DynamicEnvelopeEncryption asset delivery policy DynamicEnvelope
Met de volgende **createassetdeliverybeleid-methode** wordt de **AssetDeliveryPolicy** gemaakt die is geconfigureerd om dynamische envelope encryption **(DynamicEnvelopeEncryption)** toe te passen op Smooth Streaming,HLS en DASH-protocollen (als u besluit bepaalde protocollen niet op te geven, worden ze geblokkeerd voor streaming). De methode heeft twee parameters: **Asset** (het actief waarop u het leveringsbeleid wilt toepassen) en **IContentKey** (de inhoudssleutel van het type **EnvelopeEncryption,** zie: [Een inhoudssleutel maken).](media-services-dotnet-create-contentkey.md#envelope_contentkey)

Zie de sectie Typen die worden gebruikt bij het definiëren van de sectie [AssetDeliveryPolicy](#types) voor informatie over welke waarden u opgeven bij het maken van een AssetDeliveryPolicy.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typen die worden gebruikt bij het definiëren van AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a><a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

In het volgende enum worden waarden beschreven die u instellen voor het asset delivery protocol.

```csharp
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
```
### <a name="assetdeliverypolicytype"></a><a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

In het volgende enum worden waarden beschreven die u instellen voor het type beleid voor het leveren van activa.  
```csharp
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
```
### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

In het volgende enum worden waarden beschreven die u gebruiken om de leveringsmethode van de inhoudssleutel voor de client te configureren.
  ```csharp  
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
```
### <a name="assetdeliverypolicyconfigurationkey"></a><a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

In het volgende enum worden waarden beschreven die u instellen om sleutels te configureren die worden gebruikt om een specifieke configuratie voor een beleid voor het leveren van activa te krijgen.
```csharp
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
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

