---
title: Toegang tot een virtueel netwerk beperken
description: Toegang tot een Azure container Registry alleen toestaan van resources in een virtueel Azure-netwerk of open bare IP-adresbereiken.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74454341"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Toegang tot een Azure container Registry beperken met behulp van een virtueel netwerk of een firewall-regel van Azure

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biedt veilige, persoonlijke netwerken voor uw Azure-en on-premises resources. Door de toegang tot uw persoonlijke Azure-container register te beperken vanuit een virtueel Azure-netwerk, zorgt u ervoor dat alleen bronnen in het virtuele netwerk toegang hebben tot het REGI ster. Voor cross-premises scenario's kunt u ook firewall regels configureren om alleen toegang tot het REGI ster vanuit specifieke IP-adressen toe te staan.

Dit artikel bevat twee scenario's voor het configureren van binnenkomende regels voor netwerk toegang op een container register: van een virtuele machine die is geïmplementeerd in een virtueel netwerk of van het open bare IP-adres van een VM.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er [zijn enkele beperkingen van toepassing](#preview-limitations). Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.
>

Zie [regels configureren voor toegang tot een Azure-container register achter een firewall](container-registry-firewall-access-rules.md)als u in plaats daarvan toegangs regels wilt instellen voor bronnen om een container register van achter een firewall te bereiken.


## <a name="preview-limitations"></a>Preview-beperkingen

* Alleen een **Premium** container Registry kan worden geconfigureerd met netwerk toegangs regels. Zie [Azure container Registry sku's](container-registry-skus.md)voor meer informatie over de service lagen van het REGI ster. 

* Alleen een [Azure Kubernetes service](../aks/intro-kubernetes.md) -cluster of [virtuele](../virtual-machines/linux/overview.md) Azure-machine kan worden gebruikt als een host voor toegang tot een container register in een virtueel netwerk. *Andere Azure-Services, waaronder Azure Container Instances, worden momenteel niet ondersteund.*

* [ACR-taken](container-registry-tasks-overview.md) bewerkingen worden momenteel niet ondersteund in een container register dat wordt geopend in een virtueel netwerk.

* Elk REGI ster ondersteunt Maxi maal 100 regels voor virtuele netwerken.

## <a name="prerequisites"></a>Vereisten

* Als u de stappen van Azure CLI in dit artikel wilt gebruiken, is Azure CLI-versie 2.0.58 of later vereist. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli].

* Als u nog geen container register hebt, maakt u er een (Premium SKU vereist) en pusht u een voorbeeld `hello-world` installatie kopie, zoals van docker hub. Gebruik bijvoorbeeld de [Azure Portal][quickstart-portal] of de [Azure cli][quickstart-cli] om een REGI ster te maken. 

* Als u de toegang tot het REGI ster wilt beperken met behulp van een virtueel netwerk in een ander Azure-abonnement, moet u de resource provider registreren voor Azure Container Registry in dat abonnement. Bijvoorbeeld:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Over netwerk regels voor een container register

Een Azure-container register accepteert standaard verbindingen via Internet van hosts op elk netwerk. Met een virtueel netwerk kunt u alleen Azure-resources, zoals een AKS-cluster of Azure-VM, toestaan om veilig toegang te krijgen tot het REGI ster, zonder een netwerk grens te overschrijden. U kunt ook firewall regels voor netwerken configureren om alleen specifieke open bare IP-adresbereiken voor Internet toe te staan. 

Als u de toegang tot een REGI ster wilt beperken, wijzigt u eerst de standaard actie van het REGI ster zodat alle netwerk verbindingen worden geweigerd. Voeg vervolgens netwerk toegangs regels toe. Clients die toegang hebben verleend via de netwerk regels, moeten blijven [verifiëren bij het container register](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) en moeten worden gemachtigd om toegang te krijgen tot de gegevens.

### <a name="service-endpoint-for-subnets"></a>Service-eind punt voor subnetten

Als u toegang wilt toestaan vanaf een subnet in een virtueel netwerk, moet u een [service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) voor de Azure container Registry-service toevoegen. 

Multi tenant Services, zoals Azure Container Registry, gebruiken één set IP-adressen voor alle klanten. Een service-eind punt wijst een eind punt toe voor toegang tot een REGI ster. Dit eind punt geeft een optimale route naar de resource via het Azure-backbone-netwerk. De identiteiten van het virtuele netwerk en het subnet worden ook met elke aanvraag verzonden.

### <a name="firewall-rules"></a>Firewall-regels

Geef voor IP-netwerk regels toegestane Internet adresbereiken op met behulp van CIDR-notatie, zoals *16.17.18.0/24* , of een afzonderlijke IP-adressen, zoals *16.17.18.19*. IP-netwerk regels zijn alleen toegestaan voor *open bare* Internet-IP-adressen. IP-adresbereiken die zijn gereserveerd voor particuliere netwerken (zoals gedefinieerd in RFC 1918) zijn niet toegestaan in IP-regels.

