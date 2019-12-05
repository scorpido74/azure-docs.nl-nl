---
title: 'Snelstartgids: een aangepaste opdracht maken (preview)-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel kunt u een toepassing met gehoste aangepaste opdrachten maken en testen.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 5c90c91791af8a9a16039e9650765bd24433ff38
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815828"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Snelstartgids: een aangepaste opdracht maken (preview)

In dit artikel leert u hoe u een toepassing met gehoste aangepaste opdrachten kunt maken en testen.
De toepassing herkent een utterance zoals ' de TV inschakelen ' en reageert met een eenvoudig bericht ' OK, de TV inschakelen '.

## <a name="prerequisites"></a>Vereisten

- Een spraak abonnement. [Probeer de speech-service gratis](~/articles/cognitive-services/speech-service/get-started.md)uit.

  > [!NOTE]
  > Tijdens de preview-periode wordt alleen de regio westus2 ondersteund voor abonnements sleutels.

- Een [Language Understanding](https://www.luis.ai/home) -ontwerp sleutel (Luis):
  1. Open een webbrowser en navigeer naar de [Azure Portal](https://portal.azure.com)
  1. Selecteer een resource maken
  1. [Language Understanding](https://aka.ms/sc-luis-all) zoeken en selecteren
  1. Selecteer ontwerpen in de opties voor maken
  1. Nadat de resource is geïmplementeerd, gaat u naar de resource en kopieert u de sleutel uit de sectie Quick Start of keys

      > [!div class="mx-imgBorder"]
      > ](media/custom-speech-commands/resources-lu-authoring.png) ![maken

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ga naar de speech Studio voor aangepaste opdrachten

1. Open uw webbrowser en ga naar de [Speech Studio](https://speech.microsoft.com/)
1. Voer uw referenties in om u aan te melden bij de portal

   - De standaard weergave is uw lijst met spraak abonnementen
     > [!NOTE]
     > Als u de pagina abonnement selecteren niet ziet, kunt u daar navigeren door te klikken op spraak bronnen in het menu instellingen op de bovenste balk.

1. Selecteer uw spraak abonnement en selecteer vervolgens **Go to Studio**
1. **Aangepaste opdrachten selecteren (preview-versie)**

De standaard weergave is een lijst met de aangepaste opdrachten voor toepassingen die u hebt gemaakt.

## <a name="create-a-custom-commands-project"></a>Een project met aangepaste opdrachten maken

1. Selecteer **Nieuw project** om een nieuw project te maken

   > [!div class="mx-imgBorder"]
   > ![nieuw project maken](media/custom-speech-commands/create-new-project.png)

1. Voer de naam en taal van het project in, selecteer **volgende** om door te gaan
1. Voer uw LUIS-ontwerp sleutel in
1. Selecteer uw project zodra deze is gemaakt

Uw weer gave moet nu een overzicht zijn van de toepassing aangepaste opdrachten.

## <a name="create-a-new-command"></a>Een nieuwe opdracht maken

U kunt nu een opdracht maken. We gaan een voor beeld gebruiken waarbij één utterance wordt gebruikt, `turn on the tv`en reageert met het bericht `Ok, turning on the TV`.

1. Maak een nieuwe opdracht door het `+` pictogram naast opdrachten te selecteren en geef het de naam `TurnOn`
1. Selecteer **Opslaan**

> [!div class="mx-imgBorder"]
> ![een opdracht maken](media/custom-speech-commands/create-add-command.png)

Een opdracht is een set:

| Groep            | Beschrijving                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Voorbeeldzinnen | Voor beeld van uitingen de gebruiker kan zeggen dat deze opdracht wordt geactiveerd                                                                 |
| Parameters       | Gegevens die nodig zijn om de opdracht te volt ooien                                                                                |
| Voltooiings regels | De acties die moeten worden uitgevoerd om te voldoen aan de opdracht. Als u bijvoorbeeld op de gebruiker wilt reageren of met een andere webservice wilt communiceren |
| Geavanceerde regels   | Aanvullende regels voor het afhandelen van specifieke of complexe situaties                                                              |

### <a name="add-a-sample-sentence"></a>Een voor beeld van een zin toevoegen

Laten we beginnen met voorbeeld zinnen en een voor beeld geven van wat de gebruiker kan zeggen:

```
turn on the tv
```

We hebben nu geen para meters, dus we kunnen door gaan met de voltooiings regels.

### <a name="add-a-completion-rule"></a>Een voltooiings regel toevoegen

Voeg nu een voltooiings regel toe om te reageren op de gebruiker om aan te geven dat een actie wordt ondernomen.

> [!div class="mx-imgBorder"]
> ![een voltooiings regel maken](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Instelling    | Voorgestelde waarde                        | Beschrijving                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| Regelnaam  | "ConfirmationResponse"                 | Een naam die het doel van de regel beschrijft          |
| Voorwaarden | Geen                                   | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |
| Acties    | SpeechResponse "OK, inschakelen van de TV" | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |

## <a name="try-it-out"></a>Probeer het

Test het gedrag met behulp van het deel venster testen.

> [!div class="mx-imgBorder"]
> ![testen met webchat](media/custom-speech-commands/create-basic-test-chat.png)

- U typt: ' de TV inschakelen '
- Verwachte reactie: "OK, inschakelen van de TV"

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Snelstartgids: een aangepaste opdracht maken met para meters (preview)](./quickstart-custom-speech-commands-create-parameters.md)
