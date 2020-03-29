---
title: De Insluitende lezer starten met HTML-inhoud
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u de meeslepende lezer starten met HTML-inhoud.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946241"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Hoe de meeslepende lezer te starten met HTML-inhoud

In dit artikel wordt uitgelegd hoe u de Immersive Reader met HTML-inhoud starten.

## <a name="prepare-the-html-content"></a>De HTML-inhoud voorbereiden

Plaats de inhoud die u wilt renderen in de meeslepende lezer in een containerelement. Zorg ervoor dat het containerelement een unieke `id`heeft. De Immersive Reader biedt ondersteuning voor basis-HTML-elementen, zie de [referentie](./reference.md#html-support) voor meer informatie.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>De HTML-inhoud in JavaScript oppakken

Gebruik `id` het containerelement om de HTML-inhoud in uw JavaScript-code op te halen.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Start de meeslepende lezer met uw HTML-inhoud

Stel `ImmersiveReader.launchAsync`bij het aanroepen `mimeType` de `text/html` eigenschap van de chunk in om rendering HTML in te schakelen.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Volgende stappen

* Ontdek de [Meeslepende Reader SDK-referentie](./reference.md)