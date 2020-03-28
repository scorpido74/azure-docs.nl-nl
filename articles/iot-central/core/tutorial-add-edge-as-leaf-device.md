---
title: Een Azure IoT Edge-apparaat toevoegen aan Azure IoT Central | Microsoft Documenten
description: Voeg als operator een Azure IoT Edge-apparaat toe aan uw Azure IoT Central-toepassing
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: bf74784998de6bbad6310c48c24d6353512bff13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77027732"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Zelfstudie: Een Azure IoT Edge-apparaat toevoegen aan uw Azure IoT Central-toepassing

In deze zelfstudie ziet u hoe u een Azure IoT Edge-apparaat configureert en toevoegt aan uw Azure IoT Central-toepassing. De zelfstudie maakt gebruik van een IoT Edge-virtuele machine (VM) van Azure Marketplace om een IoT Edge-apparaat te simuleren. Het IoT Edge-apparaat maakt gebruik van een module die gesimuleerde omgevingstelemetrie genereert. U bekijkt de telemetrie op een dashboard in uw IoT Central-toepassing.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een apparaatsjabloon maken voor een IoT Edge-apparaat
> * Een IoT Edge-apparaat maken in IoT Central
> * Een gesimuleerd IoT Edge-apparaat implementeren op een Linux-vm

## <a name="prerequisites"></a>Vereisten

Voltooi de [snel start--toepassing Een Azure IoT Central](./quick-deploy-iot-central.md) maken om een IoT Central-toepassing te maken met de **aangepaste app > aangepaste toepassingssjabloon.**

Als u de stappen in deze zelfstudie wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

