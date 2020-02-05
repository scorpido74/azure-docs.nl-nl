---
title: Een Azure IoT Edge apparaat toevoegen aan Azure IoT Central | Microsoft Docs
description: Als operator kunt u een Azure IoT Edge apparaat toevoegen aan uw Azure IoT Central-toepassing
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: bf74784998de6bbad6310c48c24d6353512bff13
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027732"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Zelf studie: een Azure IoT Edge apparaat toevoegen aan uw Azure IoT Central-toepassing

Deze zelf studie laat zien hoe u een Azure IoT Edge apparaat kunt configureren en toevoegen aan uw Azure IoT Central-toepassing. In de zelf studie wordt een door de IoT Edge ingeschakelde Linux-virtuele machine (VM) van Azure Marketplace gebruikt om een IoT Edge apparaat te simuleren. Het IoT Edge apparaat gebruikt een module die gesimuleerde telemetrie van een omgeving genereert. U kunt de telemetrie bekijken in een dash board in uw IoT Central-toepassing.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een sjabloon voor een apparaat maken voor een IoT Edge apparaat
> * Een IoT Edge apparaat maken in IoT Central
> * Een gesimuleerd IoT Edge-apparaat implementeren op een virtuele Linux-machine

## <a name="prerequisites"></a>Vereisten

Voltooi de Snelstartgids [een Azure IOT Central-toepassing maken](./quick-deploy-iot-central.md) om een IOT Central toepassing te maken met behulp van de aangepaste **app > aangepaste toepassings** sjabloon.

