---
title: Statisch IP-adres voor containergroep
description: Een containergroep maken in een virtueel netwerk en een Azure-toepassingsgateway gebruiken om een statisch IP-adres aan een gecontaineriseerde webapp bloot te stellen
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481786"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Een statisch IP-adres voor een containergroep weergeven

In dit artikel wordt een manier weergegeven om een statisch, openbaar IP-adres voor een [containergroep](container-instances-container-groups.md) bloot te leggen met behulp van een [Azure-toepassingsgateway.](../application-gateway/overview.md) Volg deze stappen wanneer u een statisch toegangspunt nodig hebt voor een externe containerapp die wordt uitgevoerd in Azure Container Instances. 

In dit artikel gebruikt u de Azure CLI om de bronnen voor dit scenario te maken:

* Een virtueel Azure-netwerk
* Een containergroep die is geïmplementeerd [in het virtuele netwerk (voorbeeld)](container-instances-vnet.md) met een kleine web-app
* Een toepassingsgateway met een openbaar IP-adres aan de voorkant, een listener om een website op de gateway te hosten en een route naar de backendcontainergroep

Zolang de toepassingsgateway wordt uitgevoerd en de containergroep een stabiel privé-IP-adres in het gedelegeerde subnet van het netwerk blootstelt, is de containergroep toegankelijk op dit openbare IP-adres.

> [!NOTE]
> Azure brengt kosten in rekening voor een toepassingsgateway op basis van de hoeveelheid tijd die de gateway heeft ingericht en beschikbaar is, evenals de hoeveelheid gegevens die wordt verwerkt. Zie [prijzen](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Virtueel netwerk maken

In een typisch geval hebt u mogelijk al een virtueel Azure-netwerk. Als u er geen hebt, maakt u er een zoals weergegeven met de volgende voorbeeldopdrachten. Het virtuele netwerk heeft aparte subnetten nodig voor de toepassingsgateway en de containergroep.

Als u er een nodig hebt, maakt u een Azure-brongroep. Bijvoorbeeld:

```azureci
az group create --name myResourceGroup --location eastus
```

Maak een virtueel netwerk met de [vnet-opdracht van het AZ-netwerk.][az-network-vnet-create] Met deze opdracht wordt het *subnet myAGSubnet* in het netwerk gemaakt.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Gebruik de [opdracht vnet-subnet van het AZ-netwerk om][az-network-vnet-subnet-create] een subnet te maken voor de backendcontainergroep. Hier heet het *myACISubnet.*

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Gebruik de [opdracht public-ip-maken van het AZ-netwerk][az-network-public-ip-create] om een statische openbare IP-bron te maken. In een latere stap wordt dit adres geconfigureerd als de voorkant van de toepassingsgateway.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Containergroep maken

Voer de volgende [az-containermaken uit][az-container-create] om een containergroep te maken in het virtuele netwerk dat u in de vorige stap hebt geconfigureerd. 

De groep wordt geïmplementeerd in het *subnet myACISubnet* en bevat `aci-helloworld` één exemplaar met de naam *appcontainer* die de afbeelding trekt. Zoals in andere artikelen in de documentatie wordt weergegeven, wordt in deze afbeelding een kleine web-app gemaakt die is geschreven in Node.js en een statische HTML-pagina. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Wanneer de containergroep succesvol is geïmplementeerd, krijgt de containergroep een privé-IP-adres in het virtuele netwerk toegewezen. Voer bijvoorbeeld de volgende opdracht [voor az-containershow][az-container-show] uit om het IP-adres van de groep op te halen:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

De uitvoer is vergelijkbaar met: `10.0.2.4`.

Sla het IP-adres op in een omgevingsvariabele voor gebruik in een latere stap:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>Een toepassingsgateway maken

Maak een toepassingsgateway in het virtuele netwerk, volgens de stappen in de snelstart van de [toepassingsgateway.](../application-gateway/quick-create-cli.md) Met de volgende opdracht [az-netwerktoepassing-gateway][az-network-application-gateway-create] wordt een gateway gemaakt met een openbaar IP-adres aan de voorkant en een route naar de backendcontainergroep. Zie de documentatie van de [toepassingsgateway](/azure/application-gateway/) voor meer informatie over de gateway-instellingen.

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Het kan tot 15 minuten duren voordat Azure de toepassingsgateway maakt. 

## <a name="test-public-ip-address"></a>Openbare IP-adres testen
  
Nu u de toegang testen tot de web-app die wordt uitgevoerd in de containergroep achter de toepassingsgateway.

Voer de [opdracht public-ip-show van het AZ-netwerk uit][az-network-public-ip-show] om het openbare IP-adres van de gateway op te halen:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Output is een openbaar IP-adres, vergelijkbaar met: `52.142.18.133`.

Als u de web-app wilt weergeven wanneer deze is geconfigureerd, navigeert u naar het openbare IP-adres van de gateway in uw browser. Succesvolle toegang is vergelijkbaar met:

![Schermafbeelding van browser met toepassing die wordt uitgevoerd in een exemplaar van een Azure-container](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Volgende stappen

* Zie een [snelstartsjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) om een containergroep te maken met een WordPress-containerinstantie als backendserver achter een toepassingsgateway.
* U ook een toepassingsgateway configureren met een certificaat voor SSL-beëindiging. Zie het [overzicht](../application-gateway/ssl-overview.md) en de [tutorial](../application-gateway/create-ssl-portal.md).
* Afhankelijk van uw scenario u overwegen andere Azure-oplossingen voor taakverdeling te gebruiken met Azure Container Instances. Gebruik azure [traffic manager](../traffic-manager/traffic-manager-overview.md) bijvoorbeeld om verkeer over meerdere containerinstanties en over meerdere regio's te distribueren. Zie deze [blogpost](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show