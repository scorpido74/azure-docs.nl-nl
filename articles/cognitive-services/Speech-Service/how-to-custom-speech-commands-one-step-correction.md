---
title: 'How to: Een correctie in één stap toevoegen aan een aangepaste opdracht (Voorbeeld) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: In dit artikel leggen we uit hoe u correcties in één stap implementeert voor een opdracht in Aangepaste opdrachten.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456452"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>How To: een correctie in één stap toevoegen aan een aangepaste opdracht (voorbeeld)

In dit artikel leert u hoe u een bevestiging in één stap toevoegt aan een opdracht.

Correctie in één stap wordt gebruikt om een opdracht bij te werken die net is voltooid.

D.w.z. als u enkel een alarm instelt, u uw mening veranderen en de tijd van het alarm bijwerken.

- Invoer: Zet de wekker voor morgen om 12.00 uur
- Uitgang: "Ok, alarm ingesteld voor 12/06/2019 12:00:00"
- Input: Nee, morgen om 13.00 uur
- Uitvoer: "Ok

Houd er rekening mee dat dit impliceert dat u als ontwikkelaar een mechanisme hebt om het alarm in uw backend-toepassing bij te werken.

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:

- [Snelstart: een aangepaste opdracht maken (voorbeeld)](./quickstart-custom-speech-commands-create-new.md)
- [Snelstart: een aangepaste opdracht maken met parameters (voorbeeld)](./quickstart-custom-speech-commands-create-parameters.md)
- [How To: een bevestiging toevoegen aan een aangepaste opdracht (voorbeeld)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>De geavanceerde regels voor correctie in één stap toevoegen 

Als u correctie in één stap wilt aantonen, breiden we de opdracht **SetAlarm** die is gemaakt in de [bevestigingsinstructies uit.](./how-to-custom-speech-commands-confirmations.md)
 
1. Voeg een geavanceerde regel toe om het vorige alarm bij te werken. 

    Deze regel vraagt de gebruiker om de datum en het tijdstip van het alarm te bevestigen en verwacht een bevestiging (ja/nee) voor de volgende beurt.

   | Instelling               | Voorgestelde waarde                                                  | Beschrijving                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Vorig alarm bijwerken                                            | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | UpdateLastCommand & Vereiste parameter - DateTime                | Voorwaarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Acties               | SpeechResponse - "- Vorige alarm bijwerken naar {DateTime}"       | De actie die moet worden ondernomen wanneer de regelvoorwaarde waar is |
   | Staat na uitvoering | Voltooien, opdracht                                                 | Staat van de gebruiker na de turn                   |

1. Verplaats de regel die u zojuist hebt gemaakt naar de bovenkant van geavanceerde regels (schuif over de regel in het deelvenster en klik op de pijl-omhoog).
   > [!div class="mx-imgBorder"]
   > ![Een bereikvalidatie toevoegen](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> In een echte toepassing stuurt u in het gedeelte Acties van deze regel ook een activiteit terug naar de client of belt u een HTTP-eindpunt om het alarm in uw systeem bij te werken.

## <a name="try-it-out"></a>Uitproberen

Selecteer het deelvenster Testen en probeer een paar interacties.

- Invoer: Zet de wekker voor morgen om 12.00 uur
- Output: "Weet u zeker dat u een alarm wilt instellen voor 12/07/2019 12:00:00?"
- Invoer: Ja
- Uitgang: "Ok, alarm ingesteld voor 12/07/2019 12:00:00"
- Input: Nee, morgen om 13.00 uur
- Uitvoer: "Update van vorig alarm naar 12/07/2019 13:00:00"
