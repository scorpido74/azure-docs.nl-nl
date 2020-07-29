---
title: 'Instructies: een toepassing maken met eenvoudige opdrachten-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u een toepassing met gehoste aangepaste opdrachten maakt en test met behulp van eenvoudige opdrachten.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d166257dd28773d89a4f1fd56de3cb1a22242523
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284163"
---
# <a name="create-application-with-simple-commands"></a>Toepassing met eenvoudige opdrachten maken

In dit artikel leert u het volgende:
 - Een lege toepassing maken
 - LUIS-resources bijwerken
 - Enkele basis opdrachten toevoegen aan de toepassing aangepaste opdrachten

## <a name="create-empty-application"></a>Lege toepassing maken
Een lege toepassing voor aangepaste opdrachten maken. Raadpleeg de [Snelstartgids](quickstart-custom-commands-application.md)voor meer informatie. Deze keer, in plaats van een project te importeren, maakt u een leeg project.

1. Voer in het vak **naam** de project naam in `Smart-Room-Lite` (of iets anders van uw keuze).
1. Selecteer in de lijst **taal** de optie **Engels (Verenigde Staten)**.
1. Selecteer of maak een LUIS-resource van uw keuze.

   > [!div class="mx-imgBorder"]
   > ![Een project maken](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>LUIS-resources bijwerken (optioneel)

U kunt de ontwerp bron die u hebt geselecteerd in het venster **Nieuw project** bijwerken en een Voorspellings bron instellen. De Voorspellings resource wordt gebruikt voor herkenning wanneer uw toepassing aangepaste opdrachten wordt gepubliceerd. U hebt geen Voorspellings resource nodig tijdens de ontwikkelings-en test fasen.

## <a name="add-turnon-command"></a>Opdracht TurnOn toevoegen

In de lege aangepaste opdrachten voor het maken van **Smart room-Lite-** toepassingen die u zojuist hebt gemaakt, voegt u een eenvoudige opdracht toe waarmee een utterance wordt verwerkt, `turn on the tv` en reageert u met het bericht `Ok, turning the tv on` .

1. Maak een nieuwe opdracht door **nieuwe opdracht** boven aan het linkerdeel venster te selecteren. Het **nieuwe opdracht** venster wordt geopend.
1. Geef een waarde voor het veld **naam** op als **TurnOn**.
1. Selecteer **Maken**.

In het middelste deel venster worden de verschillende eigenschappen van de opdracht weer gegeven. U configureert de volgende eigenschappen van de opdracht. Ga naar [verwijzingen](./custom-commands-references.md)voor uitleg van alle configuratie-eigenschappen van een opdracht.

| Configuration            | Beschrijving                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Voorbeeld zinnen** | Voor beeld van uitingen de gebruiker kan zeggen dat deze opdracht wordt geactiveerd                                                                 |
| **Parameters**       | Gegevens die nodig zijn om de opdracht te volt ooien                                                                                |
| **Voltooiings regels** | De acties die moeten worden uitgevoerd om te voldoen aan de opdracht. Bijvoorbeeld om te reageren op de gebruiker of te communiceren met een andere webservice. |
| **Interactie regels**   | Aanvullende regels voor het afhandelen van specifieke of complexe situaties                                                              |


> [!div class="mx-imgBorder"]
> ![Een opdracht maken](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>Voorbeeld zinnen toevoegen

Laten we beginnen met de sectie **voor beelden van zinnen** en een voor beeld geven van wat de gebruiker kan zeggen.

1. Selecteer de sectie **voor beelden van zinnen** in het middelste deel venster.
1. Voeg in het rechterdeel venster voor beelden toe:

    ```
    turn on the tv
    ```

1.  Selecteer boven aan het deel venster **Opslaan** .

We hebben nu geen para meters, dus we kunnen door gaan naar de sectie **voltooiings regels** .

### <a name="add-a-completion-rule"></a>Een voltooiings regel toevoegen

Vervolgens moet de opdracht een voltooiings regel hebben. Deze regel geeft aan de gebruiker door dat er een uitvoerings actie wordt ondernomen. Ga naar [verwijzingen](./custom-commands-references.md)voor meer informatie over regels en voltooiings regels.

1. Selecteer de standaard voltooiings regel die u **hebt** geselecteerd en bewerk deze als volgt:

    
    | Instelling    | Voorgestelde waarde                          | Beschrijving                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Name**       | ConfirmationResponse                  | Een naam die het doel van de regel beschrijft          |
    | **Voorwaarden** | Geen                                     | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |
    | **Acties**    | Spraak antwoord verzenden > eenvoudige editor > eerste variatie >`Ok, turning the tv on` | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |
    



   > [!div class="mx-imgBorder"]
   > ![Een spraak antwoord maken](media/custom-commands/create-speech-response-action.png)

1. Selecteer **Opslaan** om de actie op te slaan.
1. Selecteer in de sectie **voltooiings regels** de optie **Opslaan** om alle wijzigingen op te slaan. 


    > [!NOTE]
    > Het is niet nodig om de standaard voltooiings regel te gebruiken die bij de opdracht wordt geleverd. Indien nodig kunt u de bestaande standaard voltooiings regel verwijderen en uw eigen regel toevoegen.

### <a name="try-it-out"></a>Probeer het eens

Het gedrag testen met het deel venster testen van chat
1. Selecteer een **trein** pictogram boven in het rechterdeel venster.
1. Als de training eenmaal is voltooid, selecteert u **testen**. Probeer het volgende utterance via Voice/Text:
    - Invoer: de TV inschakelen
    - Uitvoer: OK, TV in-of uitschakelen


> [!div class="mx-imgBorder"]
> ![Testen met Web Chat](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> In het deel venster testen kunt u Details voor informatie **inschakelen** selecteren om te zien hoe deze spraak/tekst invoer is verwerkt.  

## <a name="add-settemperature-command"></a>Opdracht SetTemperature toevoegen

Voeg nu nog één opdracht **SetTemperature** toe die één utterance gaat maken, `set the temperature to 40 degrees` en reageer met het bericht `Ok, setting temperature to 40 degrees` .

Volg de stappen in de **TurnOn** -opdracht om een nieuwe opdracht te maken met behulp van de voorbeeld zin '**de Tempe ratuur instellen op 40 graden**'.

Bewerk vervolgens **de bestaande regels** voor het volt ooien van voltooiing als volgt:

| Instelling    | Voorgestelde waarde                          |
| ---------- | ---------------------------------------- |
| Naam  | ConfirmationResponse                  |
| Voorwaarden | Geen                                     |
| Acties    | Spraak antwoord verzenden > eenvoudige editor > eerste variatie >`Ok, setting temperature to 40 degrees` |

Selecteer **Opslaan** om alle wijzigingen in de opdracht op te slaan.

## <a name="add-setalarm-command"></a>Opdracht SetAlarm toevoegen
Maak een nieuwe opdracht **SetAlarm** met behulp van de voorbeeld zin '**een wekker instellen voor 9 am morgen**'. Bewerk vervolgens **de bestaande regels** voor het volt ooien van voltooiing als volgt:

| Instelling    | Voorgestelde waarde                          |
| ---------- | ---------------------------------------- |
| Regelnaam  | ConfirmationResponse                  |
| Voorwaarden | Geen                                     |
| Acties    | Spraak antwoord verzenden > eenvoudige editor > eerste variatie >`Ok, setting an alarm for 9 am tomorrow` |

Selecteer **Opslaan** om alle wijzigingen in de opdracht op te slaan.

## <a name="try-it-out"></a>Probeer het eens

Het gedrag testen met het deel venster testen van chat
1. Selecteer **Trainen**. Nadat de training is voltooid, selecteert u **testen** en try-out:
    - U typt: Stel de Tempe ratuur in op 40 graden
    - Verwachte reactie: OK, Tempe ratuur instellen op 40 graden
    - U typt: de TV inschakelen
    - Verwachte reactie: OK, de TV inschakelen
    - U typt: een alarm instellen voor 9 am morgen
    - Verwachte reactie: OK, instellen van een alarm voor 9 am morgen

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: para meters toevoegen aan opdrachten](./how-to-custom-commands-add-parameters-to-commands.md)
