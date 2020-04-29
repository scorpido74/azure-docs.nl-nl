---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 6dd4dfd0edd334005cc2af51a46f9ca2e634272f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399898"
---
:::row:::
    :::column span="3":::
        De Java script Speech SDK is beschikbaar als een NPM-pakket, Zie <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">micro soft-cognitiveservices- <span class="docon docon-navigate-external x-hidden-focus"></span> Speech-SDK</a> en de Companion github repository <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitieve-Services-Speech-SDK <span class="docon docon-navigate-external x-hidden-focus"> </span>-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Hoewel de Java script Speech SDK beschikbaar is als een NPM-pakket, kunnen zowel node. js als webbrowsers van de client dit gebruiken. Houd rekening met de verschillende architectuur implicaties van elke omgeving. Zo is het <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Document object model (DOM) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> niet beschikbaar voor server toepassingen, net zoals het <a href="https://nodejs.org/api/fs.html" target="_blank">Bestands systeem <span class="docon docon-navigate-external x-hidden-focus"></span> </a> niet beschikbaar is voor toepassingen op de client.

### <a name="nodejs-package-manager-npm"></a>Node. js-pakket beheer (NPM)

Voer de volgende `npm install` opdracht uit om de Java script Speech SDK te installeren.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Zie de <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Snelstartgids <span class="docon docon-navigate-external x-hidden-focus"> </span>voor node. js Speech SDK </a>voor meer informatie.
