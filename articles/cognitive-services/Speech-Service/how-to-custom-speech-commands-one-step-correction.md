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
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858270"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Procedure: een correctie met één stap toevoegen aan een aangepaste opdracht (preview-versie)

In dit artikel leert u hoe u een bevestiging van één stap kunt toevoegen aan een opdracht.

Correctie van één stap wordt gebruikt om een opdracht bij te werken die zojuist is voltooid. Dat wil zeggen, als u zojuist een alarm hebt ingesteld, kunt u van gedachten veranderen en de tijd van de wekker bijwerken. Een voor beeld van een dergelijk geval is als volgt:

- Invoer: alarm instellen voor morgen om 12:00 uur
- Uitvoer: OK, alarm ingesteld voor 2020-05-02 12:00:00
- Invoer: Nee, morgen op 1pm
- Uitvoer: OK

Een echt wereld wijd scenario, dat doorgaans vergezeld gaat van een actie als gevolg van de voltooiing van de opdracht. in dit artikel wordt ervan uitgegaan dat u als ontwikkelaar een mechanisme hebt om het alarm in uw back-endtoepassing bij te werken.

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:
> [!div class="checklist"]

> * [Snelstartgids: een aangepaste opdracht maken (preview)](./quickstart-custom-speech-commands-create-new.md)
> * [Snelstartgids: een aangepaste opdracht maken met para meters (preview)](./quickstart-custom-speech-commands-create-parameters.md)
> * [Procedure: een bevestiging toevoegen aan een aangepaste opdracht (preview-versie)](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>Interactie regels toevoegen voor correctie met één stap 

Als u een correctie met één stap wilt demonstreren, gaat u naar de **SetAlarm** -opdracht die u hebt gemaakt in [How to: een bevestiging toevoegen aan een aangepaste opdracht (preview)](./how-to-custom-speech-commands-confirmations.md).
1. Een interactie regel toevoegen om het eerder ingestelde alarm niveau bij te werken. 

    Met deze regel wordt de gebruiker gevraagd om de datum en tijd van het alarm te bevestigen en wordt een bevestiging (Ja/Nee) voor de volgende beurt verwacht.

   | Instelling               | Voorgestelde waarde                                                  | Beschrijving                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Vorige waarschuwing bijwerken                                            | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | De vorige opdracht moet worden bijgewerkt & vereiste para meter-> DateTime                | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Acties               | Spraak antwoord verzenden-> eenvoudige editor: > vorige waarschuwing bijwerken naar {DateTime}      | De actie die moet worden uitgevoerd wanneer de voor waarden van de regel waar zijn |
   | Status na uitvoering | Opdracht voltooid        | Status van de gebruiker na de turn                   |

1. Verplaats de regel die u zojuist hebt gemaakt boven aan de interactie regels (Selecteer de regel in het deel venster en klik op de pijl `...` omhoog onder in het middelste deel venster).
   > [!div class="mx-imgBorder"]
   > ![Validatie van een bereik toevoegen](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > In een echte toepassing stuurt u in de sectie acties van deze regel ook een activiteit terug naar de client of roept u een HTTP-eind punt aan om de waarschuwing in uw systeem bij te werken.

## <a name="try-it-out"></a>Beleid uitproberen

Selecteer `Train`, wacht tot training is voltooid en selecteer `Test`.

- Invoer: alarm instellen voor morgen om 12:00 uur
- Uitvoer: weet u zeker dat u een alarm wilt instellen voor 2020-05-02 12:00:00
- Invoer: Ja
- Uitvoer: OK, alarm ingesteld voor 2020-05-02 12:00:00
- Invoer: Nee, morgen op 2pm
- Uitvoer: vorige waarschuwing bijwerken naar 2020-05-02 14:00:00