Download het IoT Edge-manifestbestand van GitHub. Klik met de rechtermuisknop op de volgende link en selecteer **vervolgens Koppeling opslaan als**: [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Apparaatsjabloon maken

In deze sectie maakt u een apparaatsjabloon voor een IoT Edge-apparaat dat verbinding maakt met uw IoT Central-toepassing. U importeert een IoT Edge-manifest om aan de slag te gaan en wijzigt vervolgens de sjabloon om telemetriedefinities en -weergaven toe te voegen:

### <a name="import-manifest-to-create-template"></a>Manifest importeren om sjabloon te maken

Ga als lid van het nieuws naar een apparaatsjabloon uit een IoT Edge-manifest:

1. Navigeer in uw IoT Central-toepassing naar **apparaatsjablonen** en selecteer **+ Nieuw**.

1. Selecteer op de pagina **Sjabloontype selecteren** de tegel **Azure IoT Edge.** Selecteer **vervolgens Volgende: Aanpassen**.

1. Selecteer Op de pagina **Een Azure IoT Edge-implementatiemanifest** de optie **Bladeren** om het **eerder gedownloade EnvironmentalSensorManifest.json** te uploaden. Selecteer **vervolgens Volgende: Controleren**.

1. Selecteer **op** de pagina Controleren de optie **Maken**.

1. Wanneer de sjabloon is gemaakt, wijzigt u de naam in *Het Randapparaat van de Rand van de Milieusensor*.

1. Selecteer de interface **Beheren** in de module **SimulatedTemperatureSensor** om de twee eigenschappen weer te geven die in het manifest zijn gedefinieerd:

![Apparaatsjabloon gemaakt op het IoT Edge-manifest](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Telemetrie toevoegen aan manifest

Een IoT Edge-manifest definieert niet de telemetrie die een module verzendt. U moet de telemetriedefinities toevoegen aan de apparaatsjabloon. De module **SimulatedTemperatureSensor** verzendt telemetrieberichten die op de volgende JSON lijken:

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

Ga als u de telemetriedefinities toevoegen aan de apparaatsjabloon:

1. Selecteer de interface **Beheren** in de sjabloon **Sensor Edge-apparaat van de omgevingssensor.**

1. Selecteer **+ Mogelijkheden toevoegen**. Voer *de machine* in als de naam **Weergave** en controleer of het type **Mogelijkheid** **telemetrie**is.

1. Selecteer **Object** als schematype en selecteer **Vervolgens Definiëren**. Voeg op de pagina objectdefinitie *temperatuur* en *druk* toe als kenmerken van het type **Dubbel** en selecteer **Vervolgens Toepassen**.

1. Selecteer **+ Mogelijkheden toevoegen**. Voer *de ambient* in als de naam **Weergave** en controleer of het type **Mogelijkheid** **telemetrie**is.

1. Selecteer **Object** als schematype en selecteer **Vervolgens Definiëren**. Voeg op de pagina objectdefinitie *temperatuur* en *vochtigheid* toe als kenmerken van het type **Dubbel** en selecteer **Vervolgens Toepassen**.

1. Selecteer **+ Mogelijkheden toevoegen**. Voer *tijd in Die is gemaakt* als **weergavenaam** en controleer of het **type Mogelijkheid** **telemetrie**is.

1. Selecteer **DateTime** als schematype.

1. Selecteer **Opslaan** om de sjabloon bij te werken.

De interface **Beheren** bevat nu de **typen machine,** **ambient**en **timeCreated:**

![Interface met machine- en omgevingstelemetrietypen](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Weergaven toevoegen aan sjabloon

De apparaatsjabloon heeft nog geen weergave waarmee een operator de telemetrie van het IoT Edge-apparaat kan zien. Ga als lid van het apparaat met een weergave naar de apparaatsjabloon:

1. Selecteer **Weergaven** in de sjabloon **Sensor Edge-apparaat van de omgevingssensor.**

1. Selecteer **op** de pagina Selecteren om een nieuwe weergave toe te voegen de tegel Het visualiseren van de **apparaattegel.**

1. Wijzig de weergavenaam in *IoT Edge-apparaattelemetrie weergeven*.

1. Selecteer de **typen omgevings-** en machinetelemetrie. **machine** Selecteer vervolgens **Tegel toevoegen**.

1. Selecteer **Opslaan** om de **telemetrieweergave van IoT Edge-apparaten weergeven** op te slaan.

![Apparaatsjabloon met telemetrieweergave](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>De sjabloon publiceren

Voordat u een apparaat toevoegen dat de sjabloon **Environmental Sensor Edge Device** gebruikt, moet u de sjabloon publiceren.

Navigeer naar de sjabloon Van het **randapparaat van** de omgevingssensor en selecteer **Publiceren**. Selecteer vervolgens **Publiceren** om de sjabloon te publiceren:

![De apparaatsjabloon publiceren](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>IoT Edge-apparaat toevoegen

Nu u de sjabloon **Environmental Sensor Edge Device** hebt gepubliceerd, u een apparaat toevoegen aan uw IoT Central-toepassing:

1. Navigeer in uw IoT Central-toepassing naar de pagina **Apparaten** en selecteer **Het Apparaat van de Rand van de Milieusensor** in de lijst met beschikbare sjablonen.

1. Selecteer **+** om een nieuw apparaat toe te voegen aan de sjabloon. Selecteer **op** de pagina Nieuw apparaat maken de optie **Maken**.

Je hebt nu een nieuw apparaat met de status **Geregistreerd:**

![De apparaatsjabloon publiceren](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>De referenties van het apparaat ophalen

Wanneer u het IoT Edge-apparaat later in deze zelfstudie implementeert, hebt u de referenties nodig waarmee het apparaat verbinding kan maken met uw IoT Central-toepassing. De gegevens van het apparaat krijgen:

1. Selecteer **op** de pagina Apparaat het apparaat dat u hebt gemaakt.

1. Selecteer **Verbinden**.

1. Noteer op de pagina **Apparaatverbinding** de **id-scope,** de **apparaat-id**en de **primaire sleutel**. U gebruikt deze waarden later.

1. Selecteer **Sluiten**.

U bent nu klaar met het configureren van uw IoT Central-toepassing om een IoT Edge-apparaat verbinding te maken.

## <a name="deploy-an-iot-edge-device"></a>Een IoT Edge-apparaat implementeren

In deze zelfstudie gebruikt u een Linux VM met Azure IoT Edge, gemaakt op Azure om een IoT Edge-apparaat te simuleren. Ga als het gaat om de VM met IoT Edge:

1. Navigeer naar [Azure IoT Edge op Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) in de Azure Marketplace. Selecteer vervolgens **Nu oppakken**.

1. Selecteer **Doorgaan**op de pagina **Deze app maken in Azure** . Deze koppeling brengt u naar de Azure-portal, waar u zich mogelijk moet aanmelden bij uw Azure-abonnement.

1. Selecteer op de **Pagina Azure IoT Edge op Ubuntu** in de Azure-portal de optie **Maken**.

1. Ga als > op de pagina **Een virtuele machine maken >:**

    - Selecteer uw Azure-abonnement.
    - Maak een nieuwe brongroep genaamd **iot-edge-devices.**
    - Gebruik de naam van de virtuele machine: **iotedgevm**.
    - Kies de dichtstbijzijnde regio die bij u in de buurt is.
    - Stel het verificatietype in op **Wachtwoord**.
    - Kies een gebruikersnaam en wachtwoord.
    - U de andere opties aan hun standaardwaarden overlaten.
    - Selecteer **Controleren + maken**.

1. Wanneer de validatie is voltooid, selecteert u **Maken**.

Na een paar minuten, wanneer de implementatie is voltooid, selecteert u **Ga naar resource**.

### <a name="provision-vm-as-an-iot-edge-device"></a>VM inrichten als Een IoT Edge-apparaat 

Ga als een IoT Edge-apparaat om VM in te richten als een IoT Edge-apparaat:

1. Selecteer in de sectie **Ondersteuning + probleemoplossing** de optie **Seriële console**.

1. Druk **Enter** op Enter `login:` om de prompt te bekijken. Voer uw gebruikersnaam en wachtwoord in om u aan te melden.

1. Voer de volgende opdracht uit om de Runtime-versie van IoT Edge te controleren. Op het moment van schrijven is de versie 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Gebruik `nano` de editor om het IoT Edge config.yaml-bestand te openen:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Scroll naar beneden `# Manual provisioning configuration`totdat je ziet . Reageer op de volgende drie regels zoals weergegeven in het volgende fragment:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Scroll naar beneden `# DPS symmetric key provisioning configuration`totdat je ziet . Maak de volgende acht regels ongedaan, zoals in het volgende fragment wordt weergegeven:

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

1. Vervang `{scope_id}` door de **ID-scope** waar u eerder een notitie van hebt gemaakt.

1. Vervang `{registration_id}` door de **apparaat-id waar** u eerder een notitie van hebt gemaakt.

1. Vervang `{symmetric_key}` door de **primaire sleutel waar** u eerder een notitie van hebt gemaakt.

1. Sla de wijzigingen (**Ctrl-O**) en `nano` sluit **(Ctrl-X)** de editor op.

1. Voer de volgende opdracht uit om de IoT Edge-daemon opnieuw te starten:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Voer de volgende opdracht uit om de status van de IoT Edge-modules te controleren:

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

## <a name="view-the-telemetry"></a>Bekijk de telemetrie

Het gesimuleerde IoT Edge-apparaat wordt nu uitgevoerd in de VM. In uw IoT Central-toepassing is de apparaatstatus nu **ingericht** op de pagina **Apparaten:**

![Ingerichte inrichting](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

U de telemetrie zien op de **telemetriepagina van IoT Edge-apparaten weergeven:**

![Apparaattelemetrie](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

De pagina **Modules** toont de status van de IoT Edge-modules:

![Apparaattelemetrie](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u met IoT Edge-apparaten in IoT Central werken en beheren, volgt de voorgestelde volgende stap:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Transparante gateway configureren](../../iot-edge/how-to-create-transparent-gateway.md)
