---
title: SSH in AKS-clusterknooppunten (Azure Kubernetes Service)
description: Meer informatie over het maken van een SSH-verbinding met AKS-clusterknooppunten (Azure Kubernetes Service) voor probleemoplossing en onderhoudstaken.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: dfdcda40a24142f85bbeb360aacf0971d72d181f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593628"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Via SSH verbinding maken met AKS-clusterknooppunten (Azure Kubernetes Service) voor onderhoud of probleemoplossing

Gedurende de hele levenscyclus van uw AKS-cluster (Azure Kubernetes Service) moet u mogelijk toegang krijgen tot een AKS-knooppunt. Deze toegang kan zijn voor onderhoud, logboekverzameling of andere probleemoplossingsbewerkingen. U hebt toegang tot AKS-knooppunten via SSH, inclusief Windows Server-knooppunten (momenteel in preview in AKS). U ook [verbinding maken met Windows Server-knooppunten via RDP-verbindingen (Remote Desktop Protocol).][aks-windows-rdp] Om veiligheidsredenen worden de AKS-knooppunten niet blootgesteld aan het internet. Als u SSH naar de AKS-knooppunten wilt maken, gebruikt u het privé-IP-adres.

In dit artikel ziet u hoe u een SSH-verbinding met een AKS-knooppunt maakt met behulp van hun privé-IP-adressen.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

SSH-sleutels worden standaard verkregen of gegenereerd en vervolgens toegevoegd aan knooppunten wanneer u een AKS-cluster maakt. In dit artikel ziet u hoe u andere SSH-sleutels opgeeft dan de SSH-sleutels die zijn gebruikt toen u uw AKS-cluster hebt gemaakt. Het artikel laat u ook zien hoe u het privé-IP-adres van uw knooppunt bepalen en er verbinding mee maken met Behulp van SSH. Als u geen andere SSH-sleutel hoeft op te geven, u de stap overslaan voor het toevoegen van de openbare SSH-sleutel aan het knooppunt.

Dit artikel gaat er ook van uit dat je een SSH-sleutel hebt. U een SSH-toets maken met [macOS of Linux][ssh-nix] of [Windows.][ssh-windows] Als u PuTTY Gen gebruikt om het sleutelpaar te maken, slaat u het sleutelpaar op in een OpenSSH-indeling in plaats van de standaard-putty-privésleutelindeling (.ppk-bestand).

U hebt ook de Azure CLI-versie 2.0.64 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Setgebaseerde AKS-clusters voor virtuele machinesschalen configureren voor SSH-toegang

Als u uw virtuele machineschaalset wilt configureren op basis van SSH-toegang, zoekt u de naam van de virtuele machineschaalset van uw cluster en voegt u uw SSH-openbare sleutel toe aan die schaalset.

Gebruik de opdracht [az aks show][az-aks-show] om de naam van de brongroep van uw AKS-cluster te krijgen, en vervolgens de opdracht [az vmss-lijst][az-vmss-list] om de naam van uw schaalset te krijgen.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

In het bovenstaande voorbeeld wordt de naam van de clusterbrongroep voor de *myAKSCluster* in *myResourceGroup* aan *CLUSTER_RESOURCE_GROUP*. In het voorbeeld wordt *vervolgens CLUSTER_RESOURCE_GROUP* gebruikt om de naam van de schaalset weer te geven en deze toe te wijzen aan *SCALE_SET_NAME*.

> [!IMPORTANT]
> Op dit moment moet u alleen uw SSH-sleutels bijwerken voor uw AKS-clusters op basis van de virtuele machineschaal met behulp van de Azure CLI.
> 
> Voor Linux-knooppunten kunnen SSH-sleutels momenteel alleen worden toegevoegd met de Azure CLI. Als u verbinding wilt maken met een Windows Server-knooppunt met SSH, gebruikt u de SSH-toetsen die zijn verstrekt toen u het AKS-cluster hebt gemaakt en slaat u de volgende set opdrachten voor het toevoegen van uw SSH-openbare sleutel over. U hebt nog steeds het IP-adres nodig van het knooppunt dat u wilt oplossen, wat wordt weergegeven in de laatste opdracht van deze sectie. U ook [verbinding maken met Windows Server-knooppunten via RDP-verbindingen (Remote Desktop Protocol)][aks-windows-rdp] in plaats van SSH.

Als u uw SSH-sleutels in een virtuele machineschaalset aan de knooppunten wilt toevoegen, gebruikt u de [az vmss-extensieset][az-vmss-extension-set] en de opdrachten [az vmss update-instances.][az-vmss-update-instances]

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

In het bovenstaande voorbeeld worden de *CLUSTER_RESOURCE_GROUP-* en *SCALE_SET_NAME* variabelen van de vorige opdrachten gebruikt. Het bovenstaande voorbeeld gebruikt ook *~/.ssh/id_rsa.pub* als locatie voor uw SSH-openbare sleutel.

> [!NOTE]
> Standaard is de gebruikersnaam voor de AKS-knooppunten *azureuser.*

Nadat u uw SSH-openbare sleutel hebt toegevoegd aan de schaalset, u SSH in een virtuele node-machine in die schaalset gebruiken met behulp van het IP-adres. Bekijk de privé-IP-adressen van de AKS-clusterknooppunten met behulp van de [opdracht kubectl get][kubectl-get].

