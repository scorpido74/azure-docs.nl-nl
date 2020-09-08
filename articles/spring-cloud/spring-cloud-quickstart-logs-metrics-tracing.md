---
title: 'Quickstart: Azure Spring Cloud-apps bewaken met logboeken, metrische gegevens en tracering'
description: Gebruik logboekstreaming, logboekanalyse, metrische gegevens en tracering om PiggyMetrics-voorbeeldtoepassingen te bewaken in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f9f03c355e1e619d004c8ec8c1cc2f91932db744
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046830"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Quickstart: Azure Spring Cloud-apps bewaken met logboeken, metrische gegevens en tracering

Met de ingebouwde bewaking in Azure Spring Cloud kunt u complexe problemen opsporen en bewaken. Azure Spring Cloud integreert [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) met [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) van Azure. Deze integratie biedt krachtige logboeken, metrische gegevens en functies voor gedistribueerde tracering via Azure Portal. In de volgende procedures wordt uitgelegd hoe u logboekstreaming, logboekanalyse, metrische gegevens en gedistribueerde tracering gebruikt met geïmplementeerde PiggyMetrics-apps.

## <a name="prerequisites"></a>Vereisten

Voltooiing van de vorige stappen: 

* [Een exemplaar van Azure Spring Cloud inrichten](spring-cloud-quickstart-provision-service-instance.md)
* [De configuratieserver instellen](spring-cloud-quickstart-setup-config-server.md)
* [Apps bouwen en implementeren](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Logboeken

Er zijn twee manieren om logboeken in Azure Spring Cloud te bekijken: **Logboekstreaming** van realtime logboeken per app-instantie of **logboekanalyse** voor geaggregeerde logboeken met geavanceerde querymogelijkheden.

### <a name="log-streaming"></a>Logboekstreaming

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

U kunt logboekstreaming in de Azure CLI gebruiken met de volgende opdracht.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

U ziet logboeken zoals het volgende:

[ ![Logboekstreaming vanaf de Azure CLI](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Gebruik `az spring-cloud app logs -h` om meer parameters en de functionaliteiten van logboekstreaming te verkennen.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

De logboeken ophalen met behulp van Azure-toolkit voor IntelliJ:

1. Selecteer **Azure Explorer**  en **Spring Cloud**.

1. Klik met de rechtermuisknop op de app die wordt uitgevoerd.

1. Selecteer **Streaminglogboeken** in de vervolgkeuzelijst.

   ![Streaminglogboeken selecteren](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Selecteer **Exemplaar**.

   ![Exemplaar selecteren](media/spring-cloud-intellij-howto/select-instance.png)
    
1. Het streaminglogboek wordt weergegeven in het uitvoervenster.

   ![Uitvoer van streaminglogboek](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Ga naar de pagina **service | Overzicht** en selecteer **Logboeken** in de sectie **Bewaking**. Klik op een van de voorbeeldquery's voor Azure Spring Cloud op **Uitvoeren**. 

   [ ![Logs Analytics-vermelding](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Vervolgens worden gefilterde logboeken weergegeven. Zie [Azure Log Analytics-documenten](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries) voor meer informatie over het schrijven van query's.

   [ ![Logs Analytics-query](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Metrische gegevens

1. Ga naar de pagina **service | Overzicht** en selecteer **Metrische gegevens** in de sectie **Bewaking**. Voeg uw eerste meetwaarde toe door `system.cpu.usage` voor **Meetwaarde** en `Avg` voor **Aggregatie** te selecteren om de tijdlijn voor het totale CPU-gebruik te bekijken.

   [ ![Vermelding van meetwaarde](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Klik in de werkbalk hierboven op **Filter toevoegen**, selecteer `App=Gateway` om het CPU-gebruik voor alleen de **gateway**-app te bekijken.

   [ ![Filter gebruiken in metrische gegevens](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Sluit het eerder gemaakte filter, klik op **Splitsing toepassen** en selecteer `App` voor **Waarden** om het CPU-gebruik door verschillende apps te bekijken.

   [ ![Splitsing in metrische gegevens toepassen](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Gedistribueerde tracering

1. Ga naar de pagina **service | Overzicht** en selecteer **Gedistribueerde tracering** in de sectie **Bewaking**. Klik vervolgens op het tabblad **Toepassingsoverzicht weergeven** aan de rechterkant.

   [ ![Vermelding van gedistribueerde tracering](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. U kunt nu de status van aanroepen zien tussen Piggymetrics-apps. 

   [ ![Overzicht van gedistribueerde tracering](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Klik op de koppeling tussen **gateway** en **account-service** om meer details weer te geven, zoals traagste aanroepen via HTTP-methoden.

   [ ![Gedistribueerde tracering](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Klik ten slotte op **Prestaties onderzoeken** om krachtigere, ingebouwde prestatieanalyse te verkennen.

   [ ![Prestaties van gedistribueerde tracering](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep uit de portal of door de volgende opdracht in Cloud Shell uit te voeren:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

In de voorgaande stappen stelt u ook de standaardnaam van de resourcegroep in. Voer de volgende opdracht uit in Cloud Shell om de standaardinstelling te wissen:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de kant-en-klare bewakingsmogelijkheden voor Azure Spring Cloud:

> [!div class="nextstepaction"]
> [Diagnostische services](diagnostic-services.md)
> [Gedistribueerde tracering](spring-cloud-tutorial-distributed-tracing.md)
> [Streaminglogboeken in realtime](spring-cloud-howto-log-streaming.md)
