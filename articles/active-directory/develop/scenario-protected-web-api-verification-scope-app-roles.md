---
title: Bereiken en de beveiligde web-API van de app-rollen controleren | Azure
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
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768120"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Beveiligde web-API: scopes en app-rollen controleren

In dit artikel wordt beschreven hoe u autorisatie kunt toevoegen aan uw web-API. Deze bescherming zorgt ervoor dat de API alleen wordt aangeroepen door:

- Toepassingen namens gebruikers die de juiste bereiken hebben.
- Daemon-apps met de juiste toepassings rollen.

> [!NOTE]
> De code fragmenten uit dit artikel worden geëxtraheerd uit de volgende voor beelden, die volledig functioneel zijn:
>
> - [Stapsgewijze zelf studie voor ASP.net core web-API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) op github
> - [ASP.NET Web API-voor beeld](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Als u een ASP.NET of ASP.NET Core Web-API wilt beveiligen, moet u het kenmerk `[Authorize]` toevoegen aan een van de volgende items:

- De controller zelf als u wilt dat alle controller acties worden beveiligd
- De afzonderlijke controller actie voor uw API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Deze beveiliging is echter niet voldoende. Het garandeert alleen dat ASP.NET en ASP.NET Core het token te valideren. Uw API moet controleren of het token dat wordt gebruikt om de API aan te roepen, is aangevraagd met de verwachte claims. Deze claims hebben met name betrekking op verificatie:

- De *bereiken* als de API namens een gebruiker wordt genoemd.
- De *app-rollen* als de API kan worden aangeroepen vanuit een daemon-app.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Scopes verifiëren in Api's die namens gebruikers worden aangeroepen

Als een client-app uw API namens een gebruiker aanroept, moet de API een Bearer-token aanvragen dat specifieke bereiken voor de API heeft. Zie voor meer informatie [code configuratie | Bearer-token](scenario-protected-web-api-app-configuration.md#bearer-token).

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

De methode `VerifyUserHasAnyAcceptedScope` heeft iets als de volgende stappen:

- Controleer of er een claim is met de naam `http://schemas.microsoft.com/identity/claims/scope` of `scp`.
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

De voor gaande [voorbeeld code](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) is voor ASP.net core. Vervang voor ASP.NET alleen `HttpContext.User` door `ClaimsPrincipal.Current`en vervang het claim type `"http://schemas.microsoft.com/identity/claims/scope"` door `"scp"`. Zie ook het code fragment verderop in dit artikel.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>App-rollen controleren in Api's die worden aangeroepen door daemon-apps

Als uw web-API wordt aangeroepen door een [daemon-app](scenario-daemon-overview.md), moet voor die app een toepassings machtiging voor uw web-API zijn vereist. Zoals u kunt zien in het weer geven van [toepassings machtigingen (app-rollen)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), worden deze machtigingen beschikbaar gesteld door uw API. Een voor beeld is de `access_as_application` app-rol.

U moet uw API nu controleren of het token dat wordt ontvangen de `roles` claim bevat en dat deze claim de verwachte waarde heeft. De verificatie code is vergelijkbaar met de code die de gedelegeerde machtigingen verifieert, behalve dat uw controller actie tests voor rollen heeft uitgevoerd in plaats van bereiken:

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

De `ValidateAppRole` methode kan er ongeveer als volgt uitzien:

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

Deze keer is het code fragment voor ASP.NET. Voor ASP.NET Core moet u `ClaimsPrincipal.Current` gewoon vervangen door `HttpContext.User`en de naam van de `"roles"` claim vervangen door `"http://schemas.microsoft.com/identity/claims/roles"`. Zie ook het code fragment eerder in dit artikel.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Alleen app-tokens accepteren als de Web-API alleen moet worden aangeroepen door daemon-apps

Gebruikers kunnen ook rollen claims gebruiken in gebruikers toewijzings patronen, zoals wordt weer gegeven in [procedure: app-rollen toevoegen in uw toepassing en ontvangen in het token](howto-add-app-roles-in-azure-ad-apps.md). Als de rollen kunnen worden toegewezen aan beide, kunnen gebruikers met behulp van rollen het aanmelden als gebruiker en gebruikers zich aanmelden als apps. U kunt het beste verschillende rollen voor gebruikers en apps declareren om deze Verwar ring te voor komen.

Als u wilt dat alleen daemon-apps uw web-API aanroepen, voegt u de voor waarde dat het token een token voor app-only is wanneer u de functie van de app valideert.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Door de inverse voor waarde te controleren, kunnen alleen apps die zich aanmelden bij een gebruiker, uw API aanroepen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naar productie verplaatsen](scenario-protected-web-api-production.md)
