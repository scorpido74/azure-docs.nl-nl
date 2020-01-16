---
title: Configuratie tolerantie en herstel na nood gevallen Azure-app
description: Lean implementeren van tolerantie en herstel na nood gevallen met Azure-app configuratie.
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: cd706e42eff19ebacf92b77d2438af80dc16a5fb
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028241"
---
# <a name="resiliency-and-disaster-recovery"></a>Tolerantie en herstel na noodgevallen

Azure-app configuratie is momenteel een regionale service. Elke configuratie opslag wordt in een bepaalde Azure-regio gemaakt. Een onderbreking voor de hele regio is van invloed op alle archieven in die regio. App-configuratie biedt geen automatische failover naar een andere regio. Dit artikel bevat algemene richt lijnen over hoe u meerdere configuratie archieven kunt gebruiken in azure-regio's om de geo-tolerantie van uw toepassing te verg Roten.

## <a name="high-availability-architecture"></a>Architectuur met hoge Beschik baarheid

Als u de redundantie tussen regio's wilt realiseren, moet u meerdere app-configuratie archieven maken in verschillende regio's. Bij deze installatie heeft uw toepassing ten minste één extra configuratie opslag om terug te vallen als het primaire archief niet toegankelijk is. In het volgende diagram ziet u de topologie tussen uw toepassing en de primaire en secundaire configuratie archieven:

![Geografisch redundante opslag](./media/geo-redundant-app-configuration-stores.png)

Uw toepassing laadt de configuratie van de primaire en secundaire archieven parallel. Dit verhoogt de kans dat de configuratie gegevens correct worden opgehaald. U bent verantwoordelijk voor het bewaren van de gegevens in beide archieven. In de volgende secties wordt uitgelegd hoe u geografische tolerantie kunt bouwen in uw toepassing.

## <a name="failover-between-configuration-stores"></a>Failover tussen configuratie archieven

Technisch gesp roken voert uw toepassing geen failover uit. Er wordt geprobeerd om dezelfde set configuratie gegevens uit twee app-configuratie archieven tegelijk op te halen. Rang Schik uw code zo dat deze eerst wordt geladen vanuit de secundaire Store en vervolgens op de primaire opslag. Deze aanpak zorgt ervoor dat de configuratie gegevens in de primaire Store voor rang krijgen wanneer deze beschikbaar zijn. Het volgende code fragment laat zien hoe u deze rang schikking kunt implementeren in de .NET Core SLI:

#### <a name="net-core-2xtabcore2x"></a>[.NET Core 2. x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Let op de `optional` para meter die is door gegeven aan de functie `AddAzureAppConfiguration`. Als deze para meter is ingesteld op `true`, kan de toepassing niet worden voortgezet als de functie geen configuratie gegevens kan laden.

## <a name="synchronization-between-configuration-stores"></a>Synchronisatie tussen configuratie archieven

Het is belang rijk dat uw Geo-redundante configuratie alle dezelfde set gegevens bevat. U kunt de functie **exporteren** in app-configuratie gebruiken om gegevens van het primaire archief te kopiëren naar de secundaire opslag op aanvraag. Deze functie is beschikbaar via zowel de Azure Portal als de CLI.

Vanuit het Azure Portal kunt u een wijziging naar een andere configuratie Store pushen door de volgende stappen uit te voeren.

1. Ga naar het tabblad **importeren/exporteren** en selecteer > app- **configuratie** **exporteren** > **doel** > **Selecteer een resource**.

2. Op de nieuwe blade die wordt geopend, geeft u het abonnement, de resource groep en de resource naam van uw secundaire archief op en selecteert u vervolgens **Toep assen**.

3. De gebruikers interface wordt bijgewerkt, zodat u kunt kiezen welke configuratie gegevens u wilt exporteren naar uw secundaire archief. U kunt de standaard waarde tijd laten staan en beide **van label** en **naar label** op dezelfde waarde instellen. Selecteer **Toepassen**.

4. Herhaal de vorige stappen voor alle configuratie wijzigingen.

Gebruik de Azure CLI om dit export proces te automatiseren. De volgende opdracht laat zien hoe u één configuratie wijziging van de primaire opslag naar de secundaire exporteert:

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u uw toepassing kunt uitbreiden om geo-tolerantie te krijgen tijdens de runtime voor app-configuratie. U kunt ook configuratie gegevens van de app-configuratie insluiten tijdens de build-of implementatie tijd. Zie [integreren met een CI/cd-pijp lijn](./integrate-ci-cd-pipeline.md)voor meer informatie.

