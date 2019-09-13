---
title: Een IoT Plug en Play preview-apparaat bouwen dat klaar is voor certificering | Microsoft Docs
description: Als ontwikkelaar van een apparaat leert u hoe u een IoT Plug en Play preview-apparaat kunt bouwen dat klaar is voor certificering.
author: tbhagwat3
ms.author: tanmayb
ms.date: 06/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 524bc3b2650ad7b435cba6b6b9d4084ffa5cf96c
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932682"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Een IoT Plug en Play preview-apparaat bouwen dat gereed is voor certificering

In deze zelf studie wordt beschreven hoe u als ontwikkel aars van een apparaat een IoT Plug en Play preview-apparaat kunt bouwen dat gereed is voor certificering.

De certificerings tests controleren of:

- Uw IoT Plug en Play-apparaatcode is op het apparaat geïnstalleerd.
- Uw IoT Plug en Play-apparaatcode is gebouwd met de Azure IoT-SDK.
- Uw apparaatcode ondersteunt de [Azure-IOT hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
- De apparaatcode implementeert de apparaatgegevens interface.
- Het bekwaamheids model en de apparaatcode werken met IoT Central.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- [Visual Studio Code](https://code.visualstudio.com/download)
- [Azure IOT-Hulpprogram ma's voor VS code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) -uitbreidings pakket

U hebt ook het IOT Plug en Play-apparaat nodig dat u in [de Quick Start maakt: Gebruik een mogelijkheidsprofiel voor het maken van een apparaat](quickstart-create-pnp-device.md).

## <a name="store-a-capability-model-and-interfaces"></a>Een mogelijkheidsprofiel en interfaces opslaan

Voor IoT Plug en Play-apparaten moet u een mogelijkheidsprofiel en interfaces ontwerpen waarmee de mogelijkheden van het apparaat als JSON-bestanden worden gedefinieerd.

U kunt deze JSON-bestanden opslaan op drie verschillende locaties:

- De open bare model opslagplaats.
- Uw bedrijfs model opslagplaats.
- Op het apparaat.

Op dit moment moeten de bestanden worden opgeslagen in uw bedrijfs model opslagplaats of in de open bare model opslagplaats om uw apparaat te kunnen certificeren.

## <a name="include-the-required-interfaces"></a>De vereiste interfaces toevoegen

Als u het certificerings proces wilt door geven, moet u de **apparaatgegevens** interface in uw mogelijkheidsprofiel toevoegen en implementeren. Deze interface heeft de volgende identificatie:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Als u de [Snelstartgids hebt voltooid: Gebruik een mogelijkheidsprofiel om een apparaat](quickstart-create-pnp-device.md)te maken, u hebt al de **apparaatgegevens** interface in uw model opgenomen.

Als u de **apparaatgegevens** interface wilt toevoegen aan het model van uw apparaat, voegt u `implements` de interface-id toe aan de eigenschap van het mogelijkheidsprofiel:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

De interface van de **apparaatgegevens** in VS code weer geven:

1. Gebruik **CTRL + SHIFT + P** om het opdracht palet te openen.

1. Voer **Plug en Play** in en selecteer de opdracht **IOT Plug en Play open model opslagplaats** . Kies **opslag voor open bare model openen**. De open bare model opslagplaats wordt geopend in VS code.

1. In de open bare model opslagplaats selecteert u het tabblad **interfaces** , selecteert u het filter pictogram en voert u **apparaatgegevens** in het filter veld in.

1. Als u een lokale kopie van de **apparaatgegevens** interface wilt maken, selecteert u deze in de gefilterde lijst en selecteert u **downloaden**. VS code geeft het interface bestand weer.

De interface voor **apparaatgegevens** weer geven met behulp van de Azure cli:

1. [Installeer de Azure IOT cli-extensie](howto-install-pnp-cli.md).

1. Gebruik de volgende Azure CLI-opdracht om een interface met de ID van de apparaatgegevens interface weer te geven:

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Zie [de Azure IOT-extensie voor Azure cli installeren en gebruiken](howto-install-pnp-cli.md)voor meer informatie.

## <a name="update-device-code"></a>Apparaatinstellingen bijwerken

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Apparaat inrichten inschakelen via de Azure IoT Device Provisioning Service (DPS)

Als u het apparaat wilt certificeren, moet het inrichten via de [Azure IOT Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)inschakelen. Als u de mogelijkheid wilt gebruiken om DPS toe te voegen, kunt u de C code stub genereren in VS code. Volg deze stappen:

1. Open de map met DCM-bestand in VS code, gebruik **CTRL + SHIFT + P** om het opdracht palet te openen, geef **IOT Plug en Play**op en selecteer de stub van de **apparaatcode genereren**.

1. Kies het DCM-bestand dat u wilt gebruiken voor het genereren van de stub voor het apparaat.

1. Voer de naam van het project in. Dit is de naam van uw apparaat-app.

1. Kies **ANSI C** als taal.

1. Kies **cmake project** als uw project type.

1. Kies **via DPS (Device Provisioning Service) symmetrische sleutel** als verbindings methode.

1. VS code opent een nieuw venster met gegenereerde code stub-bestanden.

1. Open `main.c`, vul de **dpsIdScope**, **sasKey**en **registratie** die u hebt voor bereid. U kunt deze informatie ophalen via de certificerings Portal. Zie [verbinding maken en testen van uw IoT Plug en Play-apparaat](tutorial-certification-test.md#connect-and-discover-interfaces)voor meer informatie.

    ```c
    // TODO: Specify DPS scope ID if you intend on using DPS / IoT Central.
    static const char *dpsIdScope = "[DPS Id Scope]";
    
    // TODO: Specify symmetric keys if you intend on using DPS / IoT Central and symmetric key based auth.
    static const char *sasKey = "[DPS symmetric key]";
    
    // TODO: specify your device registration ID
    static const char *registrationId = "[device registration Id]";
    ```

1. Sla het bestand op.

### <a name="implement-standard-interfaces"></a>Standaard interfaces implementeren

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>De model informatie en SDK-informatie interfaces implementeren

De Azure IoT Device SDK implementeert de model gegevens en SDK-informatie interfaces. Als u de functie voor het genereren van code in VS code gebruikt, gebruikt uw apparaatcode de IoT Plug en Play apparaat-SDK.

Als u ervoor hebt gekozen om de Azure IoT Device SDK niet te gebruiken, kunt u de SDK-bron code gebruiken als referentie voor uw eigen implementatie.

#### <a name="implement-the-device-information-interface"></a>De apparaatgegevens interface implementeren

Implementeer de **apparaatgegevens** interface op het apparaat en geef tijdens de uitvoering apparaatspecifieke informatie op over het apparaat.

U kunt een voorbeeld implementatie van de **device information** interface voor [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) als referentie gebruiken.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Implementeer alle mogelijkheden die in uw model zijn gedefinieerd

Tijdens de certificering wordt het apparaat via een programma getest om er zeker van te zijn dat het implementeert de mogelijkheden die in de interfaces zijn gedefinieerd. Gebruik HTTP-status code 501 om te reageren op lees-/schrijftoegang-eigenschappen en opdracht aanvragen als uw apparaat deze niet implementeert.

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Plug en Play-apparaat hebt gebouwd dat gereed is voor certificering, is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Meer informatie over het certificeren van uw apparaat](tutorial-certification-test.md)
