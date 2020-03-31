---
title: Wijzigingen in een MVC-project wanneer u verbinding maakt met Azure AD
description: Beschrijft wat er met uw MVC-project gebeurt wanneer u verbinding maakt met Azure AD met behulp van Visual Studio connected services
author: ghogen
manager: jillfra
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: ac38adba4ca0d978dd48a546bed1b1faf4fe40fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036961"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Wat is er gebeurd met mijn MVC-project (Visual Studio Azure Active Directory connected service)?

> [!div class="op_single_selector"]
> - [Aan de slag](vs-active-directory-dotnet-getting-started.md)
> - [Wat is er gebeurd](vs-active-directory-dotnet-what-happened.md)

In dit artikel worden de exacte wijzigingen in een ASP.NET MVC-project aangebracht bij het toevoegen van de [Azure Active Directory connected service met Visual Studio.](vs-active-directory-add-connected-service.md)

Zie Aan de [slag gaan](vs-active-directory-dotnet-getting-started.md)voor informatie over het werken met de verbonden service.

## <a name="added-references"></a>Toegevoegde referenties

Beïnvloedt het projectbestand (*.NET-verwijzingen) en `packages.config` (NuGet-verwijzingen).

| Type | Naslaginformatie |
| --- | --- |
| .net; NuGet (NuGet) | Microsoft.IdentityModel.Protocol.Extensions |
| .net; NuGet (NuGet) | Microsoft.Owin (Microsoft.Owin) |
| .net; NuGet (NuGet) | Microsoft.Owin.Host.SystemWeb |
| .net; NuGet (NuGet) | Microsoft.Owin.Beveiliging |
| .net; NuGet (NuGet) | Microsoft.Owin.Security.Cookies |
| .net; NuGet (NuGet) | Microsoft.Owin.Security.OpenIdConnect |
| .net; NuGet (NuGet) | Owin (Owin) |
| .NET        | System.IdentityModel |
| .net; NuGet (NuGet) | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

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
| .NET        | Microsoft.IdentityModel.Clients.activedirectory.WindowsForms (alleen Visual Studio 2015) |
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
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Elementen `<dependentAssembly>` toegevoegd `<runtime><assemblyBinding>` onder het `System.IdentityModel.Tokens.Jwt` `Microsoft.IdentityModel.Protocol.Extensions`knooppunt voor en .

Aanvullende wijzigingen als u de optie **Mapgegevens lezen** hebt geselecteerd:

- Toegevoegd de volgende `<appSettings>`configuratie-vermelding onder :

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Toegevoegd de volgende `<configuration>`elementen onder ; waarden voor het project-mdf-bestand en projectcatalogus-id variëren:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- Elementen `<dependentAssembly>` onder `<runtime><assemblyBinding>` het knooppunt `Microsoft.Data.Services.Client` `Microsoft.Data.Edm`toegevoegd `Microsoft.Data.OData`voor , en .

## <a name="code-changes-and-additions"></a>Codewijzigingen en toevoegingen

- Het `[Authorize]` kenmerk `Controllers/HomeController.cs` toegevoegd aan en alle andere bestaande controllers.

- Added an authentication `App_Start/Startup.Auth.cs`startup class, , containing startup logic for Azure AD authentication. Als u de optie **Mapgegevens lezen** hebt geselecteerd, bevat dit bestand ook code om een OAuth-code te ontvangen en deze in te ruilen voor een toegangstoken.

- Toegevoegd een controller `Controllers/AccountController.cs`klasse, `SignIn` `SignOut` , met en methoden.

- Een gedeeltelijke weergave `Views/Shared/_LoginPartial.cshtml`toegevoegd, een actiekoppeling voor `SignIn` en `SignOut`.

- Een gedeeltelijke weergave `Views/Account/SignoutCallback.cshtml`toegevoegd, met HTML voor de gebruikersinterface voor afmelden.

- De `Startup.Configuration` methode bijgewerkt om `ConfigureAuth(app)` een aanroep op te nemen als de klasse al bestond; anders een `Startup` klasse toegevoegd die aanroepen van de methode bevat.

- Toegevoegd `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) of `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), met informatie die Visual Studio gebruikt om de toevoeging van de verbonden service te volgen.

- Als u de optie **Mapgegevens lezen** hebt geselecteerd, hebt u tokencaching toegevoegd `Models/ADALTokenCache.cs` en `Models/ApplicationDbContext.cs` ondersteund. Ook een extra controller en weergave toegevoegd om de toegang `Controllers/UserProfileController.cs`tot `Views/UserProfile/Index.cshtml`gebruikersprofielgegevens te illustreren met behulp van Azure-grafiek-API's: , en`Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Bestandsback-up (Visual Studio 2015)

Bij het toevoegen van de verbonden service maakt Een back-up van Visual Studio 2015 een back-up van gewijzigde en verwijderde bestanden. Alle getroffen bestanden worden `Backup/AzureAD`opgeslagen in de map. Visual Studio 2017 en hoger maakt geen back-ups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Wijzigingen op Azure

- Een Azure AD-toepassing gemaakt in het domein dat u hebt geselecteerd bij het toevoegen van de verbonden service.
- De app bijgewerkt om de machtiging **Mapgegevens lezen** op te nemen als die optie is geselecteerd.

[Meer informatie over Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Volgende stappen

- [Verificatiescenario's voor Azure Active Directory](authentication-scenarios.md)
- [Aanmelding met Microsoft toevoegen aan een ASP.NET-web-app](quickstart-v2-aspnet-webapp.md)
