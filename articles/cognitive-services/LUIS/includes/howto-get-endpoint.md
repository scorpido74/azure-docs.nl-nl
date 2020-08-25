---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "87374638"
---
Kopieer de URL van de **Voorbeeldquery** in de sectie **Beheren** (menu rechterbovenhoek) op de pagina **Azure-resourcers** (menu links) en plak deze in een nieuw browsertabblad.

De eindpunt-URL ziet er als volgt uit, met uw eigen aangepaste subdomein, app-ID en de eindpuntsleutel, waarbij de APP-ID en de SLEUTEL-ID worden vervangen:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```