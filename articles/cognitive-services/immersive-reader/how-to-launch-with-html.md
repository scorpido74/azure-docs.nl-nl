---
title: De Insluitende lezer starten met HTML-inhoud
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de insluitende lezer met HTML-inhoud kunt starten.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946241"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>De insluitende lezer starten met HTML-inhoud

In dit artikel wordt beschreven hoe u de insluitende lezer met HTML-inhoud kunt starten.

## <a name="prepare-the-html-content"></a>De HTML-inhoud voorbereiden

Plaats de inhoud die u wilt weer geven in de insluitende lezer binnen een container element. Zorg ervoor dat het container element een unieke `id`naam heeft. De insluitende lezer biedt ondersteuning voor eenvoudige HTML-elementen. Zie de [referentie](./reference.md#html-support) voor meer informatie.

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

## <a name="get-the-html-content-in-javascript"></a>De HTML-inhoud in Java script ophalen

Gebruik het `id` element van de container om de HTML-inhoud in uw Java script-code op te halen.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>De insluitende lezer starten met uw HTML-inhoud

Wanneer u `ImmersiveReader.launchAsync`aanroept, stelt u `mimeType` de eigenschap `text/html` van het segment in op om rendering van HTML in te scha kelen.

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

* De referentie voor de [insluitende lezer-SDK](./reference.md) verkennen