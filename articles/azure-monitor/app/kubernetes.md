---
title: Gebruik Application Insights om uw Azure Kubernetes-service (AKS) of andere door Kubernetes gehoste toepassingen te bewaken-Azure Monitor | Microsoft Docs
description: Azure Monitor maakt gebruik van service mesh-technologie, Istio, op uw Kubernetes-cluster om toepassings bewaking te bieden voor elke Kubernetes-gehoste toepassing. Op deze manier kunt u Application Insights telemetrie met betrekking tot binnenkomende en uitgaande aanvragen verzamelen van en op basis van de gehele uitvoering in uw cluster.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: a5e73039db541023b1fd4a9b75e7c14030c8e219
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83797885"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Controle toepassings bewaking van nul voor Kubernetes-gehoste toepassingen met Istio-DEPRECATED

> [!IMPORTANT]
> Deze functionaliteit wordt momenteel afgeschaft en wordt na 1 augustus 2020 niet meer ondersteund.
> Momenteel kan de bewaking zonder code alleen worden ingeschakeld voor [Java via een zelfstandige agent](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Voor andere talen kunt u met de Sdk's uw apps controleren op AKS: [ASP.net core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [Java script](https://docs.microsoft.com/azure/azure-monitor/app/javascript)en [python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

Azure Monitor maakt nu gebruik van de teching van service-net op uw Kubernetes-cluster om te voorzien in de bewaking van de box-toepassing voor elke door Kubernetes gehoste app. Met de standaard functies van Application Insight zoals [toepassings overzicht](../../azure-monitor/app/app-map.md) kunt u uw afhankelijkheden model leren, [Live Metrics stream](../../azure-monitor/app/live-stream.md) voor realtime-bewaking, krachtige visualisaties met het [standaard dash board](../../azure-monitor/app/overview-dashboard.md), [metrische Explorer](../../azure-monitor/platform/metrics-getting-started.md)en [werkmappen](../../azure-monitor/platform/workbooks-overview.md). Deze functie helpt gebruikers bij het herkennen van prestatie knelpunten en fout HOTS POTS in alle Kubernetes-werk belastingen binnen een geselecteerde Kubernetes-naam ruimte. Door gebruik te maken van uw bestaande service-mesh-investeringen met technologieën zoals Istio, Azure Monitor automatisch gestuurde app-bewaking inschakelen zonder dat de code van uw toepassing hoeft te worden gewijzigd.

> [!NOTE]
> Dit is een van de vele manieren om toepassings bewaking op Kubernetes uit te voeren.U kunt ook alle apps die worden gehost in Kubernetes, met behulp van de [SDK van Application Insights](../../azure-monitor/azure-monitor-app-hub.yml) , zonder dat u een service-net hoeft te gebruiken. Als u Kubernetes wilt bewaken zonder de toepassing te instrumenteren met een SDK, kunt u de onderstaande methode gebruiken.

## <a name="prerequisites"></a>Vereisten

- Een [Kubernetes-cluster](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Console toegang tot het cluster om *kubectl*uit te voeren.
- Een [Application Insight-resource](create-new-resource.md)
- Een service-net hebben. Als er in uw cluster geen Istio is geïmplementeerd, kunt u leren hoe u [Istio in azure Kubernetes service installeert en gebruikt](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Functies

Door het gebruik van een controle toepassings bewaking van nul voor Kubernetes gehoste apps kunt u het volgende gebruiken:

- [Toepassingskaart](../../azure-monitor/app/app-map.md)
- [Live Stream metrische gegevens](../../azure-monitor/app/live-stream.md)
- [Dashboards](../../azure-monitor/app/overview-dashboard.md)
- [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [Gedistribueerd-traceren](../../azure-monitor/app/distributed-tracing.md)
- [End-to-end-transactie bewaking](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Installatiestappen

We voeren de volgende stappen uit om de oplossing in te scha kelen:
- Implementeer de toepassing (als deze nog niet is geïmplementeerd).
- Zorg ervoor dat de toepassing deel uitmaakt van het service-net.
- Bekijk verzamelde telemetrie.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Uw app configureren voor gebruik met een service-net

Istio ondersteunt twee manieren om [een pod te instrumenteren](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
In de meeste gevallen is het het gemakkelijkst om de Kubernetes-naam ruimte met uw toepassing te markeren met het *istio-injectie* label:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Omdat Service-Mesh gegevens uit de kabel opneemt, kunnen we het versleutelde verkeer niet onderscheppen. Gebruik een niet-versleuteld Protocol (bijvoorbeeld HTTP) voor verkeer dat het cluster niet verlaat. Voor extern verkeer dat moet worden versleuteld, kunt u [een TLS-beëindiging instellen](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) bij de ingangs controller.

Toepassingen die buiten het service net worden uitgevoerd, worden niet beïnvloed.

### <a name="deploy-your-application"></a>Uw toepassing implementeren

- Implementeer uw toepassing in de naam ruimte *mijn app-naam ruimte* . Als de toepassing al is geïmplementeerd en u de automatische injectie methode voor samen voegen hebt gevolgd, moet u het opnieuw samen stellen om ervoor te zorgen dat Istio de zijspan wagen injecteert; Start een rolling update of verwijder afzonderlijke peulen en wacht totdat ze opnieuw zijn gemaakt.
- Zorg ervoor dat uw toepassing voldoet aan de [Istio-vereisten](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Controle van toepassings bewaking op nul implementeren voor door Kubernetes gehoste apps

1. Down load en pak een [ *Application Insights adapter* release](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Navigeer naar */src/kubernetes/* in de map release.
3. *Application-Insights bewerken-istio-mixer-adapter-Deployment. yaml*
    - Bewerk de waarde van *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* omgevings variabele zodat de instrumentatie sleutel van de Application Insights resource in azure Portal de telemetrie bevat.
    - Als dat nodig is, bewerkt u de waarde van *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* omgevings variabele zodat deze een door komma's gescheiden lijst bevat met naam ruimten waarvoor u bewaking wilt inschakelen. Laat het veld leeg om alle naam ruimten te controleren.
4. Pas *elk* yaml-bestand toe dat is gevonden onder *src/kubernetes/* door de volgende handelingen uit te voeren (u moet nog steeds binnen */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Implementatie verifiëren

- Zorg ervoor dat Application Insights adapter is geïmplementeerd:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> In sommige gevallen is het afstemmen van nauw keurigheid vereist. Als u telemetrie wilt opnemen of uitsluiten voor het verzamelen van een afzonderlijke Pod, gebruikt u het label *appinsights/bewaking. enabled* op die pod. Dit heeft prioriteit boven alle configuratie op naam ruimte. Stel *appinsights/monitoring* in op *True* om de pod op te nemen en op *False* om deze uit te sluiten.

### <a name="view-application-insights-telemetry"></a>Application Insights telemetrie weer geven

- Genereer een voorbeeld aanvraag voor uw toepassing om te controleren of de bewaking goed werkt.
- Binnen 3-5 minuten begint u met het weer geven van telemetrie in de Azure Portal. Bekijk de sectie *toepassings overzicht* van uw Application Insights-resource in de portal.

## <a name="troubleshooting"></a>Problemen oplossen

Hieronder ziet u de stroom voor het oplossen van problemen wanneer telemetrie niet wordt weer gegeven in de Azure Portal zoals verwacht.

1. Zorg ervoor dat de toepassing wordt geladen en dat er aanvragen worden verzonden/ontvangen in gewoon HTTP. Omdat telemetrie is opgetrokken van de kabel, wordt versleuteld verkeer niet ondersteund. Als er geen binnenkomende of uitgaande aanvragen zijn, is er geen telemetrie.
2. Zorg ervoor dat de juiste instrumentatie sleutel is opgenomen in de omgevings variabele *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* in *Application-Insights-ISTIO-mixer-adapter-Deployment. yaml*. De instrumentatie sleutel vindt u op het tabblad *overzicht* van de Application Insights resource in de Azure Portal.
3. Zorg ervoor dat de juiste Kubernetes-naam ruimte is opgenomen in de omgevings variabele *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* in *Application-Insights-ISTIO-mixer-adapter-Deployment. yaml*. Laat het veld leeg om alle naam ruimten te controleren.
4. Zorg ervoor dat het Peul van uw toepassing is geïnjecteerd door Istio. Controleer of de Istio van de zijspan op elke pod bestaat.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Controleer of er een container met de naam *istio-proxy* wordt uitgevoerd op de pod.

5. De traceringen van de Application Insights-adapter weer geven.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Het aantal ontvangen telemetriegegevens wordt eenmaal per minuut bijgewerkt. Als deze niet meer dan een minuut groeit, wordt er geen telemetrie naar de adapter verzonden door Istio.
   Zoek naar fouten in het logboek.
6. Als is vastgesteld dat *Application Insight voor Kubernetes* -adapter niet wordt gevoed door telemetrie, raadpleegt u de mixer logboeken van Istio om erachter te komen waarom het geen gegevens naar de adapter verzendt:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Zoek naar eventuele fouten, met name bij communicatie met de *applicationinsightsadapter* -adapter.

## <a name="faq"></a>Veelgestelde vragen

Voor de meest recente informatie over de voortgang van dit project gaat u naar de [github van Application Insights adapter for Istio mixer](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)van het project.

## <a name="uninstall"></a>Verwijderen

Als u het product wilt verwijderen, moet u voor *elk* yaml-bestand dat is gevonden onder *src/kubernetes/* run:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over de samen werking van Azure Monitor en containers [Azure monitor voor containers Overview](../../azure-monitor/insights/container-insights-overview.md)
