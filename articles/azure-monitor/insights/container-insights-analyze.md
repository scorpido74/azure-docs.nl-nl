---
title: Kubernetes-bewaking met Azure Monitor voor containers | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de prestaties van een Kubernetes-cluster met Azure Monitor voor containers bekijken en analyseren.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 227fe70512536790d179797394b6fba22e7eb50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298380"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>Uw Kubernetes-clusterprestaties controleren met Azure Monitor voor containers

Met Azure Monitor voor containers u de prestatiegrafieken en de status gebruiken om de werkbelasting van Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS), Azure Stack of andere omgevingvanuit twee perspectieven te controleren. U rechtstreeks vanuit het cluster controleren of u alle clusters in een abonnement bekijken vanuit Azure Monitor. Het bekijken van Azure Container Instances is ook mogelijk bij het bewaken van een specifiek AKS-cluster.

In dit artikel u inzicht krijgen in de twee perspectieven en hoe Azure Monitor u helpt bij het snel beoordelen, onderzoeken en oplossen van gedetecteerde problemen.

Zie Azure Monitor voor [containers ingebouwde Azure Monitor voor meer](container-insights-onboard.md)informatie over het inschakelen van Azure Monitor voor containers.

Azure Monitor biedt een multiclusterweergave met de status van alle bewaakte Kubernetes-clusters waarop Linux en Windows Server 2019 worden geïmplementeerd in resourcegroepen in uw abonnementen. Het toont clusters die zijn ontdekt in alle omgevingen die niet worden gecontroleerd door de oplossing. U de clusterstatus onmiddellijk begrijpen en vanaf hier u inzoomen op de prestatiepagina knooppunt en controller of navigeren om prestatiegrafieken voor het cluster te bekijken. Voor AKS-clusters die zijn ontdekt en geïdentificeerd als niet-gecontroleerd, u deze op elk gewenst moment controleren. 

De belangrijkste verschillen in het bewaken van een Windows Server-cluster met Azure Monitor voor containers in vergelijking met een Linux-cluster worden [hier](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) beschreven in het overzichtsartikel.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Weergave met meerdere clusters vanuit Azure Monitor

Als u de status van alle geïmplementeerde Kubernetes-clusters wilt weergeven, selecteert u **Monitor** in het linkerdeelvenster in de Azure-portal. Selecteer **containers** in **Containers**de sectie Inzichten . 

![Voorbeeld van azure monitor-dashboard met meerdere clusteren](./media/container-insights-analyze/azmon-containers-multiview.png)

U de resultaten die in het raster worden gepresenteerd, zo uitzoeken naar clusters die:

* **Azure** - AKS- en AKS-Engine-clusters gehost in Azure Kubernetes Service
* **Azure Stack (Preview)** - AKS-Engine clusters gehost op Azure Stack
* **Niet-Azure (Preview)** - Kubernetes-clusters die on-premises worden gehost
* **Alles** - Bekijk alle Kubernetes-clusters die worden gehost in Azure, Azure Stack en on-premises omgevingen die zijn toegewezen aan Azure Monitor voor containers

Als u clusters vanuit een specifieke omgeving wilt weergeven, selecteert u deze in de optie **Omgeving en** omgeving en linksboven op de pagina.

