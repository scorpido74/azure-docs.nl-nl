---
title: Signaal beschrijvende audiotracks met Azure Media Services v3 | Microsoft Documenten
description: Volg de stappen van deze zelfstudie om een bestand te uploaden, de video te coderen, beschrijvende audiotracks toe te voegen en uw inhoud te streamen met Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: 0d8f88e6c2fe273efa969278146de67ba18eaecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392187"
---
# <a name="signal-descriptive-audio-tracks"></a>Signaal beschrijvende audiotracks

U een gesproken kuur aan uw video toevoegen om gebruikers met een visuele beperking te helpen de video-opname te volgen door naar de gesproken tekst te luisteren. In Media Services v3 signaleert u beschrijvende audiotracks door de audiotrack in het manifestbestand te annoteren.

In dit artikel ziet u hoe u een video codeert, een MP4-bestand (AAC-codec) met alleen audio uploadt dat beschrijvende audio bevat in het uitvoerelement en het .ism-bestand bewerkt om de beschrijvende audio op te nemen.

## <a name="prerequisites"></a>Vereisten

- [Een Azure Media Services-account maken](create-account-cli-how-to.md).
- Volg de stappen in [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Toegang tot de Azure Media Services-API met de Azure CLI) en sla de referenties op. U hebt deze nodig voor toegang tot de API.
- Bekijk [dynamische verpakking](dynamic-packaging-overview.md).
- Bekijk de zelfstudie [Van video's uploaden, coderen en streamen.](stream-files-tutorial-with-api.md)

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Een invoerasset maken en er een lokaal bestand in uploaden 

Met de functie **CreateInputAsset** wordt een nieuwe [invoerasset](https://docs.microsoft.com/rest/api/media/assets) gemaakt en het opgegeven lokale videobestand wordt hierin geladen. Deze **asset** wordt gebruikt als invoer voor uw coderingstaak. In Media Services v3 kan de invoer naar een **taak** een **asset**zijn of inhoud die u via HTTPS-URL's beschikbaar stelt voor uw Media Services-account. 

Zie [dit artikel](job-input-from-http-how-to.md) als u wilt weten hoe u een HTTPS-URL coderen.  

In Media Services v3 kunt u Azure Storage-API's gebruiken om bestanden te uploaden. Het volgende .NET-fragment laat zien hoe.

De volgende functie voert deze acties uit:

* Maakt een **asset** 
* Haalt een beschrijfbare [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) op voor de [container in opslag](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) van de asset
* Uploadt het bestand naar de container in opslag met de SAS-URL

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Als u de naam van het gemaakte invoerelement aan andere methoden `Name` moet doorgeven, moet `CreateInputAssetAsync`u de eigenschap gebruiken op het assetobject dat is geretourneerd uit bijvoorbeeld inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Een uitvoeritem maken om het resultaat van de coderingstaak op te slaan

In de [uitvoerasset](https://docs.microsoft.com/rest/api/media/assets) wordt het resultaat van de coderingstaak opgeslagen. In de volgende functie ziet u hoe u een uitvoerelement maakt.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Als u de naam van het gemaakte uitvoerelement moet doorgeven aan `Name` andere methoden, moet `CreateIOutputAssetAsync`u de eigenschap gebruiken op het assetobject dat is geretourneerd uit bijvoorbeeld outputAsset.Name. 

Geef in het geval van `outputAsset.Name` dit `SubmitJobAsync` artikel `UploadAudioIntoOutputAsset` de waarde door aan de en functies.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Een transformatie maken en een taak die het geüploade bestand codeert

Bij het coderen of verwerken van inhoud in Media Services is het een gangbaar patroon om de coderingsinstellingen als recept in te stellen. U dient vervolgens een **taak** in te dienen om het recept toe te passen op een video. Door voor elke nieuwe video nieuwe taken in te dienen, past u dat recept toe op alle video's in de bibliotheek. Een recept in Media Services wordt aangeroepen als een **transformatie**. Zie [Transformeren en taken voor](transform-concept.md)meer informatie. Het voorbeeld dat wordt beschreven in deze zelfstudie definieert een recept dat de video codeert om het te streamen naar tal van iOS- en Android-apparaten. 

In het volgende voorbeeld wordt een transformatie veroorzaakt (als deze niet bestaat).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

Met de volgende functie wordt een taak ingediend.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Wacht tot de taak is voltooid

De taak neemt enige tijd in beslag en wanneer deze is voltooid, wordt u hiervan op de hoogte gesteld. We raden u aan Event Grid te gebruiken om te wachten tot de taak is voltooid.

De taak gaat meestal door de volgende statussen: **Gepland**, **Wachtrij**, **Verwerking**, **Voltooid** (de uiteindelijke status). Als bij de taak een fout is opgetreden is, krijgt u de status **Fout**. Als de taak momenteel wordt geannuleerd, krijgt u de melding **Wordt geannuleerd** en **Geannuleerd** wanneer het annuleren is voltooid.

Zie [Gebeurtenissen op het afhandelen van gebeurtenisrasters](reacting-to-media-services-events.md)voor meer informatie .

## <a name="upload-the-audio-only-mp4-file"></a>Het MP4-audiobestand uploaden

Upload het aanvullende MP4-bestand (Alleen audio) met beschrijvende audio in het uitvoerelement.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Hier is een voorbeeld van `UpoadAudioIntoOutputAsset` een oproep naar de functie:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Het .ism-bestand bewerken

Wanneer uw coderingstaak is uitgevoerd, bevat het uitvoeritem de bestanden die door de coderingstaak zijn gegenereerd. 

1. Navigeer in de Azure-portal naar het opslagaccount dat is gekoppeld aan uw Media Services-account. 
1. Zoek de container met de naam van uw uitvoeractief. 
1. Zoek in de container het .ism-bestand en klik op **Blob bewerken** (in het rechtervenster). 
1. Bewerk het .ism-bestand door de informatie over het geüploade MP4-bestand (AAC-codec) met beschrijvende audio toe te voegen en druk op **Opslaan** wanneer u klaar bent.

    Als u de beschrijvende audiotracks wilt signaleren, moet u parameters 'toegankelijkheid' en 'rol' toevoegen aan het .ism-bestand. Het is uw verantwoordelijkheid om deze parameters correct in te stellen om een audiotrack als audiobeschrijving te signaleren. Voeg bijvoorbeeld `<param name="accessibility" value="description" />` toe `<param name="role" value="alternate" />` aan het .ism-bestand voor een specifieke audiotrack, zoals in het volgende voorbeeld wordt weergegeven.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Ontvang een streaming locator

Nadat de codering is voltooid, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. U kunt dit doen in twee stappen: maak eerst een [streaming-locator](https://docs.microsoft.com/rest/api/media/streaminglocators) en bouw vervolgens de streaming-URL's die clients kunnen gebruiken. 

Het proces van het maken van een **streaming-locator** wordt publiceren genoemd. De **streaming-locator** is standaard onmiddellijk geldig nadat u de API-aanroepen hebt gemaakt en totdat deze wordt verwijderd, tenzij u de optionele start- en eindtijden configureert. 

Bij het maken van een [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) moet u de gewenste **StreamingPolicyName** opgeven. In dit voorbeeld streamt u in-the-clear (of niet-versleutelde inhoud), zodat het vooraf gedefinieerde duidelijke streamingbeleid **(PredefinedStreamingPolicy.ClearStreamingOnly)** wordt gebruikt.

> [!IMPORTANT]
> Wanneer u een aangepast [streamingbeleid](https://docs.microsoft.com/rest/api/media/streamingpolicies) gebruikt, moet u een beperkte set met dergelijke beleidsregels ontwerpen voor uw Media Service-account, en deze opnieuw gebruiken voor de StreamingLocators wanneer dezelfde versleutelingsopties en protocollen nodig zijn. Uw Media Service-account heeft een quotum voor het aantal streaming-beleidsvermeldingen. U hoeft geen nieuw streaming-beleid te maken voor elke streaming-locator.

Bij de volgende code wordt ervan uitgegaan dat u de functie met een unieke locatorName aanroept.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Terwijl in het voorbeeld in dit onderwerp streaming wordt behandeld, kunt u dezelfde aanroep gebruiken om een streaming-locator te maken voor het leveren van video via progressief downloaden.

### <a name="get-streaming-urls"></a>Streaming-URL's ophalen

Nu de [streaming-locator](https://docs.microsoft.com/rest/api/media/streaminglocators) is gemaakt, kunt u de streaming-URL's ophalen, zoals weergegeven in **GetStreamingURLs**. Als u een URL wilt samenstellen, moet u de hostnaam van het [streaming-eindpunt](https://docs.microsoft.com/rest/api/media/streamingendpoints) en het pad van de **streaming-locator** samenvoegen. In dit voorbeeld wordt het *standaard* **streaming-eindpunt** gebruikt. Wanneer u voor het eerst een Media Service-account maakt, wordt dit *standaard* **streaming-eindpunt** gestopt. Daarom moet u **Start** aanroepen.

> [!NOTE]
> In deze methode hebt u de locatorName nodig die is gebruikt bij het maken van de **streaming-locator** voor de uitvoerasset.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Testen met Azure Media Player

In dit artikel gebruiken we Azure Media Player om de stream te testen. 

> [!NOTE]
> Als een speler wordt gehost op een https-site, moet u de URL bijwerken naar 'https'.

1. Open een webbrowser en [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)navigeer naar .
2. Plak in het **vak URL:** een van de streaming URL-waarden die u van uw toepassing hebt ontvangen. 
 
     U kunt de URL plakken in de HLS-, Dash-, of Smooth-indeling. Azure Media Player schakelt over op naar een geschikt streaming-protocol zodat de stream automatisch op uw apparaat wordt afgespeeld.
3. Klik op **Update Player**.

Azure Media Player kan worden gebruikt voor testdoeleinden, maar mag niet worden gebruikt in een productieomgeving. 

## <a name="next-steps"></a>Volgende stappen

[Video's analyseren](analyze-videos-tutorial-with-api.md)
