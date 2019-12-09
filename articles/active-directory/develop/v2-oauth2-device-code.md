---
title: Gebruikers aanmelden zonder browser | Azure
titleSuffix: Microsoft identity platform
description: Maak Inge sloten en browser-minder verificatie stromen met behulp van de autorisatie toekenning van het apparaat.
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
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e937955f0b122d3a878141655475f34b051622e7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919236"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Micro soft Identity platform en de OAuth 2,0-autorisatie voor het weigeren van apparaten

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Het micro soft-identiteits platform ondersteunt de [machtiging verlenen voor apparaten](https://tools.ietf.org/html/rfc8628), waarmee gebruikers zich kunnen aanmelden bij apparaten met invoer beperkingen, zoals een Smart TV, IOT-apparaat of printer.  Om deze stroom in te scha kelen, heeft de gebruiker van het apparaat een webpagina in de browser op een ander apparaat om u aan te melden.  Zodra de gebruiker zich aanmeldt, kan het apparaat toegangs tokens verkrijgen en tokens vernieuwen als dat nodig is.  

In dit artikel wordt beschreven hoe u direct kunt Program meren met het protocol in uw toepassing.  Als dat mogelijk is, kunt u het beste de ondersteunde micro soft-verificatie bibliotheken (MSAL) gebruiken in plaats van [tokens te verkrijgen en beveiligde web-api's](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)aan te roepen.  Bekijk ook de voor beeld- [apps die gebruikmaken van MSAL](sample-v2-code.md).

> [!NOTE]
> Het micro soft Identity platform-eind punt biedt geen ondersteuning voor alle Azure Active Directory-scenario's en-functies. Lees over [micro soft Identity platform-beperkingen](active-directory-v2-limitations.md)om te bepalen of u het micro soft Identity platform-eind punt moet gebruiken.

## <a name="protocol-diagram"></a>Protocol diagram

De gehele apparaatcode stroom ziet er ongeveer uit als in het volgende diagram. Verderop in dit artikel worden de stappen beschreven.

![Toestel code stroom](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Autorisatie aanvraag voor apparaat

De client moet eerst controleren met de verificatie server voor een apparaat en gebruikers code die wordt gebruikt om verificatie te initiëren. De client verzamelt deze aanvraag van het `/devicecode`-eind punt. In deze aanvraag moet de client ook de machtigingen bevatten die nodig zijn voor het verkrijgen van de gebruiker. Vanaf het moment dat deze aanvraag wordt verzonden, heeft de gebruiker slechts 15 minuten om zich aan te melden (de normale waarde voor `expires_in`), zodat deze aanvraag alleen als de gebruiker heeft aangegeven dat deze klaar zijn om zich aan te melden.

> [!TIP]
> Probeer deze aanvraag uit te voeren in postman!
> [![probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Verplicht | Kan/veelvoorkomende,/consumers of/organizations. zijn  Het kan ook de Directory Tenant zijn waarvoor u een machtiging wilt aanvragen in de indeling GUID of beschrijvende naam.  |
| `client_id` | Verplicht | De **client-id** van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen. |
| `scope` | Aanbevolen | Een lijst met door spaties gescheiden [bereiken](v2-permissions-and-consent.md) waarvan u wilt dat de gebruiker toestemming geeft.  |

### <a name="device-authorization-response"></a>Reactie van het apparaat autorisatie

Een geslaagde reactie is een JSON-object met de vereiste gegevens om de gebruiker in staat te stellen zich aan te melden.  

| Parameter | Indeling | Beschrijving |
| ---              | --- | --- |
|`device_code`     | Tekenreeks | Een lange teken reeks die wordt gebruikt om de sessie tussen de client en de autorisatie server te controleren. De client gebruikt deze para meter om het toegangs token van de autorisatie server aan te vragen. |
|`user_code`       | Tekenreeks | Een korte teken reeks die wordt weer gegeven aan de gebruiker die wordt gebruikt om de sessie op een secundair apparaat te identificeren.|
|`verification_uri`| URI | De URI waarnaar de gebruiker moet gaan met de `user_code` om zich aan te melden. |
|`expires_in`      | int | Het aantal seconden voordat de `device_code` en `user_code` verlopen. |
|`interval`        | int | Het aantal seconden dat de client moet wachten tussen polling aanvragen. |
| `message`        | Tekenreeks | Een teken reeks met lees bare tekst met instructies voor de gebruiker. Dit kan worden gelokaliseerd door een **query parameter** op te nemen in de aanvraag van de formulier `?mkt=xx-XX`en de juiste taal cultuur code in te vullen. |

> [!NOTE]
> Het veld `verification_uri_complete` antwoord wordt op dit moment niet opgenomen of ondersteund.  Dit wordt vermeld [omdat u ziet](https://tools.ietf.org/html/rfc8628) dat `verification_uri_complete` wordt weer gegeven als een optioneel onderdeel van de standaard voor de apparaatcode stroom.

## <a name="authenticating-the-user"></a>Verificatie van de gebruiker

Nadat de `user_code` en `verification_uri`zijn ontvangen, worden deze door de client weer gegeven aan de gebruiker, zodat ze zich kunnen aanmelden met hun mobiele telefoon of PC-browser.

Als de gebruiker zich verifieert met een persoonlijk account (op/veelvoorkomende of/consumers), wordt u gevraagd om u opnieuw aan te melden om de verificatie status naar het apparaat te verzenden.  Ze worden ook gevraagd toestemming te geven om ervoor te zorgen dat ze op de hoogte zijn van de machtigingen die worden verleend.  Dit is niet van toepassing op werk-of school accounts die worden gebruikt voor verificatie. 

Terwijl de gebruiker wordt geverifieerd op de `verification_uri`, moet de client het `/token`-eind punt voor het aangevraagde token pollen met behulp van de `device_code`.

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parameter | Verplicht | Beschrijving|
| -------- | -------- | ---------- |
| `tenant`  | Verplicht | Dezelfde Tenant of Tenant alias die in de eerste aanvraag wordt gebruikt. | 
| `grant_type` | Verplicht | Moet `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Verplicht | Moet overeenkomen met de `client_id` die in de eerste aanvraag wordt gebruikt. |
| `device_code`| Verplicht | De `device_code` geretourneerd in de autorisatie aanvraag voor het apparaat.  |

### <a name="expected-errors"></a>Verwachte fouten

De code stroom van het apparaat is een polling-protocol, zodat uw client fouten kan ontvangen voordat de verificatie van de gebruiker is voltooid.  

| Fout | Beschrijving | Client actie |
| ------ | ----------- | -------------|
| `authorization_pending` | De gebruiker heeft de verificatie nog niet voltooid, maar de stroom is niet geannuleerd. | Herhaal de aanvraag na minstens `interval` seconden. |
| `authorization_declined` | De eind gebruiker heeft de autorisatie aanvraag geweigerd.| Stop polling en herstel naar een niet-geverifieerde status.  |
| `bad_verification_code`| De `device_code` die naar het `/token`-eind punt wordt verzonden, wordt niet herkend. | Controleer of de client de juiste `device_code` in de aanvraag verzendt. |
| `expired_token` | Ten minste `expires_in` seconden zijn verstreken en verificatie is niet meer mogelijk met deze `device_code`. | Stop polling en terugkeren naar een niet-geverifieerde status. |   

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

| Parameter | Indeling | Beschrijving |
| --------- | ------ | ----------- |
| `token_type` | Tekenreeks| Altijd ' Bearer '. |
| `scope` | Door spaties gescheiden teken reeksen | Als er een toegangs token is geretourneerd, worden hier de scopes weer gegeven waarvoor het toegangs token geldig is. |
| `expires_in`| int | Aantal seconden voordat het opgenomen toegangs token geldig is. |
| `access_token`| Dekkende teken reeks | Uitgegeven voor de aangevraagde [bereiken](v2-permissions-and-consent.md) .  |
| `id_token`   | JWT | Verleend als de para meter voor de oorspronkelijke `scope` het `openid` bereik bevat.  |
| `refresh_token` | Dekkende teken reeks | Verleend als de oorspronkelijke `scope`-para meter `offline_access`is opgenomen.  |

U kunt het vernieuwings token gebruiken om nieuwe toegangs tokens te verkrijgen en tokens te vernieuwen met behulp van dezelfde stroom die wordt beschreven in de [documentatie over de OAuth-code stroom](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
