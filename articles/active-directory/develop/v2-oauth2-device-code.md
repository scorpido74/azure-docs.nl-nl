---
title: Micro soft Identity platform gebruiken voor het aanmelden van gebruikers op browser-minder apparaten | Azure
description: Maak Inge sloten en browser-minder verificatie stromen met behulp van de apparaatcode toekenning.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdd99899494e9f7b3c0caa4e83f18803b969db1e
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192716"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Micro soft Identity platform en de code stroom van het OAuth 2,0-apparaat

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Het micro soft-identiteits platform biedt ondersteuning voor de [toekenning](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12)van de apparaatcode, waarmee gebruikers zich kunnen aanmelden bij apparaten met invoer beperkingen, zoals een Smart TV, IOT-apparaat of printer.  Om deze stroom in te scha kelen, heeft de gebruiker van het apparaat een webpagina in de browser op een ander apparaat om u aan te melden.  Zodra de gebruiker zich aanmeldt, kan het apparaat toegangs tokens verkrijgen en tokens vernieuwen als dat nodig is.  

> [!IMPORTANT]
> Op dit moment ondersteunt het micro soft Identity platform-eind punt alleen de stroom van het apparaat voor Azure AD-tenants, maar niet voor persoonlijke accounts.  Dit betekent dat u een endpoint moet gebruiken dat is ingesteld als Tenant of het `organizations` eind punt.  Deze ondersteuning wordt binnenkort ingeschakeld. 
>
> Persoonlijke accounts die worden uitgenodigd voor een Azure AD-Tenant, kunnen gebruikmaken van de subsidie toekenning, maar alleen in de context van de Tenant.
>
> Als aanvullende opmerking wordt het `verification_uri_complete` veld antwoord op dit moment niet opgenomen of ondersteund.  Dit wordt vermeld omdat u de standaard ziet die `verification_uri_complete` wordt weer gegeven als een optioneel onderdeel van de apparaatcode flow-standaard.

> [!NOTE]
> Het micro soft Identity platform-eind punt biedt geen ondersteuning voor alle Azure Active Directory-scenario's en-functies. Lees over [micro soft Identity platform-beperkingen](active-directory-v2-limitations.md)om te bepalen of u het micro soft Identity platform-eind punt moet gebruiken.

## <a name="protocol-diagram"></a>Protocol diagram

De gehele apparaatcode stroom ziet er ongeveer uit als in het volgende diagram. Verderop in dit artikel worden de stappen beschreven.

