---
title: Een web-API aanroepen vanuit een daemon-app-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een daemon-app die web-Api's aanroept
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46f1676ab1bdcf2b23907824bb9bf543b5f28ce6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962607"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon-app die web-Api's aanroept-een web-API van de app aanroepen

Een daemon-app kan een web-API aanroepen vanuit een .NET daemon-toepassing of verschillende vooraf goedgekeurde Web-Api's aanroepen.

## <a name="calling-a-web-api-daemon-application"></a>Een web API daemon-toepassing aanroepen

Hier volgt een beschrijving van het gebruik van het token voor het aanroepen van een API:

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

Voor daemon-apps moet u de Web-Api's die u aanroept vooraf moeten worden goedgekeurd. Er is geen incrementele toestemming met daemon-apps (er is geen interactie tussen de gebruiker). De Tenant beheerder moet de toepassing en alle API-machtigingen vooraf toestemming geven. Als u verschillende Api's wilt aanroepen, moet u voor elke resource een token aanschaffen, telkens wanneer u `AcquireTokenForClient`aanroept. MSAL maakt gebruik van de toepassings token cache om onnodige service aanroepen te voor komen.

## <a name="next-steps"></a>Volgende stappen

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon-app-verplaatsen naar productie](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon-app-verplaatsen naar productie](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon-app-verplaatsen naar productie](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
