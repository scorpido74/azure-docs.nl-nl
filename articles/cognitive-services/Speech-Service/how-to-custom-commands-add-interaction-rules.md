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
ms.openlocfilehash: 7d6c0928196c9e8e1abf6aa7f724a58753ce3d2a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289039"
---
# <a name="add-interaction-rules"></a>Interactieregels toevoegen

In dit artikel vindt u meer informatie over **interactie regels**. Dit zijn aanvullende regels voor het afhandelen van specifieke of complexe situaties. Wanneer u uw eigen aangepaste interactie regels wilt maken, in dit artikel kunt u het gebruik van interactie regels voor de volgende doel scenario's gebruiken:

* Opdrachten bevestigen
* Een correctie met één stap toevoegen aan opdrachten

Ga voor meer informatie over interactie regels naar het gedeelte [verwijzingen](./custom-commands-references.md) .

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:
> [!div class="checklist"]
> * [Procedure: een toepassing maken met eenvoudige opdrachten](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Procedure: para meters toevoegen aan opdrachten](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Bevestigingen toevoegen aan een opdracht

Als u een bevestiging wilt toevoegen, gebruikt u de opdracht **SetTemperature** . U kunt met behulp van de volgende stappen interactie regels maken om de bevestiging te krijgen.

1. Selecteer de opdracht **SetTemperature** in het linkerdeel venster.
1. Voeg interactie regels toe door **toevoegen** te selecteren in het middelste deel venster. Selecteer vervolgens de opdracht regels bevestigen van de **interactie**  >  **Confirm command**.

    Met deze actie worden drie interactie regels toegevoegd waarmee de gebruiker wordt gevraagd om de datum en tijd van de waarschuwing te bevestigen en verwacht een bevestiging (Ja/Nee) voor de volgende beurt.

    1. Wijzig de interactie regel voor de **bevestigings opdracht** volgens de volgende configuratie:
        1. Wijzig **naam** om de **Tempe ratuur te bevestigen**.
        1. Voeg een nieuwe voor waarde toe als **vereiste para meters**voor de  >  **Tempe ratuur**.
        1. Een nieuwe actie toevoegen als het **type**  >  **spraak antwoord verzenden**  >  **weet u zeker dat u de Tempe ratuur wilt instellen als {Tempe ratuur} graden?**
        1. Behoud de standaard waarde van de **verwachte bevestiging van de gebruiker** in het gedeelte **verwachtingen** .
      
         > [!div class="mx-imgBorder"]
         > ![Vereist parameter antwoord maken](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Wijzig de interactie regel voor het bevestigen van de **bevestiging** om een geslaagde bevestiging te verwerken (gebruiker zei ja).
      
          1. Wijzig **naam** in **bevestigings temperatuur geslaagd**.
          1. De al bestaande **bevestiging is voltooid** .
          1. Voeg een nieuwe voor waarde toe als **type**  >  **vereiste para meters**  >  **temperatuur**.
          1. Wijzig de standaard waarde van de **status na uitvoering** als **regels**voor het uitvoeren van voltooiing.

    1. Wijzig de verwerkings regel voor **Geweigerde bevestiging** om scenario's te verwerken wanneer de bevestiging wordt geweigerd (gebruiker zei nee).

          1. Wijzig **naam** in **bevestigings temperatuur geweigerd**.
          1. De al bestaande **bevestiging is geweigerd** .
          1. Voeg een nieuwe voor waarde toe als **type**  >  **vereiste para meters**  >  **temperatuur**.
          1. Een nieuwe actie toevoegen als **type**  >  **verzenden spraak antwoord**  >  **geen probleem. Wat is de Tempe ratuur?**
          1. Wijzig de standaard waarde van de **status na uitvoering** als **wachten op de invoer van de gebruiker**.

> [!IMPORTANT]
> In dit artikel hebt u de ingebouwde bevestigings functie gebruikt. U kunt ook hand matig interactie regels één voor één toevoegen.
   

### <a name="try-out-the-changes"></a>De wijzigingen proberen

Selecteer **trainen**, wacht totdat de training is voltooid en selecteer **testen**.

- **Invoer**: Stel de Tempe ratuur in op 80 graden
- **Uitvoer**: weet u zeker dat u de Tempe ratuur wilt instellen op 80 graden?
- **Invoer**: Nee
- **Uitvoer**: geen probleem. Wat is de Tempe ratuur?
- **Invoer**: 72 graden
- **Uitvoer**: weet u zeker dat u de Tempe ratuur wilt instellen op 72 graden?
- **Invoer**: Ja
- **Uitvoer**: OK, Tempe ratuur instellen op 83 graden


## <a name="implement-corrections-in-a-command"></a>Correcties implementeren in een opdracht

In deze sectie configureert u een correctie voor één stap, die wordt gebruikt nadat de uitvoering is al uitgevoerd. U ziet ook een voor beeld van hoe een correctie standaard is ingeschakeld voor het geval de opdracht nog niet is vervuld. Als u een correctie wilt toevoegen wanneer de opdracht niet is voltooid, voegt u de nieuwe para meter **AlarmTone**toe.

Selecteer de opdracht **SetAlarm** in het linkerdeel venster en voeg de nieuwe para meter **AlarmTone**toe.
        
- **Naam**  >  **AlarmTone**
- **Type**  >  **Teken reeks**
- **Standaard waarde**  >  **Klokken**
- **Configuratie**  >  **Vooraf gedefinieerde invoer waarden accepteren uit de interne catalogus**
- **Vooraf gedefinieerde invoer waarden**  >  **Klokken**, **Jingle**en **echo** als afzonderlijke vooraf gedefinieerde invoer


Vervolgens werkt u de reactie voor de para meter **DateTime** bij op **gereed om een waarschuwing in te stellen met een Toon als {AlarmTone}. Voor welke tijd?**. Wijzig vervolgens de voltooiings regel als volgt:

1. Selecteer de bestaande voltooiings regel **ConfirmationResponse**.
1. Beweeg de muis aanwijzer in het rechterdeel venster over de bestaande actie en selecteer **bewerken**.
1. Werk de spraak reactie op **OK, alarm ingesteld voor {DateTime}. Het alarm signaal is {AlarmTone}.**

### <a name="try-out-the-changes"></a>De wijzigingen proberen

Selecteer **trainen**, wacht totdat de training is voltooid en selecteer **testen**.
Probeer de volgende uitingen:

- **Invoer**: Stel een alarm in.
- **Output**: gereed om alarm in te stellen met een Toon klok. Voor welke tijd?
- **Invoer**: Stel een alarm in met de Toon als jingle voor 9 am morgen.
- **Uitvoer**: OK, alarm ingesteld voor 2020-05-30 09:00:00. Het alarm signaal is jingle.

> [!IMPORTANT]
> De alarm Toon kan worden gewijzigd zonder uitdrukkelijke configuratie in een doorlopende opdracht, bijvoorbeeld wanneer de opdracht nog niet is voltooid. *Een correctie is standaard ingeschakeld voor alle opdracht parameters, ongeacht het Schakel nummer als aan de opdracht nog moet worden voldaan.*

### <a name="correction-when-command-is-completed"></a>Correctie wanneer opdracht is voltooid

Het aangepaste opdrachten platform biedt ook de mogelijkheid voor een correctie met één stap, zelfs wanneer de opdracht is voltooid. Deze functie is niet standaard ingeschakeld. Deze moet expliciet worden geconfigureerd. Voer de volgende stappen uit om een correctie met één stap te configureren.

1. Voeg in de opdracht **SetAlarm** een interactie regel van de opdracht type **Update Previous** toe om de eerder ingestelde waarschuwing bij te werken. Wijzig de naam van de standaard **naam** van de interactie regel om **vorige waarschuwing**bij te werken.
1. Behoud de standaard waarde **vorige opdracht moet worden bijgewerkt** als dat zo is.
1. Voeg een nieuwe voor waarde toe als vereist **type**  >  **para meter**  >  **DateTime**.
1. Een nieuwe actie toevoegen als **type**  >  **verzenden spraak antwoord**  >  **Simple editor**de  >  **vorige alarm tijd bijwerken naar {DateTime}.**
1. Wijzig de standaard waarde van de **status na uitvoering** als **opdracht is voltooid**.

### <a name="try-out-the-changes"></a>De wijzigingen proberen

Selecteer **trainen**, wacht totdat de training is voltooid en selecteer **testen**.

- **Invoer**: Stel een alarm in.
- **Output**: gereed om alarm in te stellen met een Toon klok. Voor welke tijd?
- **Invoer**: Stel een alarm in met de Toon als jingle voor 9 am morgen.
- **Uitvoer**: OK, alarm ingesteld voor 2020-05-21 09:00:00. Het alarm signaal is jingle.
- **Invoer**: Nee, 8 uur.
- **Output**: de vorige alarm tijd wordt bijgewerkt naar 2020-05-29 08:00.

> [!NOTE]
> In een echte toepassing moet u in de sectie **acties** van deze correctie regel ook een activiteit naar de client terugsturen of een http-eind punt aanroepen om de alarm tijd in uw systeem bij te werken. Deze actie moet alleen verantwoordelijk zijn voor het bijwerken van de alarm tijd en niet van een ander kenmerk van de opdracht. In dit geval is dat de alarm Toon.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: sjablonen voor het genereren van een taal toevoegen voor spraak reacties](./how-to-custom-commands-add-language-generation-templates.md)
