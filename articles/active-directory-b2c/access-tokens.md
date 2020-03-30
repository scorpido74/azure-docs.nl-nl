---
title: Een toegangstoken aanvragen - Azure Active Directory B2C | Microsoft Documenten
description: Meer informatie over het aanvragen van een toegangstoken vanuit Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8358d3378ea892ebeef653bcb51243c9f1aa0b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259771"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Een toegangstoken aanvragen in Azure Active Directory B2C

Een *toegangstoken* bevat claims die u gebruiken in Azure Active Directory B2C (Azure AD B2C) om de verleende machtigingen voor uw API's te identificeren. Wanneer u een resourceserver aanroept, moet een toegangstoken aanwezig zijn in de HTTP-aanvraag. Een toegangstoken wordt aangeduid als **access_token** in de antwoorden van Azure AD B2C.

In dit artikel ziet u hoe u een toegangstoken aanvragen voor een webtoepassing en web-API. Zie het overzicht van tokens in Azure [Active Directory B2C](tokens-overview.md)voor meer informatie over tokens in Azure AD B2C.

> [!NOTE]
> **Web API-ketens (On-Behalf-Of) worden niet ondersteund door Azure AD B2C.** - Veel architecturen bevatten een web-API die een andere downstream web-API moet aanroepen, beide beveiligd door Azure AD B2C. Dit scenario is gebruikelijk bij clients met een web-API back-end, die op hun beurt een andere service aanroept. Dit geketende web API-scenario kan worden ondersteund met behulp van de OAuth 2.0 JWT-referentiesubsidie voor dragers, ook wel bekend als de on-behalf-of-flow. De stroom voor namens en-namens-gebruik is momenteel echter niet geïmplementeerd in Azure AD B2C.

## <a name="prerequisites"></a>Vereisten

- [Maak een gebruikersstroom](tutorial-create-user-flows.md) zodat gebruikers zich kunnen aanmelden en zich kunnen aanmelden bij uw toepassing.
- Als u dit nog niet hebt gedaan, [voegt u een web-API-toepassing toe aan uw Azure Active Directory B2C-tenant.](add-web-application.md)

## <a name="scopes"></a>Bereiken

Scopes bieden een manier om machtigingen voor beveiligde bronnen te beheren. Wanneer een toegangstoken wordt aangevraagd, moet de clienttoepassing de gewenste machtigingen opgeven in de **scopeparameter** van de aanvraag. Als u bijvoorbeeld de **bereikwaarde** wilt opgeven `read` van voor de API met de APP ID **URI** van `https://contoso.onmicrosoft.com/api`, zou het bereik zijn `https://contoso.onmicrosoft.com/api/read`.

Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Bijvoorbeeld: gebruikers van de web-API kunnen zowel lees- als schrijftoegang hebben of alleen leestoegang. Als u meerdere machtigingen in dezelfde aanvraag wilt verkrijgen, u meerdere vermeldingen toevoegen aan de parameter met één **bereik** van de aanvraag, gescheiden door spaties.

In het volgende voorbeeld worden scopes weergegeven die zijn gedecodeerd in een URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

In het volgende voorbeeld worden scopes weergegeven die zijn gecodeerd in een URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Als u meer scopes aanvraagt dan wat wordt toegekend voor uw clienttoepassing, slaagt de oproep als er ten minste één toestemming wordt verleend. De **scp-claim** in het resulterende toegangstoken wordt gevuld met alleen de machtigingen die met succes zijn verleend. De OpenID Connect-standaard geeft verschillende speciale bereikwaarden op. De volgende scopes vertegenwoordigen de toestemming om toegang te krijgen tot het profiel van de gebruiker:

- **openid** - Vraagt een ID-token aan.
- **offline_access** - Vraagt een vernieuwingstoken aan met behulp van [Auth-codestromen](authorization-code-flow.md).

Als de **parameter** `/authorize` response_type `token`in een aanvraag bevat, moet de `openid` **scopeparameter** ten minste één andere resourcescope bevatten dan en `offline_access` die wordt toegekend. Anders mislukt `/authorize` het verzoek.

## <a name="request-a-token"></a>Een token aanvragen

Als u een toegangstoken wilt aanvragen, hebt u een autorisatiecode nodig. Hieronder vindt u een voorbeeld `/authorize` van een aanvraag voor het eindpunt voor een autorisatiecode. Aangepaste domeinen worden niet ondersteund voor gebruik met toegangstokens. Gebruik uw tenant-name.onmicrosoft.com domein in de aanvraag-URL.

In het volgende voorbeeld vervangt u de volgende waarden:

- `<tenant-name>`- De naam van uw Azure AD B2C-tenant.
- `<policy-name>`- De naam van uw aangepaste beleid of gebruikersstroom.
- `<application-ID>`- De toepassings-id van de webtoepassing die u hebt geregistreerd om de gebruikersstroom te ondersteunen.
- `<redirect-uri>`- De **Omleiding uri** die u hebt ingevoerd toen u de clientaanvraag registreerde.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Het antwoord met de autorisatiecode moet vergelijkbaar zijn met dit voorbeeld:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Nadat u de autorisatiecode hebt ontvangen, u deze gebruiken om een toegangstoken aan te vragen:

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

U ziet iets dat lijkt op het volgende antwoord:

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

Wanneer https://jwt.ms u het toegangstoken gebruikt dat is geretourneerd, ziet u iets dat lijkt op het volgende voorbeeld:

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

- Meer informatie over het [configureren van tokens in Azure AD B2C](configure-tokens.md)
