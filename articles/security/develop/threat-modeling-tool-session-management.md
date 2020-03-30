---
title: Sessiebeheer - Microsoft Threat Modeling Tool - Azure | Microsoft Documenten
description: oplossingen voor bedreigingen die worden blootgesteld in de Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 5d9dc1595e3cc812ba060d958b6e981867500ae2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73161510"
---
# <a name="security-frame-session-management"></a>Beveiligingsframe: sessiebeheer
| Product/service | Artikel |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[De juiste afmelding implementeren met ADAL-methoden bij het gebruik van Azure AD](#logout-adal)</li></ul> |
| IoT-apparaat | <ul><li>[Eindige levensduur gebruiken voor gegenereerde SaS-tokens](#finite-tokens)</li></ul> |
| **Azure Document DB** | <ul><li>[Minimale tokenlevensduur gebruiken voor gegenereerde Resourcetokens](#resource-tokens)</li></ul> |
| **Adfs** | <ul><li>[De juiste afmelding implementeren met WsFederation-methoden bij het gebruik van ADFS](#wsfederation-logout)</li></ul> |
| **Identiteitsserver** | <ul><li>[De juiste afmelding implementeren bij het gebruik van Identity Server](#proper-logout)</li></ul> |
| **Webtoepassing** | <ul><li>[Toepassingen die beschikbaar zijn via HTTPS moeten veilige cookies gebruiken](#https-secure-cookies)</li><li>[Alle op http gebaseerde toepassingen moeten http alleen opgeven voor cookiedefinitie](#cookie-definition)</li><li>[Beperken tegen aanvallen op Cross-Site Request Forgery (CSRF) op ASP.NET webpagina's](#csrf-asp)</li><li>[Sessie instellen voor de levensduur van inactiviteit](#inactivity-lifetime)</li><li>[De juiste afmelding van de toepassing implementeren](#proper-app-logout)</li></ul> |
| **Web-API** | <ul><li>[Beperken tegen Aanvallen van Cross-Site Request Forgery (CSRF) op ASP.NET Web API's](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>De juiste afmelding implementeren met ADAL-methoden bij het gebruik van Azure AD

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure AD | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Als de toepassing is gebaseerd op toegangstoken dat is uitgegeven door Azure AD, moet de gebeurtenishandler voor afmelden aanroepen |

### <a name="example"></a>Voorbeeld
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Voorbeeld
Het moet ook de sessie van de gebruiker vernietigen door de methode Session.Abandon() aan te roepen. De volgende methode toont een veilige implementatie van de gebruiker afmelden:
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Eindige levensduur gebruiken voor gegenereerde SaS-tokens

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-apparaat | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | SaS-tokens die zijn gegenereerd voor het verifiëren naar Azure IoT Hub, moeten een eindige vervaldatum hebben. Houd de levensduur van het SaS-token tot een minimum om de hoeveelheid tijd die ze kunnen worden afgespeeld te beperken in het geval de tokens worden aangetast.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Minimale tokenlevensduur gebruiken voor gegenereerde Resourcetokens

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Document DB | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Verlaag de tijdspanne van resourcetoken tot een vereiste minimumwaarde. Resourcetokens hebben een standaard geldige tijdspanne van 1 uur.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>De juiste afmelding implementeren met WsFederation-methoden bij het gebruik van ADFS

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | ADFS | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Als de toepassing is gebaseerd op STS-token uitgegeven door ADFS, moet de gebeurtenishandler bijafmelden de methode WSFederationAuthenticationModule.FederatedSignOut() aanroepen om de gebruiker uit te loggen. Ook de huidige sessie moet worden vernietigd en de sessietokenwaarde moet worden gereset en tenietgedaan.|

### <a name="example"></a>Voorbeeld
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>De juiste afmelding implementeren bij het gebruik van Identity Server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Identiteitsserver | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [IdentityServer3-Federated afmelden](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Stappen** | IdentityServer ondersteunt de mogelijkheid om te reageren op externe identiteitsproviders. Wanneer een gebruiker zich afmeldt bij een upstream-identiteitsprovider, afhankelijk van het gebruikte protocol, is het mogelijk om een melding te ontvangen wanneer de gebruiker zich afmeldt. Hiermee kan IdentityServer haar klanten op de hoogte stellen, zodat ze de gebruiker ook kunnen afmelden. Controleer de documentatie in de sectie referenties voor de implementatiedetails.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>Toepassingen die beschikbaar zijn via HTTPS moeten veilige cookies gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | EnvironmentType - OnPrem |
| **Verwijzingen**              | [httpCookies Element (ASP.NET Instellingen Schema)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [httpCookie.Secure Eigenschap](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Stappen** | Cookies zijn normaal gesproken alleen toegankelijk voor het domein waarvoor ze zijn ingericht. Helaas bevat de definitie van "domein" het protocol niet, zodat cookies die via HTTPS zijn gemaakt, toegankelijk zijn via HTTP. Het kenmerk 'veilig' geeft aan de browser aan dat de cookie alleen via HTTPS beschikbaar moet worden gesteld. Zorg ervoor dat alle cookies die via HTTPS worden ingesteld, het **beveiligde** kenmerk gebruiken. De vereiste kan worden afgedwongen in het web.config-bestand door het vereiste SSL-kenmerk in te stellen op true. Het is de voorkeursbenadering omdat het **beveiligde** kenmerk voor alle huidige en toekomstige cookies wordt afgedwongen zonder dat er extra codewijzigingen hoeven aan te brengen.|

### <a name="example"></a>Voorbeeld
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
De instelling wordt afgedwongen, zelfs als HTTP wordt gebruikt om toegang te krijgen tot de toepassing. Als HTTP wordt gebruikt om toegang te krijgen tot de toepassing, wordt de toepassing door de instelling afgebroken omdat de cookies zijn ingesteld met het beveiligde kenmerk en de browser deze niet terugstuurt naar de toepassing.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Webformulieren, MVC5 |
| **Kenmerken**              | EnvironmentType - OnPrem |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Wanneer de webtoepassing de relying party is en de IdP ADFS-server is, kan het beveiligde kenmerk `system.identityModel.services` van het FedAuth-token worden geconfigureerd door vereisenSSL in te stellen naar True in sectie van web.config:|

### <a name="example"></a>Voorbeeld
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Alle op http gebaseerde toepassingen moeten http alleen opgeven voor cookiedefinitie

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Secure Cookie-kenmerk](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Stappen** | Om het risico van openbaarmaking van informatie met een cross-site scripting (XSS) aanval te beperken, werd een nieuw attribuut - httpOnly - geïntroduceerd in cookies en wordt ondersteund door alle grote browsers. Het kenmerk geeft aan dat een cookie niet toegankelijk is via het script. Door gebruik te maken van HttpOnly cookies, een webapplicatie vermindert de mogelijkheid dat gevoelige informatie in de cookie kan worden gestolen via script en verzonden naar de website van een aanvaller. |

### <a name="example"></a>Voorbeeld
Alle HTTP-gebaseerde toepassingen die cookies gebruiken, moeten HttpOnly opgeven in de cookiedefinitie, door de volgende configuratie in web.config te implementeren:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Webformulieren |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [FormsAuthentication.RequireSSL , eigenschap](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Stappen** | De eigenschapswaarde RequireSSL is ingesteld in het configuratiebestand voor een ASP.NET toepassing met behulp van het kenmerk requireSSL van het configuratie-element. U in het web.config-bestand voor uw ASP.NET toepassing opgeven of SSL (Secure Sockets Layer) vereist is om de form-authentication cookie naar de server terug te sturen door het vereiste SSL-kenmerk in te stellen.|

### <a name="example"></a>Voorbeeld 
In het volgende codevoorbeeld wordt het kenmerk requireSSL ingesteld in het bestand Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC5 MVC5 |
| **Kenmerken**              | EnvironmentType - OnPrem |
| **Verwijzingen**              | [Configuratie van Windows Identity Foundation (WIF) – Deel II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Stappen** | Als u httpOnly-kenmerk voor FedAuth-cookies wilt instellen, moet de kenmerkwaarde van HideFromCsript worden ingesteld op True. |

### <a name="example"></a>Voorbeeld
De volgende configuratie toont de juiste configuratie:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>Beperken tegen aanvallen op Cross-Site Request Forgery (CSRF) op ASP.NET webpagina's

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Cross-site request forgery (CSRF of XSRF) is een type aanval waarbij een aanvaller acties kan uitvoeren in de beveiligingscontext van de gevestigde sessie van een andere gebruiker op een website. Het doel is om inhoud te wijzigen of te verwijderen, als de beoogde website uitsluitend afhankelijk is van sessiecookies om ontvangen verzoek te verifiëren. Een aanvaller kan misbruik maken van dit beveiligingslek door de browser van een andere gebruiker een URL te laten laden met een opdracht van een kwetsbare site waarop de gebruiker al is ingelogd. Er zijn veel manieren waarop een aanvaller dat kan doen, bijvoorbeeld door een andere website te hosten die een bron van de kwetsbare server laadt of door de gebruiker op een koppeling te laten klikken. De aanval kan worden voorkomen als de server een extra token naar de client stuurt, vereist dat de client dat token in alle toekomstige aanvragen opneemt en controleert of alle toekomstige aanvragen een token bevatten dat betrekking heeft op de huidige sessie, zoals het gebruik van de ASP.NET AntiForgeryToken of ViewState. |

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [XSRF/CSRF-preventie in ASP.NET MVC en webpagina's](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Stappen** | Anti-CSRF en ASP.NET MVC-formulieren - Gebruik de `AntiForgeryToken` helpermethode voor weergaven; in `Html.AntiForgeryToken()` het formulier, bijvoorbeeld,|

### <a name="example"></a>Voorbeeld
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Voorbeeld
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Voorbeeld
Html.AntiForgeryToken() geeft de bezoeker tegelijkertijd een cookie genaamd __RequestVerificationToken, met dezelfde waarde als de hierboven vermelde willekeurige verborgen waarde. Als u vervolgens een binnenkomend formulierbericht wilt valideren, voegt u het filter [ValidateAntiForgeryToken] toe aan de doelactiemethode. Bijvoorbeeld:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Autorisatiefilter dat controleert of:
* Het binnenkomende verzoek heeft een cookie genaamd __RequestVerificationToken
* De binnenkomende aanvraag `Request.Form` heeft een vermelding genaamd __RequestVerificationToken
* Deze cookie `Request.Form` en waarden komen overeen Ervan uitgaande dat alles goed is, gaat het verzoek gewoon door. Maar zo niet, dan is een autorisatie fout met bericht "Een vereiste anti-vervalsing token werd niet geleverd of ongeldig was". 

### <a name="example"></a>Voorbeeld
Anti-CSRF en AJAX: Het formuliertoken kan een probleem zijn voor AJAX-verzoeken, omdat een AJAX-verzoek JSON-gegevens kan verzenden, geen HTML-formuliergegevens. Een oplossing is om de tokens te verzenden in een aangepaste HTTP-header. De volgende code gebruikt razor syntaxis om de tokens te genereren en voegt vervolgens de tokens toe aan een AJAX-verzoek. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Voorbeeld
Wanneer u de aanvraag verwerkt, haalt u de tokens uit de aangezochte header. Bel vervolgens de AntiForgery.Validate-methode om de tokens te valideren. Met de methode Valideren wordt een uitzondering gemaakt als de tokens niet geldig zijn.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Webformulieren |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Profiteer van ASP.NET ingebouwde functies om webaanvallen af te weren](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Stappen** | CSRF-aanvallen in WebForm-gebaseerde toepassingen kunnen worden beperkt door ViewStateUserKey in te stellen op een willekeurige tekenreeks die varieert voor elke gebruiker - gebruikers-id of, beter nog, sessie-ID. Om een aantal technische en sociale redenen past sessie-ID veel beter omdat een sessie-ID onvoorspelbaar is, een time-out is en per gebruiker varieert.|

### <a name="example"></a>Voorbeeld
Hier is de code die u moet hebben in al uw pagina's:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>Sessie instellen voor de levensduur van inactiviteit

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [HttpSessionState.Timeout , eigenschap](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Stappen** | Sessietime-out vertegenwoordigt de gebeurtenis die optreedt wanneer een gebruiker gedurende een interval geen actie uitvoert op een website (gedefinieerd door de webserver). De gebeurtenis, aan serverzijde, wijzigt de status van de gebruikerssessie in 'ongeldig' (bijvoorbeeld 'niet meer gebruikt') en instrueert de webserver om deze te vernietigen (alle gegevens verwijderen die erin zitten). In het volgende codevoorbeeld wordt het kenmerk time-outsessie ingesteld op 15 minuten in het bestand Web.config.|

### <a name="example"></a>Voorbeeld
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Bedreigingsdetectie inschakelen op Azure SQL

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Webformulieren |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Formulierelement voor verificatie (ASP.NET instellingenschema)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Stappen** | Time-out van de cookie-time-out van het formulierenverificatieticket instellen op 15 minuten|

### <a name="example"></a>Voorbeeld
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Webformulieren, MVC5 |
| **Kenmerken**              | EnvironmentType - OnPrem |
| **Verwijzingen**              | [asdeqa asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Stappen** | Wanneer de webapplicatie Relying Party is en ADFS de STS is, kan de levensduur van de authenticatiecookies - FedAuth-tokens - worden ingesteld door de volgende configuratie in web.config:|

### <a name="example"></a>Voorbeeld
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Voorbeeld
Ook de levensduur van de ADFS-claimtoken moet worden ingesteld op 15 minuten, door de volgende powershell-opdracht uit te voeren op de ADFS-server:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>De juiste afmelding van de toepassing implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Voer de juiste afmelding uit van de toepassing, wanneer de gebruiker drukt op uitloggen knop. Bij het aanmelden moet de toepassing de sessie van de gebruiker vernietigen en ook de cookiewaarde van de sessie opnieuw instellen en tenietdoen, samen met het opnieuw instellen en tenietdoen van de cookiewaarde van verificatie. Wanneer meerdere sessies zijn gekoppeld aan een enkele gebruikersidentiteit, moeten ze ook collectief worden beëindigd aan de serverzijde bij een time-out of afmelding. Tot slot, zorg ervoor dat de logout-functionaliteit beschikbaar is op elke pagina. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>Beperken tegen Aanvallen van Cross-Site Request Forgery (CSRF) op ASP.NET Web API's

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Cross-site request forgery (CSRF of XSRF) is een type aanval waarbij een aanvaller acties kan uitvoeren in de beveiligingscontext van de gevestigde sessie van een andere gebruiker op een website. Het doel is om inhoud te wijzigen of te verwijderen, als de beoogde website uitsluitend afhankelijk is van sessiecookies om ontvangen verzoek te verifiëren. Een aanvaller kan misbruik maken van dit beveiligingslek door de browser van een andere gebruiker een URL te laten laden met een opdracht van een kwetsbare site waarop de gebruiker al is ingelogd. Er zijn veel manieren waarop een aanvaller dat kan doen, bijvoorbeeld door een andere website te hosten die een bron van de kwetsbare server laadt of door de gebruiker op een koppeling te laten klikken. De aanval kan worden voorkomen als de server een extra token naar de client stuurt, vereist dat de client dat token in alle toekomstige aanvragen opneemt en controleert of alle toekomstige aanvragen een token bevatten dat betrekking heeft op de huidige sessie, zoals het gebruik van de ASP.NET AntiForgeryToken of ViewState. |

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Aanvallen van cross-site aanvraagvervalsing (CSRF) voorkomen in ASP.NET Web API](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Stappen** | Anti-CSRF en AJAX: Het formuliertoken kan een probleem zijn voor AJAX-verzoeken, omdat een AJAX-verzoek JSON-gegevens kan verzenden, geen HTML-formuliergegevens. Een oplossing is om de tokens te verzenden in een aangepaste HTTP-header. De volgende code gebruikt razor syntaxis om de tokens te genereren en voegt vervolgens de tokens toe aan een AJAX-verzoek. |

### <a name="example"></a>Voorbeeld
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Voorbeeld
Wanneer u de aanvraag verwerkt, haalt u de tokens uit de aangezochte header. Bel vervolgens de AntiForgery.Validate-methode om de tokens te valideren. Met de methode Valideren wordt een uitzondering gemaakt als de tokens niet geldig zijn.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Voorbeeld
Anti-CSRF- en ASP.NET MVC-formulieren - Gebruik de AntiForgeryToken-helpermethode voor weergaven; een Html.AntiForgeryToken() in de vorm, bijvoorbeeld,
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Voorbeeld
In het bovenstaande voorbeeld wordt iets als het volgende uitgevoerd:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Voorbeeld
Html.AntiForgeryToken() geeft de bezoeker tegelijkertijd een cookie genaamd __RequestVerificationToken, met dezelfde waarde als de hierboven vermelde willekeurige verborgen waarde. Als u vervolgens een binnenkomend formulierbericht wilt valideren, voegt u het filter [ValidateAntiForgeryToken] toe aan de doelactiemethode. Bijvoorbeeld:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Autorisatiefilter dat controleert of:
* Het binnenkomende verzoek heeft een cookie genaamd __RequestVerificationToken
* De binnenkomende aanvraag `Request.Form` heeft een vermelding genaamd __RequestVerificationToken
* Deze cookie `Request.Form` en waarden komen overeen Ervan uitgaande dat alles goed is, gaat het verzoek gewoon door. Maar zo niet, dan is een autorisatie fout met bericht "Een vereiste anti-vervalsing token werd niet geleverd of ongeldig was".

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | Identity Provider - ADFS, Identity Provider - Azure AD |
| **Verwijzingen**              | [Een web-API beveiligen met afzonderlijke accounts en lokale aanmelding in ASP.NET Web API 2.2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Stappen** | Als de web-API is beveiligd met OAuth 2.0, verwacht deze een token aan toonder in de header Autorisatieaanvraag en verleent het alleen toegang tot de aanvraag als het token geldig is. In tegenstelling tot cookiegebaseerde verificatie, koppelen browsers de tokens aan de drager niet aan verzoeken. De verzoekende client moet het token aan toonder expliciet koppelen aan de aanvraagheader. Daarom worden voor ASP.NET webAPI's die worden beschermd met OAuth 2.0, tokens aan toonder beschouwd als een verdediging tegen CSRF-aanvallen. Houd er rekening mee dat als het MVC-gedeelte van de toepassing formulierenverificatie gebruikt (d.w.z. cookies gebruikt), antivervalsingstokens moeten worden gebruikt door de MVC-webapp. |

### <a name="example"></a>Voorbeeld
De Web API moet worden geïnformeerd om alleen te vertrouwen op tokens aan toonder en niet op cookies. Het kan worden gedaan door `WebApiConfig.Register` de volgende configuratie in methode:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Met de suppressdefaulthostverificatiemethode moet web-API elke verificatie negeren die plaatsvindt voordat de aanvraag de Web API-pijplijn bereikt, hetzij door IIS, hetzij door OWIN middleware. Op die manier kunnen we web-API beperken om alleen tokens aan toonder te verifiëren.
