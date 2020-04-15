---
title: Aanmelden met toestemming voor wachtwoordreferenties voor resources | Azure
titleSuffix: Microsoft identity platform
description: Ondersteuning browser-less authenticatie stromen met behulp van de resource owner password credential (ROPC) subsidie.
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
ms.openlocfilehash: 828bdab26684b29d664ea42d0b36f475c7872a80
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309453"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft-identiteitsplatform en Wachtwoordreferenties voor oAuth 2.0-bronnen

Microsoft identity platform ondersteunt de [OAuth 2.0 Resource Owner Password Credentials (ROPC) grant,](https://tools.ietf.org/html/rfc6749#section-4.3)waarmee een toepassing zich kan aanmelden bij de gebruiker door hun wachtwoord rechtstreeks te verwerken.  In dit artikel wordt beschreven hoe u rechtstreeks programmeren tegen het protocol in uw toepassing.  Waar mogelijk raden we u aan de ondersteunde Microsoft Authentication Libraries (MSAL) te gebruiken om tokens te [verkrijgen en beveiligde web-API's te bellen.](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  Kijk ook eens naar de [voorbeeld-apps die MSAL gebruiken.](sample-v2-code.md)

> [!WARNING]
> Microsoft raadt u aan de ROPC-stroom _niet_ te gebruiken. In de meeste scenario's zijn veiligere alternatieven beschikbaar en aanbevolen. Deze stroom vereist een zeer hoge mate van vertrouwen in de toepassing, en brengt risico's met zich mee die niet aanwezig zijn in andere stromen. U moet deze stroom alleen gebruiken als andere veiligere stromen niet kunnen worden gebruikt.

> [!IMPORTANT]
>
> * Het eindpunt van het Microsoft-identiteitsplatform ondersteunt alleen ROPC voor Azure AD-tenants, niet voor persoonlijke accounts. Dit betekent dat u een tenantspecifiek`https://login.microsoftonline.com/{TenantId_or_Name}`eindpunt () of het `organizations` eindpunt moet gebruiken.
> * Persoonlijke accounts die zijn uitgenodigd voor een Azure AD-tenant, kunnen ROPC niet gebruiken.
> * Accounts die geen wachtwoord hebben, kunnen zich niet aanmelden via ROPC. Voor dit scenario raden we u aan in plaats daarvan een andere stroom voor uw app te gebruiken.
> * Als gebruikers multi-factor authenticatie (MFA) moeten gebruiken om zich aan te melden bij de toepassing, worden ze in plaats daarvan geblokkeerd.
> * ROPC wordt niet ondersteund in scenario's voor [hybride identiteitsfederatie](/azure/active-directory/hybrid/whatis-fed) (bijvoorbeeld Azure AD en ADFS worden gebruikt om on-premises accounts te verifiëren). Als gebruikers op grote pagina worden doorgestuurd naar een on-premises identiteitsprovider, kan Azure AD de gebruikersnaam en het wachtwoord niet testen op die identiteitsprovider. [Pass-through authenticatie](/azure/active-directory/hybrid/how-to-connect-pta) wordt ondersteund met ROPC, echter.

## <a name="protocol-diagram"></a>Protocoldiagram

In het volgende diagram ziet u de ROPC-stroom.

![Diagram met de wachtwoordreferentiestroom van de broneigenaar](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Vergunningsaanvraag

De ROPC-stroom is één verzoek: het stuurt de klantidentificatie en de referenties van de gebruiker naar de IDP en ontvangt er vervolgens tokens voor terug. De klant moet daarvoor het e-mailadres (UPN) en het wachtwoord van de gebruiker opvragen. Onmiddellijk na een succesvol verzoek moet de client de referenties van de gebruiker veilig uit het geheugen vrijgeven. Het mag ze nooit redden.

> [!TIP]
> Probeer dit verzoek uit te voeren in Postman!
> [![Probeer dit verzoek uit te voeren in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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
| `tenant` | Vereist | De directorytenant waarop u de gebruiker wilt aanmelden. Dit kan in GUID of vriendelijke naam formaat. Deze parameter kan niet `common` worden `consumers`ingesteld op of `organizations`, maar kan worden ingesteld op . |
| `client_id` | Vereist | De toepassings-id (client) die de [Pagina Azure-portal - App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) aan uw app heeft toegewezen. | 
| `grant_type` | Vereist | Moet ingesteld `password`zijn op. |
| `username` | Vereist | Het e-mailadres van de gebruiker. |
| `password` | Vereist | Het wachtwoord van de gebruiker. |
| `scope` | Aanbevolen | Een lijst met [scopes](v2-permissions-and-consent.md)of machtigingen die de app nodig heeft. In een interactieve stroom moet de beheerder of de gebruiker van tevoren instemmen met deze scopes. |
| `client_secret`| Soms vereist | Als uw app een openbare `client_secret` client `client_assertion` is, kan de of kan deze niet worden opgenomen.  Als de app een vertrouwelijke client is, moet deze worden opgenomen. | 
| `client_assertion` | Soms vereist | Een andere `client_secret`vorm van , gegenereerd met behulp van een certificaat.  Zie [certificaatreferenties](active-directory-certificate-credentials.md) voor meer informatie. | 

### <a name="successful-authentication-response"></a>Succesvolle verificatierespons

In het volgende voorbeeld wordt een succesvol tokenantwoord weergegeven:

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
| `token_type` | Tekenreeks | Altijd ingesteld `Bearer`op . |
| `scope` | Gescheiden tekenreeksen spatie | Als een toegangstoken is geretourneerd, geeft deze parameter de scopes weer waarvoor het toegangstoken geldig is. |
| `expires_in`| int | Aantal seconden waarvoor het meegeleverde toegangstoken geldig is. |
| `access_token`| Ondoorzichtige tekenreeks | Uitgegeven voor de [gevraagde scopes.](v2-permissions-and-consent.md) |
| `id_token` | JWT JWT | Uitgegeven als `scope` de oorspronkelijke `openid` parameter het bereik bevatte. |
| `refresh_token` | Ondoorzichtige tekenreeks | Uitgegeven als `scope` de `offline_access`oorspronkelijke parameter is opgenomen . |

U het vernieuwingstoken gebruiken om nieuwe toegangstokens te verkrijgen en tokens te vernieuwen met dezelfde stroom die wordt beschreven in de documentatie van de [OAuth-codestroom.](v2-oauth2-auth-code-flow.md#refresh-the-access-token)

### <a name="error-response"></a>Foutreactie

Als de gebruiker niet de juiste gebruikersnaam of het juiste wachtwoord heeft opgegeven of als de client de gevraagde toestemming niet heeft ontvangen, mislukt de verificatie.

| Fout | Beschrijving | Clientactie |
|------ | ----------- | -------------|
| `invalid_grant` | De verificatie is mislukt | De referenties waren onjuist of de client heeft geen toestemming voor de gevraagde scopes. Als de scopes niet worden `consent_required` toegekend, wordt er een fout geretourneerd. Als dit gebeurt, moet de client de gebruiker naar een interactieve prompt sturen met behulp van een webview of browser. |
| `invalid_request` | Het verzoek is onjuist geconstrueerd | Het subsidietype wordt niet ondersteund `/common` `/consumers` op de verificatiecontexten of verificatie.  Gebruik `/organizations` in plaats daarvan een tenant-id. |

## <a name="learn-more"></a>Meer informatie

* Probeer ROPC zelf uit met behulp van de [voorbeeldconsoletoepassing](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Als u wilt bepalen of u het v2.0-eindpunt moet gebruiken, leest u over [de beperkingen van het Microsoft-identiteitsplatform.](active-directory-v2-limitations.md)
