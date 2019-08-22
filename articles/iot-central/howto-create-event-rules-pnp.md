---
title: Gebeurtenis regels maken en beheren in uw Azure IoT Central-toepassing | Microsoft Docs
description: Met Azure IoT Central-gebeurtenis regels kunt u uw apparaten bijna in realtime controleren en automatisch acties aanroepen, zoals het verzenden van een e-mail bericht wanneer de regel wordt geactiveerd.
author: dominicbetts
ms.author: dobett
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1a7fc2b38e8354fb29255bb7f9d6b2c03ed53725
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879953"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Een gebeurtenis regel maken en meldingen instellen in uw Azure IoT Central-toepassing (preview-functies)

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

U kunt Azure IoT Central gebruiken om uw verbonden apparaten op afstand te bewaken. Met Azure IoT Central-regels kunt u uw apparaten in bijna realtime bewaken en automatisch acties aanroepen, zoals het verzenden van een e-mail bericht. Met slechts enkele klikken kunt u de voor waarde definiëren waarvoor de apparaatgegevens moeten worden bewaakt en de bijbehorende actie moet worden geconfigureerd. In dit artikel wordt uitgelegd hoe u regels kunt maken voor het bewaken van gebeurtenissen die door het apparaat worden verzonden.

Apparaten kunnen meting van gebeurtenissen gebruiken om belang rijke of informatieve apparaatinstellingen te verzenden. Er wordt een gebeurtenis regel geactiveerd wanneer de geselecteerde gebeurtenis van het apparaat wordt gerapporteerd door het apparaat.

## <a name="create-an-event-rule"></a>Een gebeurtenisregel maken

Voor het maken van een gebeurtenis regel moet er ten minste één gebeurtenis meting zijn gedefinieerd in de sjabloon voor het apparaat. In dit voor beeld wordt gebruikgemaakt van een computer apparaat met gekoelde verkoop dat een ventilator motor fout gebeurtenis rapporteert. De regel controleert de gebeurtenis die wordt gerapporteerd door het apparaat en verzendt een e-mail wanneer de gebeurtenis wordt gerapporteerd.

1. Navigeer naar de pagina **regels** .

1. Als u nog geen regels hebt gemaakt, ziet u het volgende scherm:

   ![Nog geen regels](media/howto-create-event-rules-pnp/rules-landing-page1.png)

1. Selecteer **+ nieuwe regel** om de typen regels te zien die u kunt maken.

1. Kies **gebeurtenis** om een regel voor gebeurtenis bewaking te maken.

   ![Regel typen](media/howto-create-event-rules-pnp/rule-types1.png)

1. Voer een naam in waarmee u de regel kunt identificeren en druk op ENTER.

1. Selecteer in de sectie **bereiken** de definitie van het apparaat waarop u deze regel wilt beperken. In dit scherm kunt u ook de apparaten filteren waarop de regel van toepassing is met behulp van **+ filter**. De regel is automatisch van toepassing op alle apparaten onder de sjabloon apparaat. Als u de regel wilt uitschakelen, selecteert u de knop **uitschakelen** in de koptekst.

### <a name="configure-the-rule-conditions"></a>De regel voorwaarden configureren

Voor waarde definieert de criteria die door de regel worden bewaakt.

1. Selecteer of u aggregatie wilt **instellen** als aan of uit.

   - Zonder aggregatie wordt de regel geactiveerd voor elk gebeurtenis gegevens punt dat voldoet aan de voor waarde. Als u bijvoorbeeld de voor waarde van de regel zodanig configureert dat deze wordt geactiveerd wanneer een **ventilator motor fout** gebeurtenis optreedt, wordt de regel vrijwel onmiddellijk geactiveerd wanneer het apparaat die gebeurtenis rapporteert.
   - Als **Count** als een statistische functie wordt gebruikt, moet u een **waarde** en **tijd venster** opgeven waarover de voor waarde moet worden geëvalueerd. In dit geval wordt het aantal gebeurtenissen geaggregeerd en wordt de regel alleen geactiveerd als het geaggregeerde aantal gebeurtenissen overeenkomt met de waarde.

1. Kies de gebeurtenis die u wilt bewaken in de vervolg keuzelijst voor **metingen** . In dit voor beeld is een **ventilator motor fout** gebeurtenis geselecteerd.

