---
title: Snel een telemetrie naar Azure IoT Hub (CLI) verzenden
description: In deze Snelstartgids ziet u ontwikkel aars die nieuw zijn voor IoT Hub hoe u aan de slag kunt gaan met behulp van de Azure CLI om een IoT-hub te maken, telemetrie te verzenden en berichten tussen een apparaat en de hub weer te geven.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: d926f1c286c4288784d429f13b22436d7c8ae7e5
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73849274"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Quick Start: verzend telemetrie van een apparaat naar een IoT-hub en bewaak deze met de Azure CLI

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub is een Azure-service waarmee grote hoeveelheden telemetrie van uw IoT-apparaten naar de cloud kunt opnemen voor opslag of verwerking. In deze Quick Start gebruikt u de Azure CLI om een IoT Hub en een gesimuleerd apparaat te maken, telemetrie van het apparaat naar de hub te verzenden en een Cloud-naar-apparaat-bericht te verzenden. U kunt ook de Azure Portal gebruiken om metrische gegevens over apparaten te visualiseren. Dit is een basis werk stroom voor ontwikkel aars die gebruikmaken van de CLI om te communiceren met een IoT Hub-toepassing.

## <a name="prerequisites"></a>Vereisten
- Als u nog geen abonnement op Azure hebt, [Maak dan een gratis versie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Azure CLI. U kunt alle opdrachten in deze snelstartgids uitvoeren met behulp van de Azure Cloud Shell, een interactieve CLI-shell die in uw browser wordt uitgevoerd. Als u de Cloud Shell gebruikt, hoeft u niets te installeren. Als u liever de CLI lokaal wilt gebruiken, is voor deze Quick Start Azure CLI-versie 2.0.76 of hoger vereist. Voer az --version uit om de versie te zoeken. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
Meld u aan bij Azure Portal op https://portal.azure.com.

Ongeacht of u de CLI lokaal of in de Cloud Shell uitvoert, blijft de portal geopend in uw browser.  U gebruikt deze verderop in deze Quick Start.

## <a name="launch-the-cloud-shell"></a>De Cloud Shell starten
In deze sectie start u een exemplaar van de Azure Cloud Shell. Als u de CLI lokaal gebruikt, gaat u naar de sectie [twee cli-sessies voorbereiden](#prepare-two-cli-sessions).

De Cloud Shell starten:
1. Selecteer de knop **Cloud shell** in de rechter menu balk van het Azure Portal. 

    ![Knop Azure Portal Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Als dit de eerste keer is dat u de Cloud Shell gebruikt, wordt u gevraagd om opslag ruimte te maken. Dit is vereist voor het gebruik van de Cloud Shell.  Selecteer een abonnement om een opslag account en Microsoft Azure bestands share te maken. 

1. Selecteer uw voorkeurs CLI-omgeving in de vervolg keuzelijst **omgeving selecteren** . In deze Quick Start wordt gebruikgemaakt van de **bash** -omgeving. Alle volgende CLI-opdrachten werken ook in de Power shell-omgeving. 

    ![CLI-omgeving selecteren](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Twee CLI-sessies voorbereiden
In deze sectie bereidt u twee Azure CLI-sessies voor. In de Cloud Shell voert u de twee sessies uit op afzonderlijke browser tabbladen. In een lokale CLI-client voert u twee afzonderlijke CLI-instanties uit. U gebruikt de eerste sessie als een gesimuleerd apparaat en de tweede sessie om berichten te controleren en te verzenden. Als u een opdracht wilt uitvoeren, selecteert u **kopiëren** om een code blok in deze Snelstartgids te kopiëren, plakt u deze in de shell-sessie en voert u deze uit.

Voor Azure CLI moet u zijn aangemeld bij uw Azure-account. Alle communicatie tussen uw Azure CLI-shell sessie en uw IoT-hub wordt geverifieerd en versleuteld. Als gevolg hiervan heeft deze Snelstartgids geen aanvullende verificatie nodig die u zou gebruiken met een echt apparaat, zoals een connection string.

1. Voer de opdracht [AZ extension add](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add) toe om de Microsoft Azure IOT-extensie voor Azure cli toe te voegen aan uw cli-shell. De IOT-extensie voegt IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS)-specifieke opdrachten toe aan Azure CLI.

   ```azurecli
   az extension add --name azure-cli-iot-ext
   ```
    Nadat u de Azure IOT-extensie hebt geïnstalleerd, hoeft u deze niet opnieuw te installeren in een Cloud Shell sessie. 

1. Open een tweede CLI-sessie.  Als u de Cloud Shell gebruikt, selecteert u **nieuwe sessie openen**. Als u de CLI lokaal gebruikt, opent u een tweede exemplaar. 

    ![Nieuwe Cloud Shell-sessie openen](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Een IoT Hub maken
In deze sectie gebruikt u de Azure CLI voor het maken van een resource groep en een IoT Hub.  Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Een IoT Hub fungeert als een centrale Message hub voor bidirectionele communicatie tussen uw IoT-toepassing en de apparaten. 

> [!TIP]
> U kunt desgewenst een Azure-resource groep, een IoT Hub en andere resources maken met behulp van de [Azure Portal](iot-hub-create-through-portal.md), [Visual Studio code](iot-hub-create-use-iot-toolkit.md)of andere programmatische methoden.  

1. Voer de opdracht [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) uit om een resource groep te maken. Met de volgende opdracht maakt u een resource groep met de naam *MyResourceGroup* op de locatie *eastus* . 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Voer de opdracht [AZ IOT hub Create](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create) uit om een IOT-hub te maken.

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. De naam van een IoT-hub moet globaal uniek zijn in Azure. Deze tijdelijke aanduiding wordt gebruikt in de rest van deze Quick Start om de naam van uw IoT-hub aan te geven.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Een apparaat maken en bewaken
In deze sectie maakt u een gesimuleerd apparaat in de eerste CLI-sessie. Het gesimuleerde apparaat verzendt telemetrie van apparaat naar uw IoT-hub. In de tweede CLI-sessie bewaakt u gebeurtenissen en telemetrie en verzendt u een Cloud-naar-apparaat-bericht naar het gesimuleerde apparaat.

Een gesimuleerd apparaat maken en starten:
1. Voer de opdracht [AZ IOT hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) uit in de eerste cli-sessie. Hiermee maakt u de gesimuleerde apparaat-id. 

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    *simDevice*. U kunt deze naam rechtstreeks voor het gesimuleerde apparaat in de rest van deze Quick Start gebruiken. Gebruik desgewenst een andere naam. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Voer de opdracht [AZ IOT Device simuleering](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-simulate) uit in de eerste cli-sessie.  Hiermee start u het gesimuleerde apparaat. Het apparaat verzendt telemetrie naar uw IoT-hub en ontvangt hiervan berichten.  

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Een apparaat bewaken:
1. In de tweede CLI-sessie voert u de opdracht [AZ IOT hub monitor-Events](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) uit. Hiermee begint u met het bewaken van het gesimuleerde apparaat. De uitvoer toont telemetrie die het gesimuleerde apparaat verzendt naar de IoT-hub.

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Gebeurtenissen Cloud Shell controleren](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Nadat u het gesimuleerde apparaat in de tweede CLI-sessie hebt gecontroleerd, drukt u op CTRL + C om de bewaking te stoppen. 

## <a name="use-the-cli-to-send-a-message"></a>De CLI gebruiken om een bericht te verzenden
In deze sectie gebruikt u de tweede CLI-sessie om een bericht te verzenden naar het gesimuleerde apparaat.

1. Controleer in de eerste CLI-sessie of het gesimuleerde apparaat actief is. Als het apparaat is gestopt, voert u de volgende opdracht uit om het te starten:

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. In de tweede CLI-sessie voert u de opdracht [AZ IOT Device C2D-Message Send](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device/c2d-message?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-c2d-message-send) uit. Hiermee verzendt u een Cloud-naar-apparaat-bericht van uw IoT-hub naar het gesimuleerde apparaat. Het bericht bevat een teken reeks en twee sleutel-waardeparen.  

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    U kunt ook Cloud-naar-apparaat-berichten verzenden met behulp van de Azure Portal. Als u dit wilt doen, bladert u naar de overzichts pagina voor uw IoT Hub, selecteert u **IOT-apparaten**, selecteert u het gesimuleerde apparaat en selecteert **u bericht op apparaat**. 

1. Controleer in de eerste CLI-sessie of het gesimuleerde apparaat het bericht heeft ontvangen. 

    ![Cloud-naar-apparaat-bericht Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Nadat u het bericht hebt bekeken, sluit u beide CLI-sessies. 

## <a name="view-messaging-metrics-in-the-portal"></a>Metrische bericht gegevens weer geven in de portal
Met de Azure Portal kunt u alle aspecten van uw IoT Hub en apparaten beheren. In een typische IoT Hub toepassing die telemetrie van apparaten opneemt, wilt u mogelijk apparaten bewaken of metrische gegevens weer geven op telemetrie van het apparaat. 

Voor het visualiseren van metrische gegevens voor berichten in de Azure Portal:
1. Selecteer **alle resources**op de **Start** pagina in de portal. 

1. Selecteer in de lijst met resource groepen de IoT-hub die u hebt gemaakt. 

1. Selecteer **metrische gegevens** in het linkerdeel venster van uw IOT hub. 

    ![Metrische gegevens van IoT Hub bericht](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Voer de naam van uw IoT-hub in het **bereik**in.

2. Selecteer *standaard statistieken voor IOT hub* in **metrische naam ruimte**.

3. Selecteer het *totale aantal berichten dat wordt gebruikt* in de **metrische gegevens**. 

4. Beweeg de muis aanwijzer over het gebied van de tijd lijn waarin uw apparaat berichten heeft verzonden. Het totale aantal berichten op een bepaald moment wordt weer gegeven in de linkerbenedenhoek van de tijd lijn.

    ![IoT Hub metrische gegevens voor Azure weer geven](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Gebruik eventueel de vervolg keuzelijst **metrisch** om andere metrische gegevens op uw gesimuleerde apparaat weer te geven. Bijvoorbeeld C2d- *bericht leveringen voltooid* of *totaal apparaten (preview)* . 

## <a name="clean-up-resources"></a>Resources opschonen
Als u de Azure-resources die u in deze Quick Start hebt gemaakt, niet meer nodig hebt, kunt u de Azure CLI gebruiken om ze te verwijderen.

Als u doorgaat met het volgende aanbevolen artikel, kunt u de resources die u al hebt gemaakt, behouden en opnieuw gebruiken. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

Een resourcegroep verwijderen op naam:
1. Voer de opdracht [AZ Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) uit. Hiermee verwijdert u de resource groep, het IoT Hub en de apparaatregistratie die u hebt gemaakt.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Voer de opdracht [AZ Group List](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list) uit om te bevestigen dat de resource groep is verwijderd.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u de Azure CLI gebruikt om een IoT-hub te maken, een gesimuleerd apparaat te maken, telemetrie te verzenden, telemetrie te controleren, een Cloud-naar-apparaat-bericht te verzenden en resources op te schonen. U hebt de Azure Portal gebruikt om metrische gegevens van berichten op uw apparaat te visualiseren.

Als u een ontwikkelaar van een apparaat bent, is de voorgestelde volgende stap het weer geven van de telemetrie-Snelstartgids die gebruikmaakt van de Azure IoT Device SDK voor C. u kunt eventueel een van de beschik bare Quick Start-artikelen over Azure IoT Hub-telemetrie in uw voorkeurs taal of SDK bekijken.

> [!div class="nextstepaction"]
> [Quick Start: telemetrie verzenden van een apparaat naar een IoT-hub (C)](quickstart-send-telemetry-c.md)
