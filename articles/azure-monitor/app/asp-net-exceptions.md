---
title: Fouten en uitzonderingen diagnosticeren met Azure Application Insights
description: Maak uitzonderingen van ASP.NET-apps samen met aanvraagtelemetrie.
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 9f24f09e7d2ef0a3e5f3a8f6546a9115118473ab
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892339"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Uitzonderingen in web-apps diagnosticeren met Application Insights
Uitzonderingen in uw live web-app worden gerapporteerd door [Application Insights](../../azure-monitor/app/app-insights-overview.md). U mislukte aanvragen correleren met uitzonderingen en andere gebeurtenissen op zowel de client als de server, zodat u snel de oorzaken diagnosticeren.

## <a name="set-up-exception-reporting"></a>Uitzonderingsrapportage instellen
* Uitzonderingen laten melden vanuit uw server-app:
  * Azure-webapps: de [extensie Toepassingsinzichten](../../azure-monitor/app/azure-web-apps.md) toevoegen
  * Azure VM- en Azure-apps voor virtuele machineschaal instellen IIS-gehoste apps: De [extensie Voor toepassingsbewaking](../../azure-monitor/app/azure-vm-vmss-apps.md) toevoegen
  * [Application Insights SDK](../../azure-monitor/app/asp-net.md) installeren in uw app-code, of
  * IIS-webservers: Application [Insights Agent uitvoeren;](../../azure-monitor/app/monitor-performance-live-website-now.md) Of
  * Java-web-apps: [Java-agent](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) inschakelen
