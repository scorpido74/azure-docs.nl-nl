---
title: 'Procedure: validaties toevoegen aan aangepaste opdracht parameters'
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
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857181"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Procedure: validaties toevoegen aan aangepaste opdracht parameters (preview-versie)

In dit artikel voegt u validaties toe aan para meters en vraagt u om een correctie.

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:

> [!div class="checklist"]
> * [Snelstartgids: een aangepaste opdracht maken](./quickstart-custom-speech-commands-create-new.md)
> * [Snelstartgids: een aangepaste opdracht met para meters maken](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Een SetTemperature-opdracht maken

Voor het demonstreren van validaties, gaan we een nieuwe opdracht maken waarmee gebruikers temperatuur kunnen instellen.

1. Open de eerder gemaakte toepassing voor aangepaste opdrachten in [Speech Studio](https://speech.microsoft.com/)
1. Een nieuwe opdracht maken`SetTemperature`
1. Voeg een para meter voor de doel temperatuur toe.

   | Parameter configuratie           | Voorgestelde waarde    |Beschrijving                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | Naam              | Temperatuur                       | Een beschrijvende naam voor de para meter                                |
   | Vereist          | wel                           | Selectie vakje dat aangeeft of een waarde voor deze para meter vereist is voordat de opdracht wordt voltooid |
   | Antwoord voor vereiste para meter     | Eenvoudige editor-> welke Tempe ratuur wilt u?  | Een prompt om te vragen naar de waarde van deze para meter als deze niet bekend is |
   | Type              | Getal                            | Type para meter, zoals getal, teken reeks, datum en tijd   |

1. Voeg een validatie voor de para meter Tempe ratuur toe.

    - Selecteer `Add a validation` op de pagina **para meters** configureren voor `Temperature` para meter in de sectie validaties.
    - Vul de waarden in de pop-up **nieuwe validatie** als volgt in en selecteer **maken**.

  
       | Parameter configuratie         | Voorgestelde waarde                                          | Beschrijving                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | Minimumwaarde        | 60               | Voor numerieke para meters kan de minimum waarde van deze para meter aannemen |
       | Maximumwaarde        | 80               | Voor numerieke para meters kan de maximum waarde van deze para meter aannemen |
       | Fout bericht-eenvoudige editor| Eerste variatie-er kan alleen worden ingesteld tussen 60 en 80 graden      | Vragen om een nieuwe waarde te vragen als de validatie is mislukt                                       |

       > [!div class="mx-imgBorder"]
       > ![Validatie van een bereik toevoegen](media/custom-speech-commands/validations-add-temperature.png)

1. Enkele voorbeeld zinnen toevoegen

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Een voltooiings regel toevoegen om het resultaat te bevestigen

   | Instelling    | Voorgestelde waarde                                           |Beschrijving                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Naam       | Bevestigings bericht                                      |Een naam die het doel van de regel beschrijft |
   | Voorwaarden | Vereiste para meters-`Temperature`                       |Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | Acties    | Spraak antwoord verzenden-`Ok, setting temperature to {Temperature} degrees` | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |

> [!TIP]
> In dit voor beeld wordt een spraak antwoord gebruikt om het resultaat te bevestigen. Voor voor beelden van het volt ooien van de opdracht met een client actie raadpleegt [u: opdrachten uitvoeren op de client met de Speech SDK](./how-to-custom-speech-commands-fulfill-sdk.md)


## <a name="try-it-out"></a>Beleid uitproberen
1. Selecteer `Train` het pictogram dat boven in het rechterdeel venster wordt weer gegeven.

1. Zodra de training is voltooid, selecteert `Test` u een aantal interacties en probeert u het opnieuw.

    - Invoer: Stel de Tempe ratuur in op 72 graden
    - Uitvoer: OK, Tempe ratuur instellen op 72 graden
    - Invoer: Stel de Tempe ratuur in op 45 graden
    - Uitvoer: Sorry, ik kan alleen tussen 60 en 80 graden instellen
    - Invoer: Maak in plaats daarvan 72 graden
    - Uitvoer: OK, Tempe ratuur instellen op 72 graden

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: een bevestiging toevoegen aan een aangepaste opdracht (preview-versie)](./how-to-custom-speech-commands-confirmations.md)
