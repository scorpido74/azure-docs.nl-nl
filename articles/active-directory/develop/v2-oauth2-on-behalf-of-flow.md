---
title: Micro soft Identity platform en OAuth 2.0 namens-of-flow | Azure
description: In dit artikel wordt beschreven hoe u HTTP-berichten gebruikt om service te implementeren voor service verificatie met behulp van de OAuth 2.0 namens-of-stroom.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d851572731ad9c83093b7076279df112585703
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207498"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Micro soft Identity platform en OAuth 2,0-of-flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

De OAuth 2,0-of-flow (OBO) wordt gebruikt als een toepassing een service/Web-API aanroept, die op zijn beurt een andere service/Web-API moet aanroepen. Het is verstandig om de gedelegeerde gebruikers identiteit en de machtigingen via de aanvraag keten door te geven. Voor de middelste service om geverifieerde aanvragen voor de downstream-service te maken, moet het een toegangs token van het micro soft Identity-platform worden beveiligd namens de gebruiker.

In dit artikel wordt beschreven hoe u direct kunt Program meren met het protocol in uw toepassing.  Als dat mogelijk is, kunt u het beste de ondersteunde micro soft-verificatie bibliotheken (MSAL) gebruiken in plaats van [tokens te verkrijgen en beveiligde web-api's](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)aan te roepen.  Bekijk ook de voor beeld- [apps die gebruikmaken van MSAL](sample-v2-code.md).

> [!NOTE]
>
> - Het micro soft Identity platform-eind punt biedt geen ondersteuning voor alle scenario's en functies. Lees over [micro soft Identity platform-beperkingen](active-directory-v2-limitations.md)om te bepalen of u het micro soft Identity platform-eind punt moet gebruiken. In het bijzonder worden bekende client toepassingen niet ondersteund voor apps met Microsoft-account (MSA) en Azure AD-doel groepen. Daarom werkt een gemeen schappelijke toestemmings patroon voor OBO niet voor clients die zich aanmelden op zowel persoonlijke als werk-of school accounts. Zie voor meer informatie over het afhandelen van deze stap van de stroom het [verkrijgen van toestemming voor de middelste toepassing](#gaining-consent-for-the-middle-tier-application).
> - Vanaf mei 2018 kan sommige impliciete stroom die is afgeleid `id_token` niet worden gebruikt voor OBO-stroom. Apps met één pagina (SPAs) moeten een **toegangs** token door geven aan een vertrouwelijke client voor de middelste laag om in plaats daarvan OBO stromen uit te voeren. Zie [beperkingen](#client-limitations)voor meer informatie over welke clients OBO-aanroepen kunnen uitvoeren.

## <a name="protocol-diagram"></a>Protocol diagram

Stel dat de gebruiker is geverifieerd op een toepassing met behulp van de [OAuth 2,0-autorisatie code toekenning stroom](v2-oauth2-auth-code-flow.md). Op dit moment heeft de toepassing een toegangs token *voor API a* (token a) met de claims van de gebruiker en toestemming om toegang te krijgen tot de Web-API (API a) van de middelste laag. Nu moet API A een geverifieerde aanvraag indienen bij de downstream Web API (API B).

De volgende stappen vormen de OBO-stroom en worden uitgelegd in de Help van het onderstaande diagram.

![Hiermee worden de OAuth 2.0 namens-stroom weer gegeven](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. De client toepassing maakt een aanvraag voor API A met token A (met een `aud` claim van API A).
1. API A wordt geverifieerd bij het micro soft Identity platform token uitgifte-eind punt en vraagt een token aan voor toegang tot API B.
1. Het micro soft Identity platform-eind punt voor de uitgifte van tokens valideert API A met token A en geeft het toegangs token voor API B (token B).
1. Token B is ingesteld in de autorisatie-header van de aanvraag voor API B.
1. Gegevens van de beveiligde bron worden geretourneerd door API B.

> [!NOTE]
> In dit scenario heeft de middelste laag service geen interactie van de gebruiker om de toestemming van de gebruiker voor toegang tot de downstream API te verkrijgen. Daarom wordt de optie voor het verlenen van toegang tot de downstream API vooraf weer gegeven als onderdeel van de stap voor toestemming tijdens de verificatie. Zie [toestemming geven voor de middelste toepassing](#gaining-consent-for-the-middle-tier-application)voor meer informatie over het instellen van dit voor uw app.

## <a name="service-to-service-access-token-request"></a>Aanvraag voor service-naar-service-toegangs token

Als u een toegangs token wilt aanvragen, maakt u een HTTP POST naar het Tenant-specifieke micro soft Identity platform token-eind punt met de volgende para meters.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Er zijn twee gevallen, afhankelijk van het feit of de client toepassing wordt beveiligd door een gedeeld geheim of een certificaat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Eerste case: toegangs token aanvraag met een gedeeld geheim

Bij gebruik van een gedeeld geheim bevat een aanvraag voor service-naar-service-toegangs token de volgende para meters:

| Parameter |  | Beschrijving |
| --- | --- | --- |
| `grant_type` | Vereist | Het type van de token aanvraag. Voor een aanvraag met behulp van een JWT moet de waarde `urn:ietf:params:oauth:grant-type:jwt-bearer`zijn. |
| `client_id` | Vereist | De ID van de toepassing (client) waaraan [de Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina is toegewezen aan uw app. |
| `client_secret` | Vereist | Het client geheim dat u hebt gegenereerd voor uw app op de pagina Azure Portal-App-registraties. |
| `assertion` | Vereist | De waarde van het token dat in de aanvraag wordt gebruikt. |
| `scope` | Vereist | Een lijst met door spaties gescheiden bereiken voor de token aanvraag. Zie [scopes](v2-permissions-and-consent.md)voor meer informatie. |
| `requested_token_use` | Vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de OBO-stroom moet de waarde worden ingesteld op `on_behalf_of`. |

#### <a name="example"></a>Voorbeeld

De volgende HTTP POST vraagt een toegangs token en vernieuwings token met `user.read` bereik voor de https://graph.microsoft.com Web-API.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Tweede geval: toegangs token aanvraag met een certificaat

Een aanvraag voor service-naar-service-toegangs token met een certificaat bevat de volgende para meters:

| Parameter |  | Beschrijving |
| --- | --- | --- |
| `grant_type` | Vereist | Het type van de token aanvraag. Voor een aanvraag met behulp van een JWT moet de waarde `urn:ietf:params:oauth:grant-type:jwt-bearer`zijn. |
| `client_id` | Vereist |  De ID van de toepassing (client) waaraan [de Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina is toegewezen aan uw app. |
| `client_assertion_type` | Vereist | De waarde moet `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`zijn. |
| `client_assertion` | Vereist | Een verklaring (een JSON-webtoken) die u moet maken en ondertekenen met het certificaat dat u hebt geregistreerd als referenties voor uw toepassing. Zie [certificaat referenties](active-directory-certificate-credentials.md)voor meer informatie over het registreren van uw certificaat en de indeling van de bevestiging. |
| `assertion` | Vereist | De waarde van het token dat in de aanvraag wordt gebruikt. |
| `requested_token_use` | Vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de OBO-stroom moet de waarde worden ingesteld op `on_behalf_of`. |
| `scope` | Vereist | Een lijst met door spaties gescheiden bereiken voor de token aanvraag. Zie [scopes](v2-permissions-and-consent.md)voor meer informatie.|

U ziet dat de para meters bijna hetzelfde zijn als in het geval van de aanvraag van het gedeelde geheim, behalve dat de para meter `client_secret` wordt vervangen door twee para meters: een `client_assertion_type` en `client_assertion`.

#### <a name="example"></a>Voorbeeld

Met de volgende HTTP POST wordt een toegangs token met `user.read` bereik aangevraagd voor de https://graph.microsoft.com Web-API met een certificaat.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Antwoord service-to-service-toegangs token

Een reactie op geslaagde pogingen is een JSON OAuth 2,0-antwoord met de volgende para meters.

| Parameter | Beschrijving |
| --- | --- |
| `token_type` | Geeft de waarde van het token type aan. Het enige type dat door micro soft Identity platform wordt ondersteund, is `Bearer`. Zie voor meer informatie over Bearer-tokens het [OAuth 2,0 Authorization Framework: Bearer-token gebruik (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Het bereik van toegang dat in het token wordt verleend. |
| `expires_in` | De tijds duur in seconden dat het toegangs token geldig is. |
| `access_token` | Het aangevraagde toegangs token. De aanroepende service kan dit token gebruiken om te verifiëren bij de ontvangende service. |
| `refresh_token` | Het vernieuwings token voor het aangevraagde toegangs token. De aanroepende service kan dit token gebruiken om een ander toegangs token aan te vragen nadat het huidige toegangs token is verlopen. Het vernieuwings token wordt alleen opgegeven als het `offline_access` bereik is aangevraagd. |

### <a name="success-response-example"></a>Voor beeld van een geslaagd antwoord

In het volgende voor beeld ziet u een reactie op een aanvraag voor een toegangs token voor de Web-API van https://graph.microsoft.com.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> Het bovenstaande toegangs token is een token met de indeling v 1.0. Dit komt doordat het token wordt aangeboden op basis van de bron waartoe toegang wordt verkregen. Het Microsoft Graph aanvragen v 1.0-tokens, waardoor het micro soft Identity-platform v 1.0 toegangs tokens produceert wanneer een client tokens voor Microsoft Graph aanvraagt. Alleen toepassingen moeten de toegangs tokens bekijken. Clients hoeven ze niet te controleren.

### <a name="error-response-example"></a>Voor beeld van fout antwoorden

Er wordt een fout bericht geretourneerd door het token-eind punt bij het ophalen van een toegangs token voor de downstream API als voor de downstream API een beleid voor voorwaardelijke toegang is ingesteld (zoals multi-factor Authentication). De middelste laag service moet deze fout op de client toepassing belicht, zodat de client toepassing de gebruikers interactie kan bieden om te voldoen aan het beleid voor voorwaardelijke toegang.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Het toegangs token gebruiken om toegang te krijgen tot de beveiligde resource

De middelste laag service kan nu het aangeschafte token gebruiken om geverifieerde aanvragen naar de downstream Web-API te maken, door het token in te stellen in de `Authorization`-header.

### <a name="example"></a>Voorbeeld

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Toestemming geven voor de toepassing in de middelste laag

Afhankelijk van de doel groep voor uw toepassing, kunt u verschillende strategieën overwegen om ervoor te zorgen dat de OBO-stroom slaagt. In alle gevallen is het uiteindelijk doel om ervoor te zorgen dat de juiste toestemming wordt gegeven. Hoe dit gebeurt, is echter afhankelijk van welke gebruikers door uw toepassing worden ondersteund.

### <a name="consent-for-azure-ad-only-applications"></a>Toestemming voor Azure AD-toepassingen

#### <a name="default-and-combined-consent"></a>/.default en gecombineerde toestemming

Voor toepassingen die alleen werk-of school accounts moeten aanmelden, is de traditionele benadering ' bekende client toepassingen ' voldoende. De middelste laag toepassing voegt de client toe aan de lijst met bekende client toepassingen in het manifest en vervolgens kan de client een gecombineerde toestemmings stroom voor zowel zichzelf als de middelste laag toepassing activeren. Op het micro soft Identity platform-eind punt wordt dit gedaan met behulp van het [`/.default` bereik](v2-permissions-and-consent.md#the-default-scope). Wanneer een goedkeurings scherm wordt geactiveerd met behulp van bekende client toepassingen en `/.default`, toont het venster toestemming de machtigingen voor zowel de client op de middenlaag van de middelste laag, en vraagt ook welke machtigingen voor de middelste laag-API vereist zijn. De gebruiker geeft toestemming voor beide toepassingen en vervolgens werkt de OBO-stroom.

Op dit moment biedt het persoonlijke Microsoft-account systeem geen ondersteuning voor gecombineerde toestemming. deze benadering werkt daarom niet voor apps die specifiek persoonlijke accounts willen ondertekenen. Persoonlijke micro soft-accounts die worden gebruikt als gast accounts in een Tenant worden verwerkt met behulp van het Azure AD-systeem en kunnen door de gecombineerde toestemming gaan.

#### <a name="pre-authorized-applications"></a>Vooraf geautoriseerde toepassingen

Een functie van de toepassings Portal is ' vooraf geautoriseerde toepassingen '. Op deze manier kan een resource aangeven dat een bepaalde toepassing altijd gemachtigd is om bepaalde bereiken te ontvangen. Dit is vooral handig om verbindingen te maken tussen een front-end-client en een back-end-bron naadloos. Een resource kan meerdere vooraf geautoriseerde toepassingen declareren: elke toepassing kan deze machtigingen aanvragen in een OBO-stroom en ze ontvangen zonder toestemming te geven aan de gebruiker.

#### <a name="admin-consent"></a>toestemming van de beheerder

Een Tenant beheerder kan garanderen dat toepassingen gemachtigd zijn om de vereiste Api's aan te roepen door toestemming van de beheerder voor de middelste laag toepassing op te geven. Om dit te doen, kan de beheerder de toepassing voor het middelste niveau vinden in hun Tenant, de pagina vereiste machtigingen openen en ervoor kiezen om toestemming te geven voor de app. Zie de [documentatie voor toestemming en machtigingen](v2-permissions-and-consent.md)voor meer informatie over de toestemming van de beheerder.

### <a name="consent-for-azure-ad--microsoft-account-applications"></a>Toestemming voor Azure AD + Microsoft-account-toepassingen

Vanwege beperkingen in het machtigings model voor persoonlijke accounts en het ontbreken van een Tenant, zijn de vereisten voor toestemming voor persoonlijke accounts een andere bit dan Azure AD. Er is geen Tenant voor het bieden van toestemming voor de hele Tenant, noch de mogelijkheid om gecombineerde toestemming uit te voeren. Daarom zijn er ook andere strategieën aanwezig: Houd er rekening mee dat deze werk voor toepassingen die alleen Azure AD-accounts moeten ondersteunen.

#### <a name="use-of-a-single-application"></a>Gebruik van één toepassing

In sommige scenario's is het mogelijk dat u slechts één paar van de middelste laag en front-end-client hoeft te koppelen. In dit scenario is het mogelijk dat u deze app gemakkelijker kunt maken, waardoor het niet meer nodig is om een toepassing met een middelste laag te verbinden. Als u wilt verifiëren tussen de front-end en de Web-API, kunt u cookies, een id_token of een toegangs token gebruiken dat is aangevraagd voor de toepassing zelf. Vraag vervolgens toestemming van deze enkele toepassing aan de back-end-bron.

## <a name="client-limitations"></a>Client beperkingen

Als een client de impliciete stroom gebruikt om een id_token op te halen en die client ook joker tekens heeft in een antwoord-URL, kan de id_token niet worden gebruikt voor een OBO-stroom.  Toegangs tokens die zijn verkregen via de impliciete toekennings stroom kunnen echter nog steeds worden ingewisseld door een vertrouwelijke client, zelfs als de initiërende client een antwoord-URL voor joker tekens heeft geregistreerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het OAuth 2,0-protocol en een andere manier om service-to-service-verificatie uit te voeren met behulp van client referenties.

* [OAuth 2,0-client referenties verlenen in het micro soft Identity-platform](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2,0-code stroom in micro soft Identity platform](v2-oauth2-auth-code-flow.md)
* [Het `/.default` bereik gebruiken](v2-permissions-and-consent.md#the-default-scope)