```console
kubectl get nodes -o wide
```

De vervolgvoorbeelduitvoer toont de interne IP-adressen van alle knooppunten in het cluster, inclusief een Windows Server-knooppunt.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Noteer het interne IP-adres van het knooppunt dat u wilt oplossen.

Volg de stappen in [De SSH-verbinding maken](#create-the-ssh-connection)om toegang te krijgen tot uw knooppunt met SSH.

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Setgebaseerde AKS-clusters voor virtuele machines configureren voor SSH-toegang

Als u uw AKS-cluster voor de beschikbaarheid van virtuele machines wilt configureren voor SSH-toegang, zoekt u de naam van het Linux-knooppunt van uw cluster en voegt u uw SSH-openbare sleutel toe aan dat knooppunt.

Gebruik de opdracht [az aks show][az-aks-show] om de naam van de brongroep van uw AKS-cluster te krijgen, en vervolgens de opdracht [AZ VM-lijst][az-vm-list] om de virtuele machinenaam van het Linux-knooppunt van uw cluster weer te geven.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

In het bovenstaande voorbeeld wordt de naam van de clusterbrongroep voor de *myAKSCluster* in *myResourceGroup* aan *CLUSTER_RESOURCE_GROUP*. In het voorbeeld wordt *vervolgens CLUSTER_RESOURCE_GROUP* gebruikt om de naam van de virtuele machine weer te geven. De voorbeelduitvoer toont de naam van de virtuele machine:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Als u uw SSH-sleutels aan het knooppunt wilt toevoegen, gebruikt u de opdracht [voor het bijwerken van de AZ-gebruiker.][az-vm-user-update]

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

In het bovenstaande voorbeeld wordt de *CLUSTER_RESOURCE_GROUP* variabele en de naam van de virtuele machine van het knooppunt van eerdere opdrachten gebruikt. Het bovenstaande voorbeeld gebruikt ook *~/.ssh/id_rsa.pub* als locatie voor uw SSH-openbare sleutel. U ook de inhoud van uw Openbare SSH-sleutel gebruiken in plaats van een pad op te geven.

> [!NOTE]
> Standaard is de gebruikersnaam voor de AKS-knooppunten *azureuser.*

Nadat u uw SSH-openbare sleutel aan de virtuele computer van het knooppunt hebt toegevoegd, u SSH in die virtuele machine gebruiken met behulp van het IP-adres. Bekijk het privé-IP-adres van een AKS-clusterknooppunt met de opdracht [az vm list-ip-adressen.][az-vm-list-ip-addresses]

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

In het bovenstaande voorbeeld wordt de *variabele CLUSTER_RESOURCE_GROUP* in de vorige opdrachten gebruikt. In de volgende voorbeelduitvoer worden de privé-IP-adressen van de AKS-knooppunten weergegeven:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>De SSH-verbinding maken

Als u een SSH-verbinding met een AKS-knooppunt wilt maken, voert u een helperpod uit in uw AKS-cluster. Deze helperpod biedt u SSH-toegang tot het cluster en vervolgens extra SSH-knooppunttoegang. Voer de volgende stappen uit om deze helperpod te maken en te gebruiken:

1. Voer `debian` een containerafbeelding uit en voeg er een terminalsessie aan toe. Deze container kan worden gebruikt om een SSH-sessie te maken met elk knooppunt in het AKS-cluster:

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Als u Windows Server-knooppunten gebruikt (momenteel in preview in AKS), voegt u een knooppuntkiezer toe aan de opdracht om de Debian-container op een Linux-knooppunt te plannen:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Zodra de terminalsessie is aangesloten op de container, installeert u een SSH-client met behulp van: `apt-get`

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Open een nieuw terminalvenster, dat niet is aangesloten op uw container, kopieer uw privé SSH-sleutel naar de helperpod. Deze privésleutel wordt gebruikt om de SSH in het AKS-knooppunt te maken. 

   Wijzig indien nodig *~/.ssh/id_rsa* naar de locatie van uw privé SSH-toets:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Ga terug naar de terminalsessie naar uw container, `id_rsa` werk de machtigingen op de gekopieerde privé SSH-sleutel bij, zodat deze alleen voor de gebruiker is:

    ```console
    chmod 0600 id_rsa
    ```

1. Maak een SSH-verbinding met uw AKS-knooppunt. Nogmaals, de standaardgebruikersnaam voor AKS-knooppunten is *azureuser.* Accepteer de prompt om door te gaan met de verbinding terwijl de SSH-toets eerst wordt vertrouwd. U bent dan voorzien van de bash prompt van uw AKS knooppunt:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>SSH-toegang verwijderen

Wanneer u `exit` klaar bent, `exit` de SSH-sessie en vervolgens de interactieve containersessie. Wanneer deze containersessie wordt gesloten, wordt de pod die wordt gebruikt voor SSH-toegang vanuit het AKS-cluster verwijderd.

## <a name="next-steps"></a>Volgende stappen

Als u aanvullende gegevens over probleemoplossing nodig hebt, u [de kubelet-logboeken bekijken][view-kubelet-logs] of [de hoofdknooppuntlogboeken van Kubernetes bekijken.][view-master-logs]

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
