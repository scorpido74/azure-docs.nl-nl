---
title: Live-gegevens weergeven (voorbeeld) met Azure Monitor voor containers | Microsoft Documenten
description: In dit artikel wordt de realtime weergave van Kubernetes-logboeken, gebeurtenissen en podstatistieken beschreven zonder kubectl in Azure Monitor voor containers te gebruiken.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9e7c7a7b7bf276b3451cee1d289b8b07ac0f40ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216555"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Kubernetes-logboeken, gebeurtenissen en podstatistieken in realtime bekijken

Azure Monitor voor containers bevat de functie Live Data (preview), een geavanceerde diagnostische functie waarmee u direct toegang hebt tot uw Azure Kubernetes Service (AKS)-containerlogboeken (stdout/stderror), gebeurtenissen en podmetrics. Het legt directe `kubectl logs -c`toegang `kubectl get` tot, `kubectl top pods`evenementen, en bloot. In een consolevenster worden de logboeken, gebeurtenissen en statistieken weergegeven die door de containerengine worden gegenereerd om problemen in realtime verder op te lossen.

Dit artikel geeft een gedetailleerd overzicht en helpt u te begrijpen hoe u deze functie gebruiken. 

>[!NOTE]
>AKS-clusters die zijn ingeschakeld als [privéclusters,](https://azure.microsoft.com/updates/aks-private-cluster/) worden niet ondersteund met deze functie. Deze functie is afhankelijk van directe toegang tot de Kubernetes API via een proxyserver vanuit uw browser. Als u netwerkbeveiliging inschakelt om de Kubernetes API van deze proxy te blokkeren, wordt dit verkeer geblokkeerd. 

>[!NOTE]
>Deze functie is beschikbaar in alle Azure-regio's, waaronder Azure China. Het is momenteel niet beschikbaar in Azure US Government.

Voor hulp bij het instellen of oplossen van problemen met de functie Live Data (preview) bekijk je onze [installatiehandleiding.](container-insights-livedata-setup.md) Deze functie heeft rechtstreeks toegang tot de Kubernetes API en aanvullende informatie over het verificatiemodel vindt u [hier.](https://kubernetes.io/docs/concepts/overview/kubernetes-api/) 

## <a name="live-data-preview-functionality-overview"></a>Overzicht van live data (preview)

### <a name="search"></a>Search

![Voorbeeld van het filtervenster van het consolevenster van Live-gegevens](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

De functie Live Data (preview) bevat zoekfunctionaliteit. In het veld **Zoeken** u resultaten filteren door een sleutelwoord of -term te typen en worden overeenkomende resultaten gemarkeerd om snel te kunnen worden beoordeeld. Tijdens het bekijken van gebeurtenissen u bovendien de resultaten beperken met behulp van de **filterpil** rechts van de zoekbalk. Afhankelijk van welke bron u hebt geselecteerd, bevat de pil een pod, naamruimte of cluster om uit te kiezen.  

### <a name="scroll-lock-and-pause"></a>Vergrendelen en onderbreken 

Als u automatisch scrollen wilt onderbreken en het gedrag van het deelvenster wilt beheren, u handmatig door de gelezen nieuwe gegevens bladeren, u de optie **Schuiven** gebruiken. Als u automatisch scrollen opnieuw wilt inschakelen, selecteert u de optie **Schuiven** opnieuw. U ook het ophalen van logboek- of gebeurtenisgegevens onderbreken door de optie **Onderbreken** te selecteren en wanneer u klaar bent om te hervatten, selecteert u **Afspelen**.  

![Live Data-consolevenster pauzeert live-weergave](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>We raden u aan autoscroll alleen voor een korte periode op te schorten of te pauzeren terwijl u een probleem oplost. Deze aanvragen kunnen van invloed zijn op de beschikbaarheid en beperking van de Kubernetes API op uw cluster. 

>[!IMPORTANT]
>Er worden geen gegevens permanent opgeslagen tijdens het gebruik van deze functie. Alle informatie die tijdens de sessie wordt vastgelegd, wordt verwijderd wanneer u uw browser sluit of ervan wegnavigeert. Gegevens blijven alleen aanwezig voor visualisatie binnen het vijf minuten venster van de metrische functie; alle statistieken ouder dan vijf minuten worden ook verwijderd. De bufferquery's van Live Data (preview) binnen redelijke geheugengebruikslimieten (moeten hier specifieker zijn, wat is redelijk?). 

## <a name="view-logs"></a>Logboeken weergeven

U realtime logboekgegevens bekijken terwijl deze worden gegenereerd door de containerengine vanuit de weergave **Knooppunten,** **Controllers**en **Containers.** Als u logboekgegevens wilt weergeven, voert u de volgende stappen uit.

1. Blader in de Azure-portal naar de AKS-clusterbrongroep en selecteer uw AKS-bron.

2. Kies op het AKS-clusterdashboard onder **Controle** aan de linkerkant **Insights**. 

3. Selecteer het tabblad **Knooppunten,** **Controllers**of **Containers.**

4. Selecteer een object in het prestatieraster en selecteer in het eigenschappenvenster aan de rechterkant de optie **Live-gegevens weergeven (voorbeeld).** Als het AKS-cluster is geconfigureerd met één aanmelding met Azure AD, wordt u gevraagd om te verifiëren bij het eerste gebruik tijdens die browsersessie. Selecteer uw account en volledige verificatie met Azure.  

    >[!NOTE]
    >Wanneer u de gegevens uit uw werkruimte Log Analytics bekijkt door de optie **Weergave in analyse** te selecteren in het deelvenster Eigenschappen, worden in de logboekzoekresultaten mogelijk **knooppunten,** **daemonsets,** **replicasets,** **taken,** **cron-taken,** **pods**en **containers** weergegeven die mogelijk niet meer bestaan. Een poging om logboeken te zoeken `kubectl` naar een container die niet beschikbaar is in zal ook hier mislukken. Bekijk de functie [Weergave in analytics](container-insights-log-search.md#search-logs-to-analyze-data) voor meer informatie over het bekijken van historische logboeken, gebeurtenissen en statistieken.  

Nadat u de verificatie hebt uitgevoerd, wordt het consolevenster Live Data (voorbeeld) weergegeven onder het prestatiegegevensraster, waar u logboekgegevens in een continue stroom bekijken. Als de indicator voor de status ophalen een groen vinkje weergeeft, dat helemaal rechts van het deelvenster staat, betekent dit dat gegevens kunnen worden opgehaald en dat deze naar uw console wordt gestreamd.  

![Weergavegegevens van knooppunteigenschappen](./media/container-insights-livedata-overview/node-properties-pane.png)  

De titel van het deelvenster toont de naam van de pod waarmee de container is gegroepeerd.

## <a name="view-events"></a>Gebeurtenissen weergeven

U realtime gebeurtenisgegevens bekijken terwijl deze worden gegenereerd door de containerengine vanuit de **weergave Knooppunten,** **Controllers,** **Containers**en **Implementatieen (voorbeeld)** wanneer een container, pod, knooppunt, ReplicaSet, DaemonSet, taak, CronJob of Implementatie is geselecteerd. Als u gebeurtenissen wilt weergeven, voert u de volgende stappen uit.

1. Blader in de Azure-portal naar de AKS-clusterbrongroep en selecteer uw AKS-bron.

2. Kies op het AKS-clusterdashboard onder **Controle** aan de linkerkant **Insights**. 

3. Selecteer het tabblad **Knooppunten**, **Controllers,** **Containers**of **Implementatieen (voorbeeld).**

4. Selecteer een object in het prestatieraster en selecteer in het eigenschappenvenster aan de rechterkant de optie **Live-gegevens weergeven (voorbeeld).** Als het AKS-cluster is geconfigureerd met één aanmelding met Azure AD, wordt u gevraagd om te verifiëren bij het eerste gebruik tijdens die browsersessie. Selecteer uw account en volledige verificatie met Azure.  

    >[!NOTE]
    >Wanneer u de gegevens uit uw werkruimte Log Analytics bekijkt door de optie **Weergave in analyse** te selecteren in het deelvenster Eigenschappen, worden in de logboekzoekresultaten mogelijk **knooppunten,** **daemonsets,** **replicasets,** **taken,** **cron-taken,** **pods**en **containers** weergegeven die mogelijk niet meer bestaan. Een poging om logboeken te zoeken `kubectl` naar een container die niet beschikbaar is in zal ook hier mislukken. Bekijk de functie [Weergave in analytics](container-insights-log-search.md#search-logs-to-analyze-data) voor meer informatie over het bekijken van historische logboeken, gebeurtenissen en statistieken.  

Nadat het consolevenster Live Data (voorbeeld) is geauthenticerd, wordt het deelvenster Prestatiegegevens weergegeven onder het prestatiegegevensraster. Als de indicator voor de status ophalen een groen vinkje weergeeft, dat helemaal rechts van het deelvenster staat, betekent dit dat gegevens kunnen worden opgehaald en dat deze naar uw console wordt gestreamd. 
    
Als het geselecteerde object een container was, selecteert u de optie **Gebeurtenissen** in het deelvenster. Als u een knooppunt, pod of controller hebt geselecteerd, wordt het weergeven van gebeurtenissen automatisch geselecteerd. 

![Weergavegebeurtenissen in het deelvenster Controller-eigenschappen](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

De titel van het deelvenster toont de naam van de pod waarmee de container is gegroepeerd.

### <a name="filter-events"></a>Gebeurtenissen filteren 

Tijdens het bekijken van gebeurtenissen u bovendien de resultaten beperken met behulp van de **filterpil** rechts van de zoekbalk. Afhankelijk van welke bron u hebt geselecteerd, bevat de pil een pod, naamruimte of cluster om uit te kiezen.  

## <a name="view-metrics"></a>Metrische gegevens bekijken 

U realtime metrische gegevens die door de containerengine **Nodes** worden **Controllers** gegenereerd, alleen bekijken wanneer een **pod** is geselecteerd. Als u statistieken wilt weergeven, voert u de volgende stappen uit.

1. Blader in de Azure-portal naar de AKS-clusterbrongroep en selecteer uw AKS-bron.

2. Kies op het AKS-clusterdashboard onder **Controle** aan de linkerkant **Insights**. 

3. Selecteer het tabblad **Knooppunten** of **controllers.**

4. Selecteer een **Pod-object** in het prestatieraster en selecteer in het eigenschappenvenster aan de rechterkant de optie **Live-gegevens weergeven (voorbeeld).** Als het AKS-cluster is geconfigureerd met één aanmelding met Azure AD, wordt u gevraagd om te verifiëren bij het eerste gebruik tijdens die browsersessie. Selecteer uw account en volledige verificatie met Azure.  

    >[!NOTE]
    >Wanneer u de gegevens uit uw werkruimte Log Analytics bekijkt door de optie **Weergave in analyse** te selecteren in het deelvenster Eigenschappen, worden in de logboekzoekresultaten mogelijk **knooppunten,** **daemonsets,** **replicasets,** **taken,** **cron-taken,** **pods**en **containers** weergegeven die mogelijk niet meer bestaan. Een poging om logboeken te zoeken `kubectl` naar een container die niet beschikbaar is in zal ook hier mislukken. Bekijk de functie [Weergave in analytics](container-insights-log-search.md#search-logs-to-analyze-data) voor meer informatie over het bekijken van historische logboeken, gebeurtenissen en statistieken.  

Nadat het consolevenster Live Data (voorbeeld) is geauthenticerd, wordt het deelvenster Prestatiegegevens weergegeven onder het prestatiegegevensraster. Metrische gegevens worden opgehaald en beginnen te streamen naar uw console voor presentatie in de twee grafieken. De titel van het deelvenster toont de naam van de pod waarmee de container is gegroepeerd.

![Voorbeeld van podstatistieken weergeven](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>Volgende stappen

- Zie Azure [Kubernetes Service health weergeven](container-insights-analyze.md)om te blijven leren hoe u Azure Monitor gebruiken en andere aspecten van uw AKS-cluster controleren.

- Voorbeelden [van logboekquery's weergeven](container-insights-log-search.md#search-logs-to-analyze-data) om vooraf gedefinieerde query's en voorbeelden te bekijken om waarschuwingen, visualisaties of verdere analyses van uw clusters uit te voeren.
