---
title: Beveiligings token genereren voor toegang tot IoT Plug en Play preview-opslag plaats | Microsoft Docs
description: Genereer een token voor een Shared Access-hand tekening dat moet worden gebruikt wanneer u via een programma toegang hebt tot een IoT Plug en Play preview model-opslag.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159197"
---
# <a name="generate-sas-token"></a>SAS-token genereren

In deze hand leiding wordt uitgelegd hoe u programmatisch een SAS-token (Shared Access Signature) kunt genereren voor gebruik met de IoT-Plug en Play preview-model opslagplaats-Api's.

## <a name="python"></a>Python

Het volgende code fragment laat zien hoe u een SAS-token kunt genereren met behulp van python:

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

Het volgende code fragment laat zien hoe u een SAS-token kunt genereren met C \# :

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

Nadat u een SAS-token hebt gegenereerd, kunt u dit gebruiken om een HTTP POST-aanvraag te maken. Bijvoorbeeld:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Als u een SAS-token aan een client toewijst, heeft de client niet de primaire sleutel van de resource en kan de hash niet worden teruggedraaid om deze te verkrijgen. Met een SAS-token kunt u bepalen wat de client toegang heeft en hoe lang. Wanneer u de primaire sleutel in het beleid wijzigt, worden alle SAS-tokens die zijn gemaakt op basis van deze, ongeldig.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over het genereren van beveiligings tokens die u kunt gebruiken om toegang te krijgen tot het model IoT Plug en Play preview model-opslag plaatsen, is een voorgestelde volgende stap meer informatie in de [ontwikkel gids voor IoT Plug en Play preview Modeler](concepts-developer-guide.md).
