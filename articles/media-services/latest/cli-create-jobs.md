---
title: Azure CLI-voorbeeldscript - Een taak maken en verzenden | Microsoft Docs
description: In het Azure CLI-script in dit onderwerp ziet u hoe u een taak kunt verzenden naar een eenvoudige coderingstransformatie met behulp van de HTTPS-URL.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 08caf4b78b1042d5753dba2bc73e93fa3a250a17
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122376"
---
# <a name="cli-example-create-and-submit-a-job"></a>CLI-voorbeeld: Een taak maken en verzenden

Wanneer u in Media Services v3 taken verzendt voor het verwerken van uw video's, moet u aan Media Services de locatie van de invoervideo doorgeven. Een van de opties is om een HTTPS-URL op te geven als een taak invoer (zoals weer gegeven in dit artikel). 

## <a name="prerequisites"></a>Vereisten 

[Een Azure Media Services-account maken](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Voorbeeldscript

Wanneer u `az ams job start` uitvoert, kunt u een label instellen op de uitvoer van de taak. Het label kan later worden gebruikt om te bepalen waarvoor deze uitvoerasset is bedoeld. 

- Als u een waarde aan het label toewijst, stelt u '--output-assets' in op 'assetname=label'
- Als u geen waarde aan het label toewijst, stelt u '--output-assets' in op 'assetname='.
  Let erop dat u '=' toevoegt aan `output-assets`. 

```azurecli
az ams job start \
  --name testJob001 \
  --transform-name testEncodingTransform \
  --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' \
  --files 'Ignite-short.mp4' \
  --output-assets testOutputAssetName= \
  -a amsaccount \
  -g amsResourceGroup 
```

U krijgt ongeveer de volgende reactie:

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

## <a name="next-steps"></a>Volgende stappen

[AZ AMS Job (CLI)](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
