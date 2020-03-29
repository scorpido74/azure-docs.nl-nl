---
title: Offline FairPlay Streaming voor iOS met Azure Media Services v3
description: In dit onderwerp vindt u een overzicht en toont u hoe u Azure Media Services gebruiken om uw HTTP Live Streaming (HLS)-inhoud dynamisch te versleutelen met Apple FairPlay in offlinemodus.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 70256046089a59df1de79b78124c5d60fde77080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705935"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Offline FairPlay Streaming voor iOS met Media Services v3

 Azure Media Services biedt een reeks goed ontworpen [services voor contentbescherming](https://azure.microsoft.com/services/media-services/content-protection/) die betrekking hebben op:

- Microsoft PlayReady
- Google Widevine
    
    Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.
- Apple FairPlay
- AES-128-versleuteling

Digital rights management (DRM)/Advanced Encryption Standard (AES) encryptie van inhoud wordt dynamisch uitgevoerd op verzoek voor verschillende streaming protocollen. DRM licentie / AES decryptie belangrijkste levering diensten worden ook geleverd door Media Services.

Naast het beschermen van content voor online streaming via verschillende streaming protocollen, offline modus voor beschermde inhoud is ook een veel gevraagde functie. Ondersteuning in de offline modus is nodig voor de volgende scenario's:

* Afspelen wanneer de internetverbinding niet beschikbaar is, bijvoorbeeld tijdens het reizen.
* Sommige aanbieders van inhoud kunnen de levering van DRM-licenties buiten de grens van een land/regio toestaan. Als gebruikers inhoud willen bekijken terwijl ze buiten het land/de regio reizen, is offline downloaden nodig.
* In sommige landen/regio's is de beschikbaarheid en/of bandbreedte op internet nog beperkt. Gebruikers kunnen ervoor kiezen om eerst te downloaden om inhoud te kunnen bekijken in een resolutie die hoog genoeg is voor een bevredigende kijkervaring. In dit geval is het probleem meestal niet de beschikbaarheid van het netwerk, maar beperkte netwerkbandbreedte. Over-the-top (OTT)/online videoplatform (OVP) providers vragen offline-mode ondersteuning aan.

Dit artikel heeft betrekking op ondersteuning voor de offlinemodus van FairPlay Streaming (FPS) die zich richt op apparaten met iOS 10 of hoger. Deze functie wordt niet ondersteund voor andere Apple-platforms, zoals watchOS, tvOS of Safari op macOS.

> [!NOTE]
> Offline DRM wordt alleen gefactureerd voor het indienen van één aanvraag voor een licentie wanneer u de inhoud downloadt. Eventuele fouten worden niet gefactureerd.

## <a name="prerequisites"></a>Vereisten

Voordat u offline DRM voor FairPlay implementeert op een iOS 10+ apparaat:

* Bekijk de bescherming van online content voor FairPlay: 

    - [Vereisten voor en configuratie van Apple FairPlay-licenties](fairplay-license-overview.md)
    - [De Digital Rights Management-service gebruiken voor dynamische versleuteling en licentielevering](protect-with-drm.md)
    - Een .NET-voorbeeld met configuratie van online FPS-streaming: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Verkrijg de FPS SDK van het Apple Developer Network. De FPS SDK bevat twee componenten:

    - De FPS Server SDK, die de Key Security Module (KSM), clientsamples, een specificatie en een set testvectoren bevat.
    - Het FPS Deployment Pack, dat de functiespecificatie D bevat, samen met instructies over het genereren van het FPS-certificaat, klantspecifieke privésleutel en application secret key. Apple geeft het FPS Deployment Pack alleen uit aan gelicentieerde contentproviders.
* Kloon. https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git 

    U moet de code in [Versleutelen met DRM wijzigen met .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) om FairPlay-configuraties toe te voegen.  

## <a name="configure-content-protection-in-azure-media-services"></a>Inhoudsbeveiliging configureren in Azure Media Services

Ga als volgt te werk in de methode [GetOrCreateContentKeyPolicyAsync:](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189)

De opmerking ongedaan maken van de code die de optie FairPlay-beleid configureert:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Geef ook geen commentaar op de code die CBCS ContentKeyPolicyOption toevoegt aan de lijst met ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Offlinemodus inschakelen

Als u de offlinemodus wilt inschakelen, maakt u een aangepast streamingbeleid en gebruikt u de naam ervan bij het maken van een StreamingLocator in [CreateStreamingLocatorAsync.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563)
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

Nu is uw Media Services-account geconfigureerd om offline FairPlay-licenties te leveren.

## <a name="sample-ios-player"></a>Voorbeeld van iOS Player

FPS offline-modus ondersteuning is alleen beschikbaar op iOS 10 en hoger. De FPS Server SDK (versie 3.0 of hoger) bevat het document en voorbeeld voor fps offline modus. In het bijzonder bevat FPS Server SDK (versie 3.0 of hoger) de volgende twee items met betrekking tot de offlinemodus:

* Document: "Offline afspelen met FairPlay Streaming en HTTP Live Streaming." Apple, 14 september 2016. In FPS Server SDK-versie 4.0 wordt dit document samengevoegd tot het belangrijkste FPS-document.
* Voorbeeldcode: HLSCatalog-voorbeeld (onderdeel van de FPS Server SDK van Apple) voor de offline-modus van fps in de \FairPlay Streaming Server SDK-versie 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. In de HLSCatalog-voorbeeld-app worden de volgende codebestanden gebruikt om functies in de offlinemodus te implementeren:

    - AssetPersistenceManager.swift code file: AssetPersistenceManager is de belangrijkste klasse in dit voorbeeld die laat zien hoe:

        - Beheer het downloaden van HLS-streams, zoals de API's die worden gebruikt om downloads te starten en te annuleren en om bestaande assets van apparaten te verwijderen.
        - Houd de voortgang van het downloaden in de gaten.
    - AssetListTableViewController.swift en AssetListTableViewCell.swift code bestanden: AssetListTableViewController is de belangrijkste interface van dit voorbeeld. Het bevat een lijst met elementen die het voorbeeld kan gebruiken om een download af te spelen, te downloaden, te verwijderen of te annuleren. 

Deze stappen laten zien hoe u een draaiende iOS-speler instelt. Als u begint met het hlscatalogusvoorbeeld in FPS Server SDK-versie 4.0.1, voert u de volgende codewijzigingen aan:

Implementeer de methode `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` in HLSCatalog\Shared\Managers\ContentKeyDelegate.swift met behulp van de volgende code. Laat "drmUr" een variabele zijn die is toegewezen aan de HLS-URL.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

Implementeer de methode `requestApplicationCertificate()`in HLSCatalog\Shared\Managers\ContentKeyDelegate.swift. Deze implementatie is afhankelijk van of u het certificaat (alleen openbare sleutel) insluit bij het apparaat of het certificaat op het web host. De volgende implementatie maakt gebruik van het gehoste toepassingscertificaat dat in de testvoorbeelden wordt gebruikt. Laat "certUrl" een variabele zijn die de URL van het toepassingscertificaat bevat.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Voor de uiteindelijke geïntegreerde test worden zowel de video-URL als de URL van het toepassingscertificaat weergegeven in de sectie 'Geïntegreerde test'.

Voeg in HLSCatalog\Shared\Resources\Streams.plist de URL van uw testvideo toe. Voor de inhoudssleutel-ID gebruikt u de URL voor het aanschaffen van fairplay-licenties met het skd-protocol als de unieke waarde.

![Offline FairPlay iOS-appstreams](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Gebruik je eigen URL van testvideo, fairplay-licentieverwerving-URL en URL van toepassingscertificaat als je ze hebt ingesteld. Of u doorgaan naar de volgende sectie, die testmonsters bevat.

## <a name="integrated-test"></a>Geïntegreerde test

Drie testmonsters in Media Services bestrijken de volgende drie scenario's:

* FPS-beveiliging, met video, audio en alternatieve audiotrack
* FPS beschermd, met video en audio, maar geen alternatieve audiotrack
* FPS beschermd, met alleen video en geen audio

U deze voorbeelden vinden op [deze demosite,](https://aka.ms/poc#22)met het bijbehorende toepassingscertificaat dat wordt gehost in een Azure-web-app.
Met het voorbeeld van versie 3 of versie 4 van de FPS Server SDK wordt als een hoofdafspeellijst alternatieve audio bevat, wordt tijdens de offlinemodus alleen audio afgespeeld. Daarom moet u de alternatieve audio strippen. Met andere woorden, de tweede en derde voorbeelden die eerder worden vermeld, werken in de online en offline modus. Het voorbeeld dat eerst wordt weergegeven, speelt alleen audio af tijdens de offlinemodus, terwijl online streaming naar behoren werkt.

## <a name="faq"></a>Veelgestelde vragen

De volgende veelgestelde vragen bieden hulp bij het oplossen van problemen:

- **Waarom wordt alleen audio afgespeeld, maar geen video tijdens de offline modus?** Dit gedrag lijkt te zijn door het ontwerp van de steekproef app. Wanneer een alternatieve audiotrack aanwezig is (wat het geval is voor HLS) tijdens de offline modus, worden zowel iOS 10 als iOS 11 standaard weergegeven op het alternatieve audiospoor. Als u dit gedrag wilt compenseren voor de offlinefps-modus, verwijdert u het alternatieve audionummer uit de stream. Om dit te doen op Media Services, voegt u het dynamische manifestfilter 'audio-only=false' toe. Met andere woorden, een HLS URL eindigt met .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Waarom speelt het nog steeds audio alleen af zonder video tijdens de offline modus nadat ik audio-only=false heb toegevoegd?** Afhankelijk van het ontwerp van de cachesleutel van het contentdelivery-netwerk (CDN) kan de inhoud in de cache in de cache worden opgeslagen. Verwijder de cache.
- **Wordt de FPS offline modus naast iOS 10 ook ondersteund in iOS 11?** Ja. Fps offline modus wordt ondersteund voor iOS 10 en iOS 11.
- **Waarom kan ik het document 'Offline afspelen met FairPlay Streaming en HTTP Live Streaming' niet vinden in de FPS Server SDK?** Sinds FPS Server SDK versie 4 is dit document samengevoegd tot de 'FairPlay Streaming Programming Guide'.
- **Wat is de gedownloade/offline bestandsstructuur op iOS-apparaten?** De gedownloade bestandsstructuur op een iOS-apparaat ziet eruit als de volgende schermafbeelding. De `_keys` map slaat gedownloade FPS-licenties op, met één winkelbestand voor elke licentieservicehost. De `.movpkg` map slaat audio- en video-inhoud op. De eerste map met een naam die eindigt met een streepje gevolgd door een numerieke bevat video-inhoud. De numerieke waarde is de PeakBandwidth van de videoweergaven. De tweede map met een naam die eindigt met een streepje gevolgd door 0 bevat audio-inhoud. De derde map met de naam "Data" bevat de hoofdafspeellijst van de FPS-inhoud. Ten slotte biedt boot.xml een `.movpkg` volledige beschrijving van de inhoud van de map. 

![Offline FairPlay iOS voorbeeld app bestandsstructuur](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Een voorbeeld van boot.xml-bestand:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="next-steps"></a>Volgende stappen

Bekijken hoe u kunt [beveiligen met AES-128](protect-with-aes128.md)