* Installeer het [JavaScript-fragment](../../azure-monitor/app/javascript.md) op uw webpagina's om uitzonderingen in de browser te maken.
* In sommige toepassingskaders of met sommige instellingen moet u enkele extra stappen nemen om meer uitzonderingen te maken:
  * [Webformulieren](#web-forms)
  * [MVC](#mvc)
  * [Web API 1.*](#web-api-1x)
  * [Web API 2.*](#web-api-2x)
  * [WCF](#wcf)

  Dit artikel is specifiek gericht op .NET Framework-apps vanuit een codevoorbeeldperspectief. Sommige methoden die werken voor .NET Framework zijn verouderd in de .NET Core SDK. Raadpleeg de [.NET Core SDK-documentatie](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) als u een .NET Core-app hebt.

## <a name="diagnosing-exceptions-using-visual-studio"></a>Uitzonderingen diagnosticeren met Visual Studio
Open de app-oplossing in Visual Studio om te helpen bij het opsporen van debuggen.

Voer de app uit, op uw server of op uw ontwikkelingsmachine met behulp van F5.

Open het zoekvenster Voor toepassingsinzichten in Visual Studio en stel het in om gebeurtenissen in uw app weer te geven. Terwijl u debugging, u dit doen door te klikken op de knop Inzichten van toepassingen.

![Klik met de rechtermuisknop op het project en kies Application Insights, Open.](./media/asp-net-exceptions/34.png)

U het rapport filteren om alleen uitzonderingen weer te geven.

*Geen uitzonderingen laten zien? Zie [Uitzonderingen vastleggen](#exceptions).*

Klik op een uitzonderingsrapport om het stackspoor weer te geven.
Klik op een regelverwijzing in de stacktracering om het relevante codebestand te openen.

In de code wordt opgemerkt dat CodeLens gegevens over de uitzonderingen weergeeft:

![CodeLens melding van uitzonderingen.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Fouten diagnosticeren met de Azure-portal
Application Insights wordt geleverd met een samengestelde APM-ervaring om fouten in uw bewaakte toepassingen te diagnosticeren. Als u wilt starten, klikt u op de optie Fouten in het resourcemenu Toepassingsinzichten in de sectie Onderzoeken.
U ziet een weergave op volledig scherm die u de trends in het percentage fouten voor uw aanvragen laat zien, hoeveel van deze aanvragen mislukken en hoeveel gebruikers worden beïnvloed. Aan de rechterkant ziet u enkele van de meest nuttige distributies die specifiek zijn voor de geselecteerde niet-geselecteerde bewerking, waaronder top drie antwoordcodes, top drie uitzonderingstypen en top drie ontbrekende afhankelijkheidstypen.

![Drietolieweergave voor fouten (tabblad bewerkingen)](./media/asp-net-exceptions/failures0719.png)

Met één klik u vervolgens representatieve voorbeelden voor elk van deze subsets van bewerkingen bekijken. Om uitzonderingen te diagnosticeren, u met name klikken op het aantal uitzonderingen dat moet worden gepresenteerd met het tabblad End-to-end transactiedetails, zoals deze:

![Tabblad End-to-end transactiedetails](./media/asp-net-exceptions/end-to-end.png)

**Als alternatief** u in plaats van te kijken naar uitzonderingen op een specifieke niet-bewerking, beginnen met de algemene weergave van uitzonderingen door over te schakelen naar het tabblad Uitzonderingen bovenaan. Hier ziet u alle uitzonderingen die zijn verzameld voor uw bewaakte app.

*Geen uitzonderingen laten zien? Zie [Uitzonderingen vastleggen](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Aangepaste tracerings- en logboekgegevens
Als u diagnostische gegevens wilt verzamelen die specifiek zijn voor uw app, u code invoegen om uw eigen telemetriegegevens te verzenden. Dit wordt weergegeven in diagnostische zoekopdrachten naast de aanvraag, paginaweergave en andere automatisch verzamelde gegevens.

U hebt verschillende mogelijkheden:

* [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) wordt meestal gebruikt voor het bewaken van gebruikspatronen, maar de gegevens die worden verzendt, worden ook weergegeven onder Aangepaste gebeurtenissen in diagnostisch zoeken. Gebeurtenissen krijgen een naam en kunnen tekenreekseigenschappen en numerieke statistieken bevatten waarop u [uw diagnostische zoekopdrachten](../../azure-monitor/app/diagnostic-search.md)filteren.
* [Met TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) u langere gegevens verzenden, zoals POST-informatie.
* [TrackException()](#exceptions) stuurt stacksporen. [Meer over uitzonderingen](#exceptions).
* Als u al een logboekframework zoals Log4Net of NLog gebruikt, u [deze logboeken vastleggen](asp-net-trace-logs.md) en bekijken in diagnostische zoekopdrachten naast aanvraag- en uitzonderingsgegevens.

Als u deze gebeurtenissen wilt bekijken, opent [u Zoeken](../../azure-monitor/app/diagnostic-search.md) in het linkermenu, selecteert u de vervolgkeuzemenu **Gebeurtenistypen**en kiest u Aangepaste gebeurtenis, Trace er of uitzondering.

![Analyseren](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Als uw app veel telemetriegegevens genereert, beperkt de adaptieve steekproefmodule automatisch het volume dat naar de portal wordt verzonden door alleen een representatieve fractie van de gebeurtenissen te sturen. Gebeurtenissen die deel uitmaken van dezelfde bewerking worden geselecteerd of uitgeschakeld als een groep, zodat u navigeren tussen gerelateerde gebeurtenissen. [Meer informatie over sampling.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Post-gegevens aanvragen bekijken
Details van het verzoek bevatten niet de gegevens die in een postgesprek naar uw app worden verzonden. Ga als volgt te werk om deze gegevens te laten melden:

* [Installeer de SDK](../../azure-monitor/app/asp-net.md) in uw toepassingsproject.
* Code invoegen in uw toepassing om [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)te bellen. Verzend de POST-gegevens in de parameter bericht. Er is een limiet aan de toegestane grootte, dus je moet proberen om alleen de essentiële gegevens te verzenden.
* Wanneer u een mislukte aanvraag onderzoekt, zoekt u de bijbehorende sporen.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a>Uitzonderingen en gerelateerde diagnostische gegevens vastleggen
In eerste instantie ziet u in de portal niet alle uitzonderingen die storingen in uw app veroorzaken. U ziet eventuele browseruitzonderingen (als u de [JavaScript SDK](../../azure-monitor/app/javascript.md) op uw webpagina's gebruikt). Maar de meeste server uitzonderingen worden gevangen door IIS en je moet een beetje code te schrijven om ze te zien.

U kunt:

* **Log uitzonderingen expliciet** in door code in uitzonderingshandlers in te voegen om de uitzonderingen te melden.
* **Maak automatisch uitzonderingen** door uw ASP.NET framework te configureren. De noodzakelijke toevoegingen zijn verschillend voor verschillende soorten kader.

## <a name="reporting-exceptions-explicitly"></a>Uitzonderingen expliciet rapporteren
De eenvoudigste manier is om een aanroep in te voegen op TrackException() in een uitzonderingshandler.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

De eigenschappen en metingenparameters zijn optioneel, maar zijn handig voor [het filteren en toevoegen van](../../azure-monitor/app/diagnostic-search.md) extra informatie. Als u bijvoorbeeld een app hebt waarmee meerdere games kunnen worden uitgevoerd, u alle uitzonderingsrapporten met betrekking tot een bepaalde game vinden. U zoveel items toevoegen als u wilt aan elk woordenboek.

## <a name="browser-exceptions"></a>Browseruitzonderingen
De meeste browseruitzonderingen worden gerapporteerd.

Als uw webpagina scriptbestanden van netwerken voor inhoudsbezorging of andere ```crossorigin="anonymous"```domeinen bevat, moet u ervoor zorgen dat uw scripttag het kenmerk heeft en dat de server [CORS-headers](https://enable-cors.org/)verzendt. Hiermee u een stacktrace en details krijgen voor onverwerkte JavaScript-uitzonderingen uit deze bronnen.

## <a name="reuse-your-telemetry-client"></a>Uw telemetrieclient opnieuw gebruiken

> [!NOTE]
> TelemetryClient wordt aanbevolen om eenmalig te worden geinstantieerd en opnieuw te worden gebruikt gedurende de hele levensduur van een toepassing.

Hieronder vindt u een voorbeeld met telemetrieClient correct.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Webformulieren
Voor webformulieren kan de HTTP-module de uitzonderingen verzamelen wanneer er geen omleidingen zijn geconfigureerd met Aangepaste Fouten.

Maar als u actieve omleidingen hebt, voegt u de volgende regels toe aan de functie Application_Error in Global.asax.cs. (Voeg een Global.asax-bestand toe als u er nog geen hebt.)

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```
## <a name="mvc"></a>MVC
Vanaf Application Insights Web SDK versie 2.6 (beta3 en hoger) verzamelt Application Insights automatisch onverwerkte uitzonderingen die in de MVC 5+-controllersmethoden worden gegooid. Als u eerder een aangepaste handler hebt toegevoegd om dergelijke uitzonderingen bij te houden (zoals beschreven in volgende voorbeelden), u deze verwijderen om dubbele tracking van uitzonderingen te voorkomen.

Er zijn een aantal gevallen die de uitzonderingsfilters niet kunnen verwerken. Bijvoorbeeld:

* Uitzonderingen gegooid van controller constructors.
* Uitzonderingen die worden gegooid van berichthandlers.
* Uitzonderingen die tijdens het routeren worden gegooid.
* Uitzonderingen die worden gegooid tijdens de serialisatie van antwoordinhoud.
* Uitzondering gegooid tijdens het opstarten van de toepassing.
* Uitzondering die in achtergrondtaken wordt geworpen.

Alle uitzonderingen die door de toepassing *worden verwerkt,* moeten nog steeds handmatig worden bijgehouden.
Niet-verwerkte uitzonderingen die afkomstig zijn van controllers resulteren meestal in 500 "Internal Server Error" reactie. Als een dergelijk antwoord handmatig is opgebouwd als gevolg van afgehandelde uitzondering (of helemaal geen `ResultCode` uitzondering) wordt het bijgehouden in overeenkomstige aanvraagtelemetrie met 500, maar Application Insights SDK kan de overeenkomstige uitzondering niet bijhouden.

### <a name="prior-versions-support"></a>Ondersteuning voor eerdere versies
Als u MVC 4 (en voorafgaand) van Application Insights Web SDK 2.5 (en voorafgaand) gebruikt, raadpleegt u de volgende voorbeelden om uitzonderingen bij te houden.

Als de configuratie `Off` [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) is, zijn uitzonderingen beschikbaar voor de [HTTP-module](https://msdn.microsoft.com/library/ms178468.aspx) om te verzamelen. Als dit echter `RemoteOnly` (standaard) `On`is, of, wordt de uitzondering gewist en niet beschikbaar voor Application Insights om automatisch te verzamelen. U dat oplossen door de [klasse System.Web.Mvc.HandleErrorAttribute](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)te overschrijven en de overschreven klasse toe te passen zoals weergegeven voor de verschillende MVC-versies hieronder ([GitHub-bron):](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Vervang het kenmerk HandleError door uw nieuwe kenmerk in uw controllers.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Monster](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registreer `AiHandleErrorAttribute` als een globaal filter in Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Monster](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
AiHandleErrorAttribute registreren als een globaal filter in FilterConfig.cs:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Monster](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Web-API
Vanaf Application Insights Web SDK versie 2.6 (beta3 en hoger) verzamelt Application Insights onverwerkte uitzonderingen die automatisch in de verwerkingsmethoden worden gegooid voor WebAPI 2+. Als u eerder een aangepaste handler hebt toegevoegd om dergelijke uitzonderingen bij te houden (zoals beschreven in volgende voorbeelden), u deze verwijderen om dubbele tracking van uitzonderingen te voorkomen.

Er zijn een aantal gevallen die de uitzonderingsfilters niet kunnen verwerken. Bijvoorbeeld:

* Uitzonderingen gegooid van controller constructors.
* Uitzonderingen die worden gegooid van berichthandlers.
* Uitzonderingen die tijdens het routeren worden gegooid.
* Uitzonderingen die worden gegooid tijdens de serialisatie van antwoordinhoud.
* Uitzondering gegooid tijdens het opstarten van de toepassing.
* Uitzondering die in achtergrondtaken wordt geworpen.

Alle uitzonderingen die door de toepassing *worden verwerkt,* moeten nog steeds handmatig worden bijgehouden.
Niet-verwerkte uitzonderingen die afkomstig zijn van controllers resulteren meestal in 500 "Internal Server Error" reactie. Als een dergelijk antwoord handmatig is opgebouwd als gevolg van afgehandelde uitzondering (of helemaal geen `ResultCode` uitzondering) wordt het bijgehouden in een overeenkomstige aanvraagtelemetrie met 500, maar Application Insights SDK kan de overeenkomstige uitzondering niet bijhouden.

### <a name="prior-versions-support"></a>Ondersteuning voor eerdere versies
Als u WebAPI 1 (en voorafgaand) van Application Insights Web SDK 2.5 (en voorafgaand) gebruikt, raadpleegt u de volgende voorbeelden om uitzonderingen bij te houden.

#### <a name="web-api-1x"></a>Web-API 1.x
Systeem.web.http.filters.ExceptionFilterKenmerk overschrijven:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

U dit overschreven kenmerk toevoegen aan specifieke controllers of toevoegen aan de globale filterconfiguratie in de klasse WebApiConfig:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Monster](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web-API 2.x
Voeg een implementatie van IExceptionLogger toe:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Voeg dit toe aan de services in WebApiConfig:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Monster](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Als alternatieven zou je kunnen:

1. Vervang de enige ExceptionHandler door een aangepaste implementatie van IExceptionHandler. Dit wordt alleen aangeroepen wanneer het framework nog steeds kan kiezen welk antwoordbericht u wilt verzenden (niet wanneer de verbinding bijvoorbeeld wordt afgebroken)
2. Uitzonderingsfilters (zoals beschreven in de sectie op Web API 1.x-controllers hierboven) - niet in alle gevallen worden aangeroepen.

## <a name="wcf"></a>WCF
Voeg een klasse toe die kenmerk uitbreidt en IErrorHandler en IServiceBehavior implementeert.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Monster](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Prestatiemeteritems voor uitzondering
Als u de Application Insights Agent op uw server hebt [geïnstalleerd,](../../azure-monitor/app/monitor-performance-live-website-now.md) u een grafiek met het aantal uitzonderingen krijgen, gemeten door .NET. Dit omvat zowel afgehandelde als onbehandelde .NET-uitzonderingen.

Open een tabblad Metrisch Verkenner, voeg een nieuwe grafiek toe en selecteer **Uitzonderingssnelheid**onder Prestatiemeteritems.

Het .NET-framework berekent de snelheid door het aantal uitzonderingen in een interval te tellen en te delen door de lengte van het interval.

Dit is anders dan het aantal 'Uitzonderingen' berekend door de Portal Application Insights-portal die TrackException-rapporten telt. De bemonsteringsintervallen zijn verschillend en de SDK verzendt geen TrackException-rapporten voor alle afgehandelde en onverwerkte uitzonderingen.

## <a name="next-steps"></a>Volgende stappen
* [Rest-, SQL- en andere aanroepen naar afhankelijkheden controleren](../../azure-monitor/app/asp-net-dependencies.md)
* [Laadtijden van pagina's, browseruitzonderingen en AJAX-aanroepen controleren](../../azure-monitor/app/javascript.md)
* [Prestatiemeteritems controleren](../../azure-monitor/app/performance-counters.md)
