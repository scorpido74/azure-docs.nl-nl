---
title: Een netwerk beveiligings groep (klassiek) maken met behulp van de klassieke Azure-CLI
description: Meer informatie over het maken en implementeren van een netwerk beveiligings groep (klassiek) met behulp van de klassieke Azure-CLI.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: c17e75849d1127c9c1001fd87af64e3ffe0eba14
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196720"
---
# <a name="create-a-network-security-group-classic-using-the-azure-classic-cli"></a>Een netwerk beveiligings groep (klassiek) maken met behulp van de klassieke Azure-CLI
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dit artikel is van toepassing op het klassieke implementatiemodel. U kunt ook [nsg's maken in het Resource Manager-implementatie model](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

De volgende voor beelden van Azure CLI-opdrachten verwachten een eenvoudige omgeving die al is gemaakt op basis van het scenario. Als u de opdrachten wilt uitvoeren zoals ze worden weer gegeven in dit document, bouwt u eerst de test omgeving op door [een VNet te maken](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Een NSG maken voor het front-end-subnet

1. Als u Azure CLI nog nooit hebt gebruikt, raadpleegt u [de Azure cli installeren en configureren](/cli/azure/install-cli-version-1.0).
2. Schakel over naar de klassieke modus:

    ```azurecli
    azure config mode asm
    ```   

3. Een NSG maken::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Maak een beveiligings regel waarmee u toegang krijgt tot poort 3389 (RDP) vanaf internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Een regel maken waarmee toegang tot poort 80 (HTTP) via internet mogelijk is:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Koppel de NSG aan het front-end-subnet:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>De NSG voor het back-end-subnet maken

1. Maak de NSG:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Een regel maken waarmee toegang tot poort 1433 (SQL) vanaf het front-end-subnet wordt toegestaan:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Een regel maken waarmee de toegang tot internet wordt geweigerd:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Koppel de NSG aan het back-end-subnet:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```