![Stroom voor apparaatcode](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Autorisatie aanvraag voor apparaat

De client moet eerst controleren met de verificatie server voor een apparaat en gebruikers code die wordt gebruikt om verificatie te initiëren. De client verzamelt deze aanvraag van het `/devicecode` eind punt. In deze aanvraag moet de client ook de machtigingen bevatten die nodig zijn voor het verkrijgen van de gebruiker. Vanaf het moment dat deze aanvraag wordt verzonden, heeft de gebruiker slechts 15 minuten om zich aan te melden (de `expires_in`normale waarde voor), dus deze aanvraag alleen als de gebruiker heeft aangegeven dat deze klaar is om zich aan te melden.

> [!TIP]
> Probeer deze aanvraag uit te voeren in postman!
> [![Probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parameter | Voorwaarde | Description |
| --- | --- | --- |
| `tenant` | Vereist |De Directory-Tenant waarvan u toestemming wilt aanvragen. Dit kan een GUID of beschrijvende naam zijn.  |
| `client_id` | Vereist | De **client-id** van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen. |
| `scope` | Aanbevolen | Een lijst met door spaties gescheiden [bereiken](v2-permissions-and-consent.md) waarvan u wilt dat de gebruiker toestemming geeft.  |

### <a name="device-authorization-response"></a>Reactie van het apparaat autorisatie

Een geslaagde reactie is een JSON-object met de vereiste gegevens om de gebruiker in staat te stellen zich aan te melden.  

| Parameter | Indeling | Description |
| ---              | --- | --- |
|`device_code`     | Tekenreeks | Een lange teken reeks die wordt gebruikt om de sessie tussen de client en de autorisatie server te controleren. De client gebruikt deze para meter om het toegangs token van de autorisatie server aan te vragen. |
|`user_code`       | Tekenreeks | Een korte teken reeks die wordt weer gegeven aan de gebruiker die wordt gebruikt om de sessie op een secundair apparaat te identificeren.|
|`verification_uri`| URI | De URI waarnaar de gebruiker moet gaan met de `user_code` om zich aan te melden. |
|`expires_in`      | int | Het aantal seconden voor de en `device_code` `user_code` de verval datum. |
|`interval`        | int | Het aantal seconden dat de client moet wachten tussen polling aanvragen. |
| `message`        | Tekenreeks | Een teken reeks met lees bare tekst met instructies voor de gebruiker. Dit kan worden gelokaliseerd door een **query parameter** op te nemen in de aanvraag `?mkt=xx-XX`van het formulier en de juiste taal cultuur code in te vullen. |

## <a name="authenticating-the-user"></a>Verificatie van de gebruiker

Na ontvangst van `user_code` de `verification_uri`en, wordt deze door de client weer gegeven aan de gebruiker en wordt hen gevraagd zich aan te melden met hun mobiele telefoon of PC-browser.  Daarnaast kan de client een QR-code of een vergelijkbaar mechanisme gebruiken om de `verfication_uri_complete`weer te geven. Dit is de stap van `user_code` het invoeren van de voor de gebruiker.

Terwijl de gebruiker zich `verification_uri`aanmeldt, moet de client het `/token` eind punt voor het aangevraagde token pollen `device_code`met behulp van de.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parameter | Vereist | Description|
| -------- | -------- | ---------- |
| `grant_type` | Vereist | Moet`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Vereist | Moet overeenkomen `client_id` met het gebruik in de eerste aanvraag. |
| `device_code`| Vereist | De `device_code` geretourneerde in de autorisatie aanvraag voor het apparaat.  |

### <a name="expected-errors"></a>Verwachte fouten

De code stroom van het apparaat is een polling-protocol, zodat uw client fouten kan ontvangen voordat de verificatie van de gebruiker is voltooid.  

| Fout | Description | Client actie |
| ------ | ----------- | -------------|
| `authorization_pending` | De gebruiker heeft de verificatie nog niet voltooid, maar de stroom is niet geannuleerd. | Herhaal de aanvraag na minstens `interval` seconden. |
| `authorization_declined` | De eind gebruiker heeft de autorisatie aanvraag geweigerd.| Stop polling en herstel naar een niet-geverifieerde status.  |
| `bad_verification_code`| De `device_code` verzonden naar het `/token` eind punt is niet herkend. | Controleer of de client de juiste `device_code` in de aanvraag verzendt. |
| `expired_token` | Er zijn `expires_in` ten minste seconden verstreken en verificatie is niet meer mogelijk. `device_code` | Stop polling en terugkeren naar een niet-geverifieerde status. |

### <a name="successful-authentication-response"></a>Geslaagde verificatie reactie

Een geslaagd token antwoord ziet er als volgt uit:

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

| Parameter | Indeling | Description |
| --------- | ------ | ----------- |
| `token_type` | Tekenreeks| Altijd ' Bearer '. |
| `scope` | Door spaties gescheiden teken reeksen | Als er een toegangs token is geretourneerd, worden hier de scopes weer gegeven waarvoor het toegangs token geldig is. |
| `expires_in`| int | Aantal seconden voordat het opgenomen toegangs token geldig is. |
| `access_token`| Dekkende teken reeks | Uitgegeven voor de aangevraagde [bereiken](v2-permissions-and-consent.md) .  |
| `id_token`   | JWT | Uitgegeven als de oorspronkelijke `scope` para meter het `openid` bereik bevat.  |
| `refresh_token` | Dekkende teken reeks | Verleend als de oorspronkelijke `scope` para meter `offline_access`is opgenomen.  |

U kunt het vernieuwings token gebruiken om nieuwe toegangs tokens te verkrijgen en tokens te vernieuwen met behulp van dezelfde stroom die wordt beschreven in de [documentatie over de OAuth-code stroom](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
