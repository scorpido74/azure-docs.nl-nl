---
title: Telemetriekanalen in Azure Application Insights | Microsoft Documenten
description: Telemetriekanalen aanpassen in Azure Application Insights SDKs voor .NET en .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275696"
---
# <a name="telemetry-channels-in-application-insights"></a>Telemetriekanalen in Application Insights

Telemetriekanalen zijn een integraal onderdeel van de [Azure Application Insights SDKs.](../../azure-monitor/app/app-insights-overview.md) Zij beheren buffering en overdracht van telemetrie naar de Application Insights-service. De .NET- en .NET Core-versies van de SDK's hebben twee ingebouwde telemetriekanalen: `InMemoryChannel` en `ServerTelemetryChannel`. In dit artikel wordt elk kanaal in detail beschreven, inclusief het aanpassen van kanaalgedrag.

## <a name="what-are-telemetry-channels"></a>Wat zijn telemetriekanalen?

Telemetriekanalen zijn verantwoordelijk voor het bufferen van telemetrie-items en het verzenden ervan naar de Application Insights-service, waar ze zijn opgeslagen voor query's en analyses. Een telemetriekanaal is een [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) klasse die de interface implementeert.

De `Send(ITelemetry item)` methode van een telemetriekanaal wordt aangeroepen nadat alle telemetrieinitialisators en telemetrieprocessors worden aangeroepen. Dus, alle items die door een telemetrieprocessor zijn gedropt, bereiken het kanaal niet. `Send()`stuurt de items meestal niet direct naar de back-end. Meestal buffert het ze in het geheugen en stuurt ze in batches, voor een efficiënte transmissie.

[Live Metrics Stream](live-stream.md) heeft ook een aangepast kanaal dat de live streaming van telemetrie aandrijft. Dit kanaal is onafhankelijk van het reguliere telemetriekanaal en dit document is er niet op van toepassing.

## <a name="built-in-telemetry-channels"></a>Ingebouwde telemetriekanalen

De Application Insights .NET en .NET Core SDKs worden geleverd met twee ingebouwde kanalen:

* `InMemoryChannel`: Een lichtgewicht kanaal dat items buffert in het geheugen totdat ze worden verzonden. Items worden gebufferd in het geheugen en gespoeld eens in de 30 seconden, of wanneer 500 items worden gebufferd. Dit kanaal biedt minimale betrouwbaarheidsgaranties omdat het niet opnieuw probeert telemetrie te verzenden na een storing. Dit kanaal houdt ook geen items op schijf, dus alle niet-verzonden items gaan permanent verloren bij het afsluiten van de toepassing (sierlijk of niet). Dit kanaal implementeert een `Flush()` methode die kan worden gebruikt om alle in-memory telemetrie-items synchroon te forceren. Dit kanaal is zeer geschikt voor kortlopende toepassingen waarbij een synchrone flush ideaal is.

    Dit kanaal maakt deel uit van het grotere Microsoft.ApplicationInsights NuGet-pakket en is het standaardkanaal dat de SDK gebruikt wanneer er niets anders is geconfigureerd.

