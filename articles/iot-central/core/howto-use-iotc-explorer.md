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
ms.openlocfilehash: ec3ccc0a9fa6d1ae5d6193aacd1b068f2d97afe0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949610"
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
> Normaal gesp roken moet u de install-opdracht uitvoeren met `sudo` in UNIX-achtige omgevingen.

## <a name="run-iotc-explorer"></a>Voer IOTC-Explorer uit

In de volgende secties worden veelvoorkomende opdrachten en opties beschreven die u kunt gebruiken wanneer u `iotc-explorer`uitvoert. Als u de volledige set met opdrachten en opties wilt weer geven, geeft u `--help` door aan `iotc-explorer` of een van de bijbehorende subopdrachten.

### <a name="login"></a>Aanmelden

Voordat u aan de slag gaat, moet u een beheerder van uw IoT Central-toepassing hebben om een toegangs token te krijgen dat u kunt gebruiken. De beheerder voert de volgende stappen uit:

1. Navigeer naar **beheer** en vervolgens **toegangs tokens**.
1. Selecteer **token genereren**.
    scherm afbeelding van de pagina toegangs token ![](media/howto-use-iotc-explorer/accesstokenspage.png)

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

U kunt de berichten bekijken die afkomstig zijn van een specifiek apparaat of van alle apparaten in uw toepassing met behulp van de `monitor-messages` opdracht. Met deze opdracht wordt een Watcher gestart waarmee doorlopend nieuwe berichten worden uitgevoerd wanneer ze binnenkomen:

Als u alle apparaten in uw toepassing wilt bekijken, voert u de volgende opdracht uit:

```cmd/sh
iotc-explorer monitor-messages
```

Uitvoer:

![controle: berichten opdracht uitvoer](media/howto-use-iotc-explorer/monitormessages.png)

Als u een specifiek apparaat wilt bekijken, voegt u alleen de apparaat-ID toe aan het einde van de opdracht:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

U kunt ook een meer bewerkings vriendelijke indeling uitvoeren door de optie `--raw` toe te voegen aan de opdracht:

```cmd/sh
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Apparaat dubbele ophalen

U kunt de `get-twin` opdracht gebruiken om de inhoud van de dubbele voor een IoT Central apparaat op te halen. Voer hiervoor de volgende opdracht uit:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Uitvoer:

![Get-dubbele opdracht uitvoer](media/howto-use-iotc-explorer/getdevicetwin.png)

Net als bij `monitor-messages`, kunt u een meer machine vriendelijke uitvoer verkrijgen door de `--raw` optie te geven:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u de IoT Central Explorer kunt gebruiken, is de voorgestelde volgende stap om het [beheren van apparaten IOT Central](howto-manage-devices.md)te verkennen.
