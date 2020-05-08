---
title: 'Procedure: een bevestiging toevoegen aan een aangepaste opdracht (preview-versie)'
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt beschreven hoe u bevestigingen voor een opdracht in aangepaste opdrachten implementeert.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: bf1b79c1b5d7b9dfd93b354c6b6ff5a512bb74a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858238"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Procedure: een bevestiging toevoegen aan een aangepaste opdracht (preview-versie)

In dit artikel leert u hoe u een bevestiging kunt toevoegen aan een opdracht.

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:
> [!div class="checklist"]
> *  [Snelstartgids: een aangepaste opdracht maken (preview)](./quickstart-custom-speech-commands-create-new.md)
> * [Snelstartgids: een aangepaste opdracht maken met para meters (preview)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Een SetAlarm-opdracht maken

Om bevestigingen te demonstreren, laten we een nieuwe opdracht maken waarmee de gebruiker een alarm kan instellen.

1. Open de eerder gemaakte toepassing voor aangepaste opdrachten in [Speech Studio](https://speech.microsoft.com/).
1. Maak een nieuwe opdracht `SetAlarm`.
1. Voeg een para meter `DateTime` toe met de naam met de volgende configuratie.

   | Instelling                           | Voorgestelde waarde                     |  Beschrijving                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | Naam                              | DateTime                                | Een beschrijvende naam voor de para meter                                |
   | Vereist                          | wel                                 | Selectie vakje dat aangeeft of een waarde voor deze para meter vereist is voordat de opdracht wordt voltooid |
   | Antwoord voor vereiste para meter   | Eenvoudige editor-> welke tijd?                              | Een prompt om te vragen naar de waarde van deze para meter als deze niet bekend is |
   | Type                              | DateTime                                | Het type para meter, zoals getal, teken reeks, datum/tijd of geografie   |
   | Standaard waarden                     | Als datum ontbreekt, gebruikt u vandaag            | De standaard waarde van de variabele die moet worden gebruikt als deze niet wordt verschaft door de gebruiker.  |  
   | Standaard waarden voor tijd                     | Gebruik begin van dag als tijd ontbreekt     |  De standaard waarde van de variabele die moet worden gebruikt als deze niet wordt verschaft door de gebruiker.|

1. Voeg enkele voorbeeld zinnen toe.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Voeg een voltooiings regel toe om het resultaat te bevestigen.

   | Instelling    | Voorgestelde waarde                               |Beschrijving                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | Regelnaam  | Alarm instellen                                               |    Een naam die het doel van de regel beschrijft |
   | Acties    | Spraak antwoord verzenden-OK, alarm ingesteld voor {DateTime} "    |De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is

## <a name="try-it-out"></a>Beleid uitproberen

1. Selecteer `Train` het pictogram dat boven in het rechterdeel venster wordt weer gegeven.

1. Wanneer de training is voltooid, `Test`selecteert u.
    - Invoer: alarm instellen voor morgen om 12:00 uur
    - Uitvoer: OK, alarm ingesteld voor 2020-05-02 12:00:00
    - Invoer: een alarm instellen
    - Uitvoer: welk tijdstip?
    - Invoer: tot 17:00 uur
    - Uitvoer: OK, alarm ingesteld voor 2020-05-01 17:00:00

## <a name="add-the-advanced-rules-for-confirmation"></a>De geavanceerde regels voor de bevestiging toevoegen

Bevestigingen worden bereikt via toevoeging van regels voor interacties.

1. Voeg in de `SetAlarm` bestaande opdracht een **interactie regel** toe via selectie `+Add` pictogram in het middelste deel venster en selecteer vervolgens de opdracht **interactie regels** -> **bevestigen**.

    Met deze regel wordt de gebruiker gevraagd om de datum en tijd van het alarm te bevestigen en wordt een bevestiging (Ja/Nee) voor de volgende beurt verwacht.

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Datum en tijd bevestigen                                                                | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | Vereiste para meter-> DateTime                                                    | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Acties               | Spraak antwoord verzenden-> weet u zeker dat u een alarm wilt instellen voor {DateTime}?     | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |
   | Uiteen          | De verwachte bevestiging van de gebruiker                                                 | Verwachting voor de volgende beurt                      |
   | Status na uitvoering  | Wachten op invoer van de gebruiker                                                            | Status voor de gebruiker nadat deze is ingeschakeld                  |
  
      > [!div class="mx-imgBorder"]
      > ![Vereist parameter antwoord maken](media/custom-speech-commands/add-validation-set-temperature.png)

1. Een andere interactie regel toevoegen om een geslaagde bevestiging te verwerken (gebruiker zei ja)

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Geaccepteerde bevestiging                                                            | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | Bevestiging is geslaagd & vereiste para meter-> DateTime                      | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Status na uitvoering | Voltooiings regels uitvoeren                                                          | Status van de gebruiker na de turn                   |

1. Een geavanceerde regel toevoegen voor het afhandelen van een bevestiging die is geweigerd (gebruiker zei nee)

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Bevestiging geweigerd                                                                   | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | Bevestiging is geweigerd & vereiste para meter-> DateTime                               | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Acties               | Waarde van para meter wissen-> datum/tijd & spraak antwoord verzenden-> geen probleem, welke tijd dan?  | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |
   | Status na uitvoering | Wachten op invoer                                                                   | Status van de gebruiker na de turn                   |
   | Uiteen          | De verwachte para meters worden ingevoerd door de gebruiker-> DateTime                           | Verwachting voor de volgende beurt                      |

## <a name="try-out-the-changes"></a>De wijzigingen proberen

Selecteer `Train`, wacht tot de training is voltooid `Test`en selecteer deze.

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
> [Procedure: een correctie met één stap toevoegen aan een aangepaste opdracht (preview-versie)](./how-to-custom-speech-commands-one-step-correction.md)