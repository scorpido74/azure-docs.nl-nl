---
title: Include-bestand
description: Include-bestand
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 817c41a969f03ad04d372c516a16ef6b770f3e18
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755785"
---
## <a name="access-the-virtual-machine"></a>Toegang tot de virtuele machine

In de volgende stappen wordt de Azure CLI in Azure Cloud Shell gebruikt. Als u wilt, kunt u [de Azure cli installeren](/cli/azure/install-azure-cli) op uw ontwikkel computer en de opdrachten lokaal uitvoeren.

De volgende stappen laten zien hoe u de virtuele machine van Azure configureert om **SSH** -toegang toe te staan. Bij de stappen die worden weer gegeven, wordt ervan uitgegaan dat de naam die u hebt gekozen voor de oplossings versneller is **Contoso-simulatie** --Vervang deze waarde door de naam van uw implementatie:

1. Lijst met de inhoud van de resource groep die de resources voor oplossings versneller bevat:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Noteer de naam van de virtuele machine, het open bare IP-adres en de netwerk beveiligings groep: u hebt deze waarden later nodig.

1. Werk de netwerk beveiligings groep bij om SSH-toegang toe te staan. Bij de volgende opdracht wordt ervan uitgegaan dat de naam van de netwerk beveiligings groep **Contoso-simulatie-NSG** is--Vervang deze waarde door de naam van uw netwerk beveiligings groep:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Schakel SSH-toegang alleen in tijdens testen en ontwikkeling. Als u SSH inschakelt, [moet u dit zo snel mogelijk weer uitschakelen](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Werk het wacht woord voor het **azureuser** -account op de virtuele machine bij naar een wacht woord dat u kent. Kies uw eigen wacht woord wanneer u de volgende opdracht uitvoert:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Zoek het open bare IP-adres van uw virtuele machine. Bij de volgende opdracht wordt ervan uitgegaan dat de naam van de virtuele machine **VM-vikxv** is: Vervang deze waarde door de naam van de virtuele machine die u eerder hebt genoteerd:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Noteer het open bare IP-adres van uw virtuele machine.
