---
title: Regels en acties configureren in Azure IoT Central | Microsoft Docs
description: Deze zelfstudie laat u zien hoe u als bouwer op telemetrie gebaseerde regels en acties in uw Azure IoT Central-toepassing kunt configureren.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878150"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Zelfstudie: Regels en acties voor uw apparaat configureren in azure IoT Central (preview-functies)

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie maakt u een regel waarmee een e-mail bericht wordt verzonden wanneer de Tempe ratuur in een omgevings sensor van het apparaat 90&deg; F overschrijdt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een regel op basis van telemetrie maken
> * Een actie toevoegen

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het [type een nieuw apparaat definiëren in de](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) zelf studie voor uw toepassing volt ooien om de **omgevings** sjabloon voor Apparaatbeheer te maken waarmee u kunt werken.

## <a name="create-a-telemetry-based-rule"></a>Een regel op basis van telemetrie maken

1. Als u een nieuwe telemetrie-regel wilt toevoegen aan uw toepassing, selecteert u in het navigatie menu aan de linkerkant de optie **regels**.

1. Selecteer **+ Nieuw**om een nieuwe regel te maken. Kies vervolgenstelemetrie.

1. Voer **omgevings temperatuur** in als naam van de regel.

1. Selecteer in de sectie **bereik** de **omgevings sensor** als de sjabloon voor het apparaat. Het bereik waarop deze regel van toepassing is. U kunt meer filter criteria toevoegen met behulp van **+ filter**.

1. In de sectie **voor waarde** definieert u wat uw regel activeert. Gebruik de volgende informatie om een voor waarde op te geven op basis van de Tempe ratuur van de telemetrie:

    | Veld                                        | Value                             |
    | -------------------------------------------- | ------------------------------    |
    | Meting                                  | Temperatuur                       |
    | Operator                                     | is hoger dan                   |
    | Value                                        | 90                                |

    Selecteer **+ voor waarde**om meer voor waarden toe te voegen.

    ![Regel voorwaarde maken](./media/tutorial-configure-rules-pnp/condition.png)

1. Als u een actie wilt toevoegen die moet worden uitgevoerd wanneer de regel wordt geactiveerd, selecteert u **+ actie**en kiest u **e-mail adres**.

1. Gebruik de informatie in de volgende tabel om uw actie te definiëren:

    | Instelling   | Value                                             |
    | --------- | ------------------------------------------------- |
    | Naar        | Je e-mailadres                                |
    | Opmerkingen     | Omgevings temperatuur heeft de drempel waarde overschreden. |

    > [!NOTE]
    > Om een e-mailmelding te ontvangen, moet het e-mailadres een [gebruikers-ID in de toepassing](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) zijn, en moet die gebruiker zich minimaal één keer hebben aangemeld bij de toepassing.

    ![Regel actie maken](./media/tutorial-configure-rules-pnp/action.png)

1. Selecteer **Opslaan**. De regel wordt weergegeven op de pagina **Regels**.

## <a name="test-the-rule"></a>De regel testen

Kort nadat u de regel hebt opgeslagen, wordt deze actief. Wanneer aan de voorwaarden in de regel wordt voldaan, verzendt uw toepassing een bericht naar het e-mailadres dat u in de actie hebt opgegeven.

> [!NOTE]
> Als u klaar bent met testen, schakelt u de regel uit om geen waarschuwingen meer te ontvangen in uw postvak.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Een regel op basis van telemetrie maken
* Een actie toevoegen

Nu u een regel op basis van drempel hebt gedefinieerd, is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Een gebeurtenisregel maken en meldingen instellen](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
