---
title: Een IPv6 dual stack-toepassing implementeren met Basic Load Balancer in het virtuele Azure-netwerk - Resource Manger-sjabloon (voorbeeld)
titlesuffix: Azure Virtual Network
description: In dit artikel ziet u hoe u een IPv6-toepassing met twee stapelen in het virtuele Azure-netwerk implementeert met VM-sjablonen voor Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/26/2019
ms.author: kumud
ms.openlocfilehash: b397c874045a89f5992aeadacfbbd4434a486977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70012825"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template-preview"></a>Een IPv6-toepassing met dual stack implementeren met Basic Load Balancer in Azure - Sjabloon (voorbeeld)

In dit artikel vindt u een lijst met IPv6-configuratietaken met het gedeelte van de VM-sjabloon Azure Resource Manager dat van toepassing is op. Gebruik de in dit artikel beschreven sjabloon om een dual stack (IPv4 + IPv6)-toepassing te implementeren met Basic Load Balancer met een dual stack virtueel netwerk met IPv4- en IPv6-subnetten, een Basic Load Balancer met dubbele (IPv4 + IPv6) front-endconfiguraties, VM's met NIC's met een dubbele IP-configuratie, netwerkbeveiligingsgroep en openbare IP's.

Zie [Een IPv6 dual stack-toepassing implementeren met Standaardload Balancer - Sjabloon](ipv6-configure-standard-load-balancer-template-json.md)als u een toepassing voor dual stack (IPV4 + IPv6) wilt implementeren met Standard Load Balancer.

## <a name="required-configurations"></a>Vereiste configuraties

Zoek naar de sjabloonsecties in de sjabloon om te zien waar ze moeten plaatsvinden.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6-adresSpace voor het virtuele netwerk

Sjabloonsectie om toe te voegen:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6-subnet binnen het virtuele netwerkadres IPv6Space

Sjabloonsectie om toe te voegen:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>IPv6-configuratie voor de NIC

Sjabloonsectie om toe te voegen:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>NSG-regels (IPv6-netwerkbeveiligingsgroep)

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Voorwaardelijke configuratie

Als u een virtueel netwerktoestel gebruikt, voegt u IPv6-routes toe in de routetabel. Anders is deze configuratie optioneel.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Optionele configuratie

### <a name="ipv6-internet-access-for-the-virtual-network"></a>IPv6-internettoegang voor het virtuele netwerk

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6-openbare IP-adressen

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 Front-end voor Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6 Back-end-adresgroep voor Load Balancer

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6-load balancerregels voor het koppelen van binnenkomende en uitgaande poorten

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Voorbeeld VM-sjabloon JSON
Als u een IPv6 dual stack-toepassing wilt implementeren met Basic Load Balancer in het virtuele Azure-netwerk met Azure Resource Manager-sjabloon, u hier voorbeeldsjabloon [weergeven.](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)

## <a name="next-steps"></a>Volgende stappen

U vindt hier meer informatie over de prijzen voor [openbare IP-adressen,](https://azure.microsoft.com/pricing/details/ip-addresses/) [netwerkbandbreedte](https://azure.microsoft.com/pricing/details/bandwidth/)of [Load Balancer.](https://azure.microsoft.com/pricing/details/load-balancer/)
