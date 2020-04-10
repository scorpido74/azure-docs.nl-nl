---
title: Zelfstudie - Regels maken en beheren in uw Azure IoT Central-toepassing
description: In deze zelfstudie ziet u hoe de Azure IoT Central-regels u in staat stellen uw apparaten in bijna realtime te controleren en automatisch acties aan te roepen, zoals het verzenden van een e-mail, wanneer de regel wordt geactiveerd.
author: dominicbetts
ms.author: dobett
ms.date: 04/06/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 555da74da65f3b1897a276cf819a263334cfa053
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80999047"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Zelfstudie: Een regel maken en meldingen instellen in uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

U Azure IoT Central gebruiken om uw verbonden apparaten op afstand te controleren. Met azure IoT Central-regels u uw apparaten in bijna realtime controleren en automatisch acties aanroepen, zoals het verzenden van een e-mail. In dit artikel wordt uitgelegd hoe u regels maakt om de telemetrie te controleren die uw apparaten verzenden.

Apparaten gebruiken telemetrie om numerieke gegevens van het apparaat te verzenden. Een regel wordt geactiveerd wanneer de geselecteerde apparaattelemetrie een opgegeven drempelwaarde overschrijdt.

In deze zelfstudie maakt u een regel om een e-mail te&deg; verzenden wanneer de temperatuur in een gesimuleerd milieusensorapparaat hoger is dan 70 F.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een regel maken
> * Een e-mailactie toevoegen

## <a name="prerequisites"></a>Vereisten

Voordat u begint, voltooit u de [toepassing Een Azure IoT Central maken](./quick-deploy-iot-central.md) en Voegt u snel een gesimuleerd apparaat toe aan uw [IoT Central-toepassing](./quick-create-simulated-device.md) om de **MXChip IoT DevKit-apparaatsjabloon** te maken om mee te werken.

## <a name="create-a-rule"></a>Een regel maken

Als u een telemetrieregel wilt maken, moet de apparaatsjabloon ten minste één telemetriewaarde bevatten. Deze zelfstudie maakt gebruik van een gesimuleerd **mxchip IoT DevKit-apparaat** dat telemetrie over temperatuur en vochtigheid verzendt. U hebt deze apparaatsjabloon toegevoegd en een gesimuleerd apparaat gemaakt in het snel [startmaken van een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing.](./quick-create-simulated-device.md) De regel controleert de temperatuur gerapporteerd door het apparaat en stuurt een e-mail wanneer het gaat boven de 70 graden.

1. Selecteer **Regels**in het linkerdeelvenster .

