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
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75456496"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Procedure: een bevestiging toevoegen aan een aangepaste opdracht (preview-versie)

In dit artikel leert u hoe u een bevestiging kunt toevoegen aan een opdracht.

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:

- [Snelstartgids: een aangepaste opdracht maken (preview)](./quickstart-custom-speech-commands-create-new.md)
- [Snelstartgids: een aangepaste opdracht maken met para meters (preview)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Een SetAlarm-opdracht maken

Voor het demonstreren van validaties kunt u een nieuwe opdracht maken waarmee de gebruiker een alarm kan instellen.

1. Open de eerder gemaakte toepassing voor aangepaste opdrachten in [Speech Studio](https://speech.microsoft.com/)
1. Een nieuwe opdracht **SetAlarm** maken
1. Een para meter met de naam DateTime toevoegen

   | Instelling           | Voorgestelde waarde                                          | Beschrijving                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Naam              | DateTime                                                 | Een beschrijvende naam voor de opdracht parameter                                                    |
   | Vereist          | waar                                                     | Selectie vakje dat aangeeft of een waarde voor deze para meter vereist is voordat de opdracht wordt voltooid |
   | Antwoord sjabloon | "-Welk tijdstip?"                                           | Een prompt om te vragen naar de waarde van deze para meter als deze niet bekend is                              |
   | Type              | DateTime                                                 | Het type para meter, zoals getal, teken reeks of datum/tijd                                      |
   | Standaard waarden     | Als datum ontbreekt, gebruikt u vandaag                             |                                                                                                  |
   | Standaard waarden voor tijd     | Gebruik begin van dag als tijd ontbreekt                      |                                                                                                  | 

1. Enkele voorbeeld zinnen toevoegen
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Een voltooiings regel toevoegen om het resultaat te bevestigen

   | Instelling    | Voorgestelde waarde                                         | Beschrijving                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam  | Alarm instellen                                               | Een naam die het doel van de regel beschrijft          |
   | Acties    | SpeechResponse-"-OK, alarm ingesteld voor {DateTime}"       | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |

## <a name="try-it-out"></a>Uitproberen

Selecteer het deel venster testen en probeer enkele interacties.

- Invoer: alarm instellen voor morgen om 12:00 uur
- Uitvoer: "OK, alarm ingesteld voor 12/06/2019 12:00:00"

- Invoer: een alarm instellen
- Uitvoer: "welk tijdstip?"
- Invoer: tot 17:00 uur
- Uitvoer: "OK, alarm ingesteld voor 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>De geavanceerde regels voor de bevestiging toevoegen

1. Voeg een geavanceerde regel toe voor de bevestiging. 

    Met deze regel wordt de gebruiker gevraagd om de datum en tijd van het alarm te bevestigen en wordt een bevestiging (Ja/Nee) voor de volgende beurt verwacht.

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Datum en tijd bevestigen                                                                | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | Vereiste para meter-DateTime                                                    | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Acties               | SpeechResponse-'-weet u zeker dat u een alarm wilt instellen voor {DateTime}? '       | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |
   | Status na uitvoering | Wachten op invoer                                                                   | Status voor de gebruiker nadat deze is ingeschakeld                  |
   | Uiteen          | Bevestiging                                                                     | Verwachting voor de volgende beurt                      |

1. Een geavanceerde regel toevoegen voor het afhandelen van een geslaagde bevestiging (gebruiker zei ja)

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Geaccepteerde bevestiging                                                            | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | SuccessfulConfirmation & vereiste para meter-DateTime                           | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Status na uitvoering | Gereed voor voltooiing                                                             | Status van de gebruiker na de turn                   |

1. Een geavanceerde regel toevoegen voor het afhandelen van een bevestiging die is geweigerd (gebruiker zei nee)

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Bevestiging geweigerd                                                                   | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | DeniedConfirmation & vereiste para meter-DateTime                               | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Acties               | ClearParameter-DateTime & SpeechResponse-"-No-probleem, what time then?"     | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |
   | Status na uitvoering | Wachten op invoer                                                                   | Status van de gebruiker na de turn                   |
   | Uiteen          | ElicitParameters-DateTime                                                      | Verwachting voor de volgende beurt                      |

## <a name="try-it-out"></a>Uitproberen

Selecteer het deel venster testen en probeer enkele interacties.

- Invoer: alarm instellen voor morgen om 12:00 uur
- Uitvoer: ' weet u zeker dat u een alarm wilt instellen voor 12/07/2019 12:00:00? '
- Invoer: Nee
- Uitvoer: ' geen probleem, wat is dan het tijdstip? '
- Invoer: tot 17:00 uur
- Uitvoer: ' weet u zeker dat u een alarm wilt instellen voor 12/06/2019 17:00:00? '
- Invoer: Ja
- Uitvoer: "OK, alarm ingesteld voor 12/06/2019 17:00:00"

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: een correctie met één stap toevoegen aan een aangepaste opdracht (preview-versie)](./how-to-custom-speech-commands-one-step-correction.md)