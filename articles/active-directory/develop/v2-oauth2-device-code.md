---
title: Codestroom van OAuth 2.0-apparaten | Azure
titleSuffix: Microsoft identity platform
description: Meld u aan voor gebruikers zonder browser. Bouw ingesloten en browserloze verificatiestromen met behulp van de autorisatieverlening voor het apparaat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 42f3ca233597d0fbc31ce656bd856875e873e3c2
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868490"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft-identiteitsplatform en de subsidiestroom voor autorisatievan OAuth 2.0-apparaten

Het Microsoft-identiteitsplatform ondersteunt de autorisatieverlening voor [apparaten,](https://tools.ietf.org/html/rfc8628)waarmee gebruikers zich kunnen aanmelden bij apparaten met beperkte invoer, zoals een smart-tv, IoT-apparaat of printer.  Om deze stroom mogelijk te maken, laat het apparaat de gebruiker een webpagina in zijn browser bezoeken op een ander apparaat om zich aan te melden.  Zodra de gebruiker zich aanmeldt, kan het apparaat toegangstokens krijgen en tokens vernieuwen als dat nodig is.

In dit artikel wordt beschreven hoe u rechtstreeks programmeren tegen het protocol in uw toepassing.  Waar mogelijk raden we u aan de ondersteunde Microsoft Authentication Libraries (MSAL) te gebruiken om tokens te [verkrijgen en beveiligde web-API's te bellen.](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  Kijk ook eens naar de [voorbeeld-apps die MSAL gebruiken.](sample-v2-code.md)

## <a name="protocol-diagram"></a>Protocoldiagram

De volledige apparaatcodestroom lijkt op het volgende diagram. We beschrijven elk van de stappen later in dit artikel.

![Apparaatcodestroom](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Aanvraag voor apparaatautorisatie

De client moet eerst contact opnemen met de verificatieserver voor een apparaat en gebruikerscode die wordt gebruikt om verificatie te starten. De klant verzamelt deze `/devicecode` aanvraag van het eindpunt. In dit verzoek moet de client ook de machtigingen bevatten die hij van de gebruiker moet verkrijgen. Vanaf het moment dat dit verzoek wordt verzonden, heeft de gebruiker `expires_in`slechts 15 minuten om in te loggen (de gebruikelijke waarde voor), dus maak dit verzoek alleen wanneer de gebruiker heeft aangegeven dat hij klaar is om in te loggen.

> [!TIP]
> Probeer dit verzoek uit te voeren in Postman!
> [![Probeer dit verzoek uit te voeren in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Vereist | Kan /common, /consumers, of /organizations.  Het kan ook de directorytenant zijn waarvan u toestemming wilt vragen in GUID of een vriendelijke naamindeling.  |
| `client_id` | Vereist | De **toepassings-id (client)** die de [Azure-portal – App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring toegewezen aan uw app. |
| `scope` | Aanbevolen | Een lijst met [scopes](v2-permissions-and-consent.md) die door de gebruiker is gescheiden.  |

### <a name="device-authorization-response"></a>Reactie van apparaatautorisatie

Een succesvol antwoord is een JSON-object met de vereiste informatie waarmee de gebruiker zich kan aanmelden.

| Parameter | Indeling | Beschrijving |
| ---              | --- | --- |
|`device_code`     | Tekenreeks | Een lange tekenreeks die wordt gebruikt om de sessie tussen de client en de autorisatieserver te verifiëren. De client gebruikt deze parameter om het toegangstoken op te vragen bij de autorisatieserver. |
|`user_code`       | Tekenreeks | Een korte tekenreeks die wordt weergegeven aan de gebruiker die wordt gebruikt om de sessie op een secundair apparaat te identificeren.|
|`verification_uri`| URI | De URI waar de gebruiker `user_code` naartoe moet gaan met de gebruiker om in te loggen. |
|`expires_in`      | int | Het aantal seconden `device_code` voor `user_code` de en verlopen. |
|`interval`        | int | Het aantal seconden dat de client moet wachten tussen polling-aanvragen. |
| `message`        | Tekenreeks | Een door de mens leesbare tekenreeks met instructies voor de gebruiker. Dit kan worden gelokaliseerd door een **queryparameter** op `?mkt=xx-XX`te nemen in de aanvraag van het formulier , waarbij de juiste taalcultuurcode wordt ingevuld. |

> [!NOTE]
> Het `verification_uri_complete` antwoordveld is op dit moment niet opgenomen of ondersteund.  We vermelden dit omdat [standard](https://tools.ietf.org/html/rfc8628) als je `verification_uri_complete` de standaard leest, je ziet dat dat wordt vermeld als een optioneel onderdeel van de standaard voor de stroom van de apparaatcode.

## <a name="authenticating-the-user"></a>De gebruiker authenticeren

Na ontvangst `user_code` `verification_uri`van de en , de client geeft deze aan de gebruiker, hen instrueren om in te loggen met behulp van hun mobiele telefoon of pc-browser.

Als de gebruiker zich verifieert met een persoonlijk account (op /common of /consumers), wordt hem gevraagd zich opnieuw aan te melden om de verificatiestatus naar het apparaat over te zetten.  Zij zullen ook worden gevraagd om toestemming te verlenen, om ervoor te zorgen dat ze op de hoogte zijn van de machtigingen die worden verleend.  Dit geldt niet voor werk- of schoolaccounts die worden gebruikt om te verifiëren.

Terwijl de gebruiker authenticeren `verification_uri`op de , `/token` de client moet worden polling `device_code`het eindpunt voor de gevraagde token met behulp van de .

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parameter | Vereist | Beschrijving|
| -------- | -------- | ---------- |
| `tenant`  | Vereist | Dezelfde tenant of tenantalias die in de oorspronkelijke aanvraag wordt gebruikt. |
| `grant_type` | Vereist | Moet worden`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Vereist | Moet overeenkomen `client_id` met de gebruikte in het oorspronkelijke verzoek. |
| `device_code`| Vereist | De `device_code` geretourneerde in de apparaatautorisatieaanvraag.  |

### <a name="expected-errors"></a>Verwachte fouten

De apparaatcodestroom is een pollingprotocol, dus uw client moet fouten verwachten voordat de gebruiker klaar is met authenticeren.

| Fout | Beschrijving | Clientactie |
| ------ | ----------- | -------------|
| `authorization_pending` | De gebruiker is nog niet klaar met authenticeren, maar heeft de stroom niet geannuleerd. | Herhaal het verzoek `interval` na ten minste enkele seconden. |
| `authorization_declined` | De eindgebruiker heeft de autorisatieaanvraag geweigerd.| Stop polling en keer terug naar een niet-geverifieerde status.  |
| `bad_verification_code`| Het `device_code` naar `/token` het eindpunt verzonden werd niet herkend. | Controleer of de client `device_code` de juiste stuurt in de aanvraag. |
| `expired_token` | Ten `expires_in` minste seconden zijn verstreken, en `device_code`authenticatie is niet langer mogelijk met deze . | Stop met polling en keer terug naar een niet-geverifieerde status. |

### <a name="successful-authentication-response"></a>Succesvolle verificatierespons

Een succesvolle tokenreactie ziet eruit als volgt:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parameter | Indeling | Beschrijving |
| --------- | ------ | ----------- |
| `token_type` | Tekenreeks| Altijd "Drager. |
| `scope` | Gescheiden tekenreeksen spatie | Als een toegangstoken is geretourneerd, worden hier de scopes weergegeven waarvoor het toegangstoken geldig is. |
| `expires_in`| int | Aantal seconden voordat het meegeleverde toegangstoken geldig is voor. |
| `access_token`| Ondoorzichtige tekenreeks | Uitgegeven voor de [gevraagde scopes.](v2-permissions-and-consent.md)  |
| `id_token`   | JWT JWT | Uitgegeven als `scope` de oorspronkelijke `openid` parameter het bereik bevatte.  |
| `refresh_token` | Ondoorzichtige tekenreeks | Uitgegeven als `scope` de `offline_access`oorspronkelijke parameter is opgenomen .  |

U het vernieuwingstoken gebruiken om nieuwe toegangstokens te verkrijgen en tokens te vernieuwen met dezelfde stroom die is gedocumenteerd in de [OAuth-codestroomdocumentatie.](v2-oauth2-auth-code-flow.md#refresh-the-access-token)
