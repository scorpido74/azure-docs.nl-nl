---
title: HLS-inhoud beveiligen met offline Apple FairPlay-Azure | Microsoft Docs
description: Dit onderwerp bevat een overzicht en laat zien hoe u Azure Media Services kunt gebruiken om uw HTTP Live Streaming-inhoud (HLS) dynamisch te versleutelen met Apple FairPlay in de offline modus.
services: media-services
keywords: HLS, DRM, FairPlay streaming (FPS), offline, iOS 10
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
ms.openlocfilehash: 83fa8c9c6d98728d48ff4ed8993963cdbd522724
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974118"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Offline FairPlay streaming voor iOS 

 Azure Media Services biedt een aantal goed ontworpen [Content Protection-Services](https://azure.microsoft.com/services/media-services/content-protection/) die betrekking hebben op:

- Microsoft PlayReady
- Google Widevine
    
    Widevine is een service van Google Inc. en is onderworpen aan de service voorwaarden en het privacybeleid van Google, Inc.
- Apple FairPlay
- AES-128-versleuteling

Digital Rights Management (DRM)/Advanced Encryption Standard (AES) versleuteling van inhoud wordt dynamisch uitgevoerd op aanvraag voor verschillende streaming-protocollen. De bezorgings services van DRM-licentie/AES-ontsleuteling worden ook geleverd door Media Services.

Naast het beveiligen van inhoud voor online streaming via verschillende streaming-protocollen, is de offline modus voor beveiligde inhoud ook een regel matig aangevraagde functie. Ondersteuning voor offline modus is vereist voor de volgende scenario's:

* Afspelen als Internet verbinding niet beschikbaar is, zoals tijdens het reizen.
* Sommige inhouds providers kunnen geen DRM-licentie levering toestaan buiten de grens van een land/regio. Als gebruikers inhoud willen bekijken terwijl ze zich buiten het land of de regio bevinden, is offline downloaden vereist.
* In sommige landen/regio's is Internet Beschik baarheid en/of band breedte nog steeds beperkt. Gebruikers kunnen ervoor kiezen om eerst te downloaden om inhoud te kunnen bekijken in een oplossing die hoog genoeg is voor een goede kijk ervaring. In dit geval is het probleem doorgaans niet de beschik baarheid van het netwerk, maar beperkte netwerk bandbreedte. Over-the-top (OTT)/online video platform (OVP)-providers vragen om ondersteuning van offline modus.

In dit artikel wordt Inge gaan op de ondersteuning voor offline modus van FairPlay streaming (FPS) die is gericht op apparaten met iOS 10 of hoger. Deze functie wordt niet ondersteund voor andere Apple-platformen, zoals watchOS, tvOS of Safari op macOS.

> [!NOTE]
> Offline DRM wordt alleen gefactureerd voor het maken van één aanvraag voor een licentie wanneer u de inhoud downloadt. Er worden geen fouten in rekening gebracht.

## <a name="prerequisites"></a>Vereisten

Voordat u offline DRM implementeert voor FairPlay op een iOS 10 +-apparaat:

* Online inhouds beveiliging controleren voor FairPlay: 

    - [Vereisten voor en configuratie van Apple FairPlay-licenties](fairplay-license-overview.md)
    - [De Digital Rights Management-service gebruiken voor dynamische versleuteling en licentielevering](protect-with-drm.md)
    - Een .NET-voor beeld met configuratie van online FPS streaming: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Haal de FPS-SDK op uit het Apple Developer Network. De FPS-SDK bevat twee onderdelen:

    - De server-SDK van FPS, die de sleutel Security module (KSM), client voorbeelden, een specificatie en een set test vectoren bevat.
    - Het FPS-implementatie pakket, dat de D-functie specificatie bevat, evenals instructies over het genereren van het FPS-certificaat, de klantspecifieke persoonlijke sleutel en de geheime sleutel van de toepassing. Apple geeft het FPS-implementatie pakket alleen voor de gelicentieerde inhouds providers.
* https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git klonen. 

    U moet de code in [versleutelen met DRM wijzigen met behulp van .net](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) om Fairplay-configuraties toe te voegen.  

## <a name="configure-content-protection-in-azure-media-services"></a>Beveiliging van inhoud configureren in Azure Media Services

Ga als volgt te werk in de methode [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) :

Verwijder de opmerking over de code die de FairPlay-beleids optie configureert:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Verwijder ook de opmerking over de code waarmee CBCS ContentKeyPolicyOption wordt toegevoegd aan de lijst met ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Offline modus inschakelen

Als u de offline modus wilt inschakelen, maakt u een aangepaste StreamingPolicy en gebruikt u de naam bij het maken van een StreamingLocator in [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
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

Uw Media Services-account is nu geconfigureerd voor het leveren van offline FairPlay-licenties.

## <a name="sample-ios-player"></a>Voor beeld iOS-Player

Ondersteuning voor de offline modus van FPS is alleen beschikbaar op iOS 10 en hoger. De FPS Server SDK (versie 3,0 of hoger) bevat het document en het voor beeld voor de offline modus FPS. Met name de server-SDK (versie 3,0 of hoger) van FPS bevat de volgende twee items die betrekking hebben op de offline modus:

* Document: ' offline afspelen met FairPlay streaming en HTTP Live Streaming. ' Apple, 14 september 2016. In FPS Server SDK versie 4,0 wordt dit document samengevoegd in het hoofd-FPS-document.
* Voorbeeld code: HLSCatalog-voor beeld (onderdeel van de bps Server SDK van Apple) voor de offline modus van FPS in de \FairPlay streaming server SDK-versie 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. In de HLSCatalog-voor beeld-app worden de volgende code bestanden gebruikt om de functies van de offline modus te implementeren:

    - AssetPersistenceManager. Swift-code bestand: AssetPersistenceManager is de belangrijkste klasse in dit voor beeld waarin wordt getoond hoe:

        - Het downloaden van HLS-streams beheren, zoals de Api's die worden gebruikt om down loads te starten en te annuleren en om bestaande activa van apparaten te verwijderen.
        - De voortgang van de down load bewaken.
    - AssetListTableViewController. Swift en AssetListTableViewCell. Swift code files: AssetListTableViewController is de belangrijkste interface van dit voor beeld. Het bevat een lijst met assets die het voor beeld kan gebruiken voor het afspelen, downloaden, verwijderen of annuleren van een down load. 

Deze stappen laten zien hoe u een actieve iOS-speler kunt instellen. Als u begint met het HLSCatalog-voor beeld in FPS Server SDK version 4.0.1, moet u de volgende code wijzigingen aanbrengen:

Implementeer in HLSCatalog\Shared\Managers\ContentKeyDelegate.swift de methode `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` door de volgende code te gebruiken. Stel ' drmUr ' een variabele toe die is toegewezen aan de HLS-URL.

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

Implementeer in HLSCatalog\Shared\Managers\ContentKeyDelegate.swift de methode `requestApplicationCertificate()`. Deze implementatie is afhankelijk van het feit of u het certificaat (alleen open bare sleutel) insluit met het apparaat of als host van het certificaat op het web. De volgende implementatie maakt gebruik van het certificaat van de gehoste toepassing die wordt gebruikt in de test voorbeelden. Laat ' certUrl ' een variabele zijn die de URL van het toepassings certificaat bevat.

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

Voor de laatste geïntegreerde test worden zowel de video-URL als de URL van het toepassings certificaat weer gegeven in de sectie ' geïntegreerde test '.

Voeg in HLSCatalog\Shared\Resources\Streams.plist uw test video-URL toe. Gebruik voor de inhouds sleutel-ID de FairPlay-URL voor het verkrijgen van licenties met het SKD-protocol als unieke waarde.

![Offline FairPlay iOS-app-streams](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Gebruik uw eigen test video-URL, FairPlay licentie verwervings-URL en de URL van het toepassings certificaat als u deze hebt ingesteld. Of u kunt door gaan met de volgende sectie, die test voorbeelden bevat.

## <a name="integrated-test"></a>Geïntegreerde test

Drie test voorbeelden in Media Services hebben betrekking op de volgende drie scenario's:

* Met FPS beveiligde, met video, audio en alternatief audio spoor
* Beveiligt FPS, met video en audio, maar geen alternatief audio spoor
* Met FPS beveiligd, met alleen video en zonder audio

U kunt deze voor beelden vinden op [deze demo site](https://aka.ms/poc#22), met het bijbehorende toepassings certificaat dat wordt gehost in een Azure-web-app.
Met versie 3 of versie 4 van de FPS Server-SDK, als een Master-afspeel lijst alternatieve audio bevat, wordt tijdens de offline modus alleen audio afgespeeld. Daarom moet u de alternatieve audio verwijderen. Met andere woorden, de tweede en derde steek proef die eerder in de online-en offline modus werkt. Het voor beeld dat in de lijst wordt weer gegeven, speelt alleen geluid af tijdens de offline modus, terwijl online streaming goed werkt.

## <a name="faq"></a>Veelgestelde vragen

De volgende veelgestelde vragen bieden hulp bij het oplossen van problemen:

- **Waarom wordt audio alleen afgespeeld maar niet video tijdens de offline modus?** Dit gedrag lijkt inherent aan het ontwerp van de voor beeld-app. Wanneer er een alternatief audio spoor aanwezig is (dit is het geval voor HLS) tijdens de offline modus, wordt de standaard instelling voor het alternatieve audio spoor door iOS 10 en iOS 11 ingesteld. Als u dit gedrag voor de offline modus van FPS wilt compenseren, verwijdert u het alternatieve audio spoor uit de stroom. Als u dit wilt doen op Media Services, voegt u het dynamische manifest filter toe ' alleen audio = false '. Met andere woorden, een HLS-URL eindigt op. ISM/manifest (Format = M3U8-AAPL, alleen audio = false). 
- **Waarom wordt alleen audio zonder video afgespeeld tijdens de offline modus nadat ik alleen audio heb toegevoegd = False?** Afhankelijk van het ontwerp van de cache sleutel voor Content Delivery Network (CDN), kan de inhoud in de cache worden opgeslagen. Verwijder de cache.
- **Is de offline modus voor FPS ook ondersteund op iOS 11 naast iOS 10?** Ja. De offline modus van FPS wordt ondersteund voor iOS 10 en iOS 11.
- **Waarom kan ik het document ' offline afspelen met FairPlay streaming en HTTP Live Streaming ' niet vinden in de server-SDK van FPS?** Sinds FPS Server SDK versie 4 werd dit document samengevoegd in de programmeer handleiding voor FairPlay-gegevens stromen.
- **Wat is de gedownloade/offline bestands structuur op iOS-apparaten?** De gedownloade bestands structuur op een iOS-apparaat ziet eruit als in de volgende scherm afbeelding. In de map `_keys` worden gedownloade FPS-licenties opgeslagen, met één archief bestand voor elke host van de licentie service. De inhoud van audio en video wordt opgeslagen in de map `.movpkg`. De eerste map met een naam die eindigt op een streepje gevolgd door een numeriek bevat video-inhoud. De numerieke waarde is de PeakBandwidth van de video weergaven. De tweede map met een naam die eindigt op een streepje gevolgd door 0, bevat audio-inhoud. De derde map met de naam ' data ' bevat de lijst Master van de FPS-inhoud. Ten slotte biedt boot. XML een volledige beschrijving van de inhoud van de map `.movpkg`. 

![Bestands structuur van de offline-FairPlay iOS-voorbeeld toepassing](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Een voor beeld van een boot. XML-bestand:

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
