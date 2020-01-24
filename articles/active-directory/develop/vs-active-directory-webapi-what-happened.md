---
title: Wijzigingen in WebAPI-projecten bij het maken van verbinding met Azure AD
description: Beschrijft wat er gebeurt met uw WebAPI-project wanneer u verbinding maakt met Azure AD met behulp van Visual Studio
author: ghogen
manager: jillfra
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 9858fdb7da80fe03498392ea93bf3c83b8a7e4dd
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699866"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Wat is er gebeurd met mijn WebAPI-project (Visual Studio Azure Active Directory Connected service)

> [!div class="op_single_selector"]
> - [Aan de slag](vs-active-directory-webapi-getting-started.md)
> - [Wat is er gebeurd](vs-active-directory-webapi-what-happened.md)

In dit artikel worden de exacte wijzigingen aangegeven die zijn aangebracht in ASP.NET WebAPI, ASP.NET toepassing met één pagina en ASP.NET Azure API-projecten wanneer [u de Azure Active Directory verbonden service toevoegt met behulp van Visual Studio](vs-active-directory-add-connected-service.md). Is ook van toepassing op de Azure Mobile Service-projecten van ASP.NET in Visual Studio 2015.

Zie [aan](vs-active-directory-webapi-getting-started.md)de slag voor meer informatie over het werken met de verbonden service.

## <a name="added-references"></a>Verwijzingen toegevoegd

Van invloed op het project bestand *. NET references) en `packages.config` (NuGet references).

| Type | Referentie |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

Aanvullende verwijzingen als u de optie **Directory gegevens lezen** hebt geselecteerd:

| Type | Referentie |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (alleen Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Alleen Visual Studio 2015) |
| .NET; NuGet | Systeem. ruimtelijk |

De volgende verwijzingen worden verwijderd (ASP.NET 4 projecten, net als in Visual Studio 2015):

| Type | Referentie |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Wijzigingen in het project bestand

- Stel de eigenschap `IISExpressSSLPort` in op een specifiek getal.
- Stel de eigenschap `WebProject_DirectoryAccessLevelKey` in op 0 of 1 als u de optie **Directory gegevens lezen** hebt geselecteerd.
- Stel de eigenschap `IISUrl` in op `https://localhost:<port>/` waarbij `<port>` overeenkomt met de `IISExpressSSLPort` waarde.

## <a name="webconfig-or-appconfig-changes"></a>wijzigingen in web. config of app. config

- De volgende configuratie vermeldingen zijn toegevoegd:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Alleen Visual Studio 2017: u hebt ook de volgende vermelding toegevoegd onder `<appSettings>`'

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Er zijn `<dependentAssembly>` elementen toegevoegd onder het `<runtime><assemblyBinding>` knoop punt voor `System.IdentityModel.Tokens.Jwt`.

- Als u de optie **Directory gegevens lezen** hebt geselecteerd, hebt u de volgende configuratie vermelding toegevoegd onder `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Code wijzigingen en toevoegingen

- Het kenmerk `[Authorize]` is toegevoegd aan `Controllers/ValueController.cs` en andere bestaande controllers.

- Er is een opstart klasse voor verificatie toegevoegd, `App_Start/Startup.Auth.cs`, met een opstart logica voor Azure AD-verificatie, of deze dienovereenkomstig aanpassen. Als u de optie **Directory gegevens lezen** hebt geselecteerd, bevat dit bestand ook code voor het ontvangen van een OAuth-code en het uitwisselen van een toegangs token.

- (Visual Studio 2015 met alleen ASP.NET 4-app) `App_Start/IdentityConfig.cs` verwijderd en `Controllers/AccountController.cs`, `Models/IdentityModel.cs`en `Providers/ApplicationAuthProvider.cs`zijn toegevoegd.

- Er zijn `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) of `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) toegevoegd, met informatie die Visual Studio gebruikt om de toevoeging van de verbonden service bij te houden.

### <a name="file-backup-visual-studio-2015"></a>Back-up van bestanden (Visual Studio 2015)

Wanneer u de verbonden service toevoegt, maakt Visual Studio 2015 een back-up van gewijzigde en verwijderde bestanden. Alle betrokken bestanden worden opgeslagen in de map `Backup/AzureAD`. Visual Studio 2017 maakt geen back-ups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Wijzigingen in azure

- Er is een Azure AD-toepassing gemaakt in het domein dat u hebt geselecteerd bij het toevoegen van de verbonden service.
- De app is bijgewerkt met de machtiging voor het **lezen van Directory gegevens** als deze optie is geselecteerd.

Meer [informatie over Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Volgende stappen

- [Verificatie scenario's voor Azure Active Directory](authentication-scenarios.md)
- [Aanmelden met micro soft toevoegen aan een ASP.NET-Web-app](quickstart-v1-aspnet-webapp.md)
