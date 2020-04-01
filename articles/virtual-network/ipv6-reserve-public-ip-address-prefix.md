---
title: Openbare IPv6-adressen en adresbereiken reserveren in een virtueel Azure-netwerk
titlesuffix: Azure Virtual Network
description: Meer informatie over het reserveren van openbare IPv6-adressen en adresbereiken in een virtueel Azure-netwerk.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a0dd56842174d89688c862397c373326ef50d1f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420549"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Openbaar IPv6-adresvoorvoegsel reserveren
Met IPv6 voor Azure Virtual Network (VNet) u toepassingen in Azure hosten met IPv6- en IPv4-connectiviteit, zowel binnen een virtueel netwerk als van en naar internet. Naast het reserveren van afzonderlijke IPv6-adressen, u aaneengesloten bereiken van Azure IPv6-adressen (ip-voorvoegsel genoemd) reserveren voor uw gebruik. In deze artikelen wordt beschreven hoe u ip-adressen en adresbereiken van IPv6 maakt met Azure PowerShell en CLI.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>Eén gereserveerd ip-adres van IPv6 maken

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

U één gereserveerd (statisch) IPv6-ip-adres maken met Azure PowerShell met [Nieuw-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) als volgt:

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Azure CLI gebruiken

 U één gereserveerd (statisch) IPv6 Public IP-adres Azure CLI maken met [het openbaar-ip-netwerk van az-netwerk:](/cli/azure/network/public-ip)
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Een gereserveerd IPv6-voorvoegsel (bereik) maken

Als u een IPv6-voorvoegsel wilt reserveren, voegt u de IP-adresfamilie van IPv6 toe aan dezelfde opdracht die wordt gebruikt voor het maken van IPv4-voorvoegsels. Met de volgende opdrachten wordt een voorvoegsel gemaakt van grootte /125 ( 8 IPv6-adressen).  

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

U een openbaar IPv6-adres maken met Azure CLI met [het openbaar-ip-netwerk van het AZ-netwerk:](/powershell/module/az.network/new-azpublicipprefix)
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Azure CLI gebruiken

U als volgt een openbaar IPv6-adres maken met Azure CLI:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Een openbaar IP-adres toewijzen aan een gereserveerd IPv6-voorvoegsel

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

 U maakt een statisch Ip-ip-ip-ip-ip `-PublicIpPrefix` iPv6 op basis van een gereserveerd voorvoegsel door het argument toe te voegen bij het maken van het openbare IP-adres met Azure PowerShell. In het volgende voorbeeld wordt ervan uitgegaan dat een voorvoegsel is gemaakt en opgeslagen in een PowerShell-variabele met de naam *$myOwnIPv6Voorvoegsel*.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Azure CLI gebruiken
 
In het volgende voorbeeld wordt ervan uitgegaan dat een voorvoegsel is gemaakt en opgeslagen in een CLI-variabele met de naam *IPv6PrefixWestUS*.

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [iPv6-adresvoorvoegsel](ipv6-public-ip-address-prefix.md).
- Meer informatie over [IPv6-adressen](ipv6-overview.md).
