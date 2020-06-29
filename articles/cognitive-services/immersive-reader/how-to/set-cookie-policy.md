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
ms.openlocfilehash: ae4812a69a1e6833224dc4dedf36566167ae4796
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483411"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Het cookie beleid voor de insluitende lezer instellen

De insluitende lezer schakelt standaard het cookie gebruik uit. Als u cookie gebruik inschakelt, kan de insluitende lezer cookies gebruiken om gebruikers voorkeuren bij te houden en het gebruik van de functie te volgen. Als u het gebruik van cookies in de insluitende lezer inschakelt, moet u rekening houden met de vereisten van het nalevings beleid van de EU. Het is de verantwoordelijkheid van de hosttoepassing om elke vereiste toestemming van de gebruiker te verkrijgen in overeenstemming met het nalevings beleid van de EU-cookie.

Het cookie beleid kan worden ingesteld met behulp van de [Opties](../reference.md#options)voor insluitende lezer.

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

* Bekijk de [Node.js Snelstartgids](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) om te zien wat u nog meer kunt doen met de insluitende lezer-SDK met behulp van Node.js
* Bekijk de [python-zelf studie](../tutorial-python.md) om te zien wat u nog meer kunt doen met de insluitende Reader SDK met behulp van python
* Bekijk de [SWIFT-zelf studie](../tutorial-ios-picture-immersive-reader.md) om te zien wat u nog meer met de insluitende lezer-SDK kunt doen met behulp van SWIFT
* Verken de [insluitende lezer SDK](https://github.com/microsoft/immersive-reader-sdk) en de referentie voor de [insluitende lezer SDK](../reference.md)