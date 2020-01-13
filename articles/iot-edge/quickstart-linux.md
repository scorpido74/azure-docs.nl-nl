---
title: 'Snelstartgids: Een Azure IoT Edge-apparaat maken in Linux | Microsoft Docs'
description: In deze snelstartgids leert u hoe u een IoT Edge-apparaat maakt en daarna kant-en-klare code op afstand implementeert vanuit Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8d2e0b4683261a06c39b9a5f335d7f4f22a2fd05
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912330"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Quick Start: uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat

Test Azure IoT Edge in deze Snelstartgids door container code te implementeren op een virtueel IoT Edge-apparaat. Met IoT Edge kunt u code op uw apparaten op afstand beheren zodat u meer van uw workloads naar de rand kunt verzenden. Voor deze Quick Start raden wij u aan een virtuele machine van Azure te gebruiken voor uw IoT Edge-apparaat, zodat u snel een test machine kunt maken waarbij alle vereisten zijn geïnstalleerd en deze vervolgens verwijderen wanneer u klaar bent.

In deze snelstart leert u de volgende zaken:

1. Een IoT Hub maken.
2. Een IoT Edge-apparaat registreren in uw IoT-hub.
3. Installeer en start de IoT Edge runtime op het virtuele apparaat.
4. Op afstand een module implementeren op een IoT Edge-apparaat.

![Diagram - Snelstartarchitectuur voor apparaat en cloud](./media/quickstart-linux/install-edge-full.png)

In deze Quick Start wordt u begeleid bij het maken van een virtuele Linux-machine die is geconfigureerd om te worden IoT Edge apparaat. Vervolgens implementeert u een module vanuit Azure Portal op uw apparaat. De module die u in deze zelfstudie implementeert, is een gesimuleerde sensor waarmee temperatuur-, luchtvochtigheids- en drukgegevens worden gegenereerd. De andere Azure IoT Edge-zelfstudies zijn een uitbreiding op het werk dat u hier doet. Hierin worden modules geïmplementeerd waarmee de gesimuleerde gegevens worden geanalyseerd voor zakelijke inzichten.

Als u nog geen actief abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U gebruikt de Azure CLI om veel van de stappen in deze snelstart uit te voeren, en Azure IoT heeft een extensie om extra functionaliteit in te schakelen.

Voeg de Azure IoT-extensie toe aan het exemplaar van Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Vereisten

Cloudresources:

* Een resourcegroep voor het beheren van alle resources die u in deze snelstart maakt.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge-apparaat:

* Een virtueel Linux-apparaat of een virtuele Linux-computer die fungeert als uw IoT Edge-apparaat. U moet de door micro soft meegeleverde [Azure IOT Edge op](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) de virtuele Ubuntu-machine gebruiken, waarmee alles wordt geïnstalleerd dat u nodig hebt om IOT Edge op een apparaat uit te voeren. Ga akkoord met de gebruiks voorwaarden en maak deze virtuele machine met behulp van de volgende opdrachten:

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   Het maken en starten van de nieuwe virtuele machine kan een paar minuten duren.

   Wanneer u een nieuwe virtuele machine maakt, noteert u het **publicIpAddress**, dat deel uitmaakt van de uitvoer van de opdracht create. U gebruikt dit openbare IP-adres later in deze quickstart om verbinding te maken met de virtuele machine.

* Als u de Azure IoT Edge runtime liever op uw eigen apparaat wilt uitvoeren, volgt u de instructies in [de Azure IOT Edge runtime op Linux installeren](how-to-install-iot-edge-linux.md).

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Begin met de snelstart door een IoT Hub met Azure CLI te maken.

![Diagram - Een IoT-hub maken in de cloud](./media/quickstart-linux/create-iot-hub.png)

Het gratis niveau van IoT Hub werkt voor deze snelstart. Als u in het verleden IoT Hub hebt gebruikt en al een gratis hub hebt gemaakt, kunt u die IoT-hub gebruiken. Elk abonnement biedt toegang tot slechts één gratis IoT-hub.

Met de volgende code wordt een gratis **F1**-hub gemaakt in de resourcegroep **IoTEdgeResources**. Vervang `{hub_name}` door een unieke naam voor uw IoT-hub.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Als er een fout optreedt omdat er al één gratis hub in uw abonnement is, wijzigt u de SKU in **S1**. Als u het foutbericht ontvangt dat de naam van de IoT Hub niet beschikbaar is, betekent dit dat iemand anders al een hub met die naam heeft. Probeer een andere naam.

## <a name="register-an-iot-edge-device"></a>Een IoT Edge-apparaat registreren

