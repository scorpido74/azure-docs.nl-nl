---
title: Netwerkbeleid uitschakelen voor privéeindpunten in Azure
description: Meer informatie over het uitschakelen van netwerkbeleid voor privéeindpunten.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453020"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Netwerkbeleid voor privéeindpunten uitschakelen

Netwerkbeleid zoals netwerkbeveiligingsgroepen (NSG) wordt niet ondersteund voor privéeindpunten. Om een privéeindpunt op een bepaald subnet te implementeren, is een expliciete instelling voor uitschakelen vereist op dat subnet. Deze instelling is alleen van toepassing op het privéeindpunt. Voor andere bronnen in het subnet wordt de toegang beheerd op basis van de definitie van de beveiligingsregels (Network Security Groups) (NSG). 
 
Wanneer u de portal gebruikt om een privéeindpunt te maken, wordt deze instelling automatisch uitgeschakeld als onderdeel van het maakproces. Implementatie met andere clients vereist een extra stap om deze instelling te wijzigen. U de instelling uitschakelen met behulp van cloudshell vanuit de Azure-portal of lokale installaties van Azure PowerShell, Azure CLI of Azure Resource Manager-sjablonen gebruiken.  
 
In de volgende voorbeelden `PrivateEndpointNetworkPolicies` wordt beschreven hoe u een virtueel netwerk met de naam *myVirtualNetwork* uitschakelen met een *standaardsubnet* dat wordt gehost in een resourcegroep met de naam *myResourceGroup.*

## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
In deze sectie wordt beschreven hoe subnet privéeindpuntbeleid wordt uitgeschakeld met Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI gebruiken
In deze sectie wordt beschreven hoe subnet privéeindpuntbeleid wordt uitgeschakeld met Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [privéeindpunt voor Azure](private-endpoint-overview.md)
 
