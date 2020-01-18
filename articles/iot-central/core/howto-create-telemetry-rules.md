---
title: Telemetrie-regels gebruiken in uw Azure IoT Central-toepassing | Microsoft Docs
description: Met Azure IoT Central telemetrie-regels kunt u uw apparaten bijna in realtime controleren en automatisch acties aanroepen, zoals het verzenden van een e-mail bericht wanneer de regel wordt geactiveerd.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0b24c064424b00fa9acb96b03c0a3c5ca69f67f2
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264349"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Een telemetrie-regel maken en meldingen instellen in uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

U kunt Azure IoT Central gebruiken om uw verbonden apparaten op afstand te bewaken. Met Azure IoT Central-regels kunt u uw apparaten in bijna realtime bewaken en automatisch acties aanroepen, zoals het verzenden van een e-mail bericht of het activeren van Microsoft Flow. Met slechts enkele klikken kunt u de voor waarde definiëren waarvoor de apparaatgegevens moeten worden bewaakt en de bijbehorende actie moet worden geconfigureerd. In dit artikel wordt uitgelegd hoe u regels kunt maken voor het bewaken van de telemetrie die door het apparaat wordt verzonden.

Apparaten kunnen telemetrische metingen gebruiken om numerieke gegevens van het apparaat te verzenden. Een telemetrie regel wordt geactiveerd wanneer de telemetrie van het geselecteerde apparaat een opgegeven drempel overschrijdt.

## <a name="create-a-telemetry-rule"></a>Een telemetrieregel maken

Voor het maken van een telemetrie-regel moet er ten minste één telemetrie-meting zijn gedefinieerd. In dit voor beeld wordt gebruikgemaakt van een computer apparaat met een gekoelde verkoop, dat een telemetrie van de Tempe ratuur en vochtigheid verzendt. De regel controleert de door het apparaat gemelde Tempe ratuur en verzendt een e-mail wanneer deze meer dan 70&deg; F overschrijdt.

1. Ga op de pagina met **Apparaatinstellingen** naar de sjabloon voor het apparaat waarvoor u de regel wilt toevoegen.

1. Als u nog geen regels hebt gemaakt, ziet u het volgende scherm:

    ![Nog geen regels](media/howto-create-telemetry-rules/rules_landing_page1.png)

1. Selecteer op het tabblad **regels** **+ nieuwe regel** om de typen regels te zien die u kunt maken.

1. Selecteer **telemetrie** om een regel te maken voor het controleren van de telemetrie van apparaten.

    ![Regel typen](media/howto-create-telemetry-rules/rule_types1.png)

1. Voer een naam in waarmee u de regel in deze apparaatprofiel kunt identificeren.

1. Als u de regel onmiddellijk wilt inschakelen voor alle apparaten die voor deze sjabloon zijn gemaakt, schakelt u **regel inschakelen voor alle apparaten van deze sjabloon**in.

   ![Regel Details](media/howto-create-telemetry-rules/rule_detail1.png)

    De regel is automatisch van toepassing op alle apparaten onder de sjabloon apparaat.

### <a name="configure-the-rule-conditions"></a>De regel voorwaarden configureren

Voor waarde definieert de criteria die door de regel worden bewaakt.

1. Selecteer **+** naast **voor waarden** om een nieuwe voor waarde toe te voegen.

1. Selecteer de telemetrie die u wilt bewaken in de vervolg keuzelijst voor **metingen** .

