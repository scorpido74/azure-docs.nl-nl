---
title: De knop Meeslepende lezer aanpassen
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u de knop aanpassen die de meeslepende lezer lanceert.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946208"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>De knop Meeslepende lezer aanpassen

In dit artikel wordt uitgelegd hoe u de knop aanpassen die de Immersive Reader start, zodat deze aan de behoeften van uw toepassing voldoet.

## <a name="add-the-immersive-reader-button"></a>De knop Meeslepende lezer toevoegen

De Immersive Reader SDK biedt standaard styling voor de knop die de Immersive Reader lanceert. Gebruik `immersive-reader-button` het kenmerk klasse om deze styling in te schakelen.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>De knopstijl aanpassen

Gebruik `data-button-style` het kenmerk om de stijl van de knop in te stellen. De toegestane waarden `icon` `text`zijn `iconAndText`, en . De standaardwaarde is `icon`.

### <a name="icon-button"></a>Pictogramknop

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Dit geeft het volgende weer:

![Pictogramknop](./media/button-icon.png)

### <a name="text-button"></a>Knop Tekst

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Dit geeft het volgende weer:

![Pictogramknop](./media/button-text.png)

### <a name="icon-and-text-button"></a>Pictogram en tekstknop

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Dit geeft het volgende weer:

![Pictogramknop](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>De knoptekst aanpassen

Configureer de taal en de alternatieve `data-locale` tekst voor de knop met het kenmerk. De standaardtaal is Engels.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>De grootte van het pictogram aanpassen

De grootte van het pictogram Immersive Reader `data-icon-px-size` kan worden geconfigureerd met behulp van het kenmerk. Hiermee wordt de grootte van het pictogram in pixels ingesteld. De standaardgrootte is 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Volgende stappen

* Ontdek de [Meeslepende Reader SDK-referentie](./reference.md)