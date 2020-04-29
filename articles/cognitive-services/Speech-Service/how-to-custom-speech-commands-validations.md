---
title: 'Procedure: validaties toevoegen aan aangepaste opdracht parameters (preview-versie)'
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u validaties kunt toevoegen aan een para meter in aangepaste opdrachten.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76156451"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Procedure: validaties toevoegen aan aangepaste opdracht parameters (preview-versie)

In dit artikel leert u hoe u validaties kunt toevoegen aan para meters en vraagt u om een correctie.

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:

- [Snelstartgids: een aangepaste opdracht maken (preview)](./quickstart-custom-speech-commands-create-new.md)
- [Snelstartgids: een aangepaste opdracht maken met para meters (preview)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Een SetTemperature-opdracht maken

Voor het demonstreren van validaties kunt u een nieuwe opdracht maken waarmee de gebruiker de Tempe ratuur kan instellen.

1. Open de eerder gemaakte toepassing voor aangepaste opdrachten in [Speech Studio](https://speech.microsoft.com/)
1. Een nieuwe opdracht **SetTemperature** maken
1. Een para meter voor de doel temperatuur toevoegen
1. Een validatie voor de para meter Tempe ratuur toevoegen
   > [!div class="mx-imgBorder"]
   > ![Validatie van een bereik toevoegen](media/custom-speech-commands/validations-add-temperature.png)

   | Instelling           | Voorgestelde waarde                                          | Beschrijving                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Naam              | Temperatuur                                              | Een beschrijvende naam voor de opdracht parameter                                                    |
   | Vereist          | waar                                                     | Selectie vakje dat aangeeft of een waarde voor deze para meter vereist is voordat de opdracht wordt voltooid |
   | Antwoord sjabloon | "-Welke Tempe ratuur wilt u graag?"                     | Een prompt om te vragen naar de waarde van deze para meter als deze niet bekend is                              |
   | Type              | Aantal                                                   | Het type para meter, zoals getal, teken reeks of datum/tijd                                      |
   | Validatie        | Minimum waarde: 60, maximum waarde: 80                             | Voor nummer parameters, het toegestane bereik van waarden voor de para meter                             |
   | Antwoord sjabloon | "-Sorry, ik kan alleen tussen 60 en 80 graden instellen"      | Vragen om een bijgewerkte waarde te vragen als de validatie is mislukt                                       |

1. Enkele voorbeeld zinnen toevoegen

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Een voltooiings regel toevoegen om het resultaat te bevestigen

   | Instelling    | Voorgestelde waarde                                           | Beschrijving                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Regelnaam  | Bevestigings bericht                                      | Een naam die het doel van de regel beschrijft          |
   | Voorwaarden | Vereiste para meter-Tempe ratuur                          | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |
   | Acties    | SpeechResponse-"-OK, instellen op {Tempe ratuur} graden" | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |

> [!TIP]
> In dit voor beeld wordt een spraak antwoord gebruikt om het resultaat te bevestigen. Voor voor beelden van het volt ooien van de opdracht met een client actie raadpleegt [u: opdrachten uitvoeren op de client met de Speech-SDK (preview)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Uitproberen

Selecteer het deel venster testen en probeer enkele interacties.

- Invoer: Stel de Tempe ratuur in op 72 graden
- Uitvoer: "OK, instellen op 72 graden"

- Invoer: Stel de Tempe ratuur in op 45 graden
- Uitvoer: ' Sorry, ik kan alleen tussen 60 en 80 graden instellen '
- Invoer: Maak in plaats daarvan 72 graden
- Uitvoer: "OK, instellen op 72 graden"

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: een bevestiging toevoegen aan een aangepaste opdracht (preview-versie)](./how-to-custom-speech-commands-confirmations.md)
