---
title: HLS-inhoud beveiligen met Microsoft PlayReady of Apple FairPlay - Azure | Microsoft Documenten
description: In dit onderwerp vindt u een overzicht en wordt uitgelegd hoe u Azure Media Services gebruiken om uw HLS-inhoud (Live Streaming) dynamisch te versleutelen met Apple FairPlay. Het laat ook zien hoe u de mediaservicesservice voor licentieverlening gebruiken om FairPlay-licenties aan klanten te leveren.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 873bc4ab5e435b91ff4400a39c92db0d0bb9baa8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968762"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Bescherm uw HLS-inhoud met Apple FairPlay of Microsoft PlayReady

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.   > Er worden geen nieuwe functies of functionaliteit toegevoegd aan Media Services v2. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)
>

Met Azure Media Services u uw HLS-inhoud (HTTP Live Streaming) dynamisch versleutelen met behulp van de volgende indelingen:  

* **AES-128 envelop duidelijke sleutel**

    De hele brok wordt versleuteld met behulp van de **AES-128** CBC-modus. De decryptie van de stream wordt ondersteund door iOS en OS X-speler native. Zie [AES-128 dynamische versleuteling en key delivery service gebruiken](media-services-protect-with-aes128.md)voor meer informatie.
* **Apple FairPlay**

    De afzonderlijke video- en audiosamples worden versleuteld met behulp van de **AES-128 CBC-modus.** **FairPlay Streaming** (FPS) is geïntegreerd in de besturingssystemen van het apparaat, met native ondersteuning op iOS en Apple TV. Safari on OS X maakt FPS mogelijk door gebruik te maken van de EME-interfaceondersteuning (Encrypted Media Extensions).
* **Microsoft PlayReady**

De volgende afbeelding toont de **dynamische versleutelingsworkflow HLS + FairPlay of PlayReady.**

