---
title: Omleiding extern verkeer met CLI - Azure Application Gateway
description: Meer informatie over het maken van een toepassingsgateway die intern webverkeer doorverwijst naar de juiste groep met behulp van de Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: fc955b4959bb20628463f7699a0b66ec2b89a393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74011596"
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Een toepassingsgateway maken met externe omleiding met de Azure CLI

U de Azure CLI gebruiken om [de omleiding van webverkeer](multiple-site-overview.md) te configureren wanneer u een [toepassingsgateway maakt.](overview.md) In deze zelfstudie configureert u een listener en regel die webverkeer dat bij de toepassingsgateway naar een externe site wordt geleverd, omleidt.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Netwerk instellen
> * Een listener- en omleidingsregel maken
> * Een toepassingsgateway maken

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

Maak het virtuele netwerk *myVNet* en het subnet *myAGSubnet* met [az network vnet create](/cli/azure/network/vnet). Maak het openbare IP-adres *myAGPublicIPAddress* met [az network public-ip create](/cli/azure/network/public-ip). Deze resources worden gebruikt om de netwerkverbinding naar de toepassingsgateway en de bijbehorende bronnen te leveren.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

U kunt [az network application-gateway create](/cli/azure/network/application-gateway) gebruiken om de toepassingsgateway *myAppGateway* te maken. Als u een toepassingsgateway met de Azure CLI maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. De toepassingsgateway wordt toegewezen aan *myAGSubnet* en *myPublicIPAddress*, die u zojuist hebt gemaakt. 

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
  --frontend-port 8080 \
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

### <a name="add-the-redirection-configuration"></a>De omleidingsconfiguratie toevoegen

Voeg de omleidingsconfiguratie toe die verkeer van *www\.consoto.org* naar de luisteraar stuurt voor *www\.contoso.com* naar de toepassingsgateway met behulp van [az-netwerk-applicatie-gateway redirect-config maken.](/cli/azure/network/application-gateway/redirect-config)

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "https://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Een listener- en routeringsregel toevoegen

Een listener is vereist om de toepassingsgateway in te schakelen om het verkeer op de juiste manier te routeren. Maak de listener met [behulp van az-netwerk applicatie-gateway http-listener maken](/cli/azure/network/application-gateway) met de frontend poort gemaakt met az network [application-gateway frontend-port maken](/cli/azure/network/application-gateway). De luisteraar moet weten waar hij binnenkomend verkeer moet verzenden. Maak een basisregel met de naam *redirectRule* met behulp van [az-netwerk toepassings-gateway regel maken](/cli/azure/network/application-gateway).

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Gebruik [az network public-ip show](/cli/azure/network/public-ip) om het openbare IP-adres van de toepassingsgateway op te halen. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.

U ziet bing.com *in* uw browser worden weergegeven.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> * Netwerk instellen
> * Een listener- en omleidingsregel maken
> * Een toepassingsgateway maken
