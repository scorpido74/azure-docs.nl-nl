---
title: Azure-monitor configureren voor containers Prometheus-integratie | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de Azure Monitor voor containersagent configureren om statistieken van Prometheus te schrapen met uw Kubernetes-cluster.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: b774bf042778ca9118a7bc9f051655b200d87659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931424"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Schraaping van Prometheus-statistieken configureren met Azure Monitor voor containers

[Prometheus](https://prometheus.io/) is een populaire open source metrische monitoring oplossing en is een onderdeel van de [Cloud Native Compute Foundation.](https://www.cncf.io/) Azure Monitor voor containers biedt een naadloze onboarding-ervaring om Prometheus-statistieken te verzamelen. Om Prometheus te gebruiken, moet u een Prometheus-server instellen en beheren met een winkel. Door te integreren met Azure Monitor is een Prometheus-server niet vereist. U hoeft alleen maar het eindpunt van de Prometheus-statistieken te blootstellen via uw exporteurs of pods (toepassing) en de containeragent voor Azure Monitor voor containers kan de statistieken voor u schrapen. 

![Container monitoring architectuur voor Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>De minimale agentversie die wordt ondersteund voor het schrapen van Prometheus-statistieken is ciprod07092019 of `KubeMonAgentEvents` hoger, en de agentversie die wordt ondersteund voor het schrijven van configuratie- en agentfouten in de tabel is ciprod10112019. Voor meer informatie over de agentversies en wat er in elke release is opgenomen, raadpleegt [u de releasenotes van de agent.](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod) Als u de versie van uw agent wilt verifiëren, selecteert u op het tabblad **Knooppunt** een knooppunt en in het eigenschappenvenster de waarde van de eigenschap **Agent Image Tag.**

Het schrappen van Prometheus-statistieken wordt ondersteund met Kubernetes-clusters die worden gehost op:

- Azure Kubernetes Service (AKS)
- Azure Container Instances
- Azure Stack of on-premises
- Azure Red Hat OpenShift

>[!NOTE]
>Voor Azure Red Hat OpenShift wordt een sjabloon ConfigMap-bestand gemaakt in de naamruimte *openshift-azure-logging.* Het is niet geconfigureerd om actief statistieken of gegevensverzameling van de agent te schrapen.
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Azure Red Hat OpenShift-vereisten

Bevestig voordat u begint of u lid bent van de rol klantclusterbeheerder van uw Azure Red Hat OpenShift-cluster om de containererende agent en de kladinstellingen voor Prometheus te configureren. Voer de volgende opdracht uit om te controleren of u lid bent van de groep *osa-klantbeheerders:*

``` bash
  oc get groups
```

De uitvoer lijkt op het volgende:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Als u lid bent van *de osa-klantbeheerdersgroep,* moet `container-azm-ms-agentconfig` u de ConfigMap kunnen aanbieden met de volgende opdracht:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

De uitvoer lijkt op het volgende:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>Prometheus scraping instellingen

Actief schrapen van statistieken van Prometheus wordt uitgevoerd vanuit een van de twee perspectieven:

* Clusterbreed - HTTP-URL en ontdek doelen van vermelde eindpunten van een service. K8s-services zoals kube-dns en kube-state-metrics en pod-annotaties die specifiek zijn voor een toepassing. Statistieken die in deze context worden verzameld, worden gedefinieerd in de sectie ConfigMap *[Prometheus data_collection_settings.cluster]*.
* Node-wide - HTTP URL en ontdek doelen van vermelde eindpunten van een service. Statistieken die in deze context worden verzameld, worden gedefinieerd in de sectie ConfigMap *[Prometheus_data_collection_settings.node]*.

| Eindpunt | Bereik | Voorbeeld |
|----------|-------|---------|
| Pod-annotatie | Clusterbreed | Aantekeningen: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes-service | Clusterbreed | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/eindpunt | Per knooppunt en/of clusterbreed | `http://myurl:9101/metrics` |

Wanneer een URL is opgegeven, schraapt Azure Monitor voor containers alleen het eindpunt. Wanneer Kubernetes-service is opgegeven, wordt de servicenaam opgelost met de dns-server van het cluster om het IP-adres te krijgen en vervolgens wordt de opgeloste service geschraapt.

|Bereik | Sleutel | Gegevenstype | Waarde | Beschrijving |
|------|-----|-----------|-------|-------------|
| Clusterbreed | | | | Geef een van de volgende drie methoden op om eindpunten voor statistieken te schrapen. |
| | `urls` | Tekenreeks | Door komma's gescheiden array | HTTP-eindpunt (IP-adres of geldig URL-pad opgegeven). Bijvoorbeeld: `urls=[$NODE_IP/metrics]`. ($NODE_IP is een specifieke Azure Monitor voor containers parameter en kan worden gebruikt in plaats van node IP-adres. Moet alle hoofdletters.) |
| | `kubernetes_services` | Tekenreeks | Door komma's gescheiden array | Een scala aan Kubernetes-services om statistieken te schrapen uit kube-state-metrics. Bijvoorbeeld.`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`|
| | `monitor_kubernetes_pods` | Booleaans | waar of onwaar | Wanneer u `true` bent ingesteld op de instellingen voor het hele cluster, zal Azure Monitor voor containers-agent Kubernetes-pods in het hele cluster schrapen voor de volgende Prometheus-annotaties:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Booleaans | waar of onwaar | Maakt het schrapen van de pod mogelijk. `monitor_kubernetes_pods`moeten worden `true`ingesteld op . |
| | `prometheus.io/scheme` | Tekenreeks | http of https | Standaard slopen via HTTP. Indien nodig, `https`ingesteld op . | 
| | `prometheus.io/path` | Tekenreeks | Door komma's gescheiden array | Het HTTP-resourcepad waaru statistieken kunnen worden opgehaald. Als het metrische `/metrics`pad dit niet is, definieert u het met deze annotatie. |
| | `prometheus.io/port` | Tekenreeks | 9102 | Geef een poort op om uit te schrapen. Als de poort niet is ingesteld, wordt deze standaard ingesteld op 9102. |
| | `monitor_kubernetes_pods_namespaces` | Tekenreeks | Door komma's gescheiden array | Een lijst met naamruimten om statistieken uit Kubernetes-pods te schrapen.<br> Bijvoorbeeld: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Knooppuntbreed | `urls` | Tekenreeks | Door komma's gescheiden array | HTTP-eindpunt (IP-adres of geldig URL-pad opgegeven). Bijvoorbeeld: `urls=[$NODE_IP/metrics]`. ($NODE_IP is een specifieke Azure Monitor voor containers parameter en kan worden gebruikt in plaats van node IP-adres. Moet alle hoofdletters.) |
| Knooppuntbreed of clusterbreed | `interval` | Tekenreeks | 60s | De standaardinstellingsinterval is één minuut (60 seconden). U de verzameling wijzigen voor de *[prometheus_data_collection_settings.node]* en/of *[prometheus_data_collection_settings.cluster]* in tijdeenheden zoals s, m, h. |
| Knooppuntbreed of clusterbreed | `fieldpass`<br> `fielddrop`| Tekenreeks | Door komma's gescheiden array | U bepaalde statistieken opgeven die al dan niet vanaf`fieldpass`het eindpunt`fielddrop`moeten worden verzameld door de aanbieding toestaan ( ) in te stellen en ( ) te verbieden. U moet eerst de lijst met toegestane plaatsen instellen. |

ConfigMaps is een globale lijst en er kan slechts één ConfigMap op de agent worden toegepast. U geen andere ConfigMaps hebben die de collecties overrulen.

## <a name="configure-and-deploy-configmaps"></a>ConfigMaps configureren en implementeren

Voer de volgende stappen uit om uw ConfigMap-configuratiebestand voor Kubernetes-clusters te configureren.

1. [Download](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) de template ConfigMap yaml bestand en sla het op als container-azm-ms-agentconfig.yaml.

   >[!NOTE]
   >Deze stap is niet vereist bij het werken met Azure Red Hat OpenShift, omdat de ConfigMap-sjabloon al op het cluster bestaat.

2. Bewerk het ConfigMap yaml-bestand met uw aanpassingen om Prometheus-statistieken te schrapen. Als u het ConfigMap yaml-bestand voor Azure Red Hat OpenShift bewerkt, voert u eerst de opdracht `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` uit om het bestand in een teksteditor te openen.

    >[!NOTE]
    >De volgende annotatie `openshift.io/reconcile-protect: "true"` moet worden toegevoegd onder de metadata van *container-azm-ms-agentconfig* ConfigMap om verzoening te voorkomen. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Als u kubernetes-services clusterbreed wilt verzamelen, configureert u het ConfigMap-bestand met behulp van het volgende voorbeeld.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Als u het schrapen van Prometheus-statistieken van een specifieke URL in het cluster wilt configureren, configureert u het ConfigMap-bestand met behulp van het volgende voorbeeld.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Als u het schrapen van Prometheus-statistieken wilt configureren van de DaemonSet van een agent voor elk afzonderlijk knooppunt in het cluster, configureert u het volgende in de ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE_IP is een specifieke Azure Monitor voor containers parameter en kan worden gebruikt in plaats van node IP-adres. Het moet allemaal hoofdletters zijn. 

    - Voer de volgende stappen uit om het schrapen van Prometheus-statistieken te configureren door een pod-annotatie op te geven:

       1. Geef in de ConfigMap het volgende op:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Geef de volgende configuratie op voor podaantekeningen:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Als u de bewaking wilt beperken tot specifieke naamruimten voor pods met annotaties, `monitor_kubernetes_pod` moet `true` u bijvoorbeeld alleen pods opnemen `monitor_kubernetes_pods_namespaces` die zijn toegewezen aan productieworkloads, de controle instellen in ConfigMap en het naamruimtefilter toevoegen waarin de naamruimten worden opgegeven om uit te schrapen. Bijvoorbeeld: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Voer de volgende kubectl-opdracht uit voor andere `kubectl apply -f <configmap_yaml_file.yaml>`clusters dan Azure Red Hat OpenShift: .
    
    Bijvoorbeeld: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Voor Azure Red Hat OpenShift slaat u uw wijzigingen op in de editor.

Het kan enkele minuten duren voordat de configuratiewijziging is voltooid voordat deze van kracht wordt en alle omsagentpods in het cluster opnieuw worden opgestart. De herstart is een rollende herstart voor alle omsagent pods, niet allemaal opnieuw op hetzelfde moment. Wanneer de herstart is voltooid, wordt een bericht weergegeven dat vergelijkbaar is `configmap "container-azm-ms-agentconfig" created`met het volgende en het resultaat bevat: .

U de bijgewerkte ConfigMap voor Azure Red Hat `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`OpenShift bekijken door de opdracht uit te voeren. 

## <a name="applying-updated-configmap"></a>Bijgewerkte ConfigMap toepassen

Als u al een ConfigMap op uw cluster hebt geïmplementeerd en u deze wilt bijwerken met een nieuwere configuratie, u het ConfigMap-bestand dat u eerder hebt gebruikt bewerken en vervolgens toepassen met dezelfde opdrachten als voorheen.

Voer de opdracht `kubectl apply -f <configmap_yaml_file.yaml`uit voor andere Kubernetes-clusters dan Azure Red Hat OpenShift. 

Voer voor azure Red Hat OpenShift-cluster de opdracht uit `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` om het bestand in de standaardeditor te openen en vervolgens op te slaan.

Het kan enkele minuten duren voordat de configuratiewijziging is voltooid voordat deze van kracht wordt en alle omsagentpods in het cluster opnieuw worden opgestart. De herstart is een rollende herstart voor alle omsagent pods, niet allemaal opnieuw op hetzelfde moment. Wanneer de herstart is voltooid, wordt een bericht weergegeven dat vergelijkbaar is `configmap "container-azm-ms-agentconfig" updated`met het volgende en het resultaat bevat: .

## <a name="verify-configuration"></a>Configuratie verifiëren

Als u wilt controleren of de configuratie is toegepast op een cluster, `kubectl logs omsagent-fdf58 -n=kube-system`gebruikt u de volgende opdracht om de logboeken te controleren vanuit een agentpod: . 

>[!NOTE]
>Deze opdracht is niet van toepassing op azure Red Hat OpenShift-cluster.
> 

Als er configuratiefouten zijn van de omsagentpods, worden in de uitvoer fouten weergegeven die vergelijkbaar zijn met de volgende:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fouten in verband met het toepassen van configuratiewijzigingen zijn ook beschikbaar voor controle. De volgende opties zijn beschikbaar voor het uitvoeren van aanvullende probleemoplossing voor configuratiewijzigingen en het schrappen van Prometheus-statistieken:

- Logboeken van een agentpod met dezelfde `kubectl logs` opdracht 
    >[!NOTE]
    >Deze opdracht is niet van toepassing op azure Red Hat OpenShift-cluster.
    > 

- Van Live Data (preview). Live Data (preview) logs tonen fouten vergelijkbaar met de volgende:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Ga naar de tabel **KubeMonAgentEvents** in uw log analytics-werkruimte. Gegevens worden elk uur verzonden met *waarschuwing* ernst voor schraapfouten en *Fout* ernst voor configuratiefouten. Als er geen fouten zijn, bevat de vermelding in de tabel gegevens met *ernstinfo,* die geen fouten rapporteert. De eigenschap **Tags** bevat meer informatie over de pod- en container-ID waarop de fout is opgetreden en ook de eerste gebeurtenis, laatste gebeurtenis en telling in het laatste uur.

- Controleer voor Azure Red Hat OpenShift de omsagent-logboeken door in de **containerlog-tabel** te zoeken om te controleren of logboekverzameling van openshift-azure-logging is ingeschakeld.

Fouten voorkomen dat omsagent het bestand ontleedt, waardoor het opnieuw wordt opgestart en de standaardconfiguratie wordt gebruikt. Nadat u de fout(en) in ConfigMap hebt gecorrigeerd op andere clusters dan Azure Red Hat OpenShift, slaat `kubectl apply -f <configmap_yaml_file.yaml`u het yaml-bestand op en past u de bijgewerkte ConfigMaps toe door de opdracht uit te voeren: . 

Bewerk en sla de bijgewerkte ConfigMaps voor Azure Red `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`Hat OpenShift op door de opdracht uit te voeren: .

## <a name="query-prometheus-metrics-data"></a>Gegevens over prometheus-statistieken van Query

Als u prometheus-statistieken wilt bekijken die zijn geschraapt door Azure Monitor en eventuele configuratie-/schraapfouten die door de agent zijn gerapporteerd, controleert u [de gegevens van Prometheus-statistieken](container-insights-log-search.md#query-prometheus-metrics-data) en [queryconfig of schrapfouten.](container-insights-log-search.md#query-config-or-scraping-errors)

## <a name="view-prometheus-metrics-in-grafana"></a>Bekijk Prometheus metrics in Grafana

Azure Monitor voor containers ondersteunt weergavestatistieken die zijn opgeslagen in uw Log Analytics-werkruimte in Grafana-dashboards. We hebben een sjabloon beschikbaar gesteld die u downloaden uit de [dashboardopslagplaats](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) van Grafana om u op weg te helpen en te verwijzen om u te helpen leren hoe u aanvullende gegevens uit uw bewaakte clusters opvragen om te visualiseren in aangepaste Grafana-dashboards. 

## <a name="review-prometheus-data-usage"></a>Bekijk het gegevensgebruik van Prometheus

Als u het opnamevolume van elke metrische grootte in GB per dag wilt identificeren om te begrijpen of deze hoog is, wordt de volgende query verstrekt.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
De uitvoer toont resultaten die vergelijkbaar zijn met de volgende resultaten:

![Queryresultaten van gegevensopnamevolume registreren](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Als u wilt inschatten wat elke grootte van de statistieken in GB is voor een maand om te begrijpen of het volume van de gegevens die in de werkruimte zijn opgenomen hoog is, wordt de volgende query verstrekt.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

De uitvoer toont resultaten die vergelijkbaar zijn met de volgende resultaten:

![Queryresultaten van gegevensopnamevolume registreren](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Meer informatie over het monitoren van gegevensgebruik en het analyseren van kosten is beschikbaar in [Gebruik en kosten beheren met Azure Monitor Logs.](../platform/manage-cost-storage.md)

## <a name="next-steps"></a>Volgende stappen

Lees [hier](container-insights-agent-config.md)meer over het configureren van de instellingen voor het verzamelen van agentapparaten voor stdout-, stderr- en omgevingsvariabelen van containerworkloads. 
