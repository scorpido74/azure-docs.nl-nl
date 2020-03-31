---
title: Gebruikerscontext-iD's om activiteit bij te houden - Azure Application Insights
description: Houd bij hoe gebruikers door uw service gaan door elk van hen een unieke, permanente id-tekenreeks toe te kennen in Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: beb5a0f7ad3733aaf12b0880af4fba23a705a7e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670930"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Gebruikerscontext-i-d's verzenden om gebruikservaringen in Te schakelen in Azure Application Insights

## <a name="tracking-users"></a>Gebruikers volgen

Application Insights stelt u in staat om uw gebruikers te controleren en te volgen via een reeks productgebruikstools:

- [Gebruikers, sessies, gebeurtenissen](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Trechters](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Retentie](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) Cohorten
- [Werkmappen](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Om bij te houden wat een gebruiker in de loop van de tijd doet, heeft Application Insights een ID voor elke gebruiker of sessie nodig. Neem de volgende id's op in elke aangepaste gebeurtenis- of paginaweergave.

- Gebruikers, trechters, retentie en cohorten: voeg gebruikers-id toe.
- Sessies: Voeg sessie-id toe.

> [!NOTE]
> Dit is een geavanceerd artikel waarin de handmatige stappen voor het bijhouden van gebruikersactiviteit met Application Insights. Bij veel webtoepassingen **zijn deze stappen mogelijk niet vereist**, omdat de standaard-server-side SDK's in combinatie met de [Client/Browser-side JavaScript SDK](../../azure-monitor/app/website-monitoring.md )vaak voldoende zijn om de activiteit van gebruikers automatisch bij te houden. Als u naast de SDK aan de serverzijde geen [client-side monitoring](../../azure-monitor/app/website-monitoring.md ) hebt geconfigureerd, moet u dat eerst doen en testen of de analysetools voor gebruikersgedrag presteren zoals verwacht.

## <a name="choosing-user-ids"></a>Gebruikersnamen kiezen

Gebruikers-id's moeten blijven bestaan in gebruikerssessies om bij te houden hoe gebruikers zich in de loop van de tijd gedragen. Er zijn verschillende benaderingen voor het voortbestaan van de ID.

- Een definitie van een gebruiker die u al in uw service hebt.
- Als de service toegang heeft tot een browser, kan deze de browser een cookie geven met een ID erin. De ID blijft bestaan zolang de cookie in de browser van de gebruiker blijft.
- Indien nodig u elke sessie een nieuwe id gebruiken, maar de resultaten over gebruikers zijn beperkt. U bijvoorbeeld niet zien hoe het gedrag van een gebruiker in de loop van de tijd verandert.

De ID moet een Guid of een andere string complex genoeg om elke gebruiker uniek te identificeren. Het kan bijvoorbeeld een lang willekeurig getal zijn.

Als de ID persoonlijk identificeerbare informatie over de gebruiker bevat, is het geen geschikte waarde om naar Application Insights als gebruikers-id te verzenden. U een dergelijke id als [een geverifieerde gebruikersnaam](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users)verzenden, maar deze voldoet niet aan de gebruikers-id-vereiste voor gebruiksscenario's.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET-apps: de gebruikerscontext instellen in een ITelemetryInitializer

Maak een telemetrie initialisator, zoals [hier](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer)in detail beschreven . Geef de sessie-ID door de aanvraagtelemetrie en stel de Context.User.Id en de Context.Session.Id in.

In dit voorbeeld wordt de gebruikersnaam ingesteld op een id die na de sessie verloopt. Gebruik indien mogelijk een gebruikersnaam die voor alle sessies geldt.

### <a name="telemetry-initializer"></a>Telemetrieinitialisator

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Als u gebruikservaringen wilt inschakelen, begint u met het verzenden van [aangepaste gebeurtenissen](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) of [paginaweergaven.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Als u al aangepaste gebeurtenissen of paginaweergaven verzendt, raadpleegt u de hulpprogramma's Voor gebruik om te leren hoe gebruikers uw service gebruiken.
    - [Overzicht van gebruik](usage-overview.md)
    - [Gebruikers, sessies en gebeurtenissen](usage-segmentation.md)
    - [Trechters](usage-funnels.md)
    - [Retentie](usage-retention.md)
    - [Werkmappen](../../azure-monitor/app/usage-workbooks.md)
