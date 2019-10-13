---
title: Micro soft Identity-platform gebruiken om gebruikers aan te melden met behulp van de ROPC-toekenning (resource owner password Credential) | Azure
description: Ondersteuning voor browser-minder verificatie stromen met behulp van het wacht woord voor de toewijzing van de resource-eigenaar.
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
ms.date: 10/11/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fb475a5d88547cc5f39cb269cc1cbf72fcd25b3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72295393"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Referentie voor het micro soft Identity-platform en de OAuth 2,0 Resource owner-wacht woord

Het micro soft Identity-platform biedt ondersteuning voor de toekenning van de [ROPC-machtiging (Password owner Credential)](https://tools.ietf.org/html/rfc6749#section-4.3), waarmee een toepassing zich kan aanmelden bij de gebruiker door rechtstreeks hun wacht woord te verwerken. Voor de ROPC-stroom is een hoge mate van vertrouwen en gebruikers belichting vereist. u moet deze stroom alleen gebruiken wanneer andere, veiligere stromen niet kunnen worden gebruikt.

> [!IMPORTANT]
>
> * Het micro soft Identity platform-eind punt ondersteunt alleen ROPC voor Azure AD-tenants, niet voor persoonlijke accounts. Dit betekent dat u een Tenant-specifiek eind punt (@no__t 0) of het `organizations`-eind punt moet gebruiken.
> * Persoonlijke accounts die worden uitgenodigd voor een Azure AD-Tenant, kunnen ROPC niet gebruiken.
> * Accounts die geen wacht woorden hebben, kunnen zich niet aanmelden via ROPC. Voor dit scenario raden we u aan om in plaats daarvan een andere stroom te gebruiken voor uw app.
> * Als gebruikers multi-factor Authentication (MFA) moeten gebruiken om zich aan te melden bij de toepassing, zullen ze in plaats daarvan worden geblokkeerd.
> * ROPC wordt niet ondersteund in [hybride identiteits Federatie](/azure/active-directory/hybrid/whatis-fed) scenario's (bijvoorbeeld Azure AD en ADFS gebruikt voor het verifiëren van on-premises accounts). Als gebruikers worden omgeleid naar een volledige pagina naar een on-premises ID-provider, kan Azure AD de gebruikers naam en het wacht woord niet testen voor die id-aanbieder. [Pass-Through-verificatie](/azure/active-directory/hybrid/how-to-connect-pta) wordt echter ondersteund met ROPC.

## <a name="protocol-diagram"></a>Protocol diagram

In het volgende diagram ziet u de ROPC-stroom.

![Diagram van de referentie stroom van het wacht woord voor de resource-eigenaar](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Autorisatie aanvraag

De ROPC-stroom is een enkele aanvraag: de client-id en de referenties van de gebruiker worden verzonden naar de IDP, waarna de tokens in retour worden ontvangen. De client moet het e-mail adres (UPN) en het wacht woord van de gebruiker aanvragen voordat dit wordt gedaan. Direct na een geslaagde aanvraag moet de client de referenties van de gebruiker veilig vrijgeven uit het geheugen. Ze mogen ze nooit opslaan.

> [!TIP]
> Probeer deze aanvraag uit te voeren in postman!
> [![Try het uitvoeren van deze aanvraag in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required. 

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Verplicht | De Directory-Tenant waarvan u de gebruiker wilt registreren. Dit kan een GUID of beschrijvende naam zijn. Deze para meter kan niet worden ingesteld op `common` of `consumers`, maar kan worden ingesteld op `organizations`. |
| `client_id` | Verplicht | De ID van de toepassing (client) die de [Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina die aan uw app is toegewezen. | 
| `grant_type` | Verplicht | Moet worden ingesteld op `password`. |
| `username` | Verplicht | Het e-mail adres van de gebruiker. |
| `password` | Verplicht | Het wacht woord van de gebruiker. |
| `scope` | Aanbevolen | Een door spaties gescheiden lijst met [bereiken](v2-permissions-and-consent.md)of machtigingen die voor de app zijn vereist. In een interactieve stroom moet de beheerder of de gebruiker vóór de tijd toestemming geven aan deze bereiken. |
| `client_secret`| Soms vereist | Als uw app een open bare client is, kan de `client_secret` of `client_assertion` niet worden opgenomen.  Als de app een vertrouwelijke client is, moet deze worden opgenomen. | 
| `client_assertion` | Soms vereist | Een andere vorm van `client_secret`, gegenereerd met een certificaat.  Zie [certificaat referenties](active-directory-certificate-credentials.md) voor meer informatie. | 

### <a name="successful-authentication-response"></a>Geslaagde verificatie reactie

In het volgende voor beeld ziet u een geslaagd token antwoord:

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
| `token_type` | Tekenreeks | Altijd ingesteld op `Bearer`. |
| `scope` | Door spaties gescheiden teken reeksen | Als er een toegangs token is geretourneerd, worden in deze para meter de scopes vermeld waarvoor het toegangs token geldig is. |
| `expires_in`| int | Aantal seconden dat het opgenomen toegangs token geldig is voor. |
| `access_token`| Dekkende teken reeks | Uitgegeven voor de aangevraagde [bereiken](v2-permissions-and-consent.md) . |
| `id_token` | JWT | Verleend als de oorspronkelijke para meter `scope` de `openid`-bereik bevat. |
| `refresh_token` | Dekkende teken reeks | Verleend als de oorspronkelijke para meter `scope` is opgenomen `offline_access`. |

U kunt het vernieuwings token gebruiken om nieuwe toegangs tokens te verkrijgen en tokens te vernieuwen met behulp van dezelfde stroom die wordt beschreven in de [documentatie over de OAuth-code stroom](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Fout bericht

Als de gebruiker geen juiste gebruikers naam of wacht woord heeft opgegeven, of als de client niet de aangevraagde toestemming heeft ontvangen, zal de verificatie mislukken.

| Fout | Beschrijving | Client actie |
|------ | ----------- | -------------|
| `invalid_grant` | De verificatie is mislukt | De referenties zijn onjuist of de client heeft geen toestemming voor de aangevraagde bereiken. Als de bereiken niet worden verleend, wordt een `consent_required`-fout geretourneerd. Als dit het geval is, moet de client de gebruiker naar een interactieve prompt verzenden met een webweergave of browser. |
| `invalid_request` | De aanvraag is onjuist samengesteld | Het toekennings type wordt niet ondersteund voor de `/common`-of `/consumers`-verificatie contexten.  Gebruik in plaats daarvan `/organizations` of een Tenant-ID. |

## <a name="learn-more"></a>Meer informatie

* Probeer ROPC voor uzelf uit met behulp van de [voorbeeld console toepassing](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Lees over de [beperkingen van micro soft Identity-platform](active-directory-v2-limitations.md)om te bepalen of u het v 2.0-eind punt moet gebruiken.
