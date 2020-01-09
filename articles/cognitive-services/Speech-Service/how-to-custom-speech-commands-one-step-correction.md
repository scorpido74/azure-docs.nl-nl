---
title: 'Procedure: een correctie met één stap toevoegen aan een aangepaste opdracht (preview)-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u correcties in één stap voor een opdracht in aangepaste opdrachten implementeert.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456452"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Procedure: een correctie met één stap toevoegen aan een aangepaste opdracht (preview-versie)

In dit artikel leert u hoe u een bevestiging van één stap kunt toevoegen aan een opdracht.

Correctie van één stap wordt gebruikt om een opdracht bij te werken die zojuist is voltooid.

Als u zojuist een alarm hebt ingesteld, kunt u dit echter wijzigen en de tijd van de wekker bijwerken.

- Invoer: alarm instellen voor morgen om 12:00 uur
- Uitvoer: "OK, alarm ingesteld voor 12/06/2019 12:00:00"
- Invoer: Nee, morgen op 1pm
- Uitvoer: OK

Denk eraan dat u als een ontwikkelaar een mechanisme hebt voor het bijwerken van de waarschuwing in uw back-end-toepassing.

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:

- [Snelstartgids: een aangepaste opdracht maken (preview)](./quickstart-custom-speech-commands-create-new.md)
- [Snelstartgids: een aangepaste opdracht maken met para meters (preview)](./quickstart-custom-speech-commands-create-parameters.md)
- [Procedure: een bevestiging toevoegen aan een aangepaste opdracht (preview-versie)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>De geavanceerde regels voor correctie met één stap toevoegen 

Als u een correctie met één stap wilt demonstreren, gaat u naar de **SetAlarm** -opdracht die u hebt gemaakt in de [bevestigingen](./how-to-custom-speech-commands-confirmations.md).
 
1. Een geavanceerde regel toevoegen om de vorige waarschuwing bij te werken. 

    Met deze regel wordt de gebruiker gevraagd om de datum en tijd van het alarm te bevestigen en wordt een bevestiging (Ja/Nee) voor de volgende beurt verwacht.

   | Instelling               | Voorgestelde waarde                                                  | Beschrijving                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Vorige waarschuwing bijwerken                                            | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | UpdateLastCommand & vereiste para meter-DateTime                | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Acties               | SpeechResponse--vorige waarschuwing bijwerken naar {DateTime}       | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |
   | Status na uitvoering | Opdracht volt ooien                                                 | Status van de gebruiker na de turn                   |

1. Verplaats de regel die u zojuist hebt gemaakt boven aan geavanceerde regels (Blader over de regel in het deel venster en klik op de pijl-omhoog).
   > [!div class="mx-imgBorder"]
   > ![een bereik validatie toevoegen](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> In een echte toepassing stuurt u in de sectie acties van deze regel ook een activiteit terug naar de client of roept u een HTTP-eind punt aan om de waarschuwing in uw systeem bij te werken.

## <a name="try-it-out"></a>Probeer het

Selecteer het deel venster testen en probeer enkele interacties.

- Invoer: alarm instellen voor morgen om 12:00 uur
- Uitvoer: ' weet u zeker dat u een alarm wilt instellen voor 12/07/2019 12:00:00? '
- Invoer: Ja
- Uitvoer: "OK, alarm ingesteld voor 12/07/2019 12:00:00"
- Invoer: Nee, morgen op 1pm
- Uitvoer: ' vorige waarschuwing bijwerken naar 12/07/2019 13:00:00 '
