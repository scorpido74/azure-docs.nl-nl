---
title: Verbinding maken met Azure Media Services v3 API - Node.js
description: In dit artikel wordt uitgelegd hoe u verbinding maken met Media Services v3 API met Node.js.
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
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896101"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Verbinding maken met Media Services v3 API - Node.js

In dit artikel ziet u hoe u verbinding maken met de Azure Media Services v3-node.js SDK met behulp van de aanmeldingsmethode voor serviceprincipal.

## <a name="prerequisites"></a>Vereisten

- Installeer [Node.js](https://nodejs.org/en/download/).
- [Een Azure Media Services-account maken](create-account-cli-how-to.md). Zorg ervoor dat u de naam van de brongroep en de naam van het Media Services-account onthoudt.

> [!IMPORTANT]
> [Naamgevingsconventies bekijken](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Package.json maken

1. Maak een package.json-bestand met uw favoriete editor.
1. Open het bestand en plak de volgende code:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

De volgende verpakkingen moeten worden gespecificeerd:

|Pakket|Beschrijving|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Als u ervoor wilt zorgen dat u het nieuwste Azure Media Services-pakket gebruikt, schakelt u [NPM installeert azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Opslag SDK. Wordt gebruikt bij het uploaden van bestanden naar assets.|
|`ms-rest-azure`| Altijd inloggen.|

U de volgende opdracht uitvoeren om ervoor te zorgen dat u het nieuwste pakket gebruikt:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Verbinding maken met de client Node.js

1. Maak een .js-bestand met uw favoriete editor.
1. Open het bestand en plak de volgende code.
1. Stel de waarden in de sectie 'eindpuntconfig' in op waarden die u hebt gekregen van [toegangs-API's.](access-api-cli-how-to.md)

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Uw app uitvoeren

Open een opdrachtprompt. Blader naar de map van het voorbeeld en voer de volgende opdrachten uit:

```
npm install 
node index.js
```

## <a name="see-also"></a>Zie ook

- [Concepten van Media Services](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Node.js-naslagdocumentatie](/javascript/api/overview/azure/mediaservices/management) van Media Services en bekijk [voorbeelden](https://github.com/Azure-Samples/media-services-v3-node-tutorials) waarin u ziet hoe u Media Services-API gebruikt met Node.js.

