---
title: 'Quick Start: direct webverkeer via CLI'
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van de Azure CLI om een Azure-toepassing gateway te maken waarmee webverkeer wordt doorgestuurd naar virtuele machines in een back-end-groep.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: de1f30d949509ab48999d0080ccc4df74321ce01
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075205"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Snelstart: webverkeer omleiden met Azure Application Gateway - Azure CLI

In deze Quick start ziet u hoe u Azure CLI gebruikt om een toepassings gateway te maken.  Nadat u de toepassings gateway hebt gemaakt, test u deze om er zeker van te zijn dat deze correct werkt. Met Azure-toepassing gateway stuurt u het webverkeer van uw toepassing naar specifieke bronnen door listeners toe te wijzen aan poorten, regels te maken en resources toe te voegen aan een back-end-groep. In dit artikel wordt gebruikgemaakt van een eenvoudige configuratie met een openbaar front-end-IP, een basis-listener voor het hosten van één site op de toepassings gateway, twee virtuele machines die worden gebruikt voor de back-end-pool en een regel voor basis routering van aanvragen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

### <a name="azure-cli"></a>Azure-CLI

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u Azure CLI versie 2.0.4 of hoger nodig. Voer **az --version** uit om de versie te zoeken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) voor meer informatie over installeren en upgraden.

### <a name="resource-group"></a>Resourcegroep

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). 

In het volgende voorbeeld wordt de resourcegroep *myResourceGroupAG* gemaakt op de locatie *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Vereiste netwerk bronnen 

Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt.  Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.  U kunt een nieuw subnet maken voor Application Gateway of een bestaande gebruiken. In dit voor beeld maakt u twee subnetten in dit voor beeld: een voor de toepassings gateway en een voor de back-endservers. U kunt het frontend-IP-adres van de Application Gateway zo configureren dat het openbaar of privé is volgens uw use-case. In dit voor beeld kiest u een openbaar frontend-IP.

Maak het virtuele netwerk en het subnet met de opdracht [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Voer [az network public-ip create](/cli/azure/network/public-ip) uit om het openbare IP-adres te maken.

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

### <a name="backend-servers"></a>Back-endservers

Een back-end kan Nic's, virtuele-machine schaal sets, open bare Ip's, interne Ip's, FQDN-namen (Fully Qualified Domain names) en multi tenant back-ends hebben als Azure App Service. In dit voor beeld maakt u twee virtuele machines die moeten worden gebruikt als back-endservers voor de toepassings gateway. U installeert ook IIS op de virtuele machines om de toepassings gateway te testen.

#### <a name="create-two-virtual-machines"></a>Twee virtuele machines maken

U installeert ook de [NGINX-webserver](https://docs.nginx.com/nginx/) op de virtuele machines om te controleren of de toepassingsgateway is gemaakt. U kunt een cloud-init-configuratiebestand maken om NGINX te installeren en een 'Hallo wereld' Node.js-app uit te voeren op een virtuele Linux-machine. Zie [Cloud-init-ondersteuning voor virtuele machines in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) voor meer informatie over cloud-init.

Kopieer in uw Azure Cloud Shell de volgende configuratie en plak deze in een bestand met de naam *cloud-init.txt*. Voer *editor cloud-init.txt* in om het bestand te maken.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Maak de netwerkinterfaces met de opdracht [az network nic create](/cli/azure/network/nic#az-network-nic-create). Voor het maken van de virtuele machines gebruikt u [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>De toepassingsgateway maken

U kunt met de opdracht [az network application-gateway create](/cli/azure/network/application-gateway) een toepassingsgateway maken. Als u met de Azure CLI een toepassingsgateway maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. Azure voegt dan de privé-IP-adressen van de netwerkinterfaces toe als servers in de back-endpool van de toepassingsgateway.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Het kan tot 30 minuten duren om de toepassingsgateway te maken in Azure. Wanneer deze is gemaakt, kunt u de volgende instellingen bekijken in het gedeelte **Instellingen** van de pagina **Toepassingsgateway**:

- **appGatewayBackendPool**: bevindt zich op de pagina met **Back-upgroepen** . Hier is te zien wat de vereiste back-endpool is.
- **appGatewayBackendHttpSettings**: bevindt zich op de pagina **http-instellingen** . Hier wordt aangegeven dat de toepassingsgateway voor communicatie gebruikmaakt van poort 80 en het HTTP-protocol.
- **appGatewayHttpListener**: bevindt zich op de **pagina listeners**. Hier staat wat de standaard-listener is die aan **appGatewayBackendPool** is gekoppeld.
- **appGatewayFrontendIP**: bevindt zich op de pagina **frontend IP-configuraties** . Hiermee wordt *myAGPublicIPAddress* aan **appGatewayHttpListener** toegewezen.
- **firewallregel1**: bevindt zich op de pagina **regels** . Hier wordt aangegeven welke standaardrouteringsregel aan **appGatewayHttpListener** is gekoppeld.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Het is in Azure niet nodig een NGINX-webserver te installeren om de toepassingsgateway te maken, maar u hebt de server in deze quickstart geïnstalleerd om te controleren of het maken van de toepassingsgateway in Azure is geslaagd. Gebruik [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) om het openbare IP-adres van de nieuwe toepassingsgateway op te halen. 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.
    
![Toepassingsgateway testen](./media/quick-create-cli/application-gateway-nginxtest.png)

Als u de browser vernieuwt, ziet u de naam van de tweede VM. Dit geeft aan dat de toepassings gateway is gemaakt en verbinding kan maken met de back-end.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de bij de toepassingsgateway gemaakte resources niet meer nodig hebt, gebruikt u de opdracht [az group delete](/cli/azure/group#az-group-delete) om de resourcegroep te verwijderen. Als u de resourcegroep verwijdert, worden ook de toepassingsgateway en alle gerelateerde resources verwijderd.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)

