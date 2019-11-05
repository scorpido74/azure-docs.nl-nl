---
title: Migreren van Azure Container Service (ACS) naar Azure Kubernetes service (AKS)
description: Migreer van Azure Container Service (ACS) naar Azure Kubernetes service (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 84e0af89e2b3247bc922ab84286a79a0934323a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472990"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migreren van Azure Container Service (ACS) naar Azure Kubernetes service (AKS)

Dit artikel helpt u bij het plannen en uitvoeren van een geslaagde migratie tussen Azure Container Service (ACS) met Kubernetes en Azure Kubernetes service (AKS). Deze hand leiding bevat informatie over de verschillen tussen ACS en AKS en biedt een overzicht van het migratie proces, om u te helpen bij het nemen van belang rijke beslissingen.

## <a name="differences-between-acs-and-aks"></a>Verschillen tussen ACS en AKS

ACS en AKS verschillen in bepaalde belang rijke gebieden die van invloed zijn op de migratie. Voordat u een migratie kunt door nemen, moet u rekening houden met de volgende verschillen:

* AKS-knoop punten gebruiken [beheerde schijven](../virtual-machines/windows/managed-disks-overview.md).
    * Niet-beheerde schijven moeten worden geconverteerd voordat u ze kunt koppelen aan AKS-knoop punten.
    * Aangepaste `StorageClass`-objecten voor Azure-schijven moeten worden gewijzigd van `unmanaged` in `managed`.
    * Elk `PersistentVolumes` moet `kind: Managed`gebruiken.
* AKS ondersteunt [meerdere knooppunt groepen](https://docs.microsoft.com/azure/aks/use-multiple-node-pools).
* Knoop punten op basis van Windows Server zijn momenteel beschikbaar als [Preview-versie in AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS ondersteunt een beperkt aantal [regio's](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS is een beheerde service met een gehoste Kubernetes-besturings vlak. Mogelijk moet u uw toepassingen aanpassen als u de configuratie van uw ACS-Masters eerder hebt gewijzigd.

## <a name="differences-between-kubernetes-versions"></a>Verschillen tussen Kubernetes-versies

Als u migreert naar een nieuwere versie van Kubernetes, raadpleegt u de volgende bronnen om inzicht te krijgen in de strategieën van de Kubernetes-versie:

* [Kubernetes-versie en versie scheefheid ondersteunings beleid](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

## <a name="migration-considerations"></a>Overwegingen bij migraties

### <a name="agent-pools"></a>Agent groepen

Hoewel AKS het Kubernetes-besturings vlak beheert, definieert u nog steeds de grootte en het aantal knoop punten dat in het nieuwe cluster moet worden meegenomen. Als u wilt dat een toewijzing van 1:1 van ACS naar AKS, moet u de bestaande gegevens van het ACS-knoop punt vastleggen. Gebruik deze gegevens wanneer u een nieuw AKS-cluster maakt.

Voorbeeld:

| Naam | Aantal | VM-grootte | Besturingssysteem |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Omdat er tijdens de migratie extra virtuele machines in uw abonnement worden geïmplementeerd, moet u controleren of uw quota en limieten voldoende zijn voor deze resources. 

Zie [Azure-abonnement en service limieten](https://docs.microsoft.com/azure/azure-subscription-service-limits)voor meer informatie. Als u de huidige quota's wilt controleren, gaat u in het Azure Portal naar de [Blade abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selecteert u uw abonnement en selecteert u vervolgens **gebruik en quota's**.

### <a name="networking"></a>Netwerken

Voor complexe toepassingen migreert u doorgaans over tijd in plaats van allemaal tegelijk. Dit betekent dat de oude en nieuwe omgevingen mogelijk moeten communiceren via het netwerk. Toepassingen die eerder `ClusterIP` Services hebben gebruikt om te communiceren, moeten mogelijk worden weer gegeven als type `LoadBalancer` en correct worden beveiligd.

Als u de migratie wilt volt ooien, moet u clients verwijzen naar de nieuwe services die worden uitgevoerd op AKS. We raden aan dat u verkeer omleidt door DNS te laten verwijzen naar de Load Balancer die zich vóór uw AKS-cluster bevindt.

### <a name="stateless-applications"></a>Stateless toepassingen

Stateless toepassings migratie is de meest eenvoudige situatie. U past uw YAML-definities toe op het nieuwe cluster, zorg ervoor dat alles naar verwachting werkt en verkeer omleiden om uw nieuwe cluster te activeren.

### <a name="stateful-applications"></a>Stateful toepassingen

Plan uw migratie van stateful toepassingen om verlies van gegevens of onverwachte downtime te voor komen.

#### <a name="highly-available-applications"></a>Maxi maal beschik bare toepassingen

U kunt een aantal stateful toepassingen implementeren in een configuratie met hoge Beschik baarheid. Deze toepassingen kunnen gegevens kopiëren over replica's. Als u momenteel gebruikmaakt van deze soort implementatie, kunt u een nieuw lid maken op het nieuwe AKS-cluster en vervolgens migreren met mini maal effect op downstream-aanroepers. Over het algemeen zijn de migratie stappen voor dit scenario:

1. Maak een nieuwe secundaire replica op AKS.
2. Wacht tot de gegevens zijn gerepliceerd.
3. Er wordt een failover uitgevoerd om een secundaire replica de nieuwe primaire te maken.
4. Punt verkeer naar het AKS-cluster.

#### <a name="migrating-persistent-volumes"></a>Permanente volumes migreren

Als u bestaande permanente volumes migreert naar AKS, voert u de volgende stappen uit:

1. Schrijf het schrijven naar de toepassing. (Deze stap is optioneel en vereist downtime.)
2. Maak moment opnamen van de schijven.
3. Nieuwe beheerde schijven maken op basis van de moment opnamen.
4. Maak permanente volumes in AKS.
5. Update pod-specificaties om [bestaande volumes te gebruiken](https://docs.microsoft.com/azure/aks/azure-disk-volume) in plaats van PersistentVolumeClaims (static Provisioning).
6. Implementeer de toepassing op AKS.
7. Subelementid.
8. Punt verkeer naar het AKS-cluster.

> [!IMPORTANT]
> Als u ervoor kiest om schrijf bewerkingen niet stil te leggen, moet u gegevens repliceren naar de nieuwe implementatie. Anders mist u de gegevens die zijn geschreven nadat u de moment opnamen van de schijf hebt gemaakt.

Sommige open source-hulpprogram ma's kunnen u helpen bij het maken van beheerde schijven en het migreren van volumes tussen Kubernetes-clusters:

* Met [Azure cli Disk Copy extension](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopieert en converteert u schijven over resource groepen en Azure-regio's.
* Met de [extensie Azure uitvoeren cli](https://github.com/yaron2/azure-kube-cli) worden ACS-Kubernetes-volumes geïnventariseerd en gemigreerd naar een AKS-cluster.

#### <a name="azure-files"></a>Azure Files

In tegens telling tot schijven kunnen Azure Files op meerdere hosts tegelijk worden gekoppeld. In uw AKS-cluster is Azure en Kubernetes niet voor komen dat u een pod maakt die nog steeds wordt gebruikt door uw ACS-cluster. Om gegevens verlies en onverwacht gedrag te voor komen, moet u ervoor zorgen dat de clusters niet op hetzelfde moment naar dezelfde bestanden schrijven.

Als uw toepassing meerdere replica's kan hosten die naar dezelfde bestands share verwijzen, volgt u de stateless migratie stappen en implementeert u uw YAML-definities naar het nieuwe cluster. Als dat niet het geval is, omvat een mogelijke migratie aanpak de volgende stappen:

1. Implementeer uw toepassing naar AKS met een aantal replica's van 0.
2. Schaal de toepassing op ACS naar 0. (Voor deze stap is downtime vereist.)
3. Schaal de toepassing op AKS tot 1.
4. Subelementid.
5. Punt verkeer naar het AKS-cluster.

Als u met een lege share wilt beginnen en een kopie van de bron gegevens wilt maken, kunt u de [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) -opdrachten gebruiken om uw gegevens te migreren.

### <a name="deployment-strategy"></a>Implementatie strategie

We raden u aan uw bestaande CI/CD-pijp lijn te gebruiken voor het implementeren van een bekende, goede configuratie op AKS. Kloon uw bestaande implementatie taken en zorg ervoor dat `kubeconfig` naar het nieuwe AKS-cluster verwijst.

Als dat niet mogelijk is, kunt u resource definities exporteren uit ACS en vervolgens Toep assen op AKS. U kunt `kubectl` gebruiken om objecten te exporteren.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Er kunnen verschillende open source-hulpprogram ma's worden geholpen, afhankelijk van de behoeften van uw implementatie:

* [Velero](https://github.com/heptio/ark) (voor dit hulp programma is Kubernetes 1,7 vereist.)
* [Azure uitvoeren CLI-extensie](https://github.com/yaron2/azure-kube-cli)
* [Reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Migratiestappen

1. [Maak een AKS-cluster](https://docs.microsoft.com/azure/aks/create-cluster) via de sjabloon Azure Portal, Azure CLI of Azure Resource Manager.

   > [!NOTE]
   > Zoek naar voorbeeld Azure Resource Manager sjablonen voor AKS in de [Azure/AKS-](https://github.com/Azure/AKS/tree/master/examples/vnet) opslag plaats op github.

2. Breng de benodigde wijzigingen aan in uw YAML-definities. Vervang bijvoorbeeld `apps/v1beta1` door `apps/v1` voor `Deployments`.

3. [Migreer volumes](#migrating-persistent-volumes) (optioneel) van uw ACS-cluster naar uw AKS-cluster.

4. Gebruik uw CI/CD-systeem om toepassingen te implementeren in AKS. Of gebruik kubectl om de YAML-definities toe te passen.

5. Subelementid. Zorg ervoor dat uw toepassingen werken zoals verwacht en dat alle gemigreerde gegevens zijn gekopieerd.

6. Verkeer omleiden. Update DNS naar Point-clients naar uw AKS-implementatie.

7. Voltooi taken na de migratie. Als u volumes hebt gemigreerd en geen schrijf bewerkingen wilt door gegeven, kopieert u deze gegevens naar het nieuwe cluster.
