---
title: Gebeurtenis regels maken en beheren in uw Azure IoT Central-toepassing | Microsoft Docs
description: Met Azure IoT Central-gebeurtenis regels kunt u uw apparaten bijna in realtime controleren en automatisch acties aanroepen, zoals het verzenden van een e-mail bericht wanneer de regel wordt geactiveerd.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: f932cb9621e4cfc62e2bb00d11030a7ff03450fc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954017"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Een gebeurtenis regel maken en meldingen instellen in uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

U kunt Azure IoT Central gebruiken om uw verbonden apparaten op afstand te bewaken. Met Azure IoT Central-regels kunt u uw apparaten in bijna realtime bewaken en automatisch acties aanroepen, zoals het verzenden van een e-mail bericht of het activeren van Microsoft Flow. Met slechts enkele klikken kunt u de voor waarde definiëren waarvoor de apparaatgegevens moeten worden bewaakt en de bijbehorende actie moet worden geconfigureerd. In dit artikel wordt uitgelegd hoe u regels kunt maken voor het bewaken van gebeurtenissen die door het apparaat worden verzonden.

Apparaten kunnen meting van gebeurtenissen gebruiken om belang rijke of informatieve apparaatinstellingen te verzenden. Er wordt een gebeurtenis regel geactiveerd wanneer de geselecteerde gebeurtenis van het apparaat wordt gerapporteerd door het apparaat.

## <a name="create-an-event-rule"></a>Een gebeurtenisregel maken

Voor het maken van een gebeurtenis regel moet er ten minste één gebeurtenis meting zijn gedefinieerd in de sjabloon voor het apparaat. In dit voor beeld wordt gebruikgemaakt van een computer apparaat met gekoelde verkoop dat een ventilator motor fout gebeurtenis rapporteert. De regel controleert de gebeurtenis die wordt gerapporteerd door het apparaat en verzendt een e-mail wanneer de gebeurtenis wordt gerapporteerd.

1. Ga op de pagina met **Apparaatinstellingen** naar de sjabloon voor het apparaat waarvoor u de regel wilt toevoegen.

1. Als u nog geen regels hebt gemaakt, ziet u het volgende scherm:

    ![Nog geen regels](media/howto-create-event-rules/rules_landing_page1.png)

1. Selecteer op het tabblad **regels** **+ nieuwe regel** om de typen regels te zien die u kunt maken.

1. Kies de tegel **gebeurtenis** om een regel voor gebeurtenis bewaking te maken.

    ![Regel typen](media/howto-create-event-rules/rule_types1.png)

1. Voer een naam in waarmee u de regel in deze apparaatprofiel kunt identificeren.

1. Als u de regel onmiddellijk wilt inschakelen voor alle apparaten die op basis van deze sjabloon zijn gemaakt, schakelt u **regel inschakelen voor alle apparaten van deze sjabloon**in.

    ![Regel Details](media/howto-create-event-rules/rule_detail1.png)

    De regel is automatisch van toepassing op alle apparaten onder de sjabloon apparaat.

### <a name="configure-the-rule-conditions"></a>De regel voorwaarden configureren

Voor waarde definieert de criteria die door de regel worden bewaakt.

1. Kies de **+** naast **voor waarden** om een nieuwe voor waarde toe te voegen.

1. Kies de gebeurtenis die u wilt bewaken in de vervolg keuzelijst voor metingen. In dit voor beeld is een **ventilator motor fout** gebeurtenis geselecteerd.

   ![Voorwaarde](media/howto-create-event-rules/condition_filled_out1.png)