![Diagram van dynamische versleutelingswerkstroom](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

In dit artikel wordt uitgelegd hoe u Media Services gebruiken om uw HLS-inhoud dynamisch te versleutelen met Apple FairPlay. Het laat ook zien hoe u de mediaservicesservice voor licentieverlening gebruiken om FairPlay-licenties aan klanten te leveren.

> [!NOTE]
> Als u uw HLS-inhoud ook wilt versleutelen met PlayReady, moet u een algemene inhoudssleutel maken en deze koppelen aan uw asset. U moet ook het autorisatiebeleid van de inhoudssleutel configureren, zoals beschreven in [PlayReady-dynamische algemene versleuteling](media-services-protect-with-playready-widevine.md)gebruiken.
>
>

## <a name="requirements-and-considerations"></a>Eisen en overwegingen

Het volgende is vereist bij het gebruik van Media Services om HLS versleuteld met FairPlay te leveren en om FairPlay-licenties te leveren:

  * Een Azure-account. Zie gratis [proefversie van Azure voor](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)meer informatie.
  * Een Media Services-account. Zie [Een Azure Media Services-account maken met de Azure-portal](media-services-portal-create-account.md)als u er een wilt maken.
  * Meld u aan bij [het Apple Development Program](https://developer.apple.com/).
  * Apple vereist dat de eigenaar van de inhoud het [implementatiepakket verkrijgt.](https://developer.apple.com/contact/fps/) Geef aan dat u Key Security Module (KSM) al hebt geïmplementeerd met Media Services en dat u het definitieve FPS-pakket aanvraagt. Er zijn instructies in het uiteindelijke FPS-pakket om certificering te genereren en de Application Secret Key (ASK) te verkrijgen. Je gebruikt ASK om FairPlay te configureren.
  * Azure Media Services .NET SDK-versie **3.6.0** of hoger.

De volgende dingen moeten worden ingesteld op media services belangrijke levering kant:

  * **App Cert (AC)**: Dit is een .pfx-bestand dat de privésleutel bevat. U maakt dit bestand en versleutelt het met een wachtwoord.

       Wanneer u een beleid voor het leveren van sleutels configureert, moet u dat wachtwoord en het .pfx-bestand opgeven in base64-indeling.

      In de volgende stappen wordt beschreven hoe u een .pfx-certificaatbestand voor FairPlay genereren:

    1. Installeer OpenSSL https://slproweb.com/products/Win32OpenSSL.htmlvanaf .

        Ga naar de map waar het FairPlay-certificaat en andere bestanden die door Apple zijn geleverd.
    2. Voer de volgende opdracht uit via de opdrachtregel. Hiermee wordt het .cer-bestand omgezet in een .pem-bestand.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der-in FairPlay.cer -out FairPlay-out.pem
    3. Voer de volgende opdracht uit via de opdrachtregel. Hiermee wordt het .pem-bestand omgezet in een .pfx-bestand met de privésleutel. Het wachtwoord voor het .pfx-bestand wordt vervolgens gevraagd door OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem-passin file:privatekey-pem-pass.txt
  * **App Cert-wachtwoord**: het wachtwoord voor het maken van het .pfx-bestand.
  * **Wachtwoord-ID van App Cert:** u moet het wachtwoord uploaden, vergelijkbaar met de manier waarop ze andere Media Services-sleutels uploaden. Gebruik de **contentkeytype.FairPlayPfxPassword-enum-waarde** om de Media Services ID te krijgen. Dit is wat ze moeten gebruiken binnen de optie voor het belangrijkste leveringsbeleid.
  * **iv**: Dit is een willekeurige waarde van 16 bytes. Het moet overeenkomen met het iv in het beleid voor de levering van activa. U genereert het infuus en plaatst deze op beide plaatsen: het beleid voor de levering van activa en de optie voor het belangrijkste leveringsbeleid.
  * **VRAGEN:** Deze sleutel wordt ontvangen wanneer u de certificering genereert met behulp van de Apple Developer-portal. Elk ontwikkelteam ontvangt een unieke ASK. Sla een kopie van de ASK op en bewaar deze op een veilige plaats. U moet ASK later configureren als FairPlayAsk naar Media Services.
  * **ASK ID**: Deze ID wordt verkregen wanneer u ASK uploadt naar Media Services. Je moet ASK uploaden met de **contentkeytype.FairPlayAsk** enum-waarde. Als gevolg hiervan wordt de Media Services ID geretourneerd en dit is wat moet worden gebruikt bij het instellen van de optie voor het beleid voor belangrijke services.

De volgende dingen moeten worden ingesteld door de FPS-clientzijde:

  * **App Cert (AC):** Dit is een .cer/.der-bestand dat de openbare sleutel bevat, die het besturingssysteem gebruikt om een payload te versleutelen. Media Services moet hiervan op de hoogte zijn omdat dit door de speler vereist is. De belangrijke leveringsservice decodeert deze met behulp van de bijbehorende privésleutel.

Als u een FairPlay-versleutelde stream wilt afspelen, krijgt u eerst een echte ASK en genereert u vervolgens een echt certificaat. Dat proces creëert alle drie de delen:

  * .der-bestand
  * .pfx-bestand
  * wachtwoord voor de .pfx

De volgende clients ondersteunen HLS met **AES-128** CBC-encryptie: Safari op OS X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>FairPlay dynamische versleuteling en licentieleveringsservices configureren
Hieronder volgen algemene stappen voor het beschermen van uw assets met FairPlay door gebruik te maken van de mediaservices-licentieleveringsservice, en ook met behulp van dynamische versleuteling.

1. Maak een asset en upload bestanden in de asset.
2. Codeer de asset die het bestand bevat naar de Adaptive Bitrate MP4-set.
3. Maak een inhoudssleutel en koppel deze aan de gecodeerde asset.  
4. Configureer het autorisatiebeleid voor de inhoudssleutel. Geef het volgende op:

   * De leveringsmethode (in dit geval FairPlay).
   * Configuratie van fairplay-beleidsopties. Zie de methode **ConfigureFairPlayPolicyOptions()** in het onderstaande voorbeeld voor meer informatie over het configureren van FairPlay().

     > [!NOTE]
     > Meestal wilt u fairplay-beleidsopties slechts één keer configureren, omdat u slechts één set certificering en een ASK hebt.
     >
     >
   * Beperkingen (open of token).
   * Informatie die specifiek is voor het type sleutellevering die bepaalt hoe de sleutel aan de client wordt geleverd.
5. Configureer het beleid voor het leveren van activa. De configuratie van het leveringsbeleid omvat:

   * Het leveringsprotocol (HLS).
   * Het type dynamische versleuteling (gemeenschappelijke CBC-versleuteling).
   * De URL voor licentieverwerving.

     > [!NOTE]
     > Als u een stream wilt leveren die is versleuteld met FairPlay en een ander DRM-systeem (Digital Rights Management), moet u afzonderlijke leveringsbeleidsregels configureren:
     >
     > * Eén IAssetDeliveryPolicy voor het configureren van Dynamic Adaptive Streaming via HTTP (DASH) met Common Encryption (CENC) (PlayReady + Widevine) en Vloeiend met PlayReady
     > * Een ander IAssetDeliveryPolicy om FairPlay voor HLS te configureren
     >
     >
6. Maak een OnDemand-locator om een streaming-URL te verkrijgen.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>FairPlay-sleutelweergave gebruiken door apps voor spelers
U speler-apps ontwikkelen met behulp van de iOS SDK. Om FairPlay-content te kunnen spelen, moet je het licentie-uitwisselingsprotocol implementeren. Dit protocol is niet opgegeven door Apple. Het is aan elke app om belangrijke leveringsverzoeken te verzenden. De Media Services FairPlay key delivery service verwacht dat de SPC komt als een www-form-url gecodeerd bericht, in de volgende vorm:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Azure Media Player ondersteunt FairPlay-weergave. Zie [Azure Media Player-documentatie](https://amp.azure.net/libs/amp/latest/docs/index.html) voor meer informatie.
>
>

## <a name="streaming-urls"></a>URL's voor streaming
Als uw asset is versleuteld met meer dan één DRM, moet u een versleutelingstag gebruiken in de streaming-URL: (format='m3u8-aapl', encryption='xxx').

De volgende overwegingen zijn van toepassing:

* Er kan slechts nul of één versleutelingstype worden opgegeven.
* Het versleutelingstype hoeft niet in de URL te worden opgegeven als er slechts één versleuteling op het item is toegepast.
* Het versleutelingstype is ongevoelig.
* De volgende versleutelingstypen kunnen worden opgegeven:  
  * **cenc**: Algemene versleuteling (PlayReady of Widevine)
  * **cbcs-aapl**: FairPlay
  * **cbc**: AES-envelopversleuteling

## <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

1. Stel uw ontwikkelomgeving in en vul het app.config-bestand in met verbindingsgegevens, zoals beschreven in [de ontwikkeling van Media Services met .NET](media-services-dotnet-how-to-use.md). 
2. Voeg de volgende elementen toe aan **appSettings** dat in het bestand app.config is gedefinieerd:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Voorbeeld

Het volgende voorbeeld toont de mogelijkheid om Media Services te gebruiken om uw content versleuteld met FairPlay te leveren. Deze functionaliteit is geïntroduceerd in de Azure Media Services SDK voor .NET-versie 3.6.0. 

Overschrijf de code in uw Program.cs-bestand met de code die wordt weergegeven in deze sectie.

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). U moet dezelfde beleids-id gebruiken als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om gedurende een lange periode gehandhaafd te blijven (niet-upload-beleidsregels). Zie voor meer informatie [dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel.

Zorg ervoor dat variabelen zo worden bijgewerkt dat ze verwijzen naar de mappen waar uw invoerbestanden zich bevinden.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Token Authorization Policy",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                        Requirements = tokenTemplateString,
                    }
                    };

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }


        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the  
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day readonly access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="next-steps-media-services-learning-paths"></a>Volgende stappen: Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
