---
title: Een correctie met één stap toevoegen in het voor beeld van een aangepaste opdracht-Speech Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het toevoegen van een correctie voor één stap voor een opdracht in een preview-app voor aangepaste opdrachten.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310424"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>Een correctie met één stap toevoegen aan een aangepaste opdracht in een voor beeld van een toepassing met aangepaste opdrachten

In dit artikel leert u hoe u een bevestiging van één stap kunt toevoegen aan een opdracht in een preview-app voor aangepaste opdrachten.

Correctie van één stap wordt gebruikt om een opdracht bij te werken die zojuist is voltooid. Wanneer u een correctie met één stap aan een alarm opdracht toevoegt, kunt u uw gedachten wijzigen en de tijd van de wekker bijwerken. Bijvoorbeeld:

- Invoer: alarm instellen voor morgen om 12:00 uur
- Uitvoer: OK, alarm ingesteld voor 2020-05-02 12:00:00
- Invoer: Nee, morgen op 1pm
- Uitvoer: OK

> [!NOTE]
> In een praktijk scenario voert de client een actie uit als gevolg van het volt ooien van de opdracht. In dit artikel wordt ervan uitgegaan dat u een mechanisme hebt voor het bijwerken van de waarschuwing in uw back-end-toepassing.

## <a name="prerequisites"></a>Vereisten

Voer de stappen in de volgende artikelen uit:
> [!div class="checklist"]

> * [Snelstartgids: een preview-app voor aangepaste opdrachten maken](./quickstart-custom-speech-commands-create-new.md)
> * [Snelstartgids: een preview-app voor aangepaste opdrachten maken met para meters](./quickstart-custom-speech-commands-create-parameters.md)
> * [Procedure: bevestigingen toevoegen aan een opdracht in een preview-app voor aangepaste opdrachten](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>Interactie regels toevoegen voor correctie met één stap

Als u een correctie met één stap wilt demonstreren, breidt u de **SetAlarm** -opdracht uit die u hebt gemaakt in de [procedure: een bevestiging toevoegen aan een opdracht in het voor beeld van aangepaste opdrachten](./how-to-custom-speech-commands-confirmations.md).

1. Een interactie regel toevoegen om de **SetAlarm** -opdracht bij te werken.

    Deze regel vraagt de gebruiker om de datum en tijd van het alarm te bevestigen en verwacht een bevestiging (Ja/Nee) voor de volgende beurt.

   | Instelling               | Voorgestelde waarde                                                  | Beschrijving                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **Regel naam**             | **Vorige waarschuwing bijwerken**                                            | Een naam die het doel van de regel beschrijft          |
   | **Voorwaarden**            | **De vorige opdracht moet worden bijgewerkt & vereiste para meter-> DateTime**                | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | **Acties**               | **Spraak antwoord verzenden-> eenvoudige editor: > vorige waarschuwing bijwerken naar {DateTime}**      | De actie die moet worden uitgevoerd wanneer de voor waarden van de regel waar zijn |
   | **Status na uitvoering** | **Opdracht voltooid**        | Status van de gebruiker na de turn                   |

1. Selecteer in het deel venster de interactie regel die u zojuist hebt gemaakt. Selecteer de knop met het weglatings teken (**...**) in de linkerbovenhoek van het deel venster. Gebruik vervolgens de pijl **omhoog** om de regel te verplaatsen naar de bovenkant van de lijst met **interactie regels** .
   > [!div class="mx-imgBorder"]
   > ![Validatie van een bereik toevoegen](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > In een echte toepassing gebruikt u de sectie **acties** om een activiteit terug te sturen naar de client of een http-eind punt aan te roepen om de waarschuwing in uw systeem bij te werken.

## <a name="try-it-out"></a>Beleid uitproberen

1. **Trein**selecteren.

1. Nadat de training is voltooid, selecteert u **testen**en probeert u de volgende interacties:

   - Invoer: alarm instellen voor morgen om 12:00 uur
   - Uitvoer: weet u zeker dat u een alarm wilt instellen voor 2020-05-02 12:00:00
   - Invoer: Ja
   - Uitvoer: OK, alarm ingesteld voor 2020-05-02 12:00:00
   - Invoer: Nee, morgen op 2pm
   - Uitvoer: vorige waarschuwing bijwerken naar 2020-05-02 14:00:00
