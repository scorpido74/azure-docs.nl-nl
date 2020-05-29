---
title: 'Snelstartgids: een voor beeld van een aangepaste opdracht maken app-Speech Service'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt en test u een preview-toepassing met gehoste aangepaste opdrachten. Uitingen wordt verwerkt door de toepassing.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 80111ff370f3a5412b45adc04c82c9dee103c01d
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142347"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>Snelstartgids: een preview-app voor aangepaste opdrachten maken

In deze Quick Start leert u hoe u een toepassing met aangepaste opdrachten maakt en test.
De toepassing verwerkt uitingen zoals ' de TV inschakelen ' en antwoord met een eenvoudig bericht als ' OK, waarbij de TV wordt ingeschakeld. '

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een Azure-spraak resource maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > Op dit moment ondersteunt aangepaste opdrachten alleen spraak abonnementen in de regio's westus, westus2 en neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ga naar de speech Studio voor aangepaste opdrachten

1. Ga in een webbrowser naar [Speech Studio](https://speech.microsoft.com/).
1. Voer uw referenties in om u aan te melden bij de portal.

   De standaard weergave is uw lijst met spraak abonnementen.
    > [!NOTE]
    > Als u de pagina abonnement selecteren niet ziet, kunt u hier klikken op **spraak bronnen** in het menu instellingen boven aan het scherm.

1. Selecteer uw spraak abonnement en selecteer vervolgens **Go to Studio**.
1. Selecteer **aangepaste opdrachten**.

     De standaard weergave is een lijst met de aangepaste opdrachten voor toepassingen die u in het geselecteerde abonnement hebt.

## <a name="create-a-custom-commands-project"></a>Een project met aangepaste opdrachten maken

1. Selecteer **Nieuw project** om een project te maken.

   > [!div class="mx-imgBorder"]
   > ![Een project maken](media/custom-speech-commands/create-new-project.png)

1. Voer in het vak **naam** een project naam in.
1. Selecteer een taal in de lijst **taal** .
1. Selecteer in de lijst **Luis authoring resource** een authoring resource. Als er geen geldige ontwerp resources zijn, maakt u er een door **nieuwe Luis maken**te selecteren.

   > [!div class="mx-imgBorder"]
   > ![Een resource maken](media/custom-speech-commands/create-new-resource.png)

   1. Voer in het vak **resource naam** de naam van de resource in.
   1. Selecteer een resource groep in de lijst **resource groep** .
   1. Selecteer een locatie in de lijst **locatie** .
   1. Selecteer een laag in de lijst **prijs categorie** .

      > [!NOTE]
      > U kunt een resource groep maken door in het vak **resource** groep de naam van een resource groep in te voeren. De resource groep wordt gemaakt wanneer u **maken**selecteert.

1. Selecteer **Maken**.
1. Nadat het project is gemaakt, selecteert u het.

    Nu ziet u een overzicht van de nieuwe toepassing voor aangepaste opdrachten.

## <a name="update-luis-resources-optional"></a>LUIS-resources bijwerken (optioneel)

U kunt de ontwerp bron die u hebt geselecteerd in het venster **Nieuw project** bijwerken en een Voorspellings bron instellen. De Voorspellings resource wordt gebruikt voor herkenning wanneer uw toepassing aangepaste opdrachten wordt gepubliceerd. U hebt geen Voorspellings resource nodig tijdens de ontwikkelings-en test fasen.

1. Selecteer **instellingen** in het linkerdeel venster en selecteer vervolgens **Luis resources** in het middelste deel venster.
1. Selecteer een Voorspellings bron of maak een resource door **nieuwe resource maken**te selecteren.
1. Selecteer **Opslaan**.
    
    > [!div class="mx-imgBorder"]
    > ![LUIS-resources instellen](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Omdat de bewerkings resource slechts 1.000 Voorspellings eindpunt aanvragen per maand ondersteunt, moet u een LUIS-Voorspellings bron instellen voordat u de toepassing voor aangepaste opdrachten publiceert.


## <a name="create-a-command"></a>Een opdracht maken

We gaan een eenvoudige opdracht maken waarmee één utterance wordt gemaakt `turn on the tv` en met het bericht kan worden gereageerd `Ok, turning on the tv` .

1. Maak een opdracht door **nieuwe opdracht** boven aan het linkerdeel venster te selecteren. Het **nieuwe opdracht** venster wordt geopend.
1. Typ **TurnOn**in het vak **naam** .
1. Selecteer **Maken**.

In het middelste deel venster worden de eigenschappen van de opdracht weer gegeven:


| Configuratie            | Beschrijving                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Voorbeeld zinnen** | Voor beelden van uitingen de gebruiker kan zeggen dat de opdracht wordt geactiveerd.                                                                 |
| **Parameters**       | Informatie die is vereist om de opdracht te volt ooien.                                                                                |
| **Voltooiings regels** | Acties die worden uitgevoerd om aan de opdracht te voldoen. Bijvoorbeeld om te reageren op de gebruiker of om te communiceren met een andere webservice. |
| **Interactie regels**   | Aanvullende regels voor het afhandelen van specifieke of complexe situaties.                                                              |


> [!div class="mx-imgBorder"]
> ![Een opdracht maken](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Voorbeeld zinnen toevoegen

Laten we beginnen met **voorbeeld zinnen** sectie. We bieden een voor beeld van wat de gebruiker kan zeggen.

1. Selecteer **voorbeeld zinnen** in het middelste deel venster. 
1. Voeg in het rechterdeel venster voor beelden toe:

    ```
    turn on the tv
    ```

1. Selecteer boven aan het deel venster **Opslaan** .

We hebben nu geen para meters, dus we kunnen door gaan naar de sectie **voltooiings regels** .

### <a name="add-a-completion-rule"></a>Een voltooiings regel toevoegen

Voeg nu een voltooiings regel toe met de volgende configuratie. Deze regel geeft aan de gebruiker door dat er een uitvoerings actie wordt ondernomen.


| Instelling    | Voorgestelde waarde                          | Beschrijving                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **Naam**  | **ConfirmationResponse**                  | Een naam die het doel van de regel beschrijft.          |
| **Voorwaarden** | Geen                                     | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd.    |
| **Acties**    | **Spraak antwoord verzenden-> OK, de TV inschakelen** | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is. |

1. Maak een nieuwe voltooiings regel door boven aan het middelste deel venster op **toevoegen** te klikken.
1. Voer in het vak **Naam** een naam in.
1. Een actie toevoegen.
   1. Maak een actie door **een actie toevoegen** te selecteren in de sectie **acties** .
   1. Selecteer in het venster **actie bewerken** in de lijst **type** de optie **spraak antwoord verzenden**.
   1. Onder **antwoord**selecteert u **eenvoudige editor**. Voer in het vak **eerste variant** **op OK in om de TV in te scha kelen**.

   > [!div class="mx-imgBorder"]
   > ![Een antwoord maken](media/custom-speech-commands/create-speech-response-action.png)

1. Selecteer **Opslaan** om de regel op te slaan.
1. Selecteer in de sectie **voltooiings regels** de optie **Opslaan** om alle wijzigingen op te slaan. 

> [!div class="mx-imgBorder"]
> ![Een voltooiings regel maken](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Beleid uitproberen

Test het gedrag met behulp van het deel venster testen.
1. Selecteer **trein** bovenaan in het rechterdeel venster.
1. Nadat de training is voltooid, selecteert u **testen**. Er wordt een nieuw venster voor **de toepassing testen** weer gegeven.
    - **De TV inschakelen**
    - Verwachte reactie: **OK, inschakelen van de TV**


> [!div class="mx-imgBorder"]
> ![Het gedrag testen](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Quick Start: een preview-toepassing met para meters voor aangepaste opdrachten maken](./quickstart-custom-speech-commands-create-parameters.md)
