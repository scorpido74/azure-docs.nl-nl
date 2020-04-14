---
title: Systeemknooppuntgroepen gebruiken in Azure Kubernetes Service (AKS)
description: Meer informatie over het maken en beheren van systeemknooppuntgroepen in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/06/2020
ms.openlocfilehash: b567d9e618877463e1e659f368d35fbb787a4ef2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259065"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Systeemknooppuntgroepen beheren in Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS) worden knooppunten van dezelfde configuratie gegroepeerd in *knooppuntgroepen.* Knooppuntgroepen bevatten de onderliggende VM's waarop uw toepassingen worden uitgevoerd. Systeemknooppuntgroepen en gebruikersknooppuntgroepen zijn twee verschillende knooppuntgroepen voor uw AKS-clusters. Systeemknooppuntpools dienen het primaire doel van het hosten van kritieke systeempods zoals CoreDNS en tunnelfront. Groepen gebruikersknooppunts dienen het primaire doel van het hosten van uw toepassingspods. Toepassingspods kunnen echter worden gepland op groepen met systeemknooppunts als u slechts één groep in uw AKS-cluster wilt hebben. Elk AKS-cluster moet ten minste één systeemknooppuntpool bevatten met ten minste één knooppunt. 

> [!Important]
> Als u één systeemknooppuntgroep voor uw AKS-cluster uitvoert in een productieomgeving, raden we u aan ten minste drie knooppunten te gebruiken voor de knooppuntgroep.

## <a name="before-you-begin"></a>Voordat u begint

* U moet de Azure CLI-versie 2.3.1 of hoger installeren en configureren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][install-azure-cli].

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die groepen voor systeemknooppunts ondersteunen.

* Zie [Quota, beperkingen voor de grootte van virtuele machines en regiobeschikbaarheid in Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Het AKS-cluster moet worden gebouwd met virtuele machineschaalsets als vm-type.
* De naam van een knooppuntgroep mag alleen alfanumerieke tekens voor kleine letters bevatten en moet beginnen met een kleine letter. Voor Linux-knooppuntpools moet de lengte tussen 1 en 12 tekens liggen. Voor windows-knooppuntgroepen moet de lengte tussen 1 en 6 tekens liggen.

## <a name="system-and-user-node-pools"></a>Groepen systeem- en gebruikersknooppunt

Systeemknooppuntknooppunten hebben elk het label **kubernetes.azure.com/mode: systeem**. Elk AKS-cluster bevat ten minste één systeemknooppuntgroep. Groepen systeemknooppunts hebben de volgende beperkingen:

* Systeempools osType moet Linux zijn.
* User node pools osType kan Linux of Windows zijn.
* Systeemgroepen moeten ten minste één knooppunt bevatten en gebruikersknooppuntgroepen kunnen nul of meer knooppunten bevatten.
* Systeemknooppuntpools vereisen een VM SKU van ten minste 2 vCPU's en 4 GB geheugen.
* De groepen van het systeemknooppunt moeten ten minste 30 pods ondersteunen, zoals beschreven in de [formule met minimale en maximale waarde voor pods][maximum-pods].
* Voor pools van het knooppunt voor spotgroepen moeten gebruikersknooppuntgroepen worden vereist.

U de volgende bewerkingen uitvoeren met knooppuntgroepen:

* Wijzig een systeemknooppuntgroep om een gebruikersknooppuntgroep te zijn, op voorwaarde dat u een andere systeemknooppuntpool hebt om zijn plaats in het AKS-cluster in te nemen.
* Een gebruikersknooppuntgroep wijzigen om een systeemknooppunttepool te zijn.
* Gebruikersknooppuntgroepen verwijderen.
* U systeemknooppuntgroepen verwijderen, mits u een andere systeemknooppuntgroep hebt om zijn plaats in het AKS-cluster in te nemen.
* Een AKS-cluster kan meerdere systeemknooppuntgroepen hebben en vereist ten minste één systeemknooppuntpool.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Een nieuw AKS-cluster maken met een systeemknooppuntgroep

Wanneer u een nieuw AKS-cluster maakt, maakt u automatisch een systeemknooppuntmet één knooppunt. De eerste knooppuntgroep wordt standaard ingesteld op een typesysteem. Wanneer u nieuwe knooppuntpools maakt met az aks-knooppunttoevoeging, zijn deze knooppuntgroepen groepen met gebruikersknooppunt, tenzij u de modusparameter expliciet opgeeft.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* in het *eastus-gebied* geopperd.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Gebruik de opdracht [az aks create][az-aks-create] om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* met één systeemgroep met één knooppunt. Voor uw productieworkloads moet u ervoor zorgen dat u systeemknooppuntenpools gebruikt met ten minste drie knooppunten. Deze bewerking kan enkele minuten in beslag nemen.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Een systeemknooppuntgroep toevoegen aan een bestaand AKS-cluster

U een of meer systeemknooppuntgroepen toevoegen aan bestaande AKS-clusters. Met de volgende opdracht wordt een knooppuntgroep van het typesysteem met een standaardaantal van drie knooppunten toegevoegd.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Details weergeven voor uw knooppuntpool

U de details van uw knooppuntpool controleren met de volgende opdracht.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Een modus van het type **Systeem** is gedefinieerd voor systeemknooppuntgroepen en een modus van het type **Gebruiker** wordt gedefinieerd voor gebruikersknooppuntgroepen.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>Systeem- en gebruikersknooppuntgroepen bijwerken

U modi wijzigen voor zowel systeem- als gebruikersknooppuntgroepen. U een systeemknooppuntgroep alleen wijzigen in een gebruikersgroep als er al een andere systeemknooppuntgroep op het AKS-cluster bestaat.

Met deze opdracht wordt een systeemknooppuntgroep gewijzigd in een gebruikersknooppuntgroep.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Met deze opdracht wordt een gebruikersknooppuntgroep gewijzigd in een systeemknooppuntgroep.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Een systeemknooppuntgroep verwijderen

> [!Note]
> Als u systeemknooppuntgroepen wilt gebruiken op AKS-clusters vóór API-versie 2020-03-02, voegt u een nieuwe groep systeemknooppunttoe en verwijdert u vervolgens de oorspronkelijke standaardknooppuntgroep.

Voorheen kon u de systeemknooppuntgroep, de eerste standaardknooppuntgroep in een AKS-cluster, niet verwijderen. U hebt nu de flexibiliteit om een knooppuntgroep uit uw clusters te verwijderen. Aangezien aks-clusters ten minste één systeemknooppuntgroep vereisen, moet u ten minste twee systeemknooppuntgroepen op uw AKS-cluster hebben voordat u een van deze groepen verwijderen.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u systeemknooppuntgroepen in een AKS-cluster maken en beheren. Zie meerdere knooppuntpools gebruiken voor meer informatie over het gebruik van meerdere [knooppuntpools.][use-multiple-node-pools]

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: faq.md#why-cant-i-set-maxpods-below-30