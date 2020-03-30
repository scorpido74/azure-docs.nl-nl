---
title: Toepassingen en API's migreren naar b2clogin.com
titleSuffix: Azure AD B2C
description: Meer informatie over het gebruik van b2clogin.com in uw omleidings-URL's voor Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189987"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>URL's omleiden naar b2clogin.com voor Azure Active Directory B2C

Wanneer u een identiteitsprovider instelt voor aanmelding en aanmelding in uw Azure Active Directory B2C-toepassing (Azure AD B2C), moet u een omleidings-URL opgeven. U mag niet langer verwijzen naar *login.microsoftonline.com* in uw toepassingen en API's. Gebruik in plaats daarvan *b2clogin.com* voor alle nieuwe toepassingen en migreer bestaande toepassingen van *login.microsoftonline.com* naar *b2clogin.com.*

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Afschaffing van login.microsoftonline.com

Op 4 december 2019 hebben we aangekondigd dat login.microsoftonline.com ondersteuning in Azure AD B2C op **4 december 2020**wordt ingepland:

[Azure Active Directory B2C is login.microsoftonline.com deprecating](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

De afschaffing van login.microsoftonline.com treedt op 4 december 2020 in werking voor alle Azure AD B2C-tenants, waardoor bestaande tenants één (1) jaar kunnen migreren naar b2clogin.com. Nieuwe huurders die na 4 december 2019 zijn gemaakt, accepteren geen aanvragen van login.microsoftonline.com. Alle functionaliteit blijft hetzelfde op het b2clogin.com eindpunt.

De afschaffing van login.microsoftonline.com heeft geen invloed op Azure Active Directory-tenants. Alleen Azure Active Directory B2C-tenants worden door deze wijziging beïnvloed.

## <a name="benefits-of-b2clogincom"></a>Voordelen van b2clogin.com

Wanneer u *b2clogin.com* als omleidings-URL gebruikt:

* De ruimte die wordt verbruikt in de cookieheader van Microsoft-services wordt verminderd.
* Uw omleidings-URL's hoeven niet langer een verwijzing naar Microsoft op te nemen.
* JavaScript-clientcode wordt ondersteund (momenteel in [preview)](user-flow-javascript-overview.md)op aangepaste pagina's. Vanwege beveiligingsbeperkingen worden JavaScript-code en HTML-formulierelementen van aangepaste pagina's verwijderd als u *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Overzicht van vereiste wijzigingen

Er zijn verschillende wijzigingen die u mogelijk moet aanbrengen om uw toepassingen te migreren naar *b2clogin.com:*

* Wijzig de omleidings-URL in de toepassingen van uw identiteitsprovider om te verwijzen naar *b2clogin.com*.
* Werk uw Azure AD B2C-toepassingen bij om *b2clogin.com* te gebruiken in hun verwijzingen naar gebruikersstromen en tokeneindpunten.
* Werk alle **toegestane Origins** bij die u hebt gedefinieerd in de CORS-instellingen voor [het aanpassen van de gebruikersinterface.](custom-policy-ui-customization.md)

## <a name="change-identity-provider-redirect-urls"></a>URL's voor het omleiden van identiteitsprovider wijzigen

Wijzig op de website van elke identiteitsprovider waarin u een toepassing hebt `your-tenant-name.b2clogin.com` gemaakt alle vertrouwde URL's om naar te verwijzen in plaats van *login.microsoftonline.com.*

Er zijn twee indelingen die u gebruiken voor uw b2clogin.com URL's omleiden. De eerste biedt het voordeel dat "Microsoft" nergens in de URL wordt weergegeven met behulp van de tenant-id (een GUID) in plaats van uw tenantdomeinnaam:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

De tweede optie maakt gebruik van `your-tenant-name.onmicrosoft.com`uw tenant domeinnaam in de vorm van . Bijvoorbeeld:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Voor beide indelingen:

* Vervang `{your-tenant-name}` de naam van uw Azure AD B2C-tenant.
* Verwijderen `/te` als deze bestaat in de URL.

## <a name="update-your-applications-and-apis"></a>Uw toepassingen en API's bijwerken

De code in uw Azure AD B2C-toepassingen en `login.microsoftonline.com` API's kan op verschillende plaatsen worden weergegeven. Uw code kan bijvoorbeeld verwijzingen hebben naar gebruikersstromen en tokeneindpunten. Werk het volgende `your-tenant-name.b2clogin.com`bij om in plaats daarvan te verwijzen:

* Eindpunt voor autorisatie
* Tokeneindpunt
* Token-uitgever

Het eindpunt van de autoriteit voor het aanmeldings-/aanmeldingsbeleid van Contoso is nu bijvoorbeeld:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Zie [Een OWIN-gebaseerde web-API migreren naar b2clogin.com voor](multiple-token-endpoints.md)informatie over het migreren van OWIN-gebaseerde webtoepassingen naar b2clogin.com.

Zie de sectie [Migreren naar b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) van Secure an Azure API Management API's met Azure AD [B2C](secure-api-management.md)voor het migreren van Azure API-beheerAPI's.

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>ValidateAuthority, eigenschap

Als u [MSAL.NET][msal-dotnet] v2 of eerder gebruikt, stelt `false` u de eigenschap **ValidateAuthority** in op clientinstantiatie om omleidingen naar *b2clogin.com*mogelijk te maken. Deze instelling is niet vereist voor MSAL.NET v3 en hoger.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Als u [MSAL gebruikt voor JavaScript:][msal-js]

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

Zie [Een OWIN-gebaseerde web-API migreren naar b2clogin.com voor](multiple-token-endpoints.md)informatie over het migreren van OWIN-gebaseerde webtoepassingen naar b2clogin.com.

Zie de sectie [Migreren naar b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) van Secure an Azure API Management API's met Azure AD [B2C](secure-api-management.md)voor het migreren van Azure API-beheerAPI's.

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
