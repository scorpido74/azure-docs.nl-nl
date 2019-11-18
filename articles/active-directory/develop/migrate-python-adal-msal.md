---
title: Migratie handleiding voor ADAL naar MSAL voor python | Azure
description: Meer informatie over hoe u uw python-app voor Azure Active Directory Authentication Library (ADAL) kunt migreren naar de micro soft Authentication Library (MSAL) voor python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: henrikm
editor: twhitney
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f4f19b1a9f6ee77fef2ca2634fb346025f41a6d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152747"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Migratie handleiding voor ADAL naar MSAL voor python

Dit artikel bevat een overzicht van wijzigingen die u moet aanbrengen voor het migreren van een app die gebruikmaakt van de Azure Active Directory Authentication Library (ADAL) om de micro soft Authentication Library (MSAL) te gebruiken.

## <a name="difference-highlights"></a>Verschil hooglichten

ADAL werkt met het eind punt van Azure Active Directory v 1.0. De micro soft Authentication Library (MSAL) werkt met het micro soft Identity-platform, voorheen bekend als het Azure Active Directory v 2.0-eind punt. Het micro soft-identiteits platform wijkt af van Azure Active Directory v 1.0 in dat geval:

Steun
  - Werk-en school accounts (voor Azure AD ingerichte accounts)
  - Persoonlijke accounts (zoals Outlook.com of Hotmail.com)
  - Uw klanten die hun eigen e-mail adres of sociale identiteit (zoals LinkedIn, Facebook, Google) meenemen via de Azure AD B2C aanbieding

- Is standaarden compatibel met:
  - OAuth v 2.0
  - OpenID Connect Connect (OIDC)

Zie [Wat is er anders in het micro soft Identity platform (v 2.0)-eind punt?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison) voor meer informatie.

### <a name="scopes-not-resources"></a>Scopes die geen resources zijn

ADAL python schaft tokens voor bronnen aan, maar MSAL python verkrijgt tokens voor scopes. Het API-Opper vlak in MSAL python heeft geen resource parameter meer. U moet bereiken opgeven als een lijst met teken reeksen die de gewenste machtigingen en benodigde bronnen declareren. Zie [Microsoft Graph scopes](https://docs.microsoft.com/graph/permissions-reference)voor een voor beeld van bereiken.

### <a name="error-handling"></a>Foutafhandeling

Azure Active Directory Authentication Library (ADAL) voor python gebruikt de uitzonderings `AdalError` om aan te geven dat er een probleem is. MSAL voor python gebruikt in plaats daarvan fout codes. Zie [MSAL voor python-fout afhandeling](msal-handling-exceptions.md#msal-for-python-error-handling)voor meer informatie.

### <a name="api-changes"></a>API-wijzigingen

De volgende tabel bevat een overzicht van een API in ADAL voor python en de versie die moet worden gebruikt in MSAL voor python:

| ADAL voor python-API  | MSAL voor python-API |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication of ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N.v.t.  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N.v.t. (Zie de sectie hierboven) |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) en [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) en [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N.v.t. | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [Token cache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N.v.t. | Cache met persistentie, beschikbaar via [MSAL-extensies](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Bestaande vernieuwings tokens migreren voor MSAL python

De micro soft Authentication Library (MSAL) abstracten het concept van vernieuwings tokens. MSAL python biedt standaard een token cache in het geheugen, zodat u geen vernieuwings tokens hoeft op te slaan, op te zoeken of bij te werken. Gebruikers zien ook minder aanmeldings prompts, omdat het vernieuwen van tokens doorgaans kan worden bijgewerkt zonder tussen komst van de gebruiker. Zie [aangepaste token cache serialisatie in MSAL voor python](msal-python-token-cache-serialization.md)voor meer informatie over de token cache.

De volgende code helpt u bij het migreren van uw vernieuwings tokens die worden beheerd door een andere OAuth2-bibliotheek (inclusief maar niet beperkt tot ADAL python) die moeten worden beheerd door MSAL voor python. Een reden voor het migreren van deze vernieuwings tokens is om te voor komen dat bestaande gebruikers zich opnieuw moeten aanmelden wanneer u uw app migreert naar MSAL voor python.

De methode voor het migreren van een vernieuwings token is het gebruik van MSAL voor python om een nieuw toegangs token te verkrijgen met het vorige vernieuwings token. Wanneer het nieuwe vernieuwings token wordt geretourneerd, worden deze in de cache opgeslagen met MSAL voor python. Hier volgt een voor beeld van hoe u het kunt doen:

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

Zie [vergelijking van v 1.0 en v 2.0](active-directory-v2-compare.md)voor meer informatie.
