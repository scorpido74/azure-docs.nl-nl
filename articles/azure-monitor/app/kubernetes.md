---
title: Toepassingsinzichten gebruiken om uw Azure Kubernetes Service (AKS) of andere Kubernetes gehoste toepassingen te controleren - Azure Monitor | Microsoft Documenten
description: Azure Monitor maakt gebruik van servicemesh-technologie, Istio, op uw Kubernetes-cluster om toepassingsbewaking te bieden voor elke Kubernetes gehoste toepassing. Hiermee u application insights telemetrie verzamelen met betrekking tot inkomende en uitgaande aanvragen van en naar pods die in uw cluster worden uitgevoerd.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 56a0cb66f5b54c817067970ab369d7ca471a1696
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132351"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Zero instrumentation application monitoring for Kubernetes hosted applications Zero instrumentation application monitoring for Kubernetes hosted applications Zero instrumentation application monitoring for Kubernetes hosted applications Zero instrumentation

> [!IMPORTANT]
> Deze functionaliteit is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Azure Monitor maakt nu gebruik van service mesh-technologie op uw Kubernetes-cluster om out-of-the-box applicatiebewaking te bieden voor elke Kubernetes gehoste app. Met standaard functies voor toepassingsinzicht zoals [toepassingstoewijzing](../../azure-monitor/app/app-map.md) om uw afhankelijkheden te modelleren, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) voor realtime bewaking, krachtige visualisaties met het [standaarddashboard,](../../azure-monitor/app/overview-dashboard.md) [Metric Explorer](../../azure-monitor/platform/metrics-getting-started.md)en [Werkmappen](../../azure-monitor/app/usage-workbooks.md). Met deze functie kunnen gebruikers prestatieknelpunten en fouthotspots in al hun Kubernetes-workloads in een geselecteerde Kubernetes-naamruimte herkennen. Door te profiteren van uw bestaande investeringen in servicemesh met technologieën zoals Istio, maakt Azure Monitor automatische app-monitoring mogelijk zonder dat de code van uw toepassing wordt gewijzigd.

> [!NOTE]
> Dit is een van de vele manieren om applicatiebewaking op Kubernetes uit te voeren.U ook elke app die in Kubernetes wordt gehost, gebruiken met de [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml) zonder dat u een servicemesh nodig hebt. Om Kubernetes te monitoren zonder de toepassing met een SDK te instrumenteren, u de onderstaande methode gebruiken.

## <a name="prerequisites"></a>Vereisten

