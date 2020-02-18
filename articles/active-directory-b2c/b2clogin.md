---
title: Toepassingen en Api's migreren naar b2clogin.com
titleSuffix: Azure AD B2C
description: Meer informatie over het gebruik van b2clogin.com in de omleidings-Url's voor Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa1e6d8705cf4aed975ed0940087f243a06a9019
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2020
ms.locfileid: "77372690"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Omleidings-Url's instellen op b2clogin.com voor Azure Active Directory B2C

Wanneer u een id-provider voor registratie instelt en u zich aanmeldt in uw Azure Active Directory B2C-toepassing (Azure AD B2C), moet u een omleidings-URL opgeven. U moet niet langer verwijzen naar *login.microsoftonline.com* in uw toepassingen en api's. Gebruik in plaats daarvan *b2clogin.com* voor alle nieuwe toepassingen en migreer bestaande toepassingen van *login.microsoftonline.com* naar *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Afschaffing van login.microsoftonline.com

Op 04 december 2019 zijn de geplande login.microsoftonline.com-ondersteuning in Azure AD B2C op **04 december 2020**aangekondigd:

[Login.microsoftonline.com is reafschaffing van Azure Active Directory B2C](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

De afschaffing van login.microsoftonline.com gaat in van kracht voor alle Azure AD B2C tenants op 04 december 2020, waardoor bestaande tenants één (1) jaar kunnen worden gemigreerd naar b2clogin.com. Nieuwe tenants die zijn gemaakt na 04 december 2019, accepteren geen aanvragen van login.microsoftonline.com. Alle functionaliteit blijft hetzelfde op het b2clogin.com-eind punt.

De afschaffing van login.microsoftonline.com heeft geen invloed op Azure Active Directory tenants. Deze wijziging is alleen van invloed op Azure Active Directory B2C tenants.

## <a name="benefits-of-b2clogincom"></a>Voor delen van b2clogin.com

Wanneer u *b2clogin.com* als omleidings-URL gebruikt:

* De ruimte die wordt gebruikt in de cookie-header door micro soft-Services is beperkt.
* De omleidings-Url's hoeven niet langer een verwijzing naar micro soft te bevatten.
* Java script-client-side-code wordt ondersteund (momenteel in [Preview](user-flow-javascript-overview.md)) op aangepaste pagina's. Vanwege beveiligings beperkingen worden java script-code en HTML-formulier elementen verwijderd uit aangepaste pagina's als u *login.microsoftonline.com*gebruikt.

## <a name="overview-of-required-changes"></a>Overzicht van vereiste wijzigingen

Er zijn verschillende wijzigingen die u mogelijk moet maken om uw toepassingen te migreren naar *b2clogin.com*:

* Wijzig de omleidings-URL in de toepassingen van uw identiteits provider om naar *b2clogin.com*te verwijzen.
* Werk uw Azure AD B2C-toepassingen bij om *b2clogin.com* in hun gebruikers stroom en Token-eindpunt verwijzingen te gebruiken.
* Werk alle **toegestane oorsprongen** bij die u hebt gedefinieerd in de CORS-instellingen voor het aanpassen van de [gebruikers interface](custom-policy-ui-customization.md).

## <a name="change-identity-provider-redirect-urls"></a>Omleidings-Url's voor de ID-provider wijzigen

Wijzig op elke website van de identiteits provider waar u een toepassing hebt gemaakt, alle vertrouwde Url's om om te leiden naar `your-tenant-name.b2clogin.com` in plaats van *login.microsoftonline.com*.

Er zijn twee indelingen die u kunt gebruiken voor uw b2clogin.com-omleidings-Url's. De eerste biedt het voor deel dat ' micro soft ' nergens in de URL wordt weer gegeven met behulp van de Tenant-ID (een GUID) in plaats van de domein naam van de Tenant:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

De tweede optie maakt gebruik van de domein naam van uw Tenant in de vorm van `your-tenant-name.onmicrosoft.com`. Bijvoorbeeld:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Voor beide indelingen:

* Vervang `{your-tenant-name}` door de naam van uw Azure AD B2C Tenant.
* Verwijder `/te` als deze bestaat in de URL.

## <a name="update-your-applications-and-apis"></a>Uw toepassingen en Api's bijwerken

De code in uw Azure AD B2C toepassingen en Api's kunnen naar `login.microsoftonline.com` op verschillende locaties verwijzen. Uw code kan bijvoorbeeld verwijzingen bevatten naar gebruikers stromen en Token-eind punten. Werk het volgende bij in plaats van naar `your-tenant-name.b2clogin.com`:

* Autorisatie-eind punt
* Token eindpunt
* Token Uitgever

Zo zou het CA-eind punt voor het aanmeldings-en aanmeldings beleid van Contoso nu het volgende zijn:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Voor informatie over het migreren van OWIN-webtoepassingen naar b2clogin.com, raadpleegt [u een op OWIN gebaseerde web-API migreren naar b2clogin.com](multiple-token-endpoints.md).

Zie de sectie [migreren naar b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) in [een Azure API Management-API beveiligen met Azure AD B2C](secure-api-management.md)voor meer informatie over het migreren van Azure API Management-api's die worden beveiligd door Azure AD B2C.

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>Eigenschap ValidateAuthority

Als u [MSAL.net][msal-dotnet] v2 of eerder gebruikt, stelt u de eigenschap **ValidateAuthority** in op `false` op client instantiëren om omleidingen naar *b2clogin.com*toe te staan. Deze instelling is niet vereist voor MSAL.NET v3 en hoger.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Als u [MSAL voor Java script][msal-js]gebruikt:

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

## <a name="next-steps"></a>Volgende stappen

Voor informatie over het migreren van OWIN-webtoepassingen naar b2clogin.com, raadpleegt [u een op OWIN gebaseerde web-API migreren naar b2clogin.com](multiple-token-endpoints.md).

Zie de sectie [migreren naar b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) in [een Azure API Management-API beveiligen met Azure AD B2C](secure-api-management.md)voor meer informatie over het migreren van Azure API Management-api's die worden beveiligd door Azure AD B2C.

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
