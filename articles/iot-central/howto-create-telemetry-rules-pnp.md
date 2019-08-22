---
title: Regels voor telemetrie maken en beheren in uw Azure IoT Central-toepassing | Microsoft Docs
description: Met Azure IoT Central telemetrie-regels kunt u uw apparaten bijna in realtime controleren en automatisch acties aanroepen, zoals het verzenden van een e-mail bericht wanneer de regel wordt geactiveerd.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d6deecf558105701be591c1f08923eca2c1e3bbd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879940"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Een telemetrie-regel maken en meldingen instellen in uw Azure IoT Central-toepassing (preview-functies)

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

U kunt Azure IoT Central gebruiken om uw verbonden apparaten op afstand te bewaken. Met Azure IoT Central-regels kunt u uw apparaten in bijna realtime bewaken en automatisch acties aanroepen, zoals het verzenden van een e-mail bericht of het activeren van Microsoft Flow. Met slechts enkele klikken kunt u de voor waarde definiëren waarvoor de apparaatgegevens moeten worden bewaakt en de bijbehorende actie moet worden geconfigureerd. In dit artikel wordt uitgelegd hoe u regels kunt maken voor het bewaken van de telemetrie die door het apparaat wordt verzonden.

Apparaten kunnen telemetrische metingen gebruiken om numerieke gegevens van het apparaat te verzenden. Een telemetrie regel wordt geactiveerd wanneer de telemetrie van het geselecteerde apparaat een opgegeven drempel overschrijdt.

## <a name="create-a-telemetry-rule"></a>Een telemetrie-regel maken

Voor het maken van een telemetrie-regel moet er ten minste één telemetrie-meting zijn gedefinieerd voor de definitie van het apparaat. In dit voor beeld wordt gebruikgemaakt van een computer apparaat met een gekoelde verkoop, dat een telemetrie van de Tempe ratuur en vochtigheid verzendt. De regel controleert de door het apparaat gemelde Tempe ratuur en verzendt een e-mail wanneer deze meer dan 80 graden gaat.

1. Navigeer naar de pagina **regels** .

1. Als u nog geen regels hebt gemaakt, ziet u het volgende scherm:

    ![Nog geen regels](media/howto-create-telemetry-rules-pnp/rules-landing-page1.png)

1. Selecteer **+ nieuwe regel** om de typen regels te zien die u kunt maken.

1. Selecteer telemetrie om een regel te maken voor het controleren van de telemetrie van apparaten.

    ![Regel typen](media/howto-create-telemetry-rules-pnp/rule-types1.png)


1. Voer een naam in waarmee u de regel kunt identificeren en druk op ENTER.

1. Selecteer in de sectie bereiken de definitie van het apparaat waarvoor u deze regel wilt bereiken. In dit scherm kunt u ook de apparaten filteren waarop de regel van toepassing is met behulp van **+ filter**. De regel is automatisch van toepassing op alle apparaten onder de sjabloon apparaat. Als u de regel wilt uitschakelen, selecteert u de knop **uitschakelen** in de koptekst.

### <a name="configure-the-rule-conditions"></a>De regel voorwaarden configureren

Voor waarde definieert de criteria die door de regel worden bewaakt.

1. Selecteer of u aggregatie wilt **instellen** als aan of uit.

      - Aggregatie is optioneel. Zonder aggregatie wordt de regel geactiveerd voor elk telemetrie-gegevens punt dat voldoet aan de voor waarde. Als de regel bijvoorbeeld zo is geconfigureerd dat deze wordt geactiveerd wanneer de Tempe ratuur hoger is dan 80, wordt de regel bijna direct geactiveerd wanneer het apparaat de Tempe ratuur > 80.
      - Als een statistische functie, zoals gemiddelde, min, Max, aantal is gekozen, moet de gebruiker een **tijd venster** opgeven waarover de voor waarde moet worden geëvalueerd. Als u bijvoorbeeld de periode instelt op 5 minuten en uw regel zoekt naar de gemiddelde Tempe ratuur boven 80, wordt de regel geactiveerd wanneer de gemiddelde Tempe ratuur ten minste vijf minuten hoger is dan 80. De frequentie van de regel evaluatie is hetzelfde als die van het **tijd venster**. in dit voor beeld wordt de regel elke vijf minuten geëvalueerd.

