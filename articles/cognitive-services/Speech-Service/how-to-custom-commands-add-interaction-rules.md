---
title: 'Procedure: een bevestiging toevoegen aan een aangepaste opdracht'
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u bevestigingen voor een opdracht in aangepaste opdrachten implementeert.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307594"
---
# <a name="add-interaction-rules"></a>Interactie regels toevoegen

In dit artikel vindt u informatie over **interactie regels**. Dit zijn aanvullende regels voor het afhandelen van specifieke of complexe situaties. Wanneer u uw eigen aangepaste interactie regels wilt maken, in dit artikel kunt u het gebruik van interactie regels voor de volgende doel scenario's gebruiken:

* Opdrachten bevestigen
* Een stap correctie toevoegen aan opdrachten

Ga naar de sectie met [verwijzingen](./custom-commands-references.md) voor meer informatie over interactie regels.

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:
> [!div class="checklist"]
> * [Procedure: een toepassing maken met eenvoudige opdrachten](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Procedure: para meters toevoegen aan opdrachten](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Bevestigingen toevoegen aan een opdracht

Als u een bevestiging wilt toevoegen, gebruikt u de opdracht **SetTemperature** . Voor het bereiken van de bevestiging maakt u interactie regels met behulp van de volgende stappen.

1. Selecteer de opdracht **SetTemperature** in het linkerdeel venster.
2. Voeg interactie regels toe door **toevoegen** in het middelste deel venster te selecteren en vervolgens de opdracht regels bevestigen van de **interactie**te selecteren  >  **Confirm command**.

    Hiermee worden 3 interactie regels toegevoegd met deze regel wordt de gebruiker gevraagd om de datum en tijd van het alarm te bevestigen en wordt een bevestiging (Ja/Nee) voor de volgende beurt verwacht.

    1. Wijzig de interactie regel voor de **bevestigings opdracht** volgens de volgende configuratie
        1. **Naam** wijzigen in **`Confirm Temperature`** .
        1. Een nieuwe voor waarde toevoegen als **vereiste para meters > Tempe ratuur**
        1. Een nieuwe actie toevoegen als- **Type > spraak antwoord verzenden > `Are you sure you want to set the temperature as {Temperature} degrees?` **
        1. Behoud de standaard waarde van de **verwachte bevestiging van de gebruiker** in het gedeelte verwachtingen.
      
         > [!div class="mx-imgBorder"]
         > ![Vereist parameter antwoord maken](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Wijzig de interactie regel voor het bevestigen van de **bevestiging** om een geslaagde bevestiging te verwerken (gebruiker zei ja).
      
          1. Wijzig de **naam** in **`Confirmation temperature succeeded`** .
          1. De al bestaande **bevestiging is voltooid** .
          1. Een nieuwe voor waarde toevoegen als- **Type > vereiste para meters > temperatuur**
          1. Wijzig de standaard waarde van de **status na uitvoering** als **regels**voor het uitvoeren van voltooiing.

    1. Wijzig de **bevestiging geweigerd** (gebruiker zei geen) om scenario's af te handelen wanneer de bevestiging wordt geweigerd.

          1. Wijzig de **naam** in **`Confirmation temperature denied`** .
          1. De al bestaande **bevestiging is geweigerd** .
          1. Een nieuwe voor waarde toevoegen als- **Type > vereiste para meters > temperatuur**
          1. Een nieuwe actie toevoegen als- **Type > spraak antwoord verzenden > `No problem. What temperature then?` **
          1. Wijzig de standaard waarde van de **status na uitvoering** als **wachten op de invoer van de gebruiker**.

> [!IMPORTANT]
> In dit artikel hebt u de ingebouwde bevestigings mogelijkheid gebruikt. U kunt ook hetzelfde doen door de interactie regels een voor een hand matig toe te voegen.
   

### <a name="try-out-the-changes"></a>De wijzigingen proberen

Selecteer **trainen**, wacht totdat de training is voltooid en selecteer **testen**.

- Invoer: Stel de Tempe ratuur in op 80 graden
- Output: OK 80?
- Invoer: Nee
- Uitvoer: geen probleem. Wat is de Tempe ratuur?
- Invoer: 83 graden
- Output: OK 83?
- Invoer: Ja
- Uitvoer: OK, Tempe ratuur instellen op 83 graden


## <a name="implementing-corrections-in-a-command"></a>Correcties implementeren in een opdracht

In deze sectie configureert u een correctie voor één stap, die wordt gebruikt wanneer de uitvoering van de actie al is uitgevoerd. U ziet ook een voor beeld van hoe correctie standaard is ingeschakeld voor het geval de opdracht nog niet is vervuld. Als u een correctie wilt toevoegen wanneer de opdracht niet is voltooid, voegt u een nieuwe para meter **AlarmTone**toe.

Selecteer de opdracht **SetAlarm** in het linkerdeel venster en voeg een nieuwe para meter **AlarmTone**toe.
        
- **Naam** > `AlarmTone`
- **Type** > teken reeks
- **Standaard waarde** > `Chimes`
- **Configuratie** > vooraf gedefinieerde invoer waarden van interne catalogus accepteren
- **Vooraf gedefinieerde invoer waarden**  >  `Chimes` , `Jingle` en `Echo` . Elk als afzonderlijke vooraf gedefinieerde invoer.


Werk vervolgens het antwoord voor de DateTime-para meter bij naar `Ready to set alarm with tone as {AlarmTone}. For what time?` . Wijzig vervolgens de voltooiings regel als volgt.

1. Selecteer de bestaande voltooiings regel **ConfirmationResponse**.
1. Beweeg de muis aanwijzer in het rechter paneel over de bestaande actie en selecteer de knop **bewerken** .
1. Spraak reactie bijwerken naar`Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>De wijzigingen proberen

Selecteer **trainen**, wacht tot de training is voltooid en selecteer **testen**.
Probeer de volgende uitingen:

- Invoer: een alarm instellen
- Output: gereed om alarm in te stellen met een Toon klok. Voor welke tijd?
- Invoer: Stel een alarm in met een Toon als jingle voor 9 am morgen
- Uitvoer: OK, alarm ingesteld voor 2020-05-30 09:00:00. Het alarm signaal is jingle.

> [!IMPORTANT]
> U ziet hoe de alarm Toon kan worden gewijzigd zonder uitdrukkelijke configuratie in een doorlopende opdracht, d.w.z. Wanneer de opdracht nog niet is voltooid. **De correctie is standaard ingeschakeld voor alle opdracht parameters, ongeacht het Schakel nummer als aan de opdracht nog moet worden voldaan.**

### <a name="correction-when-command-is-completed"></a>Correctie wanneer opdracht is voltooid

Het platform aangepaste opdrachten biedt ook de mogelijkheid voor een correctie van één stap, zelfs wanneer de opdracht is voltooid. Deze functie is niet standaard ingeschakeld en moet expliciet worden geconfigureerd. Voer de volgende stappen uit om een correctie met één stap te configureren.

1. Voeg in de **SetAlarm** -opdracht een interactie regel van het type **Update Previous** toe om de eerder ingestelde waarschuwing bij te werken. Wijzig de naam van de standaard **naam** van de interactie regel om **vorige waarschuwing**bij te werken.
1. Behoud de standaard waarde **vorige opdracht moet worden bijgewerkt** als dat zo is.
1.  Voeg een nieuwe voor waarde toe als **Type > vereiste para meter > datetime**.
1. Een nieuwe actie toevoegen als **Type > het verzenden van een spraak antwoord > `Updating previous alarm time to {DateTime}.` eenvoudige editor >**
1. Behoud de standaard waarde van de status na uitvoering van de **opdracht is voltooid**.

### <a name="try-out-the-changes"></a>De wijzigingen proberen

Selecteer **trainen**, wacht totdat de training is voltooid en selecteer **testen**.

- Invoer: een alarm instellen
- Output: gereed om alarm in te stellen met een Toon klok. Hoe laat?
- Invoer: Stel een alarm in met een Toon als jingle voor 9 am morgen
- Uitvoer: OK, alarm ingesteld voor 2020-05-21 09:00:00. Het alarm signaal is jingle.
- Invoer: Nee, 8 uur
- Output: de vorige alarm tijd wordt bijgewerkt naar 2020-05-29 08:00.

> [!NOTE]
> In een echte toepassing moet u in de sectie acties van deze correctie regel ook een activiteit naar de client terugsturen of een HTTP-eind punt aanroepen om de alarm tijd in uw systeem bij te werken. Deze actie moet eenvoudigweg verantwoordelijk zijn voor het bijwerken van de alarm tijd en niet van een ander kenmerk van de opdracht, in dit geval alarm Toon.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: sjablonen voor het genereren van een taal toevoegen voor spraak reacties](./how-to-custom-commands-add-language-generation-templates.md)