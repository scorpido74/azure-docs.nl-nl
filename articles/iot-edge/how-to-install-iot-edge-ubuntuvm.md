---
title: Azure IoT Edge uitvoeren op virtuele ubuntu-machines | Microsoft Documenten
description: Instructies voor het instellen van Azure IoT Edge voor Virtuele Ubuntu 18.04 LTS-machines
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349594"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Azure IoT Edge uitvoeren op virtuele ubuntu-machines

De Azure IoT Edge-runtime maakt van een apparaat een IoT Edge-apparaat. De runtime kan worden geïmplementeerd op apparaten zo klein als een Raspberry Pi of zo groot als een industriële server. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, u bedrijfslogica vanuit de cloud implementeren.

Zie [De runtime van Azure IoT Edge en de architectuur ervan begrijpen](iot-edge-runtime.md)voor meer informatie over hoe de Runtime van IoT Edge werkt en welke onderdelen zijn opgenomen.

In dit artikel worden de stappen weergegeven om een Virtuele Ubuntu 18.04 LTS-machine te implementeren met de Azure IoT Edge-runtime die is geïnstalleerd en geconfigureerd met behulp van een vooraf geleverde apparaatverbindingstekenreeks. De implementatie wordt uitgevoerd met behulp van een [op een Cloud-init](../virtual-machines/linux/using-cloud-init.md
) gebaseerde [Azure Resource Manager-sjabloon](../azure-resource-manager/templates/overview.md) die wordt onderhouden in de projectrepository [iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)

Bij de eerste boot installeert de Virtuele Ubuntu 18.04 LTS virtuele machine [de nieuwste versie van de Azure IoT Edge-runtime via cloud-init.](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt) Het stelt ook een meegeleverde verbindingstekenreeks in voordat de runtime begint, zodat u het IoT Edge-apparaat eenvoudig configureren en aansluiten zonder dat u een SSH- of extern bureaublad-sessie hoeft te starten. 

## <a name="deploy-using-deploy-to-azure-button"></a>Implementeren met deploy naar Azure-knop

Met [de knop Implementeren naar Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) u een gestroomlijnde implementatie van Azure Resource [Manager-sjablonen](../azure-resource-manager/templates/overview.md) op GitHub behouden.  In deze sectie wordt het gebruik van de knop Deploy to Azure in de projectrepository [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) gedemonstreerd.  


