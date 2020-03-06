---
title: 'Quick Start: direct webverkeer via CLI'
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van de Azure CLI om een Azure-toepassing gateway te maken waarmee webverkeer wordt doorgestuurd naar virtuele machines in een back-end-groep.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 5512e44ab52a3c3d957bbc0d0a07a7a1e7b6f50e
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399577"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Snelstart: webverkeer omleiden met Azure Application Gateway - Azure CLI

In deze Quick Start gebruikt u Azure CLI om een toepassings gateway te maken. Vervolgens test u de app om te controleren of deze correct werkt. 

De toepassings gateway stuurt webverkeer van toepassingen naar specifieke bronnen in een back-end-pool. U wijst listeners toe aan poorten, maakt regels en voegt resources toe aan een back-end-groep. Voor het gemak maakt dit artikel gebruik van een eenvoudige configuratie met een openbaar front-end-IP, een basis-listener voor het hosten van één site op de toepassings gateway, een basis regel voor aanvraag Routering en twee virtuele machines in de back-end-pool.

U kunt deze Snelstartgids ook volt ooien met behulp van [Azure PowerShell](quick-create-powershell.md) of de [Azure Portal](quick-create-portal.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure CLI-versie 2.0.4 of hoger](/cli/azure/install-azure-cli) (als u Azure cli lokaal uitvoert).

## <a name="create-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Maak een resource groep met behulp van `az group create`. 

In het volgende voorbeeld wordt de resourcegroep *myResourceGroupAG* gemaakt op de locatie *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken 

Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt.  Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.  U kunt een nieuw subnet maken voor Application Gateway of een bestaande gebruiken. In dit voor beeld maakt u twee subnetten: één voor de toepassings gateway en een andere voor de back-endservers. U kunt het frontend-IP-adres van de Application Gateway zo configureren dat het openbaar of privé is volgens uw use-case. In dit voor beeld kiest u een openbaar frontend-IP-adres.

Gebruik `az network vnet create`om het virtuele netwerk en subnet te maken. Voer `az network public-ip create` uit om het open bare IP-adres te maken.

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

## <a name="create-the-backend-servers"></a>De back-endservers maken

Een back-end kan Nic's, virtuele-machine schaal sets, open bare Ip's, interne Ip's, FQDN-namen (Fully Qualified Domain names) en multi tenant back-ends hebben als Azure App Service. In dit voor beeld maakt u twee virtuele machines die moeten worden gebruikt als back-endservers voor de toepassings gateway. U installeert ook IIS op de virtuele machines om de toepassings gateway te testen.

#### <a name="create-two-virtual-machines"></a>Twee virtuele machines maken

Installeer de NGINX-webserver op de virtuele machines om te controleren of de toepassings gateway is gemaakt. U kunt een cloud-init-configuratiebestand maken om NGINX te installeren en een 'Hallo wereld' Node.js-app uit te voeren op een virtuele Linux-machine. Zie [Cloud-init-ondersteuning voor virtuele machines in Azure](../virtual-machines/linux/using-cloud-init.md) voor meer informatie over cloud-init.

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

Maak de netwerk interfaces met `az network nic create`. Als u de virtuele machines wilt maken, gebruikt u `az vm create`.

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

Maak een toepassings gateway met behulp van `az network application-gateway create`. Als u met de Azure CLI een toepassingsgateway maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. Azure voegt dan de privé-IP-adressen van de netwerkinterfaces toe als servers in de back-endpool van de toepassingsgateway.

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

## <a name="test-the-application-gateway"></a>Toepassingsgateway testen

Het is in Azure niet nodig een NGINX-webserver te installeren om de toepassingsgateway te maken, maar u hebt de server in deze quickstart geïnstalleerd om te controleren of het maken van de toepassingsgateway in Azure is geslaagd. Gebruik `az network public-ip show`om het open bare IP-adres van de nieuwe toepassings gateway op te halen. 

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

Wanneer u de resources die u hebt gemaakt met de Application Gateway niet meer nodig hebt, gebruikt u de opdracht `az group delete` om de resource groep te verwijderen. Wanneer u de resource groep verwijdert, verwijdert u ook de toepassings gateway en alle gerelateerde resources.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)

