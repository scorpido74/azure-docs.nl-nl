---
title: Linux ASP.NET Core-apps configureren
description: Meer informatie over het configureren van een vooraf gebouwde ASP.NET Core-container voor uw app. In dit artikel worden de meest voorkomende configuratietaken weergegeven.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255913"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Een Linux-ASP.NET Core-app configureren voor Azure App Service

ASP.NET Core-apps moeten worden geïmplementeerd als gecompileerde binaire bestanden. De Visual Studio publishing tool bouwt de oplossing en implementeert vervolgens de gecompileerde binaire bestanden rechtstreeks, terwijl de App Service-implementatieengine de coderepository eerst implementeert en vervolgens de binaire bestanden compileert.

Deze handleiding biedt belangrijke concepten en instructies voor ASP.NET Core-ontwikkelaars die een ingebouwde Linux-container in App Service gebruiken. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [ASP.NET Core en](quickstart-dotnetcore.md) ASP.NET Core met SQL [Database-zelfstudie.](tutorial-dotnetcore-sqldb-app.md)

## <a name="show-net-core-version"></a>.NET Core-versie weergeven

Als u de huidige .NET Core-versie wilt weergeven, voert u de volgende opdracht uit in de [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Voer de volgende opdracht uit in de Cloud [Shell](https://shell.azure.com)om alle ondersteunde .NET Core-versies weer te geven:

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>.NET Core-versie instellen

Voer de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com) om de .NET Core-versie in te stellen op 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>Build-automatisering aanpassen

Als u uw app implementeert met Git- of zip-pakketten waarbij buildautomatisering is ingeschakeld, worden de automatiseringsstappen van de App-service door de volgende reeks doorlopen:

1. Aangepaste script uitvoeren `PRE_BUILD_SCRIPT_PATH`als dit is opgegeven door .
1. Uitvoeren `dotnet restore` om NuGet-afhankelijkheden te herstellen.
1. Uitvoeren `dotnet publish` om een binaire voor de productie te bouwen.
1. Aangepaste script uitvoeren `POST_BUILD_SCRIPT_PATH`als dit is opgegeven door .

`PRE_BUILD_COMMAND`en `POST_BUILD_COMMAND` zijn standaard lege omgevingsvariabelen. Als u pre-build-opdrachten `PRE_BUILD_COMMAND`wilt uitvoeren, definieert u . Als u opdrachten na het `POST_BUILD_COMMAND`bouwen wilt uitvoeren, definieert u .

In het volgende voorbeeld worden de twee variabelen opgeeft aan een reeks opdrachten, gescheiden door komma's.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Zie [Oryx-configuratie](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)voor extra omgevingsvariabelen om de buildautomatisering aan te passen.

Zie [Oryx-documentatie: Hoe .NET Core-apps worden gedetecteerd en gebouwd voor](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)meer informatie over hoe App Service ASP.NET Core-apps in Linux draait en bouwt.

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App-service u [app-instellingen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) buiten uw app-code instellen. Vervolgens u ze in elke klasse openen met behulp van het standaard patroon van ASP.NET Core-afhankelijkheidsinjectie:

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
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Als u bijvoorbeeld een app-instelling met dezelfde naam configureert in App Service en *appsettings.json,* heeft de waarde app-service bijvoorbeeld voorrang op de waarde *appsettings.json.* Met de lokale *appsettings.json-waarde* u de app lokaal debuggen, maar met de waarde app-service u de app in product uitvoeren met productie-instellingen. Verbindingstekenreeksen werken op dezelfde manier. Op deze manier u uw toepassingsgeheimen buiten uw coderepository bewaren en toegang krijgen tot de juiste waarden zonder uw code te wijzigen.

## <a name="get-detailed-exceptions-page"></a>Pagina Gedetailleerde uitzonderingen opdoen

Wanneer uw ASP.NET-app een uitzondering genereert in de foutopsporing van Visual Studio, wordt in de browser een gedetailleerde uitzonderingspagina weergegeven, maar in App Service wordt die pagina vervangen door een algemene **HTTP 500-fout** of **is er een fout opgetreden tijdens het verwerken van uw aanvraag.** . Als u de gedetailleerde uitzonderingspagina `ASPNETCORE_ENVIRONMENT` in App-service wilt weergeven, voegt u de app-instelling toe aan uw app door de volgende opdracht uit te voeren in de <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS-sessie detecteren

In App Service vindt [SSL-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) plaats in de load balancers voor het netwerk, zodat alle HTTPS-aanvragen uw app bereiken als niet-versleutelde HTTP-aanvragen. Als uw app-logica moet weten of de gebruikersverzoeken zijn versleuteld of niet, configureert u de Middleware voor doorgestuurde kopteksten in *Startup.cs:*

- Configureer de middleware met [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) om de `X-Forwarded-For` kopteksten door `X-Forwarded-Proto` te sturen in `Startup.ConfigureServices`.
- Voeg privé-IP-adresbereiken toe aan de bekende netwerken, zodat de middleware de load balancer van de App Service kan vertrouwen.
- Roep de methode [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) in `Startup.Configure` voordat u andere middlewares aanroept.

Als u alle drie de elementen bij elkaar zet, ziet uw code eruit als het volgende voorbeeld:

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

Zie [ASP.NET Kern configureren om te werken met proxyservers en load balancers](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)voor meer informatie.

## <a name="deploy-multi-project-solutions"></a>Multi-projectoplossingen implementeren

Wanneer u een ASP.NET repository implementeert naar de implementatieengine met een *.csproj-bestand* in de hoofdmap, implementeert de engine het project. Wanneer u een ASP.NET repository implementeert met een *.sln-bestand* in de hoofdmap, kiest de engine de eerste website of webtoepassingsproject die wordt gevonden als de App Service-app. Het is mogelijk voor de motor niet om het project dat u wilt kiezen.

Als u een oplossing voor meerdere projecten wilt implementeren, u het project op twee verschillende manieren in App-service gebruiken:

### <a name="using-deployment-file"></a>Het .deployment-bestand gebruiken

Voeg een *.deployment-bestand* toe aan de hoofdmap van de opslagplaats en voeg de volgende code toe:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>App-instellingen gebruiken

Voeg in de <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>een app-instelling toe aan uw App Service-app door de volgende opdracht CLI uit te voeren. Vervang * \<>, * * \<>met de naam resourcegroep *en * \<>projectnaam* door de juiste waarden.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-sessie openen in de browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: ASP.NET Core-app met SQL-database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](app-service-linux-faq.md)
