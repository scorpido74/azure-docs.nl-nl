---
title: Daemon-app die web-Api's aanroept (Web-Api's aanroepen)-micro soft Identity-platform
description: Meer informatie over het bouwen van een daemon-app die web-Api's aanroept (Web-Api's aanroepen)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eacb574f20abeb63a9d0ab8caf534eb7abb9784
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056348"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon-app die web-Api's aanroept-een web-API van de app aanroepen

Een daemon-app kan een web-API aanroepen vanuit een .NET daemon-toepassing of verschillende vooraf goedgekeurde Web-Api's aanroepen.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Een web-API aanroepen vanuit een .NET-daemon-toepassing

Hier volgt een beschrijving van het gebruik van het token voor het aanroepen van een API

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API" 
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>Meerdere Api's aanroepen

Voor daemon-apps moet u de Web-Api's die u aanroept vooraf moeten worden goedgekeurd. Er is geen incrementele toestemming met daemon-apps (er is geen interactie tussen de gebruiker). De Tenant beheerder moet de toepassing en alle API-machtigingen vooraf toestemming geven. Als u verschillende Api's wilt aanroepen, moet u voor elke resource een token aanschaffen, telkens wanneer u de oproep `AcquireTokenForClient`uitvoert. MSAL maakt gebruik van de toepassings token cache om onnodige service aanroepen te voor komen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Daemon-app-verplaatsen naar productie](./scenario-daemon-production.md)