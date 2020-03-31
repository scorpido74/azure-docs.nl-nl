---
title: Python ADAL naar MSAL-migratiegids | Azure
description: Meer informatie over het migreren van uw Azure Active Directory Authentication Library (ADAL) Python-app naar de Microsoft Authentication Library (MSAL) voor Python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696553"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>ADAL naar MSAL-migratiegids voor Python

In dit artikel worden wijzigingen beschreven die u moet aanbrengen om een app te migreren die de Azure Active Directory Authentication Library (ADAL) gebruikt om de MsAL (Microsoft Authentication Library) te gebruiken.

## <a name="difference-highlights"></a>Verschil hoogtepunten

ADAL werkt met het Azure Active Directory (Azure AD) v1.0-eindpunt. De Microsoft Authentication Library (MSAL) werkt met het Microsoft-identiteitsplatform, voorheen bekend als het Azure Active Directory v2.0-eindpunt. Het Microsoft-identiteitsplatform verschilt van Azure AD v1.0 in die zin dat het:

Ondersteunt:
  - Werk- en schoolaccounts (azure AD-ingerichte accounts)
  - Persoonlijke accounts (zoals Outlook.com of Hotmail.com)
  - Uw klanten die hun eigen e-mail of sociale identiteit (zoals LinkedIn, Facebook, Google) meenemen via het Azure AD B2C-aanbod

- Is normen compatibel met:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Zie [Wat is er anders aan het Microsoft-identiteitsplatform (v2.0) eindpunt?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

### <a name="scopes-not-resources"></a>Scopes niet resources

ADAL Python verwerft tokens voor resources, maar MSAL Python verwerft tokens voor scopes. Het API-oppervlak in MSAL Python heeft geen resourceparameter meer. U moet scopes bieden als een lijst met tekenreeksen die de gewenste machtigingen en resources declareren die worden aangevraagd. Zie de scopes van Microsoft Graph voor een voorbeeld van [scopes.](https://docs.microsoft.com/graph/permissions-reference)

### <a name="error-handling"></a>Foutafhandeling

Azure Active Directory Authentication Library (ADAL) `AdalError` voor Python gebruikt de uitzondering om aan te geven dat er een probleem is. MSAL voor Python gebruikt meestal foutcodes. Zie [MSAL voor foutafhandeling van Python voor](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python)meer informatie .

### <a name="api-changes"></a>API-wijzigingen

In de volgende tabel wordt een API in ADAL voor Python weergegeven en die in de volgende tabel die in MSAL voor Python moet worden gebruikt:

| ADAL voor Python API  | MSAL voor Python API |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication of ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N.v.t.  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N.v.t. |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) en [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) en [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N.v.t. | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N.v.t. | Cache met doorzettingsvermogen, verkrijgbaar bij [MSAL-extensies](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Bestaande vernieuwingstokens voor MSAL Python migreren

De Microsoft-verificatiebibliotheek (MSAL) abstraheert het concept van vernieuwingstokens. MSAL Python biedt standaard een tokencache in het geheugen, zodat u tokens voor vernieuwen niet hoeft op te slaan, op te zoeken of bij te werken. Gebruikers zien ook minder aanmeldingsprompts omdat vernieuwingstokens meestal kunnen worden bijgewerkt zonder tussenkomst van de gebruiker. Zie [Seriële serialisatie van de tokencache aanpassen in MSAL voor Python voor](msal-python-token-cache-serialization.md)meer informatie over de tokencache.

Met de volgende code u uw vernieuwingstokens migreren die worden beheerd door een andere OAuth2-bibliotheek (inclusief maar niet beperkt tot ADAL Python) om door MSAL voor Python te worden beheerd. Een van de redenen voor het migreren van deze vernieuwingstokens is om te voorkomen dat bestaande gebruikers zich opnieuw moeten aanmelden wanneer u uw app migreert naar MSAL voor Python.

De methode voor het migreren van een vernieuwingstoken is om MSAL voor Python te gebruiken om een nieuw toegangstoken te verkrijgen met behulp van het vorige vernieuwingstoken. Wanneer het nieuwe vernieuwingstoken wordt geretourneerd, slaat MSAL voor Python het op in de cache. Hier is een voorbeeld van hoe dit te doen:

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie [v1.0 en v2.0-vergelijking](active-directory-v2-compare.md).
