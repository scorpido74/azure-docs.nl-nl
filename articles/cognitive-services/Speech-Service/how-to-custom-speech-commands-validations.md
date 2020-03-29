---
title: 'How To: Validaties toevoegen aan aangepaste opdrachtparameters (voorbeeld)'
titleSuffix: Azure Cognitive Services
description: In dit artikel leggen we uit hoe u validaties toevoegt aan een parameter in Aangepaste opdrachten.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156451"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>How To: Validaties toevoegen aan aangepaste opdrachtparameters (voorbeeld)

In dit artikel leert u hoe u validaties toevoegt aan parameters en vraagt om correctie.

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:

- [Snelstart: een aangepaste opdracht maken (voorbeeld)](./quickstart-custom-speech-commands-create-new.md)
- [Snelstart: een aangepaste opdracht maken met parameters (voorbeeld)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Een settemperatuur maken, opdracht

Als u validaties wilt demonstreren, maken we een nieuwe opdracht waarmee de gebruiker de temperatuur kan instellen.

1. De eerder gemaakte toepassing Aangepaste opdrachten openen in [Spraakstudio](https://speech.microsoft.com/)
1. Een nieuwe **opdrachtsettemperatuur maken**
1. Een parameter voor de doeltemperatuur toevoegen
1. Een validatie voor de temperatuurparameter toevoegen
   > [!div class="mx-imgBorder"]
   > ![Een bereikvalidatie toevoegen](media/custom-speech-commands/validations-add-temperature.png)

   | Instelling           | Voorgestelde waarde                                          | Beschrijving                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Name              | Temperatuur                                              | Een beschrijvende naam voor de parameter Command                                                    |
   | Vereist          | waar                                                     | Selectievakje dat aangeeft of een waarde voor deze parameter vereist is voordat de opdracht wordt voltooid |
   | Antwoordsjabloon | "- Welke temperatuur zou je willen?"                     | Een prompt om de waarde van deze parameter te vragen wanneer deze niet bekend is                              |
   | Type              | Aantal                                                   | Het type parameter, zoals Getal, Tekenreeks of Datumtijd                                      |
   | Validatie        | Min Waarde: 60, Max Waarde: 80                             | Voor getalparameters wordt het toegestane bereik van waarden voor de parameter                             |
   | Antwoordsjabloon | "- Sorry, ik kan alleen instellen tussen 60 en 80 graden"      | Vragen om een bijgewerkte waarde te vragen als de validatie mislukt                                       |

1. Enkele voorbeeldzinnen toevoegen

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Een voltooiingsregel toevoegen om het resultaat te bevestigen

   | Instelling    | Voorgestelde waarde                                           | Beschrijving                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam  | Bevestigingsbericht                                      | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden | Vereiste parameter - Temperatuur                          | Voorwaarden die bepalen wanneer de regel kan worden uitgevoerd    |
   | Acties    | SpeechResponse - "- Ok, instellen op {Temperature} graden" | De actie die moet worden ondernomen wanneer de regelvoorwaarde waar is |

> [!TIP]
> In dit voorbeeld wordt een spraakreactie gebruikt om het resultaat te bevestigen. Zie voor voorbeelden bij het voltooien van de opdracht met een clientactie: [Hoe u opdrachten voor de client uitvoeren met de Spraak-SDK (Preview)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Uitproberen

Selecteer het deelvenster Testen en probeer een paar interacties.

- Invoer: Stel de temperatuur in op 72 graden
- Uitvoer: "Ok, instellen op 72 graden"

- Invoer: Stel de temperatuur in op 45 graden
- Output: "Sorry, ik kan alleen instellen tussen 60 en 80 graden"
- Input: maak het 72 graden in plaats
- Uitvoer: "Ok, instellen op 72 graden"

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [How To: een bevestiging toevoegen aan een aangepaste opdracht (voorbeeld)](./how-to-custom-speech-commands-confirmations.md)