1. Kies vervolgens **aggregatie**, **operator**en geef een **drempel** waarde op.
   - Aggregatie is optioneel. Zonder aggregatie wordt de regel geactiveerd voor elk telemetrie-gegevens punt dat voldoet aan de voor waarde. Als de regel bijvoorbeeld zo is geconfigureerd dat deze wordt geactiveerd wanneer de Tempe ratuur hoger is dan 70&deg; F, wordt de regel bijna direct geactiveerd wanneer het apparaat de Tempe ratuur > 70 rapporteert.
   - Als een statistische functie zoals gemiddelde, min, Max, aantal is gekozen, moet de gebruiker een **geaggregeerd tijd venster** opgeven waarvoor de voor waarde moet worden geëvalueerd. Als u bijvoorbeeld de periode instelt op ' 5 minuten ' en uw regel zoekt naar de gemiddelde Tempe ratuur boven 70, wordt de regel geactiveerd wanneer de gemiddelde Tempe ratuur hoger is dan 70&deg; F gedurende ten minste vijf minuten. De frequentie van de regel evaluatie is hetzelfde als het **cumulatieve tijd venster**, wat betekent, in dit voor beeld wordt de regel elke vijf minuten geëvalueerd.

     ![Voorwaarde](media/howto-create-telemetry-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Er kan meer dan een telemetrie-meting worden toegevoegd onder **voor waarde**. Wanneer er meerdere voor waarden worden opgegeven, moeten aan alle voor waarden worden voldaan om de regel te activeren. Elke conditon wordt impliciet toegevoegd door een AND-component. Bij het gebruik van aggregatie moet elke meting worden geaggregeerd.

### <a name="configure-actions"></a>Acties configureren

In deze sectie wordt beschreven hoe u acties kunt instellen die moeten worden uitgevoerd wanneer de regel wordt geactiveerd. Acties worden aangeroepen wanneer alle voor waarden die in de regel zijn opgegeven, worden geëvalueerd als waar.

1. Kies de **+** naast **acties**. Hier ziet u de lijst met beschik bare acties.  

    ![Actie toevoegen](media/howto-create-telemetry-rules/add_action1.png)

1. Kies de **e-mail** actie, voer een geldig e-mail adres in het veld **aan** in en geef een notitie op die moet worden weer gegeven in de hoofd tekst van het e-mail bericht wanneer de regel wordt geactiveerd.

    > [!NOTE]
    > E-mail berichten worden alleen verzonden naar gebruikers die zijn toegevoegd aan de toepassing en die ten minste één keer zijn aangemeld. Meer informatie over [gebruikers beheer](howto-administer.md) in azure IOT Central.

   ![Actie configureren](media/howto-create-telemetry-rules/configure_action1.png)

1. Klik op **Opslaan**om de regel op te slaan. De regel is binnen enkele minuten Live en begint met de bewaking van de telemetrie die naar uw toepassing wordt verzonden. Wanneer aan de voor waarde die in de regel is opgegeven, wordt voldaan, wordt de geconfigureerde e-mail actie door de regel geactiveerd.

U kunt andere acties toevoegen aan de regel, zoals Microsoft Flow en webhooks. U kunt Maxi maal vijf acties per regel toevoegen.

- [Microsoft flow actie](howto-add-microsoft-flow.md) voor het starten van een werk stroom in Microsoft flow wanneer een regel wordt geactiveerd 
- [Webhook-actie](howto-create-webhooks.md) om andere services op de hoogte te stellen wanneer een regel wordt geactiveerd

## <a name="parameterize-the-rule"></a>De regel para meters

Regels kunnen bepaalde graad van **Apparaateigenschappen** afleiden als para meters. Het gebruik van para meters is handig in scenario's waarbij de drempel waarden voor telemetrie verschillen voor verschillende apparaten. Wanneer u de regel maakt, kiest u een eigenschap van het apparaat waarmee de drempel waarde wordt opgegeven, zoals de **maximale ideale drempel**waarde, in plaats van een absoluut getal, zoals 70&deg; F. Wanneer de regel wordt uitgevoerd, komt deze overeen met de telemetrie van het apparaat met de waarde die is ingesteld in de eigenschap apparaat.

Het gebruik van para meters is een efficiënte manier om het aantal regels te verminderen dat per apparaatprofiel moet worden beheerd.

Acties kunnen ook worden geconfigureerd met behulp van de **eigenschap apparaat** als een para meter. Als een e-mail adres als een eigenschap is opgeslagen, kan het worden gebruikt wanneer u het adres **in** definieert.

## <a name="delete-a-rule"></a>Een regel verwijderen

Als u een regel niet meer nodig hebt, verwijdert u deze door de regel te openen en **verwijderen**te kiezen. Als u de regel verwijdert, wordt deze verwijderd uit de sjabloon voor het apparaat en alle bijbehorende apparaten.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Een regel voor een apparaatprofiel in-of uitschakelen

Navigeer naar het apparaat en kies de regel die u wilt in-of uitschakelen. Scha kelen tussen de regel **inschakelen voor alle apparaten van deze sjabloon** op de regel om de regel in of uit te scha kelen voor alle apparaten die aan de sjabloon voor het apparaat zijn gekoppeld.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Een regel voor een apparaat in-of uitschakelen

Navigeer naar het apparaat en kies de regel die u wilt in-of uitschakelen. Schakel de regel **inschakelen voor dit apparaat** in of uit om de regel voor dat apparaat in of uit te scha kelen.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u regels kunt maken in uw Azure IoT Central-toepassing, volgt u een van de volgende stappen:

- [Microsoft Flow actie toevoegen in regels](howto-add-microsoft-flow.md)
- [Webhook-actie toevoegen in regels](howto-create-webhooks.md)
- [Meerdere acties groeperen om uit te voeren vanuit een of meer regels](howto-use-action-groups.md)
- [Uw apparaten beheren](howto-manage-devices.md)
