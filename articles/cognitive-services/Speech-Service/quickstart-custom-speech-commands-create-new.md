---
title: 'Snelstartgids: een aangepaste opdracht maken (preview)-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel kunt u een toepassing met gehoste aangepaste opdrachten maken en testen.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872523"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>Snelstartgids: een aangepaste opdrachten-app maken (preview)

In deze Quick Start leert u hoe u een toepassing met aangepaste opdrachten maakt en test.
De gemaakte toepassing verwerkt uitingen zoals "de TV inschakelen" en antwoord met een eenvoudig bericht "OK, inschakelen van de TV".

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een Azure-spraak resource maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > Op dit moment ondersteunt aangepaste opdrachten alleen spraak abonnementen in de regio's westus, westus2 en neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ga naar de speech Studio voor aangepaste opdrachten

1. Open uw webbrowser en ga naar de [Speech Studio](https://speech.microsoft.com/)
1. Voer uw referenties in om u aan te melden bij de portal.

   - De standaard weergave is uw lijst met spraak abonnementen.
     > [!NOTE]
     > Als u de pagina abonnement selecteren niet ziet, kunt u daar navigeren door te klikken op spraak bronnen in het menu instellingen op de bovenste balk.

1. Selecteer uw spraak abonnement en selecteer vervolgens **Go to Studio**.
1. Selecteer **aangepaste opdrachten**.

     - De standaard weergave is een lijst met de aangepaste opdrachten voor toepassingen die u onder het geselecteerde abonnement hebt.

## <a name="create-a-custom-commands-project"></a>Een project met aangepaste opdrachten maken

1. Selecteer **Nieuw project** om een nieuw project te maken.

   > [!div class="mx-imgBorder"]
   > ![Een project maken](media/custom-speech-commands/create-new-project.png)

1. Voer de project naam in.
1. Selecteer taal in de vervolg keuzelijst.
1. Selecteer een bron voor ontwerpen in de vervolg keuzelijst. Als er geen geldige ontwerp resources zijn, maakt u er een door te klikken op **nieuwe Luis maken**.

   > [!div class="mx-imgBorder"]
   > ![Een resource maken](media/custom-speech-commands/create-new-resource.png)

   - Voer de resource naam, de resource groep.
   - Kies waarde voor locatie en prijs categorie in de vervolg keuzelijst.

      > [!NOTE]
      > U kunt resource groepen maken door de naam van de gewenste resource groep in te voeren in het veld Resource groep. De resource groep wordt gemaakt wanneer **maken** is geselecteerd.

1. Selecteer vervolgens **maken** om het project te maken.
1. Selecteer uw project zodra het is gemaakt.

    - De weer gave is nu een overzicht van de nieuwe toepassing aangepaste opdrachten.

## <a name="update-luis-resources-optional"></a>LUIS-resources bijwerken (optioneel)

U kunt de ontwerp bron die is ingesteld in het venster Nieuw project, bijwerken en een Voorspellings bron instellen. De Voorspellings resource wordt gebruikt voor de herkenning zodra de toepassing voor aangepaste opdrachten is gepubliceerd. U hebt geen Voorspellings resource nodig voor de ontwikkelings-en test fasen.

1. Selecteer **instellingen** in het linkerdeel venster en navigeer vervolgens naar de sectie **Luis resources** in het middelste deel venster.
1. Selecteer een Voorspellings bron of maak een resource door **nieuwe resource maken**te selecteren.
1. Selecteer **Opslaan**.
    
    > [!div class="mx-imgBorder"]
    > ![LUIS-resources instellen](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Aangezien de bewerkings resource slechts 1.000 Voorspellings eindpunt aanvragen per maand ondersteunt, moet u een LUIS-Voorspellings bron instellen voordat u uw aangepaste opdrachten mandatorily publiceert.


## <a name="create-a-new-command"></a>Een nieuwe opdracht maken

We gaan een eenvoudige opdracht maken waarmee één utterance `turn on the tv`wordt gemaakt en met het bericht `Ok, turning on the tv`kan worden gereageerd.

1. Maak een nieuwe opdracht door het `+ New command` pictogram boven in het meest linkse deel venster te selecteren. Er wordt een nieuwe pop-up weer gegeven met de titel **nieuwe opdracht**.
1. Geef een waarde op **Name** voor het veld `TurnOn`naam.
1. Selecteer **Maken**.

In het middelste deel venster worden de verschillende eigenschappen van een opdracht Inge schreven:


| Configuratie            | Beschrijving                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Voorbeeld zinnen | Voor beeld van uitingen de gebruiker kan zeggen dat deze opdracht wordt geactiveerd                                                                 |
| Parameters       | Gegevens die nodig zijn om de opdracht te volt ooien                                                                                |
| Voltooiings regels | De acties die moeten worden uitgevoerd om te voldoen aan de opdracht. Bijvoorbeeld om te reageren op de gebruiker of te communiceren met een andere webservice. |
| Interactie regels   | Aanvullende regels voor het afhandelen van specifieke of complexe situaties                                                              |


> [!div class="mx-imgBorder"]
> ![Een opdracht maken](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Voorbeeld zinnen toevoegen

Laten we beginnen met de sectie voor beelden van zinnen en een voor beeld geven van wat de gebruiker kan zeggen.

1. Selecteer **voorbeeld zinnen** in het middelste deel venster en voeg in het rechter deel venster voor beelden toe:

    ```
    turn on the tv
    ```

1. Selecteer `Save` het pictogram dat boven in dit deel venster wordt weer gegeven.

We hebben nu geen para meters, dus we kunnen door gaan naar de sectie **voltooiings regels** .

### <a name="add-a-completion-rule"></a>Een voltooiings regel toevoegen

Voeg nu een voltooiings regel met de volgende configuratie toe. Deze regel geeft aan dat de gebruiker een uitvoerings actie onderneemt.


| Instelling    | Voorgestelde waarde                          | Beschrijving                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Regelnaam  | ConfirmationResponse                  | Een naam die het doel van de regel beschrijft          |
| Voorwaarden | Geen                                     | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |
| Acties    | SpeechResponse "-OK, inschakelen van de TV" | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |

1. Maak een nieuwe voltooiings regel door het `+Add` pictogram boven aan het middelste deel venster te selecteren.
1. Geef een waarde op in het gedeelte **naam** .
1. Een actie toevoegen
   1. Maak een nieuwe actie door in de sectie **acties** het selectie vakje **+ een actie toevoegen** te selecteren.
   1. Selecteer in het pop-upvenster **nieuwe actie** de optie `Send speech response` in de vervolg keuzelijst voor **type**.
   1. Kies `Simple editor` voor het veld **antwoord** .
       - In het **eerste variatie** veld geeft u een waarde op voor reactie als`Ok, turning on the tv`

   > [!div class="mx-imgBorder"]
   > ![Een spraak antwoord maken](media/custom-speech-commands/create-speech-response-action.png)

1. Klik op **Opslaan** om de regel op te slaan.
1. Selecteer in de sectie **voltooiings regels** de optie **Opslaan** om alle wijzigingen op te slaan. 

> [!div class="mx-imgBorder"]
> ![Een voltooiings regel maken](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Beleid uitproberen

Het gedrag testen met het deel venster testen van chat
1. Selecteer `Train` het pictogram dat boven in het rechterdeel venster wordt weer gegeven.
1. Zodra de training is voltooid, selecteert `Test`u. Er wordt een nieuw test venster voor **uw toepassing** weer gegeven.
    - U typt: de TV inschakelen
    - Verwachte reactie: OK, inschakelen van de TV


> [!div class="mx-imgBorder"]
> ![Testen met Web Chat](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Snelstartgids: een aangepaste opdracht maken met para meters (preview)](./quickstart-custom-speech-commands-create-parameters.md)
