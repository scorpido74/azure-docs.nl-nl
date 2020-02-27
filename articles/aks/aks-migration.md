---
title: Migreren naar Azure Kubernetes service (AKS)
description: Migreer naar Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624811"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migreren naar Azure Kubernetes service (AKS)

Dit artikel helpt u bij het plannen en uitvoeren van een geslaagde migratie naar Azure Kubernetes service (AKS). Deze hand leiding bevat informatie over de huidige aanbevolen configuratie voor AKS om u te helpen bij het nemen van belang rijke beslissingen. In dit artikel komen niet elk scenario aan bod, en, indien van toepassing, bevat het artikel koppelingen naar meer gedetailleerde informatie over het plannen van een geslaagde migratie.

Dit document kan worden gebruikt om de volgende scenario's te ondersteunen:

* Een AKS-cluster dat wordt ondersteund door [beschikbaarheids sets](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) , migreren naar [Virtual Machine Scale sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Een AKS-cluster migreren voor gebruik van een [standaard-SKU Load Balancer](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* Migreren van [Azure container service (ACS): 31 januari 2020 wordt buiten gebruik gesteld](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) op AKS
* Migreren van de [AKS-engine](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) naar AKS
* Migreren van niet-Azure gebaseerde Kubernetes-clusters naar AKS

Zorg ervoor dat uw doel-Kubernetes-versie binnen het ondersteunde venster voor AKS ligt tijdens de migratie. Als u een oudere versie gebruikt, kan deze zich niet binnen het ondersteunde bereik bevinden en moeten upgrades van versies worden ondersteund door AKS. Zie [AKS ondersteunde Kubernetes-versies](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) voor meer informatie.

Als u migreert naar een nieuwere versie van Kubernetes, raadpleegt u het [ondersteunings beleid voor Kubernetes versie en versie scheefheid](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Er zijn verschillende open source-hulpprogram ma's die u kunnen helpen bij de migratie, afhankelijk van uw scenario:

* [Velero](https://velero.io/) (vereist Kubernetes 1.7 +)
* [Azure uitvoeren CLI-extensie](https://github.com/yaron2/azure-kube-cli)
* [Reshifter](https://github.com/mhausenblas/reshifter)

In dit artikel wordt een overzicht gegeven van de migratie gegevens voor:

> [!div class="checklist"]
> * AKS met Standard Load Balancer en Virtual Machine Scale Sets
> * Bestaande gekoppelde Azure-Services
> * Zorg voor geldige quota's
> * Hoge Beschik baarheid en bedrijfs continuïteit
> * Overwegingen voor stateless toepassingen
> * Overwegingen voor stateful toepassingen
> * Implementatie van uw cluster configuratie

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS met Standard Load Balancer en Virtual Machine Scale Sets

AKS is een beheerde service die unieke mogelijkheden biedt met lagere beheer overhead. Als gevolg van een beheerde service, moet u selecteren uit een set [regio's](https://docs.microsoft.com/azure/aks/quotas-skus-regions) die door aks worden ondersteund. Voor de overgang van uw bestaande cluster naar AKS moet u mogelijk uw bestaande toepassingen aanpassen zodat ze in orde blijven op het door AKS beheerde besturings vlak.

We raden u aan om AKS-clusters die worden ondersteund door [Virtual Machine Scale sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets) en de [Azure-Standard Load Balancer](https://docs.microsoft.com/azure/aks/load-balancer-standard) te gebruiken om ervoor te zorgen dat u beschikt over functies zoals [meerdere knooppunt groepen](https://docs.microsoft.com/azure/aks/use-multiple-node-pools), [Beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-overview), [geautoriseerde IP-bereiken](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), [cluster automatisch schalen](https://docs.microsoft.com/azure/aks/cluster-autoscaler), [Azure Policy voor AKS](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)en andere nieuwe functies wanneer deze worden uitgebracht.

AKS-clusters die worden ondersteund door [beschikbaarheids sets voor virtuele machines](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) , bieden geen ondersteuning voor veel van deze functies.

In het volgende voor beeld wordt een AKS-cluster gemaakt met één knooppunt groep die wordt ondersteund door een virtuele-machine schaalset. Er wordt gebruikgemaakt van een standaard load balancer. Ook wordt de cluster-automatische schaal functie voor de knooppunt groep voor het cluster ingeschakeld en worden mini maal *1* en Maxi maal *drie* knoop punten ingesteld:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Bestaande gekoppelde Azure-Services

Bij het migreren van clusters hebt u mogelijk externe Azure-Services gekoppeld. Hiervoor is het niet nodig om resources te recreatie, maar ze moeten verbindingen van voor gaande naar nieuwe clusters bijwerken om de functionaliteit te behouden.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Opslagaccount
* Externe data bases

## <a name="ensure-valid-quotas"></a>Zorg voor geldige quota's

Omdat er tijdens de migratie extra virtuele machines in uw abonnement worden geïmplementeerd, moet u controleren of uw quota en limieten voldoende zijn voor deze resources. Mogelijk moet u een toename van het [vCPU-quotum](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)aanvragen.

Mogelijk moet u een verhoging van de [netwerk quota](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) aanvragen om ervoor te zorgen dat de IP-adressen niet worden uitgeput. Zie [netwerk-en IP-bereiken voor AKS](https://docs.microsoft.com/azure/aks/configure-kubenet) voor meer informatie.

Zie [Azure-abonnement en service limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)voor meer informatie. Als u de huidige quota's wilt controleren, gaat u in het Azure Portal naar de [Blade abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selecteert u uw abonnement en selecteert u vervolgens **gebruik en quota's**.

## <a name="high-availability-and-business-continuity"></a>Hoge Beschik baarheid en bedrijfs continuïteit

Als uw toepassing downtime niet kan verwerken, moet u aanbevolen procedures volgen voor migratie scenario's met hoge Beschik baarheid.  Best practices voor het plannen van complexe bedrijfs continuïteit, herstel na nood gevallen en maximale uptime van het bedrijf vallen buiten het bereik van dit document.  Lees meer over de [Aanbevolen procedures voor bedrijfs continuïteit en herstel na nood gevallen in azure Kubernetes service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) voor meer informatie.

Voor complexe toepassingen migreert u doorgaans over tijd in plaats van allemaal tegelijk. Dit betekent dat de oude en nieuwe omgevingen mogelijk moeten communiceren via het netwerk. Toepassingen die eerder `ClusterIP` Services hebben gebruikt om te communiceren, moeten mogelijk worden weer gegeven als type `LoadBalancer` en correct worden beveiligd.

Als u de migratie wilt volt ooien, moet u clients verwijzen naar de nieuwe services die worden uitgevoerd op AKS. We raden aan dat u verkeer omleidt door DNS te laten verwijzen naar de Load Balancer die zich vóór uw AKS-cluster bevindt.

Met [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) kunnen klanten worden doorgestuurd naar het gewenste Kubernetes-cluster en toepassings exemplaar.  Traffic Manager is een op DNS gebaseerd verkeer load balancer dat netwerk verkeer kan distribueren tussen regio's.  Voor de beste prestaties en redundantie moet u alle toepassings verkeer via Traffic Manager door sturen voordat het naar uw AKS-cluster gaat.  In een implementatie met verschillende clusters moeten klanten verbinding maken met een Traffic Manager DNS-naam die verwijst naar de services op elk AKS-cluster. Definieer deze services met behulp van Traffic Manager-eind punten. Elk eind punt is de *service load BALANCER IP*. Gebruik deze configuratie om netwerk verkeer van het Traffic Manager-eind punt in de ene regio naar het eind punt in een andere regio te sturen.

![AKS met Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

De [Azure front-deur service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) is een andere optie voor het routeren van verkeer voor AKS-clusters.  Met de Azure Front Door Service kunt u de internationale routering van uw webverkeer definiëren, beheren en bewaken door te optimaliseren voor de beste prestaties en directe wereldwijde failover voor hoge beschikbaarheid. 

### <a name="considerations-for-stateless-applications"></a>Overwegingen voor stateless toepassingen

Stateless toepassings migratie is de meest eenvoudige situatie. Pas uw resource definities (YAML of helm) toe op het nieuwe cluster, Controleer of alles werkt zoals verwacht, en leid verkeer om om uw nieuwe cluster te activeren.

### <a name="considerations-for-stateful-applications"></a>Overwegingen voor stateful toepassingen

Plan uw migratie van stateful toepassingen om verlies van gegevens of onverwachte downtime te voor komen.

Als u Azure Files gebruikt, kunt u de bestands share als een volume koppelen aan het nieuwe cluster:
* [Statische Azure Files koppelen als een volume](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Als u Azure Managed Disks gebruikt, kunt u de schijf alleen koppelen als deze is gekoppeld aan een virtuele machine:
* [Statische Azure-schijf koppelen als een volume](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Als geen van beide benaderingen werken, kunt u een back-up-en herstel opties gebruiken:
* [Velero op Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure Files

In tegens telling tot schijven kunnen Azure Files op meerdere hosts tegelijk worden gekoppeld. In uw AKS-cluster is Azure en Kubernetes niet voor komen dat u een pod maakt die nog steeds wordt gebruikt door uw ACS-cluster. Om gegevens verlies en onverwacht gedrag te voor komen, moet u ervoor zorgen dat de clusters niet op hetzelfde moment naar dezelfde bestanden schrijven.

Als uw toepassing meerdere replica's kan hosten die naar dezelfde bestands share verwijzen, volgt u de stateless migratie stappen en implementeert u uw YAML-definities naar het nieuwe cluster. Als dat niet het geval is, omvat een mogelijke migratie aanpak de volgende stappen:

* Controleer of de toepassing correct werkt.
* Wijs uw live verkeer naar uw nieuwe AKS-cluster.
* Verbreek de verbinding met het oude cluster.

Als u met een lege share wilt beginnen en een kopie van de bron gegevens wilt maken, kunt u de [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) -opdrachten gebruiken om uw gegevens te migreren.


#### <a name="migrating-persistent-volumes"></a>Permanente volumes migreren

Als u bestaande permanente volumes migreert naar AKS, voert u de volgende stappen uit:

* Schrijf het schrijven naar de toepassing. (Deze stap is optioneel en vereist downtime.)
* Maak moment opnamen van de schijven.
* Nieuwe beheerde schijven maken op basis van de moment opnamen.
* Maak permanente volumes in AKS.
* Update pod-specificaties om [bestaande volumes te gebruiken](https://docs.microsoft.com/azure/aks/azure-disk-volume) in plaats van PersistentVolumeClaims (static Provisioning).
* Implementeer uw toepassing in AKS.
* Controleer of de toepassing correct werkt.
* Wijs uw live verkeer naar uw nieuwe AKS-cluster.

> [!IMPORTANT]
> Als u ervoor kiest om schrijf bewerkingen niet stil te leggen, moet u gegevens repliceren naar de nieuwe implementatie. Anders mist u de gegevens die zijn geschreven nadat u de moment opnamen van de schijf hebt gemaakt.

Sommige open source-hulpprogram ma's kunnen u helpen bij het maken van beheerde schijven en het migreren van volumes tussen Kubernetes-clusters:

* Met [Azure cli Disk Copy extension](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopieert en converteert u schijven over resource groepen en Azure-regio's.
* Met de [extensie Azure uitvoeren cli](https://github.com/yaron2/azure-kube-cli) worden ACS-Kubernetes-volumes geïnventariseerd en gemigreerd naar een AKS-cluster.


### <a name="deployment-of-your-cluster-configuration"></a>Implementatie van uw cluster configuratie

We raden u aan om uw bestaande, doorlopende integratie (CI) en continue Delivery (CD)-pijp lijn te gebruiken voor het implementeren van een bekende, goede configuratie op AKS. U kunt Azure-pijp lijnen gebruiken om [uw toepassingen te bouwen en te implementeren in AKS](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops). Kloon uw bestaande implementatie taken en zorg ervoor dat `kubeconfig` naar het nieuwe AKS-cluster verwijst.

Als dat niet mogelijk is, kunt u resource definities uit uw bestaande Kubernetes-cluster exporteren en vervolgens Toep assen op AKS. U kunt `kubectl` gebruiken om objecten te exporteren.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Bestaande resources verplaatsen naar een andere regio

Misschien wilt u uw AKS-cluster verplaatsen naar een [andere regio die wordt ondersteund door aks][region-availability]. U wordt aangeraden een nieuw cluster in de andere regio te maken en vervolgens uw resources en toepassingen te implementeren op uw nieuwe cluster. Als u bovendien Services hebt zoals [Azure-ontwikkel ruimten][azure-dev-spaces] die worden uitgevoerd op uw AKS-cluster, moet u deze services ook installeren en configureren in uw cluster in de nieuwe regio.


In dit artikel wordt een overzicht gegeven van de migratie Details voor:

> [!div class="checklist"]
> * AKS met Standard Load Balancer en Virtual Machine Scale Sets
> * Bestaande gekoppelde Azure-Services
> * Zorg voor geldige quota's
> * Hoge Beschik baarheid en bedrijfs continuïteit
> * Overwegingen voor stateless toepassingen
> * Overwegingen voor stateful toepassingen
> * Implementatie van uw cluster configuratie


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/