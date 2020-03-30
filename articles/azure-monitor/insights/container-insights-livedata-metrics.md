---
title: Statistieken in realtime weergeven met Azure Monitor voor containers | Microsoft Documenten
description: In dit artikel wordt de realtime weergave van statistieken beschreven zonder kubectl met Azure Monitor voor containers te gebruiken.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216585"
---
# <a name="how-to-view-metrics-in-real-time"></a>Statistieken in realtime bekijken

Azure Monitor for containers Met de functie Live Data (preview) u in realtime statistieken over knooppunt- en podstatus in een cluster visualiseren. Het emuleert directe `kubectl top nodes`toegang `kubectl get pods –all-namespaces`tot `kubectl get nodes` de , , en commando's te bellen, ontsmet, en visualiseren van de gegevens in prestatiegrafieken die zijn opgenomen in dit Insight. 

Dit artikel geeft een gedetailleerd overzicht en helpt u te begrijpen hoe u deze functie gebruiken.  

>[!NOTE]
>AKS-clusters die zijn ingeschakeld als [privéclusters,](https://azure.microsoft.com/updates/aks-private-cluster/) worden niet ondersteund met deze functie. Deze functie is afhankelijk van directe toegang tot de Kubernetes API via een proxyserver vanuit uw browser. Als u netwerkbeveiliging inschakelt om de Kubernetes API van deze proxy te blokkeren, wordt dit verkeer geblokkeerd. 

>[!NOTE]
>Deze functie is beschikbaar in alle Azure-regio's, waaronder Azure China. Het is momenteel niet beschikbaar in Azure US Government.

Voor hulp bij het instellen of oplossen van problemen met de functie Live Data (preview) bekijk je onze [setup-gids.](container-insights-livedata-setup.md)

## <a name="how-it-works"></a>Hoe werkt het? 

De functie Live Data (preview) heeft rechtstreeks toegang tot de Kubernetes API en aanvullende informatie over het verificatiemodel vindt u [hier.](https://kubernetes.io/docs/concepts/overview/kubernetes-api/) 

Deze functie voert standaard een pollingbewerking `/api/v1/nodes`uit `/apis/metrics.k8s.io/v1beta1/nodes`op `/api/v1/pods`de eindpunten van metrische gegevens (inclusief , en ), wat standaard elke vijf seconden is. Deze gegevens worden in de cache opgeslagen in uw browser en in kaart gebracht in de vier prestatiegrafieken in Azure Monitor voor containers op het tabblad **Cluster** door **Go Live (voorbeeld)** te selecteren. Elke volgende poll wordt in kaart gebracht in een rollend visualisatievenster van vijf minuten. 

![De optie Live gaan in de clusterweergave](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

Het polling-interval is geconfigureerd vanuit de vervolgkeuzelijst **Set interval** waarmee u elke 1, 5, 15 en 30 seconden de polling voor nieuwe gegevens instellen. 

![Ga live drop-down polling interval](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>We raden u aan het steminterval in te stellen op één seconde terwijl u een probleem voor een korte periode oplost. Deze aanvragen kunnen van invloed zijn op de beschikbaarheid en beperking van de Kubernetes API op uw cluster. Vervolgens opnieuw configureren naar een langer steminterval. 

>[!IMPORTANT]
>Er worden geen gegevens permanent opgeslagen tijdens het gebruik van deze functie. Alle informatie die tijdens deze sessie wordt vastgelegd, wordt onmiddellijk verwijderd wanneer u uw browser sluit of wegnavigeert van de functie. Gegevens blijven alleen aanwezig voor visualisatie binnen het venster van vijf minuten; alle statistieken ouder dan vijf minuten worden ook permanent verwijderd.

Deze grafieken kunnen niet worden vastgemaakt aan het laatste Azure-dashboard dat u in de live-modus hebt bekeken.

## <a name="metrics-captured"></a>Statistieken vastgelegd

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Gebruik van node-CPU%quot% / Gebruik van knooppuntgeheugen % 

Deze twee prestatiegrafieken worden toegewezen `kubectl top nodes` aan een equivalent van het aanroepen en vastleggen van de resultaten van de kolommen **CPU%** en **MEMORY%** naar de desbetreffende grafiek. 

![Voorbeeldresultaten van Kubectl-topknooppunten](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Grafiek van het cpu-gebruik santen](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Node Memory usage percent chart](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

De percentielberekeningen werken in grotere clusters om uitschieterknooppunten in uw cluster te identificeren. Bijvoorbeeld om te begrijpen of knooppunten onderbenut zijn voor schaal-down doeleinden. Met behulp **Min** van de Min-aggregatie u zien welke knooppunten een laag gebruik in het cluster hebben. Om verder te onderzoeken selecteert u het tabblad **Knooppunten** en sorteert u het raster op CPU of geheugengebruik.

Dit helpt u ook te begrijpen welke knooppunten tot hun grenzen worden verschoven en of scale-out nodig kan zijn. Als u gebruik maakt van zowel de **Max-** als de P95-aggregaties, u zien of er knooppunten in het cluster zijn met een hoog resourcegebruik. **P95** Voor verder onderzoek schakelt u opnieuw over naar het tabblad **Knooppunten.**

### <a name="node-count"></a>Aantal knooppunts

Dit prestatiediagram wordt toegewezen aan `kubectl get nodes` een equivalent van het aanroepen en toewijzen van de kolom **STATUS** aan een grafiek gegroepeerd op statustypen.

![Kubectl krijgt voorbeeldresultaten knooppunten](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Diagram voor het aantal knooppunten](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Knooppunten worden gerapporteerd in de status **Ready** or **Not Ready.** Ze worden geteld (en er wordt een totaal aantal gemaakt) en de resultaten van deze twee aggregaties worden in kaart gebracht.
Bijvoorbeeld om te begrijpen of uw knooppunten in mislukte statussen vallen. Met behulp van de **niet-klare** aggregatie u snel het aantal knooppunten in uw cluster zien dat momenteel in de **status Niet klaar** is.

### <a name="active-pod-count"></a>Actieve podtelling

Dit prestatiediagram wordt toegewezen aan `kubectl get pods –all-namespaces` een equivalent van het aanroepen en brengt de **kolom STATUS** in kaart de grafiek gegroepeerd op statustypen.

![Kubectl krijgen pods voorbeeldresultaten](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Grafiek van het aantal knooppuntenpod](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Namen van de `kubectl` status zoals geïnterpreteerd door kunnen niet precies overeenkomen in de grafiek. 

## <a name="next-steps"></a>Volgende stappen

Voorbeelden [van logboekquery's weergeven](container-insights-log-search.md#search-logs-to-analyze-data) om vooraf gedefinieerde query's en voorbeelden te bekijken om waarschuwingen, visualisaties of verdere analyses van uw clusters uit te voeren.
