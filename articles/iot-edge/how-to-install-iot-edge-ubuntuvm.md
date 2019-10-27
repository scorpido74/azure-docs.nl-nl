---
title: Azure IoT Edge uitvoeren op Ubuntu Virtual Machines | Microsoft Docs
description: Installatie-instructies Azure IoT Edge op Ubuntu 16,04 Azure Marketplace Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: 0e5bec7d3e1ecd63541a319cd5a9151560ef4139
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964647"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Azure IoT Edge uitvoeren op Ubuntu Virtual Machines

Met de Azure IoT Edge runtime wordt een apparaat omgezet in een IoT Edge apparaat. De runtime kan worden geïmplementeerd op apparaten als een Raspberry Pi of zo groot als een industriële server. Zodra een apparaat is geconfigureerd met de IoT Edge runtime, kunt u beginnen met het implementeren van bedrijfs logica vanuit de Cloud.

Voor meer informatie over de werking van de IoT Edge runtime en welke onderdelen zijn opgenomen, raadpleegt u [de Azure IOT Edge runtime en de bijbehorende architectuur](iot-edge-runtime.md).

In dit artikel worden de stappen beschreven voor het uitvoeren van de Azure IoT Edge runtime op een virtuele machine met Ubuntu 16,04 met behulp van de vooraf geconfigureerde [Azure IOT Edge op Ubuntu Azure Marketplace-aanbieding](https://aka.ms/azure-iot-edge-ubuntuvm). 

Bij de eerste keer opstarten wordt de meest recente versie van de Azure IoT Edge runtime vooraf geïnstalleerd door de Azure IoT Edge op Ubuntu VM. Het bevat ook een script voor het instellen van de connection string en het opnieuw starten van de runtime, die extern kan worden geactiveerd via de Azure-VM-portal of de Azure-opdracht regel, zodat u het IoT Edge apparaat eenvoudig kunt configureren en verbinden zonder een SSH of extern te starten bureaublad sessie. Met dit script wordt gewacht tot de connection string ingesteld totdat de IoT Edge-client volledig is geïnstalleerd, zodat u deze niet hoeft te bouwen in uw automatisering.

## <a name="deploy-from-the-azure-marketplace"></a>Implementeren vanuit Azure Marketplace
1.  Ga naar de [Azure IOT Edge op Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) Marketplace-aanbieding of zoek naar ' Azure IOT Edge op Ubuntu ' op [de Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Selecteer **nu ophalen** en ga vervolgens **verder met** het volgende dialoog venster.
3.  Selecteer in de Azure Portal **maken** en volg de wizard om de virtuele machine te implementeren. 
    *   Als uw virtuele machine voor het eerst wordt gebruikt, is het handig om een wacht woord te gebruiken en om de SSH in te scha kelen in het menu open bare binnenkomende poort. 
    *   Als u een resource-intensieve werk belasting hebt, moet u de grootte van de virtuele machine upgraden door meer Cpu's en/of geheugen toe te voegen.
4.  Wanneer de virtuele machine is geïmplementeerd, configureert u deze om verbinding te maken met uw IoT Hub:
    1.  Kopieer uw apparaat connection string van uw IoT Edge apparaat dat u in uw IoT Hub hebt gemaakt (u kunt de [Connection String ophalen in de Azure Portal](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal) procedure als u niet bekend bent met dit proces)
    1.  Selecteer de zojuist gemaakte virtuele-machine bron in het Azure Portal en open de optie **opdracht uitvoeren**
    1.  Selecteer de optie **RunShellScript**
    1.  Voer het onderstaande script uit via het opdracht venster met uw apparaat connection string: `/etc/iotedge/configedge.sh “{device_connection_string}”`
    1.  Selecteer **uitvoeren**
    1.  Wacht even en het scherm moet vervolgens een geslaagd bericht geven waarin staat dat de connection string is ingesteld.


## <a name="deploy-from-the-azure-portal"></a>Implementeren vanuit de Azure Portal
Zoek in het Azure Portal naar ' Azure IoT Edge ' en selecteer **Ubuntu Server 16,04 LTS + Azure IOT Edge runtime** om de werk stroom voor het maken van vm's te starten. Voer de stappen 3 en 4 uit in de bovenstaande instructies implementeren vanuit Azure Marketplace.

## <a name="deploy-from-azure-cli"></a>Implementeren vanuit Azure CLI

1. Als u Azure CLI op uw bureau blad gebruikt, meldt u zich aan met het volgende:

   ```azurecli-interactive
   az login
   ```
    
1. Als u meerdere abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken:
   1. Uw abonnementen weer geven:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Kopieer het veld SubscriptionID voor het abonnement dat u wilt gebruiken.

   1. Stel uw werk abonnement in met de ID die u zojuist hebt gekopieerd:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Maak een nieuwe resource groep (of geef een bestaande op in de volgende stappen):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Ga akkoord met de gebruiks voorwaarden voor de virtuele machine. Als u de voor waarden eerst wilt bekijken, volgt u de stappen in [implementeren vanuit Azure Marketplace](#deploy-from-the-azure-marketplace).

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Een nieuwe virtuele machine maken:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Stel de connection string van het apparaat in (u kunt de procedure [voor het ophalen van de Connection String met de Azure cli](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) volgen als u niet bekend bent met dit proces):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Als u na de installatie SSH wilt gebruiken in deze VM, gebruikt u de publicIpAddress met de opdracht: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge apparaat hebt ingericht terwijl de runtime is geïnstalleerd, kunt u [IOT Edge modules implementeren](how-to-deploy-modules-portal.md).

Raadpleeg de pagina [probleem oplossing](troubleshoot.md) als u problemen ondervindt met het installeren van IOT Edge runtime.

Zie [de IOT Edge Security daemon en runtime bijwerken](how-to-update-iot-edge.md)als u een bestaande installatie wilt bijwerken naar de nieuwste versie van IOT Edge.

Als u poorten wilt openen voor toegang tot de virtuele machine via SSH of andere binnenkomende verbindingen, raadpleegt u de documentatie van de virtuele Azure-machine over het [openen van poorten en eind punten naar een Linux-VM](../virtual-machines/linux/nsg-quickstart.md)