1. We implementeren een Linux VM met Azure IoT Edge met behulp van de azure-vm-deploy Azure Resource Manager-sjabloon.  Klik om te beginnen op de onderstaande knop:

    [![Implementeren op Azure Button voor iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Vul in het nieuw gestarte venster de beschikbare formuliervelden in:

    > [!div class="mx-imgBorder"]
    > [![Schermafbeelding van de sjabloon iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Abonnement:** het actieve Azure-abonnement om de virtuele machine in te implementeren.

    **Resourcegroep:** een bestaande of nieuw gemaakte resourcegroep die de virtuele machine en de bijbehorende resources bevat.

    **DNS Label Prefix:** Een vereiste waarde van uw keuze die wordt gebruikt om de hostnaam van de virtuele machine vooraf te fixeren.

    **Gebruikersnaam beheerder:** een gebruikersnaam, die rootrechten krijgt bij implementatie.

    **Apparaatverbindingstekenreeks:** een [tekenreeks apparaatverbinding](how-to-register-device.md) voor een apparaat dat is gemaakt binnen de beoogde [IoT-hub.](../iot-hub/about-iot-hub.md)

    **VM-grootte**: de [grootte](../cloud-services/cloud-services-sizes-specs.md) van de virtuele machine die moet worden geïmplementeerd

    **Ubuntu OS Versie**: De versie van het Ubuntu OS te worden geïnstalleerd op de basis virtuele machine.

    **Locatie**: De [geografische regio](https://azure.microsoft.com/global-infrastructure/locations/) om de virtuele machine in te zetten, deze waarde wordt standaard ingesteld op de locatie van de geselecteerde resourcegroep.

    **Verificatietype:** kies **sshPublicKey** of **wachtwoord,** afhankelijk van uw voorkeur.

    **Beheerderswachtwoord of -sleutel:** de waarde van de Openbare SSH-sleutel of de waarde van het wachtwoord, afhankelijk van de keuze van verificatietype.

    Wanneer alle velden zijn ingevuld, schakelt u het selectievakje onder aan de pagina in om de voorwaarden te accepteren en selecteert **u Kopen** om de implementatie te starten.

1. Controleer of de implementatie is voltooid.  Een virtuele machinebron had moeten worden geïmplementeerd in de geselecteerde resourcegroep.  Let op de naam van de machine, dit moet in het formaat `vm-0000000000000`. Neem ook nota van de bijbehorende **DNS-naam**, die in het formaat `<dnsLabelPrefix>`moet zijn. `<location>`.cloudapp.azure.com.

    De **DNS-naam** kan worden verkregen uit de sectie **Overzicht** van de nieuw geïmplementeerde virtuele machine binnen de Azure-portal.

    > [!div class="mx-imgBorder"]
    > [![Schermafbeelding van de dns-naam van de iotedge-vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Als u na de installatie naar SSH wilt gaan in deze vm, gebruikt u de bijbehorende **DNS-naam** met de opdracht:`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Implementeren vanuit Azure CLI

1. Controleer of u de Azure CLI-iot-extensie hebt geïnstalleerd met:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Als u Azure CLI op uw bureaublad gebruikt, begint u vervolgens met het inloggen:

   ```azurecli-interactive
   az login
   ```

1. Als u meerdere abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken:
   1. Maak een lijst van uw abonnementen:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Kopieer het veld SubscriptionID voor het abonnement dat u wilt gebruiken.

   1. Stel uw werkabonnement in met de id die u hebt gekopieerd:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Een nieuwe resourcegroep maken (of een bestaande groep opgeven in de volgende stappen):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Maak een nieuwe virtuele machine:

    Zie het onderstaande `password`voorbeeld om een **verificatiete gebruiken:**

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Als u wilt verifiëren met een SSH-sleutel, kunt `sshPublicKey`u dit doen door een **verificatietype** van , geef vervolgens de waarde van de SSH-sleutel op in de parameter **adminPasswordOrKey.**  Hieronder kunt u een voorbeeld bekijken.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. Controleer of de implementatie is voltooid.  Een virtuele machinebron had moeten worden geïmplementeerd in de geselecteerde resourcegroep.  Let op de naam van de machine, dit moet in het formaat `vm-0000000000000`. Neem ook nota van de bijbehorende **DNS-naam**, die in het formaat `<dnsLabelPrefix>`moet zijn. `<location>`.cloudapp.azure.com.

    De **DNS-naam** kan worden verkregen uit de JSON-geformatteerde uitvoer van de vorige stap, binnen de sectie **uitvoer** als onderdeel van de **openbare SSH-vermelding.**  De waarde van deze vermelding kan worden gebruikt om SSH in om de nieuw ingezette machine.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    De **DNS-naam** kan ook worden verkregen in de sectie **Overzicht** van de nieuw geïmplementeerde virtuele machine binnen de Azure-portal.

    > [!div class="mx-imgBorder"]
    > [![Schermafbeelding van de dns-naam van de iotedge-vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Als u na de installatie naar SSH wilt gaan in deze vm, gebruikt u de bijbehorende **DNS-naam** met de opdracht:`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge-apparaat hebt ingericht met de runtime geïnstalleerd, u [IoT Edge-modules implementeren.](how-to-deploy-modules-portal.md)

Als u problemen ondervindt met het correct installeren van de Runtime van IoT Edge, raadpleegt u de [pagina probleemoplossing.](troubleshoot.md)

Zie [De IoT Edge-beveiligingsdaemon en runtime bijwerken](how-to-update-iot-edge.md)als u een bestaande installatie wilt bijwerken naar de nieuwste versie van IoT Edge.

Als u poorten wilt openen om toegang te krijgen tot de VM via SSH of andere inkomende verbindingen, raadpleegt u de Azure Virtual Machines-documentatie over [het openen van poorten en eindpunten voor een Linux-VM](../virtual-machines/linux/nsg-quickstart.md)
