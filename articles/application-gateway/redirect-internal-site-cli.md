---
title: Interne omleiding met CLI
titleSuffix: Azure Application Gateway
description: Meer informatie over het maken van een toepassingsgateway die intern webverkeer doorverwijst naar de juiste groep met behulp van de Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 7d37e36a4cdfed462904e2d02871345ad89d7ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74074557"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Een toepassingsgateway maken met interne omleiding met azure cli

U de Azure CLI gebruiken om [de omleiding van webverkeer](multiple-site-overview.md) te configureren wanneer u een [toepassingsgateway maakt.](overview.md) In deze zelfstudie definieert u een backendpool met behulp van een virtuele machineschaalset. Vervolgens configureert u luisteraars en regels op basis van domeinen die u bezit om ervoor te zorgen dat webverkeer bij de juiste groep terechtkomt. Deze zelfstudie gaat ervan uit dat u meerdere domeinen bezit en gebruikt voorbeelden van *www\.contoso.com* en *www\.contoso.org*.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Netwerk instellen
> * Een toepassingsgateway maken
> * Listeners en omleidingsregel toevoegen
> * Een virtuele machineschaalset maken met de backendpool
> * Een CNAME-record in uw domein maken

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze Quickstart gebruikmaken van Azure CLI versie 2.0.4 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep met [az group create](/cli/azure/group).

In het volgende voorbeeld wordt de resourcegroep *myResourceGroupAG* gemaakt op de locatie *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken 

Maak het virtuele netwerk *myVNet* en het subnet *myAGSubnet* met [az network vnet create](/cli/azure/network/vnet). U vervolgens het subnet met de naam *myBackendSubnet* toevoegen dat nodig is door de backendpool van servers met behulp van [het az-netwerk vnet subnet maken.](/cli/azure/network/vnet/subnet) Maak het openbare IP-adres *myAGPublicIPAddress* met [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create).

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

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

U kunt [az network application-gateway create](/cli/azure/network/application-gateway) gebruiken om de toepassingsgateway *myAppGateway* te maken. Als u een toepassingsgateway met de Azure CLI maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. De toepassingsgateway wordt toegewezen aan *myAGSubnet* en *myAGPublicIPAddress*, die u eerder hebt gemaakt. 

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
- *rule1* - De standaardrouteringsregel die aan *appGatewayHttpListener* is gekoppeld.


## <a name="add-listeners-and-rules"></a>Listeners en regels toevoegen 

Een listener is vereist om de toepassingsgateway in te schakelen om het verkeer op de juiste manier naar de back-endpool door te sturen. In deze zelfstudie maakt u twee listeners voor de twee domeinen. In dit voorbeeld worden luisteraars gemaakt voor de domeinen *www\.contoso.com* en *www\.contoso.org*.

Voeg de back-endlisteners, die voor het omleiden van verkeer nodig zijn, toe met [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>De omleidingsconfiguratie toevoegen

Voeg de omleidingsconfiguratie toe die verkeer van *www\.consoto.org* naar de luisteraar verzendt voor *www\.contoso.com* in de toepassingsgateway met behulp van [az-netwerk-applicatie-gateway redirect-config maken.](/cli/azure/network/application-gateway/redirect-config#az-network-application-gateway-redirect-config-create)

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Routeringsregels toevoegen

Regels worden verwerkt in de volgorde waarin ze zijn gemaakt en het verkeer wordt geleid met behulp van de eerste regel die overeenkomt met de URL die naar de toepassingsgateway wordt verzonden. Als u bijvoorbeeld een regel hebt die van een basislistener gebruikmaakt en een regel die via dezelfde poort van een listener voor meerdere sites gebruikmaakt, moet de regel voor de listener voor meerdere sites vermeld worden vóór de regel met de basislistener, opdat de regel voor meerdere sites kan functioneren zoals het hoort. 

In dit voorbeeld maakt u twee nieuwe regels en verwijdert u de standaardregel die is gemaakt.  U kunt de regel toevoegen met [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Schaalsets voor virtuele machines maken

In dit voorbeeld maakt u een virtuele machineschaalset die de backendpool ondersteunt die u hebt gemaakt. De schaalset die u maakt, heet *myvmss* en bevat twee virtuele machine-exemplaren waarop u NGINX installeert.

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>NGINX installeren

Voer deze opdracht uit in het shell-venster:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>CNAME-record in uw domein maken

Als de toepassingsgateway met het bijbehorende openbare IP-adres is gemaakt, kunt u het DNS-adres ophalen en dit gebruiken om een CNAME-record in uw domein te maken. Gebruik [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) om het DNS-adres van de toepassingsgateway op te halen. Kopieer de waarde *fqdn* van DNSSettings en gebruik deze als de waarde van de CNAME-record die u maakt. Het gebruik van A-records wordt niet aanbevolen, omdat de VIP kan veranderen wanneer de toepassingsgateway opnieuw wordt gestart.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Voer uw domeinnaam in de adresbalk van de browser in. Zoals, http:\//www.contoso.com.

![Contoso-site testen in toepassingsgateway](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Wijzig het adres naar uw andere\/domein, bijvoorbeeld http: /www.contoso.org en u moet zien\.dat het verkeer is doorgestuurd naar de luisteraar voor www contoso.com.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> * Netwerk instellen
> * Een toepassingsgateway maken
> * Listeners en omleidingsregel toevoegen
> * Een virtuele machineschaalset maken met de backendpool
> * Een CNAME-record in uw domein maken
