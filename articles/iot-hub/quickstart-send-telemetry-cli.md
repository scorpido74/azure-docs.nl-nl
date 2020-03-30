---
title: Telemetrie verzenden naar Azure IoT Hub (CLI) snelstart
description: Deze quickstart toont ontwikkelaars die nieuw zijn bij IoT Hub hoe ze aan de slag kunnen met Azure CLI om een IoT-hub te maken, telemetrie te verzenden en berichten tussen een apparaat en de hub te bekijken.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: 711e15986265324bbb353fb2b4404cbfeb48dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78851415"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Snelstart: telemetrie verzenden van een apparaat naar een IoT-hub en deze bewaken met de Azure CLI

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub is een Azure-service waarmee grote hoeveelheden telemetrie van uw IoT-apparaten naar de cloud kunt opnemen voor opslag of verwerking. In deze quickstart gebruikt u de Azure CLI om een IoT-hub en een gesimuleerd apparaat te maken, apparaattelemetrie naar de hub te verzenden en een cloud-naar-apparaatbericht te verzenden. U gebruikt de Azure-portal ook om apparaatstatistieken te visualiseren. Dit is een basisworkflow voor ontwikkelaars die de CLI gebruiken om te communiceren met een IoT Hub-toepassing.

## <a name="prerequisites"></a>Vereisten
- Als u geen Azure-abonnement hebt, [maakt u er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Azure CLI. U alle opdrachten in deze quickstart uitvoeren met de Azure Cloud Shell, een interactieve CLI-shell die in uw browser wordt uitgevoerd. Als u de Cloud Shell gebruikt, hoeft u niets te installeren. Als u de CLI liever lokaal gebruikt, vereist deze quickstart Azure CLI-versie 2.0.76 of hoger. Voer az --version uit om de versie te zoeken. Zie Azure CLI [installeren]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
Meld u aan bij Azure Portal op https://portal.azure.com.

Of u de CLI nu lokaal of in de Cloud Shell uitvoert, houd de portal open in uw browser.  Je gebruikt het later in deze quickstart.

## <a name="launch-the-cloud-shell"></a>Start de Cloud Shell
In deze sectie start u een instantie van de Azure Cloud Shell. Als u de CLI lokaal gebruikt, gaat u naar de sectie [Twee CLI-sessies voorbereiden](#prepare-two-cli-sessions).

Ga als volgende over op de lancering van de Cloud Shell:

1. Selecteer de knop **Cloud Shell** op de menubalk rechtsboven in de Azure-portal. 

    ![Knop Azure-portal Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Als dit de eerste keer is dat u de Cloud Shell gebruikt, wordt u gevraagd om opslag te maken, die nodig is om de Cloud Shell te gebruiken.  Selecteer een abonnement om een opslagaccount te maken en Microsoft Azure Files te delen. 

2. Selecteer de gewenste CLI-omgeving in de vervolgkeuzelijst **Select-omgeving.** Deze quickstart maakt gebruik van de **Bash-omgeving.** Alle volgende CLI-opdrachten werken ook in de Powershell-omgeving. 

    ![CLI-omgeving selecteren](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Bereid twee CLI-sessies voor

In deze sectie bereidt u twee Azure CLI-sessies voor. Als u de Cloud Shell gebruikt, voert u de twee sessies uit in afzonderlijke browsertabbladen. Als u een lokale CLI-client gebruikt, voert u twee afzonderlijke CLI-exemplaren uit. U gebruikt de eerste sessie als een gesimuleerd apparaat en de tweede sessie om berichten te controleren en te verzenden. Als u een opdracht wilt uitvoeren, selecteert u **Kopiëren** om een codeblok in deze quickstart te kopiëren, plakt u deze in uw shell-sessie en voert u deze uit.

Azure CLI vereist dat u bent aangemeld bij uw Azure-account. Alle communicatie tussen uw Azure CLI-shell-sessie en uw IoT-hub wordt geverifieerd en versleuteld. Deze quickstart heeft daarom geen extra verificatie nodig die u met een echt apparaat zou gebruiken, zoals een verbindingstekenreeks.

*  Voer de [opdracht Az-extensie toevoegen uit](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add) om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw CLI-shell. De IOT-extensie voegt specifieke opdrachten voor IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS) toe aan Azure CLI.

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Nadat u de Azure IOT-extensie hebt geïnstalleerd, hoeft u deze niet opnieuw te installeren in een Cloud Shell-sessie. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Open een tweede CLI-sessie.  Als u de Cloud Shell gebruikt, selecteert u **Nieuwe sessie openen**. Als u de CLI lokaal gebruikt, opent u een tweede instantie. 

    >[!div class="mx-imgBorder"]
    >![Nieuwe Cloud Shell-sessie openen](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Een IoT Hub maken
In deze sectie gebruikt u de Azure CLI om een brongroep en een IoT-hub te maken.  Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Een IoT Hub fungeert als een centrale berichtenhub voor bidirectionele communicatie tussen uw IoT-toepassing en de apparaten. 

> [!TIP]
> Optioneel u een Azure-brongroep, een IoT-hub en andere bronnen maken met behulp van de [Azure-portal,](iot-hub-create-through-portal.md) [Visual Studio Code](iot-hub-create-use-iot-toolkit.md)of andere programmatische methoden.  

1. Voer de opdracht [az-groep maken](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) om een resourcegroep te maken. Met de volgende opdracht wordt een resourcegroep met de naam *MyResourceGroup* op de *locatie Eastus* gea. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Voer de opdracht [az iot-hub uit](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create) om een IoT-hub te maken. Het kan enkele minuten duren voordat een IoT-hub is gemaakt. 

    *YouriotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. Een IoT-hubnaam moet wereldwijd uniek zijn in Azure. Deze tijdelijke aanduiding wordt gebruikt in de rest van deze quickstart om de naam van uw IoT-hub weer te geven.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Een apparaat maken en bewaken
In deze sectie maakt u een gesimuleerd apparaat in de eerste CLI-sessie. Het gesimuleerde apparaat stuurt apparaattelemetrie naar uw IoT-hub. In de tweede CLI-sessie houdt u gebeurtenissen en telemetrie in de gaten en verzendt u een cloud-to-device-bericht naar het gesimuleerde apparaat.

Ga als lid van het apparaat:
1. Voer de opdracht [az iot-hub-apparaat-identiteit uit](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) in de eerste CLI-sessie. Hierdoor wordt de gesimuleerde apparaatidentiteit vastgesteld. 

    *YouriotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    *simDevice*. U deze naam direct gebruiken voor het gesimuleerde apparaat in de rest van deze quickstart. Gebruik eventueel een andere naam. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Voer de opdracht [az iot-apparaat simuleren](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-simulate) in de eerste CLI-sessie.  Hiermee wordt het gesimuleerde apparaat gestart. Het apparaat stuurt telemetrie naar uw IoT-hub en ontvangt er berichten van.  

    *YouriotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Een apparaat controleren:
1. Voer in de tweede CLI-sessie de opdracht [monitor-events van AZ Iot-hub uit.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) Hiermee wordt begonnen met het monitoren van het gesimuleerde apparaat. De uitvoer toont telemetrie die het gesimuleerde apparaat naar de IoT-hub stuurt.

    *YouriotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Cloud Shell monitorgebeurtenissen](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Nadat u het gesimuleerde apparaat in de tweede CLI-sessie hebt gecontroleerd, drukt u op Ctrl+C om de bewaking te stoppen. 

## <a name="use-the-cli-to-send-a-message"></a>De CLI gebruiken om een bericht te verzenden
In deze sectie gebruikt u de tweede CLI-sessie om een bericht naar het gesimuleerde apparaat te verzenden.

1. Controleer in de eerste CLI-sessie of het gesimuleerde apparaat wordt uitgevoerd. Als het apparaat is gestopt, voert u de volgende opdracht uit om het apparaat te starten:

    *YouriotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Voer in de tweede [CLI-sessie de opdracht az iot-apparaat c2d-message send uit.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device/c2d-message?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-c2d-message-send) Hiermee wordt een cloud-to-device-bericht van uw IoT-hub naar het gesimuleerde apparaat verzonden. Het bericht bevat een tekenreeks en twee sleutelwaardeparen.  

    *YouriotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Optioneel u cloud-to-device-berichten verzenden via de Azure-portal. Ga hiervoor naar de overzichtspagina voor uw IoT-hub, selecteert **IoT-apparaten,** selecteert u het gesimuleerde apparaat en selecteert **Bericht naar apparaat**. 

1. Controleer in de eerste CLI-sessie of het gesimuleerde apparaat het bericht heeft ontvangen. 

    ![Cloud Shell cloud-to-device-bericht](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Nadat u het bericht hebt bekeken, sluit u de tweede CLI-sessie. Houd de eerste CLI-sessie open. U gebruikt het om middelen in een recentere stap op te schonen.

## <a name="view-messaging-metrics-in-the-portal"></a>Berichtenstatistieken weergeven in de portal
Met de Azure-portal u alle aspecten van uw IoT-hub en -apparaten beheren. In een typische IoT Hub-toepassing die telemetrie van apparaten inneemt, u apparaten controleren of statistieken weergeven over apparaattelemetrie. 

Ga als bedoeld als het gaat om het visualiseren van berichtenstatistieken in de Azure-portal:
1. Selecteer **alle bronnen**in het linkernavigatiemenu op de portal . Hier worden alle bronnen in uw abonnement weergegeven, inclusief de IoT-hub die u hebt gemaakt. 

1. Selecteer de koppeling op de IoT-hub die u hebt gemaakt. De portal geeft de overzichtspagina voor de hub weer.

1. Selecteer **Statistieken** in het linkerdeelvenster van uw IoT-hub. 

    ![IoT Hub-berichtenstatistieken](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Voer de naam van uw IoT-hub in **Scope**in.

2. Selecteer *standaardstatistieken van iot-hub* in **metrische naamruimte**.

3. Selecteer *Totaal aantal berichten dat wordt gebruikt* in Metrische **.** 

4. Plaats de muisaanwijzer op het gebied van de tijdlijn waarin uw apparaat berichten heeft verzonden. Het totale aantal berichten op een tijdstip wordt weergegeven in de linkerbenedenhoek van de tijdlijn.

    ![Azure IoT Hub-statistieken weergeven](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Gebruik de **vervolgkeuzelijst Metrische** optie om andere statistieken weer te geven op uw gesimuleerde apparaat. *C2d-berichtleveringen bijvoorbeeld voltooid* of *Totaal-apparaten (voorbeeld)*. 

## <a name="clean-up-resources"></a>Resources opschonen
Als u de Azure-resources die in deze quickstart zijn gemaakt, niet meer nodig hebt, u de Azure CLI gebruiken om deze te verwijderen.

Als u doorgaat met het volgende aanbevolen artikel, u de bronnen die u al hebt gemaakt behouden en deze opnieuw gebruiken. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

Een resourcegroep verwijderen op naam:
1. Voer de opdracht [verwijderen van de AZ-groep uit.](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) Hiermee worden de brongroep, de IoT-hub en de apparaatregistratie verwijderd die u hebt gemaakt.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Voer de [opdracht lijst met az-groepen](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list) uit om te bevestigen dat de brongroep is verwijderd.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u de Azure CLI gebruikt om een IoT-hub te maken, een gesimuleerd apparaat te maken, telemetrie te verzenden, telemetrie te controleren, een cloud-naar-apparaatbericht te verzenden en bronnen op te schonen. U hebt de Azure-portal gebruikt om berichtenstatistieken op uw apparaat te visualiseren.

Als u een apparaatontwikkelaar bent, wordt de volgende stap voorgesteld om de snelstart van telemetrie te zien die de Azure IoT Device SDK voor C gebruikt.

> [!div class="nextstepaction"]
> [Snelstart: telemetrie verzenden van een apparaat naar een IoT-hub (C)](quickstart-send-telemetry-c.md)
