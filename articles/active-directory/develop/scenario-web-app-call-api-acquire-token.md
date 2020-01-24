---
title: Een Token ophalen in web-apps die web-Api's aanroepen-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een web-app die web-Api's aanroept (een token voor de app ophalen)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a2bbe6e60aeb25d3c159c87228350065649c89cb
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701702"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web-app die web-Api's aanroept-een Token ophalen voor de app

Nu u het client toepassings object hebt gemaakt, gebruikt u dit om een token te verkrijgen om een web-API aan te roepen. In ASP.NET of ASP.NET Core wordt het aanroepen van een web-API uitgevoerd in de controller. Het gaat ongeveer als volgt:

- Er wordt een token voor de Web-API opgehaald met de token cache. U roept `AcquireTokenSilent`aan om dit token op te halen.
- De beveiligde API met het toegangs token aan te roepen.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

De controller methoden worden beveiligd door een `[Authorize]`-kenmerk dat ervoor zorgt dat gebruikers worden geauthenticeerd om de web-app te gebruiken. Dit is de code die Microsoft Graph aanroept.

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

De `ITokenAcquisition`-service wordt door ASP.NET geïnjecteerd door middel van afhankelijkheids injectie.


Hier volgt een vereenvoudigde code van de actie van de HomeController, waarmee een token wordt opgehaald om de Microsoft Graph aan te roepen.

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // use the access token to call a protected web API
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Zie de stap 2 ([2-1-Web-app-aanroepen Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) van de zelf studie [MS-Identity-aspnetcore-zelf studie](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) voor meer informatie over de code die is vereist voor dit scenario.

Er zijn veel extra complexer, zoals:

- Het aanroepen van verschillende Api's,
- incrementele toestemming en voorwaardelijke toegang verwerken.

Deze geavanceerde stappen worden verwerkt in hoofd stuk 3 van de zelf studie [3-webapp-multi-api's](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dingen zijn vergelijkbaar in ASP.NET:

- Een controller actie die wordt beveiligd door een kenmerk [autoriseren], extraheert de Tenant-ID en gebruikers-ID van de `ClaimsPrincipal` lid van de controller. (ASP.NET gebruikt `HttpContext.User`.)
- Vanaf daar bouwt het een MSAL.NET-`IConfidentialClientApplication`.
- Ten slotte wordt de `AcquireTokenSilent` methode van de vertrouwelijke client toepassing aangeroepen.

De code is vergelijkbaar met de code die wordt weer gegeven voor ASP.NET Core.

# <a name="javatabjava"></a>[Java](#tab/java)

In het Java-voor beeld bevindt de code waarmee een API wordt aangeroepen zich in de getUsersFromGraph-methode [AuthPageController. java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Er wordt geprobeerd om `getAuthResultBySilentFlow`aan te roepen. Als de gebruiker moet toestemming geven voor meer bereiken, verwerkt de code de `MsalInteractionRequiredException` om de gebruiker te vragen.

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

            // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
            // so user can consent to new scopes
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
// Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

In het python-voor beeld bevindt de code voor het aanroepen van micro soft Graph zich in [app. py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Er wordt geprobeerd een token op te halen uit de token cache en vervolgens de Web-API aan te roepen nadat de autorisatie-header is ingesteld. Als dat niet het geval is, wordt de gebruiker opnieuw aangemeld.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API aanroepen](scenario-web-app-call-api-call-api.md)
