---
title: Apparaten die zijn verbonden met Externe bewaking bulksgewijs beheren - Azure | Microsoft Docs
description: In deze zelfstudie leert u hoe u de apparaten die zijn verbonden met een externe bewakingsoplossing bulksgewijs kunt beheren.
author: Philmea
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: philmea
ms.openlocfilehash: eaca93ac8a4e8c660be9618aefb27921a4e0a2eb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77565575"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Zelfstudie: Uw verbonden apparaten bulksgewijs beheren

In deze zelfstudie gebruikt u de verbetering voor de externe bewakingsoplossing om de configuratie van uw verbonden apparaten bulksgewijs te beheren.

Als operator bij Contoso moet u een groep apparaten configureren met een nieuwe firmwareversie. U wilt de firmware niet op elk apparaat afzonderlijk bijwerken. Om de firmware op een groep apparaten bij te werken, kunt u apparaatgroepen en automatisch apparaatbeheer gebruiken in de verbetering voor de externe bewakingsoplossing. Elk apparaat dat u aan de apparaatgroep toevoegt, ontvangt de meest recente firmware zodra het apparaat online komt.

In deze zelfstudie hebt u:

>[!div class="checklist"]
> * Een apparaatgroep maken
> * De firmware voorbereiden en hosten
> * Een apparaatconfiguratie maken in de Azure-portal
> * Een apparaatconfiguratie importeren in uw externe bewakingsoplossing
> * De configuratie implementeren op de apparaten in de apparaatgroep
> * De implementatie bewaken

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u een geïmplementeerd exemplaar van de verbetering voor de externe bewakingsoplossing in uw Azure-abonnement nodig.

Als u de verbetering voor de externe bewakingsoplossing nog niet hebt geïmplementeerd, voltooit u eerst de snelstart [Een externe bewakingsoplossing in de cloud implementeren](quickstart-remote-monitoring-deploy.md).

U hebt een Azure-opslagaccount nodig om de firmwarebestanden op te slaan. U kunt een bestaand opslagaccount gebruiken of [een nieuw opslagaccount maken](../storage/common/storage-account-create.md) in uw abonnement.

In de zelfstudie wordt het apparaat [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) als voorbeeldapparaat gebruikt.

De volgende software moet op uw lokale computer zijn geïnstalleerd:

