---
title: Een DevKit-apparaat verbinden met uw Azure IoT Central-toepassing | Microsoft Docs
description: Als ontwikkel aars van apparaten leert u hoe u een MXChip IoT DevKit-apparaat verbindt met uw Azure IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 81a355cc7c0d1190ee86fac6ed155380e6e5c0d1
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877531"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Een MXChip IoT DevKit-apparaat verbinden met uw Azure IoT Central-toepassing

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

In dit artikel wordt beschreven hoe als een ontwikkelaar van een apparaat een MXChip IoT DevKit (DevKit)-apparaat verbindt met uw Microsoft Azure IoT Central toepassing.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources nodig om de stappen in dit artikel uit te voeren:

1. Een Azure IoT Central-toepassing gemaakt op basis van de voor beeld-toepassings sjabloon **Devkits** . Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
1. Een DevKit-apparaat. Ga naar [MXChip IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)om een DevKit-apparaat aan te schaffen.

## <a name="sample-devkits-application"></a>Voor beeld van Devkits-toepassing

Een toepassing die is gemaakt op basis van de voor **beeld-Devkits** -toepassings sjabloon bevat een **MXChip** -apparaatprofiel waarmee de volgende apparaateigenschappen worden gedefinieerd:

- Telemetrie-metingen voor **vochtigheid**, **Tempe ratuur**, **Druk**, **magnetometer** (gemeten langs x, y, z-as), **acceleratie meter** (gemeten in x, y, z-as) en **Gyroscope** (gemeten langs x, y, z-as).
- Status meting voor **Apparaatstatus**.
- Gebeurtenis meting voor **knop B**ingedrukt.
- Instellingen voor **spanning**, **huidige**, **ventilator snelheid**en een **IR** -wissel knop.
- De eigenschappen van het apparaat en de locatie van het **apparaat**. Dit is een locatie-eigenschap.
- De eigenschap Cloud die is **geproduceerd in**.
- **Echo** en aftellen van opdrachten. Wanneer een echt apparaat een **echo** opdracht ontvangt, wordt de verzonden waarde weer gegeven op het apparaat. Wanneer een echt apparaat een opdracht voor aftellen ontvangt, wordt de LED door een patroon geleid en verzendt het apparaat aftellings waarden terug naar IOT Central.