1. Als u nog geen regels hebt gemaakt, ziet u het volgende scherm:

    ![Nog geen regels](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Selecteer **+** om een nieuwe regel toe te voegen.

1. Voer de _naamtemperatuurmonitor in_ om de regel te identificeren en druk op Enter.

1. Selecteer de sjabloon **mxchip IoT DevKit-apparaat.** Standaard is de regel automatisch van toepassing op alle apparaten die aan de apparaatsjabloon zijn gekoppeld. Als u wilt filteren op een subset van de apparaten, selecteert u **+ Filtert** en gebruikt u apparaateigenschappen om de apparaten te identificeren. Als u de regel wilt uitschakelen, schakelt u de knop **Ingeschakeld/Uitgeschakeld** in de regelkop in:

    ![Filters en inschakelen](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>De regelvoorwaarden configureren

Voorwaarden definiëren de criteria die de regel bewaakt. In deze zelfstudie configureert u de regel&deg; om te vuren wanneer de temperatuur hoger is dan 70 F.

1. Selecteer **Temperatuur** in de **vervolgkeuzelijst Telemetrie.**

1. Kies vervolgens **Is groter dan** als operator en voer _70_ in als de **waarde**. **Operator**

    ![Voorwaarde](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Optioneel u een **tijdaggregatie**instellen. Wanneer u een tijdsaggregatie selecteert, moet u ook een aggregatietype selecteren, zoals gemiddeld of som uit de vervolgkeuzelijst aggregatie.

    * Zonder aggregatie wordt de regel geactiveerd voor elk telemetriegegevenspunt dat aan de voorwaarde voldoet. Als u bijvoorbeeld de regel configureert om te activeren wanneer de temperatuur boven de 70 ligt, wordt de regel vrijwel direct geactiveerd wanneer de temperatuur van het apparaat deze waarde overschrijdt.
    * Bij aggregatie wordt de regel geactiveerd als de totale waarde van de telemetriegegevenspunten in het tijdvenster voldoet aan de voorwaarde. Als u bijvoorbeeld de regel configureert om te activeren wanneer de temperatuur hoger is dan 70 en met een gemiddelde tijdsaggregatie van 10 minuten, wordt de regel geactiveerd wanneer het apparaat een gemiddelde temperatuur rapporteert van meer dan 70, berekend over een interval van 10 minuten.

     ![Geaggregeerde voorwaarde](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

U meerdere voorwaarden aan een regel toevoegen door **+ Voorwaarde**te selecteren. Wanneer meerdere voorwaarden zijn opgegeven, moet aan alle voorwaarden worden voldaan om de regel te activeren. Elke voorwaarde wordt vergezeld `AND` door een impliciete clausule. Als u tijdaggregatie met meerdere voorwaarden gebruikt, moeten alle telemetriewaarden worden samengevoegd.

### <a name="configure-actions"></a>Acties configureren

Nadat u de voorwaarde hebt gedefinieerd, stelt u de acties in die u moet uitvoeren wanneer de regel wordt geactiveerd. Acties worden ingeroepen wanneer alle voorwaarden die in de regel zijn opgegeven, worden geëvalueerd in true.

1. Selecteer **+ E-mail** in de sectie **Acties.**

1. Voer _de waarschuwing Temperatuur_ in als de weergavenaam voor de actie, uw e-mailadres in het veld **Aan** en u moet het apparaat _controleren!_ als een notitie te verschijnen in de body van de e-mail.

    > [!NOTE]
    > E-mails worden alleen verzonden naar de gebruikers die zijn toegevoegd aan de toepassing en hebben ingelogd ten minste eenmaal. Meer informatie over [gebruikersbeheer](howto-administer.md) in Azure IoT Central.

   ![Actie configureren](media/tutorial-create-telemetry-rules/configure-action1.png)

1. Als u de actie wilt opslaan, kiest u **Gereed**. U meerdere acties aan een regel toevoegen.

1. Als u de regel wilt opslaan, kiest **u Opslaan**. De regel gaat binnen een paar minuten live en begint met het monitoren van telemetrie die naar uw toepassing wordt verzonden. Wanneer aan de in de regel opgegeven voorwaarde is voldaan, wordt de geconfigureerde e-mailactie geactiveerd.

Na een tijdje ontvangt u een e-mailbericht wanneer de regel wordt geactiveerd:

![Voorbeeld e-mail](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Een regel verwijderen

Als u geen regel meer nodig hebt, verwijdert u deze door de regel te openen en **Verwijderen te**kiezen.

## <a name="enable-or-disable-a-rule"></a>Een regel in- of uitschakelen

Kies de regel die u wilt in- of uitschakelen. Schakel de knop **Ingeschakeld/uitgeschakeld** in de regel in om de regel in of uit te schakelen voor alle apparaten die in de regel zijn verdeeld.

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Een regel voor specifieke apparaten in- of uitschakelen

Kies de regel die u wilt aanpassen. Gebruik een of meer filters in de sectie **Doelapparaten** om het bereik van de regel te beperken tot de apparaten die u wilt controleren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

* Een regel op basis van telemetrie maken
* Een actie toevoegen

Nu u een op drempels gebaseerde regel hebt gedefinieerd, is de voorgestelde volgende stap om te leren hoe u:

> [!div class="nextstepaction"]
> [Continue gegevensexport configureren](./howto-export-data.md).
