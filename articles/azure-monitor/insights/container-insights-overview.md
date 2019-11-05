---
title: Overzicht van Azure Monitor voor containers | Microsoft Docs
description: In dit artikel worden Azure Monitor beschreven voor containers die de AKS-container Insights-oplossing bewaakt en de waarde die wordt geleverd door de status van uw AKS-clusters en Container Instances in azure te controleren.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 906ae92b0018430bdda02639642dd66ae2231dce
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477068"
---
# <a name="azure-monitor-for-containers-overview"></a>Overzicht van containers voor Azure Monitor

Azure Monitor voor containers is een functie die is ontworpen voor het bewaken van de prestaties van container werkbelastingen die zijn geïmplementeerd op Azure Container Instances, beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS) of zelf beheerde Kubernetes-clusters die worden gehost op Azure Stack. Het bewaken van uw containers is van cruciaal belang, vooral wanneer u een productie cluster op schaal uitvoert met meerdere toepassingen.

Azure Monitor voor containers biedt u inzicht in de prestaties door het verzamelen van geheugen-en processor metrieken van controllers, knoop punten en containers die beschikbaar zijn in Kubernetes via de API voor metrische gegevens. Er worden ook containerlogboeken verzameld.  Nadat u bewaking vanuit Kubernetes-clusters hebt ingeschakeld, worden metrische gegevens en logboeken automatisch voor u verzameld via een container versie van de Log Analytics-agent voor Linux. Metrische gegevens worden naar de metrische opslag opgeslagen en logboeken worden geschreven naar de logboeken archief die aan uw [log Analytics](../log-query/log-query-overview.md) -werk ruimte is gekoppeld. 

![Azure Monitor voor de architectuur van containers](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Wat biedt Azure Monitor voor containers?

Azure Monitor voor containers biedt een uitgebreide bewakings ervaring met behulp van verschillende functies van Azure Monitor, zodat u inzicht krijgt in de prestaties en status van uw Kubernetes-cluster en de container werkbelastingen. Met Azure Monitor voor containers kunt u het volgende doen:

* Identificeer AKS-containers die worden uitgevoerd op het knoop punt en hun gemiddelde processor-en geheugen gebruik. Deze kennis kan u helpen bij het identificeren van knel punten in de resource.
* Identificeer het processor-en geheugen gebruik van container groepen en de containers die worden gehost in Azure Container Instances.  
* Bepaal waar de container zich bevindt in een controller of een pod. Deze kennis kan u helpen de algehele prestaties van de controller of het Pod te bekijken. 
* Bekijk het resource gebruik van werk belastingen die op de host worden uitgevoerd en die geen verband houden met de standaard processen die ondersteuning bieden voor de pod.
* Meer informatie over het gedrag van het cluster in gemiddelde en zwaarste belasting. Deze kennis kan u helpen de capaciteits behoeften te identificeren en de maximale belasting te bepalen die het cluster kan vastlopen. 
* Configureer waarschuwingen om u proactief te informeren of op te nemen wanneer het CPU-en geheugen gebruik op knoop punten of containers de drempel waarden overschrijdt, of wanneer er een status wijziging optreedt in het cluster op de infra structuur, de knoop punten of de status van de werk belasting.
* Integreer met [Prometheus](https://prometheus.io/docs/introduction/overview/) om de metrische gegevens van de toepassing en werk belasting weer te geven die worden verzameld van knoop punten en Kubernetes met behulp van [query's](container-insights-log-search.md) om aangepaste waarschuwingen, Dash boards en gedetailleerde gedetailleerde analyses te maken.

    >[!NOTE]
    >De ondersteuning voor Prometheus is op dit moment een functie in open bare preview.
    >

* Bewaak de werkbelastingen van containers [die zijn geïmplementeerd](https://github.com/microsoft/OMS-docker/tree/aks-engine) op de AKS-engine on-premises en AKS- [engine op Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).

Bekijk de volgende video over een dieper niveau waarmee u meer informatie kunt over het bewaken van uw AKS-cluster met Azure Monitor voor containers.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hebt u toegang tot deze functie Hoe kan ik?

U kunt op twee manieren toegang krijgen tot Azure Monitor voor containers, vanuit Azure Monitor of rechtstreeks vanuit het geselecteerde AKS-cluster. Vanuit Azure Monitor hebt u een wereld wijd perspectief van alle geïmplementeerde containers, die worden gecontroleerd en die niet, zodat u uw abonnementen en resource groepen kunt doorzoeken en filteren, en vervolgens inzoomen op Azure Monitor voor containers van de geselecteerde container.  Als dat niet het geval is, kunt u de functie rechtstreeks vanuit een geselecteerde AKS-container openen via de pagina AKS.  

![Overzicht van methoden voor toegang tot Azure Monitor voor containers](./media/container-insights-overview/azmon-containers-experience.png)

Als u geïnteresseerd bent in het bewaken en beheren van uw docker-en Windows-container hosts die buiten AKS worden uitgevoerd om de configuratie, controle en het gebruik van bronnen weer te geven, raadpleegt u de [container bewakings oplossing](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Volgende stappen

- Als u wilt beginnen met het bewaken van uw AKS-cluster, raadpleegt u [How to Enable the Azure monitor for containers](container-insights-onboard.md) voor meer informatie over de vereisten en beschik bare methoden voor het inschakelen van 

- Om te beginnen met het bewaken van de AKS-engine op Azure Stack of Kubernetes on-premises geïmplementeerd, raadpleegt u [hybride Kubernetes-clusters configureren met Azure monitor voor containers](container-insights-hybrid-setup.md).  
