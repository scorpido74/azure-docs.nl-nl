---
title: Een nieuw Azure IoT Edge-apparaat registreren | Microsoft Documenten
description: Gebruik de IoT-extensie voor Azure CLI om een nieuw IoT Edge-apparaat te registreren en de verbindingstekenreeks op te halen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fb776b4c1ff537401a23eb272526b3043fdb1e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235728"
---
# <a name="register-an-azure-iot-edge-device"></a>Een Azure IoT Edge-apparaat registreren

Voordat u uw IoT-apparaten gebruiken met Azure IoT Edge, moet u ze registreren met uw IoT-hub. Zodra een apparaat is geregistreerd, u een verbindingstekenreeks ophalen om uw apparaat in te stellen voor IoT Edge-workloads.

Je hebt de keuze om je te registreren met een van de volgende tools:

* [Registreer een apparaat in de Azure-portal](#register-in-the-azure-portal) als u de voorkeur geeft aan een grafische gebruikersinterface om Azure-resources te maken, weer te geven en te beheren.
* [Registreer een apparaat met Visual Studio Code](#register-with-visual-studio-code) als u azure IoT-resources wilt beheren op dezelfde plaats waar u uw IoT-oplossingen ontwikkelt.
* [Registreer een apparaat bij de Azure CLI](#register-with-the-azure-cli) als u de voorkeur geeft aan opdrachtregelhulpprogramma's voor het beheren van Azure-resources of van plan bent taken te automatiseren.

## <a name="register-in-the-azure-portal"></a>Registreren in de Azure-portal

U alle registratietaken uitvoeren in de Azure-portal.

### <a name="prerequisites-for-the-azure-portal"></a>Voorwaarden voor de Azure-portal

Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Een IoT Edge-apparaat maken in de Azure-portal

In uw IoT-hub in de Azure-portal worden IoT Edge-apparaten afzonderlijk gemaakt en beheerd van IOT-apparaten die niet zijn ingeschakeld voor rand.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.
2. Selecteer **IoT Edge** in het linkerdeelvenster in het menu.
3. Selecteer **Een IoT Edge-apparaat toevoegen**.
4. Geef een beschrijvende apparaat-id op. Gebruik de standaardinstellingen om verificatiesleutels automatisch te genereren en het nieuwe apparaat aan te sluiten op uw hub.
5. Selecteer **Opslaan**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>IoT Edge-apparaten weergeven in de Azure-portal

Alle edge-enabled apparaten die verbinding maken met uw IoT-hub worden vermeld op de **IoT** Edge-pagina.

![Alle IoT Edge-apparaten weergeven in uw IoT-hub](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>De verbindingstekenreeks in de Azure-portal ophalen

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de verbindingstekenreeks nodig die uw fysieke apparaat koppelt aan de identiteit ervan in de IoT-hub.

1. Klik op de **IoT Edge-pagina** in de portal op de apparaat-id in de lijst met IoT Edge-apparaten.
2. Kopieer de waarde van **primaire verbindingstekenreeks** of **secundaire verbindingstekenreeks**.

## <a name="register-with-visual-studio-code"></a>Registreren met Visual Studio Code

Er zijn meerdere manieren om de meeste bewerkingen uit te voeren in VS Code. In dit artikel wordt de Explorer gebruikt, maar u het opdrachtpalet ook gebruiken om de stappen uit te voeren.

### <a name="prerequisites-for-visual-studio-code"></a>Voorwaarden voor Visual Studio Code

* Een [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement
* [Visual Studio-code](https://code.visualstudio.com/)
* [Azure IoT-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) voor visual studiocode

### <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan om toegang te krijgen tot uw IoT-hub

U de Azure IoT-extensies voor Visual Studio Code gebruiken om bewerkingen uit te voeren met uw IoT Hub. Als deze bewerkingen werken, moet u zich aanmelden bij uw Azure-account en uw IoT-hub selecteren.

1. Open in Visual Studio Code de **explorer-weergave.**
1. Vouw onder aan de Explorer de sectie **Azure IoT Hub** uit.

   ![Azure IoT Hub-apparaten uitbreiden, sectie](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klik op de **sectiekop van** de **Azure IoT Hub.** Als u de ellips niet ziet, klikt u op of zweeft u over de koptekst.
1. Kies **IoT-hub selecteren**.
1. Als u niet bent aangemeld bij uw Azure-account, volgt u de aanwijzingen hiervoor.
1. Selecteer uw Azure-abonnement.
1. Selecteer uw IoT-hub.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Een IoT Edge-apparaat maken met Visual Studio-code

1. Vouw in de VS Code Explorer de sectie **Azure IoT Hub-apparaten** uit.
1. Klik op de sectiekop **van** **De Azure IoT Hub-apparaten.** Als u de ellips niet ziet, klikt u op of zweeft u over de koptekst.
1. Selecteer **IoT Edge-apparaat maken**.
1. Geef uw apparaat in het tekstvak dat wordt geopend een id.

In het uitvoerscherm ziet u het resultaat van de opdracht. De apparaatgegevens worden afgedrukt, waaronder de door u opgegeven **deviceId** en de **verbindingstekenreeks** die u gebruiken om uw fysieke apparaat aan te sluiten op uw IoT-hub.

In het uitvoerscherm ziet u het resultaat van de opdracht. De apparaatgegevens worden afgedrukt, waaronder de door u opgegeven **deviceId** en de **verbindingstekenreeks** die u gebruiken om uw fysieke apparaat aan te sluiten op uw IoT-hub.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>IoT Edge-apparaten weergeven met Visual Studio-code

Alle apparaten die verbinding maken met uw IoT-hub worden weergegeven in het azure **IoT-hubgedeelte** van de Visual Studio Code Explorer. IoT Edge-apparaten zijn te onderscheiden van niet-Edge-apparaten met een ander pictogram en het feit dat de **$edgeAgent-** en **$edgeHub-modules** worden geïmplementeerd op elk IoT Edge-apparaat.

![Alle IoT Edge-apparaten weergeven in uw IoT-hub](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>De verbindingstekenreeks ophalen met Visual Studio Code

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de verbindingstekenreeks nodig die uw fysieke apparaat koppelt aan de identiteit ervan in de IoT-hub.

1. Klik met de rechtermuisknop op de id van uw apparaat in de azure **IoT** Hub-sectie.
1. Selecteer **De tekenreeks Apparaatverbinding kopiëren**.

   De verbindingstekenreeks wordt gekopieerd naar uw klembord.

U **apparaatgegevens** ophalen ook selecteren in het menu met de rechtermuisknop om alle apparaatgegevens, inclusief de verbindingstekenreeks, in het uitvoervenster weer te geven.

## <a name="register-with-the-azure-cli"></a>Registreren bij de Azure CLI

De [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is een open-source cross-platform command-line tool voor het beheren van Azure-resources zoals IoT Edge. Hiermee u Azure IoT Hub-resources, apparaatinrichtingsservice-exemplaren en gekoppelde hubs out-of-the-box beheren. De IoT-extensie verrijkt Azure CLI met functies zoals apparaatbeheer en volledige IoT Edge-mogelijkheden.

### <a name="prerequisites-for-the-azure-cli"></a>Voorwaarden voor de Azure CLI

* Een [IoT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet minimaal 2.0.70 of hoger zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Een IoT Edge-apparaat maken met de Azure CLI

Gebruik de opdracht voor het maken van een nieuwe apparaatidentiteit van [az iot-hub-hub](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) om een nieuwe apparaatidentiteit in uw IoT-hub te maken. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Deze opdracht bevat drie parameters:

* **apparaat-id:** geef een beschrijvende naam op die uniek is voor uw IoT-hub.
* **hubnaam:** geef de naam van uw IoT-hub op.
* **edge-enabled:** Deze parameter verklaart dat het apparaat is voor gebruik met IoT Edge.

   ![az iot hub device-identity create output az iot hub device-identity create output az iot hub device-identity create output az iot](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>IoT Edge-apparaten weergeven met de Azure CLI

Gebruik de opdracht voor de lijst met [apparaatidentiteiten van AZ Iot-hub](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) om alle apparaten in uw IoT-hub weer te geven. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Elk apparaat dat is geregistreerd als een IoT Edge-apparaat heeft de **eigenschapsmogelijkheden.iotEdge** ingesteld op **true.**

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>De verbindingstekenreeks met de Azure CLI ophalen

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de verbindingstekenreeks nodig die uw fysieke apparaat koppelt aan de identiteit ervan in de IoT-hub. Gebruik de opdracht [show-string voor de show-string van de AZ Iot-hub-hub](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) om de verbindingstekenreeks voor één apparaat terug te sturen:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

De waarde `device-id` voor de parameter is hoofdlettergevoelig. Kopieer de aanhalingstekens rond de verbindingstekenreeks niet.

## <a name="next-steps"></a>Volgende stappen

Nu u een apparaatidentiteit hebt geregistreerd in uw IoT-hub, bent u klaar om de IoT Edge-runtime op uw apparaten te installeren. Installeer de runtime volgens het besturingssysteem van het apparaat:

* [Azure IoT Edge installeren in Windows](how-to-install-iot-edge-windows.md)
* [De Azure IoT Edge-runtime op Linux installeren](how-to-install-iot-edge-linux.md)
