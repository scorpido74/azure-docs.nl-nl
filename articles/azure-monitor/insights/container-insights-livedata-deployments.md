---
title: Azure-monitor weergeven voor implementaties van containers (voorbeeld) | Microsoft Documenten
description: In dit artikel wordt de realtime weergave van Kubernetes-implementaties beschreven zonder kubectl in Azure Monitor voor containers te gebruiken.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75404779"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Implementaties bekijken (preview) in realtime

Met Azure Monitor voor containers emuleert de functie Weergaveimplementaties (preview) de directe `kubeclt get deployments` toegang `kubectl describe deployment {your deployment}` tot Kubernetes Deployment-objecten in realtime door de opdrachten en opdrachten bloot te leggen. 

>[!NOTE]
>AKS-clusters die zijn ingeschakeld als [privéclusters,](https://azure.microsoft.com/updates/aks-private-cluster/) worden niet ondersteund met deze functie. Deze functie is afhankelijk van directe toegang tot de Kubernetes API via een proxyserver vanuit uw browser. Als u netwerkbeveiliging inschakelt om de Kubernetes API van deze proxy te blokkeren, wordt dit verkeer geblokkeerd. 

>[!NOTE]
>Deze functie is beschikbaar in alle Azure-regio's, waaronder Azure China. Het is momenteel niet beschikbaar in Azure US Government.

Bekijk voor meer informatie de Kubernetes-documentatie over [implementaties.](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) 

## <a name="how-it-works"></a>Hoe werkt het?

De functie Live Data (preview) heeft rechtstreeks toegang tot de Kubernetes API en aanvullende informatie over het verificatiemodel vindt u [hier.](https://kubernetes.io/docs/concepts/overview/kubernetes-api/) 

De functie Implementaties (voorbeeld) voert een eenmalige (refreshable) `/apis/apps/v1/deployments`belasting uit ten opzichte van het eindpunt van de implementaties . Hiermee u een bepaalde implementatie selecteren en de beschrijvingsdetails `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`voor die specifieke implementatie laden ten opzichte van het implementatieeindpunt. 

Als **u Vernieuwen** linksboven op de pagina selecteert, wordt de implementatielijst vernieuwd. Dit simuleert het `kubectl` opnieuw uitvoeren van de opdracht. 

>[!IMPORTANT]
>Er worden geen gegevens permanent opgeslagen tijdens het gebruik van deze functie. Alle informatie die tijdens de sessie wordt vastgelegd, wordt verwijderd wanneer u uw browser sluit of ervan wegnavigeert.  

>[!NOTE]
>U geen livegegevens (Preview)-gegevens van de console vastmaken aan een Azure-dashboard.

## <a name="deployments-describe"></a>Implementaties beschrijven

Als u Details beschrijven voor een implementatie `kubectl describe deployment`wilt weergeven, wat overeenkomt met, voert u de volgende stappen uit.

1. Blader in de Azure-portal naar de AKS-clusterbrongroep en selecteer uw AKS-bron.

2. Kies op het AKS-clusterdashboard onder **Controle** aan de linkerkant **Insights**. 

3. Selecteer het tabblad **Implementaties (voorbeeld).**

    ![Weergave Implementaties in de Azure-portal](./media/container-insights-livedata-deployments/deployment-view.png)

De weergave toont een lijst met alle lopende implementaties, samen met de naamruimte en andere gedetailleerde informatie, waarbij de uitvoering van de opdracht wordt nabootst. `kubectl get deployments –all-namespaces` U de resultaten sorteren door een van de kolommen te selecteren. 

![Details van het deelvenster Implementaties eigenschappen](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Wanneer u een implementatie in de lijst selecteert, wordt er automatisch een eigenschappenvenster weergegeven aan de rechterkant van de pagina. Het toont informatie met betrekking tot de geselecteerde implementatie `kubectl describe deployment {deploymentName}`die u zou bekijken als u de opdracht zou uitvoeren. Het is u misschien opgevallen dat de beschrijvingsinformatie enkele details mist. Het meest in het bijzonder de **sjabloon** ontbreekt. Als u het tabblad **Raw** selecteert, u naar de niet-ongeparseerde details beschrijven navigeren.  

![Onbewerkte details van het deelvenster Implementaties](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Terwijl u de implementatiegegevens bekijkt, u containerlogboeken en gebeurtenissen in realtime bekijken. Selecteer de **liveconsole weergeven** en het consolevenster Live Data (voorbeeld) wordt weergegeven onder het gegevensraster voor implementaties, waar u live logboekgegevens in een continue stream bekijken. Als de indicator voor de status ophalen een groen vinkje weergeeft, dat helemaal rechts van het deelvenster staat, betekent dit dat gegevens kunnen worden opgehaald en dat deze naar uw console wordt gestreamd.

U ook filteren op naamruimte of clusterniveaugebeurtenissen. Zie [Live Data (preview) weergeven met Azure Monitor voor containers voor](container-insights-livedata-overview.md)meer informatie over de weergavegegevens in de console. 

![Implementaties weergeven van livegegevens in de console](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Volgende stappen

- Zie Azure [Kubernetes Service health weergeven](container-insights-analyze.md)om te blijven leren hoe u Azure Monitor gebruiken en andere aspecten van uw AKS-cluster controleren.

- Voorbeelden [van logboekquery's weergeven](container-insights-log-search.md#search-logs-to-analyze-data) om vooraf gedefinieerde query's en voorbeelden te bekijken om waarschuwingen, visualisaties of verdere analyses van uw clusters uit te voeren.
