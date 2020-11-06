---
title: Azure-app configuratie REST API versie beheer
description: Naslag pagina's voor versie beheer met behulp van de Azure-app-configuratie REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 90d131cdc7c496853f2520951c95b9903d69f8fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424064"
---
# <a name="versioning"></a>Versiebeheer

API-versie: 1,0

Elke client aanvraag moet een expliciete API-versie als een query reeks parameter opgeven. Bijvoorbeeld: `https://{myconfig}.azconfig.io/kv?api-version=1.0`

`api-version` wordt uitgedrukt in de SemVer-indeling (primair. secundair). De onderhandeling over het bereik of de versie wordt niet ondersteund.

## <a name="error-response"></a>Fout bericht

Hieronder wordt een overzicht gegeven van de mogelijke fout berichten die door de server zijn geretourneerd, wanneer de aangevraagde API-versie niet kan worden gevonden.

### <a name="api-version-unspecified"></a>De API-versie is niet opgegeven

Treedt op wanneer een client een aanvraag doet zonder een API-versie op te geven.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

### <a name="unsupported-api-version"></a>Niet-ondersteunde API-versie

Treedt op wanneer een door de client aangevraagde API-versie niet overeenkomt met een van de ondersteunde API-versies van de server.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="invalid-api-version"></a>Ongeldige API-versie

Treedt op wanneer een client een aanvraag doet met een API-versie, maar de waarde ongeldig is of niet kan worden geparseerd door de server.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="ambiguous-api-version"></a>Ondubbelzinnige API-versie

Treedt op wanneer een client een API-versie opvraagt die dubbel zinnig is voor de server. Bijvoorbeeld meerdere verschillende waarden.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