1. U kunt eventueel ook **tellen** als **aggregatie** instellen en de bijbehorende drempel waarde opgeven.

   - Zonder aggregatie wordt de regel geactiveerd voor elk gebeurtenis gegevens punt dat voldoet aan de voor waarde. Als u bijvoorbeeld de voor waarde van de regel zodanig configureert dat deze wordt geactiveerd wanneer een **ventilator motor fout** gebeurtenis optreedt, wordt de regel vrijwel onmiddellijk geactiveerd wanneer het apparaat die gebeurtenis rapporteert.
   - Als Count als een statistische functie wordt gebruikt, moet u een **drempel waarde** en een **geaggregeerd tijd venster** opgeven waarvoor de voor waarde moet worden geëvalueerd. In dit geval wordt het aantal gebeurtenissen geaggregeerd en wordt de regel alleen geactiveerd als het geaggregeerde aantal gebeurtenissen overeenkomt met de drempel waarde.

     Als u bijvoorbeeld een waarschuwing wilt ontvangen wanneer er binnen vijf minuten meer dan drie Device-gebeurtenissen zijn, selecteert u de gebeurtenis en stelt u de statistische functie in op ' aantal ', de operator ' groter dan ' en ' drempelwaarde ' als 3. Stel de tijds periode voor samen voeging in als 5 minuten. De regel wordt geactiveerd wanneer er binnen vijf minuten meer dan drie gebeurtenissen worden verzonden door het apparaat. De frequentie van de regel evaluatie is hetzelfde als het **cumulatieve tijd venster**, wat betekent, in dit voor beeld wordt de regel elke vijf minuten geëvalueerd.

     ![Gebeurtenis voorwaarde toevoegen](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Er kan meer dan één gebeurtenis meting worden toegevoegd onder **voor waarde**. Wanneer er meerdere voor waarden worden opgegeven, moeten aan alle voor waarden worden voldaan om de regel te activeren. Elke voor waarde wordt impliciet toegevoegd door een AND-component. Bij het gebruik van aggregatie moet elke meting worden geaggregeerd.

### <a name="configure-actions"></a>Acties configureren

In deze sectie wordt beschreven hoe u acties kunt instellen die moeten worden uitgevoerd wanneer de regel wordt geactiveerd. Acties worden aangeroepen wanneer alle voor waarden die in de regel zijn opgegeven, worden geëvalueerd als waar.

1. Kies de **+** naast **acties**. Hier ziet u de lijst met beschik bare acties.

    ![Actie toevoegen](media/howto-create-event-rules/add_action1.png)

1. Kies de **e-mail** actie, voer een geldig e-mail adres in het veld **aan** in en geef een notitie op die moet worden weer gegeven in de hoofd tekst van het e-mail bericht wanneer de regel wordt geactiveerd.

    > [!NOTE]
    > E-mail berichten worden alleen verzonden naar gebruikers die zijn toegevoegd aan de toepassing en die ten minste één keer zijn aangemeld. Meer informatie over [gebruikers beheer](howto-administer.md) in azure IOT Central.

   ![Actie configureren](media/howto-create-event-rules/configure_action1.png)

1. Klik op **Opslaan**om de regel op te slaan. De regel gaat binnen enkele minuten Live en begint met het bewaken van de gebeurtenissen die naar uw toepassing worden verzonden. Wanneer de voor waarde die is opgegeven in de regel overeenkomt, wordt de geconfigureerde e-mail actie door de regel geactiveerd.

U kunt andere acties toevoegen aan de regel, zoals Microsoft Flow en webhooks. U kunt Maxi maal vijf acties per regel toevoegen.

- [Microsoft flow actie](howto-add-microsoft-flow.md) voor het starten van een werk stroom in Microsoft flow wanneer een regel wordt geactiveerd 
- [Webhook-actie](howto-create-webhooks.md) om andere services op de hoogte te stellen wanneer een regel wordt geactiveerd

## <a name="parameterize-the-rule"></a>De regel para meters

Acties kunnen ook worden geconfigureerd met behulp van de **eigenschap apparaat** als een para meter. Als een e-mail adres wordt opgeslagen als een eigenschap van het apparaat, kan het worden gebruikt wanneer u het adres **in** definieert.

## <a name="delete-a-rule"></a>Een regel verwijderen

Als u een regel niet meer nodig hebt, verwijdert u deze door de regel te openen en **verwijderen**te kiezen. Als u de regel verwijdert, wordt deze verwijderd uit de sjabloon voor het apparaat en alle bijbehorende apparaten.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Een regel voor een apparaatprofiel in-of uitschakelen

Navigeer naar het apparaat en kies de regel die u wilt in-of uitschakelen. Scha kelen tussen **regel inschakelen voor alle apparaten van deze sjabloon** om de regel in of uit te scha kelen voor alle apparaten die zijn gekoppeld aan de sjabloon voor het apparaat.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Een regel voor een apparaat in-of uitschakelen

Navigeer naar het apparaat en kies de regel die u wilt in-of uitschakelen. Schakel de regel **inschakelen voor dit apparaat** in of uit om de regel voor dat apparaat in of uit te scha kelen.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u regels kunt maken in uw Azure IoT Central-toepassing, volgt u een van de volgende stappen:

- [Microsoft Flow actie toevoegen in regels](howto-add-microsoft-flow.md)
- [Webhook-actie toevoegen in regels](howto-create-webhooks.md)
- [Meerdere acties groeperen om uit te voeren vanuit een of meer regels](howto-use-action-groups.md)
- [Uw apparaten beheren](howto-manage-devices.md)
