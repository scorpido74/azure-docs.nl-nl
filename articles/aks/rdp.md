---
title: RDP in Azure Kubernetes Service (AKS)-cluster Windows Server-knooppunten
description: Meer informatie over het maken van een RDP-verbinding met AKS-knooppunten (Azure Kubernetes Service) voor probleemoplossing en onderhoudstaken.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 897504aa9902d0feaf4245c719d3a4a3c6fd2241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594478"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Verbinding maken met RDP met AKS-clusterwindows serverknooppunten (Azure Kubernetes Service) voor onderhoud of probleemoplossing

Gedurende de gehele levenscyclus van uw AKS-cluster (Azure Kubernetes Service) moet u mogelijk toegang krijgen tot een AKS Windows Server-knooppunt. Deze toegang kan zijn voor onderhoud, logboekverzameling of andere probleemoplossingsbewerkingen. U hebt toegang tot de AKS Windows Server-knooppunten via RDP. Als u SSH wilt gebruiken om toegang te krijgen tot de AKS Windows Server-knooppunten en u hebt toegang tot dezelfde keypair die tijdens het maken van het cluster is gebruikt, u de stappen in SSH volgen [naar AKS-clusterknooppunten (Azure Kubernetes Service).][ssh-steps] Om veiligheidsredenen worden de AKS-knooppunten niet blootgesteld aan het internet.

Ondersteuning voor windows Server-knooppunt is momenteel in preview in AKS.

In dit artikel ziet u hoe u een RDP-verbinding met een AKS-knooppunt maakt met behulp van hun privé-IP-adressen.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt met een Windows Server-knooppunt. Als u een AKS-cluster nodig hebt, raadpleegt u het artikel over [het maken van een AKS-cluster met een Windows-container met de Azure CLI.][aks-windows-cli] U hebt de gebruikersnaam en het wachtwoord van de Windows-beheerder nodig voor het Windows Server-knooppunt dat u wilt oplossen. U hebt ook een RDP-client nodig, zoals [Microsoft Remote Desktop.][rdp-mac]

U hebt ook de Azure CLI-versie 2.0.61 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Een virtuele machine implementeren op hetzelfde subnet als uw cluster

De Windows Server-knooppunten van uw AKS-cluster hebben geen extern toegankelijke IP-adressen. Als u een RDP-verbinding wilt maken, u een virtuele machine met een openbaar toegankelijk IP-adres implementeren op hetzelfde subnet als uw Windows Server-knooppunten.

In het volgende voorbeeld wordt een virtuele machine met de naam *myVM* gemaakt in de resourcegroep *myResourceGroup.*

Zorg eerst voor het subnet dat wordt gebruikt door de windows server-knooppuntgroep. Om de subnet-id te krijgen, heb je de naam van het subnet nodig. Om de naam van het subnet te krijgen, heb je de naam van het vnet nodig. Download de vnet-naam door uw cluster op te vragen voor de lijst met netwerken. Als u het cluster wilt opvragen, hebt u de naam nodig. U al deze krijgen door het volgende uit te voeren in de Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Nu u de SUBNET_ID hebt, voert u de volgende opdracht uit in hetzelfde Azure Cloud Shell-venster om de VM te maken:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

In de volgende voorbeelduitvoerwordt weergegeven dat de VM is gemaakt en wordt het openbare IP-adres van de virtuele machine weergegeven.

```console
13.62.204.18
```

Noteer het openbare IP-adres van de virtuele machine. U gebruikt dit adres in een latere stap.

## <a name="allow-access-to-the-virtual-machine"></a>Toegang tot de virtuele machine toestaan

AKS-knooppuntgroepsubnetten zijn standaard beveiligd met NSG's (Network Security Groups). Om toegang te krijgen tot de virtuele machine, moet u toegang inschakelen in de NSG.

> [!NOTE]
> De NSG's worden gecontroleerd door de AKS-service. Elke wijziging die u aanbrengt in de NSG wordt op elk moment overschreven door het controlevliegtuig.
>

Laat eerst de resourcegroep en de NSG-naam van de nsg de regel toevoegen aan:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Maak vervolgens de NSG-regel:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Het knooppuntadres opvragen

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdrachtregelclient. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Vermeld het interne IP-adres van de Windows Server-knooppunten met de opdracht [kubectl-get:][kubectl-get]

```console
kubectl get nodes -o wide
```

De vervolgvoorbeelduitvoer toont de interne IP-adressen van alle knooppunten in het cluster, inclusief de Windows Server-knooppunten.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Neem het interne IP-adres op van het Windows Server-knooppunt dat u wilt oplossen. U gebruikt dit adres in een latere stap.

## <a name="connect-to-the-virtual-machine-and-node"></a>Verbinding maken met de virtuele machine en het knooppunt

Maak verbinding met het openbare IP-adres van de virtuele machine die u eerder hebt gemaakt met behulp van een RDP-client zoals [Microsoft Remote Desktop.][rdp-mac]

![Afbeelding van verbinding maken met de virtuele machine met behulp van een RDP-client](media/rdp/vm-rdp.png)

Nadat u verbinding hebt gemaakt met uw virtuele machine, maakt u verbinding met het *interne IP-adres* van het Windows Server-knooppunt dat u wilt oplossen met een RDP-client vanuit uw virtuele machine.

![Afbeelding van verbinding maken met het Windows Server-knooppunt met behulp van een RDP-client](media/rdp/node-rdp.png)

U bent nu verbonden met uw Windows Server-knooppunt.

![Afbeelding van het cmd-venster in het Windows Server-knooppunt](media/rdp/node-session.png)

U nu alle opdrachten voor het oplossen van problemen uitvoeren in het *cmd-venster.* Aangezien Windows Server-knooppunten Windows Server Core gebruiken, is er geen volledige GUI- of andere GUI-hulpprogramma's wanneer u verbinding maakt met een Windows Server-knooppunt via RDP.

## <a name="remove-rdp-access"></a>RDP-toegang verwijderen

Als u klaar bent, sluit u de RDP-verbinding af met het Windows Server-knooppunt en sluit u de RDP-sessie af naar de virtuele machine. Nadat u beide RDP-sessies hebt afgesloten, verwijdert u de virtuele machine met de opdracht [az vm delete:][az-vm-delete]

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

En de NSG regel:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Volgende stappen

Als u aanvullende gegevens over probleemoplossing nodig hebt, u [de logboeken van het Hoofdknooppunt van Kubernetes][view-master-logs] of Azure Monitor [bekijken.][azure-monitor-containers]

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
