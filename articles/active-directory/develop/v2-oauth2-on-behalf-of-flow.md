---
title: Microsoft-identiteitsplatform en OAuth2.0 Voor naam-of-stroom | Azure
description: In dit artikel wordt beschreven hoe u HTTP-berichten gebruiken om serviceverificatie te implementeren met behulp van de oauth2.0-stroom namens de nl-nl-uitvoerstroom.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a689e0f588046296591069801d621f3608a372e5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886259"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft-identiteitsplatform en OAuth 2.0 On-Behalf-Of flow


De OAuth 2.0 On-Behalf-Of flow (OBO) dient de use case waarbij een toepassing een service/web API aanroept, die op zijn beurt een andere service/web API moet aanroepen. Het idee is om de gedelegeerde gebruikersidentiteit en machtigingen te verspreiden via de aanvraagketen. Als de middle-tier-service geverifieerde aanvragen voor de downstreamservice wil indienen, moet deze namens de gebruiker een toegangstoken van het Microsoft-identiteitsplatform beveiligen.

In dit artikel wordt beschreven hoe u rechtstreeks programmeren tegen het protocol in uw toepassing.  Waar mogelijk raden we u aan de ondersteunde Microsoft Authentication Libraries (MSAL) te gebruiken om tokens te [verkrijgen en beveiligde web-API's te bellen.](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  Kijk ook eens naar de [voorbeeld-apps die MSAL gebruiken.](sample-v2-code.md)

> [!NOTE]
>
> - Het eindpunt van het Microsoft-identiteitsplatform ondersteunt niet alle scenario's en functies. Als u wilt bepalen of u het eindpunt van het Microsoft-identiteitsplatform moet gebruiken, leest u over [de beperkingen van het Microsoft-identiteitsplatform.](active-directory-v2-limitations.md) 
> - Vanaf mei 2018 `id_token` kan een impliciete stroom niet meer worden gebruikt voor OBO-stroom. Apps met één pagina (SB's) moeten een **toegangstoken** doorgeven aan een vertrouwelijke client op het middenniveau om obo-stromen uit te voeren. Zie [beperkingen](#client-limitations)voor meer informatie over welke clients OBO-gesprekken kunnen uitvoeren.

## <a name="protocol-diagram"></a>Protocoldiagram

Ga ervan uit dat de gebruiker is geverifieerd op een toepassing met behulp van de [OAuth 2.0-autorisatiecode-subsidiestroom](v2-oauth2-auth-code-flow.md) of een andere inlogstroom. Op dit moment heeft de toepassing een toegangstoken *voor API A* (token A) met de claims en toestemming van de gebruiker om toegang te krijgen tot de middle-tier web API (API A). API A moet nu een geverifieerd verzoek indienen bij de downstream web API (API B).

De volgende stappen vormen de OBO-stroom en worden uitgelegd met behulp van het volgende diagram.

![Toont de OAuth2.0 On-Behalf-Of flow](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. De clienttoepassing doet een verzoek om API `aud` A met token A (met een claim van API A).
1. API A verifieert naar het eindpunt voor de uitgifte van tokenvan microsoft-identiteitsplatformen en vraagt om een token om toegang te krijgen tot API B.
1. Het eindpunt voor de uitgifte van het Microsoft-identiteitsplatform-token valideert de referenties van API A samen met token A en geeft het toegangstoken voor API B (token B) uit aan API A.
1. Token B wordt ingesteld door API A in de autorisatiekop van de aanvraag naar API B.
1. Gegevens uit de beveiligde bron worden door API B naar API A en van daaruit naar de client teruggestuurd.

> [!NOTE]
> In dit scenario heeft de middle-tier service geen gebruikersinteractie om toestemming van de gebruiker te verkrijgen om toegang te krijgen tot de downstream API. Daarom wordt de optie om toegang te verlenen tot de downstream-API vooraf gepresenteerd als onderdeel van de toestemmingsstap tijdens de verificatie. Zie Toestemming krijgen voor de toepassing [op de middelste laag](#gaining-consent-for-the-middle-tier-application)voor meer informatie over het instellen van deze app voor uw app.

## <a name="service-to-service-access-token-request"></a>Tokenaanvraag service-to-service-toegang

Als u een toegangstoken wilt aanvragen, maakt u een HTTP-BERICHT naar het tokeneindpunt voor het tenant-specifieke Microsoft-identiteitsplatform met de volgende parameters.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Er zijn twee gevallen, afhankelijk van de vraag of de clienttoepassing ervoor kiest om te worden beveiligd door een gedeeld geheim of een certificaat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Eerste geval: Toegang tot tokenaanvraag met een gedeeld geheim

Bij het gebruik van een gedeeld geheim bevat een service-to-service access tokenaanvraag de volgende parameters:

| Parameter |  | Beschrijving |
| --- | --- | --- |
| `grant_type` | Vereist | Het type tokenaanvraag. Voor een aanvraag met een JWT `urn:ietf:params:oauth:grant-type:jwt-bearer`moet de waarde . |
| `client_id` | Vereist | De toepassings-id (client) die [de Azure-portal - app-registratiepagina](https://go.microsoft.com/fwlink/?linkid=2083908) aan uw app heeft toegewezen. |
| `client_secret` | Vereist | Het clientgeheim dat u voor uw app hebt gegenereerd op de pagina Azure-portal - App-registraties. |
| `assertion` | Vereist | De waarde van het token dat in de aanvraag wordt gebruikt.  Dit token moet een publiek van de app hebben die deze `client-id` OBO-aanvraag doet (de app die door het veld wordt aangeduid). |
| `scope` | Vereist | Een ruimtegescheiden lijst met scopes voor de tokenaanvraag. Zie [scopes](v2-permissions-and-consent.md)voor meer informatie . |
| `requested_token_use` | Vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de OBO-stroom moet de `on_behalf_of`waarde worden ingesteld op . |

#### <a name="example"></a>Voorbeeld

De volgende HTTP POST vraagt om `user.read` een access https://graph.microsoft.com token en refresh token met ruimte voor de web-API.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>Tweede aanvraag: Aanvraag voor toegangstoken met een certificaat

Een service-to-service access tokenaanvraag met een certificaat bevat de volgende parameters:

| Parameter |  | Beschrijving |
| --- | --- | --- |
| `grant_type` | Vereist | Het type tokenaanvraag. Voor een aanvraag met een JWT `urn:ietf:params:oauth:grant-type:jwt-bearer`moet de waarde . |
| `client_id` | Vereist |  De toepassings-id (client) die [de Azure-portal - app-registratiepagina](https://go.microsoft.com/fwlink/?linkid=2083908) aan uw app heeft toegewezen. |
| `client_assertion_type` | Vereist | De waarde `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`moet . |
| `client_assertion` | Vereist | Een bewering (een JSON-webtoken) dat u moet maken en ondertekenen met het certificaat dat u hebt geregistreerd als referenties voor uw toepassing. Zie [certificaatreferenties](active-directory-certificate-credentials.md)voor meer informatie over het registreren van uw certificaat en de notatie van de bewering. |
| `assertion` | Vereist | De waarde van het token dat in de aanvraag wordt gebruikt. |
| `requested_token_use` | Vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de OBO-stroom moet de `on_behalf_of`waarde worden ingesteld op . |
| `scope` | Vereist | Een ruimte-gescheiden lijst met scopes voor de tokenaanvraag. Zie [scopes](v2-permissions-and-consent.md)voor meer informatie .|

Merk op dat de parameters zijn bijna hetzelfde als in `client_secret` het geval van het `client_assertion_type` verzoek `client_assertion`door gedeeld geheim, behalve dat de parameter wordt vervangen door twee parameters: a en .

#### <a name="example"></a>Voorbeeld

De volgende HTTP POST vraagt `user.read` een https://graph.microsoft.com toegangstoken aan met ruimte voor de web-API met een certificaat.

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

## <a name="service-to-service-access-token-response"></a>Service-toegang tot tokenrespons

Een succesrespons is een JSON OAuth 2.0 respons met de volgende parameters.

| Parameter | Beschrijving |
| --- | --- |
| `token_type` | Geeft de waarde van het tokentype aan. Het enige type dat het `Bearer`Identiteitsplatform van Microsoft ondersteunt, is . Zie het [OAuth 2.0 Authorization Framework: Toondertokengebruik (RFC 6750) voor](https://www.rfc-editor.org/rfc/rfc6750.txt)meer informatie over tokens aan toonder. |
| `scope` | De reikwijdte van de toegang die in het token wordt verleend. |
| `expires_in` | De tijdsduur, in seconden, dat het toegangstoken geldig is. |
| `access_token` | Het gevraagde toegangstoken. De oproepservice kan dit token gebruiken om te verifiëren aan de ontvangende service. |
| `refresh_token` | Het vernieuwingstoken voor het gevraagde toegangstoken. De aanroepende service kan dit token gebruiken om een ander toegangstoken aan te vragen nadat het huidige toegangstoken is verlopen. Het vernieuwingstoken wordt `offline_access` alleen verstrekt als het bereik is aangevraagd. |

### <a name="success-response-example"></a>Voorbeeld van succesrespons

In het volgende voorbeeld wordt een succesreactie weergegeven https://graph.microsoft.com op een aanvraag voor een toegangstoken voor de web-API.

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
> Het bovenstaande toegangstoken is een v1.0-geformatteerd token. Dit komt omdat het token wordt geleverd op basis van de **bron** die wordt geopend. De Microsoft Graph is ingesteld om v1.0-tokens te accepteren, dus Microsoft-identiteitsplatform produceert v1.0-toegangstokens wanneer een client tokens voor Microsoft Graph aanvraagt. Alleen toepassingen moeten kijken naar toegangstokens. Klanten mogen ze **niet** inspecteren.

### <a name="error-response-example"></a>Voorbeeld van foutreactie

Een foutantwoord wordt geretourneerd door het tokeneindpunt wanneer u een toegangstoken voor de downstream-API probeert te verkrijgen, als de downstream-API een conditional access-beleid (zoals meervoudige verificatie) heeft ingesteld. De service op het middenniveau moet deze fout aan de clienttoepassing weergeven, zodat de clienttoepassing de gebruikersinteractie kan bieden om te voldoen aan het beleid voor voorwaardelijke toegang.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Het toegangstoken gebruiken om toegang te krijgen tot de beveiligde bron

Nu kan de middle-tier service het hierboven verkregen token gebruiken om geverifieerde aanvragen te `Authorization` doen voor de downstream web-API, door het token in de koptekst in te stellen.

### <a name="example"></a>Voorbeeld

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Toestemming verkrijgen voor de toepassing op het middenniveau

Afhankelijk van de architectuur of het gebruik van uw toepassing, u verschillende strategieën overwegen om ervoor te zorgen dat de OBO-stroom succesvol is. In alle gevallen is het uiteindelijke doel om ervoor te zorgen dat de juiste toestemming wordt gegeven, zodat de client-app de middelste-tier-app kan bellen en de middelste laag-app toestemming heeft om de back-endbron te bellen. 

> [!NOTE]
> Voorheen ondersteunde het Microsoft-accountsysteem (persoonlijke accounts) het veld 'Bekende clienttoepassing' niet en kon het ook geen gecombineerde toestemming weergeven.  Dit is toegevoegd en alle apps in het Microsoft-identiteitsplatform kunnen de bekende clientapplicatiebenadering gebruiken voor gettign-toestemming voor OBO-gesprekken. 

### <a name="default-and-combined-consent"></a>/.standaard- en gecombineerde toestemming

De toepassing op de middelste laag voegt de client toe aan de lijst met bekende clienttoepassingen in het manifest en vervolgens kan de client een gecombineerde toestemmingsstroom activeren voor zowel zichzelf als de toepassing op de middelste laag. Op het eindpunt van het Microsoft-identiteitsplatform gebeurt dit met behulp van het [ `/.default` bereik.](v2-permissions-and-consent.md#the-default-scope) Wanneer u een toestemmingsscherm activeert met behulp van bekende clienttoepassingen en `/.default`, worden in het toestemmingsscherm machtigingen weergegeven voor **zowel** de client naar de API op de middelste laag) en worden ook de machtigingen opgevraagd die vereist zijn voor de API op het middenniveau. De gebruiker geeft toestemming voor beide toepassingen, en dan werkt de OBO-stroom.

### <a name="pre-authorized-applications"></a>Vooraf geautoriseerde aanvragen

Bronnen kunnen aangeven dat een bepaalde toepassing altijd toestemming heeft om bepaalde scopes te ontvangen. Dit is vooral handig om verbindingen tussen een front-end client en een back-end resource naadlooser te maken. Een resource kan meerdere vooraf geautoriseerde toepassingen declareren - een dergelijke toepassing kan deze machtigingen aanvragen in een OBO-stroom en deze ontvangen zonder dat de gebruiker toestemming geeft.

### <a name="admin-consent"></a>toestemming van de beheerder

Een tenantbeheerder kan garanderen dat toepassingen toestemming hebben om hun vereiste API's te bellen door beheerderstoestemming te geven voor de toepassing op de middelste laag. Om dit te doen, kan de beheerder de toepassing op de middelste laag in zijn tenant vinden, de pagina vereiste machtigingen openen en ervoor kiezen om toestemming te geven voor de app. Zie de [documentatie over toestemming en machtigingen](v2-permissions-and-consent.md)voor meer informatie over beheerderstoestemming.

### <a name="use-of-a-single-application"></a>Gebruik van één toepassing

In sommige scenario's hebt u mogelijk slechts één koppeling van de midden- en front-endclient. In dit scenario u het gemakkelijker vinden om dit één enkele toepassing te maken, waardoor de noodzaak van een toepassing op het middenniveau helemaal wordt ontkend. Als u wilt verifiëren tussen de front-end en de web-API, u cookies, een id_token of een toegangstoken gebruiken dat voor de toepassing zelf is aangevraagd. Vraag vervolgens toestemming van deze afzonderlijke toepassing aan de back-endbron.

## <a name="client-limitations"></a>Clientbeperkingen

Als een client de impliciete stroom gebruikt om een id_token te krijgen en die client heeft ook wildcards in een antwoord-URL, kan de id_token niet worden gebruikt voor een OBO-stroom.  Toegangstokens die via de impliciete subsidiestroom zijn verkregen, kunnen echter nog steeds worden ingewisseld door een vertrouwelijke client, zelfs als de initiërende client een URL met een wildcard-antwoord heeft geregistreerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het OAuth 2.0-protocol en een andere manier om service uit te voeren met behulp van clientreferenties.

* [OAuth 2.0 clientreferenties verlenen in Microsoft identity platform](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0-codestroom in microsoft-identiteitsplatform](v2-oauth2-auth-code-flow.md)
* [Het `/.default` bereik gebruiken](v2-permissions-and-consent.md#the-default-scope)