- Een [Kubernetes-cluster](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Consoletoegang tot het cluster om *kubectl*uit te voeren .
- Een [application insight-bron](create-new-resource.md)
- Hebben een service mesh. Als istio niet geïmplementeerd in uw cluster, u leren hoe u [Istio installeert en gebruikt in Azure Kubernetes Service.](https://docs.microsoft.com/azure/aks/istio-install)

## <a name="capabilities"></a>Functionaliteit

Door zero instrumentation application monitoring te gebruiken voor Kubernetes gehoste apps, u het volledige nieuwe programma gebruiken:

- [Toepassingskaart](../../azure-monitor/app/app-map.md)
- [Live Stream-statistieken](../../azure-monitor/app/live-stream.md)
- [Dashboards](../../azure-monitor/app/overview-dashboard.md)
- [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [Gedistribueerd-tracering](../../azure-monitor/app/distributed-tracing.md)
- [End-to-end transactiebewaking](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Installatiestappen

Om de oplossing in te schakelen, voeren we de volgende stappen uit:
- Implementeer de toepassing (als deze nog niet is geïmplementeerd).
- Zorg ervoor dat de toepassing deel uitmaakt van de servicemesh.
- Observeer verzamelde telemetrie.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Uw app configureren om te werken met een servicemesh

Istio ondersteunt twee manieren [om een pod te instrumenteren.](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)
In de meeste gevallen is het het gemakkelijkst om de Kubernetes-naamruimte met uw toepassing te markeren met het *istio-injectielabel:*

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Aangezien servicemesh gegevens van de draad haalt, kunnen we het versleutelde verkeer niet onderscheppen. Gebruik een onversleuteld protocol (bijvoorbeeld HTTP) voor verkeer dat het cluster niet verlaat. Voor extern verkeer dat moet worden versleuteld, u overwegen [TLS-beëindiging in](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) te stellen bij de insin- en seincontroller.

Toepassingen die buiten het servicenetwerk worden uitgevoerd, worden niet beïnvloed.

### <a name="deploy-your-application"></a>Uw toepassing implementeren

- Implementeer uw toepassing in *naamruimte voor mijn-app-naamruimte.* Als de toepassing al is geïmplementeerd en u de hierboven beschreven automatische zijspaninjectiemethode hebt gevolgd, moet u pods opnieuw maken om ervoor te zorgen dat Istio zijn zijspan injecteert; start een rolling update of verwijder afzonderlijke pods en wacht tot ze opnieuw worden gemaakt.
- Zorg ervoor dat uw aanvraag voldoet aan [de Istio-vereisten.](https://istio.io/docs/setup/kubernetes/prepare/requirements/)

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Zero instrumentation application monitoring implementeren voor Kubernetes gehoste apps

1. Download en extraheer een [ *application insights adapter* release.](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)
2. Navigeer naar */src/kubernetes/* in de releasemap.
3. Bewerken *application-insights-istio-mixer-adapter-deployment.yaml*
    - bewerk de waarde van *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* omgevingsvariabele om de instrumentatiesleutel van de Application Insights-bron in Azure-portal te bevatten om de telemetrie te bevatten.
    - Bewerk indien nodig de waarde van *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* omgevingsvariabele om een door komma's gescheiden lijst met naamruimten te bevatten waarvoor u bewaking wilt inschakelen. Laat het leeg om alle naamruimten te controleren.
4. Pas *elk* YAML-bestand dat onder *src/kubernetes/* wordt gevonden toe door het volgende uit te voeren (je moet nog steeds in */src/kubernetes/* zitten):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Implementatie verifiëren

- Controleer of de Application Insights-adapter is geïmplementeerd:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> In sommige gevallen is fine-tuning vereist. Als u telemetrie voor een afzonderlijke pod wilt opnemen of uitsluiten, gebruikt u het label *appinsights/monitoring.enabled* op die pod. Dit heeft voorrang op alle op naamruimte gebaseerde configuratie. Stel *appinsights/monitoring.enabled in* *om* de pod op te nemen en *false* op te nemen om deze uit te sluiten.

### <a name="view-application-insights-telemetry"></a>Telemetrie van Application Insights weergeven

- Genereer een voorbeeldaanvraag tegen uw toepassing om te bevestigen dat de controle naar behoren functioneert.
- Binnen 3-5 minuten moet u telemetrie zien verschijnen in de Azure-portal. Bekijk het gedeelte *Toepassingskaart* van uw toepassingsstatistiekenbron in de portal.

## <a name="troubleshooting"></a>Problemen oplossen

Hieronder vindt u de stroom voor probleemoplossing die u gebruiken wanneer telemetrie niet wordt weergegeven in de Azure-portal zoals verwacht.

1. Zorg ervoor dat de toepassing onder belasting staat en dat aanvragen in eenvoudige HTTP-berichten worden verzonden/ontvangen. Aangezien telemetrie van de draad wordt getild, wordt versleuteld verkeer niet ondersteund. Als er geen inkomende of uitgaande aanvragen zijn, is er ook geen telemetrie.
2. Zorg ervoor dat de juiste instrumentatiesleutel wordt geleverd in de *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* omgevingsvariabele in *application-insights-istio-mixer-adapter-deployment.yaml.* De instrumentatiesleutel is te vinden op het tabblad *Overzicht* van de bron Application Insights in de Azure-portal.
3. Zorg ervoor dat de juiste *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* Kubernetes-naamruimte wordt geleverd in de ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES-omgevingsvariabele in *application-insights-istio-mixer-adapter-deployment.yaml*. Laat het leeg om alle naamruimten te controleren.
4. Zorg ervoor dat de pods van uw toepassing zijn zijspan geïnjecteerd door Istio. Controleer of Istio's zijspan op elke pod bestaat.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Controleer of er een container met de naam *istio-proxy* op de pod wordt uitgevoerd.

5. Bekijk de sporen van de Application Insights-adapter.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Het aantal ontvangen telemetrie-items wordt eenmaal per minuut bijgewerkt. Als het niet groeien minuut over minuut - geen telemetrie wordt verzonden naar de adapter door Istio.
   Zoek naar eventuele fouten in het logboek.
6. Als is vastgesteld dat *Application Insight voor Kubernetes-adapter* niet wordt gevoed telemetrie, controleert u de Mixer-logboeken van Istio om erachter te komen waarom het geen gegevens naar de adapter verzendt:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Zoek naar eventuele fouten, met name met betrekking tot communicatie met *applicationinsightsadapter* adapter.

## <a name="faq"></a>Veelgestelde vragen

Ga voor de laatste informatie over de voortgang van dit project naar de [Application Insights-adapter voor gitHub van het Istio Mixer-project.](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)

## <a name="uninstall"></a>Verwijderen

Om het product te verwijderen, voor *elk* YAML-bestand dat wordt gevonden onder *src/kubernetes/* run:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over de samenwerking tussen Azure Monitor en containers naar [Azure Monitor voor containers](../../azure-monitor/insights/container-insights-overview.md)
