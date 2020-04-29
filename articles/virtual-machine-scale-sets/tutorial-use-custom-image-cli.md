---
title: Zelf studie-een aangepaste VM-installatie kopie gebruiken in een schaalset met Azure CLI
description: Informatie over het gebruik van Azure CLI voor het maken van een aangepaste VM-installatiekopie die u kunt gebruiken om een virtuele-machineschaalset te implementeren
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6d9f625bf425a33b690fd303a4f13d032bd59fa0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80062718"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Zelfstudie: Een aangepaste installatiekopie voor virtuele-machineschaalsets maken en gebruiken met Azure CLI
Wanneer u een schaalset maakt, geeft u een installatiekopie op die moet worden gebruikt wanneer de VM-exemplaren zijn geïmplementeerd. Om het aantal taken na de implementatie van VM-exemplaren te verminderen, kunt u een aangepaste VM-installatiekopie gebruiken. Deze aangepaste VM-installatiekopie bevat alle geïnstalleerde toepassingen of configuraties die vereist zijn. Alle VM-exemplaren die in de schaalset zijn gemaakt, gebruiken de aangepaste VM-installatiekopie en zijn gereed voor uw toepassingsverkeer. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een VM maken en aanpassen
> * De inrichting van de VM ongedaan maken en de VM generaliseren
> * Een aangepaste VM-installatiekopie maken
> * Een schaalset implementeren die gebruikmaakt van de aangepaste VM-installatiekopie

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie Azure CLI 2.0.29 of hoger gebruiken. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Een bron-VM maken en configureren

>[!NOTE]
> Deze zelfstudie leidt u door het proces van het maken en gebruiken van een algemene VM-installatiekopie. Het maken van een schaalset op basis van een gespecialiseerde VM-installatiekopie wordt niet ondersteund.

Voordat u een virtuele machine kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group) en daarna een virtuele machine maken met [az vm create](/cli/azure/vm). Deze VM wordt vervolgens gebruikt als bron voor een aangepaste VM-installatiekopie. In het volgende voorbeeld wordt een virtuele machine gemaakt met de naam *myVM* in de resourcegroep met de naam *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het openbare IP-adres van uw virtuele machine wordt weergegeven in de uitvoer van de opdracht [az vm create](/cli/azure/vm). SSH in het openbare IP-adres van uw VM als volgt:

```console
ssh azureuser@<publicIpAddress>
```

We gaan een eenvoudige webserver voor het aanpassen van uw VM installeren. Bij implementatie van het VM-exemplaar in de schaalset heeft dit hiermee alle vereiste pakketten om een webtoepassing uit te voeren. Gebruik `apt-get` om *NGINX* als volgt te installeren:

```bash
sudo apt-get install -y nginx
```

Als laatste stap voor het voorbereiden van uw virtuele machine voor gebruik als aangepaste installatiekopie moet u de inrichting van de virtuele machine ongedaan maken. Met deze stap verwijdert u systeemspecifieke gegevens uit de virtuele machine en maakt u het mogelijk om een groot aantal virtuele machines op basis van één installatiekopie te implementeren. Tijdens het ongedaan maken van de inrichting van de virtuele machine wordt de naam van de host opnieuw ingesteld op *localhost.localdomain*. SSH-hostsleutels, naamserverconfiguraties hoofdwachtwoord en in de cache geplaatste DHCP-leases worden ook verwijderd.

Voor het ongedaan maken van de inrichting van de virtuele machine gebruikt u de Azure VM-agent (*waagent*). De Azure VM-agent is geïnstalleerd op elke virtuele machine en wordt gebruikt om te communiceren met het Azure-platform. De parameter `-force` instrueert de agent aanvragen te accepteren om de machine-specifieke informatie opnieuw in te stellen.

```bash
sudo waagent -deprovision+user -force
```

Sluit de SSH-verbinding naar de virtuele machine:

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Een aangepaste VM-installatiekopie maken van de bron-VM
De bron-VM is nu aangepast met de geïnstalleerde Nginx-webserver. We maken een aangepaste VM-installatiekopie om deze te gebruiken met een schaalset.

Voor het maken van een installatiekopie moet de toewijzing van de VM ongedaan worden gemaakt. Maak de toewijzing van de VM ongedaan met [az vm deallocate](/cli//azure/vm). Stel nu de status van de virtuele machine met [az vm generalize](/cli//azure/vm) in als gegeneraliseerd, zodat het Azure-platform weet dat de virtuele machine klaar is voor gebruik van een aangepaste installatiekopie. U kunt alleen een installatiekopie maken op basis van een gegeneraliseerde virtuele machine:


```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

Het duurt enkele minuten om de toewijzing ongedaan te maken en de virtuele machine te generaliseren.

U kunt nu een installatiekopie van de virtuele machine maken met behulp van [az image create](/cli//azure/image). In het volgende voorbeeld wordt er een installatiekopie met de naam *myImage* gemaakt van uw virtuele machine:

> ERAAN Als de resource groep en de locatie van de virtuele machine verschillen, kunt u `--location` de para meter aan de onderstaande opdrachten toevoegen aan de locatie van de bron-VM die wordt gebruikt om de installatie kopie te maken. 

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Een schaalset maken op basis van de aangepaste VM-installatiekopie
Maak een schaalset met [az vmss create](/cli/azure/vmss#az-vmss-create). In plaats van een platforminstallatiekopie, zoals *UbuntuLTS* of *CentOS*, geeft u de naam van uw aangepaste VM-installatiekopie op. In het volgende voorbeeld wordt een schaalset gemaakt met de naam *myScaleSet*, die gebruikmaakt van de aangepaste installatiekopie met de naam *myImage* uit de vorige stap:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.


## <a name="test-your-scale-set"></a>Uw schaalset testen
Als u wilt dat verkeer de webserver kan bereiken, maakt u een load balancer-regel met behulp van [az network lb rule create](/cli/azure/network/lb/rule). In het volgende voorbeeld wordt een regel met de naam *myLoadBalancerRuleWeb* gemaakt die verkeer toestaat op *TCP*-poort *80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Als u de schaalset in actie wilt zien, achterhaalt u het openbare IP-adres van uw load balancer met [az network public-ip show](/cli/azure/network/public-ip). In het volgende voorbeeld wordt het IP-adres voor *myScaleSetLBPublicIP* opgehaald, dat is gemaakt als onderdeel van de schaalset:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Voer in uw webbrowser het openbare IP-adres in. De standaard NGINX-webpagina wordt weergegeven, zoals in het volgende voorbeeld:

![NGINX uitgevoerd vanuit een aangepaste VM-installatiekopie](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en aanvullende resources wilt verwijderen, verwijdert u de resource groep en alle bijbehorende resources met [AZ Group delete](/cli/azure/group). De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een aangepaste VM-installatiekopie maakt en gebruikt voor uw schaalsets met Azure CLI:

> [!div class="checklist"]
> * Een VM maken en aanpassen
> * De inrichting van de VM ongedaan maken en de VM generaliseren
> * Een aangepaste VM-installatiekopie maken
> * Een schaalset implementeren die gebruikmaakt van de aangepaste VM-installatiekopie

Ga door naar de volgende zelfstudie voor informatie over het implementeren van toepassingen naar uw schaalset.

> [!div class="nextstepaction"]
> [Toepassingen in uw schaalsets implementeren](tutorial-install-apps-cli.md)
