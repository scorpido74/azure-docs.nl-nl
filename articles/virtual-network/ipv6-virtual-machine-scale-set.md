---
title: Schaal sets voor virtuele machines implementeren met IPv6 in azure
titlesuffix: Azure Virtual Network
description: In dit artikel wordt beschreven hoe u schaal sets voor virtuele machines met IPv6 kunt implementeren in een virtueel Azure-netwerk.
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
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164989"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>Schaal sets voor virtuele machines implementeren met IPv6 in azure (preview)

In dit artikel wordt beschreven hoe u een virtuele-machine Schaalset met dubbele stack (IPv4 + IPv6) implementeert met een externe load balancer met dubbele stack in een virtueel Azure-netwerk. Het proces voor het maken van een schaalset voor virtuele machines die met IPv6 kan worden uitgevoerd, is bijna identiek aan het proces voor het maken van afzonderlijke Vm's die [hier](ipv6-configure-standard-load-balancer-template-json.md)worden beschreven. U begint met de stappen die vergelijkbaar zijn met die van afzonderlijke Vm's:
1.  Open bare IPv4-en IPv6-Ip's maken.
2.  Maak een dual stack-load balancer.  
3.  Maak regels voor netwerk beveiligings groepen (NSG).  

De enige stap die verschilt van afzonderlijke Vm's is het maken van de netwerk interface configuratie (NIC) die gebruikmaakt van de virtuele-machine Scale set resource: networkProfile/Networkinterfaceconfigurations schaalset. De JSON-structuur is vergelijkbaar met die van het object micro soft. Network/networkInterfaces dat wordt gebruikt voor afzonderlijke Vm's, waarbij het instellen van de NIC en de IPv4 IpConfiguration als primaire interface wordt gebruikt, zoals wordt weer gegeven **in de** volgende voor beeld:

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


## <a name="sample-virtual-machine-scale-set-template-json"></a>Voor beeld van een JSON-sjabloon voor virtuele-machine schaal sets

Voor het implementeren van een virtuele-machine Schaalset met dubbele stack (IPv4 + IPv6) met dubbele stack externe Load Balancer en de [voorbeeld sjabloon virtuele](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)netwerk weergave.
## <a name="next-steps"></a>Volgende stappen

Zie [Wat is IPv6 voor azure Virtual Network?](ipv6-overview.md)voor meer informatie over IPv6-ondersteuning in azure Virtual Networks.
