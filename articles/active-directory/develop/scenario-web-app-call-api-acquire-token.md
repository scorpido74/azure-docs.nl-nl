---
title: Ontvang een token in een web-app die web-API's aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het aanschaffen van een token voor een web-app die web-API's aanroept
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 196d941c5c3b18b737f7a11c25ebbb9eab91be1e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885035"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Een web-app die web-API's aanroept: een token voor de app aanschaffen

U hebt uw clienttoepassingsobject gebouwd. Nu gebruikt u het om een token te verkrijgen om een web-API aan te roepen. In ASP.NET of ASP.NET Core wordt het aanroepen van een web-API in de controller gedaan:

- Download een token voor de web-API met behulp van de tokencache. Om dit token te `AcquireTokenSilent` krijgen, belt u de methode.
- Roep de beveiligde API aan en geef het toegangstoken door als parameter.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

De verwerkingsmethoden worden beschermd `[Authorize]` door een kenmerk dat gebruikers die worden geverifieerd dwingt de web-app te gebruiken. Hier is de code die Microsoft Graph aanroept:

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

De `ITokenAcquisition` service wordt geïnjecteerd door ASP.NET met behulp van afhankelijkheidinjectie.

Hier is vereenvoudigde code voor de `HomeController`actie van de , die een token krijgt om Microsoft Graph te bellen:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Zie de stap fase 2 ([2-1-Web App Calls Microsoft Graph)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)van de [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) om beter te begrijpen welke code nodig is voor dit scenario.

Er zijn andere complexe variaties, zoals:

- Meerdere API's bellen.
- Verwerking van incrementele toestemming en voorwaardelijke toegang.

Deze geavanceerde stappen komen aan bod in hoofdstuk 3 van de [3-WebApp-multi-API's-zelfstudie.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

De code voor ASP.NET is vergelijkbaar met de code die wordt weergegeven voor ASP.NET Core:

- Een controlleractie, beschermd door een kenmerk [Machtig], haalt de `ClaimsPrincipal` tenant-id en gebruikersnaam van het lid van de controller uit. (ASP.NET `HttpContext.User`gebruikt .)
- Van daaruit bouwt het `IConfidentialClientApplication` een MSAL.NET object.
- Ten slotte noemt `AcquireTokenSilent` het de methode van de vertrouwelijke clientapplicatie.

# <a name="java"></a>[Java](#tab/java)

In het Java-voorbeeld bevindt de code die een API aanroept zich in de getUsersFromGraph-methode in [AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

De methode probeert `getAuthResultBySilentFlow`te bellen . Als de gebruiker toestemming moet geven voor meer `MsalInteractionRequiredException` scopes, verwerkt de code het object om de gebruiker uit te dagen.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

In het Voorbeeld van Python staat de code die Microsoft Graph aanroept in [app.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

De code probeert een token uit de tokencache te halen. Vervolgens, na het instellen van de autorisatie header, het roept de web-API. Als het geen token kan krijgen, wordt de gebruiker opnieuw inlogt.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API aanroepen](scenario-web-app-call-api-call-api.md)
