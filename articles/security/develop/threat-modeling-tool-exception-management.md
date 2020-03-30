---
title: Exception Management - Microsoft Threat Modeling Tool - Azure | Microsoft Documenten
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
ms.openlocfilehash: b8fad566b54ab645660011ad3188394b6f8190b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728073"
---
# <a name="security-frame-exception-management--mitigations"></a>Beveiligingskader: Exception Management | Mitigaties 
| Product/service | Artikel |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF- Neem geen serviceDebug-knooppunt op in configuratiebestand](#servicedebug)</li><li>[WCF- Neem geen serviceMetadata-knooppunt op in configuratiebestand](#servicemetadata)</li></ul> |
| **Web-API** | <ul><li>[Ervoor zorgen dat de juiste afhandeling van uitzonderingen wordt uitgevoerd in ASP.NET Web API](#exception)</li></ul> |
| **Webtoepassing** | <ul><li>[Beveiligingsgegevens niet blootstellen in foutberichten](#messages)</li><li>[Pagina Standaardfoutafhandeling implementeren](#default)</li><li>[Implementatiemethode instellen op Detailhandel in IIS](#deployment)</li><li>[Uitzonderingen moeten veilig mislukken](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF- Neem geen serviceDebug-knooppunt op in configuratiebestand

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Stappen** | WcF-services (Windows Communication Framework) kunnen worden geconfigureerd om foutopsporingsgegevens bloot te leggen. Foutopsporingsgegevens mogen niet worden gebruikt in productieomgevingen. De `<serviceDebug>` tag definieert of de functie foutopsporingsinformatie is ingeschakeld voor een WCF-service. Als het kenmerk ExceptionDetailInFaults op true is ingesteld, worden uitzonderingsgegevens van de toepassing teruggestuurd naar clients. Aanvallers kunnen gebruikmaken van de aanvullende informatie die ze krijgen van debugging output om aanvallen gericht op het kader, database, of andere middelen die worden gebruikt door de toepassing te monteren. |

### <a name="example"></a>Voorbeeld
Het volgende configuratiebestand `<serviceDebug>` bevat de tag: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Foutopsporingsgegevens in de service uitschakelen. Dit kan worden bereikt `<serviceDebug>` door de tag uit het configuratiebestand van uw toepassing te verwijderen. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF- Neem geen serviceMetadata-knooppunt op in configuratiebestand

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Generiek, NET Framework 3 |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Stappen** | Het openbaar maken van informatie over een service kan aanvallers waardevol inzicht geven in hoe ze de service kunnen misbruiken. De `<serviceMetadata>` tag maakt de publicatiefunctie voor metagegevens mogelijk. Metagegevens van service kunnen gevoelige informatie bevatten die niet openbaar toegankelijk mag zijn. Sta ten minste vertrouwde gebruikers alleen toe toegang te krijgen tot de metagegevens en ervoor te zorgen dat onnodige informatie niet wordt blootgesteld. Beter nog, volledig uitschakelen van de mogelijkheid om metadata te publiceren. Een veilige WCF-configuratie `<serviceMetadata>` bevat de tag niet. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Ervoor zorgen dat de juiste afhandeling van uitzonderingen wordt uitgevoerd in ASP.NET Web API

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC 5, MVC 6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Exception Handling in ASP.NET Web API](https://www.asp.net/web-api/overview/error-handling/exception-handling), [Modelvalidatie in ASP.NET Web API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Stappen** | De meeste niet-gevangen uitzonderingen in ASP.NET Web API worden standaard vertaald in een HTTP-antwoord met statuscode`500, Internal Server Error`|

### <a name="example"></a>Voorbeeld
Als u de statuscode wilt `HttpResponseException` beheren die door de API wordt geretourneerd, u worden gebruikt zoals hieronder wordt weergegeven: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Voorbeeld
Voor verdere controle over het `HttpResponseMessage` uitzonderingsantwoord kan de klasse worden gebruikt zoals hieronder wordt weergegeven: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
Om onverwerkte uitzonderingen te vangen `HttpResponseException`die niet van het type zijn, kunnen uitzonderingsfilters worden gebruikt. Uitzonderingsfilters `System.Web.Http.Filters.IExceptionFilter` implementeren de interface. De eenvoudigste manier om een uitzonderingsfilter `System.Web.Http.Filters.ExceptionFilterAttribute` te schrijven, is door uit de klasse af te leiden en de Methode OnException te overschrijven. 

### <a name="example"></a>Voorbeeld
Hier is een `NotImplementedException` filter dat uitzonderingen `501, Not Implemented`omzet in HTTP-statuscode: 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Er zijn verschillende manieren om een filter voor web-API-uitzondering te registreren:
- Door actie
- Per controller
- Wereldwijd

### <a name="example"></a>Voorbeeld
Als u het filter wilt toepassen op een specifieke actie, voegt u het filter als kenmerk toe aan de actie: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Voorbeeld
Als u het filter wilt toepassen `controller`op alle acties op `controller` een , voegt u het filter als kenmerk toe aan de klasse: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Voorbeeld
Als u het filter globaal wilt toepassen op alle Web `GlobalConfiguration.Configuration.Filters` API-controllers, voegt u een instantie van het filter toe aan de verzameling. Uitzonderingsfilters in deze verzameling zijn van toepassing op elke actie van een Web API-controller. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Voorbeeld
Voor modelvalidatie kan de modelstatus worden doorgegeven aan de methode CreateErrorResponse, zoals hieronder wordt weergegeven: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Controleer de koppelingen in de sectie Referenties voor meer informatie over uitzonderlijke afhandeling en modelvalidatie in ASP.NET Web API 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Beveiligingsgegevens niet blootstellen in foutberichten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Generieke foutberichten worden rechtstreeks aan de gebruiker verstrekt zonder gevoelige toepassingsgegevens op te nemen. Voorbeelden van gevoelige gegevens zijn:</p><ul><li>Servernamen</li><li>Verbindingsreeksen</li><li>Gebruikersnamen</li><li>Wachtwoorden</li><li>SQL-procedures</li><li>Details van dynamische SQL-fouten</li><li>Stacktrace en coderegels</li><li>Variabelen die zijn opgeslagen in het geheugen</li><li>Stations- en maplocaties</li><li>Installatiepunten voor toepassingen</li><li>Configuratie-instellingen voor host</li><li>Andere interne toepassingsgegevens</li></ul><p>Als u alle fouten in een toepassing overschrijft en algemene foutberichten verstrekt, en aangepaste fouten binnen IIS mogelijk maakt, wordt informatieopenbaar gemaakt. SQL Server-database en .NET Exception-afhandeling zijn, naast andere foutafhandelingsarchitecturen, bijzonder breed en uiterst nuttig voor een kwaadwillende gebruiker die uw toepassing profileert. Geef niet direct de inhoud weer van een klasse die is afgeleid van de klasse .NET Exception en zorg ervoor dat u de juiste uitzonderingsafhandeling hebt, zodat een onverwachte uitzondering niet per ongeluk rechtstreeks naar de gebruiker wordt verhoogd.</p><ul><li>Geef algemene foutmeldingen rechtstreeks aan de gebruiker die specifieke details die rechtstreeks in het uitzonderingsbericht zijn gevonden, abstraheer</li><li>De inhoud van een .NET-uitzonderingsklasse niet rechtstreeks aan de gebruiker weergeven</li><li>Alle foutberichten overvullen en zo nodig de gebruiker informeren via een algemeen foutbericht dat naar de toepassingsclient wordt verzonden</li><li>Stel de inhoud van de klasse Exception niet rechtstreeks bloot `.ToString()`aan de gebruiker, met name de retourwaarde van of de waarden van de eigenschappen Message of StackTrace. Log deze informatie veilig aan en geef een onschuldiger bericht weer aan de gebruiker</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Pagina Standaardfoutafhandeling implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Het dialoogvenster Instellingen voor ASP.NET-foutpagina's bewerken](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Stappen** | <p>Wanneer een ASP.NET toepassing uitvalt en een http/1.x 500 interne serverfout veroorzaakt, of wanneer een functieconfiguratie (zoals Filtering aanvragen) voorkomt dat een pagina wordt weergegeven, wordt een foutbericht gegenereerd. Beheerders kunnen kiezen of de toepassing een vriendelijk bericht aan de client moet weergeven, een gedetailleerd foutbericht aan de client of een gedetailleerd foutbericht aan localhost. De `<customErrors>` tag in het web.config heeft drie modi:</p><ul><li>**Op:** Hiermee geeft u op dat aangepaste fouten zijn ingeschakeld. Als er geen standaardredirect-kenmerk is opgegeven, zien gebruikers een algemene fout. De aangepaste fouten worden getoond aan de externe clients en aan de lokale host</li><li>**Uit:** Hiermee geeft u op dat aangepaste fouten zijn uitgeschakeld. De gedetailleerde ASP.NET fouten worden getoond aan de externe clients en aan de lokale host</li><li>**Alleen op afstand:** Hiermee geeft u op dat aangepaste fouten alleen worden weergegeven aan de externe clients en dat ASP.NET fouten worden weergegeven aan de lokale host. Dit is de standaardwaarde</li></ul><p>Open `web.config` het bestand voor de toepassing/site en `<customErrors mode="RemoteOnly" />` `<customErrors mode="On" />` zorg ervoor dat de tag een of andere tag heeft of gedefinieerd.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Implementatiemethode instellen op Detailhandel in IIS

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [implementatie-element (ASP.NET instellingenschema)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Stappen** | <p>De `<deployment retail>` switch is bedoeld voor gebruik door productie-IIS-servers. Deze schakelaar wordt gebruikt om toepassingen te helpen draaien met de best mogelijke prestaties en de minst mogelijke beveiligingsinformatie lekken door het uitschakelen van de toepassing het vermogen om trace output te genereren op een pagina, het uitschakelen van de mogelijkheid om gedetailleerde foutmeldingen weer te geven aan eindgebruikers en het uitschakelen van de foutopsporingsschakelaar.</p><p>Vaak worden switches en opties die op ontwikkelaars zijn gericht, zoals mislukte het traceren van aanvragen en foutopsporing, ingeschakeld tijdens actieve ontwikkeling. Het wordt aanbevolen dat de implementatiemethode op elke productieserver wordt ingesteld op de detailhandel. open het bestand machine.config `<deployment retail="true" />` en zorg ervoor dat dit op true blijft.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>Uitzonderingen moeten veilig mislukken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Veilig falen](https://www.owasp.org/index.php/Fail_securely) |
| **Stappen** | Toepassing moet veilig mislukken. Elke methode die een Booleaanse waarde retourneert, op basis waarvan bepaalde beslissing wordt genomen, moet een uitzonderingsblok zorgvuldig hebben gemaakt. Er zijn veel logische fouten als gevolg van welke beveiligingsproblemen kruipen in, wanneer de uitzondering blok is achteloos geschreven.|

### <a name="example"></a>Voorbeeld
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
De bovenstaande methode zal altijd true retourneren, als er een uitzondering optreedt. Als de eindgebruiker een misvormde URL verstrekt, `Uri()` die de browser respecteert, maar de constructor niet, zal dit een uitzondering gooien en wordt het slachtoffer naar de geldige maar misvormde URL geleid. 
