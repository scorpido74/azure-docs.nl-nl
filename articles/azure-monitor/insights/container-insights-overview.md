---
title: Overzicht van Azure Monitor voor containers | Microsoft Docs
description: Dit artikel wordt beschreven van Azure Monitor voor containers die bewaakt AKS Container Insights-oplossing en de waarde die het biedt een door de bewaking van de status van uw AKS-clusters en exemplaren van de Container in Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/18/2019
ms.openlocfilehash: 12860d70cad2dbcfa3d06bf4df6939dd27ab3ab3
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279636"
---
# <a name="azure-monitor-for-containers-overview"></a>Azure Monitor voor containers: overzicht

Azure Monitor voor containers is een functie die is ontworpen voor het bewaken van de prestaties van container werkbelastingen die zijn geïmplementeerd op:

- Managed Kubernetes-clusters die worden gehost op de Azure Kubernetes-service (AKS)
- Azure Container Instances
- Zelf beheerde Kubernetes-clusters die worden gehost op Azure Stack of on-premises
- Azure Red Hat OpenShift

Controle van uw containers is kritiek, met name wanneer u een productiecluster op schaal, met meerdere toepassingen uitvoert.

Azure Monitor voor containers biedt u de zichtbaarheid van de prestaties door verzamelen geheugen en processors metrische gegevens van domeincontrollers, knooppunten en containers die beschikbaar in Kubernetes via de API voor metrische gegevens zijn. Er worden ook containerlogboeken verzameld.  Nadat u bewaking vanuit Kubernetes-clusters hebt ingeschakeld, worden metrische gegevens en logboeken automatisch voor u verzameld via een container versie van de Log Analytics-agent voor Linux. Metrische gegevens worden naar de metrische opslag opgeslagen en logboeken worden geschreven naar de logboeken archief die aan uw [log Analytics](../log-query/log-query-overview.md) -werk ruimte is gekoppeld. 

![Azure Monitor voor de architectuur van containers](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Wat kost Azure Monitor voor containers bieden?

Azure Monitor voor containers biedt een uitgebreide bewakings ervaring met behulp van verschillende functies van Azure Monitor, zodat u inzicht krijgt in de prestaties en status van uw Kubernetes-cluster en de container werkbelastingen. Met Azure Monitor voor containers kunt u het volgende doen:

* Identificeer de AKS-containers die worden uitgevoerd op het knooppunt en het gemiddelde gebruik van de processor en geheugen. Aan de hand van deze kennis kunt u knelpunten in de resource.
* Identificeer de processor en geheugen gebruik van groepen met containers en de containers die worden gehost in Azure Container Instances.  
* Bepaal waar de container zich bevindt in een controller of een pod. Aan de hand van deze kennis kunt u de algehele prestaties van de van de domeincontroller of de schil weergeven. 
* Controleer het brongebruik van workloads die worden uitgevoerd op de host die niet gerelateerd zijn aan de standaard processen die ondersteuning bieden voor de schil.
* Begrijp het gedrag van het cluster bij gemiddelde en zwaarste belasting. Deze kennis kunt u identificeren behoeften aan capaciteit en bepaal de maximale belasting van het cluster kan tolereren. 
* Configureer waarschuwingen om u proactief te informeren of op te nemen wanneer het CPU-en geheugen gebruik op knoop punten of containers de drempel waarden overschrijdt, of wanneer er een status wijziging in het cluster optreedt bij het samen vouwen van de infra structuur of de knooppunt status.
* Integreer met [Prometheus](https://prometheus.io/docs/introduction/overview/) om de metrische gegevens van de toepassing en werk belasting weer te geven die worden verzameld van knoop punten en Kubernetes met behulp van [query's](container-insights-log-search.md) om aangepaste waarschuwingen, Dash boards en gedetailleerde gedetailleerde analyses te maken.

    >[!NOTE]
    >De ondersteuning voor Prometheus is op dit moment een functie in open bare preview.
    >

* Bewaak de werkbelastingen van containers [die zijn geïmplementeerd](https://github.com/microsoft/OMS-docker/tree/aks-engine) op de AKS-engine on-premises en AKS- [engine op Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
* Bewaak de werk belasting van containers [die zijn geïmplementeerd in azure Red Hat open Shift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >De ondersteuning voor Red Hat open Shift is op dit moment een functie in Public Preview.
    >

Bekijk de volgende video over een dieper niveau waarmee u meer informatie kunt over het bewaken van uw AKS-cluster met Azure Monitor voor containers.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hoe krijg ik toegang tot deze functie?

U kunt toegang tot Azure Monitor voor twee manieren van Azure Monitor of rechtstreeks vanuit het geselecteerde AKS-cluster-containers. Vanuit Azure Monitor hebt u een wereld wijd perspectief van alle geïmplementeerde containers, die worden gecontroleerd en die niet, zodat u uw abonnementen en resource groepen kunt doorzoeken en filteren, en vervolgens inzoomen op Azure Monitor voor containers van de geselecteerde container.  Als dat niet het geval is, kunt u de functie rechtstreeks vanuit een geselecteerde AKS-container openen via de pagina AKS.  

![Overzicht van methoden voor toegang tot Azure Monitor voor containers](./media/container-insights-overview/azmon-containers-experience.png)

Als u geïnteresseerd bent in het bewaken en beheren van uw docker-en Windows-container hosts die buiten AKS worden uitgevoerd om de configuratie, controle en het gebruik van bronnen weer te geven, raadpleegt u de [container bewakings oplossing](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Volgende stappen

Als u wilt beginnen met het bewaken van uw Kubernetes-cluster, raadpleegt u [How to Enable the Azure monitor for containers](container-insights-onboard.md) voor meer informatie over de vereisten en beschik bare methoden voor het inschakelen van 
