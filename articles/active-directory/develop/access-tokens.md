---
title: Referentie voor toegangstokens voor Microsoft-identiteitsplatform | Azure
description: Meer informatie over toegangstokens die worden uitgestoten door de eindpunten Azure AD v1.0 en Microsoft Identity Platform (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: ed583abc8f60f3d367bf75254807e3f28cd0f1c9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309716"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Toegangstokens voor microsoft-identiteitsplatform

Met Toegangstokens kunnen clients apis's die door Azure worden beschermd, veilig aanbellen. Toegangstokens voor microsoft-identiteitsplatform zijn [JWT's,](https://tools.ietf.org/html/rfc7519)Base64-gecodeerde JSON-objecten die zijn ondertekend door Azure. Clients moeten toegangstokens behandelen als ondoorzichtige tekenreeksen, omdat de inhoud van het token alleen voor de bron is bedoeld. Voor validatie- en foutopsporingsdoeleinden kunnen ontwikkelaars JWT's decoderen met behulp van een site zoals [jwt.ms.](https://jwt.ms) Uw client kan een toegangstoken krijgen van het v1.0-eindpunt of het v2.0-eindpunt met behulp van verschillende protocollen.

Wanneer uw client een toegangstoken aanvraagt, retourneert Azure AD ook enkele metagegevens over het toegangstoken voor het verbruik van uw app. Deze informatie bevat de vervaldatum van het toegangstoken en de scopes waarvoor het geldig is. Met deze gegevens kan uw app intelligente caching van toegangstokens doen zonder het toegangstoken zelf te hoeven ontken.

Als uw toepassing een bron (web-API) is waartoe clients toegang kunnen vragen, bieden toegangstokens nuttige informatie voor gebruik in verificatie en autorisatie, zoals de gebruiker, client, uitgever, machtigingen en meer.

Zie de volgende secties voor meer informatie over hoe een resource de claims in een toegangstoken kan valideren en gebruiken.

> [!IMPORTANT]
> Toegangstokens worden gemaakt op basis van het *publiek* van het token, wat betekent dat de toepassing de scopes in het token bezit.  Dit is hoe `accessTokenAcceptedVersion` een resourceinstelling `2` in het [app-manifest](reference-app-manifest.md#manifest-reference) een client die het v1.0-eindpunt aanroept, een v2.0-toegangstoken kan ontvangen.  Op dezelfde manier verandert het wijzigen van de [optionele claim](active-directory-optional-claims.md) voor toegangstoken voor uw `user.read`client het toegangstoken dat wordt ontvangen wanneer een token wordt aangevraagd, dat eigendom is van de bron.
> Om dezelfde reden u tijdens het testen van uw clienttoepassing met een persoonlijk account (zoals hotmail.com of outlook.com) merken dat het toegangstoken dat door uw client wordt ontvangen, een ondoorzichtige tekenreeks is. Dit komt omdat de bron die wordt geopend, verouderde MSA-tickets (Microsoft-account) heeft aangevraagd die versleuteld zijn en niet door de client kunnen worden begrepen.

## <a name="sample-tokens"></a>Voorbeeldtokens

v1.0- en v2.0-tokens lijken op elkaar en bevatten veel van dezelfde claims. Een voorbeeld van elk is hier.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Bekijk dit v1.0-token in [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Bekijk dit v2.0-token in [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Claims in toegangstokens

JWT's zijn opgesplitst in drie stukken:

* **Koptekst** - Geeft informatie over het valideren van [het token,](#validating-tokens) inclusief informatie over het type token en hoe het is ondertekend.
* **Payload** - Bevat alle belangrijke gegevens over de gebruiker of app die probeert om uw service te bellen.
* **Handtekening** - Wordt de grondstof gebruikt om het token te valideren.

Elk stuk wordt gescheiden`.`door een periode ( ) en afzonderlijk Base64 gecodeerd.

Claims zijn alleen aanwezig als er een waarde bestaat om deze te vullen. Uw app mag dus niet afhankelijk zijn van een claim die aanwezig is. Voorbeelden hiervan `pwd_exp` zijn (niet elke tenant `family_name` vereist dat wachtwoorden verlopen) of (clientreferentie[(v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0)](v2-oauth2-client-creds-grant-flow.md)stromen zijn voor rekening van toepassingen, die geen namen hebben). Claims die worden gebruikt voor validatie van toegangstokens zullen altijd aanwezig zijn.

> [!NOTE]
> Sommige claims worden gebruikt om Azure AD te helpen bij het beveiligen van tokens in geval van hergebruik. Deze zijn gemarkeerd als niet voor openbare consumptie in de beschrijving als "Ondoorzichtig". Deze claims kunnen al dan niet worden weergegeven in een token en nieuwe claims kunnen zonder kennisgeving worden toegevoegd.

### <a name="header-claims"></a>Header-claims

|Claim | Indeling | Beschrijving |
|--------|--------|-------------|
| `typ` | String - altijd "JWT" | Geeft aan dat het token een JWT is.|
| `nonce` | Tekenreeks | Een unieke id die wordt gebruikt om te beschermen tegen token replay-aanvallen. Uw resource kan deze waarde registreren om te beschermen tegen herhalingen. |
| `alg` | Tekenreeks | Geeft het algoritme aan dat is gebruikt om het token te ondertekenen, bijvoorbeeld 'RS256'. |
| `kid` | Tekenreeks | Hiermee geeft u de duimafdruk op voor de openbare sleutel die wordt gebruikt om dit token te ondertekenen. Uitgezonden in zowel v1.0 als v2.0 access tokens. |
| `x5t` | Tekenreeks | Functioneert hetzelfde (in gebruik `kid`en waarde) als . `x5t`is een verouderde claim die alleen wordt uitgezonden in v1.0-toegangstokens voor compatibiliteitsdoeleinden. |

### <a name="payload-claims"></a>Payload claims

| Claim | Indeling | Beschrijving |
|-----|--------|-------------|
| `aud` | Tekenreeks, een URI voor app-id's | Hiermee wordt de beoogde ontvanger van het token geïdentificeerd. In ID-tokens is het publiek de toepassings-id van uw app, toegewezen aan uw app in de Azure-portal. Uw app moet deze waarde valideren en het token afwijzen als de waarde niet overeenkomt. |
| `iss` | String, een STS URI | Hiermee identificeert u de BEVEILIGINGStokenservice (STS) die het token construeert en retourneert, en de Azure AD-tenant waarin de gebruiker is geverifieerd. Als het uitgegeven token een v2.0-token is (zie de `ver` claim), eindigt de URI in `/v2.0`. De GUID die aangeeft dat de gebruiker een `9188040d-6c67-4c5b-b112-36a304b66dad`gebruiker van een Microsoft-account is. Uw app moet het GUID-gedeelte van de claim gebruiken om de set tenants te beperken die zich kunnen aanmelden bij de app, indien van toepassing. |
|`idp`| String, meestal een STS URI | Registreert de identiteitsprovider waarmee het onderwerp van het token is geverifieerd. Deze waarde is identiek aan de waarde van de claim van de uitgever, tenzij de gebruikersaccount niet in dezelfde tenant als de uitgever is - gasten, bijvoorbeeld. Als de claim niet aanwezig is, betekent `iss` dit dat de waarde van in plaats daarvan kan worden gebruikt.  Voor persoonlijke accounts die worden gebruikt in een organisatorische context (bijvoorbeeld `idp` een persoonlijk account dat is uitgenodigd voor een Azure `9188040d-6c67-4c5b-b112-36a304b66dad`AD-tenant), kan de claim 'live.com' of een STS URI zijn die de Microsoft-accounttenant bevat. |
| `iat` | int, een UNIX-tijdstempel | 'Uitgegeven bij' geeft aan wanneer de verificatie voor dit token heeft plaatsgevonden. |
| `nbf` | int, een UNIX-tijdstempel | De "nbf" (niet eerder) claim identificeert de tijd waarvoor de JWT niet voor verwerking mag worden aanvaard. |
| `exp` | int, een UNIX-tijdstempel | De claim "exp" (vervaldatum) identificeert de vervaldatum op of waarna de JWT niet mag worden geaccepteerd voor verwerking. Het is belangrijk op te merken dat een resource het token ook voor deze tijd kan afwijzen, bijvoorbeeld wanneer een wijziging in de verificatie vereist is of wanneer een tokenintrekking is gedetecteerd. |
| `aio` | Ondoorzichtige tekenreeks | Een interne claim die door Azure AD wordt gebruikt om gegevens op te nemen voor hergebruik van token. Resources mogen deze claim niet gebruiken. |
| `acr` | Tekenreeks, een "0" of "1" | Alleen aanwezig in v1.0 tokens. De claim 'Contextklasse verificatie'. Een waarde van "0" geeft aan dat de eindgebruikersverificatie niet voldeed aan de vereisten van ISO/IEC 29115. |
| `amr` | JSON-array van tekenreeksen | Alleen aanwezig in v1.0 tokens. Hiermee wordt aangegeven hoe het onderwerp van het token is geverifieerd. Zie [de amr claim sectie](#the-amr-claim) voor meer details. |
| `appid` | Tekenreeks, een GUID | Alleen aanwezig in v1.0 tokens. De toepassings-ID van de client met behulp van het token. De applicatie kan fungeren als zichzelf of namens een gebruiker. De toepassings-id vertegenwoordigt doorgaans een toepassingsobject, maar kan ook een hoofdobject van de service in Azure AD vertegenwoordigen. |
| `appidacr` | "0", "1" of "2" | Alleen aanwezig in v1.0 tokens. Geeft aan hoe de client is geverifieerd. Voor een openbare klant is de waarde "0". Als client-ID en clientgeheim worden gebruikt, is de waarde "1". Als een clientcertificaat is gebruikt voor verificatie, is de waarde '2'. |
| `azp` | Tekenreeks, een GUID | Alleen aanwezig in v2.0 tokens, een vervanging voor `appid`. De toepassings-ID van de client met behulp van het token. De applicatie kan fungeren als zichzelf of namens een gebruiker. De toepassings-id vertegenwoordigt doorgaans een toepassingsobject, maar kan ook een hoofdobject van de service in Azure AD vertegenwoordigen. |
| `azpacr` | "0", "1" of "2" | Alleen aanwezig in v2.0 tokens, een vervanging voor `appidacr`. Geeft aan hoe de client is geverifieerd. Voor een openbare klant is de waarde "0". Als client-ID en clientgeheim worden gebruikt, is de waarde "1". Als een clientcertificaat is gebruikt voor verificatie, is de waarde '2'. |
| `preferred_username` | Tekenreeks | De primaire gebruikersnaam die de gebruiker vertegenwoordigt. Het kan een e-mailadres, telefoonnummer of een algemene gebruikersnaam zijn zonder een opgegeven indeling. De waarde ervan is veranderlijk en kan in de loop van de tijd veranderen. Aangezien deze waarde veranderlijk is, mag deze waarde niet worden gebruikt om autorisatiebeslissingen te nemen.  Het kan worden gebruikt voor gebruikersnaam hints wel. De `profile` scope is vereist om deze claim te ontvangen. |
| `name` | Tekenreeks | Biedt een door de mens leesbare waarde die het onderwerp van het token identificeert. De waarde is niet gegarandeerd uniek, het is veranderlijk, en het is ontworpen om alleen te worden gebruikt voor weergavedoeleinden. De `profile` scope is vereist om deze claim te ontvangen. |
| `scp` | Tekenreeks, een lijst met gesplitste spatiemet scopes | De set scopes die door uw aanvraag worden blootgesteld waarvoor de clientapplicatie toestemming heeft gevraagd (en ontvangen). Uw app moet controleren of deze scopes geldig zijn en autorisatiebeslissingen nemen op basis van de waarde van deze scopes. Alleen inbegrepen voor [gebruikerstokens.](#user-and-application-tokens) |
| `roles` | Reeks tekenreeksen, een lijst met machtigingen | De set machtigingen die door uw toepassing worden weergegeven en die de aanvragende toepassing of gebruiker toestemming heeft gekregen om te bellen. Voor [toepassingstokens](#user-and-application-tokens)wordt dit gebruikt tijdens de clientreferentiestroom[(v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) in plaats van gebruikersscopes.  Voor [gebruikerstokens](#user-and-application-tokens) wordt dit gevuld met de rollen waaraan de gebruiker is toegewezen op de doeltoepassing. |
| `wids` | Array met [RoleTemplateID-GUIDs](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) | Hiermee wordt de tenantbrede rollen aandeze gebruiker aangeduid, in het gedeelte rollen dat aanwezig is op [de pagina beheerdersrollen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids).  Deze claim wordt per toepassing geconfigureerd via `groupMembershipClaims` de eigenschap van het [toepassingsmanifest.](reference-app-manifest.md)  Het instellen op 'Alles' of 'DirectoryRole' is vereist.  Kan niet aanwezig zijn in tokens verkregen via de impliciete stroom als gevolg van token lengte zorgen. |
| `groups` | JSON-array van GUIDs | Biedt object-id's die de groepslidmaatschappen van het onderwerp vertegenwoordigen. Deze waarden zijn uniek (zie Object-ID) en kunnen veilig worden gebruikt voor het beheren van toegang, zoals het afdwingen van autorisatie om toegang te krijgen tot een bron. De groepen die in de groepenclaim zijn opgenomen, worden `groupMembershipClaims` per toepassing geconfigureerd via de eigenschap van het [toepassingsmanifest.](reference-app-manifest.md) Een waarde van null sluit alle groepen uit, een waarde van 'SecurityGroup' bevat alleen Active Directory Security Group-lidmaatschappen en een waarde van 'Alles' omvat zowel beveiligingsgroepen als Office 365-distributielijsten. <br><br>Zie `hasgroups` de claim hieronder voor `groups` meer informatie over het gebruik van de claim met de impliciete subsidie. <br>Voor andere stromen, als het aantal groepen waarin de gebruiker zich bevindt, over een limiet gaat (150 voor SAML, 200 voor JWT), wordt een overschattingsclaim toegevoegd aan de claimbronnen die verwijzen naar het Microsoft Graph-eindpunt met de lijst met groepen voor de gebruiker. |
| `hasgroups` | Booleaans | Indien aanwezig, `true`altijd , het aangeven van de gebruiker is in ten minste een groep. Gebruikt in plaats `groups` van de claim voor JWT's in impliciete subsidiestromen als de volledige groepenclaim het URI-fragment zou uitbreiden tot buiten de URL-lengtelimieten (momenteel 6 of meer groepen). Geeft aan dat de client de Microsoft Graph API`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`moet gebruiken om de groepen van de gebruiker te bepalen ( ). |
| `groups:src1` | JSON-object | Voor tokenaanvragen die niet beperkt `hasgroups` zijn (zie hierboven), maar nog steeds te groot voor het token, wordt een koppeling naar de volledige groepenlijst voor de gebruiker opgenomen. Voor JWT's als gedistribueerde claim, voor SAML als `groups` een nieuwe vordering in plaats van de vordering. <br><br>**Voorbeeld JWT-waarde:** <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | Tekenreeks, een GUID | De principal waarover het token informatie bevestigt, zoals de gebruiker van een app. Deze waarde is onveranderlijk en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt. Het kan worden gebruikt om autorisatiecontroles veilig uit te voeren, bijvoorbeeld wanneer het token wordt gebruikt om toegang te krijgen tot een bron, en kan worden gebruikt als een sleutel in databasetabellen. Omdat het onderwerp altijd aanwezig is in de tokens die Azure AD uitgeeft, raden we u aan deze waarde te gebruiken in een autorisatiesysteem voor algemene doeleinden. Het onderwerp is echter een pairwise identifier - het is uniek voor een bepaalde applicatie-ID. Als een enkele gebruiker zich aanmeldt bij twee verschillende apps met twee verschillende client-id's, ontvangen deze apps daarom twee verschillende waarden voor de claim van het onderwerp. Dit kan al dan niet gewenst zijn, afhankelijk van uw architectuur en privacyvereisten. Zie ook `oid` de claim (die wel hetzelfde blijft voor apps binnen een tenant). |
| `oid` | Tekenreeks, een GUID | De onveranderlijke id voor een object in het Microsoft-identiteitsplatform, in dit geval een gebruikersaccount. Het kan ook worden gebruikt om autorisatiecontroles veilig uit te voeren en als sleutel in databasetabellen. Deze ID identificeert de gebruiker op unieke wijze in verschillende toepassingen - twee `oid` verschillende toepassingen die dezelfde gebruiker aanmelden, ontvangen dezelfde waarde in de claim. Zo `oid` kan worden gebruikt bij het maken van query's voor online services van Microsoft, zoals de Microsoft Graph. In de Microsoft Graph wordt `id` deze id als eigenschap voor een bepaald [gebruikersaccount teruggegeven.](/graph/api/resources/user) Omdat `oid` het meerdere apps toestaat `profile` om gebruikers te correleren, is het bereik vereist om deze claim te ontvangen. Houd er rekening mee dat als er één gebruiker in meerdere tenants bestaat, de gebruiker een andere object-id in elke tenant bevat - deze worden beschouwd als verschillende accounts, hoewel de gebruiker zich aanmeldt bij elk account met dezelfde referenties. |
| `tid` | Tekenreeks, een GUID | Vertegenwoordigt de Azure AD-tenant van waaruit de gebruiker afkomstig is. Voor werk- en schoolaccounts is de GUID de onveranderlijke tenant-id van de organisatie waartoe de gebruiker behoort. Voor persoonlijke accounts is `9188040d-6c67-4c5b-b112-36a304b66dad`de waarde . De `profile` scope is vereist om deze claim te ontvangen. |
| `unique_name` | Tekenreeks | Alleen aanwezig in v1.0 tokens. Biedt een voor mensen leesbare waarde waarmee het onderwerp van het token wordt geïdentificeerd. Deze waarde is niet gegarandeerd uniek binnen een tenant en mag alleen worden gebruikt voor weergavedoeleinden. |
| `uti` | Ondoorzichtige tekenreeks | Een interne claim die door Azure wordt gebruikt om tokens opnieuw te valideren. Resources mogen deze claim niet gebruiken. |
| `rh` | Ondoorzichtige tekenreeks | Een interne claim die door Azure wordt gebruikt om tokens opnieuw te valideren. Resources mogen deze claim niet gebruiken. |
| `ver` | Tekenreeks, `1.0` of`2.0` | Geeft de versie van het toegangstoken aan. |

**Groepen overage claim**

Azure AD beperkt het aantal object-id's dat het bevat in de groepsclaim om ervoor te zorgen dat de tokengrootte niet hoger is dan de limieten voor http-headers. Als een gebruiker lid is van meer groepen dan de overschrijdingslimiet (150 voor SAML-tokens, 200 voor JWT-tokens), zendt Azure AD de groepsclaim in het token niet uit. In plaats daarvan bevat het een overschrijdingsclaim in het token die aangeeft dat de toepassing de Microsoft Graph-API moet opvragen om het groepslidmaatschap van de gebruiker op te halen.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
 }
 ```

U `BulkCreateGroups.ps1` de meegeleverde in de map [Scripts voor app-bewerkingen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) gebruiken om overagescenario's te testen.

#### <a name="v10-basic-claims"></a>v1.0 basisclaims

De volgende claims worden opgenomen in v1.0-tokens indien van toepassing, maar zijn niet standaard opgenomen in v2.0-tokens. Als u v2.0 gebruikt en een van deze claims nodig hebt, vraagt u deze aan met behulp van [optionele claims.](active-directory-optional-claims.md)

| Claim | Indeling | Beschrijving |
|-----|--------|-------------|
| `ipaddr`| Tekenreeks | Het IP-adres waar de gebruiker van is geverifieerd. |
| `onprem_sid`| Tekenreeks, in [SID-indeling](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | In gevallen waarin de gebruiker een on-premises authenticatie heeft, biedt deze claim zijn SID. U kunt `onprem_sid` autorisatie gebruiken in oudere toepassingen.|
| `pwd_exp`| int, een UNIX-tijdstempel | Geeft aan wanneer het wachtwoord van de gebruiker verloopt. |
| `pwd_url`| Tekenreeks | Een URL waar gebruikers kunnen worden verzonden om hun wachtwoord opnieuw in te stellen. |
| `in_corp`| booleaans | Signalen als de client inlogt vanuit het bedrijfsnetwerk. Als dat niet het zo is, is de claim niet inbegrepen. |
| `nickname`| Tekenreeks | Een extra naam voor de gebruiker, los van voor- of achternaam.|
| `family_name` | Tekenreeks | Hiermee geeft u de achternaam, achternaam of familienaam van de gebruiker op, zoals gedefinieerd op het gebruikersobject. |
| `given_name` | Tekenreeks | Hiermee geeft u de eerste of de opgegeven naam van de gebruiker, zoals ingesteld op het gebruikersobject. |
| `upn` | Tekenreeks | De gebruikersnaam van de gebruiker. Kan een telefoonnummer, e-mailadres of niet-opgemaakte tekenreeks zijn. Mag alleen worden gebruikt voor weergavedoeleinden en het verstrekken van gebruikersnaam hints in herverificatie scenario's. |

#### <a name="the-amr-claim"></a>De `amr` claim

Microsoft-identiteiten kunnen op verschillende manieren worden geverifieerd, wat relevant kan zijn voor uw toepassing. De `amr` claim is een array die meerdere `["mfa", "rsa", "pwd"]`items kan bevatten, zoals voor een verificatie die zowel een wachtwoord als de Authenticator-app heeft gebruikt.

| Waarde | Beschrijving |
|-----|-------------|
| `pwd` | Wachtwoordverificatie, het Microsoft-wachtwoord van een gebruiker of het clientgeheim van een app. |
| `rsa` | Verificatie was gebaseerd op het bewijs van een RSA-sleutel, bijvoorbeeld met de [Microsoft Authenticator-app.](https://aka.ms/AA2kvvu) Dit geldt ook als verificatie is gedaan door een zelfondertekende JWT met een X509-certificaat dat eigendom is van een service. |
| `otp` | Eenmalige toegangscode met behulp van een e-mail of een sms-bericht. |
| `fed` | Er is een federatieve verificatiebewering (zoals JWT of SAML) gebruikt. |
| `wia` | Geïntegreerde Windows-verificatie |
| `mfa` | Er is gebruik gemaakt van multifactorauthenticatie. Wanneer dit aanwezig is, worden ook de andere authenticatiemethoden opgenomen. |
| `ngcmfa` | Gelijkwaardig `mfa`aan , gebruikt voor de inrichting van bepaalde geavanceerde referentietypen. |
| `wiaormfa`| De gebruiker gebruikte Windows of een MFA-referentie om te verifiëren. |
| `none` | Er is geen verificatie gedaan. |

## <a name="validating-tokens"></a>Valideren van tokens

Als u een id_token of een access_token wilt valideren, moet uw app zowel de handtekening van het token als de claims valideren. Om toegangstokens te valideren, moet uw app ook de uitgever, het publiek en de ondertekeningstokens valideren. Deze moeten worden gevalideerd op basis van de waarden in het Detectiedocument Van OpenID. De tenantonafhankelijke versie van het document bevindt zich bijvoorbeeld op [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration).

De Azure AD middleware heeft ingebouwde mogelijkheden voor het valideren van toegangstokens en u door onze [voorbeelden](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) bladeren om er een te vinden in de taal van uw keuze.

We bieden bibliotheken en codevoorbeelden die laten zien hoe u tokenvalidatie verwerken. De onderstaande informatie wordt verstrekt voor degenen die het onderliggende proces willen begrijpen. Er zijn ook verschillende open-source bibliotheken van derden beschikbaar voor JWT-validatie - er is ten minste één optie voor bijna elk platform en elke taal die er zijn. Zie [v1.0-verificatiebibliotheken](../azuread-dev/active-directory-authentication-libraries.md) en [v2.0-verificatiebibliotheken](reference-v2-libraries.md)voor meer informatie over Azure AD-verificatiebibliotheken en codevoorbeelden.

### <a name="validating-the-signature"></a>Het valideren van de handtekening

Een JWT bevat drie segmenten, `.` die worden gescheiden door het teken. Het eerste segment staat bekend als de **koptekst**, de tweede als de **body**, en het derde als de **handtekening**. Het handtekeningsegment kan worden gebruikt om de authenticiteit van het token te valideren, zodat het door uw app kan worden vertrouwd.

Tokens uitgegeven door Azure AD worden ondertekend met behulp van industriestandaard asymmetrische versleutelingsalgoritmen, zoals RS256. De koptekst van de JWT bevat informatie over de sleutel- en versleutelingsmethode die wordt gebruikt om het token te ondertekenen:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

De `alg` claim geeft het algoritme aan dat is `kid` gebruikt om het token te ondertekenen, terwijl de claim de specifieke openbare sleutel aangeeft die is gebruikt om het token te valideren.

Op een bepaald moment kan Azure AD een id_token ondertekenen met een van een bepaalde set publiek-private sleutelparen. Azure AD roteert de mogelijke set sleutels op een periodieke basis, dus uw app moet worden geschreven om deze belangrijke wijzigingen automatisch te verwerken. Een redelijke frequentie om te controleren op updates voor de openbare sleutels die door Azure AD worden gebruikt, is elke 24 uur.

U de ondertekeningssleutelgegevens verkrijgen die nodig zijn om de handtekening te valideren met behulp van het [metagegevensdocument van OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) op:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Probeer deze [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) in een browser!

Dit metagegevensdocument:

* Is een JSON-object met verschillende nuttige informatie, zoals de locatie van de verschillende eindpunten die nodig zijn voor het doen van OpenID Connect-verificatie.
* Inclusief `jwks_uri`een , die de locatie van de set van openbare sleutels gebruikt om tokens te ondertekenen geeft. De JSON Web Key (JWK) op de `jwks_uri` bevat alle openbare belangrijke informatie in gebruik op dat moment in de tijd.  Het JWK-formaat wordt beschreven in [RFC 7517](https://tools.ietf.org/html/rfc7517).  Uw app kan `kid` de claim in de JWT-header gebruiken om te selecteren welke openbare sleutel in dit document is gebruikt om een bepaald token te ondertekenen. Het kan vervolgens handtekening validatie doen met behulp van de juiste openbare sleutel en de aangegeven algoritme.

> [!NOTE]
> Het v1.0-eindpunt retourneert zowel het als `x5t` `kid` de claims, terwijl `kid` het v2.0-eindpunt reageert met alleen de claim. In de toekomst raden `kid` we u aan de claim te gebruiken om uw token te valideren.

Het uitvoeren van handtekeningvalidatie valt buiten het bereik van dit document - er zijn veel open source-bibliotheken beschikbaar om u te helpen dit te doen indien nodig.  Het Microsoft Identity-platform heeft echter één tokenondertekeningsextensie voor de standaarden : aangepaste ondertekeningssleutels.

Als uw app aangepaste ondertekeningssleutels heeft als gevolg van het gebruik van de functie [claimtoewijzing,](active-directory-claims-mapping.md) moet u een `appid` queryparameter toevoegen die de app-id bevat om een `jwks_uri` aanmelding te krijgen naar de ondertekeningssleutelgegevens van uw app, die moet worden gebruikt voor validatie. Bijvoorbeeld: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` bevat `jwks_uri` een `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`van .

### <a name="claims-based-authorization"></a>Autorisatie op basis van claims

De bedrijfslogica van uw toepassing zal deze stap dicteren, worden enkele algemene autorisatiemethoden hieronder uiteengezet.

* Controleer `scp` de `roles` of claim om te controleren of alle huidige scopes overeenkomen met de scopes die door uw API zijn blootgesteld, en laat de client de gevraagde actie uitvoeren.
* Zorg ervoor dat de aanroepende client `appid` uw API mag aanroepen met behulp van de claim.
* Valideer de verificatiestatus `appidacr` van de aanroepende client met behulp van - het zou geen 0 moeten zijn als openbare clients uw API niet mogen aanroepen.
* Controleer op een `nonce` lijst met eerdere claims om te controleren of het token niet wordt afgespeeld.
* Controleer of `tid` de overeenkomsten overeenkomen met een tenant die uw API mag aanroepen.
* Gebruik `acr` de claim om te controleren of de gebruiker MFA heeft uitgevoerd. Dit moet worden afgedwongen met behulp van [voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Als u de `roles` of `groups` claims in het toegangstoken hebt aangevraagd, controleert u of de gebruiker deel uitmaakt van de groep die deze actie mag uitvoeren.
  * Voor tokens die met de impliciete stroom worden opgehaald, moet u waarschijnlijk de Microsoft Graph voor deze gegevens [opvragen,](https://developer.microsoft.com/graph/) omdat deze vaak te groot is om in het token te passen.

## <a name="user-and-application-tokens"></a>Gebruikers- en toepassingstokens

Uw toepassing kan tokens ontvangen namens een gebruiker (de gebruikelijke stroom) of rechtstreeks vanuit een toepassing (via de clientreferentiestroom[(v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0).](v2-oauth2-client-creds-grant-flow.md) Deze app-only tokens geven aan dat dit gesprek afkomstig is van een toepassing en geen gebruiker heeft die deze ondersteunt. Deze tokens worden grotendeels hetzelfde behandeld, met enkele verschillen:

* App-only tokens hebben `scp` geen claim en kunnen `roles` in plaats daarvan een claim hebben. Dit is waar toepassingstoestemming (in tegenstelling tot gedelegeerde machtigingen) wordt geregistreerd. Zie toestemming en toestemming[(v1.0](../azuread-dev/v1-permissions-consent.md), [v2.0)](v2-permissions-and-consent.md)voor meer informatie over gedelegeerde en toepassingsmachtigingen.
* Veel mensspecifieke claims zullen ontbreken, `name` `upn`zoals of .
* De `sub` `oid` en vorderingen zullen hetzelfde zijn.

## <a name="token-revocation"></a>Tokenintrekking

Vernieuwingstokens kunnen om verschillende redenen op elk gewenst moment ongeldig of ingetrokken worden. Deze vallen in twee hoofdcategorieën: time-outs en intrekkingen.

### <a name="token-timeouts"></a>Time-outs van token

Met behulp van [token levensduur configuratie](active-directory-configurable-token-lifetimes.md), de levensduur van refresh tokens kunnen worden gewijzigd.  Het is normaal en verwacht dat sommige tokens zonder gebruik gaan (bijvoorbeeld de gebruiker opent de app niet voor 3 maanden) en vervalt daarom.  Apps ondervinden scenario's waarin de inlogserver een vernieuwingstoken afwijst vanwege zijn leeftijd. 

* MaxInactiveTime: Als het vernieuwingstoken niet is gebruikt binnen de tijd die wordt gedicteerd door de MaxInactiveTime, is het vernieuwingstoken niet langer geldig.
* MaxSessionAge: Als MaxAgeSessionMultiFactor of MaxAgeSessionSingleFactor zijn ingesteld op iets anders dan hun standaard (Tot ingetrokken), dan is herverificatie vereist nadat de tijd is ingesteld in de MaxAgeSession* verstreken.
* Voorbeelden:
  * De tenant heeft een MaxInactiveTime van vijf dagen en de gebruiker is een week op vakantie geweest, en daarom heeft Azure AD al 7 dagen geen nieuw tokenverzoek van de gebruiker gezien. De volgende keer dat de gebruiker een nieuw token aanvraagt, vindt hij dat zijn of haar vernieuwingstoken is ingetrokken en moeten ze hun referenties opnieuw invoeren.
  * Een gevoelige toepassing heeft een MaxAgeSessionSingleFactor van één dag. Als een gebruiker zich op maandag en op dinsdag (na 25 uur is verstreken) aanmeldt, moet hij zich opnieuw verifiëren.

### <a name="revocation"></a>Intrekking

Vernieuwingstokens kunnen door de server worden ingetrokken vanwege een wijziging in de referenties of vanwege gebruik of beheeractie.  Vernieuwingstokens vallen in twee klassen - die worden uitgegeven aan vertrouwelijke clients (de meest rechtse kolom) en die worden uitgegeven aan openbare klanten (alle andere kolommen).   

|   | Cookie op basis van wachtwoorden | Token op basis van wachtwoord | Cookie op basis van een cookie zonder wachtwoord | Token op basis van geen wachtwoord | Vertrouwelijk clienttoken |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Wachtwoord verloopt | Blijft in leven | Blijft in leven | Blijft in leven | Blijft in leven | Blijft in leven |
| Wachtwoord gewijzigd door gebruiker | Revoked | Revoked | Blijft in leven | Blijft in leven | Blijft in leven |
| Gebruiker doet SSPR | Revoked | Revoked | Blijft in leven | Blijft in leven | Blijft in leven |
| Beheerder stelt wachtwoord opnieuw in | Revoked | Revoked | Blijft in leven | Blijft in leven | Blijft in leven |
| Gebruiker trekt zijn vernieuwingstokens [in via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revoked | Revoked | Revoked | Revoked | Revoked |
| Beheerder trekt alle vernieuwingstokens in voor een gebruiker [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revoked | Revoked |Revoked | Revoked | Revoked |
| Enkele afmelding[(v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v2.0)](v2-protocols-oidc.md#single-sign-out) op het web | Revoked | Blijft in leven | Revoked | Blijft in leven | Blijft in leven |

> [!NOTE]
> Een "Non-password based" login is er een waar de gebruiker niet in een wachtwoord om het te krijgen. Bijvoorbeeld het gebruik van uw gezicht met Windows Hello, een FIDO2-sleutel of een pincode.
>
> Primaire vernieuwingstokens (PRT) op Windows 10 worden gescheiden op basis van de referentie. Windows Hello en wachtwoord hebben bijvoorbeeld hun respectievelijke PWT's, geïsoleerd van elkaar. Wanneer een gebruiker zich aanmeldt met een Hello-referentie (pincode of biometrie) en vervolgens het wachtwoord wijzigt, wordt het eerder verkregen OP een wachtwoord gebaseerde PRT ingetrokken. Als u zich opnieuw aanmeldt met een wachtwoord, wordt de oude PRT ongeldig gemaakt en wordt een nieuwe aanvragen ingediend.
>
> Vernieuwingstokens worden niet ongeldig of ingetrokken wanneer ze worden gebruikt om een nieuw toegangstoken op te halen en token te vernieuwen.  Uw app moet de oude echter weggooien zodra deze wordt gebruikt en deze vervangen door het nieuwe token, omdat het nieuwe token een nieuwe vervaldatum heeft. 

## <a name="next-steps"></a>Volgende stappen

* Meer [ `id_tokens` ](id-tokens.md)informatie over in Azure AD .
* Meer informatie over toestemming en toestemming [(v1.0](../azuread-dev/v1-permissions-consent.md), [v2.0](v2-permissions-and-consent.md)).
