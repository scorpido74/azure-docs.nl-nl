---
title: Tokens voor servicetoegang ophalen
description: Beschrijft hoe u tokens maakt voor toegang tot de ARR REST API's
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: fd510f90887353d7486908ee076d5308db72c59d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687068"
---
# <a name="get-service-access-tokens"></a>Tokens voor servicetoegang ophalen

Toegang tot de ARR REST API's wordt alleen verleend aan geautoriseerde gebruikers. Om uw autorisatie te bewijzen, moet u een *toegangstoken* samen met REST-aanvragen verzenden. Deze tokens worden uitgegeven door de *Secure Token Service* (STS) in ruil voor een accountsleutel. Tokens hebben een **levensduur van 24 uur** en kunnen dus worden uitgegeven aan gebruikers zonder ze volledige toegang tot de service te geven.

In dit artikel wordt beschreven hoe u een dergelijk toegangstoken maakt.

## <a name="prerequisites"></a>Vereisten

[Maak een ARR-account](create-an-account.md)aan als je er nog geen hebt.

## <a name="token-service-rest-api"></a>Tokenservice REST API

Om toegangstokens te maken, biedt de *Secure Token Service* één REST-API. De URL voor de ARR STS-service is https:\//sts.mixedreality.azure.com.

### <a name="get-token-request"></a>'Token-aanvraag krijgen'

| URI | Methode |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| Header | Waarde |
|--------|:------|
| Autorisatie | **"AccountId aan**toonder :**accountKey**" |

Vervang *accountId* en *accountKey* door uw respectievelijke gegevens.

### <a name="get-token-response"></a>'Token-reactie ontvangen'

| Statuscode | JSON-payload | Opmerkingen |
|-----------|:-----------|:-----------|
| 200 | AccessToken: tekenreeks | Geslaagd |

| Header | Doel |
|--------|:------|
| MS-CV | Deze waarde kan worden gebruikt om het gesprek binnen de service te traceren |

## <a name="getting-a-token-using-powershell"></a>Een token krijgen met PowerShell

De PowerShell-code hieronder laat zien hoe u het benodigde REST-verzoek naar de STS verzenden. Vervolgens wordt het token afgedrukt op de PowerShell-prompt.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Het script drukt het token gewoon af op de uitvoer, van waaruit u het kopiëren & plakken. Voor een echt project moet u dit proces automatiseren.

## <a name="next-steps"></a>Volgende stappen

* [PowerShell-voorbeeldscripts](../samples/powershell-example-scripts.md)
* [Azure Frontend-API's](../how-tos/frontend-apis.md)
* [API voor sessiebeheer REST](../how-tos/session-rest-api.md)
