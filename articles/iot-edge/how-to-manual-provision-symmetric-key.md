---
title: Inrichten met symmetrische sleutels-Azure IoT Edge | Microsoft Docs
description: Na de installatie dient u een IoT Edge apparaat met symmetrische sleutels in te richten met behulp van de connection string en te verifiëren bij IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 9e288bcbebe4118bfc8cfa7cff46c79d7075555a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979536"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>Een Azure IoT Edge apparaat instellen met behulp van symmetrische sleutel verificatie

Dit artikel bevat de stappen voor het registreren van een nieuw IoT Edge apparaat in IoT Hub en het configureren van het apparaat voor verificatie met symmetrische sleutels.

De stappen in dit artikel begeleiden u bij het proces hand matig inrichten, waarbij u elk apparaat hand matig verbindt met de IoT-hub. Het alternatief wordt automatisch ingericht met behulp van de IoT Hub Device Provisioning Service. Dit is handig als u veel apparaten hebt om in te richten.

<!--TODO: Add auto-provision info/links-->

Voor hand matige inrichting hebt u twee opties voor het verifiëren van IoT Edge apparaten:

* **Symmetrische sleutel**: wanneer u een nieuwe apparaat-id in IOT hub maakt, maakt de service twee sleutels. U plaatst een van de sleutels op het apparaat en geeft de sleutel aan IoT Hub bij het verifiëren.

  Deze verificatie methode is sneller om aan de slag te gaan, maar niet zo veilig.

* **X. 509 zelfondertekend**: u maakt twee X. 509-identiteits certificaten en plaatst deze op het apparaat. Wanneer u een nieuwe apparaat-id in IoT Hub maakt, geeft u de vinger afdrukken van beide certificaten. Wanneer het apparaat wordt geverifieerd bij IoT Hub, worden de bijbehorende certificaten weer gegeven en kan IoT Hub controleren of ze overeenkomen met de vinger afdrukken.

  Deze verificatie methode is veiliger en aanbevolen voor productie scenario's.

Dit artikel begeleidt het registratie-en inrichtings proces met symmetrische sleutel verificatie. Als u meer wilt weten over het instellen van een apparaat met X. 509-certificaten, raadpleegt u [een Azure IOT edge apparaat instellen met x. 509-certificaat verificatie](how-to-manual-provision-x509.md).

## <a name="prerequisites"></a>Vereisten

Voordat u de stappen in dit artikel uitvoert, moet u een apparaat hebben waarop de IoT Edge runtime is geïnstalleerd. Als dat niet het geval is, volgt u de stappen in [de Azure IOT Edge runtime installeren of verwijderen](how-to-install-iot-edge.md).

## <a name="register-a-new-device"></a>Een nieuw apparaat registreren

Elk apparaat dat verbinding maakt met een IoT Hub heeft een apparaat-ID die wordt gebruikt voor het bijhouden van Cloud-naar-apparaat-en apparaat-naar-Cloud-communicatie. U configureert een apparaat met de verbindings gegevens, waaronder de hostnaam van de IoT-hub, de apparaat-ID en de informatie die het apparaat gebruikt om te verifiëren bij IoT Hub.

Voor symmetrische-sleutel verificatie wordt deze informatie verzameld in een *Connection String* die u kunt ophalen uit IOT hub en vervolgens op uw IOT edge-apparaat plaatst.

U kunt verschillende hulpprogram ma's gebruiken om een nieuw IoT Edge apparaat te registreren in IoT Hub en de connection string op te halen, afhankelijk van uw voor keur.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Vereisten voor de Azure Portal

