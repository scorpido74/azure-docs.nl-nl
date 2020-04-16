---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 6dd4dfd0edd334005cc2af51a46f9ca2e634272f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399898"
---
:::row:::
    :::column span="3":::
        De JavaScript Speech SDK is beschikbaar als een npm-pakket, zie <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> en het is metgezel GitHub repository <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Hoewel de JavaScript Speech SDK beschikbaar is als een npm-pakket, kunnen zowel Node.js als clientwebbrowsers het gebruiken - houd rekening met de verschillende architectonische implicaties van elke omgeving. Het <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">documentobjectmodel <span class="docon docon-navigate-external x-hidden-focus"></span> (DOM)</a> is bijvoorbeeld niet beschikbaar voor servertoepassingen, net zoals het <a href="https://nodejs.org/api/fs.html" target="_blank">bestandssysteem <span class="docon docon-navigate-external x-hidden-focus"></span> </a> niet beschikbaar is voor toepassingen aan de clientzijde.

### <a name="nodejs-package-manager-npm"></a>Node.js Package Manager (NPM)

Voer de volgende `npm install` opdracht hieronder uit om de JavaScript Speech SDK te installeren.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Zie de <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Node.js Speech SDK snel <span class="docon docon-navigate-external x-hidden-focus"> </span>aan de slag </a>voor meer informatie.
