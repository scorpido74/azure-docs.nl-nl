---
title: Videobestanden streamen met Azure Media Services en de Azure CLI
description: Volg de stappen van deze zelfstudie om een nieuw Azure Media Services-account te maken, een bestand te coderen en te streamen naar Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, streamen
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: ''
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 91259e10966173cb701b867f5b3ed362112beef3
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382780"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---azure-cli"></a>Zelfstudie: Een extern bestand coderen op basis van URL en de video streamen - Azure CLI

In deze zelfstudie ziet u hoe u video's eenvoudig coderen en streamen op verschillende browsers en apparaten met Azure Media Services en de Azure CLI. U invoerinhoud opgeven met HTTPS- of SAS-URL's of paden naar bestanden in Azure Blob-opslag.

Het voorbeeld in dit artikel codeert inhoud die u toegankelijk maakt via een HTTPS-URL. Media Services v3 ondersteunt momenteel geen chunked transfer codering via HTTPS URL's.

Tegen het einde van deze zelfstudie kun je een video streamen.  

![De video afspelen](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

Voordat u media-inhoud in Azure versleutelen, coderen, analyseren, beheren en streamen, moet u een Media Services-account maken. Dat account moet zijn gekoppeld aan een of meer opslagaccounts.

Uw Media Services-account en alle bijbehorende opslagaccounts moeten in hetzelfde Azure-abonnement zitten. We raden u aan opslagaccounts te gebruiken die zich op dezelfde plaats bevinden als het Media Services-account om de kosten voor latentie en gegevensuitgangte beperken.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

```azurecli-interactive
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken

In dit voorbeeld maken we een Algemeen Doel v2 Standard LRS-account aan.

Als u wilt experimenteren met opslagaccounts, gebruikt u `--sku Standard_LRS`. Wanneer u een SKU kiest voor `--sku Standard_RAGRS`productie, u overwegen om geografische replicatie te gebruiken voor bedrijfscontinuïteit. Zie [Opslagaccounts](/cli/azure/storage/account) voor meer informatie.

```azurecli-interactive
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Een Azure Media Services-account maken

```azurecli-interactive
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Je krijgt een antwoord als volgt:

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-the-streaming-endpoint"></a>Het streaming-eindpunt starten

Met de volgende opdracht Azure CLI wordt het standaard **eindpunt voor streaming gestart.**

```azurecli-interactive
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Je krijgt een antwoord als volgt:

```
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

Als het streamingeindpunt al actief is, ontvangt u dit bericht:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Een transformatie maken voor adaptieve bitratecodering

Maak een **Transformatie** om veelvoorkomende taken voor het coderen of analyseren van video's te configureren. In dit voorbeeld doen we adaptieve bitrate codering. Vervolgens dienen we een baan in onder de transformatie die we hebben gemaakt. De taak is het verzoek aan Media Services om de transformatie toe te passen op de opgegeven invoer van video- of audio-inhoud.

```azurecli-interactive
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Je krijgt een antwoord als volgt:

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>Een uitvoeractivum maken

Maak een **uitvoeritem** dat u gebruiken als de uitvoer van de coderingstaak.

```azurecli-interactive
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Je krijgt een antwoord als volgt:

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-a-job-by-using-https-input"></a>Een taak starten met HTTPS-invoer

Wanneer u taken indient om video's te verwerken, moet u Media Services vertellen waar u de invoervideo vinden. Een optie is het opgeven van een HTTPS-URL als de taakinvoer, zoals in dit voorbeeld wordt weergegeven.

Wanneer u `az ams job start` uitvoert, kunt u een label instellen op de uitvoer van de taak. U het label vervolgens gebruiken om te bepalen waar het uitvoermiddel voor is.

- Als u een waarde aan het label toewijst, stelt u '--output-assets' in op 'assetname=label'.
- Als u geen waarde aan het label toewijst, stelt u '--output-assets' in op 'assetname='.

  Merk op dat we "=" toevoegen aan de `output-assets`.

```azurecli-interactive
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup
```

Je krijgt een antwoord als volgt:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>Status controleren

Controleer binnen vijf minuten de status van de taak. Het zou 'Klaar' moeten zijn. Het is nog niet klaar, controleer het over een paar minuten nog eens. Als het klaar is, gaat u naar de volgende stap en maakt u een **streaminglocator.**

```azurecli-interactive
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Maak een streaming locator en krijg een pad

Wanneer de codering is voltooid, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. Maak hiervoor eerst een Streaming Locator. Bouw vervolgens streaming-URL's die clients kunnen gebruiken.

### <a name="create-a-streaming-locator"></a>Een streaming-locator te maken

```azurecli-interactive
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Je krijgt een antwoord als volgt:

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>Download streaming locator-paden

```azurecli-interactive
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Je krijgt een antwoord als volgt:

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

Kopieer het HTTP-pad voor live streaming (HLS). In dit geval is `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`het.

## <a name="build-the-url"></a>De URL samenstellen

### <a name="get-the-streaming-endpoint-host-name"></a>De naam van de streaming endpointhost opmaken

```azurecli-interactive
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Kopieer de waarde van `hostName`. In dit geval is `amsaccount-usw22.streaming.media.azure.net`het.

### <a name="assemble-the-url"></a>De URL samenvoegen

'https://' + &lt;waarde van hostnaam&gt; + &lt;padwaarde Hls&gt;

Hier volgt een voorbeeld:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Afspelen testen met Azure Media Player

> [!NOTE]
> Als een speler wordt gehost op een HTTPS-site, moet u de URL starten met 'https'.

1. Open een webbrowser en [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)ga naar .
2. Plak in het **vak URL** de URL die u in de vorige sectie hebt gebouwd. U de URL plakken in de indeling HLS, Dash of Vloeiend. Azure Media Player gebruikt automatisch een geschikt streamingprotocol voor afspelen op uw apparaat.
3. Selecteer **Speler bijwerken**.

>[!NOTE]
>Azure Media Player kan worden gebruikt voor testdoeleinden, maar mag niet worden gebruikt in een productieomgeving.

## <a name="clean-up-resources"></a>Resources opschonen

Als u geen van de bronnen in uw resourcegroep meer nodig hebt, inclusief de MediaServices en opslagaccounts die u voor deze zelfstudie hebt gemaakt, verwijdert u de brongroep.

Voer deze opdracht Azure CLI uit:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Media Services](media-services-overview.md)
