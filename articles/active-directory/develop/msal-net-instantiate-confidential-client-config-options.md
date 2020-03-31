---
title: Instantiate een vertrouwelijke client app (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het momentmaken van een vertrouwelijke clienttoepassing met configuratieopties met behulp van de Microsoft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a520c5a1002e401f880fba84f8fc02a0a678133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084733"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Instantiate een vertrouwelijke clientapplicatie met configuratie-opties met behulp van MSAL.NET

In dit artikel wordt beschreven hoe u een [vertrouwelijke clienttoepassing](msal-client-applications.md) instantiëren met Microsoft Authentication Library voor .NET (MSAL.NET).  De toepassing wordt geinstantieerd met configuratieopties die zijn gedefinieerd in een instellingenbestand.

Voordat u een toepassing initialiseert, moet u deze eerst [registreren,](quickstart-register-app.md) zodat uw app kan worden geïntegreerd met het Microsoft-identiteitsplatform. Na registratie hebt u mogelijk de volgende informatie nodig (die te vinden is in de Azure-portal):

- De client-id (een tekenreeks die een GUID vertegenwoordigt)
- De URL van de identiteitsprovider (de naam van de instantie) en de aanmeldingsdoelgroep voor uw toepassing. Deze twee parameters staan gezamenlijk bekend als de autoriteit.
- De tenant-id als u een regel van zakelijke toepassing uitsluitend voor uw organisatie (ook wel single-tenant applicatie) schrijft.
- Het toepassingsgeheim (clientsecret string) of certificaat (van type X509Certificate2) als het een vertrouwelijke client-app is.
- Voor web-apps, en soms voor openbare client-apps (met name wanneer uw app een broker moet gebruiken), hebt u ook de redirectUri ingesteld waar de identiteitsprovider contact opneemt met uw toepassing met de beveiligingstokens.

## <a name="configure-the-application-from-the-config-file"></a>De toepassing configureren vanuit het config-bestand
De naam van de eigenschappen van de opties in `AzureADOptions` MSAL.NET overeenkomen met de naam van de eigenschappen van de in ASP.NET Core, zodat u geen lijmcode hoeft te schrijven.

Een ASP.NET Core-toepassingsconfiguratie wordt beschreven in een *appsettings.json-bestand:*

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Vanaf MSAL.NET v3.x u uw vertrouwelijke clienttoepassing configureren vanuit het config-bestand.

In de klasse waarin u uw toepassing wilt configureren en `ConfidentialClientApplicationOptions` instantiëren, moet u een object declareren.  Bind de configuratie die wordt gelezen uit de bron (inclusief het appconfig.json-bestand) aan de instantie van de toepassingsopties, met behulp van de `IConfigurationRoot.Bind()` methode van het [nuget-pakket Microsoft.Extensions.Configuration.Binder:](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Hierdoor kan de inhoud van het gedeelte 'AzureAD' van het *bestand appsettings.json* worden gekoppeld aan de bijbehorende eigenschappen van het `ConfidentialClientApplicationOptions` object.  Bouw vervolgens `ConfidentialClientApplication` een object:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Runtime-configuratie toevoegen
In een vertrouwelijke clienttoepassing hebt u meestal een cache per gebruiker. Daarom moet u de cache gekoppeld aan de gebruiker te krijgen en de applicatie bouwer dat u het wilt gebruiken. Op dezelfde manier u een dynamisch berekende omleiding URI hebben. In dit geval is de code de volgende:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