* [Visual Studio Code (VS Code)](https://code.visualstudio.com/).
* De VS Code-extensie [ van Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench).

Voordat u begint:

* Controleer of de [bootloader op uw IoT DevKit-apparaat versie 1.4.0 of hoger](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) is.
* Controleer of de SDK van IoT DevKit dezelfde versie heeft als de bootloader. U kunt de SDK van IoT DevKit bijwerken met behulp van de Azure IoT Workbench in VS Code. Open het opdrachtenpalet en voer **Arduino: Board Manager** in. Zie [Prepare the development environment](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment) (De ontwikkelomgeving voorbereiden) voor meer informatie.

U hebt ook minimaal één IoT DevKit-apparaat met de verbetering voor de externe bewakingsoplossing nodig. Als u nog geen IoT DevKit-apparaat hebt verbonden, zie dan [Connect MXChip IoT DevKit AZ3166 to the IoT Remote Monitoring solution accelerator](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md) (MXChip IoT DevKit AZ3166 verbinden met de IoT-verbetering voor de externe bewakingsoplossing).

## <a name="navigate-to-the-dashboard"></a>Naar het dashboard navigeren

Als u het dashboard van de externe bewakingsoplossing wilt weergeven in uw browser, navigeert u eerst naar [Microsoft Azure IoT-oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators#dashboard). Mogelijk wordt u gevraagd u aan te melden met de referenties van uw Azure-abonnement.

Klik vervolgens op **Starten** op de tegel van de verbetering voor de externe bewakingsoplossing die u hebt geïmplementeerd in de [Snelstart](quickstart-remote-monitoring-deploy.md).

## <a name="create-a-device-group"></a>Een apparaatgroep maken

Als u de firmware automatisch wilt bijwerken op een groep apparaten, moeten de apparaten lid zijn van een apparaatgroep in uw externe bewakingsoplossing:

1. Selecteer op de pagina **Apparaten** alle **IoT DevKit**-apparaten die u hebt verbonden met de oplossingsverbetering. Klik vervolgens op **taken**.

1. In het deel venster **taken** selecteert u **Tags**, stelt u de taak naam in op **AddDevKitTag**en voegt u vervolgens een tekst code toe met de naam **IsDevKitDevice** met een waarde van **Y**. Klik vervolgens op **Toep assen**.

1. Nu kunt u de tagwaarden gebruiken om een apparaatgroep te maken. Klik op de pagina **apparaten** op **Apparaatgroepen beheren**.

1. Maak een tekstfilter dat de tagnaam **IsDevKitDevice** en de waarde **Y** in de voorwaarde gebruikt. Sla apparaatgroep op als **IoT DevKit-apparaten**.

Verderop in deze zelfstudie gaat u op deze apparaatgroep een apparaatconfiguratie toepassen waarmee de firmware van alle leden wordt bijgewerkt.

## <a name="prepare-and-host-the-firmware"></a>De firmware voorbereiden en hosten

De VS Code-extensie van [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) bevat de voorbeeldcode voor de firmware-update van het apparaat.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Open het Firmware OTA-voorbeeld in VS Code

1. Zorg dat uw IoT DevKit niet is verbonden met uw computer. Start VS Code en verbind de DevKit verbinden met uw computer.

1. Druk op **F1** om het opdrachtenpalet te openen. Typ of selecteer **IoT Workbench: Examples**. Selecteer **IoT DevKit** als het bord.

1. Zoek **Firmware OTA** en klik op **Open Sample**. In een nieuw VS Code-venster wordt de projectmap **firmware_ota** weergegeven:

    ![IoT Workbench, selecteer Firmware OTA-voorbeeld](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>De nieuwe firmware bouwen

De eerste versie van de apparaatfirmware is 1.0.0. De nieuwe firmware moet een hoger versienummer hebben.

1. Open het bestand **FirmwareOTA.ino** in VS Code en wijzig de `currentFirmwareVersion` van `1.0.0` in `1.0.1`:

    ![Firmwareversie wijzigen](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Open het opdrachtenpalet en typ of selecteer **IoT Workbench: Device**. Selecteer vervolgens **Device Compile** om de code te compileren:

    ![Device compile](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    Het gecompileerde bestand wordt in VS Code opgeslagen in de map `.build` van het project. Afhankelijk van uw instellingen is het mogelijk dat de map `.build` in VS Code in de Verkenner wordt verborgen.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>De CRC-waarde genereren en de grootte van het firmwarebestand berekenen

1. Open het opdrachtenpalet en typ of selecteer **IoT Workbench: Device**. Selecteer vervolgens **Generate CRC**:

    ![Generate CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. In VS Code worden de CRC-waarde, de bestandsnaam en het pad van de firmware, en de bestandsgrootte gegenereerd en weergegeven in het uitvoervenster. Noteer deze waarden voor later:

    ![CRC-info](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>De firmware uploaden naar de cloud

Gebruik het Azure storage-account om het nieuwe firmwarebestand te hosten in de cloud.

1. Ga in Azure Portal naar uw opslagaccount. Selecteer in het gedeelte Services de optie **Blobs**. Maak een openbare container met de naam **Firmware** voor het opslaan van uw firmwarebestanden:

    ![Map maken](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. U uploadt het firmwarebestand naar de container door de container **Firmware** te selecteren en op **Upload** te klikken.

1. Selecteer **FirmwareOTA.ino.bin**. U hebt in de vorige sectie het volledige pad naar dit bestand genoteerd.

1. Wanneer het firmwarebestand volledig is geüpload, noteert u de URL van het bestand.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>De oorspronkelijke firmware bouwen en uploaden naar het IoT DevKit-apparaat

1. Open het bestand **FirmwareOTA.ino** in VS Code en zet de `currentFirmwareVersion` weer terug op `1.0.0`:

    ![Versie 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Open het opdrachtenpalet en typ of selecteer **IoT Workbench: Device**. Selecteer vervolgens **Device Upload**:

    ![Device Upload](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. In VS Code wordt de code gecontroleerd en geüpload naar het IoT DevKit-apparaat.

1. Wanneer het uploaden is voltooid, wordt het IoT DevKit-apparaat opnieuw opgestart. Wanneer het apparaat opnieuw is opgestart, wordt in het scherm van IoT DevKit **FW-versie: 1.0.0** weergegeven en ziet u dat er op nieuwe firmware wordt gecontroleerd:

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Een apparaatconfiguratie maken

Een apparaatconfiguratie geeft de gewenste status van uw apparaten aan. Normaal gesproken is het de ontwikkelaar die [een configuratie maakt](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) op de pagina **IoT-apparaatconfiguratie** van de Azure-portal. Een apparaatconfiguratie is een JSON-document dat de gewenste status van uw apparaten en een set metrische gegevens bevat.

Sla de volgende configuratie als een bestand met de naam **firmware-update.json** op uw lokale computer op. Vervang de tijdelijke aanduidingen voor `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM` en `YOURPACKAGESIZE` door de waarden die u eerder hebt genoteerd:

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

U gebruikt dit configuratiebestand in de volgende sectie.

## <a name="import-a-configuration"></a>Een configuratie importeren

In deze sectie importeert u de apparaatconfiguratie als een pakket in de verbetering voor de externe bewakingsoplossing. Normaal gesproken wordt deze taak voltooid door een operator.

1. Navigeer in de webgebruikersinterface van Externe bewaking naar de pagina **Pakketten** en klik op **+ Nieuw pakket**:

    ![Nieuw pakket](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. In het deelvenster **Nieuw pakket** kiest u **Apparaatconfiguratie** als het pakkettype en **Firmware** als het configuratietype. Klik op **Bladeren** om het bestand **firmware-update.json** op uw lokale computer te zoeken en klik vervolgens op **Uploaden**:

    ![Pakket uploaden](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. De lijst met pakketten bevat nu het pakket **firmware-update**.

## <a name="deploy-the-configuration-to-your-devices"></a>De configuratie implementeren op uw apparaten

In deze sectie gaat u een implementatie maken en uitvoeren die de apparaatconfiguratie toepast op uw IoT DevKit-apparaten.

1. Navigeer in de webgebruikersinterface van Externe bewaking naar de pagina **Implementaties** en klik op **+ Nieuwe implementatie**:

    ![Nieuwe implementatie](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. Maak in het deelvenster **Nieuwe implementatie** een implementatie met de volgende instellingen:

    |Optie|Waarde|
    |---|---|
    |Naam|Firmware-update implementeren|
    |Pakkettype|Apparaatconfiguratie|
    |Configuratietype|Firmware|
    |Pakket|firmware-update.json|
    |Apparaatgroep|IoT DevKit-apparaten|
    |Prioriteit|10|

    ![Implementatie maken](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Klik op **Toepassen**. Op de pagina **Implementaties** ziet u een nieuwe implementatie die de volgende metrische gegevens bevat:

    * **Gericht** toont het aantal apparaten in de groep met apparaten.
    * **Toegepast** toont het aantal apparaten die is bijgewerkt met de configuratie-inhoud.
    * **Geslaagd** toont het aantal apparaten in de implementatie waarvoor de implementatie is geslaagd.
    * **Mislukt** toont het aantal apparaten in de implementatie waarvoor de implementatie is mislukt.

## <a name="monitor-the-deployment"></a>De implementatie bewaken

Na enkele minuten ontvangt de IoT DevKit de informatie van de nieuwe firmware en wordt het downloaden ervan naar het apparaat gestart:

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

Afhankelijk van de snelheid van uw netwerk kan het downloaden enkele minuten duren. Wanneer de firmware is gedownload, worden de bestandsgrootte en CRC-waarde op het apparaat gecontroleerd. In het scherm op de MXChip wordt **passed** weergegeven als de controle is geslaagd.

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Als de controle is geslaagd, wordt het apparaat opnieuw opgestart. U ziet een aftelling van **5** naar **0** voordat het apparaat opnieuw wordt opgestart.

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Wanneer het apparaat opnieuw is opgestart, wordt er een upgrade van de IoT DevKit bootloader uitgevoerd naar de nieuwe versie. De upgrade kan enkele seconden duren. In deze fase is het RGB-lampje van het apparaat rood en is het scherm leeg.

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Wanneer het apparaat opnieuw is opgestart, werkt het IoT DevKit-apparaat met versie 1.0.1 van de firmware.

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Klik op de pagina **Implementaties** op een implementatie om de status van uw apparaten te zien wanneer ze worden bijgewerkt. U kunt de status van elk apparaat in de apparaatgroep en de door u opgegeven aangepaste metrische gegevens bekijken.

![Implementatiedetails](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gezien hoe u de firmware bijwerkt van een groep apparaten die zijn verbonden met uw oplossing. Uw oplossing maakt gebruik van automatische apparaatbeheer voor het bijwerken van de apparaten. Voor meer informatie over de functie voor automatisch apparaatbeheer in de onderliggende IoT Hub van uw oplossing raadpleegt u [Configure and monitor IoT devices at scale using the Azure portal](../iot-hub/iot-hub-auto-device-config.md) (IoT-apparaten op schaal configureren en controleren met de Azure-portal).