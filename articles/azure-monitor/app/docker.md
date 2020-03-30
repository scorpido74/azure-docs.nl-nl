---
title: Docker-toepassingen controleren in Azure-toepassingsinzichten | Microsoft Documenten
description: Docker perf-tellers, gebeurtenissen en uitzonderingen kunnen worden weergegeven op Application Insights, samen met de telemetrie van de gecontaineriseerde apps.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 5ff3d6a2b31425a3fb6857c8c6aed45f7ef18d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669604"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Docker-toepassingen controleren in toepassingsinzichten (afgeschaft)

> [!NOTE]
> Deze oplossing is afgeschaft. Voor meer informatie over onze huidige investeringen in containerbewaking raden we u aan [Azure Monitor voor containers te](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)controleren.

Levenscyclusgebeurtenissen en prestatiemeteritems van [Docker-containers](https://www.docker.com/) kunnen in kaart worden gebracht op Application Insights. Installeer de afbeelding [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) in een container in uw host en de weergave van prestatiemeteritems voor de host en voor de andere afbeeldingen.

Met Docker distribueert u uw apps in lichtgewicht containers, compleet met alle afhankelijkheden. Ze draaien op elke hostmachine die een Docker Engine draait.

Wanneer u de [image Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) uitvoert op uw Docker-host, krijgt u de volgende voordelen:

* Levenscyclus telemetrie over alle containers die op de host worden uitgevoerd - start, stop, enzovoort.
* Prestatiemeteritems voor alle containers. CPU, geheugen, netwerkgebruik en meer.
* Als u [Application Insights SDK voor Java hebt geïnstalleerd](../../azure-monitor/app/java-get-started.md) in de apps die in de containers worden uitgevoerd, hebben alle telemetrie van deze apps extra eigenschappen die de container- en hostmachine identificeren. Als u bijvoorbeeld exemplaren van een app in meer dan één host hebt, u uw app-telemetrie eenvoudig filteren op host.

## <a name="set-up-your-application-insights-resource"></a>Uw Application Insights-bron instellen

1. Meld u aan bij [de Microsoft Azure-portal](https://azure.com) en open de Application Insights-bron voor uw app. of [maak een nieuwe](../../azure-monitor/app/create-new-resource.md ). 
   
    *Welke resource moet ik gebruiken?* Als de apps die u op uw host uitvoert, door iemand anders zijn ontwikkeld, moet u [een nieuwe Application Insights-bron maken.](../../azure-monitor/app/create-new-resource.md ) Hier bekijkt en analyseert u de telemetrie. (Selecteer 'Algemeen' voor het app-type.)
   
    Maar als je de ontwikkelaar van de apps bent, dan hopen we dat je Application Insights SDK aan elk van hen [hebt toegevoegd.](../../azure-monitor/app/java-get-started.md) Als het allemaal echt onderdelen zijn van één bedrijfstoepassing, u ze allemaal configureren om telemetrie naar één bron te verzenden en gebruikt u dezelfde bron om de levenscyclus- en prestatiegegevens van Docker weer te geven. 
   
    Een derde scenario is dat u de meeste apps hebt ontwikkeld, maar dat u afzonderlijke bronnen gebruikt om hun telemetrie weer te geven. In dat geval wilt u waarschijnlijk ook een aparte bron maken voor de Docker-gegevens.

2. Klik op de vervolgkeuzelijst **Essentials** en kopieer de instrumentatiesleutel. U gebruikt dit om de SDK te vertellen waar de telemetrie moet worden verzonden.

Houd dat browservenster bij de hand, want je komt er snel op terug om naar je telemetrie te kijken.

## <a name="run-the-application-insights-monitor-on-your-host"></a>De monitor Application Insights uitvoeren op uw host

Nu u de telemetrie ergens weergeven, u de containerapp instellen die deze verzamelt en verzendt.

1. Maak verbinding met uw Docker-host.
2. Bewerk de instrumentatiesleutel in deze opdracht en voer deze uit:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Per Docker-host is slechts één Application Insights-afbeelding vereist. Als uw toepassing is geïmplementeerd op meerdere Docker-hosts, herhaalt u de opdracht op elke host.

## <a name="update-your-app"></a>Uw app bijwerken
Als uw toepassing is uitgerust met de [Application Insights SDK voor Java,](../../azure-monitor/app/java-get-started.md)voegt u de `<TelemetryInitializers>` volgende regel toe aan het bestand ApplicationInsights.xml in uw project, onder het element:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Hiermee worden Docker-informatie zoals container- en host-id toegevoegd aan elk telemetrie-item dat vanuit uw app wordt verzonden.

## <a name="view-your-telemetry"></a>Uw telemetrie weergeven
Ga terug naar uw Application Insights-bron in de Azure-portal.

Klik door de docker-tegel.

U ziet binnenkort gegevens uit de Docker-app aankomen, vooral als er andere containers op uw Docker-engine worden uitgevoerd.

### <a name="docker-container-events"></a>Containergebeurtenissen docker
![Voorbeeld](./media/docker/13.png)

Als u afzonderlijke gebeurtenissen wilt onderzoeken, klikt u op [Zoeken](../../azure-monitor/app/diagnostic-search.md). Zoek en filter om de gewenste gebeurtenissen te vinden. Klik op een evenement om meer details te krijgen.

### <a name="exceptions-by-container-name"></a>Uitzonderingen op containernaam
![Voorbeeld](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Docker-context toegevoegd aan app-telemetrie
Verzoek telemetrie verzonden van de toepassing die is uitgerust met AI SDK, is verrijkt met Docker context informatie.

## <a name="q--a"></a>Vragen en antwoorden
*Wat geeft Application Insights mij dat ik niet van Docker kan krijgen?*

* Gedetailleerde uitsplitsing van prestatiemeteritems per container en afbeelding.
* Integreer container- en app-gegevens in één dashboard.
* [Telemetrie exporteren](export-telemetry.md) voor verdere analyse naar een database, Power BI of ander dashboard.

*Hoe krijg ik telemetrie van de app zelf?*

* Installeer de Application Insights SDK in de app. Meer informatie over: [Java-webapps](../../azure-monitor/app/java-get-started.md), [Windows-webapps](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Volgende stappen

* [Applicatie-inzichten voor Java](../../azure-monitor/app/java-get-started.md)
* [Application Insights voor Node.js](../../azure-monitor/app/nodejs.md)
* [Toepassingsinzichten voor ASP.NET](../../azure-monitor/app/asp-net.md)
