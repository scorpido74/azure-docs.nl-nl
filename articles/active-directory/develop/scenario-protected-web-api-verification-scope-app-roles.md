---
title: Bereiken en app-rollen controleren met de beveiligde web-API
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een beveiligde web-API en het configureren van de code van uw toepassing.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4340d92bdfe871010021edcbefcde62ab8202462
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149235"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Beveiligde web-API: scopes en app-rollen controleren

In dit artikel wordt beschreven hoe u autorisatie kunt toevoegen aan uw web-API. Deze bescherming zorgt ervoor dat de API alleen wordt aangeroepen door:

- Toepassingen namens gebruikers die de juiste bereiken hebben.
- Daemon-apps met de juiste toepassings rollen.

> [!NOTE]
> De code fragmenten uit dit artikel worden geëxtraheerd uit de volgende voor beelden, die volledig functioneel zijn
>
> - [Stapsgewijze zelf studie voor ASP.net core web-API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) op github
> - [ASP.NET Web API-voor beeld](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Als u een ASP.NET/ASP.NET core web-API wilt beveiligen, moet u het `[Authorize]` kenmerk toevoegen aan een van de volgende:

- De controller zelf als u wilt dat alle acties van de controller worden beveiligd
- De afzonderlijke controller actie voor uw API

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Deze beveiliging is echter niet voldoende. Hiermee wordt alleen gegarandeerd dat ASP.NET/ASP.NET core het token valideert. Uw API moet controleren of het token dat wordt gebruikt om uw web-API aan te roepen, is aangevraagd met de claims die worden verwacht, met name:

- De *bereiken*, als de API namens een gebruiker wordt genoemd.
- De *app-rollen*, als de API kan worden aangeroepen vanuit een daemon-app.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Scopes verifiëren in Api's die namens gebruikers worden aangeroepen

Als uw API namens een gebruiker wordt aangeroepen door een client-app, moet deze een Bearer-token aanvragen dat specifieke bereiken voor de API heeft. (Zie [code configuratie | Bearer-token](scenario-protected-web-api-app-configuration.md#bearer-token).)

```CSharp
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

De `VerifyUserHasAnyAcceptedScope` methode zou er ongeveer als volgt uitzien:

- Controleer of er een claim is met de naam `http://schemas.microsoft.com/identity/claims/scope` of `scp`.
- Controleer of de claim een waarde heeft die het bereik bevat dat door de API wordt verwacht.

```CSharp
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

Deze [voorbeeld code](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) is voor ASP.net core. Vervang voor ASP.NET alleen `HttpContext.User` door `ClaimsPrincipal.Current`en vervang het claim type `"http://schemas.microsoft.com/identity/claims/scope"` door `"scp"`. (Zie ook het code fragment verderop in dit artikel.)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>App-rollen controleren in Api's die worden aangeroepen door daemon-apps

Als uw web-API wordt aangeroepen door een [daemon-app](scenario-daemon-overview.md), moet voor die app een toepassings machtiging voor uw web-API zijn vereist. We hebben in de weer gegeven [toepassings machtigingen (app-rollen)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) gezien die de API van deze machtigingen (bijvoorbeeld de `access_as_application` app-rol).
U moet uw Api's nu controleren of het ontvangen token de `roles` claim bevat en dat deze claim de verwachte waarde heeft. De code die deze verificatie uitvoert, is vergelijkbaar met de code die de gedelegeerde machtigingen verifieert, behalve dat, in plaats van te testen op `scopes`, uw controller actie zal testen op `roles`:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

De `ValidateAppRole()` methode kan er ongeveer als volgt uitzien:

```CSharp
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

Deze keer is het code fragment voor ASP.NET. Voor ASP.NET Core moet u `ClaimsPrincipal.Current` gewoon vervangen door `HttpContext.User`en de naam van de `"roles"` claim vervangen door `"http://schemas.microsoft.com/identity/claims/roles"`. (Zie ook het code fragment eerder in dit artikel.)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Alleen app-tokens accepteren als de Web-API alleen moet worden aangeroepen door daemon-apps

De `roles` claim wordt ook gebruikt voor gebruikers in een gebruikers toewijzings patroon. (Zie [procedure: app-rollen toevoegen in uw toepassing en deze in het token ontvangen](howto-add-app-roles-in-azure-ad-apps.md).) Door rollen te controleren, kunnen apps zich dus aanmelden als gebruikers en op een andere manier, als de rollen kunnen worden toegewezen aan beide. U kunt het beste verschillende rollen voor gebruikers en apps declareren om deze Verwar ring te voor komen.

Als u alleen daemon-Apps wilt toestaan om uw web-API aan te roepen, voegt u een voor waarde toe, wanneer u de app-rol valideert, dat het token alleen een app-token is:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Door de inverse voor waarde te controleren, kunnen alleen apps die zich aanmelden bij een gebruiker, uw API aanroepen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naar productie verplaatsen](scenario-protected-web-api-production.md)
