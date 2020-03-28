---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5b1b361778de145a5e32a07bb0164ff2293d9a1a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279688"
---
Kopieer in de sectie **Beheren** (menu rechtsboven) op de pagina **Azure Resources** (linkermenu) de URL **Van voorbeeldquery** en plak vervolgens in een nieuw browsertabblad.

De URL van het eindpunt ziet eruit als de volgende indeling, waarbij uw eigen app-id en eindpuntsleutel APP-ID en KEY-ID vervangen:

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```