![Voorbeeld van de pilkiezer van het milieu](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

Op het tabblad **Bewaakte clusters** leest u het volgende:

- Hoeveel clusters zich in een kritieke of ongezonde staat bevinden, versus hoeveel clusters wel of niet worden gemeld (een onbekende status genoemd).
- Of alle [Azure Kubernetes Engine (AKS-engine)](https://github.com/Azure/aks-engine) implementaties in orde zijn.
- Hoeveel knooppunten en gebruikers- en systeempods per cluster worden geïmplementeerd.
- Hoeveel schijfruimte beschikbaar is en als er een capaciteitsprobleem is.

De opgenomen gezondheidstoestanden zijn: 

* **Gezond:** er worden geen problemen gedetecteerd voor de VM en het werkt naar behoefte. 
* **Kritiek**: Er worden een of meer kritieke problemen gedetecteerd die moeten worden aangepakt om de normale bedrijfstoestand zoals verwacht te herstellen.
* **Waarschuwing:** Er worden een of meer problemen gedetecteerd die moeten worden aangepakt of de gezondheidstoestand kan kritiek worden.
* **Onbekend:** als de service geen verbinding kon maken met het knooppunt of de pod, wordt de status gewijzigd in een onbekende status.
* **Niet gevonden:** de werkruimte, de brongroep of het abonnement dat de werkruimte voor deze oplossing bevat, is verwijderd.
* **Ongeautoriseerd:** de gebruiker heeft geen machtigingen nodig om de gegevens in de werkruimte te lezen.
* **Fout:** er is een fout opgetreden tijdens het lezen van gegevens uit de werkruimte.
* **Verkeerd geconfigureerd**: Azure Monitor voor containers is niet correct geconfigureerd in de opgegeven werkruimte.
* **Geen gegevens**: Gegevens hebben de afgelopen 30 minuten niet gemeld aan de werkruimte.

Status berekent de algehele clusterstatus als de *slechtste van* de drie statussen met één uitzondering. Als een van de drie statussen onbekend is, wordt in de algemene clusterstatus **Onbekende**weergegeven. 

In de volgende tabel ziet u een uitsplitsing van de berekening die de statussen voor een bewaakt cluster in de multiclusterweergave regelt.

| |Status |Beschikbaarheid |  
|-------|-------|-----------------|  
|**Gebruikerspod**| | |  
| |Goed |100% |  
| |Waarschuwing |90 - 99% |  
| |Kritiek |<90% |  
| |Onbekend |Indien niet gemeld in de laatste 30 minuten |  
|**Systeempod**| | |  
| |Goed |100% |
| |Waarschuwing |N.v.t. |
| |Kritiek |<100% |
| |Onbekend |Indien niet gemeld in de laatste 30 minuten |
|**Node** | | |
| |Goed |>85% |
| |Waarschuwing |60 - 84% |
| |Kritiek |<60% |
| |Onbekend |Indien niet gemeld in de laatste 30 minuten |

In de lijst met clusters u inzoomen op de **clusterpagina** door de naam van het cluster te selecteren. Ga vervolgens naar de **prestatiepagina Knooppunten** door de rollup van knooppunten in de kolom **Knooppunten** voor dat specifieke cluster te selecteren. U ook inzoomen op de **prestatiepagina Controllers** door de rollup van de kolom **Gebruikerspods** of **Systeempods te** selecteren.

## <a name="view-performance-directly-from-a-cluster"></a>Prestaties rechtstreeks vanuit een cluster weergeven

Toegang tot Azure Monitor voor containers is rechtstreeks beschikbaar vanuit een AKS-cluster door **Insights** > **Cluster** te selecteren in het linkerdeelvenster of wanneer u een cluster hebt geselecteerd in de multiclusterweergave. Informatie over uw cluster is ingedeeld in vier perspectieven:

- Cluster
- Knooppunten 
- Controllers 
- Containers

>[!NOTE]
>De ervaring die in de rest van dit artikel wordt beschreven, is ook van toepassing voor het weergeven van de prestaties en status van uw Kubernetes-clusters die worden gehost op Azure Stack of een andere omgeving wanneer deze zijn geselecteerd in de weergave met meerdere clusteren. 

De standaardpagina opent en toont vier lijnprestatiegrafieken met belangrijke prestatiestatistieken van uw cluster. 

![Voorbeeldprestatiegrafieken op het tabblad Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

De prestatiegrafieken geven vier prestatiestatistieken weer:

- **Node CPU-gebruik&nbsp;**: Een geaggregeerd perspectief van CPU-gebruik voor het hele cluster. Als u de resultaten voor het tijdsbereik wilt filteren, selecteert u **Avg,** **Min**, **50e,** **90e,** **95e**of **Max** in de percentielskiezer boven de grafiek. De filters kunnen afzonderlijk of gecombineerd worden gebruikt. 
- **Gebruik van&nbsp;het knooppuntgeheugen**: een geaggregeerd perspectief op geheugengebruik voor het hele cluster. Als u de resultaten voor het tijdsbereik wilt filteren, selecteert u **Avg,** **Min**, **50e,** **90e,** **95e**of **Max** in de percentielskiezer boven de grafiek. De filters kunnen afzonderlijk of gecombineerd worden gebruikt. 
- **Knooppunttelling**: een knooppunt telling en status van Kubernetes. Statussen van de weergegeven clusterknooppunten zijn Totaal, Klaar en Niet gereed. Ze kunnen afzonderlijk worden gefilterd of gecombineerd in de selector boven de grafiek. 
- **Active pod count**: Een pod telling en status van Kubernetes. Statussen van de weergegeven pods zijn Totaal, In behandeling, Lopend, Onbekend, Geslaagd of Mislukt. Ze kunnen afzonderlijk worden gefilterd of gecombineerd in de selector boven de grafiek. 

Gebruik de pijltoetsen Links en Rechts om elk gegevenspunt in de grafiek door te fietsen. Gebruik de pijl-omhoog en pijl-omlaag om door de percentiellijnen te fietsen. Selecteer het pictogram van de pin in de rechterbovenhoek van een van de grafieken om de geselecteerde grafiek vast te maken aan het laatste Azure-dashboard dat u hebt bekeken. Vanuit het dashboard u het formaat en de positie van de grafiek wijzigen. Als u de grafiek in het dashboard selecteert, wordt u omgeleid naar Azure Monitor voor containers en wordt het juiste bereik en de juiste weergave geladen.

Azure Monitor voor containers ondersteunt ook Azure Monitor [metrics explorer,](../platform/metrics-getting-started.md)waar u uw eigen plotgrafieken maken, trends correleren en onderzoeken en aan dashboards vastmaken. Vanuit metrics explorer u ook de criteria gebruiken die u hebt ingesteld om uw statistieken te visualiseren als basis voor een [op metrische gegevens gebaseerde waarschuwingsregel.](../platform/alerts-metric.md) 

## <a name="view-container-metrics-in-metrics-explorer"></a>Containerstatistieken weergeven in metrics explorer

In metrics explorer u geaggregeerde node- en podgebruiksstatistieken van Azure Monitor voor containers weergeven. In de volgende tabel worden de details samengevat om u te helpen begrijpen hoe u de metrische grafieken gebruiken om containerstatistieken te visualiseren.

|Naamruimte | Gegevens | Beschrijving | 
|----------|--------|-------------|
| insights.container/nodes | |
| | cpuUsageMillicores | Geaggregeerde meting van het CPU-gebruik in het cluster. Het is een CPU-kern verdeeld in 1000 eenheden (milli = 1000). Wordt gebruikt om het gebruik van kernen in een container te bepalen waar veel toepassingen mogelijk één kern gebruiken.| 
| | cpuUsagePercentage | Geaggregeerde gemiddelde CPU-benutting gemeten in percentage over het cluster.|
| | memoryRssBytes | Container RSS-geheugen dat wordt gebruikt in bytes.| 
| | memoryRssPercentage | Container RSS-geheugen gebruikt in procenten.|
| | memoryWorkingSetBytes | Gebruikte geheugen van de containerwerkgroep.| 
| | memoryWorkingSetPercentage | Container werkgeheugen gebruikt in procenten. | 
| | nodesCount | Een knooppunt telling van Kubernetes.|
| insights.container/pods | |
| | PodCount (PodCount) | Een pod telling van Kubernetes.|

U een statistiek [splitsen](../platform/metrics-charts.md#apply-splitting-to-a-chart) om deze te bekijken op dimensie en visualiseren hoe verschillende segmenten ervan zich tot elkaar verhouden. Voor een knooppunt u de grafiek segmenteren op basis van de *hostdimensie.* Vanuit een pod u deze segmenteren op de volgende dimensies:

* Controller
* Kubernetes-naamruimte
* Knooppunt
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Knooppunten, controllers en containerstatus analyseren

Wanneer u overschakelt naar de tabbladen **Knooppunten,** **Controllers**en **Containers,** wordt aan de rechterkant van de pagina automatisch een eigenschapsvenster weergegeven. Het toont de eigenschappen van het geselecteerde item, waaronder de labels die u hebt gedefinieerd om Kubernetes-objecten te ordenen. Wanneer een Linux-knooppunt is geselecteerd, wordt in de sectie **Lokale schijfcapaciteit** ook de beschikbare schijfruimte en het percentage weergegeven voor elke schijf die op het knooppunt wordt weergegeven. Selecteer **>>** de koppeling in het deelvenster om het deelvenster weer te geven of te verbergen.

Terwijl u de objecten in de hiërarchie uitvouwt, wordt het eigenschappenvenster bijgewerkt op basis van het geselecteerde object. In het deelvenster u ook Kubernetes-containerlogboeken (stdout/stderror), gebeurtenissen en podstatistieken bekijken door de koppeling **Live-gegevens (voorbeeld)** weergeven boven aan het deelvenster te selecteren. Zie [De livegegevens instellen (voorbeeld)](container-insights-livedata-setup.md)voor meer informatie over de configuratie die nodig is om deze gegevens te kunnen weergeven en te beheren. Terwijl u clusterbronnen bekijkt, u deze gegevens uit de container in realtime bekijken. Zie [Kubernetes-logboeken, gebeurtenissen en podmetrics in realtime bekijken](container-insights-livedata-overview.md)voor meer informatie over deze functie. Als u Kubernetes-logboekgegevens wilt weergeven die zijn opgeslagen in uw werkruimte op basis van vooraf gedefinieerde logboekzoekopdrachten, selecteert u **Containerlogboeken weergeven** in de vervolgkeuzelijst **Weergave in analytics.** Zie [Zoeklogboeken voor meer](container-insights-log-search.md#search-logs-to-analyze-data)informatie over dit onderwerp om gegevens te analyseren.

Gebruik de optie **Filter + Toevoegen** boven aan de pagina om de resultaten voor de weergave te filteren op **service,** **knooppunt,** **naamruimte**of **knooppuntgroep**. Nadat u het filterbereik hebt geselecteerd, selecteert u een van de waarden die worden weergegeven in het veld **Waarde(en selecteren).** Nadat het filter is geconfigureerd, wordt het wereldwijd toegepast terwijl het elk perspectief van het AKS-cluster bekijkt. De formule ondersteunt alleen het gelijke teken. U extra filters toevoegen bovenop de eerste om uw resultaten verder te verkleinen. Als u bijvoorbeeld een filter per **knooppunt**opgeeft, u alleen **Service** of **Naamruimte** selecteren voor het tweede filter.

Als u een filter opgeeft op het ene tabblad, blijft dit worden toegepast wanneer u een ander filter selecteert. Het wordt verwijderd nadat u het **x-symbool** naast het opgegeven filter hebt geselecteerd. 

Ga naar het tabblad **Knooppunten** en de rijhiërarchie volgt het Objectmodel Kubernetes, dat begint met een knooppunt in uw cluster. Vouw het knooppunt uit om een of meer pods op het knooppunt weer te geven. Als er meer dan één container is gegroepeerd op een pod, worden deze weergegeven als de laatste rij in de hiërarchie. U ook zien hoeveel niet-podgerelateerde workloads op de host worden uitgevoerd als de host processor- of geheugendruk heeft.

![Voorbeeld van de hiërarchie Kubernetes Node in de prestatieweergave](./media/container-insights-analyze/containers-nodes-view.png)

Windows Server-containers waarop het Windows Server 2019-besturingssysteem wordt uitgevoerd, worden weergegeven na alle op Linux gebaseerde knooppunten in de lijst. Wanneer u een Windows Server-knooppunt uitvouwt, u een of meer pods en containers weergeven die op het knooppunt worden uitgevoerd. Nadat een knooppunt is geselecteerd, worden in het deelvenster Eigenschappen versiegegevens weergegeven. Agentgegevens zijn uitgesloten omdat er geen agent is geïnstalleerd door Windows Server-knooppunten. 

![Voorbeeldknooppunthiërarchie met Windows Server-knooppunten weergegeven](./media/container-insights-analyze/nodes-view-windows.png) 

Virtuele knooppunten van Azure Container Instances waarop het Linux-besturingssysteem wordt uitgevoerd, worden weergegeven na het laatste AKS-clusterknooppunt in de lijst. Wanneer u een virtueel knooppunt ContainerInstances uitvouwt, u een of meer pods en containers voor containerinstanties weergeven die op het knooppunt worden uitgevoerd. Statistieken worden niet verzameld en gerapporteerd voor knooppunten, alleen voor pods.

![Voorbeeldhiërarchie knooppunt met containerinstanties weergegeven](./media/container-insights-analyze/nodes-view-aci.png)

Vanuit een uitgevouwen knooppunt u inzoomen vanaf de pod of container die op het knooppunt naar de controller wordt uitgevoerd om prestatiegegevens weer te geven die voor die controller zijn gefilterd. Selecteer de waarde onder de kolom **Controller** voor het specifieke knooppunt.
 
![Voorbeeld van knooppunt naar controller in de prestatieweergave](./media/container-insights-analyze/drill-down-node-controller.png)

Selecteer controllers of containers boven aan de pagina om de status en het gebruik van resources voor die objecten te controleren. Als u het geheugengebruik wilt controleren, selecteert u in de vervolgkeuzelijst **Metrische** gegevens de optie **GeheugenRSS** of **geheugenwerkset**. **Geheugen RSS** wordt alleen ondersteund voor Kubernetes versie 1.8 en hoger. Anders geeft u waarden voor **Min&nbsp; ** weer als *NaN&nbsp;*, een numerieke waarde van het gegevenstype die een ongedefinieerde of niet-vertegenwoordigbare waarde vertegenwoordigt.

![Prestatieweergave containerknooppunten](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Geheugen werkset** toont zowel de resident geheugen en virtueel geheugen (cache) opgenomen en is een totaal van wat de toepassing gebruikt. **Geheugen RSS** toont alleen het hoofdgeheugen (dat is niets anders dan de resident geheugen met andere woorden). Deze statistiek toont de werkelijke capaciteit van het beschikbare geheugen. Wat is het verschil tussen resident geheugen en virtueel geheugen?

- Resident geheugen of hoofdgeheugen, is de werkelijke hoeveelheid machinegeheugen beschikbaar voor de knooppunten van het cluster.

- Virtueel geheugen is gereserveerd harde schijf ruimte (cache) gebruikt door het besturingssysteem om gegevens uit te wisselen van geheugen naar schijf wanneer onder geheugendruk, en vervolgens terug te halen naar het geheugen wanneer dat nodig is.

Standaard zijn prestatiegegevens gebaseerd op de afgelopen zes uur, maar u het venster wijzigen met de optie **TimeRange** linksboven. U de resultaten ook binnen het tijdsbereik filteren door **Min**, **Avg**, **50e,** **90e,** **95e**en **Max** te selecteren in de percentielkiezer. 

![Percentielselectie voor gegevensfiltering](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Wanneer u de boventoon aanwijzer tovert onder de kolom **Trend,** geeft elke balk het CPU- of geheugengebruik weer, afhankelijk van welke statistiek is geselecteerd, binnen een voorbeeldperiode van 15 minuten. Nadat u het trenddiagram via een toetsenbord hebt geselecteerd, gebruikt u de alt+page-uptoets of alt+page down-toets om elke balk afzonderlijk te doorlopen. Je krijgt dezelfde details als je zou doen als je boven de bar zweefde.

![Voorbeeld van zwevende grafiek met trendbalk](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

In het volgende voorbeeld is de waarde *aks-nodepool1-* voor **containers** 9 voor het eerste knooppunt in de lijst. Deze waarde is een oproling van het totale aantal ingezette containers.

![Voorbeeld van het oprollen van containers per knooppunt](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Met deze informatie u snel bepalen of u een goede balans hebt tussen containers tussen knooppunten in uw cluster. 

De informatie die wordt weergegeven wanneer u het tabblad **Knooppunten bekijkt,** wordt beschreven in de volgende tabel.

| Kolom | Beschrijving | 
|--------|-------------|
| Name | De naam van de gastheer. |
| Status | Kubernetes-weergave van de knooppuntstatus. |
| Min&nbsp;%,&nbsp;Avg %,&nbsp;50e&nbsp;%, 90e %, 95e&nbsp;%, Max&nbsp;%  | Gemiddeld nodepercentage op basis van percentiel tijdens de geselecteerde duur. |
| Min, Avg, 50e, 90e, 95e, Max | De werkelijke waarde van de gemiddelde knooppunten op basis van percentiel tijdens de geselecteerde tijdsduur. De gemiddelde waarde wordt gemeten aan de basis van de CPU/Memory-limiet die is ingesteld voor een knooppunt. Voor pods en containers is dit de gemiddelde waarde die door de host wordt gerapporteerd. |
| Containers | Aantal containers. |
| Uptime | Geeft de tijd weer sinds een knooppunt is gestart of opnieuw is opgestart. |
| Controller | Alleen voor containers en peulen. Het laat zien in welke controller het zich bevindt. Niet alle pods bevinden zich in een controller, dus sommige kunnen **N/A**weergeven. | 
| Trend&nbsp;Min %, Avg&nbsp;%, 50e&nbsp;&nbsp;&nbsp;%, 90e %, 95e %, Max&nbsp;% | De trend van staafgrafieken vertegenwoordigt het gemiddelde percentielmetrische percentage van de controller. |

Mogelijk ziet u een werkbelasting na het uitbreiden van een knooppunt met de naam **Ander proces**. Het vertegenwoordigt niet-gecontaineriseerde processen die op uw knooppunt worden uitgevoerd en omvat:

* Zelfbeheerde of beheerde Kubernetes niet-gecontaineriseerde processen

* Runtime-processen voor containers  

* Kubelet  

* Systeemprocessen die op uw knooppunt worden uitgevoerd

* Andere niet-Kubernetes-workloads die worden uitgevoerd op nodehardware of VM

Het wordt berekend door: *Totaal gebruik van CAdvisor-gebruik* - *van containerized proces*.  

Selecteer **Controllers**in de kiezer .

![De weergave Controllers selecteren](./media/container-insights-analyze/containers-controllers-tab.png)

Hier u de prestatiestatus van uw controllers en containerinstanties virtueel knooppuntcontrollers of virtuele nodepods bekijken die niet zijn verbonden met een controller.

![\<Prestatieweergave Naam> controllers](./media/container-insights-analyze/containers-controllers-view.png)

De rijhiërarchie begint met een controller. Wanneer u een controller uitvouwt, bekijkt u een of meer pods. Vouw een pod uit en in de laatste rij wordt de container weergegeven die is gegroepeerd op de pod. Vanuit een uitgebreide controller u inzoomen op het knooppunt waarop het wordt uitgevoerd om prestatiegegevens weer te geven die voor dat knooppunt zijn gefilterd. Container Instances-pods die niet zijn verbonden met een controller, worden als laatste in de lijst weergegeven.

![Voorbeeldhiërarchie voor controllers met containerinstanties-pods weergegeven](./media/container-insights-analyze/controllers-view-aci.png)

Selecteer de waarde onder de kolom **Knooppunt** voor de specifieke controller.

![Voorbeeld van knooppunt naar controller in de prestatieweergave](./media/container-insights-analyze/drill-down-controller-node.png)

De informatie die wordt weergegeven wanneer u controllers bekijkt, wordt beschreven in de volgende tabel.

| Kolom | Beschrijving | 
|--------|-------------|
| Name | De naam van de controller.|
| Status | De oprolstatus van de containers nadat deze is uitgevoerd met status zoals *OK,* *Beëindigd,* *Mislukt,* *Gestopt*of *Onderbroken*. Als de container wordt uitgevoerd, maar de status niet goed is weergegeven of niet is opgehaald door de agent en niet langer dan 30 minuten heeft gereageerd, is de status *Onbekend*. In de volgende tabel vindt u meer informatie over het statuspictogram.|
| Min&nbsp;%,&nbsp;Avg %,&nbsp;50e&nbsp;%, 90e %, 95e&nbsp;%, Max&nbsp;%| Rollupgemiddelde van het gemiddelde percentage van elke entiteit voor de geselecteerde statistiek en percentiel. |
| Min, Avg, 50e, 90e, 95e, Max  | Rollup van de gemiddelde CPU millicore of geheugenprestaties van de container voor het geselecteerde percentiel. De gemiddelde waarde wordt gemeten aan de basis van de CPU/Memory-limiet die is ingesteld voor een pod. |
| Containers | Totaal aantal containers voor de controller of pod. |
| Opnieuw opgestart | Rollup van het aantal herstarten van containers. |
| Uptime | Geeft de tijd weer sinds een container is gestart. |
| Knooppunt | Alleen voor containers en peulen. Het laat zien in welke controller het zich bevindt. | 
| Trend&nbsp;Min %, Avg&nbsp;%, 50e&nbsp;&nbsp;&nbsp;%, 90e %, 95e %, Max&nbsp;% | De trend van staafgrafiek en vertegenwoordigt de gemiddelde percentielstatistiek van de controller. |

De pictogrammen in het statusveld geven de online status van de containers aan.
 
| Pictogram | Status | 
|--------|-------------|
| ![Pictogram Status klaar maken](./media/container-insights-analyze/containers-ready-icon.png) | Hardlopen (klaar)|
| ![Pictogram Wacht- of Onderbroken status](./media/container-insights-analyze/containers-waiting-icon.png) | Wachten of onderbroken|
| ![Laatst gerapporteerd actief statuspictogram](./media/container-insights-analyze/containers-grey-icon.png) | Laatst gemeld, maar heeft niet gereageerd voor meer dan 30 minuten|
| ![Pictogram Geslaagde status](./media/container-insights-analyze/containers-green-icon.png) | Gestopt of kan niet worden gestopt|

In het statuspictogram wordt een telling weergegeven op basis van wat de pod biedt. Het toont de slechtste twee toestanden, en wanneer u over de status zweeft, wordt een rollupstatus weergegeven van alle pods in de container. Als er geen gereedstaande status is, wordt de statuswaarde **(0)** weergegeven.

Selecteer **containers**in de kiezer .

![De weergave Containers selecteren](./media/container-insights-analyze/containers-containers-tab.png)

Hier u de prestatiestatus van uw Azure Kubernetes- en Azure Container Instances-containers bekijken. 

![\<Prestatieweergave> containers benoemen](./media/container-insights-analyze/containers-containers-view.png)

Vanuit een container u inzoomen op een pod of knooppunt om prestatiegegevens weer te geven die voor dat object zijn gefilterd. Selecteer de waarde onder de kolom **Pod** of **Knooppunt** voor de specifieke container.

![Voorbeeld van inzoomen van knooppunt naar containers in de prestatieweergave](./media/container-insights-analyze/drill-down-controller-node.png)

De informatie die wordt weergegeven wanneer u containers bekijkt, wordt beschreven in de volgende tabel.

| Kolom | Beschrijving | 
|--------|-------------|
| Name | De naam van de controller.|
| Status | Status van de containers, indien aanwezig. In de volgende tabel vindt u meer informatie over het statuspictogram.|
| Min&nbsp;%,&nbsp;Avg %,&nbsp;50e&nbsp;%, 90e %, 95e&nbsp;%, Max&nbsp;% | De roll-up van het gemiddelde percentage van elke entiteit voor de geselecteerde statistiek en percentiel. |
| Min, Avg, 50e, 90e, 95e, Max | De rollup van de gemiddelde CPU-millicore of geheugenprestaties van de container voor het geselecteerde percentiel. De gemiddelde waarde wordt gemeten aan de basis van de CPU/Memory-limiet die is ingesteld voor een pod. |
| Pod | Container waar de pod zich bevindt.| 
| Knooppunt |  Knooppunt waar de container zich bevindt. | 
| Opnieuw opgestart | Geeft de tijd weer sinds een container is gestart. |
| Uptime | Geeft de tijd weer sinds een container is gestart of opnieuw is opgestart. |
| Trend&nbsp;Min %, Avg&nbsp;%, 50e&nbsp;&nbsp;&nbsp;%, 90e %, 95e %, Max&nbsp;% | De trend van staafgrafieken vertegenwoordigt het gemiddelde percentielmetrische percentage van de container. |

De pictogrammen in het statusveld geven de online statussen van pods aan, zoals beschreven in de volgende tabel.
 
| Pictogram | Status |  
|--------|-------------|  
| ![Pictogram Status klaar maken](./media/container-insights-analyze/containers-ready-icon.png) | Hardlopen (klaar)|  
| ![Pictogram Wacht- of Onderbroken status](./media/container-insights-analyze/containers-waiting-icon.png) | Wachten of onderbroken|  
| ![Laatst gerapporteerd actief statuspictogram](./media/container-insights-analyze/containers-grey-icon.png) | Laatst gemeld, maar heeft niet gereageerd in meer dan 30 minuten|  
| ![Pictogram Beëindigd status](./media/container-insights-analyze/containers-terminated-icon.png) | Gestopt of kan niet worden gestopt|  
| ![Pictogram Mislukte status](./media/container-insights-analyze/containers-failed-icon.png) | Mislukte status |  

## <a name="workbooks"></a>Werkmappen

Werkmappen combineren tekst, [logboekquery's,](../log-query/query-language.md) [statistieken](../platform/data-platform-metrics.md)en parameters in uitgebreide interactieve rapporten. Werkmappen kunnen worden bewerkt door andere teamleden die toegang hebben tot dezelfde Azure-resources.

Azure Monitor voor containers bevat vier werkmappen om u op weg te helpen:

- **Schijfcapaciteit:** presenteert interactieve schijfgebruiksdiagrammen voor elke schijf die aan het knooppunt in een container wordt weergegeven met de volgende perspectieven:

    - Schijfprocentgebruik voor alle schijven.
    - Vrije schijfruimte voor alle schijven.
    - Een raster dat de schijf van elk knooppunt, het percentage gebruikte ruimte, de trend van percentage gebruikte ruimte, vrije schijfruimte (GiB) en trend van vrije schijfruimte (GiB) weergeeft. Wanneer een rij in de tabel is geselecteerd, wordt het percentage gebruikte ruimte en vrije schijfruimte (GiB) onder de rij weergegeven. 

- **Schijf IO:** presenteert interactieve schijfgebruiksdiagrammen voor elke schijf die aan het knooppunt in een container wordt gepresenteerd door de volgende perspectieven:

    - Schijf I/O samengevat over alle schijven door bytes per seconde te lezen, schrijft bytes per seconde en lees en schrijf bytes per seconde trends.
    - Acht prestatiegrafieken tonen belangrijke prestatie-indicatoren om knelpunten in schijf-i/o te meten en te identificeren.

- **Kubelet**: Bevat twee rasters die de bedrijfsstatistieken van belangrijke knooppunts weergeven:

    - Overzicht per knooppuntraster vat de totale bewerking, totale fouten en succesvolle bewerkingen per percentage en trend voor elk knooppunt samen.
    - Overzicht op bewerkingstype vat voor elke bewerking de totale bewerking, totale fouten en succesvolle bewerkingen per percentage en trend samen.

- **Netwerk:** presenteert interactieve netwerkgebruiksdiagrammen voor de netwerkadapter van elk knooppunt en een raster presenteert de belangrijkste prestatie-indicatoren om de prestaties van uw netwerkadapters te meten.

U hebt toegang tot deze werkmappen door ze allemaal te selecteren in de vervolgkeuzelijst **Werkmappen weergeven.**

![Vervolgkeuzelijst Werkmappen weergeven](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Volgende stappen

- Bekijk [Prestatiewaarschuwingen maken met Azure Monitor voor containers](container-insights-alerts.md) voor meer informatie over het maken van waarschuwingen voor een hoog CPU- en geheugengebruik ter ondersteuning van uw DevOps of operationele processen en procedures.

- Voorbeelden [van logboekquery's](container-insights-log-search.md#search-logs-to-analyze-data) weergeven om vooraf gedefinieerde query's en voorbeelden te bekijken om uw clusters te waarschuwen, visualiseren of analyseren.

- Bekijk [de monitorclusterstatus](container-insights-health.md) voor meer informatie over het weergeven van de status van uw Kubernetes-cluster.
