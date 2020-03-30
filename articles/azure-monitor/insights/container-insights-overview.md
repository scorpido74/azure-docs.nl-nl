---
title: Overzicht van Azure Monitor voor containers | Microsoft Documenten
description: In dit artikel wordt beschreven Azure Monitor voor containers die de AKS Container Insights-oplossing en de waarde die deze levert, bewaakt door de status van uw AKS-clusters en containerinstances in Azure te bewaken.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275228"
---
# <a name="azure-monitor-for-containers-overview"></a>Overzicht van Azure Monitor voor containers

Azure Monitor voor containers is een functie die is ontworpen om de prestaties van containerworkloads te controleren die zijn geïmplementeerd op:

- Beheerde Kubernetes-clusters gehost op [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)
- Zelfbeheerde Kubernetes-clusters gehost op Azure met [AKS Engine](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Zelfbeheerde Kubernetes-clusters gehost op [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) of on-premises
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Azure Monitor voor containers ondersteunt clusters met het Linux- en Windows Server 2019-besturingssysteem. 

Het bewaken van uw containers is van cruciaal belang, vooral wanneer u een productiecluster uitvoert, op schaal, met meerdere toepassingen.

Azure Monitor voor containers geeft u inzicht in de prestaties door geheugen- en processorstatistieken te verzamelen van controllers, knooppunten en containers die beschikbaar zijn in Kubernetes via de Metrics API. Er worden ook containerlogboeken verzameld.  Nadat u monitoring van Kubernetes-clusters hebt ingeschakeld, worden statistieken en logboeken automatisch voor u verzameld via een containerversie van de Log Analytics-agent voor Linux. Statistieken worden geschreven naar het statistiekenarchief en logboekgegevens worden geschreven naar de logboekarchief die is gekoppeld aan uw [Logboekanalysewerkruimte.](../log-query/log-query-overview.md) 

![Azure Monitor voor containersarchitectuur](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Wat biedt Azure Monitor voor containers?

Azure Monitor voor containers biedt een uitgebreide monitoringervaring met behulp van verschillende functies van Azure Monitor. Met deze functies u inzicht krijgen in de prestaties en status van uw Kubernetes-cluster met het Linux- en Windows Server 2019-besturingssysteem en de containerworkloads. Met Azure Monitor voor containers u:

* Identificeer AKS-containers die op het knooppunt worden uitgevoerd en het gemiddelde processor- en geheugengebruik. Deze kennis kan u helpen knelpunten in resources te identificeren.
* Identificeer het processor- en geheugengebruik van containergroepen en hun containers die worden gehost in Azure Container Instances.  
* Bepaal waar de container zich in een controller of een pod bevindt. Met deze kennis u de algehele prestaties van de controller of pod bekijken. 
* Controleer het resourcegebruik van workloads die op de host worden uitgevoerd en die geen verband houden met de standaardprocessen die de pod ondersteunen.
* Begrijp het gedrag van het cluster onder gemiddelde en zwaarste belastingen. Deze kennis kan u helpen bij het identificeren van capaciteitsbehoeften en het bepalen van de maximale belasting die het cluster kan dragen. 
* Configureer waarschuwingen om u proactief op de hoogte te stellen of op te nemen wanneer het cpu- en geheugengebruik op knooppunten of containers uw drempelwaarden overschrijdt of wanneer er een statuswijziging optreedt in het cluster op de rollup van de status infrastructuur of knooppunten.
* Integreer met [Prometheus](https://prometheus.io/docs/introduction/overview/) om toepassings- en workloadstatistieken te bekijken die het verzamelt van knooppunten en Kubernetes met behulp van query's om aangepaste [waarschuwingen,](container-insights-log-search.md) dashboards en gedetailleerde gedetailleerde analyses uit te voeren.
* Monitor containerworkloads die zijn geïmplementeerd op [AKS Engine](https://github.com/Azure/aks-engine) on-premises en [AKS Engine op Azure Stack.](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)
* Monitor containerworkloads [die zijn geïmplementeerd in Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Ondersteuning voor Azure Red Hat OpenShift is een functie in openbare preview op dit moment.
    >

De belangrijkste verschillen in het bewaken van een Windows Server-cluster ten opzichte van een Linux-cluster zijn de volgende:

- Geheugen RSS-statistiek is niet beschikbaar voor Windows-knooppunt en containers.
- Informatie over de opslagcapaciteit van de schijf is niet beschikbaar voor Windows-knooppunten.
- Containerlogboeken zijn niet beschikbaar voor containers die worden uitgevoerd in Windows-knooppunten.
- Ondersteuning voor live gegevens (preview) is beschikbaar, met uitzondering van Windows-containerlogboeken.
- Alleen podomgevingen worden bewaakt, niet Docker-omgevingen.
- Met de preview-release worden maximaal 30 Windows Server-containers ondersteund. Deze beperking geldt niet voor Linux containers. 

Bekijk de volgende video met een diepe duik op tussenniveau om meer te weten te komen over het bewaken van uw AKS-cluster met Azure Monitor voor containers.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hoe krijg ik toegang tot deze functie?

U hebt op twee manieren toegang tot Azure Monitor voor containers, vanuit Azure Monitor of rechtstreeks vanuit het geselecteerde AKS-cluster. Vanuit Azure Monitor hebt u een globaal perspectief van alle geïmplementeerde containers, die worden gecontroleerd en die niet zijn, zodat u zoeken en filteren op uw abonnementen en brongroepen en vervolgens inzoomen op Azure Monitor voor containers uit de geselecteerde container.  Anders hebt u rechtstreeks toegang tot de functie vanuit een geselecteerde AKS-container vanaf de AKS-pagina.  

![Overzicht van methoden om toegang te krijgen tot Azure Monitor voor containers](./media/container-insights-overview/azmon-containers-experience.png)

Als u geïnteresseerd bent in het bewaken en beheren van uw Docker- en Windows-containerhosts die buiten AKS worden uitgevoerd om configuratie, audit en resourcegebruik weer te geven, raadpleegt u de [oplossing Containermonitoring.](../../azure-monitor/insights/containers.md)

## <a name="next-steps"></a>Volgende stappen

Als u wilt beginnen met het bewaken van uw Kubernetes-cluster, controleert u [Hoe u de Azure-monitor voor containers inschakelt](container-insights-onboard.md) om inzicht te krijgen in de vereisten en beschikbare methoden om bewaking in te schakelen. 
