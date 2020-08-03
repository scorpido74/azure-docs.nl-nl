---
title: Azure IoT Edge uitvoeren op Windows Server Virtual Machines | Microsoft Docs
description: Installatie-instructies Azure IoT Edge op Windows Server Marketplace Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0d8344f5f6b4ccc9f58cc1809f5d625e7e4adf68
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494057"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Azure IoT Edge uitvoeren op Windows Server Virtual Machines

Met de Azure IoT Edge runtime wordt een apparaat omgezet in een IoT Edge apparaat. De runtime kan worden geïmplementeerd op apparaten als een Raspberry Pi of zo groot als een industriële server. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, kunt u beginnen met het implementeren van bedrijfslogica vanuit de cloud.

Voor meer informatie over de werking van de IoT Edge runtime en welke onderdelen zijn opgenomen, raadpleegt u [de Azure IOT Edge runtime en de bijbehorende architectuur](iot-edge-runtime.md).

In dit artikel worden de stappen beschreven voor het uitvoeren van de Azure IoT Edge runtime op een virtuele machine met Windows Server 2019, met behulp van de [Windows Server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace-aanbieding. Volg de instructies in [de Azure IOT Edge runtime installeren](how-to-install-iot-edge-windows.md) op Windows voor gebruik met andere versies.

## <a name="deploy-from-the-azure-marketplace"></a>Implementeren vanuit Azure Marketplace

1. Ga naar de [Windows Server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace-aanbieding of zoek in Windows Server op [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Selecteer **nu downloaden**
3. Zoek in **Software plan**' Windows Server 2019 Data Center Server Core with containers ' en selecteer vervolgens **door gaan** in het volgende dialoog venster.
    * U kunt deze instructies ook gebruiken voor andere versies van Windows Server met containers
4. Selecteer in de Azure Portal **maken** en volg de wizard om de virtuele machine te implementeren.
    * Als uw virtuele machine voor het eerst wordt gebruikt, is het handig om een wacht woord te gebruiken en om RDP en SSH in te scha kelen in het menu open bare binnenkomende poort.
    * Als u een resource-intensieve werk belasting hebt, moet u de grootte van de virtuele machine upgraden door meer Cpu's en/of geheugen toe te voegen.
5. Wanneer de virtuele machine is geïmplementeerd, configureert u deze om verbinding te maken met uw IoT Hub:
    1. Kopieer uw apparaat connection string van uw IoT Edge apparaat dat u hebt gemaakt in uw IoT Hub. Zie de procedure [de Connection String ophalen in de Azure Portal](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Selecteer de zojuist gemaakte virtuele-machine bron in het Azure Portal en open de optie **opdracht uitvoeren**
    1. Selecteer de optie **RunPowerShellScript**
    1. Kopieer dit script naar het opdracht venster met uw apparaat connection string:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Voer het script uit om de IoT Edge runtime te installeren en stel de connection string in door **uitvoeren** te selecteren.
    1. Na een paar minuten wordt een bericht weer gegeven dat de Edge-runtime is geïnstalleerd en is ingericht.

## <a name="deploy-from-the-azure-portal"></a>Implementeren vanuit de Azure Portal

1. Zoek vanuit het Azure Portal naar ' Windows Server ' en selecteer **Windows server 2019 Data Center** om de werk stroom voor het maken van vm's te starten.
2. Kies voor **een software abonnement selecteren** "Windows Server 2019 Data Center Server Core with containers" en selecteer vervolgens **maken**
3. Voltooi stap 5 in de bovenstaande instructies ' implementeren vanuit Azure Marketplace '.

## <a name="deploy-from-azure-cli"></a>Implementeren vanuit Azure CLI

1. Als u Azure CLI op uw bureau blad gebruikt, meldt u zich aan met het volgende:

   ```azurecli-interactive
   az login
   ```

1. Als u meerdere abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken:
   1. Maak een lijst van uw abonnementen:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Kopieer het veld SubscriptionID voor het abonnement dat u wilt gebruiken
   1. Voer deze opdracht uit met de ID die u hebt gekopieerd:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Maak een nieuwe resource groep (of geef een bestaande op in de volgende stappen):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Een nieuwe virtuele machine maken:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Met deze opdracht wordt u gevraagd om een wacht woord, maar u kunt de optie `--admin-password` voor het eenvoudiger in een script toevoegen.
   * De Windows Server Core-installatie kopie heeft alleen opdracht regel ondersteuning met extern bureau blad, dus als u de volledige bureaublad ervaring wilt, geeft u als `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` de installatie kopie op.

1. Stel de connection string van het apparaat in (u kunt de procedure [voor het ophalen van de Connection String met Azure cli](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) volgen als u niet bekend bent met dit proces):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge apparaat hebt ingericht terwijl de runtime is geïnstalleerd, kunt u [IOT Edge modules implementeren](how-to-deploy-modules-portal.md).

Als u problemen ondervindt met het installeren van de Edge-runtime, raadpleegt u de pagina [probleem oplossing](troubleshoot.md) .

Zie [de IOT Edge Security daemon en runtime bijwerken](how-to-update-iot-edge.md)als u een bestaande installatie wilt bijwerken naar de nieuwste versie van IOT Edge.

Meer informatie over het gebruik van virtuele Windows-machines vindt u in de [documentatie van Windows virtual machines](https://docs.microsoft.com/azure/virtual-machines/windows/).

Als u na de installatie SSH wilt maken naar deze VM, volgt u de [installatie van OpenSSH voor Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) Guide met extern bureau blad of externe Power shell.
