---
title: Azure IoT Edge uitvoeren op virtuele machines van Windows Server | Microsoft Documenten
description: Instructies voor het instellen van Azure IoT Edge op virtuele machines van Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77045906"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Azure IoT Edge uitvoeren op virtuele windows server-machines

De Azure IoT Edge-runtime maakt van een apparaat een IoT Edge-apparaat. De runtime kan worden geïmplementeerd op apparaten zo klein als een Raspberry Pi of zo groot als een industriële server. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, u bedrijfslogica vanuit de cloud implementeren.

Zie [De runtime van Azure IoT Edge en de architectuur ervan begrijpen](iot-edge-runtime.md)voor meer informatie over hoe de Runtime van IoT Edge werkt en welke onderdelen zijn opgenomen.

In dit artikel worden de stappen weergegeven om de Azure IoT Edge-runtime uit te voeren op een virtuele windows server van Server 2019 met behulp van de [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace-aanbieding. Volg de instructies bij [Het installeren van de Azure IoT Edge-runtime](how-to-install-iot-edge-windows.md) op Windows voor gebruik met andere versies.

## <a name="deploy-from-the-azure-marketplace"></a>Implementeren vanuit azure marketplace

1. Navigeer naar de [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace-aanbieding of door te zoeken op 'Windows Server' op [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Selecteer **NU GET IT**
3. Zoek in **Softwareplan**'Windows Server 2019 Datacenter Server Core with Containers' en selecteer **Verder gaan** in het volgende dialoogvenster.
    * U deze instructies ook gebruiken voor andere versies van Windows Server met containers
4. Selecteer eenmaal in de Azure-portal De wizard **maken** en volgen om de VM te implementeren.
    * Als het de eerste keer is dat u een VM uitprobeert, u het gemakkelijkst een wachtwoord gebruiken en RDP en SSH inschakelen in het menu van de openbare binnenkomende poort.
    * Als u een resource-intensieve werkbelasting hebt, moet u de grootte van de virtuele machine upgraden door meer CPU's en/of geheugen toe te voegen.
5. Zodra de virtuele machine is geïmplementeerd, configureert u deze om verbinding te maken met uw IoT-hub:
    1. Kopieer de tekenreeks van de apparaatverbinding vanaf uw IoT Edge-apparaat dat is gemaakt in uw IoT-hub. Zie de procedure [De verbindingstekenreeks ophalen in de Azure-portal](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Selecteer uw nieuw gemaakte virtuele machinebron in de Azure-portal en open de optie **uitvoeren**
    1. De optie **RunPowerShellScript** selecteren
    1. Kopieer dit script naar het opdrachtvenster met de tekenreeks apparaatverbinding:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Voer het script uit om de Tio Edge-runtime te installeren en stel de verbindingstekenreeks in door **Uitvoeren te** selecteren
    1. Na een minuut of twee ziet u een bericht dat de Edge-runtime is geïnstalleerd en ingericht.

## <a name="deploy-from-the-azure-portal"></a>Implementeren vanuit de Azure-portal

1. Zoek in de Azure-portal naar 'Windows Server' en selecteer **Windows Server 2019 Datacenter** om de workflow voor het maken van vm's te starten.
2. Kies **in Een softwareplan** selecteren 'Windows Server 2019 Datacenter Server Core with Containers' en selecteer **Vervolgens Maken**
3. Voltooi stap 5 in de bovenstaande instructies 'Implementeren vanuit de Azure Marketplace'.

## <a name="deploy-from-azure-cli"></a>Implementeren vanuit Azure CLI

1. Als u Azure CLI op uw bureaublad gebruikt, begint u met het aanmelden:

   ```azurecli-interactive
   az login
   ```

1. Als u meerdere abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken:
   1. Maak een lijst van uw abonnementen:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Het veld SubscriptionID kopiëren voor het abonnement dat u wilt gebruiken
   1. Voer deze opdracht uit met de id die u hebt gekopieerd:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Een nieuwe resourcegroep maken (of een bestaande groep opgeven in de volgende stappen):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Maak een nieuwe virtuele machine:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Met deze opdracht wordt u om een wachtwoord `--admin-password` gevraagd, maar u de optie toevoegen om het gemakkelijker in te stellen in een script
   * De Windows Server Core-afbeelding heeft alleen ondersteuning voor opdrachtregel met extern bureaublad, dus als u de volledige bureaubladervaring wilt, geeft u op `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` als de afbeelding

1. Stel de tekenreeks voor apparaatverbinding in (U de [verbindingstekenreeks ophalen met Azure CLI-procedure](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) volgen als u niet bekend bent met dit proces):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge-apparaat hebt ingericht met de runtime geïnstalleerd, u [IoT Edge-modules implementeren.](how-to-deploy-modules-portal.md)

Als u problemen ondervindt met het correct installeren van de Edge-runtime, raadpleegt u de [pagina probleemoplossing.](troubleshoot.md)

Zie [De IoT Edge-beveiligingsdaemon en runtime bijwerken](how-to-update-iot-edge.md)als u een bestaande installatie wilt bijwerken naar de nieuwste versie van IoT Edge.

Meer informatie over het gebruik van virtuele Windows-machines in de [windows Virtual Machines-documentatie](https://docs.microsoft.com/azure/virtual-machines/windows/).

Als u na de installatie naar Deze VM wilt gaan, volgt u de [handleiding Installatie van OpenSSH voor Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) met extern bureaublad of externe powershell.
