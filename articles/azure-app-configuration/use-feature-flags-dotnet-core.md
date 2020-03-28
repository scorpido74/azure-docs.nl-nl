---
title: Zelfstudie voor het gebruik van functievlaggen in een .NET Core-app | Microsoft Documenten
description: In deze zelfstudie leert u hoe u functievlaggen implementeert in .NET Core-apps.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 3182961f928a9befc5a55fb6d58e22c74ba81089
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79473435"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Zelfstudie: Functievlaggen gebruiken in een ASP.NET Core-app

De .NET Core Feature Management-bibliotheken bieden idiomatische ondersteuning voor het implementeren van functievlaggen in een .NET- of ASP.NET Core-toepassing. Met deze bibliotheken u declaratief functievlaggen aan uw code toevoegen, zodat `if` u niet alle instructies handmatig hoeft te schrijven.

De functiesbeheerbibliotheken beheren ook de levenscyclus van functievlag's achter de schermen. De bibliotheken vernieuwen en cachevlagen of garanderen bijvoorbeeld dat een vlagstatus onveranderlijk is tijdens een aanvraaggesprek. Daarnaast biedt de ASP.NET Core-bibliotheek out-of-the-box-integraties, waaronder MVC-controlleracties, weergaven, routes en middleware.

Met [functievlaggen toevoegen aan een ASP.NET Core-app Snelstart](./quickstart-feature-flag-aspnet-core.md) worden verschillende manieren weergegeven om functievlaggen toe te voegen in een ASP.NET Core-toepassing. Deze zelfstudie legt deze methoden in meer detail uit. Zie de [documentatie ASP.NET Core-functiebeheer](https://go.microsoft.com/fwlink/?linkid=2091410)voor een volledige referentie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Voeg functievlaggen toe in belangrijke delen van uw toepassing om de beschikbaarheid van functies te beheren.
> * Integreer met app-configuratie wanneer u deze gebruikt om functievlaggen te beheren.

## <a name="set-up-feature-management"></a>Functiebeheer instellen

Voeg een verwijzing `Microsoft.FeatureManagement` toe naar het NuGet-pakket om de .NET Core-functiebeheerder te gebruiken.
    
De .NET Core `IFeatureManager` feature manager krijgt functievlaggen van het native configuratiesysteem van het framework. Als gevolg hiervan u de functievlaggen van uw toepassing definiëren met behulp van elke configuratiebron die .NET Core ondersteunt, inclusief het lokale *appsettings.json-bestand* of omgevingsvariabelen. `IFeatureManager`vertrouwt op .NET Core afhankelijkheidsinjectie. U de functiesbeheerservices registreren met behulp van standaardconventies:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Standaard haalt de functiebeheerder functievlaggen `"FeatureManagement"` op uit het gedeelte van de configuratiegegevens van .NET Core. In het volgende voorbeeld moet de functiebeheerder `"MyFeatureFlags"` lezen uit een andere sectie die in plaats daarvan wordt genoemd:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Als u filters gebruikt in uw functievlaggen, moet u een extra bibliotheek toevoegen en deze registreren. In het volgende voorbeeld ziet u hoe `PercentageFilter`u een ingebouwd functiefilter gebruikt, genaamd:

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

We raden u aan functievlaggen buiten de toepassing te houden en deze afzonderlijk te beheren. Hiermee u op elk gewenst moment vlagstatussen wijzigen en deze wijzigingen meteen in de toepassing laten toepassen. App-configuratie biedt een centrale plaats voor het ordenen en beheren van al uw functievlaggen via een speciale portal-gebruikersinterface. App-configuratie levert de vlaggen ook rechtstreeks via de .NET Core-clientbibliotheken aan uw toepassing.

De eenvoudigste manier om uw ASP.NET Core-toepassing aan `Microsoft.Azure.AppConfiguration.AspNetCore`te sluiten op app-configuratie is via de configuratieprovider. Volg deze stappen om dit NuGet-pakket te gebruiken.

1. Open *Program.cs* bestand en voeg de volgende code toe.

   ```csharp
   using Microsoft.Extensions.Configuration.AzureAppConfiguration;

   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
       WebHost.CreateDefaultBuilder(args)
              .ConfigureAppConfiguration((hostingContext, config) => {
                  var settings = config.Build();
                  config.AddAzureAppConfiguration(options => {
                      options.Connect(settings["ConnectionStrings:AppConfig"])
                             .UseFeatureFlags();
                   });
              })
              .UseStartup<Startup>();
   ```

2. Open *Startup.cs* en `Configure` werk de methode bij om een middleware toe te voegen, zodat de waarden van de functievlag met een terugkerend interval kunnen worden vernieuwd terwijl de ASP.NET Core-web-app aanvragen blijft ontvangen.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Functievlagwaarden zullen naar verwachting in de loop van de tijd veranderen. Standaard worden de waarden van de functievlag gedurende een periode van 30 seconden in de cache opgeslagen, dus een vernieuwingsbewerking die wordt geactiveerd wanneer de middleware een aanvraag ontvangt, zou de waarde niet bijwerken totdat de waarde in de cache is verlopen. In de volgende code ziet u hoe u de vervaldatum `options.UseFeatureFlags()` van de cache of het steminterval wijzigt in 5 minuten in het gesprek.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Verklaring functievlag

Elke functievlag bestaat uit twee delen: een naam en een lijst met een of *on* meer filters die worden `True`gebruikt om te evalueren of de status van een functie is ingeschakeld (dat wil zeggen wanneer de waarde ervan is). Een filter definieert een use case voor wanneer een functie moet worden ingeschakeld.

Wanneer een functievlag meerdere filters heeft, wordt de filterlijst in volgorde doorlopen totdat een van de filters bepaalt dat de functie moet zijn ingeschakeld. Op dat moment is de functievlag *ingeschakeld*en worden de resterende filterresultaten overgeslagen. Als er geen filter aangeeft dat de functie moet zijn ingeschakeld, is de functievlag *uitgeschakeld.*

De functiebeheerder ondersteunt *appsettings.json* als configuratiebron voor functievlaggen. In het volgende voorbeeld ziet u hoe u functievlaggen in een JSON-bestand instelt:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Volgens de `FeatureManagement` conventie wordt het gedeelte van dit JSON-document gebruikt voor instellingen voor functievlag. In het vorige voorbeeld worden drie functievlaggen `EnabledFor` weergegeven met hun filters die in de eigenschap zijn gedefinieerd:

* `FeatureA`is *ingeschakeld.*
* `FeatureB`is *uitgeschakeld.*
* `FeatureC`hiermee wordt `Percentage` een `Parameters` filter opgegeven met de naam een eigenschap. `Percentage`is een configureerbaar filter. Hiermee geeft u een kans van 50 `FeatureC` procent op dat de vlag moet worden ingeschakeld . *on* `Percentage`

## <a name="feature-flag-references"></a>Verwijzingen naar functievlag

Zodat u eenvoudig verwijzen naar functievlaggen in `enum` code, moet u ze definiëren als variabelen:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Functievlagcontroles

Het basispatroon van functiebeheer is om eerst te controleren of een functievlag is ingesteld *op*. Als dat het zo is, voert de functiebeheerder vervolgens de acties uit die de functie bevat. Bijvoorbeeld:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Afhankelijkheidsinjectie

In ASP.NET Core MVC heeft u `IFeatureManager` toegang tot de functiebeheerder via afhankelijkheidsinjectie:

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Acties voor controller

In MVC-controllers gebruikt `FeatureGate` u het kenmerk om te bepalen of een hele controllerklasse of een specifieke actie is ingeschakeld. De `HomeController` volgende `FeatureA` controller moet *ingeschakeld* zijn voordat een actie die de controllerklasse bevat kan worden uitgevoerd:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

De `Index` volgende `FeatureA` actie moet worden *ingeschakeld* voordat deze kan worden uitgevoerd:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Wanneer een MVC-controller of actie wordt geblokkeerd omdat de `IDisabledFeaturesHandler` vlag van de controlerende functie *is uitgeschakeld,* wordt een geregistreerde interface aangeroepen. De `IDisabledFeaturesHandler` standaardinterface retourneert een statuscode van 404 naar de client zonder antwoordbody.

## <a name="mvc-views"></a>MVC-weergaven

In MVC-weergaven `<feature>` u een tag gebruiken om inhoud weer te geven op basis van de vraag of een functievlag is ingeschakeld:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Als u alternatieve inhoud wilt weergeven wanneer niet aan de vereisten is voldaan, kan het `negate` kenmerk worden gebruikt.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

De `<feature>` functietag kan ook worden gebruikt om inhoud weer te geven als alle functies in een lijst zijn ingeschakeld.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC-filters

U MVC-filters zo instellen dat ze worden geactiveerd op basis van de status van een functievlag. Met de volgende code wordt `SomeMvcFilter`een MVC-filter met de naam . Dit filter wordt alleen geactiveerd binnen `FeatureA` de MVC-pijplijn als deze is ingeschakeld. Deze mogelijkheid is `IAsyncActionFilter`beperkt tot . 

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="middleware"></a>Middleware

U functievlaggen ook gebruiken om toepassingsbranches en middleware voorwaardelijk toe te voegen. Met de volgende code wordt een middleware-component alleen in de aanvraagpijplijn ingevoegd wanneer deze `FeatureA` is ingeschakeld:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Deze code bouwt af op de meer generieke mogelijkheid om de hele toepassing te vertakken op basis van een functievlag:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u functievlaggen `Microsoft.FeatureManagement` in uw ASP.NET Core-toepassing implementeert met behulp van de bibliotheken. Zie de volgende bronnen voor meer informatie over ondersteuning voor functiebeheer in ASP.NET kern- en app-configuratie:

* [voorbeeldcode van ASP.NET Core-functievlag](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Documentatie van Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Functievlaggen beheren](./manage-feature-flags.md)