## <a name="create-a-docker-enabled-virtual-machine"></a>Een virtuele machine maken die is ingeschakeld voor docker

Gebruik voor dit artikel een docker-Ubuntu-VM om toegang te krijgen tot een Azure container Registry. Als u Azure Active Directory verificatie wilt gebruiken voor het REGI ster, installeert u ook de [Azure cli][azure-cli] op de VM. Als u al een virtuele machine van Azure hebt, kunt u deze stap voor het maken overs Laan.

U kunt dezelfde resource groep gebruiken voor uw virtuele machine en het container register. Deze installatie vereenvoudigt aan het einde van het opschonen, maar is niet vereist. Als u ervoor kiest om een afzonderlijke resource groep te maken voor de virtuele machine en het virtuele netwerk, voert u [AZ Group Create][az-group-create]uit. In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *westcentralus* :

```azurecli
az group create --name myResourceGroup --location westus
```

Implementeer nu een standaard Ubuntu Azure-virtuele machine met [AZ VM Create][az-vm-create]. In het volgende voor beeld wordt een VM gemaakt met de naam *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt. Wanneer de opdracht is voltooid, noteert u de `publicIpAddress` weer gegeven door de Azure cli. Gebruik dit adres voor het maken van SSH-verbindingen met de virtuele machine en eventueel voor latere installatie van firewall regels.

### <a name="install-docker-on-the-vm"></a>Docker installeren op de VM

Nadat de virtuele machine is uitgevoerd, maakt u een SSH-verbinding met de VM. Vervang *publicIpAddress* door het open bare IP-adres van uw virtuele machine.

```bash
ssh azureuser@publicIpAddress
```

Voer de volgende opdracht uit om docker te installeren op de Ubuntu-VM:

```bash
sudo apt install docker.io -y
```

Na de installatie voert u de volgende opdracht uit om te controleren of docker correct wordt uitgevoerd op de VM:

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

Volg de stappen in [Azure cli installeren met apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) om de Azure CLI op uw virtuele Ubuntu-machine te installeren. Voor dit artikel moet u versie 2.0.58 of hoger installeren.

Sluit de SSH-verbinding.

## <a name="allow-access-from-a-virtual-network"></a>Toegang vanaf een virtueel netwerk toestaan

In deze sectie configureert u het container register zodanig dat toegang is toegestaan vanuit een subnet in een virtueel Azure-netwerk. Vergelijk bare stappen die gebruikmaken van de Azure CLI en Azure Portal worden meegeleverd.

### <a name="allow-access-from-a-virtual-network---cli"></a>Toegang vanaf een virtueel netwerk-CLI toestaan

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Een service-eind punt toevoegen aan een subnet

Wanneer u een virtuele machine maakt, maakt Azure standaard een virtueel netwerk in dezelfde resource groep. De naam van het virtuele netwerk is gebaseerd op de naam van de virtuele machine. Als u bijvoorbeeld een *myDockerVM*naam voor de virtuele machine hebt, is de standaard naam van het virtuele netwerk *myDockerVMVNET*, met een subnet met de naam *myDockerVMSubnet*. Controleer dit in de Azure Portal of met behulp van de opdracht [AZ Network vnet List][az-network-vnet-list] :

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

Gebruik de opdracht [AZ Network vnet subnet update][az-network-vnet-subnet-update] om een service-eind punt van **micro soft. ContainerRegistry** toe te voegen aan uw subnet. Vervang de namen van uw virtuele netwerk en subnet door de volgende opdracht:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Gebruik de opdracht [AZ Network vnet subnet show][az-network-vnet-subnet-show] om de resource-id van het subnet op te halen. U hebt dit in een latere stap nodig om een netwerk toegangs regel te configureren.

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

#### <a name="change-default-network-access-to-registry"></a>De standaard netwerk toegang wijzigen in het REGI ster

Een Azure container Registry staat standaard verbindingen toe van hosts op elk netwerk. Als u de toegang tot een geselecteerd netwerk wilt beperken, wijzigt u de standaard actie om de toegang te weigeren. Vervang de naam van het REGI ster in de volgende [AZ ACR update][az-acr-update] -opdracht:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Netwerk regel toevoegen aan REGI ster

