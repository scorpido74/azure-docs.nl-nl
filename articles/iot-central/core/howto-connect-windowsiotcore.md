---
title: Windows IoT core verbinden met uw Azure IoT Central-toepassing | Microsoft Docs
description: Als ontwikkel aars van apparaten leert u hoe u een MXChip IoT DevKit-apparaat verbindt met uw Azure IoT Central-toepassing.
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 452d18908406214bb7e1253363a42d8ba8287d96
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454040"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Een Windows IoT core-apparaat verbinden met uw Azure IoT Central-toepassing

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In dit artikel wordt beschreven hoe u als een ontwikkelaar van een apparaat een Windows IoT core-apparaat verbindt met uw Microsoft Azure IoT Central toepassing.

## <a name="before-you-begin"></a>Voordat u begint

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

- Een Azure IoT Central-toepassing die is gemaakt op basis van de **oude** toepassings sjabloon. Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).

- Een apparaat met het Windows 10 IoT core-besturings systeem. Zie [uw Windows 10 IOT core-apparaat instellen](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup)voor meer informatie.

- Een ontwikkelings machine waarop de [node. js](https://nodejs.org/) -versie 8.0.0 of hoger is geïnstalleerd. U kunt `node --version` uitvoeren op de opdracht regel om uw versie te controleren. Node.js is beschikbaar voor een groot aantal verschillende besturingssystemen.

## <a name="add-a-device-template"></a>Een apparaatsjabloon toevoegen

Voeg in uw Azure IoT Central-toepassing een nieuw **Windows IOT core** -apparaat sjabloon toe met de volgende kenmerken:

- Telemetrie-metingen voor het apparaat: **vochtigheid**, **Tempe ratuur**en **Druk**.
- Instelling voor het beheren van de snelheid van de **ventilator**.
- Een **berekenings** -en Cloud eigenschaps **locatie**van een apparaat.

1. Selecteer **+ Nieuw** uit Apparaatinstellingen ![-apparaatprofiel](media/howto-connect-windowsiotcore/adddevicetemplate.png)
   

2. Selecteer **Windows IOT core** en maak de sjabloon Windows IOT core-apparaat ![een apparaatprofiel toevoegen](media/howto-connect-windowsiotcore/newdevicetemplate.png)

Zie voor volledige informatie over de configuratie van de sjabloon de [Details van Windows IOT core-apparaatgegevens](#device-template-details).

## <a name="add-a-real-device"></a>Echt apparaat toevoegen

Gebruik in uw Azure IoT Central-toepassing de pagina **device Explorer** om een echt apparaat toe te voegen vanuit de **Windows 10 IOT core** Device-sjabloon. Noteer de verbindings Details van het apparaat (**scope-id**, **apparaat-id**en **primaire sleutel**).

## <a name="prepare-the-device"></a>Het apparaat voorbereiden

Het apparaat kan alleen verbinding maken met IoT Central als het een connection string heeft:

1. Gebruik het `dps-keygen` opdracht regel hulpprogramma voor het genereren van een connection string:

    Voer de volgende opdracht uit om het [hulp programma voor sleutel Generator](https://github.com/Azure/dps-keygen)te installeren:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Als u een connection string wilt genereren, voert u de volgende opdracht uit met de verbindings gegevens die u eerder hebt genoteerd:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Kopieer de connection string van de `dps-keygen` uitvoer zodat u deze kunt gebruiken in de code van uw apparaat.

Voor de apparaatcode voor toegang tot de connection string, slaat u deze op in een bestand met de naam **Connection. String. iothub** in de map `C:\Data\Users\DefaultAccount\Documents\` op uw Windows 10 IOT core-apparaat.

Als u het bestand **Connection. String. iothub** van uw desktop computer wilt kopiëren naar de map `C:\Data\Users\DefaultAccount\Documents\` op uw apparaat, kunt u de [Windows-portal voor apparaten](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)gebruiken:

1. Gebruik uw webbrowser om naar de Windows-Portal op het apparaat te navigeren.
1. Als u door de bestanden op uw apparaat wilt bladeren, kiest u **Apps > Verkenner**.
1. Ga naar **gebruiker Folders\Documents**. Upload vervolgens de **verbinding. String. iothub** -bestand:

    ![connection string uploaden](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Implementeren en uitvoeren

Als u de voorbeeld toepassing op uw apparaat wilt implementeren en uitvoeren, kunt u de [Windows-portal voor apparaten](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)gebruiken:

1. Gebruik uw webbrowser om naar de Windows-Portal op het apparaat te navigeren.
1. Als u de **Azure IOT hub-client** toepassing wilt implementeren en uitvoeren, kiest u **apps > voor beelden van snelle uitvoering**. Kies vervolgens **Azure IOT hub-client**.
1. Kies vervolgens **implementeren en uitvoeren**.

    ![Implementeren en uitvoeren](media/howto-connect-windowsiotcore/quick-run.png)

Na een paar minuten kunt u de telemetrie van uw apparaat weer geven in uw IoT Central-toepassing.

De [Windows-portal voor apparaten](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) bevat hulpprogram ma's die u kunt gebruiken om problemen met uw apparaat op te lossen:

- Op de pagina **beheer van apps** kunt u de apps beheren die op het apparaat worden uitgevoerd.
- Als u geen monitor hebt aangesloten op uw apparaat, kunt u de pagina **Apparaatinstellingen** gebruiken om scherm opnamen van uw apparaat vast te leggen. Bijvoorbeeld:

    ![Scherm afbeelding app](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>De bron code downloaden

Als u de bron code voor de client toepassing wilt verkennen en wijzigen, kunt u deze downloaden via de [github-opslag plaats Windows-iotcore-voor beelden](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Details van de apparaatprofiel

Een toepassing die is gemaakt op basis van de voor **beeld-Devkits** -toepassings sjabloon bevat een **Windows IOT** -apparaat sjabloon met de volgende kenmerken:

### <a name="telemetry-measurements"></a>Telemetrische metingen

| Veldnaam     | Eenheden  | Minimum | Maximum | Aantal decimalen |
| -------------- | ------ | ------- | ------- | -------------- |
| vochtigheid       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Instellingen

Numerieke instellingen

| Weergavenaam | Veldnaam | Eenheden | Aantal decimalen | Minimum | Maximum | Oorspronkelijk |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Snelheid van ventilator    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Eigenschappen

| Type            | Weergavenaam | Veldnaam | Gegevenstype |
| --------------- | ------------ | ---------- | --------- |
| Eigenschap apparaat | Aantal dobbelten   | dieNumber  | getal    |
| Tekst            | Locatie     | location   | N/A       |

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een Windows IoT core-apparaat verbindt met uw Azure IoT Central-toepassing, is de voorgestelde volgende stap informatie over het [instellen van een sjabloon voor aangepaste apparaten](howto-set-up-template.md) voor uw eigen IOT-apparaat.
