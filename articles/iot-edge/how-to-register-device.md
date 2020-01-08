---
title: Een nieuw Azure IoT Edge apparaat registreren | Microsoft Docs
description: De IoT-extensie voor Azure CLI gebruiken om te registreren van een nieuwe IoT Edge-apparaat en de verbindingsreeks ophalen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ab9c778adef29a8e531158e062e9d35d4e80ae4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434138"
---
# <a name="register-an-azure-iot-edge-device"></a>Een Azure IoT Edge-apparaat registreren

Voordat u uw IoT-apparaten met Azure IoT Edge kunt gebruiken, moet u ze registreren bij uw IoT-hub. Zodra een apparaat is geregistreerd, kunt u een connection string ophalen om uw apparaat in te stellen voor IoT Edge werk belastingen.

U kunt kiezen uit een van de volgende hulpprogram ma's:

* [Azure Portal](https://portal.azure.com) biedt een graphical user interface om Azure-resources te maken, weer te geven en te beheren.
* [Visual Studio code](https://code.visualstudio.com/) is een bron code-editor. Met Azure IoT-uitbrei dingen kunt u eenvoudig IoT-resources beheren met hetzelfde hulp programma voor het ontwikkelen van IoT-oplossingen.
* [Azure cli](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is een opdracht regel programma voor het beheer van Azure-resources. De herbruikbare opdrachten zijn handig voor het automatiseren van taken.

## <a name="register-in-the-azure-portal"></a>Registreren in de Azure Portal

U kunt alle registratie taken uitvoeren in de Azure Portal.

### <a name="prerequisites-for-the-azure-portal"></a>Vereisten voor de Azure Portal

Een gratis of standaard [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Een IoT Edge apparaat maken in de Azure Portal

In uw IoT Hub in de Azure Portal worden IoT Edge apparaten afzonderlijk gemaakt en beheerd van IOT-apparaten die niet Edge zijn ingeschakeld.

1. Aanmelden bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.
2. Selecteer in het linkerdeel venster **IOT Edge** in het menu.
3. Selecteer **een IOT edge apparaat toevoegen**.
4. Geef een beschrijvende apparaat-ID. Gebruik de standaard instellingen voor het automatisch genereren van verificatie sleutels en het verbinden van het nieuwe apparaat met uw hub.
5. Selecteer **Opslaan**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>IoT Edge apparaten weer geven in de Azure Portal

Alle de edge-apparaten die verbinding met uw IoT-hub maken worden weergegeven op de **IoT Edge** pagina.

![Alle IoT Edge-apparaten in uw IoT-hub bekijken](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>De connection string in het Azure Portal ophalen

Wanneer u klaar bent om uw apparaat instellen, moet u de verbindingsreeks die is gekoppeld aan uw fysieke apparaat met de identiteit van de IoT-hub.

1. Klik op de pagina **IOT Edge** in de portal op de apparaat-id in de lijst met IOT edge apparaten.
2. Kopieer de waarde van de **primaire verbindings reeks** of **secundaire verbindings reeks**.

## <a name="register-with-visual-studio-code"></a>Registreren met Visual Studio code

Er zijn meerdere manieren om uit te voeren van de meeste bewerkingen in VS Code. In dit artikel wordt de Explorer gebruikt, maar u kunt ook het opdracht palet gebruiken om de stappen uit te voeren.

### <a name="prerequisites-for-visual-studio-code"></a>Vereisten voor Visual Studio code

* Een [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) voor Visual Studio code

### <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan voor toegang tot uw IoT-hub

U kunt de Azure IoT-uitbrei dingen voor Visual Studio code gebruiken om bewerkingen met uw IoT Hub uit te voeren. Als u deze bewerkingen wilt gebruiken, moet u zich aanmelden bij uw Azure-account en uw IoT Hub selecteren.

1. Open in Visual Studio Code, de **Explorer** weergeven.
1. Vouw de sectie **Azure-IOT hub** aan de onderkant van de Explorer uit.

   ![Vouw de sectie Azure IoT Hub-apparaten](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klik in de koptekst van de Azure- **IOT hub** op de sectie **..** .. Als u het beletselteken niet ziet, klikt u op of Beweeg de muisaanwijzer over de header.
1. Kies **IoT-Hub selecteren**.
1. Als u niet bent aangemeld bij uw Azure-account, volgt u de aanwijzingen om dit te doen.
1. Selecteer uw Azure-abonnement.
1. Selecteer uw IoT-hub.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Een IoT Edge-apparaat maken met Visual Studio code

1. Vouw in de VS Code Explorer de **Azure IoT Hub-apparaten** sectie.
1. Klik op de **...**  in de **Azure IoT Hub-apparaten** sectiekop. Als u het beletselteken niet ziet, klikt u op of Beweeg de muisaanwijzer over de header.
1. Selecteer **IoT Edge-apparaat maken**.
1. Geef in het tekstvak dat wordt geopend, uw apparaat een ID.

In het uitvoerscherm ziet u het resultaat van de opdracht. De apparaatgegevens wordt afgedrukt, waaronder de **deviceId** die u hebt opgegeven en de **connectionString** waarmee u kunt uw fysieke apparaat verbinden met uw IoT-hub.

In het uitvoerscherm ziet u het resultaat van de opdracht. De apparaatgegevens wordt afgedrukt, waaronder de **deviceId** die u hebt opgegeven en de **connectionString** waarmee u kunt uw fysieke apparaat verbinden met uw IoT-hub.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>IoT Edge apparaten weer geven met Visual Studio code

Alle apparaten die verbinding maken met uw IoT-hub, worden weer gegeven in de sectie **Azure IOT hub** van Visual Studio code Explorer. IoT Edge apparaten zijn onderscheiden van niet-rand apparaten met een ander pictogram, en het feit dat de **$edgeAgent** en **$edgeHub** modules op elk IOT edge apparaat worden geïmplementeerd.

![Alle IoT Edge-apparaten in uw IoT-hub bekijken](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>De connection string ophalen met Visual Studio code

Wanneer u klaar bent om uw apparaat instellen, moet u de verbindingsreeks die is gekoppeld aan uw fysieke apparaat met de identiteit van de IoT-hub.

1. Klik met de rechter muisknop op de ID van uw apparaat in het gedeelte **Azure IOT hub** .
1. Selecteer **Apparaatverbindingsreeks kopiëren**.

   De verbindingsreeks is naar het Klembord gekopieerd.

U kunt ook selecteren **apparaatgegevens ophalen** in het contextmenu om te zien van alle apparaat-informatie, met inbegrip van de verbindingsreeks in het uitvoervenster weergegeven.

## <a name="register-with-the-azure-cli"></a>Registreren bij Azure CLI

[Azure cli](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is een open source-opdracht regel programma voor meerdere platformen voor het beheer van Azure-resources, zoals IOT Edge. Hiermee kunt u voor het beheren van Azure IoT Hub-resources, device provisioning service-exemplaren en gekoppelde hubs buiten het vak. Azure CLI verrijkt de nieuwe IoT-extensie met functies zoals Apparaatbeheer en de volledige functionaliteit van IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Vereisten voor de Azure CLI

* Een [IoT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet ten minste 2.0.24 of hoger. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Een IoT Edge-apparaat maken met de Azure CLI

Gebruik de opdracht [AZ IOT hub apparaat-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) om een nieuwe apparaat-id te maken in uw IOT-hub. Bijvoorbeeld:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Met deze opdracht worden drie parameters:

* **apparaat-id**: Geef een beschrijvende naam die uniek is voor uw IoT-hub.
* **naam van de hub**: Geef de naam van uw IoT-hub.
* **Edge-functionaliteit**: deze parameter geeft aan dat het apparaat voor gebruik met IoT Edge.

   ![AZ iot hub device-identity maken uitvoer](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>IoT Edge apparaten weer geven met de Azure CLI

Gebruik de opdracht [AZ IOT hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) om alle apparaten in uw IOT-hub weer te geven. Bijvoorbeeld:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Een apparaat dat is geregistreerd als een IoT Edge-apparaat de eigenschap heeft **capabilities.iotEdge** ingesteld op **waar**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>De connection string ophalen met de Azure CLI

Wanneer u klaar bent om uw apparaat instellen, moet u de verbindingsreeks die is gekoppeld aan uw fysieke apparaat met de identiteit van de IoT-hub. Gebruik de opdracht [AZ IOT hub Device-identiteit show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) om de Connection String voor één apparaat te retour neren:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

De waarde voor de para meter `device-id` is hoofdletter gevoelig. Kopieer niet de aanhalingstekens rond de verbindingsreeks.

## <a name="next-steps"></a>Volgende stappen

Nu u een apparaat-id in uw IoT-hub hebt geregistreerd, bent u klaar om de IoT Edge runtime op uw apparaten te installeren. Installeer de runtime op basis van het besturings systeem van het apparaat:

* [Azure IoT Edge installeren in Windows](how-to-install-iot-edge-windows.md)
* [Installeer de Azure IoT Edge runtime op Linux](how-to-install-iot-edge-linux.md)
