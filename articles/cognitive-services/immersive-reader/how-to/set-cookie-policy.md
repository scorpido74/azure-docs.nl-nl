---
title: Meeslepend cookiebeleid voor lezers instellen
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u het cookiebeleid voor de meeslepende lezer instelt.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946109"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Het cookiebeleid instellen voor de meeslepende lezer

De Immersive Reader schakelt het gebruik van cookies standaard uit. Als u cookiegebruik inschakelt, kan de Immersive Reader cookies gebruiken om gebruikersvoorkeuren bij te houden en het gebruik van functies bij te houden. Als u cookiegebruik inschakelt in de meeslepende lezer, moet u rekening houden met de vereisten van het NALEVINGsbeleid van DE EU-cookie. Het is de verantwoordelijkheid van de hostapplicatie om de benodigde toestemming van de gebruiker te verkrijgen in overeenstemming met het EU-cookiecompliancebeleid.

Het cookiebeleid kan worden ingesteld via de [opties](../reference.md#options)Voor meeslepende lezers. Zie [CookiePolicy enum](../reference.md#cookiepolicy-enum) voor meer informatie.

## <a name="enable-cookie-usage"></a>Cookiegebruik inschakelen

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Cookiegebruik uitschakelen

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [Node.js snel start](../quickstart-nodejs.md) om te zien wat je nog meer doen met de Immersive Reader SDK met behulp van Node.js
* Bekijk de [Python-zelfstudie](../tutorial-python.md) om te zien wat u nog meer doen met de Immersive Reader SDK met Python
* Bekijk de [Swift-zelfstudie](../tutorial-ios-picture-immersive-reader.md) om te zien wat je nog meer doen met de Immersive Reader SDK met Swift
* Ontdek de [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) en de [Immersive Reader SDK Reference](../reference.md)