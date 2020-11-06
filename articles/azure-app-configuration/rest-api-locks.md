---
title: Azure-app configuratie REST API-vergren delingen
description: Naslag pagina's voor het werken met sleutel-waardeparen met behulp van de Azure-app configuratie REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4949db646c54d75f60d29d3c631d0f4ee8d7c26e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424312"
---
# <a name="locks"></a>Vergrendelingen

API-versie: 1,0

Deze API biedt semantiek voor vergren delen/ontgrendelen voor de bron van de sleutel waarde. Het ondersteunt de volgende bewerkingen:

- Vergren deling
- Vergren deling verwijderen

Indien aanwezig, `label` moet een expliciete label waarde zijn ( **geen** Joker teken). Voor alle bewerkingen is het een optionele para meter. Als u dit weglaat, betekent dit dat er geen label is.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Key-Value vergren delen

- **Vereist:** ``{key}`` , ``{api-version}``  
- *Optioneel:*``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Rapporten**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Als de sleutel waarde niet bestaat, wordt het volgende antwoord geretourneerd:

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Key-Value ontgrendelen

- **Vereist:** ``{key}`` , ``{api-version}``  
- *Optioneel:*``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Rapporten**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Als de sleutel waarde niet bestaat, wordt het volgende antwoord geretourneerd:

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lockunlock"></a>Voorwaardelijke vergren deling/ontgrendelen

Gebruik `If-Match` of aanvraag headers om race voorwaarden te voor komen `If-None-Match` . Het `etag` argument maakt deel uit van de sleutel weergave. Als `If-Match` of `If-None-Match` wordt wegge laten, wordt de bewerking onvoorwaardelijk uitgevoerd.

Met de volgende aanvraag wordt de bewerking alleen toegepast als de huidige weer gave van de sleutel waarde overeenkomt met de opgegeven `etag` :

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Met de volgende aanvraag wordt de bewerking alleen toegepast als de huidige weer gave van de sleutel waarde bestaat, maar niet overeenkomt met de opgegeven `etag` :

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
