---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399910"
---
:::row:::
    :::column span="3":::
        De Java script Speech SDK is beschikbaar als een NPM-pakket, Zie <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">micro soft-cognitiveservices- <span class="docon docon-navigate-external x-hidden-focus"></span> Speech-SDK</a> en de Companion github repository <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitieve-Services-Speech-SDK <span class="docon docon-navigate-external x-hidden-focus"> </span>-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Hoewel de Java script Speech SDK beschikbaar is als een NPM-pakket, kunnen webbrowsers van de client en node. js het gebruiken. Houd rekening met de verschillende architectuur implicaties van elke omgeving. Zo is het <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Document object model (DOM) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> niet beschikbaar voor server toepassingen, net zoals het <a href="https://nodejs.org/api/fs.html" target="_blank">Bestands systeem <span class="docon docon-navigate-external x-hidden-focus"></span> </a> niet beschikbaar is voor toepassingen op de client.

### <a name="nodejs-package-manager-npm"></a>Node. js-pakket beheer (NPM)

Voer de volgende `npm install` opdracht uit om de Java script Speech SDK te installeren.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML-script code

U kunt ook rechtstreeks een `<script>` tag in het HTML- `<head>` element toevoegen, afhankelijk van de <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM Syndicate <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Zie de <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">Quick <span class="docon docon-navigate-external x-hidden-focus"> </span>start voor de webbrowser Speech SDK </a>voor meer informatie.
