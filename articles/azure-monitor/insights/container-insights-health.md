---
title: Monitor Kubernetes-clusterstatus met Azure Monitor voor containers | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de status van uw AKS- en niet-AKS-clusters bekijken en analyseren met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843987"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Inzicht in Kubernetes-clusterstatus met Azure Monitor voor containers

Met Azure Monitor voor containers controleert en rapporteert het de status van de beheerde infrastructuurcomponenten en alle knooppunten die worden uitgevoerd op een Kubernetes-cluster dat wordt ondersteund door Azure Monitor voor containers. Deze ervaring gaat verder dan de clusterstatus die is berekend en gerapporteerd in de [weergave met meerdere clusteren,](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)waar u nu begrijpen of een of meer knooppunten in het cluster resourcebeperkt zijn of dat een knooppunt of pod niet beschikbaar is die van invloed kan zijn op een lopende toepassing in het cluster op basis van samengestelde statistieken.

>[!NOTE]
>De functie Gezondheid is op dit moment in openbare preview.
>

Zie Azure Monitor voor [containers ingebouwde Azure Monitor voor meer](container-insights-onboard.md)informatie over het inschakelen van Azure Monitor voor containers.

>[!NOTE]
>Als u AKS Engine-clusters wilt ondersteunen, controleert u of deze voldoet aan het volgende:
>- Het maakt gebruik van de nieuwste versie van de [HELM client.](https://helm.sh/docs/using_helm/)
>- De containerversie is *microsoft/oms:ciprod11012019*. Zie [upgradeagent op kubernetes-cluster](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster)om de agent te upgraden.
>

## <a name="overview"></a>Overzicht

In Azure Monitor voor containers biedt de functie Gezondheid (voorbeeld) proactieve statusbewaking van uw Kubernetes-cluster om u te helpen problemen te identificeren en te diagnosticeren. Het geeft u de mogelijkheid om belangrijke gedetecteerde problemen te bekijken. Monitors voor de evaluatie van de status van uw cluster die worden uitgevoerd op de containeragent in uw cluster en de statusgegevens worden geschreven naar de **kubeHealth-tabel** in uw werkruimte Log Analytics. 

Kubernetes cluster status is gebaseerd op een aantal monitoring scenario's georganiseerd door de volgende Kubernetes objecten en abstracties:

- Kubernetes-infrastructuur - biedt een rollup van de Kubernetes API-server, ReplicaSets en DaemonSets die worden uitgevoerd op knooppunten die in uw cluster zijn geïmplementeerd door het cpu- en geheugengebruik te evalueren en de beschikbaarheid van pods te evalueren

    ![Rollup-weergave van de statusvan Kubernetes-infrastructuur](./media/container-insights-health/health-view-kube-infra-01.png)

- Knooppunten - biedt een roll-up van de knooppuntenpools en status van afzonderlijke knooppunten in elke groep, door het cpu- en geheugengebruik te evalueren en de status van een knooppunt zoals gerapporteerd door Kubernetes.

    ![Rollupweergave van de statusknooppunten](./media/container-insights-health/health-view-nodes-01.png)

Momenteel wordt alleen de status van een virtuele kubelet ondersteund. De status voor CPU en geheugengebruik van virtuele kublet-knooppunten wordt gerapporteerd als **Onbekend,** omdat er geen signaal van hen wordt ontvangen.

Alle monitors worden weergegeven in een hiërarchische indeling in het deelvenster Statushiërarchie, waarbij een geaggregeerde monitor die het Kubernetes-object of -abstractie (dat wil zeggen Kubernetes-infrastructuur of knooppunten) de bovenste monitor is die de gecombineerde status van alle afhankelijke onderliggende monitoren. De belangrijkste monitoringscenario's die worden gebruikt om de gezondheid af te leiden zijn:

* Evalueer het CPU-gebruik vanaf het knooppunt en de container.
* Evalueer het geheugengebruik vanaf het knooppunt en de container.
* Status van pods en knooppunten op basis van de berekening van hun kant-en-klare status gerapporteerd door Kubernetes.

Met de volgende pictogrammen wordt de status aangegeven:

|Pictogram|Betekenis|  
|--------|-----------|  
|![Pictogram groen vinkje geeft In orde aan](./media/container-insights-health/healthyicon.png)|Succes, status is in orde (groen)|  
|![Gele driehoek en uitroepteken is waarschuwing](./media/container-insights-health/warningicon.png)|Waarschuwing (geel)|  
|![Rode knop met witte X geeft kritieke status aan](./media/container-insights-health/criticalicon.png)|Kritiek (rood)|  
|![Pictogram Grijs-out](./media/container-insights-health/grayicon.png)|Onbekend (grijs)|  

## <a name="monitor-configuration"></a>Monitorconfiguratie

Zie [Statusmonitorconfiguratiehandleiding statusstatus](container-insights-health-monitors-config.md)voor ziet in een beter gedrag en de configuratie van elke monitor die Azure Monitor voor containers status ondersteunt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Status van een AKS- of niet-AKS-cluster weergeven

Toegang tot de functie Azure Monitor for containers Health (preview) is rechtstreeks beschikbaar vanuit een AKS-cluster door **Inzichten** te selecteren in het linkerdeelvenster in de Azure-portal. Selecteer **containers** in **Containers**de sectie Inzichten . 

Als u de status wilt weergeven vanuit een niet-AKS-cluster, dat een AKS Engine-cluster is dat on-premises of op Azure Stack wordt gehost, selecteert u **Azure Monitor** in het linkerdeelvenster in de Azure-portal. Selecteer **containers** in **Containers**de sectie Inzichten .  Selecteer op de pagina met meerdere clusteren het niet-AKS-cluster in de lijst.

Selecteer in Azure Monitor voor containers op de **clusterpagina** De optie **Status**.

![Voorbeeld van het dashboard clusterstatus](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Clusterstatus controleren

Wanneer de pagina Status wordt geopend, wordt **Kubernetes-infrastructuur** standaard geselecteerd in het raster **Gezondheidsaspect.**  Het raster vat de huidige status van Kubernetes-infrastructuur en clusterknooppunten samen. Als u een van beide statusaspecten selecteert, worden de resultaten bijgewerkt in het deelvenster Statushiërarchie (dat wil zeggen het middelste deelvenster) en worden alle onderliggende beeldschermen in een hiërarchische indeling weergegeven, waarbij de huidige status wordt weergegeven. Als u meer informatie wilt weergeven over een afhankelijke monitor, u er een selecteren en wordt er in een eigenschapvenster automatisch een venster op de pagina weergegeven. 

![Eigenschappenvenster clusterstatus](./media/container-insights-health/health-view-property-pane.png)

In het eigenschappenvenster leert u het volgende:

- Op het tabblad **Overzicht** wordt de huidige status van de geselecteerde monitor weergegeven, wanneer de monitor voor het laatst is berekend en wanneer de laatste statuswijziging heeft plaatsgevonden. Aanvullende informatie wordt weergegeven, afhankelijk van het type monitor dat in de hiërarchie is geselecteerd.

    Als u een geaggregeerde monitor selecteert in het deelvenster Statushiërarchie, wordt onder het tabblad **Overzicht** in het eigenschappenvenster een roll-up weergegeven van het totale aantal onderliggende beeldschermen in de hiërarchie en het aantal geaggregeerde monitors in een kritieke, waarschuwings- en gezonde status. 

    ![Tabblad Overzicht van het deelvenster Eigenschappen van de eigenschap voor aggregaatmonitor](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Als u een eenheidsmonitor selecteert in het deelvenster Statushiërarchie, wordt dit ook weergegeven onder **Laatste statuswijziging de** vorige monsters die in de afgelopen vier uur door de containeragent zijn berekend en gerapporteerd. Dit is gebaseerd op de berekening van de eenheidsmonitoren voor het vergelijken van verschillende opeenvolgende waarden om de status ervan te bepalen. Als u bijvoorbeeld de monitor van de *pod-ready state unit* hebt geselecteerd, worden de laatste twee voorbeelden weergegeven die worden beheerd door de parameter *ConsecutiveSamplesForStateTransition*. Zie voor meer informatie de gedetailleerde beschrijving van [eenheidsmonitoren](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Tabblad Overzicht van het deelvenster Eigenschappen van de eigenschap](./media/container-insights-health/health-overview-unit-monitor.png)

    Als de tijd die door **Laatste statuswijziging** wordt gerapporteerd een dag of ouder is, is deze het resultaat van geen statuswijzigingen voor de monitor. Als het laatste monster dat voor een eenheidsmonitor is ontvangen echter meer dan vier uur oud is, geeft dit waarschijnlijk aan dat de containeragent geen gegevens heeft verzonden. Als de agent weet dat er een bepaalde resource bestaat, bijvoorbeeld een knooppunt, maar deze geen gegevens heeft ontvangen van de CPU- of geheugengebruiksmonitors van het knooppunt (als voorbeeld), wordt de status van de monitor ingesteld op **Onbekend**.  

- Op het tabblad**Config** worden de standaardconfiguratieparameterinstellingen weergegeven (alleen voor eenheidsmonitoren, niet voor geaggregeerde monitoren) en hun waarden.
- Op het tabblad **Kennis** bevat het informatie over het gedrag van de monitor en hoe deze evalueert op de ongezonde toestand.

Het controleren van gegevens op deze pagina wordt niet automatisch vernieuwd en u moet **Vernieuwen** boven aan de pagina selecteren om de meest recente status van het cluster te zien.

## <a name="next-steps"></a>Volgende stappen

Voorbeelden [van logboekquery's](container-insights-log-search.md#search-logs-to-analyze-data) weergeven om vooraf gedefinieerde query's en voorbeelden te bekijken om uw clusters te waarschuwen, visualiseren of analyseren.
