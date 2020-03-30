---
title: Wijzigingen in WebAPI-projecten bij verbinding met Azure AD
description: Beschrijft wat er met uw WebAPI-project gebeurt wanneer u verbinding maakt met Azure AD met Visual Studio
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
ms.openlocfilehash: 595a89ca58e970a9c886d0b6c2dd05aecd1411ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159383"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Wat is er gebeurd met mijn WebAPI-project (Visual Studio Azure Active Directory connected service)

> [!div class="op_single_selector"]
> - [Aan de slag](vs-active-directory-webapi-getting-started.md)
> - [Wat is er gebeurd](vs-active-directory-webapi-what-happened.md)

In dit artikel worden de exacte wijzigingen in ASP.NET WebAPI, ASP.NET toepassing met één pagina en ASP.NET Azure API-projecten weergegeven bij het toevoegen van de [Azure Active Directory connected service met Visual Studio](vs-active-directory-add-connected-service.md). Geldt ook voor de ASP.NET Azure Mobile Service-projecten in Visual Studio 2015.

Zie Aan de [slag gaan](vs-active-directory-webapi-getting-started.md)voor informatie over het werken met de verbonden service.

## <a name="added-references"></a>Toegevoegde referenties

Beïnvloedt het projectbestand *.NET-verwijzingen) en `packages.config` (NuGet-verwijzingen).

| Type | Naslaginformatie |
| --- | --- |
| .net; NuGet (NuGet) | Microsoft.Owin (Microsoft.Owin) |
| .net; NuGet (NuGet) | Microsoft.Owin.Host.SystemWeb |
| .net; NuGet (NuGet) | Microsoft.Owin.Beveiliging |
| .net; NuGet (NuGet) | Microsoft.Owin.Security.ActiveDirectory |
| .net; NuGet (NuGet) | Microsoft.Owin.Security.Jwt |
| .net; NuGet (NuGet) | Microsoft.Owin.Security.OAuth |
| .net; NuGet (NuGet) | Owin (Owin) |
| .net; NuGet (NuGet) | System.IdentityModel.Tokens.Jwt |

Aanvullende verwijzingen als u de optie **Mapgegevens lezen** hebt geselecteerd:

| Type | Naslaginformatie |
| --- | --- |
| .net; NuGet (NuGet) | EntiteitKader |
| .NET        | EntityFramework.SqlServer (alleen Visual Studio 2015) |
| .net; NuGet (NuGet) | Microsoft.Azure.ActiveDirectory.GraphClient |
| .net; NuGet (NuGet) | Microsoft.Data.Edm |
| .net; NuGet (NuGet) | Microsoft.Data.OData |
| .net; NuGet (NuGet) | Microsoft.Data.Services.Client |
| .net; NuGet (NuGet) | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.activedirectory.WindowsForms<br>(Alleen Visual Studio 2015) |
| .net; NuGet (NuGet) | System.Spatial System.Spatial System.Spatial System. |

De volgende verwijzingen worden verwijderd (ASP.NET 4 projecten, zoals in Visual Studio 2015):

| Type | Naslaginformatie |
| --- | --- |
| .net; NuGet (NuGet) | Microsoft.AspNet.Identity.Core |
| .net; NuGet (NuGet) | Microsoft.AspNet.Identity.EntityFramework |
| .net; NuGet (NuGet) | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Wijzigingen in projectbestanden

- Stel de `IISExpressSSLPort` eigenschap in op een duidelijk getal.
- Stel de `WebProject_DirectoryAccessLevelKey` eigenschap in op 0 of 1 als u de optie **Mapgegevens lezen** hebt geselecteerd.
- Stel de `IISUrl` `https://localhost:<port>/` eigenschap `<port>` in `IISExpressSSLPort` op de plaats waar de waarde overeenkomt.

## <a name="webconfig-or-appconfig-changes"></a>web.config of app.config wijzigingen

- De volgende configuratievermeldingen toegevoegd:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Alleen Visual Studio 2017: heeft `<appSettings>`ook het volgende item toegevoegd onder "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Elementen `<dependentAssembly>` toegevoegd `<runtime><assemblyBinding>` onder het `System.IdentityModel.Tokens.Jwt`knooppunt voor .

- Als u de optie **Mapgegevens lezen** hebt `<appSettings>`geselecteerd, voegt u de volgende configuratie-item toe onder :

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Codewijzigingen en toevoegingen

- Het `[Authorize]` kenmerk `Controllers/ValueController.cs` toegevoegd aan en alle andere bestaande controllers.

- Added an authentication `App_Start/Startup.Auth.cs`startup class, containing startup logic for Azure AD authentication, or modified the accordingly. Als u de optie **Mapgegevens lezen** hebt geselecteerd, bevat dit bestand ook code om een OAuth-code te ontvangen en deze in te ruilen voor een toegangstoken.

- (Visual Studio 2015 met alleen ASP.NET 4 app) Verwijderd `App_Start/IdentityConfig.cs` en `Controllers/AccountController.cs` `Models/IdentityModel.cs`toegevoegd `Providers/ApplicationAuthProvider.cs`, en .

- Toegevoegd `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) of `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), met informatie die Visual Studio gebruikt om de toevoeging van de verbonden service te volgen.

### <a name="file-backup-visual-studio-2015"></a>Bestandsback-up (Visual Studio 2015)

Bij het toevoegen van de verbonden service maakt Een back-up van Visual Studio 2015 een back-up van gewijzigde en verwijderde bestanden. Alle getroffen bestanden worden `Backup/AzureAD`opgeslagen in de map. Visual Studio 2017 maakt geen back-ups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Wijzigingen op Azure

- Een Azure AD-toepassing gemaakt in het domein dat u hebt geselecteerd bij het toevoegen van de verbonden service.
- De app bijgewerkt om de machtiging **Mapgegevens lezen** op te nemen als die optie is geselecteerd.

[Meer informatie over Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Volgende stappen

- [Verificatiescenario's voor Azure Active Directory](authentication-scenarios.md)
- [Aanmelding met Microsoft toevoegen aan een ASP.NET-web-app](quickstart-v2-aspnet-webapp.md)