Voor het volt ooien van de stappen in deze zelf studie hebt u een actief Azure-abonnement nodig.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Down load het IoT Edge-manifest bestand vanuit GitHub. Klik met de rechter muisknop op de volgende koppeling en selecteer vervolgens **Koppeling opslaan als**: [EnvironmentalSensorManifest. json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Sjabloon voor het maken van een apparaat

In deze sectie maakt u een sjabloon voor een IoT Edge apparaat dat verbinding maakt met uw IoT Central-toepassing. U importeert een IoT Edge-manifest om aan de slag te gaan en wijzigt vervolgens de sjabloon om telemetriegegevens en weer gaven toe te voegen:

### <a name="import-manifest-to-create-template"></a>Manifest importeren om sjabloon te maken

Een sjabloon voor een apparaat maken vanuit een IoT Edge-manifest:

1. Navigeer in uw IoT Central-toepassing naar **Apparaatinstellingen** en selecteer **+ Nieuw**.

1. Selecteer op de pagina **sjabloon type selecteren** de tegel **Azure IOT Edge** . Selecteer vervolgens **volgende: aanpassen**.

1. Selecteer op de pagina **een Azure IOT Edge implementatie manifest uploaden** de optie **Bladeren** om de **EnvironmentalSensorManifest. json** te uploaden die u eerder hebt gedownload. Selecteer **volgende: controleren**.

1. Op de pagina **controleren** selecteert u **maken**.

1. Wanneer de sjabloon is gemaakt, wijzigt u de naam ervan in het *apparaat voor omgevings sensor rand*.

1. Selecteer in de **SimulatedTemperatureSensor** -module de optie **Manage** interface om de twee eigenschappen te bekijken die in het manifest zijn gedefinieerd:

![De apparaatprofiel is gemaakt op basis van IoT Edge manifest](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Telemetrie aan manifest toevoegen

Een IoT Edge-manifest definieert niet de telemetrie die een module verzendt. U moet de telemetrie-definities toevoegen aan de sjabloon voor het apparaat. De **SimulatedTemperatureSensor** -module verzendt telemetrie-berichten die eruitzien als de volgende JSON:

```json
{
    "machine": {
        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

De telemetrie-definities toevoegen aan de sjabloon voor het apparaat:

1. Selecteer de interface **beheren** in het **apparaat sjabloon rand van omgevings sensor** .

1. Selecteer **+ toevoegen**. Voer de *computer* in als **weergave naam** en zorg ervoor dat het **capaciteits type** **telemetrie**is.

1. Selecteer **object** als schema type en selecteer vervolgens **definiëren**. Voeg op de pagina object definitie de *Tempe ratuur* en *Druk* toe als kenmerken van het type **Double** en selecteer vervolgens **Toep assen**.

1. Selecteer **+ toevoegen**. Voer een *omgeving* in als **weergave naam** en zorg ervoor dat het **capaciteits type** **telemetrie**is.

1. Selecteer **object** als schema type en selecteer vervolgens **definiëren**. Voeg op de pagina object definitie de *Tempe ratuur* en *vochtigheid* toe als kenmerken van het type **Double** en selecteer vervolgens **Toep assen**.

1. Selecteer **+ toevoegen**. Voer *TimeCreated* in als **weergave naam** en zorg ervoor dat het **capaciteits type** **telemetrie**is.

1. Selecteer **DateTime** als schema type.

1. Selecteer **Opslaan** om de sjabloon bij te werken.

De **beheer** interface bevat nu de telemetrie-typen **machine**, **omgeving**en **TimeCreated** :

![Interface met computer-en omgevings telemetrie-typen](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Weer gaven toevoegen aan sjabloon

De sjabloon voor het apparaat heeft nog geen weer gave waarmee een operator de telemetrie van het IoT Edge apparaat kan zien. Een weer gave toevoegen aan de sjabloon voor het apparaat:

1. Selecteer **weer gaven** in het **apparaat sjabloon rand van omgevings sensor** .

1. Selecteer op de pagina **selecteren om een nieuwe weer gave toe te voegen** de tegel **apparaat visualiseren** .

1. Wijzig de weergave naam om de *telemetrie van IOT edge apparaat weer te geven*.

1. Selecteer de typen **omgeving** en **machine** telemetrie. Selecteer vervolgens **tegel toevoegen**.

1. Selecteer **Opslaan** om de **weer gave IOT Edge de telemetrie** van het apparaat op te slaan.

![Device-sjabloon met telemetrie-weer gave](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>De sjabloon publiceren

Voordat u een apparaat kunt toevoegen dat gebruikmaakt van de sjabloon **omgevings rand apparaat** , moet u de sjabloon publiceren.

Ga naar de sjabloon voor het apparaat van de **omgevings sensor rand** en selecteer **publiceren**. Selecteer vervolgens **publiceren** om de sjabloon te publiceren:

![De sjabloon voor het apparaat publiceren](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>IoT Edge apparaat toevoegen

Nu u de sjabloon **omgevings rand apparaat** hebt gepubliceerd, kunt u een apparaat toevoegen aan uw IOT Central-toepassing:

1. Navigeer in uw IoT Central-toepassing naar de pagina **apparaten** en selecteer **omgevings sensor rand apparaat** in de lijst met beschik bare sjablonen.

1. Selecteer **+** om een nieuw apparaat uit de sjabloon toe te voegen. Selecteer op de pagina **nieuw apparaat** maken de optie **maken**.

U hebt nu een nieuw apparaat met de status **geregistreerd**:

![De sjabloon voor het apparaat publiceren](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>De referenties van het apparaat ophalen

Wanneer u het IoT Edge-apparaat later in deze zelf studie implementeert, hebt u de referenties nodig waarmee het apparaat verbinding kan maken met uw IoT Central-toepassing. De referenties van het apparaat ophalen:

1. Selecteer op de pagina **apparaat** het apparaat dat u hebt gemaakt.

1. Selecteer **Verbinden**.

1. Noteer de **id-Scope**, de **apparaat-id**en de **primaire sleutel**op de pagina **apparaat-verbinding** . U kunt deze waarden later gebruiken.

1. Selecteer **Sluiten**.

U bent nu klaar met het configureren van uw IoT Central-toepassing om een IoT Edge apparaat in te scha kelen om verbinding te maken.

## <a name="deploy-an-iot-edge-device"></a>Een IoT Edge-apparaat implementeren

In deze zelf studie gebruikt u een met Azure IoT Edge ingeschakelde Linux-VM die is gemaakt in azure om een IoT Edge apparaat te simuleren. De met IoT Edge ingeschakelde VM maken:

1. Navigeer naar [Azure IOT Edge op Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) in azure Marketplace. Selecteer **nu ophalen**.

1. Op de pagina **deze app maken in azure** selecteert u **door gaan**. Met deze koppeling gaat u naar de Azure Portal, waar u zich mogelijk moet aanmelden bij uw Azure-abonnement.

1. Selecteer op de pagina **Azure IOT Edge op Ubuntu** in de Azure Portal **maken**.

1. Op de pagina basis pagina's voor het **maken van een virtuele machine >** :

    - Selecteer uw Azure-abonnement.
    - Maak een nieuwe resource groep met de naam **IOT-Edge-devices**.
    - De naam van de virtuele machine gebruiken: **iotedgevm**.
    - Kies de dichtstbijzijnde regio voor u.
    - Stel het verificatie type in op **wacht woord**.
    - Kies een gebruikers naam en wacht woord.
    - U kunt de andere opties op de standaard waarden laten staan.
    - Selecteer **Controleren + maken**.

1. Wanneer de validatie is voltooid, selecteert u **maken**.

Nadat de implementatie is voltooid, selecteert u na enkele minuten **naar resource**.

### <a name="provision-vm-as-an-iot-edge-device"></a>VM inrichten als een IoT Edge apparaat 

Als u een virtuele machine als IoT Edge apparaat wilt inrichten:

1. Selecteer **seriële console**in het gedeelte **ondersteuning en probleem oplossing** .

1. Druk op **Enter** om de `login:` prompt weer te geven. Voer uw gebruikers naam en wacht woord in om u aan te melden.

1. Voer de volgende opdracht uit om de runtime versie van IoT Edge te controleren. Op het moment van schrijven is de versie 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Gebruik de `nano` editor om het bestand IoT Edge config. yaml te openen:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Schuif omlaag totdat u `# Manual provisioning configuration`ziet. Commentaar bij de volgende drie regels, zoals wordt weer gegeven in het volgende code fragment:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Schuif omlaag totdat u `# DPS symmetric key provisioning configuration`ziet. Verwijder de opmerking bij de volgende acht regels, zoals wordt weer gegeven in het volgende code fragment:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Vervang `{scope_id}` door het **id-bereik** voor u eerder een notitie hebt gemaakt.

1. Vervang `{registration_id}` door de **apparaat-id** die u eerder hebt genoteerd.

1. Vervang `{symmetric_key}` door de **primaire sleutel** die u eerder hebt genoteerd.

1. Sla de wijzigingen op (**CTRL-O**) en sluit (**CTRL-X**) de `nano` editor.

1. Voer de volgende opdracht uit om de IoT Edge-daemon opnieuw te starten:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Voer de volgende opdracht uit om de status van de IoT Edge modules te controleren:

    ```bash
    iotedge list
    ```

    De uitvoer ziet er als volgt uit:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>De telemetrie weer geven

Het gesimuleerde IoT Edge apparaat wordt nu uitgevoerd in de virtuele machine. In uw IoT Central-toepassing wordt de apparaatstatus nu **ingericht** op de pagina **apparaten** :

![Ingericht apparaat](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

U kunt de telemetrie bekijken op de pagina **IOT edge apparaat-telemetrie** :

![Telemetrie van apparaat](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

Op de pagina **modules** wordt de status van de IOT Edge modules weer gegeven:

![Telemetrie van apparaat](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u IoT Edge apparaten in IoT Central kunt gebruiken en beheren, volgt u de voorgestelde volgende stap:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Transparante gateway configureren](../../iot-edge/how-to-create-transparent-gateway.md)
