---
title: Linux ASP.NET Core-Apps configureren
description: Meer informatie over het configureren van een vooraf gemaakte ASP.NET Core-container voor uw app. In dit artikel vindt u de meest voorkomende configuratie taken.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255913"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Een Linux ASP.NET Core-app configureren voor Azure App Service

ASP.NET Core-apps moeten worden geïmplementeerd als gecompileerde binaire bestanden. Het Visual Studio-hulp programma voor publiceren bouwt de oplossing en implementeert vervolgens de gecompileerde binaire bestanden rechtstreeks, terwijl de App Service implementatie-engine de code opslagplaats eerst implementeert en vervolgens de binaire bestanden compileert.

Deze hand leiding bevat belang rijke concepten en instructies voor ASP.NET Core ontwikkel aars die een ingebouwde Linux-container gebruiken in App Service. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [ASP.net core Snelstartgids](quickstart-dotnetcore.md) en [ASP.net core SQL database zelf studie](tutorial-dotnetcore-sqldb-app.md) .

## <a name="show-net-core-version"></a>.NET core-versie weer geven

Als u de huidige versie van .NET core wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Als u alle ondersteunde .NET Core-versies wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>.NET core-versie instellen

Voer de volgende opdracht uit in de [Cloud shell](https://shell.azure.com) om de .net core-versie in te stellen op 2,1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>Bouw automatisering aanpassen

Als u uw app implementeert met git-of ZIP-pakketten waarvoor build Automation is ingeschakeld, wordt de App Service stapsgewijs door de volgende reeks gemaakt:

1. Voer een aangepast script uit als dit wordt opgegeven door `PRE_BUILD_SCRIPT_PATH`.
1. Voer `dotnet restore` uit om NuGet-afhankelijkheden te herstellen.
1. Voer `dotnet publish` uit om een binair bestand voor productie te maken.
1. Voer een aangepast script uit als dit wordt opgegeven door `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND` en `POST_BUILD_COMMAND` zijn omgevings variabelen die standaard leeg zijn. Definieer `PRE_BUILD_COMMAND`voor het uitvoeren van opdrachten die vooraf zijn gebouwd. Als u opdrachten na het bouwen wilt uitvoeren, definieert u `POST_BUILD_COMMAND`.

In het volgende voor beeld worden de twee variabelen opgegeven voor een reeks opdrachten, gescheiden door komma's.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Zie [Oryx-configuratie](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)voor meer omgevings variabelen voor het aanpassen van het bouwen van Automation.

Zie Oryx-documentatie voor meer informatie over hoe App Service worden uitgevoerd en gebouwd ASP.NET Core apps in Linux [: hoe .net core-apps zijn gedetecteerd en gebouwd](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

## <a name="access-environment-variables"></a>Omgevingsvariabelen openen

In App Service kunt u de [app-instellingen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) buiten uw app-code instellen. Vervolgens kunt u ze in een wille keurige klasse openen met behulp van het standaard ASP.NET Core afhankelijkheids injectie patroon:

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

Als u een app-instelling met dezelfde naam in App Service en in *appSettings. json*configureert, heeft de app service-waarde voor rang boven de waarde *appSettings. json* . Met de lokale waarde *appSettings. json* kunt u de app lokaal fouten opsporen, maar met de app service waarde kunt u de app uitvoeren in het product met productie-instellingen. Verbindings reeksen werken op dezelfde manier. Op deze manier kunt u uw toepassings geheimen buiten uw code opslagplaats houden en toegang krijgen tot de juiste waarden zonder uw code te wijzigen.

## <a name="get-detailed-exceptions-page"></a>Pagina gedetailleerde uitzonde ringen ophalen

Wanneer uw ASP.NET-app een uitzonde ring genereert in het Visual Studio-fout opsporingsprogramma, wordt een gedetailleerde uitzonderings pagina weer gegeven in de browser, maar in App Service die pagina wordt vervangen door een algemene **HTTP 500-** fout of er is **een fout opgetreden tijdens het verwerken van uw aanvraag.** Bericht. Als u de gedetailleerde uitzonderings pagina in App Service wilt weer geven, voegt u de instelling van de app `ASPNETCORE_ENVIRONMENT` toe aan uw app door de volgende opdracht uit te voeren in de <a target="_blank" href="https://shell.azure.com" >Cloud shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS-sessie detecteren

In App Service vindt [SSL-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) plaats in de load balancers voor het netwerk, zodat alle HTTPS-aanvragen uw app bereiken als niet-versleutelde HTTP-aanvragen. Als uw app-logica moet weten of de gebruikers aanvragen zijn versleuteld of niet, configureert u de doorgestuurde headers-middleware in *Startup.cs*:

- Configureer de middleware met [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) om de `X-Forwarded-For` en `X-Forwarded-Proto` headers in `Startup.ConfigureServices`door te sturen.
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

## <a name="deploy-multi-project-solutions"></a>Oplossingen voor meerdere projecten implementeren

Wanneer u een ASP.NET-opslag plaats implementeert in de implementatie-engine met een *. csproj* -bestand in de hoofdmap, implementeert de engine het project. Wanneer u een ASP.NET-opslag plaats met een *. SLN* -bestand in de hoofdmap implementeert, kiest de engine de eerste website of het webtoepassings project dat wordt gevonden als de app app service. Het is mogelijk dat de engine niet het gewenste project selecteert.

Als u een oplossing voor meerdere projecten wilt implementeren, kunt u op twee verschillende manieren het project opgeven dat in App Service moet worden gebruikt:

### <a name="using-deployment-file"></a>Implementatie bestand gebruiken

Voeg een *. implementatie* bestand toe aan de hoofdmap van de opslag plaats en voeg de volgende code toe:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>App-instellingen gebruiken

Voeg in de <a target="_blank" href="https://shell.azure.com">Azure Cloud shell</a>een app-instelling toe aan uw app service app door de volgende CLI-opdracht uit te voeren. Vervang *\<app-naam >* , *\<resource-group-name >* en *\<project naam >* met de juiste waarden.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-sessie openen in browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: app ASP.NET Core met SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](app-service-linux-faq.md)
