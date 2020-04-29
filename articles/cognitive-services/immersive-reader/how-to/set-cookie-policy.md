---
title: Beleid voor insluitend lezers cookie instellen
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u het cookie beleid voor de insluitende lezer kunt instellen.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946109"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Het cookie beleid voor de insluitende lezer instellen

De insluitende lezer schakelt standaard het cookie gebruik uit. Als u cookie gebruik inschakelt, kan de insluitende lezer cookies gebruiken om gebruikers voorkeuren bij te houden en het gebruik van de functie te volgen. Als u het gebruik van cookies in de insluitende lezer inschakelt, moet u rekening houden met de vereisten van het nalevings beleid van de EU. Het is de verantwoordelijkheid van de hosttoepassing om elke vereiste toestemming van de gebruiker te verkrijgen in overeenstemming met het nalevings beleid van de EU-cookie.

Het cookie beleid kan worden ingesteld met behulp van de [Opties](../reference.md#options)voor insluitende lezer. Zie [CookiePolicy Enum](../reference.md#cookiepolicy-enum) voor meer informatie.

## <a name="enable-cookie-usage"></a>Cookie gebruik inschakelen

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Cookie gebruik uitschakelen

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [Snelstartgids voor node. js](../quickstart-nodejs.md) om te zien wat u nog meer kunt doen met de insluitende lezer-SDK met behulp van node. js
* Bekijk de [python-zelf studie](../tutorial-python.md) om te zien wat u nog meer kunt doen met de insluitende Reader SDK met behulp van python
* Bekijk de [SWIFT-zelf studie](../tutorial-ios-picture-immersive-reader.md) om te zien wat u nog meer met de insluitende lezer-SDK kunt doen met behulp van SWIFT
* Verken de [insluitende lezer SDK](https://github.com/microsoft/immersive-reader-sdk) en de referentie voor de [insluitende lezer SDK](../reference.md)