---
title: Video bestanden streamen met Azure Media Services-node. js | Microsoft Docs
description: Volg de stappen in deze zelf studie om een nieuw Azure Media Services-account te maken, een bestand te coderen en te streamen naar Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, streamen
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: fa9fbf3bac55ca0b26c3644b7f6818fa96088612
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639394"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Zelfstudie: Een extern bestand coderen op basis van URL en het video knooppunt. js streamen

In deze zelf studie leert u hoe eenvoudig het is om Video's te coderen en te streamen op een groot aantal verschillende browsers en apparaten met behulp van Azure Media Services. De invoerinhoud kan worden opgegeven met HTTPS-URL's, SAS-URL's of paden naar bestanden in Azure Blob-opslag.

In het voor beeld in dit artikel wordt inhoud gecodeerd die u via een HTTPS-URL kunt openen. Op dit moment biedt AMS v3 geen ondersteuning voor gesegmenteerde overdrachtscodering via HTTPS-URL's.

Aan het einde van de zelf studie kunt u een video streamen.  

![De video afspelen](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Node.js](https://nodejs.org/en/download/) installeren
- [Een Azure Media Services-account maken](create-account-cli-how-to.md).<br/>Vergeet niet de waarden die u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account.
- Volg de stappen in [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Toegang tot de Azure Media Services-API met de Azure CLI) en sla de referenties op. U hebt deze nodig voor toegang tot de API.

## <a name="download-and-configure-the-sample"></a>Het voorbeeld downloaden en configureren

Kloon een GitHub-opslag plaats met het streaming node. js-voor beeld naar uw computer met behulp van de volgende opdracht:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Het voor beeld bevindt zich in de map [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) .

Open [index. js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) in het gedownloade project. Vervang de `endpoint config` waarden door referenties die u hebt gekregen van [toegang tot api's](access-api-cli-how-to.md).

In het voorbeeld worden de volgende acties uitgevoerd:

1. Er wordt een **transformatie** gemaakt (eerst wordt gecontroleerd of de opgegeven transformatie bestaat). 
2. Er wordt een uitvoer**asset** gemaakt die wordt gebruikt als uitvoer van de coderings**taak**.
3. De invoer van de **taak** wordt gemaakt en is gebaseerd op een HTTPS-URL.
4. De coderings**taak** wordt met behulp van de eerder gemaakte invoer en uitvoer verzonden.
5. De status van de taak wordt gecontroleerd.
6. Er wordt een **streaming-locator** gemaakt.
7. Er worden streaming-URL's samengesteld.

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

1. De App downloadt gecodeerde bestanden. Maak een map waarin de uitvoer bestanden moeten worden geplaatst en werk de waarde van de variabele **outputFolder** in het bestand [index. js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) bij.
1. Open de **opdracht prompt**, blader naar de map van het voor beeld en voer de volgende opdrachten uit.

    ```
    npm install 
    node index.js
    ```

Nadat deze is uitgevoerd, ziet u vergelijk bare uitvoer:

![Voer](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testen met Azure Media Player

In dit artikel gebruiken we Azure Media Player om de stream te testen. 

> [!NOTE]
> Als een speler wordt gehost op een https-site, moet u de URL bijwerken naar 'https'.

1. Open een browser en ga naar [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Plak in het vak **URL:** een van de streaming URL's die u hebt verkregen door het uitvoeren van de toepassing. 
 
     U kunt de URL plakken in de HLS-, Dash-, of Smooth-indeling. Azure Media Player schakelt over op naar een geschikt streaming-protocol zodat de stream automatisch op uw apparaat wordt afgespeeld.
3. Klik op **Update Player**.

Azure Media Player kan worden gebruikt voor testdoeleinden, maar mag niet worden gebruikt in een productieomgeving. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u een van de resources in de resource groep niet meer nodig hebt, met inbegrip van de Media Services-en opslag accounts die u voor deze zelf studie hebt gemaakt, verwijdert u de resource groep.

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Zie ook

[Taak fout codes](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Media Services concepten](concepts-overview.md)
