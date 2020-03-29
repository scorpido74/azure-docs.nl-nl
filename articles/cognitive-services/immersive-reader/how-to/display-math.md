---
title: Wiskundige weergave in de meeslepende lezer
titleSuffix: Azure Cognitive Services
description: In dit artikel zie je hoe je wiskunde weergeven in de meeslepende lezer.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946120"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Hoe wiskunde weer te geven in de meeslepende lezer

De meeslepende lezer kan wiskunde weergeven wanneer deze wordt geleverd in de vorm van wiskundige opmaaktaal[(MathML).](https://developer.mozilla.org/docs/Web/MathML)
Het MIME-type kan worden ingesteld via de immersive reader [chunk](../reference.md#chunk). Zie [ondersteunde MIME-typen](../reference.md#supported-mime-types) voor meer informatie.

## <a name="send-math-to-the-immersive-reader"></a>Stuur wiskunde naar de meeslepende lezer
Als u wiskunde naar de meeslepende lezer wilt verzenden, levert u een ```application/mathml+xml```stuk met MathML en stelt u het MIME-type in op ;

Als uw inhoud bijvoorbeeld de volgende gegevens zijn:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Vervolgens u uw inhoud weergeven met behulp van de volgende JavaScript.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

Wanneer u de immersive reader start, moet u zien:

![Wiskunde in meeslepende lezer](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Volgende stappen

* Ontdek de [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) en de [Immersive Reader SDK Reference](../reference.md)