1. U kunt eventueel ook **tellen** als aggregatie instellen en de waarde opgeven waarmee de regel wordt geactiveerd.

     Als u bijvoorbeeld een waarschuwing wilt ontvangen wanneer er binnen vijf minuten meer dan drie Device-gebeurtenissen zijn, selecteert u de gebeurtenis en stelt u de statistische functie in als **aantal**, operator **groter dan**en **waarde** 3. Stel **tijd venster** in op **5 minuten**. De regel wordt geactiveerd wanneer er binnen vijf minuten meer dan drie gebeurtenissen worden verzonden door het apparaat. De frequentie van de regel evaluatie is hetzelfde als die van het **tijd venster**. in dit voor beeld wordt de regel elke vijf minuten geëvalueerd.

 ![Voorwaarde](media/howto-create-event-rules-pnp/aggregate-condition-filled-out1.png)

> [!NOTE]
> Er kan meer dan één gebeurtenis meting worden toegevoegd onder **voor waarde**. Wanneer er meerdere voor waarden worden opgegeven, moeten aan alle voor waarden worden voldaan om de regel te activeren. Elke voor waarde wordt impliciet toegevoegd door een AND-component. Bij het gebruik van aggregatie moet elke meting worden geaggregeerd.

### <a name="configure-actions"></a>Acties configureren

In deze sectie wordt beschreven hoe u acties kunt instellen die moeten worden uitgevoerd wanneer de regel wordt geactiveerd. Acties worden aangeroepen wanneer alle voor waarden die in de regel zijn opgegeven, worden geëvalueerd als waar.

1. Klik op de **+-actie** in de **actie** secties. Hier ziet u de lijst met beschik bare acties.  

   ![Actie toevoegen](media/howto-create-event-rules-pnp/add-action1.png)

1. Kies de actie voor het **e-mail bericht** , voer een weergave naam in voor de actie, een geldig e-mail adres in het veld **aan** en geef een notitie op die moet worden weer gegeven in de hoofd tekst van het e-mail bericht wanneer de regel wordt geactiveerd.

   > [!NOTE]
   > E-mail berichten worden alleen verzonden naar gebruikers die zijn toegevoegd aan de toepassing en die ten minste één keer zijn aangemeld. Meer informatie over [gebruikers beheer](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) in azure IOT Central.

   ![Actie configureren](media/howto-create-event-rules-pnp/configure-action1.png)

1. Als u de actie wilt opslaan, kiest u **gereed**.

1. Klik op **Opslaan**om de regel op te slaan. De regel gaat binnen enkele minuten Live en start de bewakings gebeurtenissen die naar uw toepassing worden verzonden. Wanneer aan de voor waarde die in de regel is opgegeven, wordt voldaan, wordt de geconfigureerde e-mail actie door de regel geactiveerd.

## <a name="parameterize-the-rule"></a>De regel para meters

Regels kunnen bepaalde graad van **Apparaateigenschappen** afleiden als para meters. Het gebruik van para meters is handig in scenario's waarbij gebeurtenis drempels voor verschillende apparaten verschillen. Wanneer u de regel maakt, kiest u een eigenschap van het apparaat waarmee de drempel waarde wordt opgegeven, zoals de **maximale ideale drempel**waarde, in plaats van een absoluut getal, zoals 3 gebeurtenissen. Wanneer de regel wordt uitgevoerd, komt deze overeen met de gebeurtenissen van het apparaat met de waarde die is ingesteld in de eigenschap apparaat.

Het gebruik van para meters is een efficiënte manier om het aantal regels te beperken dat moet worden beheerd.

Acties kunnen ook worden geconfigureerd met behulp van de **eigenschap apparaat** als een para meter. Als een e-mail adres wordt opgeslagen als een eigenschap van het apparaat, kan het worden gebruikt wanneer u het adres **in** definieert.

## <a name="delete-a-rule"></a>Een regel verwijderen

Als u een regel niet meer nodig hebt, verwijdert u deze door de regel te openen en **verwijderen**te kiezen. Als u de regel verwijdert, wordt deze verwijderd uit de sjabloon voor het apparaat en alle bijbehorende apparaten.

## <a name="enable-or-disable-a-rule"></a>Een regel in-of uitschakelen

Kies de regel die u wilt in-of uitschakelen. Scha kelen tussen de knop **inschakelen** of **uitschakelen** in de regel om de regel in of uit te scha kelen voor alle apparaten die binnen het bereik van de regel vallen.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Een regel voor een apparaat in-of uitschakelen

Kies de regel die u wilt in-of uitschakelen. Voeg in de sectie **bereiken** een filter toe om een bepaald apparaat in de sjabloon op te nemen of uit te sluiten.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u regels maakt in uw Azure IoT Central-toepassing, is de voorgestelde volgende stap meer informatie [over het beheren van uw apparaten](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
