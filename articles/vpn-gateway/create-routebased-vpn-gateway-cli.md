---
title: 'Een op route gebaseerde Azure VPN-gateway maken: CLI'
description: Leer snel hoe u een VPN-gateway maakt met CLI
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: 121790fce220874babedf67cd72471caa7e92ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241092"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Een op route gebaseerde VPN-gateway maken met CLI

Met dit artikel u snel een op route gebaseerde Azure VPN-gateway maken met behulp van de Azure CLI. Een VPN-gateway wordt gebruikt bij het maken van een VPN-verbinding met uw on-premises netwerk. U ook een VPN-gateway gebruiken om VNets met elkaar te verbinden.

De stappen in dit artikel maken een VNet, een subnet, een gateway subnet en een route-gebaseerde VPN-gateway (virtual network gateway). Het maken van een virtuele netwerkgateway kan 45 minuten of langer duren. Zodra de gatewaycreatie is voltooid, u verbindingen maken. Voor deze stappen is een Azure-abonnement vereist. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0.4 of hoger. Voer `az --version` uit om te zien welke versie is geïnstalleerd. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [az group create](/cli/azure/group). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met de [opdracht vnet create van het AZ-netwerk.](/cli/azure/network/vnet) In het volgende voorbeeld wordt een virtueel netwerk met de naam **VNet1** op de **EastUS-locatie** ge:

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Een gatewaysubnet toevoegen

Het gatewaysubnet bevat de gereserveerde IP-adressen die de virtuele netwerkgatewayservices gebruiken. Gebruik de volgende voorbeelden om een gateway-subnet toe te voegen:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Een openbaar IP-adres aanvragen

Een VPN-gateway moet een dynamisch toegewezen openbaar IP-adres hebben. Het openbare IP-adres wordt toegewezen aan de VPN-gateway die u maakt voor uw virtuele netwerk. Gebruik het volgende voorbeeld om een openbaar IP-adres aan te vragen:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>De VPN-gateway maken

Maak de VPN Gateway met behulp van de opdracht [az network vnet-gateway create](/cli/azure/group).

Als u deze opdracht `--no-wait` uitvoert met behulp van de parameter, ziet u geen feedback of uitvoer. Met `--no-wait` de parameter kan de gateway op de achtergrond worden gemaakt. Het betekent niet dat de VPN-gateway onmiddellijk wordt gemaakt.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

Het maken van een VPN-gateway kan tot 45 minuten of langer duren.

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Bekijk de VPN-gateway

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Het antwoord lijkt op deze:

```output
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>Het openbare IP-adres weergeven

Als u het openbare IP-adres wilt weergeven dat aan uw gateway is toegewezen, gebruikt u het volgende voorbeeld:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

De waarde die aan het **ipAddress-veld** is gekoppeld, is het openbare IP-adres van uw VPN-gateway.

Voorbeeld van een reactie:

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt niet meer nodig hebt, gebruikt u [de naam AZ-groep](/cli/azure/group) om de brongroep te verwijderen. Hiermee verwijdert u de resourcegroep en alle resources die deze bevat.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Volgende stappen

Zodra de gateway is voltooid, u een verbinding maken tussen uw virtuele netwerk en een andere VNet. Of maak een verbinding tussen uw virtuele netwerk en een on-premises locatie.

> [!div class="nextstepaction"]
> [Een site-naar-site-verbinding maken](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Een punt-naar-site-verbinding maken](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Een verbinding maken met een andere VNet](vpn-gateway-vnet-vnet-rm-ps.md)
