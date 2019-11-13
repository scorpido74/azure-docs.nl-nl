---
title: Op URL-pad gebaseerde routerings regels met CLI-Azure-toepassing gateway
description: Meer informatie over het maken van op URL-pad gebaseerde routerings regels voor een toepassings gateway en een schaalset voor virtuele machines met behulp van de Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 7198e68530a51e6c2002b3beb08f14615a5c70fb
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012331"
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-the-azure-cli"></a>Een toepassings gateway met op URL-pad gebaseerde routerings regels maken met behulp van Azure CLI

U kunt Azure CLI gebruiken om [op een URL-pad gebaseerde routeringsregels](application-gateway-url-route-overview.md) te configureren als u een [toepassingsgateway maakt](application-gateway-introduction.md). In deze zelf studie maakt u back-end-Pools met behulp van een [schaalset voor virtuele machines](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Vervolgens maakt u routerings regels die ervoor zorgen dat webverkeer binnenkomt op de juiste servers in de groepen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Het netwerk instellen
> * Een toepassings gateway met een URL-toewijzing maken
> * Schaalsets voor virtuele machines maken met de back-endpools

![Voorbeeld van URL-routering](./media/application-gateway-create-url-route-cli/scenario.png)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze Quickstart gebruikmaken van Azure CLI versie 2.0.4 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep met [az group create](/cli/azure/group).

In het volgende voorbeeld wordt de resourcegroep *myResourceGroupAG* gemaakt op de locatie *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken 

Maak het virtuele netwerk *myVNet* en het subnet *myAGSubnet* met [az network vnet create](/cli/azure/network/vnet). Vervolgens kunt u het subnet *myBackendSubnet*, dat voor de back-endservers vereist is, toevoegen met [az network vnet subnet create](/cli/azure/network/vnet/subnet). Maak het openbare IP-adres*myAGPublicIPAddress* met [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway-with-url-map"></a>Toepassingsgateway met URL-toewijzing maken

U kunt [az network application-gateway create](/cli/azure/network/application-gateway) gebruiken om de toepassingsgateway *myAppGateway* te maken. Als u met de Azure CLI een toepassingsgateway maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. De toepassingsgateway wordt toegewezen aan *myAGSubnet* en *myAGPublicIPAddress*, die u eerder hebt gemaakt. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Het kan enkele minuten duren voordat de toepassingsgateway is gemaakt. Nadat de toepassingsgateway is gemaakt, kunt u de volgende nieuwe functies ervan zien:

- *appGatewayBackendPool*: een toepassingsgateway moet ten minste één back-endadresgroep hebben.
- *appGatewayBackendHttpSettings*: hiermee wordt aangegeven dat voor de communicatie poort 80 en een HTTP-protocol worden gebruikt.
- *appGatewayHttpListener*: de standaard-listener die aan *appGatewayBackendPool* is gekoppeld.
- *appGatewayFrontendIP*: hiermee wordt *myAGPublicIPAddress* aan *appGatewayHttpListener* toegewezen.
- *rule1*: de standaardrouteringsregel die aan *appGatewayHttpListener* is gekoppeld.


### <a name="add-image-and-video-backend-pools-and-port"></a>Back-endpools en -poort voor afbeeldingen en video toevoegen

U kunt back-endservers met de naam *imagesBackendPool* en *videoBackendPool* toevoegen aan uw toepassings gateway met behulp van [AZ Network Application-Gateway address-pool Create](/cli/azure/network/application-gateway/address-pool#az-network-application-gateway-address-pool-create). U voegt de front-endpoorten voor de pools toe met [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port#az-network-application-gateway-frontend-port-create). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool
az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>Back-endlistener toevoegen

Voeg back-endlistener *backendListener*, die voor het omleiden van verkeer nodig zijn, toe met [az network application-gateway http-listener create](/cli/azure/network/application-gateway).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Toewijzing van URL-pad toevoegen

URL-padtoewijzingen zorgen ervoor dat specifieke URL's naar specifieke back-endpools worden omgeleid. U kunt de URL-padtoewijzingen *imagePathRule* en *videoPathRule* maken met [az network application-gateway url-path-map create](/cli/azure/network/application-gateway) en [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule
az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>Routeringsregel toevoegen

De routeringsregel koppelt de URL-toewijzingen aan de listener die u hebt gemaakt. U kunt de regel met de naam *firewallregel2* toevoegen met [AZ Network Application-Gateway Rule Create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Virtuele-machineschaalset maken

In dit voorbeeld maakt u drie virtuele-machineschaalsets die ondersteuning bieden voor de drie back-endpools die u hebt gemaakt. De schaalsets die u maakt, hebben de namen *myvmss1*, *myvmss2* en *myvmss3*. Elke schaalset bevat twee exemplaren van virtuele machines waarop u NGINX installeert.

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>NGINX installeren

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Gebruik [az network public-ip show](/cli/azure/network/public-ip) om het openbare IP-adres van de toepassingsgateway op te halen. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Zoals, `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm`of `http://40.121.222.19:8080/video/test.htm`.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Basis-URL testen in de toepassingsgateway](./media/application-gateway-create-url-route-cli/application-gateway-nginx.png)

Wijzig de URL in `http://<ip-address>:8080/video/test.html` naar het einde van de basis-URL en u ziet iets als in het volgende voor beeld:

![Afbeeldingen-URL in toepassingsgateway testen](./media/application-gateway-create-url-route-cli/application-gateway-nginx-images.png)

Wijzig de URL in `http://<ip-address>:8080/video/test.html` en u ziet iets als in het volgende voor beeld.

![Video-URL testen in de toepassingsgateway](./media/application-gateway-create-url-route-cli/application-gateway-nginx-video.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Het netwerk instellen
> * Een toepassings gateway met een URL-toewijzing maken
> * Schaalsets voor virtuele machines maken met de back-endpools

Als u meer wilt weten over toepassings gateways en de bijbehorende resources, gaat u door naar de artikelen met procedures.
