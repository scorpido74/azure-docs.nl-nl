---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 39bfa5b6800c65112850faa8842b915ceedb9312
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648277"
---
Kopieer in het gedeelte **beheren** (rechtsboven) op de pagina **Azure-resources** (menu links) de **voorbeeld query** -URL en plak deze in een nieuw browser tabblad. 

De eind punt-URL ziet eruit als in de volgende notatie, met uw eigen App-ID en de sleutel van het eind punt, waarbij de APP-ID en de sleutel-ID worden vervangen:

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```