Een gratis of standaard [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Een IoT Edge apparaat maken in de Azure Portal

In uw IoT Hub in de Azure Portal worden IoT Edge apparaten afzonderlijk gemaakt en beheerd van IOT-apparaten die niet Edge zijn ingeschakeld.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.

1. Selecteer in het linkerdeel venster **IOT Edge** in het menu en selecteer vervolgens **een IOT edge apparaat toevoegen**.

   ![Een IoT Edge apparaat toevoegen vanuit de Azure Portal](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Geef op de pagina **een apparaat maken** de volgende informatie op:

   * Een beschrijvende apparaat-ID maken.
   * Selecteer **symmetrische sleutel** als het verificatie type.
   * Gebruik de standaard instellingen voor het automatisch genereren van verificatie sleutels en het verbinden van het nieuwe apparaat met uw hub.

1. Selecteer **Opslaan**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>IoT Edge apparaten weer geven in de Azure Portal

Alle apparaten met rand mogelijkheden die verbinding maken met uw IoT-hub, worden weer gegeven op de pagina **IOT Edge** .

![De Azure Portal gebruiken om alle IoT Edge apparaten in uw IoT-hub weer te geven](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>De connection string in het Azure Portal ophalen

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de connection string nodig die het fysieke apparaat koppelt aan de identiteit in de IoT-hub.

1. Klik op de pagina **IOT Edge** in de portal op de apparaat-id in de lijst met IOT edge apparaten.
2. Kopieer de waarde van de **primaire verbindings reeks** of **secundaire verbindings reeks**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Vereisten voor Visual Studio code

* Een gratis of standaard [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) voor Visual Studio code

### <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan voor toegang tot uw IoT-hub

U kunt de Azure IoT-uitbrei dingen voor Visual Studio code gebruiken om bewerkingen met uw IoT Hub uit te voeren. Als u deze bewerkingen wilt gebruiken, moet u zich aanmelden bij uw Azure-account en uw IoT Hub selecteren.

1. Open in Visual Studio code de weer gave **Explorer** .
1. Vouw de sectie **Azure-IOT hub** aan de onderkant van de Explorer uit.

   ![Sectie Azure IoT Hub-apparaten uitvouwen](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

1. Klik in de koptekst van de Azure- **IOT hub** op de sectie **..** .. Als u het weglatings teken niet ziet, klikt u op de koptekst of beweegt u de muis aanwijzer over de kop.
1. Kies **IOT hub selecteren**.
1. Als u niet bent aangemeld bij uw Azure-account, volgt u de aanwijzingen om dit te doen.
1. Selecteer uw Azure-abonnement.
1. Selecteer uw IoT-hub.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Een IoT Edge-apparaat maken met Visual Studio code

1. Vouw in de VS code Explorer het gedeelte **Azure IOT Hub-apparaten** uit.
1. Klik op de sectie **..** . in de koptekst van de **Azure IOT Hub-apparaten** . Als u het weglatings teken niet ziet, klikt u op de koptekst of beweegt u de muis aanwijzer over de kop.
1. Selecteer **IOT edge apparaat maken**.
1. Geef in het tekstvak dat wordt geopend, een ID op voor uw apparaat.

In het uitvoer scherm ziet u het resultaat van de opdracht. De apparaatgegevens worden afgedrukt, inclusief de **deviceId** die u hebt geleverd en de **Connections Tring** die u kunt gebruiken om uw fysieke apparaat te verbinden met uw IOT-hub.

In het uitvoer scherm ziet u het resultaat van de opdracht. De apparaatgegevens worden afgedrukt, inclusief de **deviceId** die u hebt geleverd en de **Connections Tring** die u kunt gebruiken om uw fysieke apparaat te verbinden met uw IOT-hub.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>IoT Edge apparaten weer geven met Visual Studio code

Alle apparaten die verbinding maken met uw IoT-hub, worden weer gegeven in de sectie **Azure IOT hub** van Visual Studio code Explorer. IoT Edge apparaten zijn onderscheiden van niet-rand apparaten met een ander pictogram, en het feit dat de **$edgeAgent** en **$edgeHub** modules op elk IOT edge apparaat worden geïmplementeerd.

![VS code gebruiken om alle IoT Edge apparaten in uw IoT-hub weer te geven](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>De connection string ophalen met Visual Studio code

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de connection string nodig die het fysieke apparaat koppelt aan de identiteit in de IoT-hub.

1. Klik met de rechter muisknop op de ID van uw apparaat in het gedeelte **Azure IOT hub** .
1. Selecteer **apparaat verbindings reeks kopiëren**.

   De connection string wordt naar het klem bord gekopieerd.

U kunt ook **apparaatgegevens ophalen** selecteren in het menu met de rechter muisknop om alle apparaatgegevens, inclusief de Connection String, weer te geven in het uitvoer venster.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Vereisten voor de Azure CLI

* Een [IOT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet mini maal 2.0.70 of nieuwer zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Een IoT Edge-apparaat maken met de Azure CLI

Gebruik de opdracht [AZ IOT hub apparaat-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) om een nieuwe apparaat-id te maken in uw IOT-hub. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Deze opdracht omvat drie para meters:

* `--device-id` of `-d` : Geef een beschrijvende naam op die uniek is voor uw IOT-hub.
* `hub-name` of `-n` : Geef de naam op van uw IOT-hub.
* `--edge-enabled` of `--ee` : Declareer dat het apparaat een IOT edge apparaat is.

   ![AZ IOT hub apparaat-Identity Create output](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>IoT Edge apparaten weer geven met de Azure CLI

Gebruik de opdracht [AZ IOT hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) om alle apparaten in uw IOT-hub weer te geven. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Elk apparaat dat is geregistreerd als IoT Edge apparaat heeft de eigenschaps **mogelijkheden. iotEdge** ingesteld op **True**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>De connection string ophalen met de Azure CLI

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de connection string nodig die het fysieke apparaat koppelt aan de identiteit in de IoT-hub. Gebruik de opdracht [AZ IOT hub Device-identiteit show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) om de Connection String voor één apparaat te retour neren:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

De waarde voor de `device-id` para meter is hoofdletter gevoelig. Kopieer de aanhalings tekens rond het connection string niet.

---

## <a name="provision-an-iot-edge-device"></a>Een IoT Edge-apparaat inrichten

Zodra het IoT Edge apparaat een identiteit in IoT Hub heeft en een connection string die voor verificatie kan worden gebruikt, moet u het apparaat zelf inrichten met deze gegevens.

Op een Linux-apparaat geeft u de connection string op door een config. yaml-bestand te bewerken. Op een Windows-apparaat geeft u de connection string op door een Power shell-script uit te voeren.

# <a name="linux"></a>[Linux](#tab/linux)

Open het configuratie bestand op het IoT Edge apparaat.

```bash
sudo nano /etc/iotedge/config.yaml
```

Zoek de inrichtings configuraties van het bestand en verwijder de **hand matige inrichtings configuratie met behulp van een Connection String** sectie. 

Werk de waarde van **device_connection_string** met de Connection String van uw IOT edge-apparaat bij. Zorg ervoor dat alle andere inrichtings secties van commentaar zijn. Zorg ervoor dat het **inrichten:** de regel heeft geen voorafgaande spatie en dat geneste items met twee spaties worden inge sprongen.

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

De inhoud van het klem bord plakken in nano `Shift+Right Click` of Press `Shift+Insert` .

Sla het bestand op en sluit het.

   `CTRL + X`, `Y`, `Enter`

Nadat u de inrichtings gegevens in het configuratie bestand hebt ingevoerd, start u de daemon opnieuw op:

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. Voer Power shell uit als beheerder op het IoT Edge-apparaat.

2. Gebruik de opdracht [initialiseren-IoTEdge](reference-windows-scripts.md#initialize-iotedge) om de IOT Edge runtime op uw computer te configureren. De opdracht wordt standaard ingesteld op handmatig inrichten met Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Als u Linux-containers gebruikt, voegt u de `-ContainerOs` para meter toe aan de vlag. Wees consistent met de container optie die u hebt gekozen met de `Deploy-IoTEdge` opdracht die u eerder hebt uitgevoerd.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * Als u het IoTEdgeSecurityDaemon.ps1 script op uw apparaat hebt gedownload voor offline of installatie van een specifieke versie, moet u ervoor zorgen dat u naar de lokale kopie van het script verwijst.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Wanneer u hierom wordt gevraagd, geeft u de connection string op van het apparaat dat u in de vorige sectie hebt opgehaald. Het apparaat connection string het fysieke apparaat koppelt aan een apparaat-ID in IoT Hub en biedt verificatie-informatie.

   De connection string van het apparaat heeft de volgende indeling en mag geen aanhalings tekens bevatten: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Wanneer u een apparaat hand matig inricht, kunt u extra para meters gebruiken om het proces te wijzigen, met inbegrip van:

* Direct verkeer om een proxy server te passeren
* Een specifieke edgeAgent-container installatie kopie declareren en referenties opgeven als deze zich in een persoonlijk REGI ster bevinden

Zie [Power shell-scripts voor IOT Edge in Windows](reference-windows-scripts.md)voor meer informatie over deze aanvullende para meters.

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Volgende stappen

Ga door met het [implementeren van IOT Edge-modules](how-to-deploy-modules-portal.md) voor meer informatie over het implementeren van modules op uw apparaat.
