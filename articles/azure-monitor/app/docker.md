---
title: Docker-toepassingen bewaken in Azure-toepassing Insights | Microsoft Docs
description: Docker-prestatie meter items, gebeurtenissen en uitzonde ringen kunnen worden weer gegeven op Application Insights, samen met de telemetrie van de apps in de container.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 5ff3d6a2b31425a3fb6857c8c6aed45f7ef18d64
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669604"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Docker-toepassingen in Application Insights bewaken (afgeschaft)

> [!NOTE]
> Deze oplossing is afgeschaft. Als u meer wilt weten over onze huidige investeringen in container bewaking, kunt u het beste [Azure monitor voor containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)uitchecken.

Levenscyclus gebeurtenissen en prestatie meter items van [docker](https://www.docker.com/) -containers kunnen worden gediagrameerd op Application Insights. Installeer de [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) installatie kopie in een container in uw host en Hiermee worden de prestatie meter items voor de host weer gegeven, evenals de andere installatie kopieën.

Met docker distribueert u uw apps in licht gewicht containers met alle afhankelijkheden. Ze worden uitgevoerd op elke hostcomputer waarop een docker-engine wordt uitgevoerd.

Wanneer u de [Application Insights-installatie kopie](https://hub.docker.com/r/microsoft/applicationinsights/) op uw docker-host uitvoert, krijgt u de volgende voor delen:

* Levenscyclus telemetrie over alle containers die worden uitgevoerd op de host-starten, stoppen, enzovoort.
* Prestatie meter items voor alle containers. CPU, geheugen, netwerk gebruik en meer.
* Als u [Application INSIGHTS SDK voor Java hebt geïnstalleerd](../../azure-monitor/app/java-get-started.md) in de apps die worden uitgevoerd in de containers, hebben alle telemetrie van deze apps aanvullende eigenschappen die de container en de hostmachine identificeren. Als er bijvoorbeeld exemplaren van een app worden uitgevoerd op meer dan één host, kunt u eenvoudig uw app-telemetrie filteren op host.

## <a name="set-up-your-application-insights-resource"></a>Uw Application Insights resource instellen

1. Meld u aan bij [Microsoft Azure-Portal](https://azure.com) en open de Application Insights resource voor uw app. of [Maak een nieuwe](../../azure-monitor/app/create-new-resource.md ). 
   
    *Welke resource moet ik gebruiken?* Als de apps die u op uw host uitvoert, zijn ontwikkeld door iemand anders, moet u [een nieuwe Application Insights-resource maken](../../azure-monitor/app/create-new-resource.md ). Hier kunt u de telemetrie weer geven en analyseren. (Selecteer algemeen voor het app-type.)
   
    Maar als u de ontwikkelaar van de apps bent, hopen we dat u [Application INSIGHTS SDK](../../azure-monitor/app/java-get-started.md) aan elk daarvan hebt toegevoegd. Als ze alle onderdelen van één zakelijke toepassing zijn, kunt u deze allemaal zo configureren dat telemetrie naar één resource wordt verzonden. u gebruikt dezelfde resource voor het weer geven van de gegevens van de docker-levens cyclus en de prestaties. 
   
    Een derde scenario is dat u de meeste apps hebt ontwikkeld, maar u gebruikt afzonderlijke resources om de telemetrie weer te geven. In dat geval wilt u waarschijnlijk ook een afzonderlijke resource maken voor de docker-gegevens.

2. Klik op de vervolg keuzelijst **Essentials** en kopieer de instrumentatie sleutel. U kunt dit gebruiken om de SDK te laten weten waar de telemetrie naartoe moet worden verzonden.

Houd het browser venster zo goed mogelijk, omdat u het binnenkort weer kunt gebruiken om uw telemetrie te bekijken.

## <a name="run-the-application-insights-monitor-on-your-host"></a>De Application Insights monitor op uw host uitvoeren

Nu u de telemetrie hebt weer gegeven, kunt u de in de container geplaatste app instellen om deze te verzamelen en te verzenden.

1. Maak verbinding met uw docker-host.
2. Bewerk de instrumentatie sleutel in deze opdracht en voer deze uit:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Er is slechts één Application Insights installatie kopie vereist per docker-host. Als uw toepassing is geïmplementeerd op meerdere docker-hosts, herhaalt u de opdracht op elke host.

## <a name="update-your-app"></a>Uw app bijwerken
Als uw toepassing is voorzien van de [Application INSIGHTS SDK voor Java](../../azure-monitor/app/java-get-started.md), voegt u de volgende regel toe aan het bestand ApplicationInsights. XML in uw project, onder het element `<TelemetryInitializers>`:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Hiermee voegt u docker-informatie, zoals container en host-id, toe aan elk telemetrie-item dat vanuit uw app wordt verzonden.

## <a name="view-your-telemetry"></a>Uw telemetrie weergeven
Ga terug naar uw Application Insights-resource in de Azure Portal.

Klik op de tegel docker.

U ziet binnenkort gegevens die arriveren vanuit de docker-app, met name als u andere containers hebt die worden uitgevoerd op uw docker-engine.

### <a name="docker-container-events"></a>Docker-container gebeurtenissen
![Voorbeeld](./media/docker/13.png)

Klik op [zoeken](../../azure-monitor/app/diagnostic-search.md)om afzonderlijke gebeurtenissen te onderzoeken. Zoek en filter om de gewenste gebeurtenissen te vinden. Klik op een gebeurtenis om meer details weer te geven.

### <a name="exceptions-by-container-name"></a>Uitzonde ringen op container naam
![Voorbeeld](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Docker-context is toegevoegd aan app-telemetrie
Aanvraag-telemetrie die is verzonden vanuit de toepassings instrument met AI SDK, wordt verrijkt met de gegevens van docker-context.

## <a name="q--a"></a>Q & A
*Wat staat er Application Insights ik mij niet kan ophalen uit docker?*

* Gedetailleerde uitsplitsing van prestatie meter items per container en installatie kopie.
* Container-en app-gegevens integreren in één dash board.
* [Telemetrie exporteren](export-telemetry.md) voor verdere analyse naar een data base, Power bi of een ander dash board.

*Hoe kan ik telemetrie ophalen uit de app zelf?*

* Installeer de Application Insights SDK in de app. Meer informatie over: [Java Web apps](../../azure-monitor/app/java-get-started.md), [Windows Web apps](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Volgende stappen

* [Application Insights voor Java](../../azure-monitor/app/java-get-started.md)
* [Application Insights voor node. js](../../azure-monitor/app/nodejs.md)
* [Application Insights voor ASP.NET](../../azure-monitor/app/asp-net.md)
