---
title: De insluitende lezer starten met HTML-inhoud
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
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946241"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>De insluitende lezer starten met HTML-inhoud

In dit artikel wordt beschreven hoe u de insluitende lezer met HTML-inhoud kunt starten.

## <a name="prepare-the-html-content"></a>De HTML-inhoud voorbereiden

Plaats de inhoud die u wilt weer geven in de insluitende lezer binnen een container element. Zorg ervoor dat het container element een unieke `id`heeft. De insluitende lezer biedt ondersteuning voor eenvoudige HTML-elementen. Zie de [referentie](./reference.md#html-support) voor meer informatie.

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

Gebruik de `id` van het container element om de HTML-inhoud in uw Java script-code op te halen.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>De insluitende lezer starten met uw HTML-inhoud

Wanneer u `ImmersiveReader.launchAsync`aanroept, stelt u de eigenschap `mimeType` van het segment in op `text/html` om het weer geven van HTML in te scha kelen.

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