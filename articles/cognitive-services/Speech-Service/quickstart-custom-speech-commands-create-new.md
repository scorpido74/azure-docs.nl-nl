---
title: 'Snelstart: een aangepaste opdracht (voorbeeld) maken - Spraakservice'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt en test u een gehoste toepassing voor aangepaste opdrachten.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76155584"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Snelstart: een aangepaste opdracht maken (voorbeeld)

In dit artikel leert u hoe u een gehoste toepassing voor aangepaste opdrachten maken en testen.
De applicatie herkent een uiting als "zet de tv aan" en reageert met een eenvoudig bericht "Ok, het inschakelen van de tv".

## <a name="prerequisites"></a>Vereisten

- Een spraakabonnement.

Als u geen spraakabonnement hebt, u er een maken door naar de [Spraakstudio](https://speech.microsoft.com/) te navigeren en **Een spraakbron maken te**selecteren.

  > [!div class="mx-imgBorder"]
  > [![Een project](media/custom-speech-commands/create-new-subscription.png) maken](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > Tijdens de preview wordt alleen de regio Westus2 ondersteund.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ga naar de spraakstudio voor aangepaste opdrachten

1. Uw webbrowser openen en naar de [spraakstudio](https://speech.microsoft.com/) navigeren
1. Voer uw referenties in om u aan te melden bij de portal

   - De standaardweergave is uw lijst met spraakabonnementen
     > [!NOTE]
     > Als u de pagina Selecteren abonnement niet ziet, u daar naartoe navigeren door 'Spraakbronnen' te kiezen in het instellingenmenu op de bovenste balk.

1. Selecteer uw spraakabonnement en selecteer **Ga naar Studio**
1. **Aangepaste opdrachten selecteren (voorbeeld)**

De standaardweergave is een lijst met de aangepaste opdrachtentoepassingen die u hebt gemaakt.

## <a name="create-a-custom-commands-project"></a>Een project Aangepaste opdrachten maken

1. Nieuw **project selecteren** om een nieuw project te maken

   > [!div class="mx-imgBorder"]
   > ![Een project maken](media/custom-speech-commands/create-new-project.png)

1. Voer de naam en taal van het project in.
1. Selecteer een ontwerpbron. Als er geen geldige bronnen voor het ontwerpen zijn, maakt u er een door **Nieuwe resource maken te**selecteren.

   > [!div class="mx-imgBorder"]
   > ![Een resource maken](media/custom-speech-commands/create-new-resource.png)

   1. Voer de resourcenaam, groep, locatie en prijslaag in.

         > [!NOTE]
         > U resourcegroepen maken door de gewenste naam van de resourcegroep in te voeren in het veld Resourcegroep. De resourcegroep wordt gemaakt wanneer **Maken** is geselecteerd.

1. Klik **op Maken** om uw project te maken.
1. Selecteer uw project nadat u bent gemaakt.

Uw weergave moet nu een overzicht zijn van uw toepassing Aangepaste opdrachten.

## <a name="update-luis-resources-optional"></a>LUIS-bronnen bijwerken (optioneel)

U de ontwerpbronset bijwerken in het nieuwe projectvenster en een voorspellingsbron instellen die wordt gebruikt om invoer tijdens runtime te herkennen.

> [!NOTE]
> U moet een voorspellingsbron instellen voordat uw toepassing voorspellingen opvraagt die verder gaan dan de 1.000 aanvragen die door de auteursbron worden verstrekt.

> [!div class="mx-imgBorder"]
> ![LUIS-bronnen instellen](media/custom-speech-commands/set-luis-resources.png)

1. Navigeer naar het deelvenster LUIS-bronnen door **Instellingen** in het linkerdeelvenster te selecteren en vervolgens **LUIS-bronnen** in het middelste deelvenster.
1. Selecteer een voorspellingsbron of maak er een door **Nieuwe resource maken te** selecteren
1. Selecteer **Opslaan**

## <a name="create-a-new-command"></a>Een nieuwe opdracht maken

Nu u een opdracht maken. Laten we een voorbeeld gebruiken dat één `turn on the tv`utterance neemt en `Ok, turning on the TV`met het bericht reageert.

1. Een nieuwe opdracht maken `+` door het pictogram naast opdrachten te selecteren en deze de naam te geven`TurnOn`
1. Selecteer **Opslaan**

> [!div class="mx-imgBorder"]
> ![Een opdracht maken](media/custom-speech-commands/create-add-command.png)

Een opdracht is een set van:

| Groep            | Beschrijving                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Voorbeeldzinnen | Voorbeelduitingen die de gebruiker kan zeggen om deze opdracht te activeren                                                                 |
| Parameters       | Informatie die nodig is om de opdracht te voltooien                                                                                |
| Voltooiingsregels | De acties die moeten worden ondernomen om het commando te vervullen. Bijvoorbeeld om te reageren op de gebruiker of te communiceren met een andere webservice |
| Geavanceerde regels   | Aanvullende regels voor het omgaan met meer specifieke of complexe situaties                                                              |

### <a name="add-a-sample-sentence"></a>Een voorbeeldzin toevoegen

Laten we beginnen met voorbeeldzinnen en een voorbeeld geven van wat de gebruiker kan zeggen:

```
turn on the tv
```

Voor nu hebben we geen parameters, dus we kunnen verder gaan met voltooiingsregels.

### <a name="add-a-completion-rule"></a>Een voltooiingsregel toevoegen

Voeg nu een voltooiingsregel toe om te reageren op de gebruiker die aangeeft dat er een actie wordt uitgevoerd.

1. Een nieuwe voltooiingsregel `+` maken door het pictogram naast voltooiingsregels te selecteren
1. Voer de regelnaam in
1. Een actie toevoegen
   1. Een nieuwe actie voor spraakrespons maken door het `+` pictogram naast Acties te selecteren en`SpeechResponse`
   1. Voer het antwoord in

   > [!NOTE]
   > Reguliere tekst moet beginnen met een streepje. Voor meer informatie, ga [hier](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![Een spraakreactie maken](media/custom-speech-commands/create-speech-response-action.png)

1. Klik **op Opslaan** om de regel op te slaan

> [!div class="mx-imgBorder"]
> ![Een voltooiingsregel maken](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Instelling    | Voorgestelde waarde                          | Beschrijving                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Regelnaam  | "Bevestigingreactie"                   | Een naam die het doel van de regel beschrijft          |
| Voorwaarden | Geen                                     | Voorwaarden die bepalen wanneer de regel kan worden uitgevoerd    |
| Acties    | SpeechResponse "- Ok, het inschakelen van de TV" | De actie die moet worden ondernomen wanneer de regelvoorwaarde waar is |

## <a name="try-it-out"></a>Uitproberen

Test het gedrag met het chatpaneel Testen.

> [!div class="mx-imgBorder"]
> ![Testen met webchat](media/custom-speech-commands/create-basic-test-chat.png)

- U typt: "zet de tv aan"
- Verwachte reactie: "Ok, het draaien van de tv"

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Snelstart: een aangepaste opdracht maken met parameters (voorbeeld)](./quickstart-custom-speech-commands-create-parameters.md)
