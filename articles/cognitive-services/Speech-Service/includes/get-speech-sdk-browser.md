---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399910"
---
:::row:::
    :::column span="3":::
        De JavaScript Speech SDK is beschikbaar als een npm-pakket, zie <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> en het is metgezel GitHub repository <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Hoewel de JavaScript Speech SDK beschikbaar is als een npm-pakket, kunnen zowel clientwebbrowsers als Node.js deze gebruiken - rekening houden met de verschillende architectonische implicaties van elke omgeving. Het <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">documentobjectmodel <span class="docon docon-navigate-external x-hidden-focus"></span> (DOM)</a> is bijvoorbeeld niet beschikbaar voor servertoepassingen, net zoals het <a href="https://nodejs.org/api/fs.html" target="_blank">bestandssysteem <span class="docon docon-navigate-external x-hidden-focus"></span> </a> niet beschikbaar is voor toepassingen aan de clientzijde.

### <a name="nodejs-package-manager-npm"></a>Node.js Package Manager (NPM)

Voer de volgende `npm install` opdracht hieronder uit om de JavaScript Speech SDK te installeren.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML-scripttag

U ook direct `<script>` een tag opnemen `<head>` in het HTMLs-element, waarbij u vertrouwt op het <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM-syndicaat. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Zie de webbrowser <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">SpraakSDK snel aan <span class="docon docon-navigate-external x-hidden-focus"> </span>de slag </a>.
