---
title: Toegang beperken met een virtueel netwerk
description: Geef alleen toegang tot een Azure-containerregister toe vanuit bronnen in een virtueel Azure-netwerk of vanuit openbare IP-adresbereiken.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454341"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Toegang tot een Azure-containerregister beperken met behulp van een Azure virtueel netwerk of firewallregels

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biedt veilige, privénetwerken voor uw Azure- en on-premises bronnen. Door de toegang tot uw privé-Azure-containerregister te beperken vanuit een virtueel Azure-netwerk, zorgt u ervoor dat alleen resources in het virtuele netwerk toegang krijgen tot het register. Voor cross-premises scenario's u ook firewallregels configureren om alleen registertoegang toe te staan vanaf specifieke IP-adressen.

In dit artikel worden twee scenario's weergegeven voor het configureren van binnenkomende netwerktoegangsregels voor een containerregister: vanaf een virtuele machine die is geïmplementeerd in een virtueel netwerk of vanaf het openbare IP-adres van een VM.

> [!IMPORTANT]
> Deze functie is momenteel in preview en er zijn enkele [beperkingen van toepassing.](#preview-limitations) Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.
>

Zie [Regels configureren om toegang te krijgen tot een Azure-containerregister achter een firewall](container-registry-firewall-access-rules.md)als u in plaats daarvan toegangsregels moet instellen voor resources om een containerregister van achter een firewall te bereiken.


## <a name="preview-limitations"></a>Preview-beperkingen

* Alleen **Premium** een Premium-containerregister kan worden geconfigureerd met netwerktoegangsregels. Zie [SKU's](container-registry-skus.md)voor Azure Container Registry voor informatie over registerservicelagen. 

* Alleen een [Azure Kubernetes Service-cluster](../aks/intro-kubernetes.md) of [Azure-virtuele machine](../virtual-machines/linux/overview.md) kan worden gebruikt als host om toegang te krijgen tot een containerregister in een virtueel netwerk. *Andere Azure-services, waaronder Azure Container Instances, worden momenteel niet ondersteund.*

* [ACR-takenworden](container-registry-tasks-overview.md) momenteel niet ondersteund in een containerregister dat is geopend in een virtueel netwerk.

* Elk register ondersteunt maximaal 100 virtuele netwerkregels.

## <a name="prerequisites"></a>Vereisten

* Als u de Azure CLI-stappen in dit artikel wilt gebruiken, is Azure CLI-versie 2.0.58 of hoger vereist. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli].

* Als u nog geen containerregister hebt, maakt u er een (Premium SKU vereist) en drukt u op een voorbeeldafbeelding, zoals `hello-world` vanuit Docker Hub. Gebruik bijvoorbeeld de [Azure-portal][quickstart-portal] of de [Azure CLI][quickstart-cli] om een register te maken. 

* Als u de toegang tot het register wilt beperken met behulp van een virtueel netwerk in een ander Azure-abonnement, moet u de bronprovider voor Azure Container Registry in dat abonnement registreren. Bijvoorbeeld:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Informatie over netwerkregels voor een containerregister

Een Azure-containerregister accepteert standaard verbindingen via internet van hosts in elk netwerk. Met een virtueel netwerk u alleen Azure-bronnen zoals een AKS-cluster of Azure VM toestaan om veilig toegang te krijgen tot het register, zonder een netwerkgrens te overschrijden. U ook netwerkfirewallregels configureren om alleen specifieke IP-adresbereiken voor openbaar internet toe te staan. 

Als u de toegang tot een register wilt beperken, wijzigt u eerst de standaardactie van het register, zodat alle netwerkverbindingen worden ontnomen. Voeg vervolgens regels voor netwerktoegang toe. Clients die toegang krijgen via de netwerkregels moeten zich blijven [verifiëren in het containerregister](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) en gemachtigd zijn om toegang te krijgen tot de gegevens.

### <a name="service-endpoint-for-subnets"></a>Serviceeindpunt voor subnetten

Als u toegang wilt verlenen vanaf een subnet in een virtueel netwerk, moet u een [serviceeindpunt](../virtual-network/virtual-network-service-endpoints-overview.md) toevoegen voor de Azure Container Registry-service. 

Multitenant-services, zoals Azure Container Registry, gebruiken één set IP-adressen voor alle klanten. Een serviceeindpunt wijst een eindpunt toe om toegang te krijgen tot een register. Dit eindpunt geeft verkeer een optimale route naar de bron via het Azure-backbonenetwerk. De identiteiten van het virtuele netwerk en het subnet worden ook bij elk verzoek verzonden.

### <a name="firewall-rules"></a>Firewall-regels

Voor IP-netwerkregels moet u toegestane internetadresbereiken opgeven met CIDR-notatie, zoals *16.17.18.0/24* of een individueel IP-adres zoals *16.17.18.19*. IP-netwerkregels zijn alleen toegestaan voor *openbare* ip-adressen op internet. IP-adresbereiken die zijn gereserveerd voor particuliere netwerken (zoals gedefinieerd in RFC 1918) zijn niet toegestaan in IP-regels.

## <a name="create-a-docker-enabled-virtual-machine"></a>Een docker-enabled virtuele machine maken

Gebruik voor dit artikel een Ubuntu VM met Docker om toegang te krijgen tot een Azure-containerregister. Als u Azure Active Directory-verificatie wilt gebruiken in het register, installeert u ook de [Azure CLI][azure-cli] op de VM. Als u al een virtuele Azure-machine hebt, slaat u deze creatiestap over.

U dezelfde resourcegroep gebruiken voor uw virtuele machine en uw containerregister. Deze installatie vereenvoudigt het opruimen aan het einde, maar is niet vereist. Als u ervoor kiest een aparte resourcegroep voor de virtuele machine en het virtuele netwerk te maken, voert u [de AZ-groep uit.][az-group-create] In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de locatie *Westcentralus* ge:

```azurecli
az group create --name myResourceGroup --location westus
```

Implementeer nu een standaard Ubuntu Azure virtuele machine met [az vm maken.][az-vm-create] In het volgende voorbeeld wordt een VM met de naam *myDockerVM geopperd:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt. Wanneer de opdracht is voltooid, `publicIpAddress` neemt u nota van de weergave door de Azure CLI. Gebruik dit adres om SSH-verbindingen met de VM te maken en optioneel voor het later instellen van firewallregels.

### <a name="install-docker-on-the-vm"></a>Docker installeren op de VM

Nadat de VM is uitgevoerd, maakt u een SSH-verbinding met de VM. Vervang *publicIpAddress* door het openbare IP-adres van uw vm.

```bash
ssh azureuser@publicIpAddress
```

Voer de volgende opdracht uit om Docker op de Ubuntu VM te installeren:

```bash
sudo apt install docker.io -y
```

Voer na de installatie de volgende opdracht uit om te controleren of Docker correct op de VM wordt uitgevoerd:

```bash
sudo docker run -it hello-world
```

Uitvoer:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure-CLI installeren

Volg de stappen in [Azure CLI installeren met de toepasselijke inteom](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) de Azure CLI op uw virtuele Ubuntu-machine te installeren. Zorg ervoor dat u versie 2.0.58 of hoger installeert voor dit artikel.

Sluit de SSH-verbinding af.

## <a name="allow-access-from-a-virtual-network"></a>Toegang toestaan vanuit een virtueel netwerk

Configureer in deze sectie uw containerregister om toegang toe te staan vanaf een subnet in een virtueel Azure-netwerk. Er worden gelijkwaardige stappen met de Azure CLI- en Azure-portal aangeboden.

### <a name="allow-access-from-a-virtual-network---cli"></a>Toegang toestaan vanuit een virtueel netwerk - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Een serviceeindpunt toevoegen aan een subnet

Wanneer u een vm maakt, maakt Azure standaard een virtueel netwerk in dezelfde brongroep. De naam van het virtuele netwerk is gebaseerd op de naam van de virtuele machine. Als u bijvoorbeeld uw virtuele machine *myDockerVM noemt,* is de standaardnaam van het virtuele netwerk *myDockerVMVNET*, met een subnet met de naam *myDockerVMSubnet*. Controleer dit in de Azure-portal of met de opdracht [vnet-lijst van az-netwerk:][az-network-vnet-list]

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Uitvoer:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Gebruik de opdracht [vnet subnetupdate van het AZ-netwerk][az-network-vnet-subnet-update] om een eindpunt van de **Microsoft.ContainerRegistry-service** toe te voegen aan uw subnet. Vervang de namen van uw virtuele netwerk en subnet in de volgende opdracht:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Gebruik de [opdracht vnet-subnet show van het AZ-netwerk][az-network-vnet-subnet-show] om de resource-id van het subnet op te halen. U hebt dit in een latere stap nodig om een netwerktoegangsregel te configureren.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Uitvoer:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Standaardnetwerktoegang wijzigen in register

Standaard staat een Azure-containerregister verbindingen toe van hosts in elk netwerk. Als u de toegang tot een geselecteerd netwerk wilt beperken, wijzigt u de standaardactie om de toegang te weigeren. Vervang de naam van uw register in de volgende [opdracht az acr-update:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Netwerkregel toevoegen aan register

Gebruik de opdracht [AZ ACR-netwerkregel toevoegen][az-acr-network-rule-add] om een netwerkregel toe te voegen aan uw register waarmee toegang wordt geboden via het subnet van de VM. Vervang de naam van het containerregister en de resource-id van het subnet in de volgende opdracht: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Blijf [de toegang tot het register verifiëren.](#verify-access-to-the-registry)

### <a name="allow-access-from-a-virtual-network---portal"></a>Toegang toestaan vanuit een virtueel netwerk - portal

#### <a name="add-service-endpoint-to-subnet"></a>Eindpunt van service toevoegen aan subnet

Wanneer u een vm maakt, maakt Azure standaard een virtueel netwerk in dezelfde brongroep. De naam van het virtuele netwerk is gebaseerd op de naam van de virtuele machine. Als u bijvoorbeeld uw virtuele machine *myDockerVM noemt,* is de standaardnaam van het virtuele netwerk *myDockerVMVNET*, met een subnet met de naam *myDockerVMSubnet*.

Ga als lid van de service voor Azure Container Registry naar een subnet:

1. Voer *virtuele netwerken*in in het zoekvak boven aan de [Azure-portal][azure-portal]. Wanneer **virtuele netwerken** worden weergegeven in de zoekresultaten, selecteert u deze.
1. Selecteer in de lijst met virtuele netwerken het virtuele netwerk waar uw virtuele machine wordt geïmplementeerd, zoals *myDockerVMVNET.*
1. Selecteer **Subnetten**onder **Instellingen**.
1. Selecteer het subnet waar uw virtuele machine wordt geïmplementeerd, zoals *myDockerVMSubnet*.
1. Selecteer **Microsoft.ContainerRegistry**onder **Service-eindpunten**.
1. Selecteer **Opslaan**.

![Eindpunt van service toevoegen aan subnet][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Netwerktoegang configureren voor register

Standaard staat een Azure-containerregister verbindingen toe van hosts in elk netwerk. Ga als gevolg van het beperken van de toegang tot het virtuele netwerk:

1. Navigeer in de portal naar uw containerregister.
1. Selecteer **onder Instellingen**Firewall en virtuele **netwerken**.
1. Als u de toegang standaard wilt weigeren, kiest u ervoor om toegang toe te staan **vanuit geselecteerde netwerken**. 
1. Selecteer **Bestaand virtueel netwerk toevoegen**en selecteer het virtuele netwerk en subnet dat u hebt geconfigureerd met een serviceeindpunt. Selecteer **Toevoegen**.
1. Selecteer **Opslaan**.

![Virtueel netwerk configureren voor containerregister][acr-vnet-portal]

Blijf [de toegang tot het register verifiëren.](#verify-access-to-the-registry)

## <a name="allow-access-from-an-ip-address"></a>Toegang vanaf een IP-adres toestaan

Configureer in deze sectie uw containerregister om toegang toe te staan vanaf een specifiek IP-adres of -bereik. Er worden gelijkwaardige stappen met de Azure CLI- en Azure-portal aangeboden.

### <a name="allow-access-from-an-ip-address---cli"></a>Toegang toestaan vanaf een IP-adres - CLI

#### <a name="change-default-network-access-to-registry"></a>Standaardnetwerktoegang wijzigen in register

Als u dit nog niet hebt gedaan, werkt u de registerconfiguratie bij om de toegang standaard te weigeren. Vervang de naam van uw register in de volgende [opdracht az acr-update:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Netwerkregel uit register verwijderen

Als u eerder een netwerkregel hebt toegevoegd om toegang toe te staan vanuit het subnet van de VM, verwijdert u het serviceeindpunt van het subnet en de netwerkregel. Vervang de naam van het containerregister en de resource-id van het subnet dat u in een eerdere stap in de opdracht verwijderen van het [AZ ACR-netwerk verwijderd][az-acr-network-rule-remove] hebt opgehaald: 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Netwerkregel toevoegen aan register

Gebruik de opdracht [AZ ACR-netwerkregel toevoegen][az-acr-network-rule-add] om een netwerkregel toe te voegen aan uw register waarmee toegang wordt geboden vanaf het IP-adres van de VM. Vervang de naam van het containerregister en het openbare IP-adres van de virtuele machine in de volgende opdracht.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Blijf [de toegang tot het register verifiëren.](#verify-access-to-the-registry)

### <a name="allow-access-from-an-ip-address---portal"></a>Toegang toestaan vanaf een IP-adres - portal

#### <a name="remove-existing-network-rule-from-registry"></a>Bestaande netwerkregel uit register verwijderen

Als u eerder een netwerkregel hebt toegevoegd om toegang toe te staan vanuit het subnet van de VM, verwijdert u de bestaande regel. Sla deze sectie over als u toegang wilt krijgen tot het register vanaf een andere virtuele machine.

* Werk de subnetinstellingen bij om het serviceeindpunt van het subnet voor Azure Container Registry te verwijderen. 

  1. Navigeer in de [Azure-portal][azure-portal]naar het virtuele netwerk waar uw virtuele machine is geïmplementeerd.
  1. Selecteer **Subnetten**onder **Instellingen**.
  1. Selecteer het subnet waar uw virtuele machine wordt geïmplementeerd.
  1. Verwijder **onder Service-eindpunten**het selectievakje voor **Microsoft.ContainerRegistry**. 
  1. Selecteer **Opslaan**.

* Verwijder de netwerkregel waarmee het subnet toegang heeft tot het register.

  1. Navigeer in de portal naar uw containerregister.
  1. Selecteer **onder Instellingen**Firewall en virtuele **netwerken**.
  1. Selecteer **onder Virtuele netwerken**de naam van het virtuele netwerk en selecteer **Verwijderen**.
  1. Selecteer **Opslaan**.

#### <a name="add-network-rule-to-registry"></a>Netwerkregel toevoegen aan register

1. Navigeer in de portal naar uw containerregister.
1. Selecteer **onder Instellingen**Firewall en virtuele **netwerken**.
1. Als u dit nog niet hebt gedaan, kiest u ervoor om toegang toe te staan **vanuit geselecteerde netwerken.** 
1. Zorg **er onder Virtuele netwerken**voor dat er geen netwerk is geselecteerd.
1. Voer **onder Firewall**het openbare IP-adres van een virtuele machine in. Of voer een adresbereik in cidr-notatie in dat het IP-adres van de VM bevat.
1. Selecteer **Opslaan**.

![Firewallregel configureren voor containerregister][acr-vnet-firewall-portal]

Blijf [de toegang tot het register verifiëren.](#verify-access-to-the-registry)

## <a name="verify-access-to-the-registry"></a>Toegang tot het register verifiëren

Controleer na enkele minuten wachten tot de configuratie is bijgewerkt of de VM toegang heeft tot het containerregister. Maak een SSH-verbinding met uw VM en voer de az [acr-inlogopdracht][az-acr-login] uit om in te loggen op uw register. 

```bash
az acr login --name mycontainerregistry
```

U registerbewerkingen `docker pull` uitvoeren, zoals uitvoeren om een voorbeeldafbeelding uit het register te halen. Vervang een afbeelding en tagwaarde die geschikt is voor uw register, vooraf bevestigd met de naam van de registerinlogserver (alle kleine letters):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker trekt de afbeelding naar de VM.

In dit voorbeeld wordt aangetoond dat u toegang hebt tot het register voor privécontainers via de regel voor netwerktoegang. Het register is echter niet toegankelijk vanaf een andere inloghost die geen netwerktoegangsregel heeft geconfigureerd. Als u probeert in te `az acr login` loggen `docker login` vanaf een andere host met de opdracht of opdracht, is de uitvoer vergelijkbaar met de volgende:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Standaardregistertoegang herstellen

Als u het register wilt herstellen om standaard toegang te verlenen, verwijdert u netwerkregels die zijn geconfigureerd. Stel vervolgens de standaardactie in om toegang toe te staan. Er worden gelijkwaardige stappen met de Azure CLI- en Azure-portal aangeboden.

### <a name="restore-default-registry-access---cli"></a>Standaardregistertoegang herstellen - CLI

#### <a name="remove-network-rules"></a>Netwerkregels verwijderen

Voer de volgende [opdracht az acr-netwerkregellijst][az-acr-network-rule-list] uit om een lijst met netwerkregels voor uw register te bekijken:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Voer voor elke regel die is geconfigureerd de opdracht [az acr-netwerkverwijderen][az-acr-network-rule-remove] uit om deze te verwijderen. Bijvoorbeeld:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Toegang toestaan

Vervang de naam van uw register in de volgende [opdracht az acr-update:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Standaardregistertoegang herstellen - portal


1. Navigeer in de portal naar uw containerregister en selecteer **Firewall en virtuele netwerken.**
1. Selecteer **onder Virtuele netwerken**elk virtueel netwerk en selecteer **Verwijderen**.
1. Selecteer **onder Firewall**elk adresbereik en selecteer vervolgens het pictogram Verwijderen.
1. Selecteer Alle netwerken **onder Toegang toestaan van**alle **netwerken**. 
1. Selecteer **Opslaan**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle Azure-bronnen in dezelfde brongroep hebt gemaakt en deze niet meer nodig hebt, u de resources optioneel verwijderen met één opdracht [voor het verwijderen van az-groepen:](/cli/azure/group)

```azurecli
az group delete --name myResourceGroup
```

Als u uw resources in de portal wilt opschonen, navigeert u naar de brongroep myResourceGroup. Zodra de resourcegroep is geladen, klikt u op **Resourcegroep verwijderen** om de resourcegroep en de bronnen die daar zijn opgeslagen te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Verschillende virtuele netwerkbronnen en -functies werden besproken in dit artikel, hoewel kort. De documentatie van het Azure Virtual Network behandelt deze onderwerpen uitgebreid:

* [Virtueel netwerk](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Service-eindpunten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
