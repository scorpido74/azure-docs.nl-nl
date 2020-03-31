---
title: Referentie van Microsoft Identity Platform ID-token | Microsoft Documenten
description: Meer informatie over het gebruik van id_tokens die worden uitgestoten door de eindpunten Azure AD v1.0 en Microsoft Identity Platform (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 1efd027edb85cabcfdc2a170771ef19182b5c9f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160947"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft identity platform ID-tokens

`id_tokens`worden verzonden naar de clienttoepassing als onderdeel van een [OpenID Connect-stroom.](v2-protocols-oidc.md) Ze kunnen worden verzonden langs de kant of in plaats van een access token, en worden gebruikt door de client om de gebruiker te verifiëren.

## <a name="using-the-id_token"></a>De id_token gebruiken

ID-tokens moeten worden gebruikt om te valideren dat een gebruiker is wie hij beweert te zijn en aanvullende nuttige informatie over hen te krijgen - het mag niet worden gebruikt voor autorisatie in plaats van een [toegangstoken.](access-tokens.md) De claims die het biedt kunnen worden gebruikt voor UX in uw toepassing, als sleutels in een database, en het verstrekken van toegang tot de clientapplicatie.  Bij het maken van `idp` sleutels voor een database, mag niet worden gebruikt omdat het messes up gast scenario's.  Keying moet worden `sub` gedaan op alleen (dat is altijd uniek), met `tid` gebruikt voor routing indien nodig.  Als u gegevens moet delen `oid` + `sub` + `tid` tussen services, werkt dit `oid`omdat meerdere services allemaal hetzelfde krijgen.

## <a name="claims-in-an-id_token"></a>Claims in een id_token

`id_tokens`voor een Microsoft-identiteit zijn [JWT's,](https://tools.ietf.org/html/rfc7519)wat betekent dat ze bestaan uit een header, payload, en handtekening gedeelte. U de koptekst en handtekening gebruiken om de authenticiteit van het token te verifiëren, terwijl de payload de informatie bevat over de gebruiker die door uw client wordt gevraagd. Behalve waar vermeld, alle claims die hier worden vermeld, worden weergegeven in zowel v1.0- als v2.0-tokens.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Bekijk dit v1.0-voorbeeldtoken in [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Bekijk dit v2.0-voorbeeldtoken in [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Header-claims

|Claim | Indeling | Beschrijving |
|-----|--------|-------------|
|`typ` | String - altijd "JWT" | Geeft aan dat het token een JWT is.|
|`alg` | Tekenreeks | Geeft het algoritme aan dat is gebruikt om het token te ondertekenen. Voorbeeld: "RS256" |
|`kid` | Tekenreeks | Duimafdruk voor de openbare sleutel die wordt gebruikt om dit token te ondertekenen. Uitgestoten in zowel v1.0 als `id_tokens`v2.0 . |
|`x5t` | Tekenreeks | Hetzelfde (in gebruik en `kid`waarde) als . Dit is echter een verouderde claim die alleen `id_tokens` in v1.0 wordt uitgestoten voor compatibiliteitsdoeleinden. |

### <a name="payload-claims"></a>Payload claims

Deze lijst toont de claims die in de meeste id_tokens standaard (behalve waar vermeld).  Uw app kan echter [optionele claims](active-directory-optional-claims.md) gebruiken om aanvullende claims aan te vragen in de id_token.  Deze kunnen variëren `groups` van de claim tot informatie over de naam van de gebruiker.

|Claim | Indeling | Beschrijving |
|-----|--------|-------------|
|`aud` |  Tekenreeks, een URI voor app-id's | Hiermee wordt de beoogde ontvanger van het token geïdentificeerd. In `id_tokens`is het publiek de toepassings-id van uw app die is toegewezen aan uw app in de Azure-portal. Uw app moet deze waarde valideren en het token afwijzen als de waarde niet overeenkomt. |
|`iss` |  String, een STS URI | Hiermee identificeert u de BEVEILIGINGStokenservice (STS) die het token construeert en retourneert, en de Azure AD-tenant waarin de gebruiker is geverifieerd. Als het token is uitgegeven door het v2.0-eindpunt, eindigt de URI in `/v2.0`.  De GUID die aangeeft dat de gebruiker een `9188040d-6c67-4c5b-b112-36a304b66dad`gebruiker van een Microsoft-account is. Uw app moet het GUID-gedeelte van de claim gebruiken om de set tenants te beperken die zich kunnen aanmelden bij de app, indien van toepassing. |
|`iat` |  int, een UNIX-tijdstempel | 'Uitgegeven bij' geeft aan wanneer de verificatie voor dit token heeft plaatsgevonden.  |
|`idp`|String, meestal een STS URI | Registreert de identiteitsprovider waarmee het onderwerp van het token is geverifieerd. Deze waarde is identiek aan de waarde van de claim van de uitgever, tenzij de gebruikersaccount niet in dezelfde tenant als de uitgever is - gasten, bijvoorbeeld. Als de claim niet aanwezig is, betekent `iss` dit dat de waarde van in plaats daarvan kan worden gebruikt.  Voor persoonlijke accounts die worden gebruikt in een organisatorische context (bijvoorbeeld `idp` een persoonlijk account dat is uitgenodigd voor een Azure `9188040d-6c67-4c5b-b112-36a304b66dad`AD-tenant), kan de claim 'live.com' of een STS URI zijn die de Microsoft-accounttenant bevat. |
|`nbf` |  int, een UNIX-tijdstempel | De "nbf" (niet eerder) claim identificeert de tijd vóór welke de JWT niet mag worden aanvaard voor verwerking.|
|`exp` |  int, een UNIX-tijdstempel | De "exp" (vervaldatum) claim identificeert de vervaldatum op of waarna de JWT NIET mag worden geaccepteerd voor verwerking.  Het is belangrijk op te merken dat een resource het token ook voor deze tijd kan afwijzen - als er bijvoorbeeld een wijziging in de verificatie vereist is of als een tokenintrekking is gedetecteerd. |
| `c_hash`| Tekenreeks |De codehash wordt alleen opgenomen in ID-tokens wanneer het ID-token is uitgegeven met een OAuth 2.0-autorisatiecode. Het kan worden gebruikt om de authenticiteit van een autorisatiecode te valideren. Zie de [OpenID Connect-specificatie](https://openid.net/specs/openid-connect-core-1_0.html)voor meer informatie over het uitvoeren van deze validatie. |
|`at_hash`| Tekenreeks |De access token hash is alleen opgenomen in ID-tokens wanneer het ID-token is uitgegeven met een OAuth 2.0-toegangstoken. Het kan worden gebruikt om de authenticiteit van een toegangstoken te valideren. Zie de [OpenID Connect-specificatie](https://openid.net/specs/openid-connect-core-1_0.html)voor meer informatie over het uitvoeren van deze validatie. |
|`aio` | Ondoorzichtige tekenreeks | Een interne claim die door Azure AD wordt gebruikt om gegevens op te nemen voor hergebruik van token. Moet worden genegeerd.|
|`preferred_username` | Tekenreeks | De primaire gebruikersnaam die de gebruiker vertegenwoordigt. Het kan een e-mailadres, telefoonnummer of een algemene gebruikersnaam zijn zonder een opgegeven indeling. De waarde ervan is veranderlijk en kan in de loop van de tijd veranderen. Aangezien deze waarde veranderlijk is, mag deze waarde niet worden gebruikt om autorisatiebeslissingen te nemen. De `profile` scope is vereist om deze claim te ontvangen.|
|`email` | Tekenreeks | De `email` claim is standaard aanwezig voor gastaccounts met een e-mailadres.  Uw app kan de e-mailclaim aanvragen voor beheerde gebruikers `email` (gebruikers van dezelfde tenant als de resource) met behulp van de [optionele claim.](active-directory-optional-claims.md)  Op het v2.0-eindpunt kan uw `email` app ook de OpenID Connect-scope aanvragen - u hoeft niet zowel de optionele claim als de scope aan te vragen om de claim te krijgen.  De e-mailclaim ondersteunt alleen adresseerbare e-mail van de profielgegevens van de gebruiker. |
|`name` | Tekenreeks | De `name` claim biedt een door de mens leesbare waarde die het onderwerp van het token identificeert. De waarde is niet gegarandeerd uniek, het is veranderlijk en is ontworpen om alleen te worden gebruikt voor weergavedoeleinden. De `profile` scope is vereist om deze claim te ontvangen. |
|`nonce`| Tekenreeks | De nonce komt overeen met de parameter die is opgenomen in het oorspronkelijke /geautoriseerde verzoek aan het IDP. Als het niet overeenkomt, moet uw toepassing het token afwijzen. |
|`oid` | Tekenreeks, een GUID | De onveranderlijke id voor een object in het Microsoft-identiteitssysteem, in dit geval, een gebruikersaccount. Deze ID identificeert de gebruiker op unieke wijze in verschillende toepassingen - twee `oid` verschillende toepassingen die dezelfde gebruiker aanmelden, ontvangen dezelfde waarde in de claim. De Microsoft Graph retourneert `id` deze id als eigenschap voor een bepaald gebruikersaccount. Omdat `oid` het meerdere apps toestaat `profile` om gebruikers te correleren, is het bereik vereist om deze claim te ontvangen. Als er één gebruiker in meerdere tenants bestaat, bevat de gebruiker in elke tenant een andere object-id- deze wordt beschouwd als verschillende accounts, ook al logt de gebruiker in op elk account met dezelfde referenties. De `oid` claim is een GUID en kan niet opnieuw worden gebruikt. |
|`roles`| Matrix van tekenreeksen | De set rollen die zijn toegewezen aan de gebruiker die zich aanmeldt. |
|`rh` | Ondoorzichtige tekenreeks |Een interne claim die door Azure wordt gebruikt om tokens opnieuw te valideren. Moet worden genegeerd. |
|`sub` | Tekenreeks, een GUID | De principal waarover het token informatie bevestigt, zoals de gebruiker van een app. Deze waarde is onveranderlijk en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt. Het onderwerp is een pairwise identifier - het is uniek voor een bepaalde applicatie-ID. Als een enkele gebruiker zich aanmeldt bij twee verschillende apps met behulp van twee verschillende client-id's, ontvangen deze apps twee verschillende waarden voor de claim voor het onderwerp. Dit kan wel of niet gewenst zijn, afhankelijk van uw architectuur en privacyvereisten. |
|`tid` | Tekenreeks, een GUID | Een GUID die de Azure AD-tenant vertegenwoordigt waar de gebruiker vandaan komt. Voor werk- en schoolaccounts is de GUID de onveranderlijke tenant-id van de organisatie waartoe de gebruiker behoort. Voor persoonlijke accounts is `9188040d-6c67-4c5b-b112-36a304b66dad`de waarde . De `profile` scope is vereist om deze claim te ontvangen. |
|`unique_name` | Tekenreeks | Biedt een voor mensen leesbare waarde waarmee het onderwerp van het token wordt geïdentificeerd. Deze waarde is uniek op een bepaald moment, maar omdat e-mails en andere id's opnieuw kunnen worden gebruikt, kan deze waarde opnieuw worden weergegeven op andere accounts en mag daarom alleen worden gebruikt voor weergavedoeleinden. Alleen uitgegeven in v1.0 `id_tokens`. |
|`uti` | Ondoorzichtige tekenreeks | Een interne claim die door Azure wordt gebruikt om tokens opnieuw te valideren. Moet worden genegeerd. |
|`ver` | Tekenreeks, 1.0 of 2.0 | Geeft de versie van de id_token aan. |


> [!NOTE]
> De v1 en v2 id_token verschillen in de hoeveelheid informatie die zij zullen dragen, zoals blijkt uit de bovenstaande voorbeelden. De versie geeft in wezen het eindpunt van het Azure AD-platform op van waar het is uitgegeven. [Azure AD Oauth implementatie](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) zijn geëvolueerd door de jaren heen. Momenteel hebben we twee verschillende oAuth-eindpunten voor AzureAD-toepassingen. U gebruik maken van een van de nieuwe eindpunten die zijn gecategoriseerd als v2 of de oude die wordt gezegd dat v1. De Oauth eindpunten voor beiden zijn verschillend. Het V2-eindpunt is het nieuwere punt waarin we proberen om alle functies van v1-eindpunt te migreren en nieuwe ontwikkelaars aan te bevelen om het v2-eindpunt te gebruiken. 
> - V1: Azure Active Directory-eindpunten:`https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: Eindpunten van het Microsoft Identity Platform:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Een id_token valideren

Het valideren `id_token` van een is vergelijkbaar met de eerste stap van het valideren van [een toegangstoken](access-tokens.md#validating-tokens) - uw client moet valideren dat de juiste uitgever het token heeft teruggestuurd en dat er niet mee is geknoeid. Omdat `id_tokens` het altijd een JWT is, bestaan er veel bibliotheken om deze tokens te valideren - we raden u aan een van deze te gebruiken in plaats van het zelf te doen.

Als u het token handmatig wilt valideren, raadpleegt u de stappendetails bij [het valideren van een toegangstoken.](access-tokens.md#validating-tokens) Nadat u de handtekening op het token hebt gevalideerd, moeten de volgende claims worden gevalideerd in de id_token (deze kunnen ook worden gedaan door uw tokenvalidatiebibliotheek):

* Tijdstempels: `iat`de `nbf`, `exp` , en tijdstempels moeten allemaal vallen voor of na de huidige tijd, indien van toepassing. 
* Doelgroep: `aud` de claim moet overeenkomen met de app-id voor uw toepassing.
* Nonce: `nonce` de claim in de payload moet overeenkomen met de nonce parameter die tijdens de eerste aanvraag wordt doorgegeven aan het /authorize-eindpunt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [toegangstokens](access-tokens.md)
* Pas de claims in uw id_token aan met behulp van [optionele claims.](active-directory-optional-claims.md)
