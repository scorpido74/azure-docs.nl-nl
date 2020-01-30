---
title: Een toegangs token aanvragen-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het aanvragen van een toegangs token van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0694b18bc674b7b682724b8128542b8bc3035405
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846729"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Een toegangs token aanvragen in Azure Active Directory B2C

Een *toegangs token* bevat claims die u in Azure Active Directory B2C (Azure AD B2C) kunt gebruiken om de verleende machtigingen voor uw api's te identificeren. Bij het aanroepen van een bron server moet er een toegangs token aanwezig zijn in de HTTP-aanvraag. Een toegangs token wordt aangeduid als **access_token** in de antwoorden van Azure AD B2C.

In dit artikel wordt beschreven hoe u een toegangs token kunt aanvragen voor een webtoepassing en Web-API. Zie het [overzicht van tokens in azure Active Directory B2C](tokens-overview.md)voor meer informatie over tokens in azure AD B2C.

> [!NOTE]
> **Web-API-ketens (namens-of) worden niet ondersteund door Azure AD B2C.** -Veel architecturen bevatten een web-API die een andere stroomafwaartse Web-API moet aanroepen, zowel beveiligd door Azure AD B2C. Dit scenario is gebruikelijk in clients met een web API-back-end, die op zijn beurt een andere service aanroept. Deze geketende Web API-scenario kan worden ondersteund met behulp van de OAuth 2,0 JWT Bearer-referentie toekenning, ook wel bekend als de namens-stroom. De namens-stroom is momenteel echter niet geïmplementeerd in Azure AD B2C.

## <a name="prerequisites"></a>Vereisten

- [Maak een gebruikers stroom](tutorial-create-user-flows.md) zodat gebruikers zich kunnen registreren en zich kunnen aanmelden bij uw toepassing.
- Als u dit nog niet hebt gedaan, [voegt u een web-API-toepassing toe aan uw Azure Active Directory B2C-Tenant](add-web-application.md).

## <a name="scopes"></a>Bereiken

Bereiken bieden een manier om machtigingen voor beveiligde bronnen te beheren. Wanneer een toegangs token wordt aangevraagd, moet de client toepassing de gewenste machtigingen opgeven in de **bereik** parameter van de aanvraag. Als u bijvoorbeeld de **bereik waarde** van `read` voor de API met de **App-ID-URI** `https://contoso.onmicrosoft.com/api`wilt opgeven, zou het bereik `https://contoso.onmicrosoft.com/api/read`zijn.

Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Bijvoorbeeld: gebruikers van de web-API kunnen zowel lees- als schrijftoegang hebben of alleen leestoegang. Als u meerdere machtigingen in dezelfde aanvraag wilt ophalen, kunt u meerdere vermeldingen toevoegen in de para meter single **Scope** van de aanvraag, gescheiden door spaties.

In het volgende voor beeld ziet u de scopes die in een URL zijn gedecodeerd:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

In het volgende voor beeld ziet u scopes die zijn gecodeerd in een URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Als u meer bereiken aanvraagt dan wat voor uw client toepassing wordt verleend, wordt de aanroep uitgevoerd als er ten minste één machtiging is verleend. De **SCP** -claim in het resulterende toegangs token is gevuld met alleen de machtigingen die zijn verleend. De OpenID Connect Connect Standard specificeert verschillende speciale bereik waarden. De volgende bereiken vertegenwoordigen de machtiging voor toegang tot het profiel van de gebruiker:

- **OpenID Connect** : vraagt een id-token in.
- **offline_access** : vraagt een vernieuwings token aan met behulp van [auth-code stromen](authorization-code-flow.md).

Als de **response_type** -para meter in een `/authorize` aanvraag `token`bevat, moet de **bereik** parameter ten minste één resource bereik bevatten, behalve `openid` en `offline_access` dat wordt verleend. Anders mislukt de `/authorize` aanvraag.

## <a name="request-a-token"></a>Een token aanvragen

Als u een toegangs token wilt aanvragen, hebt u een autorisatie code nodig. Hieronder ziet u een voor beeld van een aanvraag voor het `/authorize`-eind punt voor een autorisatie code. Aangepaste domeinen worden niet ondersteund voor gebruik met toegangs tokens. Gebruik uw tenant-name.onmicrosoft.com-domein in de aanvraag-URL.

In het volgende voor beeld vervangt u deze waarden:

- `<tenant-name>`: de naam van uw Azure AD B2C Tenant.
- `<policy-name>`: de naam van het aangepaste beleid of de gebruikers stroom.
- `<application-ID>`: de toepassings-id van de webtoepassing die u hebt geregistreerd ter ondersteuning van de gebruikers stroom.
- `<redirect-uri>`-de **omleidings-URI** die u hebt ingevoerd bij het registreren van de client toepassing.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Het antwoord met de autorisatie code moet vergelijkbaar zijn met dit voor beeld:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Nadat u de autorisatie code hebt ontvangen, kunt u deze gebruiken om een toegangs token aan te vragen:

```HTTP
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Er wordt een bericht weer gegeven dat vergelijkbaar is met het volgende antwoord:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Wanneer u https://jwt.ms gebruikt om het toegangs token te onderzoeken dat is geretourneerd, ziet u iets dat lijkt op het volgende voor beeld:

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van tokens in azure AD B2C](configure-tokens.md)
