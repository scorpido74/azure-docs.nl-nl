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
ms.openlocfilehash: 081327e7c70128dad764d66942f1703a889f77fd
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951211"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Snelstartgids: een aangepaste opdracht maken (preview)

In dit artikel leert u hoe u een toepassing met gehoste aangepaste opdrachten kunt maken en testen.
De toepassing herkent een utterance zoals ' de TV inschakelen ' en reageert met een eenvoudig bericht ' OK, de TV inschakelen '.

## <a name="prerequisites"></a>Vereisten

- Een spraak abonnement. [Probeer de speech-service gratis](~/articles/cognitive-services/speech-service/get-started.md)uit.

  > [!NOTE]
  > Tijdens de preview-periode wordt alleen de regio westus2 ondersteund voor abonnements sleutels.

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
   > ![een project maken](media/custom-speech-commands/create-new-project.png)

1. Voer de naam en taal van het project in.
1. Selecteer een ontwerp bron. Als er geen geldige ontwerp resources zijn, maakt u er een door **nieuwe resource maken**te selecteren.

   > [!div class="mx-imgBorder"]
   > ![Een resource maken](media/custom-speech-commands/create-new-resource.png)

   1. Voer de resource naam, de groep, de locatie en de prijs categorie in.

         > [!NOTE]
         > U kunt resource groepen maken door de naam van de gewenste resource groep in te voeren in het veld Resource groep. De resource groep wordt gemaakt wanneer **maken** is geselecteerd.

1. Klik op **maken** om het project te maken.
1. Selecteer uw project zodra het is gemaakt.

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

1. Een nieuwe voltooiings regel maken door het `+` pictogram naast voltooiings regels te selecteren
1. Voer de naam van de regel in
1. Een actie toevoegen
   1. Maak een nieuwe actie voor spraak reacties door het `+` pictogram naast acties te selecteren en `SpeechResponse` te selecteren
   1. Voer het antwoord in

   > [!NOTE]
   > Normale tekst moet beginnen met een streepje. Meer informatie vindt u [hier](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![een spraak antwoord maken](media/custom-speech-commands/create-speech-response-action.png)

1. Klik op **Opslaan** om de regel op te slaan

> [!div class="mx-imgBorder"]
> ![een voltooiings regel maken](media/custom-speech-commands/create-basic-completion-response-rule.png)


| Instelling    | Voorgestelde waarde                        | Beschrijving                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| Regelnaam  | "ConfirmationResponse"                 | Een naam die het doel van de regel beschrijft          |
| Voorwaarden | Geen                                   | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |
| Acties    | SpeechResponse "-OK, inschakelen van de TV" | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |

## <a name="try-it-out"></a>Probeer het

Test het gedrag met behulp van het deel venster testen.

> [!div class="mx-imgBorder"]
> ![testen met Web Chat](media/custom-speech-commands/create-basic-test-chat.png)

- U typt: ' de TV inschakelen '
- Verwachte reactie: "OK, inschakelen van de TV"

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Snelstartgids: een aangepaste opdracht maken met para meters (preview)](./quickstart-custom-speech-commands-create-parameters.md)
