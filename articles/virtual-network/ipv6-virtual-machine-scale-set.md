---
title: Virtuele machineschaalsets implementeren met IPv6 in Azure
titlesuffix: Azure Virtual Network
description: In dit artikel ziet u hoe u virtuele machineschaalsets implementeert met IPv6 in een virtueel Azure-netwerk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: kumud
ms.openlocfilehash: b90910614bcd86a54198b1a0961a3378427ea87e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73164989"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>Virtuele machineschaalsets implementeren met IPv6 in Azure (Voorbeeld)

In dit artikel ziet u hoe u een dual stack (IPv4 + IPv6) Virtual Machine Scale Set implementeert met een externe load balancer met twee stapels in een virtueel Azure-netwerk. Het proces om een IPv6-geschikte virtuele machine schaal set te maken is bijna identiek aan het proces voor het maken van individuele VM's [hier](ipv6-configure-standard-load-balancer-template-json.md)beschreven. U begint met de stappen die vergelijkbaar zijn met de stappen die zijn beschreven voor afzonderlijke VM's:
1.  IPv4- en IPv6-ip's maken.
2.  Maak een dual stack load balancer.  
3.  NSG-regels (Network Security Group) maken.  

De enige stap die verschilt van afzonderlijke VM's is het maken van de NIC-configuratie (network interface) die gebruikmaakt van de bron voor de virtuele machineschaalset: networkProfile/networkInterfaceConfigurations. De JSON-structuur is vergelijkbaar met die van het object Microsoft.Network/networkInterfaces dat wordt gebruikt voor afzonderlijke VM's met de toevoeging van het instellen van de NIC en de IPv4 IpConfiguration als primaire interface met behulp van het **kenmerk "primair": true** attribute zoals te zien in het volgende voorbeeld:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Voorbeeld van sjabloon JSON voor virtuele machineschaalset

Een dual stack (IPv4 + IPv6) Virtual Machine Scale Set implementeren met dual stack externe Load Balancer en virtuele netwerkweergave sample template [hier.](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)
## <a name="next-steps"></a>Volgende stappen

Zie [Wat is IPv6 voor Azure Virtual Network?](ipv6-overview.md).
