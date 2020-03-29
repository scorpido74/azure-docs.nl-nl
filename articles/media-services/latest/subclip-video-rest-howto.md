---
title: Een video onderknippen wanneer u codeert met Azure Media Services REST
description: In dit onderwerp wordt beschreven hoe u een video subclipt wanneer u codeert met Azure Media Services met REST
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: c39aded55fe36cb130459a4f6f119f872b1adbc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514320"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Een video onderknippen bij het coderen met Media Services - REST

U een video bijsnijden of subclipen wanneer u deze codeert met een [taak.](https://docs.microsoft.com/rest/api/media/jobs) Deze functionaliteit werkt met elke [transformatie](https://docs.microsoft.com/rest/api/media/transforms) die is gebouwd met de [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) presets of de [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) presets. 

In het voorbeeld REST in dit onderwerp wordt een taak gemaakt waarmee een video wordt bijgemaakt terwijl deze een coderingstaak verzendt. 

## <a name="prerequisites"></a>Vereisten

Als u de in dit onderwerp beschreven stappen wilt voltooien, moet u het volgende doen:

- [Maak een Azure Media Services-account](create-account-cli-how-to.md).
- [Postman configureren voor API-aanroepen van Azure Media Services REST](media-rest-apis-with-postman.md).
    
    Volg de laatste stap in het onderwerp [Azure AD-token opvragen](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Maak een transformatie en een uitvoeractiva. U zien hoe u een transformatie en een uitvoerelementen maakt in het [afstandsbedieningsbestand coderen op basis van URL en de video - REST-zelfstudie streamt.](stream-files-tutorial-with-rest.md)
- Bekijk het [concepttopic codering.](encoding-concept.md)

## <a name="create-a-subclipping-job"></a>Een subclipping-taak maken

1. Selecteer in de Postman-verzameling die u hebt gedownload de optie **Transformaties en taken** -> **Job maken met subclipping**.
    
    Het **PUT-verzoek** ziet er als volgt uit:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Werk de waarde van de omgevingsvariabele 'transformName' bij met uw transformatienaam. 
1. Selecteer het tabblad **Hoofdlichaam** en werk de naam 'myOutputAsset' bij met de naam uitvoerasset.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Druk op **Verzenden**.

    U ziet het **antwoord** met de informatie over de taak die is gemaakt en verzonden en de status van de taak. 

## <a name="next-steps"></a>Volgende stappen

[Coderen met een aangepaste transformatie](custom-preset-rest-howto.md) 
