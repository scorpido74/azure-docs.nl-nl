---
title: Micro soft Identity platform ID-token referentie
description: Meer informatie over het gebruik van id_tokens die worden verzonden door de Azure AD v 1.0-en micro soft Identity platform (v 2.0)-eind punten.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 0d1a5ee3ae56e8b5c4886308624159853c52b52c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690183"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokens van micro soft Identity platform ID

`id_tokens`worden verzonden naar de client toepassing als onderdeel van een [OpenID Connect Connect](v2-protocols-oidc.md) -stroom. Ze kunnen worden verzonden aan de zijkant of in plaats van een toegangs token en worden door de client gebruikt om de gebruiker te verifiëren.

## <a name="using-the-id_token"></a>De id_token gebruiken

ID-tokens moeten worden gebruikt om te controleren of een gebruiker aan wie ze beweren te zijn en meer nuttige informatie hierover kan krijgen, moet worden gebruikt voor autorisatie in plaats van een [toegangs token](access-tokens.md). De claims die het biedt, kunnen worden gebruikt voor UX in uw toepassing, als sleutels in een Data Base en toegang bieden tot de client toepassing.  Wanneer u sleutels voor een Data Base `idp` maakt, moet u deze niet gebruiken, omdat deze gast scenario's ophoopt.  Keying moet worden uitgevoerd op `sub` zichzelf (dit is altijd uniek), met `tid` voor route ring, als dat nodig is.  Als u gegevens in verschillende services moet delen, `oid` + `sub` + `tid` werkt dit omdat meerdere services hetzelfde `oid`zijn.

## <a name="claims-in-an-id_token"></a>Claims in een id_token

