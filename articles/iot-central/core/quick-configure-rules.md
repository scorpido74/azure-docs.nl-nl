---
title: 'Quick Start: regels en acties in azure IoT Central configureren'
description: In deze Quick start ziet u als een opbouw functie voor het configureren van regels en acties op basis van telemetrie in uw Azure IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 66c3bd8650d1194d5d753c1dc967ec8e870c8748
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80998966"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Snelstartgids: regels en acties voor uw apparaat configureren in azure IoT Central

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

In deze Quick Start maakt u een regel waarmee een e-mail bericht wordt verzonden wanneer de Tempe ratuur die wordt gerapporteerd&deg; door een apparaat-sensor groter is dan 90 F.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u de twee vorige Quick starts volt ooien om [een Azure IOT Central-toepassing te maken](./quick-deploy-iot-central.md) en [een gesimuleerd apparaat toe te voegen aan uw IOT Central-toepassing](./quick-create-simulated-device.md) om de **MXChip IOT DevKit** -Device-sjabloon te maken waarmee u kunt werken.

## <a name="create-a-telemetry-based-rule"></a>Een regel op basis van telemetrie maken

1. Als u een nieuwe regel op basis van telemetrie wilt toevoegen aan uw toepassing, selecteert u in het linkerdeel venster **regels**.

1. Selecteer **+** om een nieuwe regel te maken.

1. Voer **omgevings temperatuur** in als naam van de regel.

1. Selecteer in de sectie **doel apparaten** **MXChip IOT DevKit** als de sjabloon voor het apparaat. Met deze optie worden de apparaten gefilterd waarop de regel van toepassing is op basis van het type apparaat. U kunt meer filter criteria toevoegen door **+ filter**te selecteren.

1. In de sectie **voor waarden** definieert u wat uw regel activeert. Gebruik de volgende informatie om een voor waarde op te geven op basis van de Tempe ratuur van de telemetrie:

    | Veld        | Waarde            |
    | ------------ | ---------------- |
    | Meting  | Temperatuur      |
    | Operator     | is groter dan  |
    | Waarde        | 90               |

    Selecteer **+ voor waarde**om meer voor waarden toe te voegen.

    ![Regel voorwaarde maken](./media/quick-configure-rules/condition.png)

1. Selecteer **+ e-mail**om een e-mail actie toe te voegen die moet worden uitgevoerd wanneer de regel wordt geactiveerd.

1. Gebruik de informatie in de volgende tabel om uw actie te definiëren en selecteer vervolgens **gereed**:

    | Instelling   | Waarde                                             |
    | --------- | ------------------------------------------------- |
    | Weergavenaam | E-mail actie voor operator                          |
    | Handeling        | Uw e-mailadres                                |
    | Opmerkingen     | Omgevings temperatuur heeft de drempel waarde overschreden. |

    > [!NOTE]
    > Om een e-mailmelding te ontvangen, moet het e-mailadres een [gebruikers-ID in de toepassing](howto-administer.md) zijn, en moet die gebruiker zich minimaal één keer hebben aangemeld bij de toepassing.

    ![Regel actie maken](./media/quick-configure-rules/action.png)

1. Selecteer **Opslaan**. Uw regel wordt weer gegeven op de pagina **regels** .

## <a name="test-the-rule"></a>De regel testen

Kort nadat u de regel hebt opgeslagen, wordt deze actief. Wanneer aan de voorwaarden in de regel wordt voldaan, verzendt uw toepassing een bericht naar het e-mailadres dat u in de actie hebt opgegeven.

> [!NOTE]
> Als u klaar bent met testen, schakelt u de regel uit om geen waarschuwingen meer te ontvangen in uw postvak.

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u de volgende zaken geleerd:

* Een regel op basis van telemetrie maken
* Een actie toevoegen

Ga naar de Snelstartgids voor meer informatie over het bewaken van apparaten die zijn verbonden met uw toepassing:

> [!div class="nextstepaction"]
> [Gebruik Azure IOT Central om uw apparaten te bewaken](quick-monitor-devices.md).
