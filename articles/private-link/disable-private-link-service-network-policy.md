---
title: 'Netwerkbeleid uitschakelen voor IP-adres azure Private Link-servicebron '
description: Meer informatie over het uitschakelen van netwerkbeleid voor Azure private Link
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452995"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Netwerkbeleid uitschakelen voor IP-adres van private link-servicebron

Om een bron-IP-adres voor uw Private Link-service te kiezen, `privateLinkServiceNetworkPolicies` is een expliciete instelling voor uitschakelen vereist op het subnet. Deze instelling is alleen van toepassing op het specifieke privé-IP-adres dat u hebt gekozen als bron-IP van de Private Link-service. Voor andere bronnen in het subnet wordt de toegang beheerd op basis van de definitie van de beveiligingsregels (Network Security Groups) (NSG). 
 
Bij het gebruik van een Azure-client (PowerShell, CLI of sjablonen) is een extra stap vereist om deze eigenschap te wijzigen. U het beleid uitschakelen met behulp van de cloudshell vanuit de Azure-portal of lokale installaties van Azure PowerShell, Azure CLI of Azure Resource Manager-sjablonen gebruiken.  
 
Volg de onderstaande stappen om het netwerkbeleid voor privékoppelingenvoor een virtueel netwerk met de naam *myVirtualNetwork* uit te schakelen met een *standaardsubnet* dat wordt gehost in een brongroep met de naam *myResourceGroup.* 

## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
In deze sectie wordt beschreven hoe subnet privéeindpuntbeleid wordt uitgeschakeld met Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI gebruiken
In deze sectie wordt beschreven hoe subnet privéeindpuntbeleid wordt uitgeschakeld met Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Een sjabloon gebruiken
In deze sectie wordt beschreven hoe subnet privéeindpuntbeleid wordt uitgeschakeld met Azure Resource Manager-sjabloon.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Private Endpoint](private-endpoint-overview.md)
 