Registreer een IoT Edge-apparaat bij uw net gemaakte IoT Hub.

![Diagram - Een apparaat registreren met een IoT Hub-id](./media/quickstart-linux/register-device.png)

Maak een apparaat-id voor uw IoT Edge-apparaat, zodat het met uw IoT Hub kan communiceren. De apparaat-id is opgeslagen in de cloud, en u gebruikt een unieke apparaatverbindingsreeks om een fysiek apparaat te koppelen aan een apparaat-id.

Omdat IoT Edge-apparaten zich anders gedragen en anders kunnen worden beheerd dan gewone IoT-apparaten, declareert u deze identiteit met een `--edge-enabled`-vlag als een identiteit van een IoT Edge-apparaat.

1. Voer in Azure Cloud Shell de volgende opdracht in om een ​​apparaat met de naam **myEdgeDevice** in uw hub te maken.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Als u een foutbericht over iothubowner-beleidssleutels ontvangt, controleer dan of in de cloudshell de meest recente versie van de azure-cli-iot-ext-extensie wordt uitgevoerd.

2. Haal de verbindingsreeks voor uw apparaat op, zodat uw fysieke apparaat aan de bijbehorende identiteit in IoT Hub wordt gekoppeld.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Kopieer de waarde van de sleutel `connectionString` uit de JSON-uitvoer en sla deze op. Deze waarde is de verbindingsreeks van het apparaat. In de volgende sectie gaat u deze verbindingsreeks gebruiken om de IoT Edge-runtime te configureren.

   ![Verbindingsreeks ophalen uit de CLI-uitvoer](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Een IoT Edge-apparaat configureren

Start de Azure IoT Edge-runtime op uw IoT Edge-apparaat.

![Diagram - De runtime op apparaat starten](./media/quickstart-linux/start-runtime.png)

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. Deze bevat drie onderdelen. De **IoT Edge-beveiligingsdaemon** wordt telkens gestart wanneer een IoT Edge-apparaat wordt opgestart en start het apparaat op door de IoT Edge-agent te starten. De **IoT Edge-agent** faciliteert de implementatie en bewaking van modules op het IoT Edge-apparaat, inclusief de IoT Edge-hub. Met de **IoT Edge-hub** wordt de communicatie tussen modules op het IoT Edge-apparaat en tussen het apparaat en IoT Hub beheerd.

Tijdens de installatie van de runtime geeft u een apparaatverbindingsreeks op. Gebruik de tekenreeks die u hebt opgehaald via de Azure CLI. Deze tekenreeks koppelt uw fysieke apparaat aan de IoT Edge-apparaat-id in Azure.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>De verbindingsreeks instellen op het IoT Edge-apparaat

Als u de Azure IoT Edge op Ubuntu virtuele machine gebruikt, zoals beschreven in de vereisten, heeft het apparaat al de IoT Edge runtime geïnstalleerd. U hoeft alleen nog uw apparaat te configureren met de apparaatverbindingsreeks die u in de vorige sectie hebt opgehaald. U kunt dit op afstand doen zonder verbinding te maken met de virtuele machine. Voer de volgende opdracht uit en vervang `{device_connection_string}` door uw eigen teken reeks.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Als u IoT Edge op uw lokale machine of een ARM32-of ARM64-apparaat uitvoert, moet u de IoT Edge runtime en de vereisten op uw apparaat installeren. Volg de instructies in [install the Azure IOT Edge runtime op Linux](how-to-install-iot-edge-linux.md)en ga vervolgens terug naar deze Snelstartgids.

### <a name="view-the-iot-edge-runtime-status"></a>De IoT Edge runtime-status bekijken

De overige opdrachten in deze snelstart worden toegepast op uw IoT Edge-apparaat zelf, zodat u kunt zien wat er op het apparaat gebeurt. Als u een virtuele machine gebruikt, moet u verbinding maken met die machine met het openbare IP-adres dat is uitgevoerd door de maakopdracht. U kunt het openbare IP-adres ook vinden op de overzichtspagina van de virtuele machine in de Azure-portal. Gebruik de volgende opdracht om verbinding te maken met uw virtuele machine. Vervang `{azureuser}` als u een andere gebruikers naam hebt gebruikt dan de gebruiker die wordt voorgesteld in de vereisten. Vervang `{publicIpAddress}` door het adres van de computer.

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

Controleer of de runtime goed is geïnstalleerd en geconfigureerd op uw IoT Edge-apparaat.

>[!TIP]
>U hebt verhoogde bevoegdheden nodig om `iotedge`-opdrachten uit te voeren. Nadat u zich de eerste keer na de installatie van de IoT Edge-runtime hebt afgemeld en opnieuw hebt aangemeld, worden uw machtigingen automatisch bijgewerkt. Tot slot gebruikt u `sudo` voor de opdrachten.

1. Controleer of de IoT Edge Security daemon wordt uitgevoerd als een systeem service.

   ```bash
   sudo systemctl status iotedge
   ```

   ![De IoT Edge-daemon weer geven die wordt uitgevoerd als een systeem service](./media/quickstart-linux/iotedged-running.png)

2. Als u problemen met de service moet oplossen, haalt u de servicelogboeken op.

   ```bash
   journalctl -u iotedge
   ```

3. Bekijk de modules die op uw apparaat worden uitgevoerd.

   ```bash
   sudo iotedge list
   ```

   ![Eén module op uw apparaat bekijken](./media/quickstart-linux/iotedge-list-1.png)

Uw IoT Edge-apparaat is nu geconfigureerd. Het is gereed voor de uitvoering van modules die in de cloud zijn geïmplementeerd.

## <a name="deploy-a-module"></a>Een module implementeren

Beheer uw Azure IoT Edge-apparaat vanuit de cloud om een module te implementeren waarmee telemetriegegevens worden verzonden naar IoT Hub.
![Diagram - Module implementeren vanuit cloud op apparaat](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

In deze snelstart hebt u een nieuw IoT Edge-apparaat gemaakt en de IoT Edge-runtime erop geïnstalleerd. Vervolgens hebt u de Azure-portal gebruikt om een IoT Edge-module te implementeren om te worden uitgevoerd op het apparaat zonder dat er wijzigingen aan het apparaat zelf hoefden te worden aangebracht.

In dit geval worden met de module die u hebt gepusht voorbeeldgegevens gemaakt die u voor testen kunt gebruiken. De gesimuleerde temperatuursensormodule genereert omgevingsgegevens die u later kunt gebruiken voor testen. De gesimuleerde sensor bewaakt zowel een machine als de omgeving rond die machine. Deze sensor kan zich bijvoorbeeld in een serverruimte, in een fabriek of op een windturbine bevinden. Het bericht bevat informatie over de omgevingstemperatuur, de luchtvochtigheid, de machinetemperatuur en de druk, evenals een tijdstempel. In de IoT Edge-zelfstudies worden gegevens van deze module gebruikt als testgegevens voor analyses.

Open opnieuw de opdrachtprompt op uw IoT Edge-apparaat of gebruik de SSH-verbinding vanuit de Azure CLI. Bevestig dat de module die vanuit de cloud is geïmplementeerd, op uw IoT Edge -apparaat wordt uitgevoerd:

   ```bash
   sudo iotedge list
   ```

   ![Drie modules op uw apparaat bekijken](./media/quickstart-linux/iotedge-list-2.png)

De berichten bekijken die vanuit de temperatuursensormodule worden verzonden:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge-opdrachten zijn hoofdlettergevoelig wanneer u naar modulenamen verwijst.

   ![De gegevens van uw module bekijken](./media/quickstart-linux/iotedge-logs.png)

U kunt ook de berichten bekijken die binnenkomen op uw IoT-hub met behulp van de [Azure IOT hub-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt doorgaan met de IoT Edge-zelfstudies, kunt u het apparaat gebruiken dat u hebt geregistreerd en ingesteld in deze snelstart. Als dat niet het geval is, kunt u de Azure-resources die u hebt gemaakt, verwijderen om kosten te voor komen.

Als u uw virtuele machine en IoT-hub in een nieuwe resourcegroep hebt gemaakt, kunt u die groep en alle bijbehorende resources verwijderen. Controleer de inhoud van de resourcegroep zorgvuldig om te na te gaan of er niets is dat u wilt behouden. Als u niet de hele groep wilt verwijderen, kunt u in plaats daarvan afzonderlijke resources verwijderen.

Verwijder de groep **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een IoT Edge-apparaat gemaakt en de Azure IoT Edge-cloudinterface gebruikt om code te implementeren op het apparaat. U hebt nu een testapparaat waarmee ruwe gegevens over de omgeving worden gegenereerd.

De volgende stap is het instellen van uw lokale ontwikkel omgeving, zodat u kunt beginnen met het maken van IoT Edge-modules die uw bedrijfs logica uitvoeren.

> [!div class="nextstepaction"]
> [Beginnen met het ontwikkelen van IoT Edge-modules voor Linux-apparaten](tutorial-develop-for-linux.md)
