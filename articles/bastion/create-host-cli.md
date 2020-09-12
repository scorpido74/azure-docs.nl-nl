---
title: Een bastion-host maken met behulp van Azure CLI | Azure-Bastion
description: In dit artikel leest u hoe u een bastion-host maakt en verwijdert
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: 8ee90d80230f9115946525ede325e874e98e358e
ms.sourcegitcommit: 70ee014d1706e903b7d1e346ba866f5e08b22761
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90024329"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Een Azure bastion-host maken met behulp van Azure CLI

In dit artikel wordt beschreven hoe u een Azure bastion-host maakt met behulp van Azure CLI. Zodra u de Azure Bastion-service in uw virtuele netwerk hebt ingericht, is de naadloze RDP/SSH-ervaring beschikbaar voor alle virtuele machines in hetzelfde virtuele netwerk. De Azure Bastion-implementatie vindt plaats per virtueel netwerk, niet per abonnement/account of virtuele machine.

U kunt desgewenst een Azure bastion-host maken met behulp van de [Azure Portal](bastion-create-host-portal.md), of [Azure PowerShell](bastion-create-host-powershell.md)gebruiken.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Een bastion-host maken

Deze sectie helpt u bij het maken van een nieuwe Azure Bastion-resource met behulp van Azure CLI.

> [!NOTE]
> Zoals in de voor beelden wordt weer gegeven, gebruikt `--location` u de para meter met `--resource-group` voor elke opdracht om ervoor te zorgen dat de resources samen worden geïmplementeerd.

1. Maak een virtueel netwerk en een Azure Bastion-subnet. U moet het Azure Bastion-subnet maken met de naam waarde **AzureBastionSubnet**. Deze waarde geeft Azure aan op welk subnet de Bastion-resources moeten worden geïmplementeerd. Dit wijkt af van een gateway-subnet. U moet een subnet van Mini maal/27 of groter subnet (/27,/26, enzovoort) gebruiken. Maak de **AzureBastionSubnet** zonder route tabellen of delegaties. Als u netwerk beveiligings groepen op de **AzureBastionSubnet**gebruikt, raadpleegt u het artikel [over het werken met nsg's](bastion-nsg.md) .

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Maak een openbaar IP-adres voor Azure Bastion. De open bare IP is het open bare IP-adres van de Bastion-bron waarop RDP/SSH wordt gebruikt (via poort 443). Het open bare IP-adres moet zich in dezelfde regio bevinden als de Bastion-resource die u maakt.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Maak een nieuwe Azure Bastion-resource in de AzureBastionSubnet van uw virtuele netwerk. Het duurt ongeveer vijf minuten voordat de Bastion-resource wordt gemaakt en geïmplementeerd.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>Volgende stappen

* Lees de [Veelgestelde vragen over Bastion](bastion-faq.md) voor meer informatie.

* Als u netwerk beveiligings groepen wilt gebruiken met het Azure Bastion-subnet, raadpleegt u [werken met nsg's](bastion-nsg.md).