`id_tokens`voor een micro soft-identiteit zijn [JWTs](https://tools.ietf.org/html/rfc7519), wat betekent dat ze bestaan uit een header, lading en hand tekening gedeelte. U kunt de header en hand tekening gebruiken om de authenticiteit van het token te controleren. de payload bevat de informatie over de gebruiker die door de client is aangevraagd. Tenzij anders vermeld, worden alle hier vermelde claims weer gegeven in de tokens v 1.0 en v 2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Bekijk dit v 1.0-voorbeeld token in [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Bekijk dit v 2.0-voorbeeld token in [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Header claims

|Claim | Indeling | Beschrijving |
|-----|--------|-------------|
|`typ` | Teken reeks-altijd "JWT" | Geeft aan dat het token een JWT-waarde is.|
|`alg` | Tekenreeks | Hiermee wordt het algoritme aangegeven dat is gebruikt om het token te ondertekenen. Voor beeld: "RS256" |
|`kid` | Tekenreeks | Vinger afdruk voor de open bare sleutel die wordt gebruikt om dit token te ondertekenen. Verzonden in zowel v 1.0 als v 2.0 `id_tokens`. |
|`x5t` | Tekenreeks | Hetzelfde (in gebruik en waarde) als `kid`. Dit is echter een verouderde claim die alleen is verzonden in `id_tokens` v 1.0 voor compatibiliteits doeleinden. |

### <a name="payload-claims"></a>Nettolading claims

In deze lijst worden de claims weer gegeven die standaard in de meeste id_tokens zijn (tenzij anders vermeld).  Uw app kan echter gebruikmaken van [optionele claims](active-directory-optional-claims.md) om aanvullende claims in de id_token aan te vragen.  Deze kunnen variëren van de `groups` claim tot informatie over de naam van de gebruiker.

|Claim | Indeling | Beschrijving |
|-----|--------|-------------|
|`aud` |  Teken reeks, een app-ID-URI | Identificeert de beoogde ontvanger van het token. In `id_tokens`is de doel groep de toepassings-id van uw app, toegewezen aan uw app in de Azure Portal. Uw app moet deze waarde valideren en het token afwijzen als de waarde niet overeenkomt. |
|`iss` |  Teken reeks, een STS-URI | Identificeert de Security Token Service (STS) die het token bouwt en retourneert en de Azure AD-Tenant waarin de gebruiker is geverifieerd. Als het token is uitgegeven door het v 2.0-eind punt, loopt de URI `/v2.0`af in.  De GUID waarmee wordt aangegeven dat de gebruiker een consumenten gebruiker van een Microsoft-account is `9188040d-6c67-4c5b-b112-36a304b66dad`. Uw app moet het GUID-gedeelte van de claim gebruiken om de set tenants te beperken die zich kunnen aanmelden bij de app, indien van toepassing. |
|`iat` |  int, een UNIX-time stamp | ' Uitgegeven op ' geeft aan wanneer de authenticatie voor dit token is opgetreden.  |
|`idp`|Teken reeks, meestal een STS-URI | Registreert de identiteitsprovider waarmee het onderwerp van het token is geverifieerd. Deze waarde is gelijk aan de waarde van de verlener-claim tenzij het gebruikers account zich niet in dezelfde Tenant bevindt als de verlener-gasten, bijvoorbeeld. Als de claim niet aanwezig is, betekent dit dat de waarde `iss` van kan worden gebruikt.  Voor persoonlijke accounts die worden gebruikt in een organisatie context (bijvoorbeeld een persoonlijk account dat is uitgenodigd voor een Azure AD-Tenant), `idp` is de claim mogelijk ' Live.com ' of een STS-URI met de `9188040d-6c67-4c5b-b112-36a304b66dad`Microsoft-account Tenant. |
|`nbf` |  int, een UNIX-time stamp | De claim ' NBF ' (niet vóór) identificeert de tijd waarna de JWT niet moet worden geaccepteerd voor verwerking.|
|`exp` |  int, een UNIX-time stamp | De claim ' exp ' (verval tijd) identificeert de verval tijd op of waarna de JWT niet moet worden geaccepteerd voor verwerking.  Het is belang rijk te weten dat een resource het token vóór deze tijd kan afwijzen, als er bijvoorbeeld een wijziging in de verificatie is vereist of als er een token is ingetrokken. |
| `c_hash`| Tekenreeks |De code-hash is alleen opgenomen in ID-tokens wanneer het ID-token wordt uitgegeven met een OAuth 2,0-autorisatie code. Het kan worden gebruikt om de authenticiteit van een autorisatie code te valideren. Zie [OpenID Connect Connect Specification](https://openid.net/specs/openid-connect-core-1_0.html)(Engelstalig) voor meer informatie over het uitvoeren van deze validatie. |
|`at_hash`| Tekenreeks |De hash van het toegangs token is alleen opgenomen in ID-tokens wanneer het ID-token wordt uitgegeven met een OAuth 2,0-toegangs token. Het kan worden gebruikt om de authenticiteit van een toegangs token te valideren. Zie [OpenID Connect Connect Specification](https://openid.net/specs/openid-connect-core-1_0.html)(Engelstalig) voor meer informatie over het uitvoeren van deze validatie. |
|`aio` | Dekkende teken reeks | Een interne claim die door Azure AD wordt gebruikt om gegevens te registreren voor het opnieuw gebruiken van tokens. Moet worden genegeerd.|
|`preferred_username` | Tekenreeks | De primaire gebruikers naam die de gebruiker vertegenwoordigt. Dit kan een e-mail adres, telefoon nummer of een algemene gebruikers naam zijn zonder een opgegeven indeling. De waarde is onveranderbaar en kan in de loop van de tijd veranderen. Omdat de waarde is ververanderbaar, mag deze niet worden gebruikt om autorisatie beslissingen te nemen. Het `profile` bereik is vereist om deze claim te ontvangen.|
|`email` | Tekenreeks | De `email` claim is standaard aanwezig voor gast accounts met een e-mail adres.  Uw app kan de e-mail claim voor beheerde gebruikers (die van dezelfde Tenant als de resource) aanvragen met `email` behulp van de [optionele claim](active-directory-optional-claims.md).  Op het v 2.0-eind punt kan uw app ook aanvragen `email` voor het OpenID Connect-verbindings bereik. u hoeft niet zowel de optionele claim als de scope op te vragen om de claim op te halen.  De e-mail claim ondersteunt alleen adresseer bare e-mail van de profiel gegevens van de gebruiker. |
|`name` | Tekenreeks | De `name` claim levert een lees bare waarde die het onderwerp van het token aanduidt. De waarde is niet gegarandeerd uniek, is onveranderbaar en is ontworpen om alleen te worden gebruikt voor weergave doeleinden. Het `profile` bereik is vereist om deze claim te ontvangen. |
|`nonce`| Tekenreeks | De nonce komt overeen met de para meter die is opgenomen in de oorspronkelijke/authorize-aanvraag voor de IDP. Als deze niet overeenkomt, moet uw toepassing het token afwijzen. |
|`oid` | Teken reeks, een GUID | De onveranderbare id voor een object in het micro soft-identiteits systeem, in dit geval een gebruikers account. Met deze ID wordt de gebruiker op unieke wijze in verschillende toepassingen geïdentificeerd: twee verschillende toepassingen die in dezelfde gebruiker worden ondertekend, ontvangen `oid` dezelfde waarde in de claim. De Microsoft Graph retourneert deze ID als de `id` eigenschap voor een bepaald gebruikers account. Omdat meerdere `oid` apps toestaan gebruikers te correleren, is het `profile` bereik vereist om deze claim te ontvangen. Houd er rekening mee dat als één gebruiker bestaat in meerdere tenants, de gebruiker een andere object-ID in elke Tenant bevat. deze worden beschouwd als verschillende accounts, zelfs als de gebruiker zich aanmeldt bij elke account met dezelfde referenties. De `oid` claim is een GUID en kan niet opnieuw worden gebruikt. |
|`roles`| Matrix van tekenreeksen | De set met rollen die zijn toegewezen aan de gebruiker die zich aanmeldt. |
|`rh` | Dekkende teken reeks |Een interne claim die door Azure wordt gebruikt om tokens opnieuw te valideren. Moet worden genegeerd. |
|`sub` | Teken reeks, een GUID | De principal over welke het token informatie bedient, zoals de gebruiker van een app. Deze waarde is onveranderbaar en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt. Het onderwerp is een Pairwise id en is uniek voor een bepaalde toepassings-ID. Als één gebruiker zich bij twee verschillende apps aanmeldt met twee verschillende client-Id's, ontvangen deze apps twee verschillende waarden voor de claim van de certificaat houder. Dit kan al dan niet gewenst zijn, afhankelijk van uw architectuur en privacy-vereisten. |
|`tid` | Teken reeks, een GUID | Een GUID die de Azure AD-Tenant vertegenwoordigt waaruit de gebruiker zich bevindt. Voor werk-en school accounts is de GUID de onveranderlijke Tenant-ID van de organisatie waartoe de gebruiker behoort. De waarde is `9188040d-6c67-4c5b-b112-36a304b66dad`voor persoonlijke accounts. Het `profile` bereik is vereist om deze claim te ontvangen. |
|`unique_name` | Tekenreeks | Biedt een voor mensen leesbare waarde waarmee het onderwerp van het token wordt geïdentificeerd. Deze waarde is uniek op een bepaald moment, maar als e-mail berichten en andere id's opnieuw kunnen worden gebruikt, kan deze waarde opnieuw worden weer gegeven op andere accounts en moet daarom alleen worden gebruikt voor weergave doeleinden. Alleen uitgegeven in v 1.0 `id_tokens`. |
|`uti` | Dekkende teken reeks | Een interne claim die door Azure wordt gebruikt om tokens opnieuw te valideren. Moet worden genegeerd. |
|`ver` | Teken reeks, ofwel 1,0 of 2,0 | Hiermee wordt de versie van de id_token. |


> [!NOTE]
> De id_token v1 en v2 hebben verschillen in de hoeveelheid gegevens die ze in de bovenstaande voor beelden kunnen verwerken. Met de versie in wezen wordt het Azure AD-platform eindpunt opgegeven vanaf waar het is uitgegeven. [Azure AD OAuth-implementatie](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) is door de jaren ontwikkeld. Momenteel hebben we twee verschillende oAuth-eind punten voor AzureAD-toepassingen. U kunt elk van de nieuwe eind punten die zijn gecategoriseerd als v2 of de oude, gebruiken als v1. De OAuth-eind punten voor beide verschillen. Het v2-eind punt is de nieuwere versie van alle functies van v1-eind punt en raadt nieuwe ontwikkel aars aan om het v2-eind punt te gebruiken. 
> - V1: Azure Active Directory eind punten:`https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: micro soft Identity platform-eind punten:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Een id_token valideren

Het valideren van `id_token` een is vergelijkbaar met de eerste stap bij het [valideren van een toegangs token](access-tokens.md#validating-tokens) . uw client moet controleren of de juiste uitgever het token terugstuurde en dat er niet mee is geknoeid. Omdat `id_tokens` er altijd een JWT is, zijn er veel bibliotheken voor het valideren van deze tokens. u wordt aangeraden een van deze te gebruiken in plaats van dit zelf te doen.

Als u het token hand matig wilt valideren, raadpleegt u de stappen voor het [valideren van een toegangs token](access-tokens.md#validating-tokens). Nadat de hand tekening op het token is gevalideerd, moeten de volgende claims worden gevalideerd in de id_token (dit kan ook worden gedaan door de bibliotheek voor token validatie):

* Tijds tempels: de `iat`, `nbf`en `exp` de tijds tempels moeten alle vallen vóór of na de huidige tijd, indien van toepassing. 
* Doel groep: `aud` de claim moet overeenkomen met de app-id voor uw toepassing.
* Nonce: de `nonce` claim in de payload moet overeenkomen met de nonce-para meter die is door gegeven aan het/authorize-eind punt tijdens de eerste aanvraag.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [toegangs tokens](access-tokens.md)
* Pas de claims in uw id_token aan met behulp van [optionele claims](active-directory-optional-claims.md).
