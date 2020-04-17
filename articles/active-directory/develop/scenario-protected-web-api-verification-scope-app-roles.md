---
title: Scopes en app-rollen protected web API verifiëren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een beveiligde web-API en het configureren van de code van uw toepassing.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 018e7f9bc389e3d148ff6860dae9fef88991e5c4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537165"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Beveiligde web-API: scopes en app-rollen verifiëren

In dit artikel wordt beschreven hoe u autorisatie toevoegen aan uw web-API. Deze beveiliging zorgt ervoor dat de API alleen wordt aangeroepen door:

- Toepassingen namens gebruikers die over de juiste scopes beschikken.
- Daemon-apps met de juiste toepassingsrollen.

> [!NOTE]
> De codefragmenten uit dit artikel worden uit de volgende voorbeelden gehaald, die volledig functioneel zijn:
>
> - [ASP.NET Core web API incrementele zelfstudie](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) op GitHub
> - [ASP.NET voorbeeld van web-API](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Als u een ASP.NET of ASP.NET Core-web-API wilt beveiligen, moet u het `[Authorize]` kenmerk toevoegen aan een van de volgende items:

- De controller zelf als u wilt dat alle controlleracties worden beschermd
- De individuele controlleractie voor uw API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Maar deze bescherming is niet genoeg. Het garandeert alleen dat ASP.NET en ASP.NET Core het token valideren. Uw API moet controleren of het token dat wordt gebruikt om de API aan te roepen, wordt opgevraagd bij de verwachte claims. Deze beweringen moeten met name worden gecontroleerd:

- De *scopes* als de API namens een gebruiker wordt aangeroepen.
- De *app speelt een rol* als de API kan worden aangeroepen vanuit een daemon-app.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Scopes verifiëren in API's die namens gebruikers worden aangeroepen

Als een client-app uw API namens een gebruiker aanroept, moet de API een token aan toonder aanvragen met specifieke scopes voor de API. Zie Codeconfiguratie voor meer informatie [| Toondertoken](scenario-protected-web-api-app-configuration.md#bearer-token).

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

De `VerifyUserHasAnyAcceptedScope` methode doet iets als de volgende stappen:

- Controleer of er een `http://schemas.microsoft.com/identity/claims/scope` `scp`claim is met de naam of .
- Controleer of de claim een waarde heeft die het bereik bevat dat door de API wordt verwacht.

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

De voorgaande [voorbeeldcode](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) is voor ASP.NET Core. Voor ASP.NET, `HttpContext.User` gewoon `ClaimsPrincipal.Current`vervangen door , `"http://schemas.microsoft.com/identity/claims/scope"` `"scp"`en vervang de claim type met . Zie ook het codefragment later in dit artikel.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>App-rollen verifiëren in API's die worden aangeroepen door daemon-apps

Als uw web-API wordt aangeroepen door een [daemon-app,](scenario-daemon-overview.md)moet die app een toepassingsmachtiging voor uw web-API vereisen. Zoals wordt weergegeven in [Het blootstellen van toepassingsmachtigingen (app-rollen),](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)worden dergelijke machtigingen weergegeven door uw API. Een voorbeeld `access_as_application` is de app-rol.

U moet nu uw API laten verifiëren dat `roles` het token dat het ontvangt de claim bevat en of deze claim de verwachte waarde heeft. De verificatiecode is vergelijkbaar met de code die gedelegeerde machtigingen verifieert, behalve dat de actievan de controller test op rollen in plaats van scopes:

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

De `ValidateAppRole` methode kan ongeveer als volgt zijn:

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

Deze keer is het codefragment voor ASP.NET. Voor ASP.NET Core, `ClaimsPrincipal.Current` `HttpContext.User`gewoon vervangen `"roles"` door , `"http://schemas.microsoft.com/identity/claims/roles"`en vervang de naam van de claim met . Zie ook het codefragment eerder in dit artikel.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>App-only tokens accepteren als de web-API alleen door daemon-apps moet worden aangeroepen

Gebruikers kunnen ook rollenclaims gebruiken in gebruikerstoewijzingspatronen, zoals wordt weergegeven in [Hoe toe te voegen: App-rollen toevoegen aan uw toepassing en deze ontvangen in het token.](howto-add-app-roles-in-azure-ad-apps.md) Als de rollen aan beide kunnen worden toegewezen, kunnen apps zich aanmelden als gebruikers en gebruikers om zich aan te melden als apps. We raden u aan verschillende rollen voor gebruikers en apps te declareren om deze verwarring te voorkomen.

Als u alleen daemon-apps wilt aanroepen, voegt u de voorwaarde toe dat het token een app-only token is wanneer u de app-rol valideert.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Als u de omgekeerde toestand controleert, kunnen alleen apps die zich aanmelden bij een gebruiker uw API aanroepen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verplaatsen naar productie](scenario-protected-web-api-production.md)
