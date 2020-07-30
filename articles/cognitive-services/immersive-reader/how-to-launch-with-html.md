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
ms.custom: devx-track-javascript
ms.openlocfilehash: fbe54b3bd6e0fc8c42b4dfc7401be74ae65ce6cd
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406925"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>De insluitende lezer starten met HTML-inhoud

In dit artikel wordt beschreven hoe u de insluitende lezer met HTML-inhoud kunt starten.

## <a name="prepare-the-html-content"></a>De HTML-inhoud voorbereiden

Plaats de inhoud die u wilt weer geven in de insluitende lezer binnen een container element. Zorg ervoor dat het container element een unieke naam heeft `id` . De insluitende lezer biedt ondersteuning voor eenvoudige HTML-elementen. Zie de [referentie](./reference.md#html-support) voor meer informatie.

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

Wanneer u aanroept `ImmersiveReader.launchAsync` , stelt u de eigenschap van het segment `mimeType` in op `text/html` om rendering van HTML in te scha kelen.

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