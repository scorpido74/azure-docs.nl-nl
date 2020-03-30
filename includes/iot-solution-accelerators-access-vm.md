---
title: bestand opnemen
description: bestand opnemen
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a58e408feadd10e6dbc9d6878b82a4d045918ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68781444"
---
## <a name="access-the-virtual-machine"></a>Toegang tot de virtuele machine

De volgende stappen gebruiken de Azure CLI in Azure Cloud Shell. U [desgevraagd de Azure CLI op](/cli/azure/install-azure-cli) uw ontwikkelingsmachine installeren en de opdrachten lokaal uitvoeren.

In de volgende stappen ziet u hoe u de virtuele Azure-machine configureert om **SSH-toegang** toe te staan. De getoonde stappen gaan ervan uit dat de naam die u hebt gekozen voor de oplossingsversneller **contoso-simulatie** is - deze waarde vervangen door de naam van uw implementatie:

1. Vermeld de inhoud van de resourcegroep die de bronnen voor oplossingsversneller bevat:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Noteer de naam van de virtuele machine, het openbare IP-adres en de netwerkbeveiligingsgroep - u hebt deze waarden later nodig.

1. Werk de netwerkbeveiligingsgroep bij om SSH-toegang toe te staan. De volgende opdracht gaat ervan uit dat de naam van de netwerkbeveiligingsgroep **contoso-simulatie-nsg** is - deze waarde vervangt door de naam van uw netwerkbeveiligingsgroep:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Alleen SSH-toegang inschakelen tijdens de test en ontwikkeling. Als u SSH inschakelt, [moet u het zo snel mogelijk opnieuw uitschakelen.](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines)

1. Werk het wachtwoord voor het **azureuser-account** op de virtuele machine bij naar een wachtwoord dat u kent. Kies uw eigen wachtwoord wanneer u de volgende opdracht uitvoert:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Zoek het openbare IP-adres van uw virtuele machine. De volgende opdracht gaat ervan uit dat de naam van de virtuele machine **vm-vikxv** is - vervang deze waarde door de naam van de virtuele machine waar u eerder een notitie van hebt gemaakt:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Noteer het openbare IP-adres van uw virtuele machine.