1. Selecteer de telemetrie die u wilt bewaken in de vervolg keuzelijst voor **metingen** .

1. Kies vervolgens een **operator** en geef een **waarde**op.

     ![Voorwaarde](media/howto-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)


>[!NOTE]
>U kunt meer dan één telemetrie-meting toevoegen door **+ voor waarde**te selecteren. Wanneer er meerdere voor waarden worden opgegeven, moeten aan alle voor waarden worden voldaan om de regel te activeren. Elke voor waarde wordt impliciet toegevoegd door een AND-component. Bij het gebruik van aggregatie moet elke meting worden geaggregeerd.

### <a name="configure-actions"></a>Acties configureren

In deze sectie wordt beschreven hoe u acties kunt instellen die moeten worden uitgevoerd wanneer de regel wordt geactiveerd. Acties worden aangeroepen wanneer alle voor waarden die in de regel zijn opgegeven, worden geëvalueerd als waar.

1. Klik op de **+-actie** in de **actie** secties. Hier ziet u de lijst met beschik bare acties.  

    ![Actie toevoegen](media/howto-create-telemetry-rules-pnp/add-action1.png)


1. Kies de actie voor het **e-mail bericht** , voer een weergave naam in voor de actie, een geldig e-mail adres in het veld **aan** en geef een notitie op die moet worden weer gegeven in de hoofd tekst van het e-mail bericht wanneer de regel wordt geactiveerd.

    > [!NOTE]
    > E-mail berichten worden alleen verzonden naar gebruikers die zijn toegevoegd aan de toepassing en die ten minste één keer zijn aangemeld. Meer informatie over [gebruikers beheer](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) in azure IOT Central.

   ![Actie configureren](media/howto-create-telemetry-rules-pnp/configure-action1.png)


1. Als u de actie wilt opslaan, kiest u **gereed**.

1. Klik op **Opslaan**om de regel op te slaan. De regel is binnen enkele minuten Live en begint met de bewaking van de telemetrie die naar uw toepassing wordt verzonden. Wanneer aan de voor waarde die in de regel is opgegeven, wordt voldaan, wordt de geconfigureerde e-mail actie door de regel geactiveerd.

## <a name="parameterize-the-rule"></a>De regel para meters

Regels kunnen bepaalde graad van **Apparaateigenschappen** afleiden als para meters. Het gebruik van para meters is handig in scenario's waarbij de drempel waarden voor telemetrie verschillen voor verschillende apparaten. Kies bij het maken van de regel een eigenschap van het apparaat waarmee de drempel waarde wordt opgegeven, zoals de **maximale ideale drempel**waarde, in plaats van een absoluut getal, zoals 80 graden. Wanneer de regel wordt uitgevoerd, komt deze overeen met de telemetrie van het apparaat met de waarde die is ingesteld in de eigenschap apparaat.

Het gebruik van para meters is een efficiënte manier om het aantal regels te beperken dat moet worden beheerd.

Acties kunnen ook worden geconfigureerd met behulp van de **eigenschap apparaat** als een para meter. Als een e-mail adres als een eigenschap is opgeslagen, kan het worden gebruikt wanneer u het adres **in** definieert.

## <a name="delete-a-rule"></a>Een regel verwijderen

Als u een regel niet meer nodig hebt, verwijdert u deze door de regel te openen en **verwijderen**te kiezen. Als u de regel verwijdert, wordt deze verwijderd uit de sjabloon voor het apparaat en alle bijbehorende apparaten.

## <a name="enable-or-disable-a-rule"></a>Een regel in-of uitschakelen

Kies de regel die u wilt in-of uitschakelen. Scha kelen tussen de knop **inschakelen** of **uitschakelen** in de regel om de regel in of uit te scha kelen voor alle apparaten die in de regel zijn ingedeeld.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Een regel voor een apparaat in-of uitschakelen

Kies de regel die u wilt in-of uitschakelen. Voeg in de sectie **bereiken** een filter toe om een bepaald apparaat in de sjabloon op te nemen of uit te sluiten.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u regels maakt in uw Azure IoT Central-toepassing, is de voorgestelde volgende stap meer informatie [over het beheren van uw apparaten](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
