---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: a6e6c89c34723fb9e11b0c7e4ab8c9bedb8aa9ca
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959017"
---
Kopieer de URL van de **Voorbeeldquery** in de sectie **Beheren** (menu rechterbovenhoek) op de pagina **Azure-resourcers** (menu links) en plak deze in een nieuw browsertabblad.

De eindpunt-URL ziet er als volgt uit, met uw eigen aangepaste subdomein, app-ID en de eindpuntsleutel, waarbij de APP-ID en de SLEUTEL-ID worden vervangen:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```