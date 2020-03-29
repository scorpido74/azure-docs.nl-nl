---
title: 'How to: Een bevestiging toevoegen aan een aangepaste opdracht (Voorbeeld)'
titleSuffix: Azure Cognitive Services
description: In dit artikel u bevestigingen implementeren voor een opdracht in Aangepaste opdrachten.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456496"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>How To: een bevestiging toevoegen aan een aangepaste opdracht (voorbeeld)

In dit artikel leert u hoe u een bevestiging toevoegt aan een opdracht.

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:

- [Snelstart: een aangepaste opdracht maken (voorbeeld)](./quickstart-custom-speech-commands-create-new.md)
- [Snelstart: een aangepaste opdracht maken met parameters (voorbeeld)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Een setalarm maken, opdracht

Als u validaties wilt demonstreren, maken we een nieuwe opdracht waarmee de gebruiker een alarm kan instellen.

1. De eerder gemaakte toepassing Aangepaste opdrachten openen in [Spraakstudio](https://speech.microsoft.com/)
1. Een nieuw **opdrachtsetalarm maken**
1. Een parameter met de naam DateTime toevoegen

   | Instelling           | Voorgestelde waarde                                          | Beschrijving                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Name              | DateTime                                                 | Een beschrijvende naam voor de parameter Command                                                    |
   | Vereist          | waar                                                     | Selectievakje dat aangeeft of een waarde voor deze parameter vereist is voordat de opdracht wordt voltooid |
   | Antwoordsjabloon | "- Hoe laat?"                                           | Een prompt om de waarde van deze parameter te vragen wanneer deze niet bekend is                              |
   | Type              | DateTime                                                 | Het type parameter, zoals Getal, Tekenreeks of Datumtijd                                      |
   | Datumstandaardwaarden     | Als datum ontbreekt gebruik vandaag                             |                                                                                                  |
   | Standaardinstellingen voor tijd     | Als de tijd ontbreekt gebruik begin van de dag                      |                                                                                                  | 

1. Enkele voorbeeldzinnen toevoegen
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Een voltooiingsregel toevoegen om het resultaat te bevestigen

   | Instelling    | Voorgestelde waarde                                         | Beschrijving                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam  | Alarm instellen                                               | Een naam die het doel van de regel beschrijft          |
   | Acties    | SpeechResponse - "- Ok, alarm ingesteld voor {DateTime}"       | De actie die moet worden ondernomen wanneer de regelvoorwaarde waar is |

## <a name="try-it-out"></a>Uitproberen

Selecteer het deelvenster Testen en probeer een paar interacties.

- Invoer: Zet de wekker voor morgen om 12.00 uur
- Uitgang: "Ok, alarm ingesteld voor 12/06/2019 12:00:00"

- Invoer: een alarm instellen
- Output: "Hoe laat?"
- Input: 17.00 uur
- Uitgang: "Ok, alarm ingesteld voor 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>De geavanceerde regels voor bevestiging toevoegen

1. Voeg een geavanceerde regel toe voor bevestiging. 

    Deze regel vraagt de gebruiker om de datum en het tijdstip van het alarm te bevestigen en verwacht een bevestiging (ja/nee) voor de volgende beurt.

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Datumtijd bevestigen                                                                | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | Vereiste parameter - DateTime                                                    | Voorwaarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Acties               | SpeechResponse - "- Weet u zeker dat u een alarm wilt instellen voor {DateTime}?"       | De actie die moet worden ondernomen wanneer de regelvoorwaarde waar is |
   | Staat na uitvoering | Wachten op invoer                                                                   | Status voor de gebruiker na de turn                  |
   | Verwachtingen          | Bevestiging                                                                     | Verwachting voor de volgende beurt                      |

1. Een geavanceerde regel toevoegen om een succesvolle bevestiging af te handelen (gebruiker zei ja)

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Geaccepteerde bevestiging                                                            | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | Geslaagde bevestiging & vereiste parameter - DateTime                           | Voorwaarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Staat na uitvoering | Klaar voor voltooiing                                                             | Staat van de gebruiker na de turn                   |

1. Een geavanceerde regel toevoegen om een geweigerde bevestiging af te handelen (gebruiker zei nee)

   | Instelling               | Voorgestelde waarde                                                                  | Beschrijving                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam             | Geweigerd bevestigen                                                                   | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden            | DeniedConfirmation & Vereiste parameter - DateTime                               | Voorwaarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Acties               | ClearParameter - DateTime & SpeechResponse - "- Geen probleem, hoe laat dan?"     | De actie die moet worden ondernomen wanneer de regelvoorwaarde waar is |
   | Staat na uitvoering | Wachten op invoer                                                                   | Staat van de gebruiker na de turn                   |
   | Verwachtingen          | ElicitParameters - DateTime                                                      | Verwachting voor de volgende beurt                      |

## <a name="try-it-out"></a>Uitproberen

Selecteer het deelvenster Testen en probeer een paar interacties.

- Invoer: Zet de wekker voor morgen om 12.00 uur
- Output: "Weet u zeker dat u een alarm wilt instellen voor 12/07/2019 12:00:00?"
- Invoer: Nee
- Output: "Geen probleem, hoe laat dan?"
- Input: 17.00 uur
- Output: "Weet u zeker dat u een alarm wilt instellen voor 12/06/2019 17:00:00?"
- Invoer: Ja
- Uitgang: "Ok, alarm ingesteld voor 12/06/2019 17:00:00"

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [How To: een correctie in één stap toevoegen aan een aangepaste opdracht (voorbeeld)](./how-to-custom-speech-commands-one-step-correction.md)