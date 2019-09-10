---
title: Connectiviteit van apparaten bewaken met behulp van Azure IoT Central Explorer
description: Controleer de apparaatgegevens en Bekijk de dubbele wijzigingen van het apparaat via de IoT Central Explorer-CLI.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 38cbe43e9038a47c4e222fd4744f0b844f9ddb4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845680"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Connectiviteit van apparaten bewaken met behulp van Azure IoT Central Explorer

*Dit onderwerp is van toepassing op bouwers en beheerders.*

Gebruik de IoT Central Explorer CLI om berichten weer te geven die uw apparaten naar IoT Central verzenden en wijzigingen in de IoT Hub dubbele. U kunt dit open source-hulp programma gebruiken om dieper inzicht te krijgen in de status van de connectiviteit van apparaten en problemen op te lossen van de berichten die de Cloud niet bereiken of apparaten die niet reageren op twee wijzigingen.

[Ga naar de IOTC-Explorer-opslag plaats in GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Vereisten

+ Node. js versie 8. x of hoger- https://nodejs.org
+ Een beheerder van uw toepassing moet een toegangs token genereren dat u kunt gebruiken in IOTC-Explorer

## <a name="install-iotc-explorer"></a>IOTC-Explorer installeren

Voer de volgende opdracht uit vanaf de opdracht regel die u wilt installeren:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Normaal gesp roken moet u de install-opdracht `sudo` uitvoeren met in UNIX-achtige omgevingen.

## <a name="run-iotc-explorer"></a>Voer IOTC-Explorer uit

In de volgende secties worden veelvoorkomende opdrachten en opties beschreven die u kunt gebruiken `iotc-explorer`wanneer u uitvoert. Als u de volledige set met opdrachten en opties wilt weer `--help` geven `iotc-explorer` , geeft u een of meer van de bijbehorende subopdrachten door.

### <a name="login"></a>Aanmelden

Voordat u aan de slag gaat, moet u een beheerder van uw IoT Central-toepassing hebben om een toegangs token te krijgen dat u kunt gebruiken. De beheerder voert de volgende stappen uit:

1. Navigeer naar **beheer** en vervolgens **toegangs tokens**.
1. Selecteer **token genereren**.
    ![Scherm opname van toegangs token pagina](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Voer een token naam in, selecteer **volgende**en **Kopieer**vervolgens.
    > [!NOTE]
    > De token waarde wordt slechts eenmaal weer gegeven, dus moet worden gekopieerd voordat het dialoog venster wordt gesloten. Nadat het dialoog venster is gesloten, wordt het niet meer weer gegeven.

    ![Scherm opname van het dialoog venster toegangs token kopiëren](media/howto-use-iotc-explorer/copyaccesstoken.png)

U kunt het token gebruiken om u als volgt aan te melden bij de CLI:

```cmd/sh
iotc-explorer login "<Token value>"
```

Als u het token niet in uw shell geschiedenis wilt behouden, kunt u het token uit en in plaats daarvan opgeven wanneer u hierom wordt gevraagd:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Apparaat-berichten bewaken

Met de `monitor-messages` opdracht kunt u de berichten bekijken die afkomstig zijn van een specifiek apparaat of van alle apparaten in uw toepassing. Met deze opdracht wordt een Watcher gestart waarmee doorlopend nieuwe berichten worden uitgevoerd wanneer ze binnenkomen:

Als u alle apparaten in uw toepassing wilt bekijken, voert u de volgende opdracht uit:

```cmd/sh
iotc-explorer monitor-messages
```

Uitvoer:

![controle: berichten opdracht uitvoer](media/howto-use-iotc-explorer/monitormessages.png)

Als u een specifiek apparaat wilt bekijken, voegt u alleen de apparaat-id toe aan het einde van de opdracht:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

U kunt ook een meer bewerkings vriendelijke indeling uitvoeren door de `--raw` optie toe te voegen aan de opdracht:

```cmd/sh
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Apparaat dubbele ophalen

U kunt de `get-twin` opdracht gebruiken om de inhoud van de dubbele voor een IOT Central apparaat op te halen. Voer hiervoor de volgende opdracht uit:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Uitvoer:

![Get-dubbele opdracht uitvoer](media/howto-use-iotc-explorer/getdevicetwin.png)

Net als `monitor-messages`bij kunt u een meer machine vriendelijke uitvoer verkrijgen door de `--raw` optie te door geven:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u de IoT Central Explorer kunt gebruiken, is de voorgestelde volgende stap om het [beheren van apparaten IOT Central](howto-manage-devices.md)te verkennen.
