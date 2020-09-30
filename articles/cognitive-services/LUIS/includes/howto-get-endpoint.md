---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545149"
---
Kopieer de URL van de **Voorbeeldquery** in de sectie **Beheren** (menu rechterbovenhoek) op de pagina **Azure-resourcers** (menu links) en plak deze in een nieuw browsertabblad.

De eindpunt-URL ziet er als volgt uit, met uw eigen aangepaste subdomein, app-ID en de eindpuntsleutel, waarbij de APP-ID en de SLEUTEL-ID worden vervangen:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```