Zie Details van de [MXChip-Apparaatbeheer](#mxchip-device-template-details) voor volledige informatie over de configuratie.

## <a name="add-a-real-device"></a>Echt apparaat toevoegen

### <a name="get-your-device-connection-details"></a>De verbindings gegevens van uw apparaat ophalen

Voeg in uw Azure IoT Central-toepassing een echt apparaat toe uit de sjabloon **MXChip** en noteer de details van de apparaatgegevens: **Bereik-id, apparaat-id en primaire sleutel**:

1. Een **echt apparaat** toevoegen uit Device Explorer, selecteer **+ Nieuw > Real** om een echt apparaat toe te voegen.

    * Voer een **apparaat-id**in kleine letters in of gebruik de voorgestelde **apparaat-id**.
    * Voer een **naam**voor het apparaat in of gebruik de voorgestelde naam

    ![Apparaat toevoegen](media/howto-connect-devkit/add-device.png)

1. Selecteer **verbinding maken** op de pagina apparaat om de verbindings Details van het apparaat, de **scope-id**, de **apparaat-id**en de **primaire sleutel**op te halen.

    ![Verbindingsdetails](media/howto-connect-devkit/device-connect.png)

1. Noteer de verbindings gegevens. U hebt tijdelijk geen verbinding meer met Internet wanneer u uw DevKit-apparaat voorbereidt in de volgende stap.

### <a name="prepare-the-devkit-device"></a>Het DevKit-apparaat voorbereiden

Als u het apparaat eerder hebt gebruikt en het opnieuw wilt configureren voor gebruik van een ander WiFi-netwerk, connection string of een telemetrie-meting, drukt u op de knoppen **a** en **B** op hetzelfde moment. Als het niet werkt, drukt u op de knop **opnieuw instellen** en probeert u het opnieuw.

#### <a name="to-prepare-the-devkit-device"></a>Het DevKit-apparaat voorbereiden

1. Down load de nieuwste vooraf ontwikkelde Azure IoT Central-firmware voor het MXChip op de pagina [releases](https://aka.ms/iotcentral-docs-MXChip-releases) op github.
1. Verbind het DevKit-apparaat met de ontwikkel computer via een USB-kabel. In Windows wordt een bestand Verkenner-venster geopend op een station dat is toegewezen aan de opslag op het DevKit-apparaat. Het station kan bijvoorbeeld **AZ3166 (D:)** worden genoemd.
1. Sleep het **iotCentral. bin** -bestand naar het station-venster. Wanneer het kopiëren is voltooid, wordt het apparaat opnieuw opgestart met de nieuwe firmware.

1. Wanneer het DevKit-apparaat opnieuw wordt opgestart, wordt het volgende scherm weer gegeven:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Als op het scherm iets anders wordt weer gegeven, stelt u het apparaat opnieuw in en drukt u op de knoppen **A** en **B** op het apparaat tegelijk om het apparaat opnieuw op te starten.

1. Het apparaat bevindt zich nu in de toegangs punt modus (AP). U kunt verbinding maken met dit Wi-Fi-toegangs punt vanaf uw computer of mobiel apparaat.

1. Op uw computer, telefoon of Tablet kunt u verbinding maken met de Wi-Fi-netwerk naam die wordt weer gegeven op het scherm van het apparaat. Wanneer u verbinding met dit netwerk maakt, hebt u geen toegang tot internet. Deze status wordt verwacht en u hebt gedurende korte tijd alleen verbinding met dit netwerk tijdens het configureren van het apparaat.

1. Open uw webbrowser en ga naar [http://192.168.0.1/start](http://192.168.0.1/start). De volgende webpagina wordt weer gegeven:

    ![Configuratie pagina voor apparaten](media/howto-connect-devkit/configpage.png)

    Voer op de webpagina het volgende in:
    - De naam van uw WiFi-netwerk
    - Uw WiFi-netwerk wachtwoord
    - De PIN-code die wordt weer gegeven op de weer gave van het apparaat
    - De verbindings Details **bereik-id**, **apparaat-id**en **primaire sleutel** van uw apparaat (u moet dit al hebben opgeslagen door de stappen te volgen)
    - Alle beschik bare telemetrie-metingen selecteren

1. Nadat u **apparaat configureren**hebt gekozen, ziet u deze pagina:

    ![Apparaat is geconfigureerd](media/howto-connect-devkit/deviceconfigured.png)

1. Druk op de knop **Reset** op het apparaat.

## <a name="view-the-telemetry"></a>De telemetrie weer geven

Wanneer het DevKit-apparaat opnieuw wordt opgestart, wordt het scherm op het apparaat weer gegeven:

* Het aantal telemetriegegevens dat is verzonden.
* Het aantal fouten.
* Het aantal gewenste eigenschappen dat is ontvangen en het aantal gerapporteerde eigenschappen dat is verzonden.

> [!NOTE]
> Als het apparaat wordt weer gegeven wanneer er wordt geprobeerd verbinding te maken, controleert u of het apparaat is **geblokkeerd** in IOT Central en **blokkeert** u het apparaat zodat het verbinding kan maken met de app.

Schud het apparaat om een gerapporteerde eigenschap te verzenden. Het apparaat verzendt een wille keurig getal als de eigenschap van het Dobbelten- **nummer** .

U kunt de telemetrie-metingen en gerapporteerde eigenschaps waarden weer geven en instellingen configureren in azure IoT Central:

1. Gebruik **device Explorer** om te navigeren naar de pagina **metingen** voor het echte MXChip-apparaat dat u hebt toegevoegd:

    ![Naar echt apparaat navigeren](media/howto-connect-devkit/realdevicenew.png)

1. Op de pagina **metingen** kunt u de telemetrie weer geven die afkomstig zijn van het MXChip-apparaat:

    ![Telemetrie van een echt apparaat weer geven](media/howto-connect-devkit/devicetelemetrynew.png)

1. Op de pagina **Eigenschappen** kunt u het laatste nummer van de dobbel steen bekijken en de locatie van het apparaat die wordt gerapporteerd door het apparaat:

    ![Apparaateigenschappen weer geven](media/howto-connect-devkit/devicepropertynew.png)

1. Op de pagina **instellingen** kunt u de instellingen op het MXChip-apparaat bijwerken:

    ![Apparaatinstellingen weer geven](media/howto-connect-devkit/devicesettingsnew.png)

1. Op de pagina **opdrachten** kunt u de opdrachten **echo** en aftellen aanroepen:

    ![Opdrachten oproepen](media/howto-connect-devkit/devicecommands.png)

1. Op de pagina **dash board** ziet u de locatie kaart

    ![Het dash board van het apparaat weer geven](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>De bron code downloaden

Als u de apparaatcode wilt verkennen en wijzigen, kunt u de code downloaden van GitHub. Als u van plan bent om de code te wijzigen, moet u deze instructies volgen om [de ontwikkelings omgeving](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) voor te bereiden voor het besturings systeem van uw bureau blad.

Als u de bron code wilt downloaden, voert u de volgende opdracht uit op de computer van het bureau blad:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Met de vorige opdracht wordt de bron code gedownload naar een `iot-central-firmware`map met de naam.

> [!NOTE]
> Als **Git** niet is geïnstalleerd in uw ontwikkel omgeving, kunt u het downloaden van [https://git-scm.com/download](https://git-scm.com/download).

## <a name="review-the-code"></a>De code bekijken

Gebruik Visual Studio code om de `MXCHIP/mxchip_advanced` map in de `iot-central-firmware` map te openen:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Als u wilt zien hoe de telemetrie wordt verzonden naar de Azure IOT Central-toepassing, opent u het bestand telemetrie **. cpp** in de `src` map:

- De functie `TelemetryController::buildTelemetryPayload` maakt de JSON-telemetrie-nettolading met gegevens van de Sens oren op het apparaat.

- De functie `TelemetryController::sendTelemetryPayload` roept `sendTelemetry` het **AzureIOTClient. cpp** om de JSON-Payload te verzenden naar de IOT hub uw Azure IOT Central-toepassing gebruikt.

Als u wilt zien hoe eigenschaps waarden worden gerapporteerd aan de Azure IOT Central-toepassing, opent u het bestand telemetrie **. cpp** in de `src` map:

- De functie `TelemetryController::loop` verzendt de **locatie** gemelde eigenschap ongeveer elke 30 seconden. De `sendReportedProperty` functie wordt gebruikt in het bron bestand **AzureIOTClient. cpp** .

- De functie `TelemetryController::loop` verzendt de gerapporteerde eigenschap **dieNumber** wanneer de versnellings meter van het apparaat dubbel tikken detecteert. De `sendReportedProperty` functie wordt gebruikt in het bron bestand **AzureIOTClient. cpp** .

Als u wilt zien hoe het apparaat reageert op opdrachten die worden aangeroepen vanuit de IOT Central toepassing, opent u het bestand `src` **registeredMethodHandlers. cpp** in de map:

- De functie **dmEcho** is de handler voor de **echo** opdracht. Hierin wordt de **displayedValue** vermeld die is opgeslagen in de payload op het scherm van het apparaat.

- De functie **dmCountdown** is de handler voor de aftellings opdracht. Hiermee wordt de kleur van de LED van het apparaat gewijzigd en wordt een gerapporteerde eigenschap gebruikt om de aftellings waarde terug te sturen naar de IoT Central toepassing. De gerapporteerde eigenschap heeft dezelfde naam als de opdracht. De functie maakt gebruik `sendReportedProperty` van de functie in het bron bestand **AzureIOTClient. cpp** .

De code in het bron bestand **AzureIOTClient. cpp** maakt gebruik van functies uit de [Microsoft Azure IOT sdk's en bibliotheken voor C](https://github.com/Azure/azure-iot-sdk-c) om met IOT hub te communiceren.

Zie het **README.MD** -bestand in de `MXCHIP/mxchip_advanced` map voor meer informatie over het wijzigen, bouwen en uploaden van de voorbeeld code naar uw apparaat.

## <a name="mxchip-device-template-details"></a>Details van MXChip-Apparaatbeheer

Een toepassing die is gemaakt op basis van de voor beeld-Devkits-toepassings sjabloon bevat een MXChip-apparaatprofiel met de volgende kenmerken:

### <a name="measurements"></a>Metingen

#### <a name="telemetry"></a>Telemetrie

| Veldnaam     | Eenheden  | Minimum | Maximum | Aantal decimalen |
| -------------- | ------ | ------- | ------- | -------------- |
| vochtigheid       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| versnellings meter | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>States 
| Name          | `Display name`   | OPMAAKPROFIEL | VOORZICHTIG | GEVAAR | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Apparaatstatus   | Groen  | Oranje  | Rood    | 

#### <a name="events"></a>Events 
| Name             | `Display name`      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Knop B ingedrukt  | 

### <a name="settings"></a>Instellingen

Numerieke instellingen

| `Display name` | Veldnaam | Eenheden | Aantal decimalen | Minimum | Maximum | Oorspronkelijk |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Verbruik      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Huidige      | setCurrent | Amp  | 0              | 0       | 100     | 0       |
| Snelheid van ventilator    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Instellingen in-/uitschakelen

| `Display name` | Veldnaam | In tekst | Uit tekst | Oorspronkelijk |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | AAN      | UIT      | Uit     |

### <a name="properties"></a>properties

| type            | `Display name` | Veldnaam | Gegevenstype |
| --------------- | ------------ | ---------- | --------- |
| Eigenschap apparaat | Aantal dobbelten   | dieNumber  | nummer    |
| Eigenschap apparaat | Locatie van apparaat   | location  | location    |
| Text            | Gefabriceerd in     | manufacturedIn   | N/A       |

### <a name="commands"></a>Opdrachten

| `Display name` | Veldnaam | Retour type | Weergave naam van invoer veld | Naam van invoer veld | Type invoer veld |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Echo         | echo       | text        | weer te geven waarde         | displayedValue   | text             |
| Geteld    | geteld  | nummer      | Aantal van               | countFrom        | nummer           |

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een MXChip IoT DevKit kunt verbinden met uw Azure IoT Central-toepassing, is de voorgestelde volgende stap informatie over het [instellen van een aangepaste apparaatprofiel](howto-set-up-template.md) voor uw eigen IOT-apparaat.
