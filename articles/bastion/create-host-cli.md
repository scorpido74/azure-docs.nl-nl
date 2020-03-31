---
title: Een Bastion-host maken met Azure CLI | Azure Bastion
description: In dit artikel leert u hoe u een Bastion-host maakt en verwijdert
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: cherylmc
ms.openlocfilehash: 9e216bf2bb8e2e605723256f0511b9bdb63083b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337569"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Een Azure Bastion-host maken met Azure CLI

In dit artikel ziet u hoe u een Azure Bastion-host maakt met Azure CLI. Zodra u de Azure Bastion-service in uw virtuele netwerk indient, is de naadloze RDP/SSH-ervaring beschikbaar voor alle VM's in hetzelfde virtuele netwerk. Azure Bastion-implementatie is per virtueel netwerk, niet per abonnement/account of virtuele machine.

Optioneel u een Azure Bastion-host maken met behulp van de [Azure-portal](bastion-create-host-portal.md)of Azure [PowerShell](bastion-create-host-powershell.md)gebruiken.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Een Bastion-host maken

Met deze sectie u een nieuwe Azure Bastion-bron maken met Azure CLI.

1. Maak een virtueel netwerk en een Subnet Azure Bastion. U moet het subnet Azure Bastion maken met de naamwaarde **AzureBastionSubnet**. Met deze waarde weet Azure naar welk subnet de Bastion-resources moeten worden geïmplementeerd. Dit is anders dan een Gateway-subnet. U moet een subnet van ten minste /27 of groter subnet gebruiken (/27, /26, enzovoort). Maak het **AzureBastionSubnet** zonder routetabellen of delegaties. Als u netwerkbeveiligingsgroepen op het **AzureBastionSubnet**gebruikt, raadpleegt u het artikel [Werken met NSGs.](bastion-nsg.md)

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Maak een openbaar IP-adres voor Azure Bastion. Het openbare IP-adres is het openbare IP-adres van de Bastion-bron waarop RDP/SSH zal worden geopend (via poort 443). Het openbare IP-adres moet zich in dezelfde regio bevinden als de Bastion-bron die u maakt.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. Maak een nieuwe Azure Bastion-bron in het AzureBastionSubnet van uw virtuele netwerk. Het duurt ongeveer 5 minuten voordat de Bastion-bron is gemaakt en geïmplementeerd.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Volgende stappen

* Lees de [Veelgestelde vragen over bastion](bastion-faq.md) voor meer informatie.

* Zie [Werken met NSGs](bastion-nsg.md)als u netwerkbeveiligingsgroepen wilt gebruiken met het subnet Azure Bastion.
