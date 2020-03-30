---
title: Configuratie per omgeving gebruiken
titleSuffix: Azure App Configuration
description: Labels gebruiken om configuratiewaarden per omgeving op te geven
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056804"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Labels gebruiken om verschillende configuraties voor verschillende omgevingen in te schakelen

Veel toepassingen moeten verschillende configuraties gebruiken voor verschillende omgevingen. Stel dat een toepassing een configuratiewaarde heeft die de verbindingstekenreeks definieert die moet worden gebruikt voor de back-enddatabase. De ontwikkelaars van de toepassing gebruiken een andere database dan die welke in de productie wordt gebruikt. De databaseverbindingstekenreeks die door de toepassing wordt gebruikt, moet worden gewijzigd wanneer de toepassing van ontwikkeling naar productie gaat.

In Azure App-configuratie u *labels* gebruiken om verschillende waarden voor dezelfde sleutel te definiëren. U bijvoorbeeld één sleutel definiëren met verschillende waarden voor *ontwikkeling* en *productie.* U opgeven welk label(en) moet worden geladen wanneer u verbinding maakt met app-configuratie.

Om deze functionaliteit aan te tonen, wijzigen we de web-app die is gemaakt in [Quickstart: Een ASP.NET Core-app maken met Azure App-configuratie](./quickstart-aspnet-core-app.md) om verschillende configuratie-instellingen te gebruiken voor ontwikkeling versus productie. Vul de quickstart snel in voordat u verdergaat.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Een label opgeven bij het toevoegen van een configuratiewaarde

Ga in de Azure-portal naar **Configuration Explorer** en zoek de *TestApp:Instellingen:FontColor-toets* die u in de quickstart hebt gemaakt. Selecteer het contextmenu en klik op **Waarde toevoegen**.

> [!div class="mx-imgBorder"]
> ![Menu-item Waarde toevoegen](media/labels-add-value.png)

Voer **in** het scherm Waarde toevoegen een **waarde** van **rood** en een **label** van **ontwikkeling**in . **Inhoudstype** leeg laten. Selecteer **Toepassen**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Configuratiewaarden laden met een opgegeven label

Azure App Configuration laadt standaard alleen configuratiewaarden zonder label. Als u labels hebt gedefinieerd voor uw configuratiewaarden, wilt u het label(s) opgeven dat u moet gebruiken wanneer u verbinding maakt met app-configuratie.

In het laatste gedeelte hebt u een andere configuratiewaarde voor de *ontwikkelomgeving* gemaakt. U gebruikt `HostingEnvironment.EnvironmentName` de variabele om dynamisch te bepalen in welke omgeving de app momenteel wordt uitgevoerd. Zie [Meerdere omgevingen gebruiken in ASP.NET Core](/aspnet/core/fundamentals/environments)voor meer informatie.

Regelconfiguratiewaarden met het label dat overeenkomt met de `Select` huidige omgeving door de omgevingsnaam door te geven aan de methode:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> Het bovenstaande codefragment laadt de verbindingstekenreeks app-configuratie uit een omgevingsvariabele genaamd `AppConfigConnectionString`. Zorg ervoor dat deze omgevingsvariabele goed is ingesteld.

De `Select` methode wordt twee keer aangeroepen. De eerste keer laadt het configuratiewaarden zonder label. Vervolgens worden configuratiewaarden geladen met het label dat overeenkomt met de huidige omgeving. Deze omgevingsspecifieke waarden overschrijven alle bijbehorende waarden zonder label. U hoeft niet voor elke sleutel omgevingsspecifieke waarden te definiëren. Als een sleutel geen waarde heeft met een label dat overeenkomt met de huidige omgeving, wordt de waarde zonder label gebruikt.

## <a name="testing-in-different-environments"></a>Testen in verschillende omgevingen

Als u de verschillende configuratiewaarden wilt testen, opent u het `launchSettings.json` bestand onder de `Properties` map. Zoek `config` de `profiles`vermelding onder . Stel `environmentVariables` in de `ASPNETCORE_ENVIRONMENT` sectie `Production`de variabele in op .

Met de nieuwe waarden stel je je toepassing in en voer je deze uit.

```dotnetcli
dotnet build
dotnet run
```

Gebruik een webbrowser om `http://localhost:5000`naar . U zult merken dat de lettertypekleur zwart is.

![Webtoepassing die wordt uitgevoerd met productieconfiguratie](media/labels-website-prod.png)

Nu `launchSettings.json` bijwerken om `ASPNETCORE_ENVIRONMENT` de `Development`variabele in te stellen op . Voer `dotnet run` opnieuw uit. U zult merken dat de lettertypekleur nu rood is. Dit komt omdat de toepassing `TestApp:Settings:FontColor` nu de `Development` waarde gebruikt van die het label heeft. Alle andere configuratiewaarden blijven hetzelfde als hun productiewaarden.

![Webtoepassing die wordt uitgevoerd met ontwikkelingsconfiguratie](media/labels-website-dev.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Configuratie in ASP.NET Core](/aspnet/core/fundamentals/configuration/)