Gebruik de opdracht [AZ ACR Network-Rule add][az-acr-network-rule-add] om een netwerk regel toe te voegen aan het REGI ster waarmee toegang vanuit het subnet van de virtuele machine wordt toegestaan. Vervang de naam van het container register en de bron-ID van het subnet in de volgende opdracht: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Ga door met [het controleren van de toegang tot het REGI ster](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Toegang vanaf een virtueel netwerk-Portal toestaan

#### <a name="add-service-endpoint-to-subnet"></a>Service-eind punt toevoegen aan subnet

Wanneer u een virtuele machine maakt, maakt Azure standaard een virtueel netwerk in dezelfde resource groep. De naam van het virtuele netwerk is gebaseerd op de naam van de virtuele machine. Als u bijvoorbeeld een *myDockerVM*naam voor de virtuele machine hebt, is de standaard naam van het virtuele netwerk *myDockerVMVNET*, met een subnet met de naam *myDockerVMSubnet*.

Een service-eind punt voor Azure Container Registry toevoegen aan een subnet:

1. Voer in het zoekvak boven aan de [Azure Portal][azure-portal] *virtuele netwerken*in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
1. Selecteer in de lijst met virtuele netwerken het virtuele netwerk waarin uw virtuele machine is geïmplementeerd, bijvoorbeeld *myDockerVMVNET*.
1. Selecteer onder **instellingen**de optie **subnetten**.
1. Selecteer het subnet waarin uw virtuele machine is geïmplementeerd, bijvoorbeeld *myDockerVMSubnet*.
1. Selecteer onder **service-eind punten** **micro soft. ContainerRegistry**.
1. Selecteer **Opslaan**.

![Service-eind punt toevoegen aan subnet][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Netwerk toegang voor het REGI ster configureren

Een Azure container Registry staat standaard verbindingen toe van hosts op elk netwerk. De toegang tot het virtuele netwerk beperken:

1. Navigeer in de portal naar het container register.
1. Onder **instellingen**selecteert u **firewall en virtuele netwerken**.
1. Als u de toegang standaard wilt weigeren, kiest u toegang vanaf **geselecteerde netwerken**toestaan. 
1. Selecteer **bestaand virtueel netwerk toevoegen**en selecteer het virtuele netwerk en het subnet dat u hebt geconfigureerd met een service-eind punt. Selecteer **Toevoegen**.
1. Selecteer **Opslaan**.

![Virtueel netwerk configureren voor container register][acr-vnet-portal]

Ga door met [het controleren van de toegang tot het REGI ster](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Toegang vanaf een IP-adres toestaan

In deze sectie configureert u het container register om toegang vanaf een specifiek IP-adres of bereik toe te staan. Vergelijk bare stappen die gebruikmaken van de Azure CLI en Azure Portal worden meegeleverd.

### <a name="allow-access-from-an-ip-address---cli"></a>Toegang vanaf een IP-adres-CLI toestaan

#### <a name="change-default-network-access-to-registry"></a>De standaard netwerk toegang wijzigen in het REGI ster

Als u dit nog niet hebt gedaan, werkt u de Register configuratie bij om de toegang standaard te weigeren. Vervang de naam van het REGI ster in de volgende [AZ ACR update][az-acr-update] -opdracht:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Netwerk regel uit REGI ster verwijderen

Als u eerder een netwerk regel hebt toegevoegd om toegang vanuit het subnet van de virtuele machine toe te staan, verwijdert u het service-eind punt van het subnet en de netwerk regel. Vervang de naam van het container register en de resource-ID van het subnet dat u in een eerdere stap hebt opgehaald in de opdracht [AZ ACR Network-regel Remove][az-acr-network-rule-remove] : 

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

#### <a name="add-network-rule-to-registry"></a>Netwerk regel toevoegen aan REGI ster

Gebruik de opdracht [AZ ACR Network-Rule add][az-acr-network-rule-add] om een netwerk regel toe te voegen aan het REGI ster waarmee toegang vanaf het IP-adres van de virtuele machine wordt toegestaan. Vervang de naam van het container register en het open bare IP-adres van de virtuele machine in de volgende opdracht.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Ga door met [het controleren van de toegang tot het REGI ster](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Toegang vanaf een IP-adres toestaan-Portal

#### <a name="remove-existing-network-rule-from-registry"></a>Bestaande netwerk regel uit REGI ster verwijderen

Als u eerder een netwerk regel hebt toegevoegd om toegang vanuit het subnet van de virtuele machine toe te staan, verwijdert u de bestaande regel. Sla deze sectie over als u toegang tot het REGI ster wilt krijgen vanaf een andere VM.

* Werk de instellingen van het subnet bij om het service-eind punt van het subnet voor Azure Container Registry te verwijderen. 

  1. Ga in het [Azure Portal][azure-portal]naar het virtuele netwerk waarin uw virtuele machine is geïmplementeerd.
  1. Selecteer onder **instellingen**de optie **subnetten**.
  1. Selecteer het subnet waarin uw virtuele machine is geïmplementeerd.
  1. Verwijder onder **service-eind punten**het selectie vakje voor **micro soft. ContainerRegistry**. 
  1. Selecteer **Opslaan**.

* Verwijder de netwerk regel waarmee het subnet toegang kan krijgen tot het REGI ster.

  1. Navigeer in de portal naar het container register.
  1. Onder **instellingen**selecteert u **firewall en virtuele netwerken**.
  1. Onder **virtuele netwerken**selecteert u de naam van het virtuele netwerk en selecteert u vervolgens **verwijderen**.
  1. Selecteer **Opslaan**.

#### <a name="add-network-rule-to-registry"></a>Netwerk regel toevoegen aan REGI ster

1. Navigeer in de portal naar het container register.
1. Onder **instellingen**selecteert u **firewall en virtuele netwerken**.
1. Als u dit nog niet hebt gedaan, kunt u kiezen of u toegang hebt tot **geselecteerde netwerken**. 
1. Zorg ervoor dat onder **virtuele netwerken**geen netwerk is geselecteerd.
1. Onder **firewall**voert u het open bare IP-adres van een virtuele machine in. Of voer een adres bereik in de CIDR-notatie in die het IP-adres van de virtuele machine bevat.
1. Selecteer **Opslaan**.

![Firewall regel voor container register configureren][acr-vnet-firewall-portal]

Ga door met [het controleren van de toegang tot het REGI ster](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Toegang tot het REGI ster controleren

Nadat u een paar minuten hebt gewacht voordat de configuratie is bijgewerkt, controleert u of de virtuele machine toegang heeft tot het container register. Maak een SSH-verbinding met uw virtuele machine en voer de opdracht [AZ ACR login][az-acr-login] uit om u aan te melden bij het REGI ster. 

```bash
az acr login --name mycontainerregistry
```

U kunt register bewerkingen uitvoeren zoals uitvoeren `docker pull` om een voorbeeld installatie kopie uit het REGI ster te halen. Vervang een installatie kopie en label waarde die geschikt is voor uw REGI ster, voorafgegaan door de naam van de aanmeldings server van het REGI ster (alle kleine letters):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker haalt de installatie kopie op naar de virtuele machine.

In dit voor beeld wordt gedemonstreerd dat u toegang hebt tot het persoonlijke container register via de netwerk toegangs regel. Het REGI ster kan echter niet worden geopend vanaf een andere aanmeldings host waarvoor geen netwerk toegangs regel is geconfigureerd. Als u zich probeert aan te melden bij een andere `az acr login` host met `docker login` behulp van de opdracht of opdracht, is de uitvoer vergelijkbaar met het volgende:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Standaard toegang tot het REGI ster herstellen

Als u het REGI ster wilt herstellen om standaard toegang toe te staan, verwijdert u de netwerk regels die zijn geconfigureerd. Stel vervolgens de standaard actie in om toegang toe te staan. Vergelijk bare stappen die gebruikmaken van de Azure CLI en Azure Portal worden meegeleverd.

### <a name="restore-default-registry-access---cli"></a>Standaard toegang tot het REGI ster herstellen-CLI

#### <a name="remove-network-rules"></a>Netwerk regels verwijderen

Als u een lijst wilt weer geven met netwerk regels die voor het REGI ster zijn geconfigureerd, voert u de volgende opdracht [AZ ACR Network-Rule List][az-acr-network-rule-list] uit:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Voer voor elke geconfigureerde regel de opdracht [AZ ACR Network-regel Remove][az-acr-network-rule-remove] uit om deze te verwijderen. Bijvoorbeeld:

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

Vervang de naam van het REGI ster in de volgende [AZ ACR update][az-acr-update] -opdracht:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Standaard toegang tot het REGI ster herstellen-Portal


1. Navigeer in de portal naar het container register en selecteer **firewall en virtuele netwerken**.
1. Selecteer onder **virtuele netwerken**elk virtueel netwerk en selecteer vervolgens **verwijderen**.
1. Onder **firewall**selecteert u elk adres bereik en selecteert u vervolgens het pictogram verwijderen.
1. Selecteer onder **toegang toestaan vanuit** **de optie alle netwerken**. 
1. Selecteer **Opslaan**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle Azure-resources in dezelfde resource groep hebt gemaakt en deze niet meer nodig hebt, kunt u de resources eventueel verwijderen met behulp van één opdracht [AZ Group delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

Als u uw resources in de portal wilt opschonen, gaat u naar de resource groep myResourceGroup. Zodra de resource groep is geladen, klikt u op **resource groep verwijderen** om de resource groep en de resources die daar zijn opgeslagen, te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel worden kortere bronnen en functies voor het virtuele netwerk beschreven. De Azure Virtual Network-documentatie behandelt deze onderwerpen uitgebreid:

* [Virtueel netwerk](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Subnetrouter](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
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
