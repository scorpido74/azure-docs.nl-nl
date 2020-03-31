---
title: Azure-servicefabric diagnosticeren veelvoorkomende scenario's
description: Meer informatie over het oplossen van algemene bewakings- en diagnostische scenario's binnen Azure Service Fabric-toepassingen.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3c7f027bad71d48db5fba002f778f23db8225fa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906946"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Veelvoorkomende scenario's diagnosticeren met Service Fabric

Dit artikel illustreert veelvoorkomende scenario's die gebruikers hebben ondervonden op het gebied van monitoring en diagnostiek met Service Fabric. De gepresenteerde scenario's hebben betrekking op alle 3 lagen servicefabric: Toepassing, cluster en infrastructuur. Elke oplossing maakt gebruik van Application Insights en Azure Monitor-logboeken, Azure-bewakingshulpprogramma's, om elk scenario te voltooien. De stappen in elke oplossing geven gebruikers een inleiding over het gebruik van Application Insights en Azure Monitor-logboeken in de context van Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Voorwaarden en aanbevelingen

De oplossingen in dit artikel zullen gebruik maken van de volgende tools. We raden u aan deze in te stellen en te configureren:

* [Applicatie-inzichten met servicestof](service-fabric-tutorial-monitoring-aspnet.md)
* [Azure Diagnostics inschakelen op uw cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Een logboekanalysewerkruimte instellen](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics-agent om prestatiemeteritems bij te houden](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Hoe kan ik onverwerkte uitzonderingen zien in mijn toepassing?

1. Navigeer naar de resource Application Insights waarmee uw toepassing is geconfigureerd.
2. Klik linksboven op *Zoeken.* Klik vervolgens op filter in het volgende deelvenster.

    ![Overzicht van AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. U ziet veel soorten gebeurtenissen (traces, aanvragen, aangepaste gebeurtenissen). Kies 'Uitzondering' als filter.

    ![AI-filterlijst](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Door op een uitzondering in de lijst te klikken, u meer details bekijken, waaronder de servicecontext als u de Service Fabric Application Insights SDK gebruikt.

    ![AI-uitzondering](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Hoe kan ik zien welke HTTP-oproepen worden gebruikt in mijn services?

1. In dezelfde Application Insights-bron u filteren op 'aanvragen' in plaats van uitzonderingen en alle aanvragen bekijken
2. Als u de Service Fabric Application Insights SDK gebruikt, ziet u een visuele weergave van uw services die met elkaar zijn verbonden en het aantal geslaagde en mislukte aanvragen. Klik links op 'Toepassingskaart'.

    ![AI-app](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![Map Blade AI-appkaart](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Ga voor meer informatie over de aanvraagkaart naar de documentatie van de [toepassingskaart](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Hoe maak ik een waarschuwing wanneer een knooppunt naar beneden gaat

1. Knooppuntgebeurtenissen worden bijgehouden door uw cluster Service Fabric. Navigeer naar de bron Service Fabric Analytics-oplossing met de naam **ServiceFabric(NameofResourceGroup)**
2. Klik op de grafiek op de onderkant van het blad met de titel "Samenvatting"

    ![Azure Monitor-logboekenoplossing](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Hier heb je veel grafieken en tegels met verschillende statistieken. Klik op een van de grafieken en het brengt u naar de Log Search. Hier u vragen stellen voor clustergebeurtenissen of prestatiemeteritems.
4. Voer de volgende query in. Deze gebeurtenis---geïdentificeerde gegevensvindt zich in de [verwijzing naar knooppuntgebeurtenissen](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Klik bovenaan op 'Nieuwe waarschuwingsregel' en op elk moment dat een gebeurtenis op basis van deze query binnenkomt, ontvangt u een waarschuwing in de door u gekozen communicatiemethode.

    ![Azure Monitor registreert nieuwe waarschuwing](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Hoe kan ik worden gewaarschuwd voor rollbacks van toepassingsupgrades?

1. Voer in hetzelfde venster Logboekzoeken als voorheen de volgende query in voor rollbacks van de upgrade. Deze gebeurtenis---geïdentificeerde gegevensworden gevonden onder [verwijzing naar toepassingsgebeurtenissen](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Klik bovenaan op 'Nieuwe waarschuwingsregel' en nu er op basis van deze query een gebeurtenis binnenkomt, ontvangt u een waarschuwing.

## <a name="how-do-i-see-container-metrics"></a>Hoe zie ik containerstatistieken?

In dezelfde weergave met alle grafieken, ziet u een aantal tegels voor de prestaties van uw containers. U hebt de oplossing Log Analytics Agent en [Container Monitoring](service-fabric-diagnostics-oms-containers.md) nodig om deze tegels in te vullen.

![Containerstatistieken voor logboekanalyses](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Om telemetrie **vanuit** uw container te gebruiken, moet u het [Nuget-pakket van Application Insights voor containers](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios)toevoegen.

## <a name="how-can-i-monitor-performance-counters"></a>Hoe kan ik prestatiemeteritems controleren?

1. Zodra u de log-analyse-agent aan uw cluster hebt toegevoegd, moet u de specifieke prestatiemeteritems toevoegen die u wilt bijhouden. Navigeer naar de pagina van de Log Analytics-werkruimte in de portal: vanaf de pagina van de oplossing bevindt het tabblad werkruimte zich in het linkermenu.

    ![Tabblad Werkruimte logboekanalyse](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Als u eenmaal op de pagina van de werkruimte bent, klikt u in hetzelfde linkermenu op 'Geavanceerde instellingen'.

    ![Geavanceerde instellingen voor Log Analytics](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Klik op Gegevens > Windows Performance Counters (Data > Linux Performance Counters voor Linux-machines) om via de Log Analytics-agent specifieke tellers van uw knooppunten te verzamelen. Hier volgen voorbeelden van de indeling voor tellers om toe te voegen

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     In de quickstart, VotingData en VotingWeb zijn de gebruikte procesnamen, dus het bijhouden van deze tellers zou eruit zien als

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Log Analytics Perf-tellers](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Zo u zien hoe uw infrastructuur omgaat met uw workloads en relevante waarschuwingen instellen op basis van het gebruik van resources. Bijvoorbeeld: u bijvoorbeeld een waarschuwing instellen als het totale processorgebruik hoger is dan 90% of minder dan 5%. De tellernaam die u hiervoor zou gebruiken is '% Processortijd'. U dit doen door een waarschuwingsregel te maken voor de volgende query:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Hoe kan ik de prestaties van mijn betrouwbare services en acteurs bijhouden?

Als u de prestaties van betrouwbare services of actoren in uw toepassingen wilt bijhouden, moet u ook de tellers Service Fabric Actor, Actor Method, Service en Service Method verzamelen. Hier zijn voorbeelden van betrouwbare service en acteur performance tellers te verzamelen

>[!NOTE]
>Service Fabric-prestatiemeteritems kunnen momenteel niet worden verzameld door de Log Analytics-agent, maar kunnen worden verzameld door [andere diagnostische oplossingen](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Bekijk deze links voor de volledige lijst met prestatiebalies over betrouwbare [services](service-fabric-reliable-serviceremoting-diagnostics.md) en [actoren](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Volgende stappen

* [Fouten in het activeren van algemene codepakketten opzoeken](./service-fabric-diagnostics-code-package-errors.md)
* [Waarschuwingen in AI instellen](../azure-monitor/app/alerts.md) om op de hoogte te worden gesteld van wijzigingen in prestaties of gebruik
* [Smart Detection in Application Insights](../azure-monitor/app/proactive-diagnostics.md) voert een proactieve analyse uit van de telemetrie die naar AI wordt verzonden om u te waarschuwen voor mogelijke prestatieproblemen
* Meer informatie over Azure Monitor-logboeken [die waarschuwen](../log-analytics/log-analytics-alerts.md) voor hulp bij detectie en diagnose.
* Voor on-premises clusters bieden Azure Monitor-logboeken een gateway (HTTP Forward Proxy) die kan worden gebruikt om gegevens naar Azure Monitor-logboeken te verzenden. Lees daar meer over in [Het verbinden van computers zonder internettoegang tot Azure Monitor-logboeken met behulp van de Logboekanalysegateway](../azure-monitor/platform/gateway.md)
* Maak kennis met de [functies voor zoeken en query's voor logboeken](../log-analytics/log-analytics-log-searches.md) die worden aangeboden als onderdeel van Azure Monitor-logboeken
* Lees [Wat zijn Azure Monitor-logboeken?](../operations-management-suite/operations-management-suite-overview.md)
