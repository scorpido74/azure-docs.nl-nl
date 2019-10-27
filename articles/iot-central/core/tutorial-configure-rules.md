---
title: Regels en acties configureren in Azure IoT Central | Microsoft Docs
description: Deze zelfstudie laat u zien hoe u als bouwer op telemetrie gebaseerde regels en acties in uw Azure IoT Central-toepassing kunt configureren.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5358563de9f35d2cef74a32cb1794de418066810
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955793"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Zelfstudie: Regels en acties voor uw apparaat configureren in Azure IoT Central

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In deze zelfstudie maakt u een regel die een e-mail verzendt wanneer de temperatuur in een aangesloten airconditioningapparaat hoger is dan 32 &deg;C.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een regel op basis van telemetrie maken
> * Een actie toevoegen

## <a name="prerequisites"></a>Vereisten

Voer voordat u begint de zelfstudie [Een nieuw apparaattype definiëren in uw toepassing](tutorial-define-device-type.md) uit om de apparaatsjabloon **Aangesloten airconditioner** te maken om mee te werken.

## <a name="create-a-telemetry-based-rule"></a>Een regel op basis van telemetrie maken

1. Als u een nieuwe regel voor telemetrie wilt toevoegen aan uw toepassing, selecteert u in het linkerdeel venster **Apparaatbeheer**:

    ![De pagina Apparaatsjablonen](media/tutorial-configure-rules/templatespage1.png)

    U ziet de apparaatsjabloon **Aangesloten airconditioner (1.0.0)**  die u in de vorige zelfstudie hebt gemaakt.

2. Als u uw sjabloon wilt aanpassen, selecteert u de **verbonden Air-voorwaarde** sjabloon die u hebt gemaakt in de vorige zelf studie.

3. Als u een regel op basis van telemetrie wilt toevoegen in de weer gave **regels** , selecteert u **regels**, selecteert u **+ nieuwe regel**en selecteert u **telemetrie**:

    ![Weergave Regels](media/tutorial-configure-rules/newrule.png)

5. Gebruik de informatie in de volgende tabel om uw regel te definiëren:

    | Instelling                                      | Waarde                             |
    | -------------------------------------------- | ------------------------------    |
    | Naam                                         | Waarschuwing temperatuur airconditioner |
    | Regel inschakelen voor alle apparaten van deze sjabloon | Aan                                |
    | Voorwaarde                                    | Temperatuur is hoger dan 32    |
    | Aggregatie                                  | Geen                              |

    ![Voorwaarde temperatuurregel](media/tutorial-configure-rules/temperaturerule.png)

    Selecteer vervolgens **Opslaan**.

## <a name="add-an-action"></a>Een actie toevoegen

Wanneer u een regel definieert, definieert u ook een actie die moet worden uitgevoerd wanneer aan de voorwaarden van de regel wordt voldaan. In deze zelfstudie maakt u een regel met een actie die een e-mailmelding verstuurt.

1. Als u een **Actie**wilt toevoegen, moet u de regel eerst **Opslaan**. Daarna schuift u omlaag in het deelvenster **Telemetrieregel configureren** en kiest u **+** naast **Acties** en selecteert u vervolgens **E-mail**:

    ![Actie temperatuurregel](media/tutorial-configure-rules/addaction.png)

2. Gebruik de informatie in de volgende tabel om uw actie te definiëren:

    | Instelling   | Waarde                          |
    | --------- | ------------------------------ |
    | Tot        | Uw e-mailadres             |
    | Opmerkingen     | De temperatuur van de airconditioner heeft de drempelwaarde overschreden. |

    > [!NOTE]
    > Om een e-mailmelding te ontvangen, moet het e-mailadres een [gebruikers-ID in de toepassing](howto-administer.md) zijn, en moet die gebruiker zich minimaal één keer hebben aangemeld bij de toepassing.

    ![Actie temperatuurregel](media/tutorial-configure-rules/temperatureaction.png)

3. Selecteer **Opslaan**. De regel wordt weergegeven op de pagina **Regels**.

## <a name="test-the-rule"></a>De regel testen

Kort nadat u de regel hebt opgeslagen, wordt deze actief. Wanneer aan de voorwaarden in de regel wordt voldaan, verzendt uw toepassing een bericht naar het e-mailadres dat u in de actie hebt opgegeven.

> [!NOTE]
> Als u klaar bent met testen, schakelt u de regel uit om geen waarschuwingen meer te ontvangen in uw postvak.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Een regel op basis van telemetrie maken
> * Een actie toevoegen

Nu u een regel op basis van drempelwaarden hebt gedefinieerd, is de voorgestelde volgende stap [het aanpassen van de weergaven van de operator](tutorial-customize-operator.md).

Zie voor meer informatie over verschillende soorten regels in Azure IoT Central en over het parametriseren van de regeldefinitie:
* [Een telemetrieregel maken en meldingen instellen](howto-create-telemetry-rules.md).
* [Een gebeurtenisregel maken en meldingen instellen](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->