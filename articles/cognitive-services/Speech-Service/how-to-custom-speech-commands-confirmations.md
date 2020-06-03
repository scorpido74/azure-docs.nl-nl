---
title: Bevestigingen toevoegen in een aangepaste opdracht voor beeld van app-Speech Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het toevoegen van bevestigingen aan opdrachten in een preview-app voor aangepaste opdrachten.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 1cb0624012b22b6cae2c98bfa6ddc9495e09615d
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310465"
---
# <a name="add-confirmations-to-a-command-in-a-custom-commands-preview-application"></a>Bevestigingen toevoegen aan een opdracht in een voorbeeld toepassing met aangepaste opdrachten

In dit artikel leert u hoe u bevestigingen kunt maken voor opdrachten in een preview-app met aangepaste opdrachten.

## <a name="prerequisites"></a>Vereisten

Voer de stappen in de volgende artikelen uit:
> [!div class="checklist"]
> * [Snelstartgids: een preview-app voor aangepaste opdrachten maken](./quickstart-custom-speech-commands-create-new.md)
> * [Snelstartgids: een preview-app voor aangepaste opdrachten maken met para meters](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Een SetAlarm-opdracht maken

Om bevestigingen te demonstreren, maakt u een nieuwe opdracht waarmee een alarm wordt ingesteld.

1. Open in [Speech Studio](https://speech.microsoft.com/)de voor beeld-app voor aangepaste opdrachten die u hebt gemaakt.
1. Maak een nieuwe **SetAlarm** -opdracht.
1. Voeg een **DateTime** -para meter met de volgende configuratie toe:

   | Instelling                           | Voorgestelde waarde                     |  Beschrijving                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | **Naam**                              | **Datum/tijd**                                | Beschrijvende naam voor de para meter                                |
   | **Vereist**                          | Ingeschakeld                                 | Selectie vakje dat aangeeft of een waarde voor deze para meter vereist is voordat de opdracht wordt voltooid |
   | **Antwoord voor een vereiste para meter**   | **Eenvoudige editor-> welke tijd?**                              | Een prompt om te vragen naar de waarde van deze para meter als deze niet bekend is |
   | **Type**                              | **Datum/tijd**                                | Type para meter, zoals Number, String, DateTime of geografie   |
   | **Standaard waarden**                     | Als de datum ontbreekt, gebruikt u de datum van vandaag            | Standaard waarde van de variabele die moet worden gebruikt als niet wordt aangegeven door de gebruiker  |  
   | **Standaard waarden voor tijd**                     | Als de tijd ontbreekt, gebruikt u het begin van de dag     |  Standaard waarde van de variabele die moet worden gebruikt als deze niet wordt verschaft door de gebruiker|

1. Voeg enkele voorbeeld zinnen toe.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Voeg een voltooiings regel toe om het resultaat te bevestigen. Gebruik de volgende configuratie:

   | Instelling    | Voorgestelde waarde                               |Beschrijving                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | **Regel naam**  | **Alarm instellen**                                               |    Een naam die het doel van de regel beschrijft |
   | **Acties**    | **Spraak antwoord verzenden-> OK, alarm ingesteld voor {DateTime}**    |De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is

## <a name="try-it-out"></a>Beleid uitproberen

1. Selecteer **trein** bovenaan in het rechterdeel venster.

1. Nadat de training is voltooid, selecteert u **testen**en probeert u de volgende interacties:
    - Invoer: alarm instellen voor morgen om 12:00 uur
    - Uitvoer: OK, alarm ingesteld voor 2020-05-02 12:00:00
    - Invoer: een alarm instellen
    - Uitvoer: welk tijdstip?
    - Invoer: tot 17:00 uur
    - Uitvoer: OK, alarm ingesteld voor 2020-05-01 17:00:00

## <a name="add-interaction-rules-for-the-confirmation"></a>Interactie regels voor de bevestiging toevoegen

Maak bevestigingen door interactie regels toe te voegen.

1. Selecteer in de **SetAlarm** -opdracht **toevoegen** in het middelste deel venster en selecteer vervolgens de opdracht regels bevestigen van de **interactie**  >  **Confirm command**.

    Deze regel vraagt de gebruiker om de datum en tijd van de waarschuwing te bevestigen. Gebruik deze instellingen:

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Regel naam**             | **Datum en tijd bevestigen**                                                                | Een naam die het doel van de regel beschrijft          |
   | **Voorwaarden**            | **Vereiste para meter-> DateTime**                                                    | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | **Acties**               | **Spraak antwoord verzenden-> weet u zeker dat u een alarm wilt instellen voor {DateTime}?**     | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |
   | **Uiteen**          | **De verwachte bevestiging van de gebruiker**                                                 | Verwachting voor de volgende beurt                      |
   | **Status na uitvoering**  | **Wachten op invoer van de gebruiker**                                                            | Status voor de gebruiker nadat deze is ingeschakeld                  |
  
      > [!div class="mx-imgBorder"]
      > ![Vereist parameter antwoord maken](media/custom-speech-commands/add-validation-set-temperature.png)

1. Een interactie regel toevoegen voor een geaccepteerde bevestiging (gebruiker zei ' ja '). Gebruik de volgende configuratie:

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Regel naam**             | **Geaccepteerde bevestiging**                                                            | Een naam die het doel van de regel beschrijft          |
   | **Voorwaarden**            | **Bevestiging is geslaagd & vereiste para meter-> DateTime**                      | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | **Status na uitvoering** | **Voltooiings regels uitvoeren**                                                          | Status van de gebruiker na de turn                   |

1. Een interactie regel voor een geweigerde bevestiging toevoegen (gebruiker zei "nee"). Gebruik de volgende configuratie:

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Regel naam**             | **Bevestiging geweigerd**                                                                   | Een naam die het doel van de regel beschrijft          |
   | **Voorwaarden**            | **Bevestiging is geweigerd & vereiste para meter-> DateTime**                               | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | **Acties**               | **Waarde van para meter wissen-> datum/tijd & spraak antwoord verzenden-> geen probleem, welke tijd dan?**  | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |
   | **Status na uitvoering** | **Wachten op invoer**                                                                   | Status van de gebruiker na de turn                   |
   | **Uiteen**          | **De invoer van para meters wordt verwacht van de datum/tijd van de gebruiker >**                           | Verwachting voor de volgende beurt                      |

## <a name="try-out-the-changes"></a>De wijzigingen proberen

1. **Trein**selecteren.

1. Nadat de training is voltooid, selecteert u **testen**en probeert u de volgende interacties:

    - Invoer: alarm instellen voor morgen om 12:00 uur
    - Uitvoer: weet u zeker dat u een alarm wilt instellen voor 2020-05-02 12:00:00?
    - Invoer: Nee
    - Uitvoer: geen probleem, welke tijd dan?
    - Invoer: tot 17:00 uur
    - Uitvoer: ' weet u zeker dat u een alarm wilt instellen voor 2020-05-01 17:00:00? '
    - Invoer: Ja
    - Uitvoer: OK, alarm ingesteld voor 2020-05-01 17:00:00

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een correctie met één stap toevoegen aan een opdracht in een preview-app voor aangepaste opdrachten](./how-to-custom-speech-commands-one-step-correction.md)
