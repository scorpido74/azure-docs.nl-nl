---
title: Windows ASP.NET Core-Apps configureren
description: Meer informatie over het configureren van een ASP.NET Core-app in de systeem eigen Windows-exemplaren van App Service. In dit artikel worden de meest algemene configuratietaken beschreven.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5819fc5b2d6e64d1812dacd88a2a4f840f6e03c5
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908091"
---
# <a name="configure-a-windows-aspnet-core-app-for-azure-app-service"></a>Een Windows ASP.NET Core-app configureren voor Azure App Service

> [!NOTE]
> Zie voor ASP.NET in .NET Framework [een ASP.net-app configureren voor Azure app service](configure-language-dotnet-framework.md)

ASP.NET Core-apps moeten worden geïmplementeerd op Azure App Service als gecompileerde binaire bestanden. Het Visual Studio-hulp programma voor publiceren bouwt de oplossing en implementeert vervolgens de gecompileerde binaire bestanden rechtstreeks, terwijl de App Service implementatie-engine de code opslagplaats eerst implementeert en vervolgens de binaire bestanden compileert. Zie [Configure a Linux ASP.net core app for Azure app service](containers/configure-language-dotnetcore.md)voor meer informatie over Linux-apps.

Deze hand leiding bevat belang rijke concepten en instructies voor ASP.NET Core ontwikkel aars. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [ASP.net Snelstartgids](app-service-web-get-started-dotnet.md) en [ASP.net core met SQL database zelf studie](app-service-web-tutorial-dotnetcore-sqldb.md) .

## <a name="show-supported-net-core-runtime-versions"></a>Ondersteunde .NET core runtime-versies weer geven

In App Service hebben de Windows-exemplaren al alle ondersteunde .NET Core-versies geïnstalleerd. Als u de .NET core runtime-en SDK-versies die beschikbaar zijn voor u wilt weer geven, gaat u naar `https://<app-name>.scm.azurewebsites.net/DebugConsole` en voert u de volgende opdracht uit in de browser-console:

```azurecli-interactive
dotnet --info
```

## <a name="set-net-core-version"></a>.NET core-versie instellen

Stel het doel raamwerk in het project bestand voor uw ASP.NET Core-project in. Zie voor meer informatie [de .net core-versie selecteren die u wilt gebruiken in de](https://docs.microsoft.com/dotnet/core/versions/selection) documentatie van .net core.

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App Service kunt u [app-instellingen configureren](configure-common.md#configure-app-settings) buiten uw app-code. Vervolgens kunt u ze in een wille keurige klasse openen met behulp van het standaard ASP.NET Core afhankelijkheids injectie patroon:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Als u een app-instelling met dezelfde naam in App Service configureert en in *appsettings.jsop*bijvoorbeeld de app service-waarde heeft voor rang *op deappsettings.js* waarde. Met de lokale *appsettings.jsop* waarde kunt u de app lokaal fouten opsporen, maar met de app service waarde kunt u de app uitvoeren in het product met productie-instellingen. Verbindings reeksen werken op dezelfde manier. Op deze manier kunt u uw toepassings geheimen buiten uw code opslagplaats houden en toegang krijgen tot de juiste waarden zonder uw code te wijzigen.

> [!NOTE]
> Houd rekening met de [hiërarchische configuratie gegevens](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) in *appsettings.jsop* wordt geopend met behulp `:` van het scheidings teken dat standaard is voor .net core. Als u een specifieke hiërarchische configuratie-instelling in App Service wilt overschrijven, stelt u de naam van de app-instelling in op dezelfde gescheiden indeling in de sleutel. u kunt het volgende voor beeld uitvoeren in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Oplossingen voor meerdere projecten implementeren

Wanneer een Visual Studio-oplossing meerdere projecten bevat, omvat het Visual Studio-publicatie proces al het project selecteren dat u wilt implementeren. Wanneer u implementeert voor de App Service-implementatie-engine, zoals met git of met ZIP-implementatie, terwijl build Automation is ingeschakeld, kiest de App Service-implementatie-engine de eerste website of het webtoepassings project dat wordt gevonden als App Service app. U kunt opgeven welk project App Service moet gebruiken door de app-instelling op te geven `PROJECT` . Voer bijvoorbeeld het volgende in het [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

ASP.NET Core biedt een [ingebouwde logboek registratie provider voor app service](https://docs.microsoft.com/aspnet/core/fundamentals/logging/#azure-app-service). Voeg in *Program.cs* van uw project de provider toe aan uw toepassing via de `ConfigureLogging` uitbreidings methode, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

U kunt vervolgens logboeken configureren en genereren met het [standaard .net-kern patroon](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Zie [probleem oplossing ASP.net Core op Azure app service en IIS](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis) voor meer informatie over het oplossen van problemen met ASP.net core-apps in app service.

## <a name="get-detailed-exceptions-page"></a>Pagina gedetailleerde uitzonde ringen ophalen

Wanneer uw ASP.NET Core-app een uitzonde ring genereert in het Visual Studio-fout opsporingsprogramma, wordt een gedetailleerde uitzonderings pagina weer gegeven in de browser, maar in App Service die pagina wordt vervangen door een algemene **HTTP 500-** fout of **Er is een fout opgetreden tijdens het verwerken van uw aanvraag.** . Als u de gedetailleerde uitzonderings pagina in App Service wilt weer geven, voegt `ASPNETCORE_ENVIRONMENT` u de app-instelling toe aan uw app door de volgende opdracht uit te voeren in de <a target="_blank" href="https://shell.azure.com" >Cloud shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS-sessie detecteren

In App Service vindt [SSL-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) plaats in de load balancers voor het netwerk, zodat alle HTTPS-aanvragen uw app bereiken als niet-versleutelde HTTP-aanvragen. Als uw app-logica moet weten of de gebruikers aanvragen zijn versleuteld of niet, configureert u de doorgestuurde headers-middleware in *Startup.cs*:

- Configureer de middleware met [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) voor het door sturen `X-Forwarded-For` van de en- `X-Forwarded-Proto` headers in `Startup.ConfigureServices` .
- Voeg persoonlijke IP-adresbereiken toe aan de bekende netwerken, zodat de middleware de App Service load balancer kan vertrouwen.
- Roep de methode [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) aan in `Startup.Configure` voordat u andere middlewares aanroept.

Als u alle drie de elementen samen plaatst, ziet uw code eruit als in het volgende voor beeld:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Zie [ASP.net core configureren voor het werken met proxy servers en load balancers](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: app ASP.NET Core met SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
