---
title: Een web-API aanroepen vanuit een daemon-app - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het bouwen van een daemon-app die web-API's aanroept
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e440628526dada7655cc71f63fc9fff006cc5ef5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885443"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon-app die web-API's aanroept - een web-API oproepen vanuit de app

.NET daemon-apps kunnen een web-API aanroepen. .NET daemon apps kunnen ook verschillende vooraf goedgekeurde web API's aanroepen.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Een web-API aanroepen vanuit een daemon-toepassing

U het token als u het token gebruiken om een API aan te roepen:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

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

## <a name="calling-several-apis"></a>Meerdere API's bellen

Voor daemon-apps moeten de web-API's die u aanroept vooraf zijn goedgekeurd. Er is geen incrementele toestemming met daemon apps. (Er is geen interactie met de gebruiker.) De tenantbeheerder moet vooraf toestemming geven voor de toepassing en alle API-machtigingen. Als u meerdere API's wilt aanroepen, moet u voor `AcquireTokenForClient`elke resource een token aanschaffen, telkens wanneer u aanroept. MSAL gebruikt de cache van het toepassingstoken om onnodige serviceoproepen te voorkomen.

## <a name="next-steps"></a>Volgende stappen

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon app - overgaan naar productie](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon app - overgaan naar productie](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon app - overgaan naar productie](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
