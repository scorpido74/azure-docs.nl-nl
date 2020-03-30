---
title: 'Snelstart: Direct webverkeer met CLI'
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van de Azure CLI om een Azure Application Gateway te maken die webverkeer naar virtuele machines in een backendpool leidt.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f60b26756c0affffbd45c8596fdf73d11ffa8e81
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239514"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Snelstart: webverkeer omleiden met Azure Application Gateway - Azure CLI

In deze quickstart gebruikt u Azure CLI om een toepassingsgateway te maken. Dan test je het om ervoor te zorgen dat het goed werkt. 

De toepassingsgateway leidt het webverkeer van toepassingen naar specifieke bronnen in een backend-groep. U wijst luisteraars toe aan poorten, maakt regels en voegt resources toe aan een backendpool. Omwille van de eenvoud, dit artikel maakt gebruik van een eenvoudige setup met een openbare front-end IP, een fundamentele luisteraar om een enkele site host op de applicatie gateway, een basisaanvraag routing regel, en twee virtuele machines in de backend pool.

U deze quickstart ook voltooien met [Azure PowerShell](quick-create-powershell.md) of de [Azure-portal.](quick-create-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Azure CLI-versie 2.0.4 of hoger](/cli/azure/install-azure-cli) (als u Azure CLI lokaal uitvoert).

## <a name="create-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Een resourcegroep maken `az group create`met behulp van . 

In het volgende voorbeeld wordt de resourcegroep *myResourceGroupAG* gemaakt op de locatie *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken 

Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt.  Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.  U een nieuw subnet voor application gateway maken of een bestaand subnet gebruiken. In dit voorbeeld maakt u twee subnetten: een voor de toepassingsgateway en een voor de backendservers. U het Frontend IP van de Application Gateway configureren als openbaar of privé volgens uw use case. In dit voorbeeld kiest u een IP-adres van Frontend.

Als u het virtuele netwerk `az network vnet create`en subnet wilt maken, gebruikt u . Voer `az network public-ip create` uit om het openbare IP-adres te maken.

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

## <a name="create-the-backend-servers"></a>De backendservers maken

Een backend kan nics, virtuele machineschaalsets, openbare IP's, interne IP's, volledig gekwalificeerde domeinnamen (FQDN) en back-ends met meerdere tenant's hebben, zoals Azure App Service. In dit voorbeeld maakt u twee virtuele machines om te gebruiken als backendservers voor de toepassingsgateway. U installeert ook IIS op de virtuele machines om de toepassingsgateway te testen.

#### <a name="create-two-virtual-machines"></a>Twee virtuele machines maken

U installeert ook de NGINX-webserver op de virtuele machines om te controleren of de toepassingsgateway is gemaakt. U kunt een cloud-init-configuratiebestand maken om NGINX te installeren en een 'Hallo wereld' Node.js-app uit te voeren op een virtuele Linux-machine. Zie [Cloud-init-ondersteuning voor virtuele machines in Azure](../virtual-machines/linux/using-cloud-init.md) voor meer informatie over cloud-init.

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

Maak de netwerkinterfaces met `az network nic create`. Om de virtuele machines `az vm create`te maken, gebruikt u .

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

Een toepassingsgateway `az network application-gateway create`maken met behulp van . Als u met de Azure CLI een toepassingsgateway maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. Azure voegt dan de privé-IP-adressen van de netwerkinterfaces toe als servers in de back-endpool van de toepassingsgateway.

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

- **appGatewayBackendPool**: Bevindt zich op de pagina **Backend-pools.** Hier is te zien wat de vereiste back-endpool is.
- **appGatewayBackendHttpInstellingen:** Op de **pagina HTTP-instellingen.** Hier wordt aangegeven dat de toepassingsgateway voor communicatie gebruikmaakt van poort 80 en het HTTP-protocol.
- **appGatewayHttpListener:** Bevindt zich op de **pagina Luisteraars**. Hier staat wat de standaard-listener is die aan **appGatewayBackendPool** is gekoppeld.
- **appGatewayFrontendIP**: Bevindt zich op de pagina **Frontend IP-configuraties.** Hiermee wordt *myAGPublicIPAddress* aan **appGatewayHttpListener toegedeeld.**
- **regel1**: Gelegen op de pagina **Regels.** Hier wordt aangegeven welke standaardrouteringsregel aan **appGatewayHttpListener** is gekoppeld.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Het is in Azure niet nodig een NGINX-webserver te installeren om de toepassingsgateway te maken, maar u hebt de server in deze quickstart geïnstalleerd om te controleren of het maken van de toepassingsgateway in Azure is geslaagd. Als u het openbare IP-adres van `az network public-ip show`de nieuwe toepassingsgateway wilt krijgen, gebruikt u . 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.
    
![Toepassingsgateway testen](./media/quick-create-cli/application-gateway-nginxtest.png)

Als u de browser vernieuwt, ziet u de naam van de tweede VM. Dit geeft aan dat de toepassingsgateway is gemaakt en verbinding kan maken met de backend.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u met de toepassingsgateway hebt gemaakt, niet meer nodig hebt, gebruikt u de opdracht om de `az group delete` brongroep te verwijderen. Wanneer u de brongroep verwijdert, verwijdert u ook de toepassingsgateway en alle bijbehorende bronnen.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)

