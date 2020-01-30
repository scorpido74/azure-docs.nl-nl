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
ms.openlocfilehash: 338b638d6b33bcbbb5cf377643a96c71b0d314bd
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775194"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon-app die web-Api's aanroept-een web-API van de app aanroepen

.NET daemon-apps kunnen een web-API aanroepen. .NET daemon-apps kunnen ook verschillende vooraf goedgekeurde Web-Api's aanroepen.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Een web-API aanroepen vanuit een daemon-toepassing

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

Voor daemon-apps moet u de Web-Api's die u aanroept vooraf moeten worden goedgekeurd. Er is geen incrementele toestemming met daemon-apps. (Er is geen tussen komst van de gebruiker.) De Tenant beheerder moet vooraf toestemming geven voor de toepassing en alle API-machtigingen. Als u verschillende Api's wilt aanroepen, moet u voor elke resource een token verkrijgen, telkens wanneer u `AcquireTokenForClient`aanroept. MSAL maakt gebruik van de toepassings token cache om onnodige service aanroepen te voor komen.

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
