---
title: Migreren naar Azure Kubernetes-service (AKS)
description: Migreren naar Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624811"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migreren naar Azure Kubernetes-service (AKS)

Met dit artikel u een succesvolle migratie naar Azure Kubernetes Service (AKS) plannen en uitvoeren. Om u te helpen belangrijke beslissingen te nemen, bevat deze handleiding details voor de huidige aanbevolen configuratie voor AKS. Dit artikel behandelt niet elk scenario en waar nodig bevat het artikel koppelingen naar meer gedetailleerde informatie voor het plannen van een succesvolle migratie.

Dit document kan worden gebruikt om de volgende scenario's te ondersteunen:

* Een AKS-cluster migreren dat wordt ondersteund door [beschikbaarheidssets](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) naar [virtuele machineschaalsets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Een AKS-cluster migreren om een [Standaard SKU-loadbalancer](https://docs.microsoft.com/azure/aks/load-balancer-standard) te gebruiken
* Migreren van [Azure Container Service (ACS) - met pensioen op 31 januari 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) naar AKS
* Migreren van [AKS-engine](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) naar AKS
* Migreren van niet-Azure-gebaseerde Kubernetes-clusters naar AKS

Controleer bij het migreren dat uw doelKubernetes-versie zich binnen het ondersteunde venster voor AKS bevindt. Als u een oudere versie gebruikt, bevindt deze zich mogelijk niet binnen het ondersteunde bereik en moet upgradeversies worden ondersteund door AKS. Zie [door AKS ondersteunde Kubernetes-versies](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) voor meer informatie.

Als u migreert naar een nieuwere versie van Kubernetes, bekijkt u [kubernetes-versie en versiebeleid voor het scheeftrekken van de versie](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Verschillende open-source tools kunnen helpen bij uw migratie, afhankelijk van uw scenario:

* [Velero](https://velero.io/) (Vereist Kubernetes 1.7+)
* [Azure Kube CLI-extensie](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

In dit artikel zullen we migratiedetails samenvatten voor:

> [!div class="checklist"]
> * AKS met standaard load balancer en virtuele machineschaalsets
> * Bestaande gekoppelde Azure-services
> * Zorgen voor geldige quota
> * Hoge beschikbaarheid en bedrijfscontinuïteit
> * Overwegingen voor staatloze toepassingen
> * Overwegingen voor stateful toepassingen
> * Implementatie van uw clusterconfiguratie

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS met standaard load balancer en virtuele machineschaalsets

AKS is een managed service die unieke mogelijkheden biedt met lagere management overhead. Als gevolg van het feit dat een beheerde service, moet u kiezen uit een set [van regio's](https://docs.microsoft.com/azure/aks/quotas-skus-regions) die AKS ondersteunt. Voor de overgang van uw bestaande cluster naar AKS moet mogelijk uw bestaande toepassingen worden gewijzigd, zodat ze gezond blijven in het door AKS beheerde besturingsvlak.

We raden u aan AKS-clusters te gebruiken die worden ondersteund door [virtuele machineschaalsets](https://docs.microsoft.com/azure/virtual-machine-scale-sets) en de [Azure Standard Load Balancer](https://docs.microsoft.com/azure/aks/load-balancer-standard) om ervoor te zorgen dat u functies krijgt, zoals [meerdere knooppuntgroepen,](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) [beschikbaarheidszones,](https://docs.microsoft.com/azure/availability-zones/az-overview) [geautoriseerde IP-bereiken,](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges) [Cluster Autoscaler,](https://docs.microsoft.com/azure/aks/cluster-autoscaler) [Azure Policy for AKS](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)en andere nieuwe functies wanneer deze worden vrijgegeven.

AKS-clusters ondersteund door [Virtual Machine Availability Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) missen ondersteuning voor veel van deze functies.

In het volgende voorbeeld wordt een AKS-cluster gemaakt met een enkele knooppuntgroep die wordt ondersteund door een virtuele machineschaalset. Het maakt gebruik van een standaard load balancer. Het maakt ook de clusterautoscaler op de knooppuntgroep voor het cluster mogelijk en stelt minimaal *1* en maximaal *3* knooppunten in:

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

## <a name="existing-attached-azure-services"></a>Bestaande gekoppelde Azure-services

Bij het migreren van clusters u externe Azure-services hebben gekoppeld. Deze vereisen geen resourcerecreatie, maar ze vereisen het bijwerken van verbindingen van eerdere naar nieuwe clusters om de functionaliteit te behouden.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Opslagaccount
* Externe databases

## <a name="ensure-valid-quotas"></a>Zorgen voor geldige quota

Omdat extra virtuele machines tijdens de migratie in uw abonnement worden geïmplementeerd, moet u controleren of uw quota en limieten voldoende zijn voor deze resources. Mogelijk moet u een verhoging van het [vCPU-quotum aanvragen.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)

Mogelijk moet u een verhoging van [netwerkquota](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) aanvragen om ervoor te zorgen dat u IP's niet uitput. Zie [netwerk- en IP-bereiken voor AKS](https://docs.microsoft.com/azure/aks/configure-kubenet) voor meer informatie.

Zie [Azure-abonnements- en servicelimieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)voor meer informatie. Als u uw huidige quota wilt controleren, gaat u in de Azure-portal naar het [abonnementsblad,](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)selecteert u uw abonnement en selecteert u **Gebruik + quota**.

## <a name="high-availability-and-business-continuity"></a>Hoge beschikbaarheid en bedrijfscontinuïteit

Als uw toepassing downtime niet aankan, moet u aanbevolen procedures volgen voor migratiescenario's met hoge beschikbaarheid.  Aanbevolen procedures voor complexe bedrijfscontinuïteitsplanning, disaster recovery en het maximaliseren van uptime vallen buiten het bereik van dit document.  Lees meer over [Best practices voor bedrijfscontinuïteit en disaster recovery in Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) voor meer informatie.

Voor complexe toepassingen migreert u meestal in de loop van de tijd in plaats van in één keer. Dat betekent dat de oude en nieuwe omgevingen mogelijk via het netwerk moeten communiceren. Toepassingen die `ClusterIP` voorheen services gebruikten om te `LoadBalancer` communiceren, moeten mogelijk als type worden weergegeven en op de juiste manier worden beveiligd.

Als u de migratie wilt voltooien, wilt u clients aanwijzen op de nieuwe services die op AKS worden uitgevoerd. We raden u aan verkeer om te leiden door DNS bij te werken om de Load Balancer aan te wijzen die voor uw AKS-cluster zit.

[Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) kan klanten doorverwijzen naar het gewenste Kubernetes-cluster- en toepassingsexemplaar.  Traffic Manager is een op DNS gebaseerde traffic load balancer die netwerkverkeer over regio's kan distribueren.  Voor de beste prestaties en redundantie u al het toepassingsverkeer door Traffic Manager leiden voordat het naar uw AKS-cluster gaat.  In een implementatie met meerdere clusteren moeten klanten verbinding maken met een DNS-naam van Traffic Manager die verwijst naar de services op elk AKS-cluster. Definieer deze services met behulp van eindpunten voor traffic manager. Elk eindpunt is het *IP-adres van de serviceloadbalancer*. Gebruik deze configuratie om netwerkverkeer te leiden van het eindpunt Traffic Manager in een regio naar het eindpunt in een andere regio.

![AKS met Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) is een andere optie voor het routeren van verkeer voor AKS-clusters.  Met de Azure Front Door Service kunt u de internationale routering van uw webverkeer definiëren, beheren en bewaken door te optimaliseren voor de beste prestaties en directe wereldwijde failover voor hoge beschikbaarheid. 

### <a name="considerations-for-stateless-applications"></a>Overwegingen voor staatloze toepassingen

Statusloze toepassingsmigratie is het meest eenvoudige geval. Pas uw resourcedefinities (YAML of Helm) toe op het nieuwe cluster, zorg ervoor dat alles werkt zoals verwacht en leid verkeer om om uw nieuwe cluster te activeren.

### <a name="considerations-for-stateful-applications"></a>Overwegingen voor stateful toepassingen

Plan uw migratie van stateful applicaties zorgvuldig om gegevensverlies of onverwachte downtime te voorkomen.

Als u Azure Files gebruikt, u de bestandsshare als volume in het nieuwe cluster monteren:
* [Statische Azure-bestanden als volume monteren](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Als u Azure Managed Disks gebruikt, u de schijf alleen monteren als deze niet is gekoppeld aan een vm:
* [Statische Azure-schijf als volume monteren](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Als geen van deze benaderingen werkt, u een back-up- en herstelopties gebruiken:
* [Velero op Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure Files

In tegenstelling tot schijven kunnen Azure-bestanden tegelijkertijd op meerdere hosts worden gemonteerd. In uw AKS-cluster verhinderen Azure en Kubernetes niet dat u een pod maakt die uw ACS-cluster nog steeds gebruikt. Als u gegevensverlies en onverwacht gedrag wilt voorkomen, moet u ervoor zorgen dat de clusters niet tegelijkertijd naar dezelfde bestanden schrijven.

Als uw toepassing meerdere replica's kan hosten die naar dezelfde bestandsshare wijzen, volgt u de statusloze migratiestappen en implementeert u uw YAML-definities in uw nieuwe cluster. Zo niet, dan omvat een mogelijke migratieaanpak de volgende stappen:

* Het valideren van uw toepassing werkt correct.
* Richt uw live verkeer naar uw nieuwe AKS-cluster.
* Koppel het oude cluster los.

Als u wilt beginnen met een lege share en een kopie [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) van de brongegevens wilt maken, u de opdrachten gebruiken om uw gegevens te migreren.


#### <a name="migrating-persistent-volumes"></a>Permanente volumes migreren

Als u bestaande permanente volumes migreert naar AKS, volgt u over het algemeen de volgende stappen:

* Quiesce schrijft naar de aanvraag. (Deze stap is optioneel en vereist downtime.)
* Maak foto's van de schijven.
* Maak nieuwe beheerde schijven van de momentopnamen.
* Maak permanente volumes in AKS.
* Podspecificaties bijwerken om [bestaande volumes](https://docs.microsoft.com/azure/aks/azure-disk-volume) te gebruiken in plaats van PersistentVolumeClaims (statische inrichting).
* Implementeer uw toepassing op AKS.
* Het valideren van uw toepassing werkt correct.
* Richt uw live verkeer naar uw nieuwe AKS-cluster.

> [!IMPORTANT]
> Als u ervoor kiest om schrijft niet te schrijven, moet u gegevens repliceren naar de nieuwe implementatie. Anders mis je de gegevens die zijn geschreven nadat je de schijfsnapshots hebt gemaakt.

Sommige open-sourcetools kunnen u helpen beheerde schijven te maken en volumes te migreren tussen Kubernetes-clusters:

* [Azure CLI Disk Copy-extensie](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopieert en converteert schijven in resourcegroepen en Azure-regio's.
* [Azure Kube CLI-extensie](https://github.com/yaron2/azure-kube-cli) somt ACS Kubernetes-volumes op en migreert deze naar een AKS-cluster.


### <a name="deployment-of-your-cluster-configuration"></a>Implementatie van uw clusterconfiguratie

We raden u aan uw bestaande Continuous Integration (CI) en Continuous Deliver (CD) pijplijn te gebruiken om een bekende goede configuratie te implementeren op AKS. U Azure Pipelines gebruiken om [uw toepassingen te bouwen en te implementeren op AKS.](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops) Kloon uw bestaande implementatietaken `kubeconfig` en zorg ervoor dat deze naar het nieuwe AKS-cluster wijst.

Als dat niet mogelijk is, exporteert u brondefinities uit uw bestaande Kubernetes-cluster en past u deze vervolgens toe op AKS. U kunt `kubectl` objecten exporteren.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Bestaande resources verplaatsen naar een andere regio

U uw AKS-cluster verplaatsen naar een [andere regio die wordt ondersteund door AKS.][region-availability] We raden u aan een nieuw cluster in het andere gebied te maken en vervolgens uw resources en toepassingen in uw nieuwe cluster te implementeren. Als u services zoals Azure [Dev Spaces][azure-dev-spaces] op uw AKS-cluster hebt, moet u deze services ook installeren en configureren op uw cluster in de nieuwe regio.


In dit artikel hebben we migratiedetails samengevat voor:

> [!div class="checklist"]
> * AKS met standaard load balancer en virtuele machineschaalsets
> * Bestaande gekoppelde Azure-services
> * Zorgen voor geldige quota
> * Hoge beschikbaarheid en bedrijfscontinuïteit
> * Overwegingen voor staatloze toepassingen
> * Overwegingen voor stateful toepassingen
> * Implementatie van uw clusterconfiguratie


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/