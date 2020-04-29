---
title: De knop insluitende lezer aanpassen
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de knop kunt aanpassen waarmee de insluitende lezer wordt gestart.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946208"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>De knop voor de insluitende lezer aanpassen

In dit artikel wordt beschreven hoe u de knop voor het starten van de insluitende lezer kunt aanpassen, zodat deze voldoet aan de behoeften van uw toepassing.

## <a name="add-the-immersive-reader-button"></a>De knop insluitende lezer toevoegen

De insluitende lezer-SDK biedt standaard stijlen voor de knop waarmee de insluitende lezer wordt gestart. Gebruik het `immersive-reader-button` kenmerk Class om deze stijl in te scha kelen.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>De knop stijl aanpassen

Gebruik het `data-button-style` kenmerk om de stijl van de knop in te stellen. De toegestane waarden zijn `icon`, `text`en `iconAndText`. De standaardwaarde is `icon`.

### <a name="icon-button"></a>Pictogram knop

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Hiermee wordt het volgende weer gegeven:

![Pictogram knop](./media/button-icon.png)

### <a name="text-button"></a>Knop tekst

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Hiermee wordt het volgende weer gegeven:

![Pictogram knop](./media/button-text.png)

### <a name="icon-and-text-button"></a>Knop pictogram en tekst

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Hiermee wordt het volgende weer gegeven:

![Pictogram knop](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>De knop tekst aanpassen

Configureer de taal en de ALT-tekst voor de knop met `data-locale` behulp van het-kenmerk. De standaardtaal is Engels.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>De grootte van het pictogram aanpassen

De grootte van het pictogram van de insluitende lezer kan worden `data-icon-px-size` geconfigureerd met behulp van het-kenmerk. Hiermee stelt u de grootte van het pictogram in pixels. De standaard grootte is 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Volgende stappen

* De referentie voor de [insluitende lezer-SDK](./reference.md) verkennen