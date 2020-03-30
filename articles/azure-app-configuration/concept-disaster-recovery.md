---
title: Tolerantie voor azure-app-configuratie en herstel na noodgevallen
description: Lean hoe u tolerantie en herstel na noodgevallen implementeert met Azure App-configuratie.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 96ef09ac081aa328014217592a7fcd3ed6314c0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523761"
---
# <a name="resiliency-and-disaster-recovery"></a>Tolerantie en herstel na noodgevallen

Momenteel is Azure App Configuration een regionale service. Elk configuratiearchief wordt gemaakt in een bepaalde Azure-regio. Een regio-brede storing treft alle winkels in die regio. App-configuratie biedt geen automatische failover naar een andere regio. In dit artikel vindt u algemene richtlijnen voor het gebruik van meerdere configuratieopslag in Azure-regio's om de geotolerantie van uw toepassing te vergroten.

## <a name="high-availability-architecture"></a>Architectuur met hoge beschikbaarheid

Als u redundantie in meerdere regio's wilt realiseren, moet u meerdere app-configuratiewinkels in verschillende regio's maken. Met deze instelling heeft uw toepassing ten minste één extra configuratiearchief om op terug te vallen als de primaire winkel ontoegankelijk wordt. In het volgende diagram wordt de topologie tussen uw toepassing en de primaire en secundaire configuratieopgeslagen geïllustreerd:

![Georedundante winkels](./media/geo-redundant-app-configuration-stores.png)

Uw toepassing laadt de configuratie van zowel de primaire als secundaire opslag parallel. Hierdoor vergroot u de kans op het succesvol verkrijgen van de configuratiegegevens. U bent verantwoordelijk voor het synchroon houden van de gegevens in beide winkels. In de volgende secties wordt uitgelegd hoe u geo-tolerantie in uw toepassing inbouwen.

## <a name="failover-between-configuration-stores"></a>Failover tussen configuratieopslag

Technisch gezien voert uw toepassing geen failover uit. Het probeert dezelfde set configuratiegegevens tegelijkertijd op te halen uit twee app-configuratieopslag. Schik uw code zo dat deze eerst vanuit de secundaire winkel wordt geladen en vervolgens de primaire winkel. Deze aanpak zorgt ervoor dat de configuratiegegevens in het primaire archief voorrang hebben wanneer deze beschikbaar zijn. In het volgende codefragment ziet u hoe u deze regeling implementeren in .NET Core:

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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

Let `optional` op de `AddAzureAppConfiguration` parameter die in de functie wordt doorgegeven. Wanneer deze `true`parameter is ingesteld, voorkomt deze parameter dat de toepassing niet wordt voortgezet als de functie geen configuratiegegevens kan laden.

## <a name="synchronization-between-configuration-stores"></a>Synchronisatie tussen configuratieopslag

Het is belangrijk dat uw georedundante configuratiewinkels allemaal dezelfde set gegevens hebben. U de functie **Exporteren** in app-configuratie gebruiken om gegevens uit het primaire archief naar het secundaire on-demand te kopiëren. Deze functie is beschikbaar via zowel de Azure-portal als de CLI.

Vanuit de Azure-portal u een wijziging naar een ander configuratiearchief pushen door deze stappen te volgen.

1. Ga naar het tabblad **Importeren/exporteren** en**Target** >  **selecteer** > **Doel voor app-configuratie** > **exporteren Selecteer een bron**.

1. Geef in het nieuwe blad dat wordt geopend de abonnements-, resourcegroep- en resourcenaam van uw secundaire winkel op en selecteer **Vervolgens Toepassen**.

1. De gebruikersinterface wordt bijgewerkt, zodat u kiezen welke configuratiegegevens u wilt exporteren naar uw secundaire winkel. U de standaardtijdwaarde zoals deze is en zowel **Van label** als **Label** instellen op dezelfde waarde. Selecteer **Toepassen**.

1. Herhaal de vorige stappen voor alle configuratiewijzigingen.

Als u dit exportproces wilt automatiseren, gebruikt u de Azure CLI. In de volgende opdracht ziet u hoe u één configuratiewijziging exporteert van het primaire archief naar het secundaire:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u uw toepassing uitbreiden om geo-tolerantie te bereiken tijdens runtime voor app-configuratie. U ook configuratiegegevens van app-configuratie insluiten op het bouwen of implementeren. Zie [Integreren met een CI/CD-pijplijn](./integrate-ci-cd-pipeline.md)voor meer informatie.