* `ServerTelemetryChannel`: Een geavanceerder kanaal met een nieuw beleid en de mogelijkheid om gegevens op een lokale schijf op te slaan. Dit kanaal probeert telemetrie te verzenden als er tijdelijke fouten optreden. Dit kanaal maakt ook gebruik van lokale schijfopslag om items op schijf te houden tijdens netwerkuitval of hoge telemetrievolumes. Vanwege deze mechanismen voor het opnieuw proberen en lokale schijfopslag wordt dit kanaal als betrouwbaarder beschouwd en wordt het aanbevolen voor alle productiescenario's. Dit kanaal is de standaardinstelling voor [ASP.NET-](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) en [ASP.NET Core-toepassingen](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) die zijn geconfigureerd volgens de officiële documentatie. Dit kanaal is geoptimaliseerd voor serverscenario's met langlopende processen. De [`Flush()`](#which-channel-should-i-use) methode die door dit kanaal wordt geïmplementeerd, is niet synchroon.

    Dit kanaal wordt verzonden als het Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet-pakket en wordt automatisch verkregen wanneer u microsoft.applicationInsights.Web of Microsoft.ApplicationInsights.AspNetCore NuGet gebruikt Pakket.

## <a name="configure-a-telemetry-channel"></a>Een telemetriekanaal configureren

U configureert een telemetriekanaal door het in te stellen op de actieve telemetrieconfiguratie. Voor ASP.NET toepassingen bestaat de configuratie uit `TelemetryConfiguration.Active`het instellen `ApplicationInsights.config`van de telemetriekanaalinstantie op of door het wijzigen van . Voor ASP.NET Core-toepassingen omvat de configuratie het toevoegen van het kanaal aan de afhankelijkheidsinjectiecontainer.

In de volgende secties worden `StorageFolder` voorbeelden weergegeven van het configureren van de instelling voor het kanaal in verschillende toepassingstypen. `StorageFolder`is slechts een van de configureerbare instellingen. Zie de [sectie Instellingen](telemetry-channels.md#configurable-settings-in-channels) later in dit artikel voor de volledige lijst met configuratie-instellingen.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuratie met ApplicationInsights.config voor ASP.NET toepassingen

In de volgende sectie van [ApplicationInsights.config](configuration-with-applicationinsights-config.md) wordt het `ServerTelemetryChannel` kanaal weergegeven dat is geconfigureerd op `StorageFolder` een aangepaste locatie:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Configuratie in code voor ASP.NET toepassingen

Met de volgende code wordt een instantie `StorageFolder` 'ServerTelemetryChannel' ingesteld die is ingesteld op een aangepaste locatie. Voeg deze code toe aan het begin `Application_Start()` van de toepassing, meestal in methode in Global.aspx.cs.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Configuratie in code voor ASP.NET Core-toepassingen

Wijzig `ConfigureServices` de methode `Startup.cs` van de klasse zoals hier wordt weergegeven:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> Het configureren van `TelemetryConfiguration.Active` het kanaal met behulp wordt niet aanbevolen voor ASP.NET Core-toepassingen.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configuratie in code voor .NET/.NET Core-consoletoepassingen

Voor console-apps is de code hetzelfde voor zowel .NET als .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Operationele details van ServerTelemetryChannel

`ServerTelemetryChannel`slaat aankomende artikelen op in een geheugenbuffer. De items worden geserialiseerd, gecomprimeerd `Transmission` en opgeslagen in een instantie eenmaal per 30 seconden, of wanneer 500 items zijn gebufferd. Een `Transmission` enkele instantie bevat maximaal 500 items en vertegenwoordigt een batch telemetrie die via één HTTPS-aanroep naar de Application Insights-service wordt verzonden.

Standaard kunnen maximaal 10 `Transmission` exemplaren parallel worden verzonden. Als telemetrie sneller verloopt of als het netwerk of de back-end van Application Insights traag is, `Transmission` worden instanties opgeslagen in het geheugen. De standaardcapaciteit van deze `Transmission` in-memory buffer is 5 MB. Wanneer de geheugencapaciteit is overschreden, `Transmission` worden instanties op de lokale schijf opgeslagen tot een limiet van 50 MB. `Transmission`instanties worden ook op de lokale schijf opgeslagen wanneer er netwerkproblemen zijn. Alleen items die op een lokale schijf zijn opgeslagen, overleven een crash van een toepassing. Ze worden verzonden wanneer de aanvraag opnieuw begint.

## <a name="configurable-settings-in-channels"></a>Configureerbare instellingen in kanalen

Zie voor de volledige lijst met configureerbare instellingen voor elk kanaal:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel ServerTelemetryChannel ServerTelemetryChannel ServerTelemetr](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Dit zijn de meest `ServerTelemetryChannel`gebruikte instellingen voor:

1. `MaxTransmissionBufferCapacity`: De maximale hoeveelheid geheugen, in bytes, die door het kanaal wordt gebruikt om transmissies in het geheugen te bufferen. Wanneer deze capaciteit is bereikt, worden nieuwe items rechtstreeks op de lokale schijf opgeslagen. De standaardwaarde is 5 MB. Het instellen van een hogere waarde leidt tot minder schijfgebruik, maar vergeet niet dat items in het geheugen verloren gaan als de toepassing vastloopt.

1. `MaxTransmissionSenderCapacity`: het maximum `Transmission` aantal exemplaren dat tegelijkertijd naar Application Insights wordt verzonden. De standaardwaarde is 10. Deze instelling kan worden geconfigureerd naar een hoger getal, wat wordt aanbevolen wanneer een enorm volume telemetrie wordt gegenereerd. Hoog volume treedt meestal op tijdens het testen van de belasting of wanneer de bemonstering is uitgeschakeld.

1. `StorageFolder`: de map die door het kanaal wordt gebruikt om items naar schijf op te slaan als dat nodig is. In Windows wordt %LOCALAPPDATA% of %TEMP% gebruikt als er geen ander pad expliciet wordt opgegeven. In andere omgevingen dan Windows moet u een geldige locatie opgeven of telemetrie wordt niet opgeslagen op de lokale schijf.

## <a name="which-channel-should-i-use"></a>Welk kanaal moet ik gebruiken?

`ServerTelemetryChannel`wordt aanbevolen voor de meeste productiescenario's met langlopende toepassingen. De `Flush()` methode die `ServerTelemetryChannel` wordt geïmplementeerd is niet synchroon en garandeert ook niet dat alle items in behandeling zijn vanuit het geheugen of de schijf. Als u dit kanaal gebruikt in scenario's waarin de toepassing op het `Flush()`punt staat af te sluiten, raden we u aan enige vertraging in te voeren na het bellen. De exacte hoeveelheid vertraging die u nodig zou kunnen hebben is niet voorspelbaar. Het hangt af van factoren `Transmission` zoals hoeveel items of exemplaren in het geheugen staan, hoeveel er op de schijf staan, hoeveel er naar de back-end worden verzonden en of het kanaal zich in het midden van exponentiële back-offscenario's bevindt.

Als je een synchrone flush moet doen, raden we je aan `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Garandeert het Application Insights-kanaal telemetrielevering? Zo niet, wat zijn de scenario's waarin telemetrie verloren kan gaan?

Het korte antwoord is dat geen van de ingebouwde kanalen een transactie-type garantie van telemetrie levering aan de back-end bieden. `ServerTelemetryChannel`is geavanceerder `InMemoryChannel` in vergelijking met voor betrouwbare levering, maar het maakt ook slechts een best-effort poging om telemetrie te verzenden. Telemetrie kan nog steeds verloren gaan in verschillende situaties, waaronder deze veelvoorkomende scenario's:

1. Items in het geheugen gaan verloren wanneer de toepassing vastloopt.

1. Telemetrie gaat verloren tijdens langere perioden van netwerkproblemen. Telemetrie wordt opgeslagen op de lokale schijf tijdens netwerkuitval of wanneer zich problemen voordoen met de back-end van Application Insights. Items ouder dan 48 uur worden echter weggegooid.

1. De standaardschijflocaties voor het opslaan van telemetrie in Windows zijn %LOCALAPPDATA% of %TEMP%. Deze locaties zijn meestal lokaal voor de machine. Als de toepassing fysiek van de ene locatie naar de andere migreert, gaat elke telemetrie die op de oorspronkelijke locatie is opgeslagen, verloren.

1. In Web Apps op Windows is de standaardschijfopslaglocatie D:\local\LocalAppData. Deze locatie is niet blijven bestaan. Het is weggevaagd in app opnieuw opstarten, scale-outs, en andere dergelijke bewerkingen, wat leidt tot verlies van alle telemetrie daar opgeslagen. U de standaardinstelling overschrijven en opslag opgeven op een locatie die nog lang is gehandhaafd, zoals D:\home. Dergelijke persistente locaties worden echter bediend door externe opslag en kunnen dus traag zijn.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Werkt ServerTelemetryChannel op andere systemen dan Windows?

Hoewel de naam van het pakket en de naamruimte "WindowsServer" bevat, wordt dit kanaal ondersteund op andere systemen dan Windows, met de volgende uitzondering. Op andere systemen dan Windows maakt het kanaal standaard geen lokale opslagmap. U moet een lokale opslagmap maken en het kanaal configureren om deze te gebruiken. Nadat de lokale opslag is geconfigureerd, werkt het kanaal op dezelfde manier op alle systemen.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Maakt de SDK tijdelijke lokale opslag? Zijn de gegevens versleuteld bij opslag?

De SDK slaat telemetrie-items op in lokale opslag tijdens netwerkproblemen of tijdens beperking. Deze gegevens worden niet lokaal versleuteld.

Voor Windows-systemen maakt de SDK automatisch een tijdelijke lokale map in de map %TEMP% of %LOCALAPPDATA% en beperkt het de toegang tot beheerders en alleen de huidige gebruiker.

Voor andere systemen dan Windows wordt er geen lokale opslag automatisch gemaakt door de SDK en worden er dus standaard geen gegevens lokaal opgeslagen. U zelf een opslagmap maken en het kanaal configureren om deze te gebruiken. In dit geval bent u verantwoordelijk voor het waarborgen dat de directory is beveiligd.
Lees meer over [gegevensbescherming en privacy](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Open-source SDK
Zoals elke SDK voor Application Insights zijn kanalen open source. Lees en draag bij aan de code, of meld problemen, op [de officiële GitHub repo](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Volgende stappen

* [Steekproeven](../../azure-monitor/app/sampling.md)
* [Probleemoplossing voor SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
