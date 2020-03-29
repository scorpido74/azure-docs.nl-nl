---
title: Beveiligingstoken genereren om toegang te krijgen tot IoT Plug and Play Preview-repository | Microsoft Documenten
description: Genereer een handtekeningtoken voor gedeelde toegang om te gebruiken wanneer u een Programmatisch IoT Plug and Play Preview-modelrepository inziet.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159197"
---
# <a name="generate-sas-token"></a>SAS-token genereren

Deze handleiding laat u zien hoe u programmatisch een SAS-token (Shared Access Signature) genereren om te gebruiken met de API's van het IoT Plug and Play Preview-model repository.

## <a name="python"></a>Python

In het volgende fragment ziet u hoe u een SAS-token genereren met Python:

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

In het volgende fragment ziet u hoe\#u een SAS-token genereert met C:

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>Het SAS-token gebruiken

Nadat u een SAS-token hebt gegenereerd, u het gebruiken om een HTTP POST-verzoek in te dienen. Bijvoorbeeld:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Als u een client een SAS-token geeft, heeft de client de primaire sleutel van de resource niet en kan hij de hash niet omkeren om deze te verkrijgen. Een SAS-token geeft u controle over waartoe de client toegang heeft en voor hoe lang. Wanneer u de primaire sleutel in het beleid wijzigt, worden alle SAS-tokens die hiermee zijn gemaakt, ongeldig verklaard.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over het genereren van beveiligingstokens om te gebruiken om toegang te krijgen tot het model IoT Plug and Play Preview-modelrepositories, is een voorgestelde volgende stap om meer te weten te komen in de [handleiding voor modelleringsontwikkelaars van IoT Plug and Play Preview.](concepts-developer-guide.md)
