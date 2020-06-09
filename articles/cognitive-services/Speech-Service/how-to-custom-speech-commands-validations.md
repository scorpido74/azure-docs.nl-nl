---
title: Validaties toevoegen in aangepaste opdrachten preview-Speech Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het toevoegen van validaties aan een opdracht parameter in een preview-app voor aangepaste opdrachten.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: nitinme
ms.openlocfilehash: eb011510a9f636aea9910a4be445cd094acf0c21
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509573"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>Validaties toevoegen aan een opdracht parameter in een voorbeeld toepassing met aangepaste opdrachten

In dit artikel leert u hoe u validaties kunt toevoegen aan para meters en vraagt u om een correctie.

## <a name="prerequisites"></a>Vereisten

Voer de stappen in de volgende artikelen uit:

> [!div class="checklist"]
 
> * [Snelstartgids: een preview-app voor aangepaste opdrachten maken](./quickstart-custom-speech-commands-create-new.md)
> * [Snelstartgids: een preview-app voor aangepaste opdrachten maken met para meters](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Een SetTemperature-opdracht maken

Voor het demonstreren van validaties maakt u een nieuwe opdracht waarmee gebruikers temperatuur kunnen instellen.

1. Open in [Speech Studio](https://speech.microsoft.com/)de voor beeld-app voor aangepaste opdrachten die u hebt gemaakt.
1. Maak een nieuwe **SetTemperature** -opdracht.
1. Voeg een para meter voor de Tempe ratuur toe die de volgende configuratie bevat:

   | Parameter configuratie           | Voorgestelde waarde    |Beschrijving                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **Naam**              | **Temperatuur**                       | Een beschrijvende naam voor de para meter                                |
   | **Vereist**          | Ingeschakeld                           | Selectie vakje dat aangeeft of een waarde voor deze para meter vereist is voordat de opdracht wordt voltooid |
   | **Antwoord voor vereiste para meter**     | **Eenvoudige editor-> welke Tempe ratuur wilt u?**  | Een prompt om te vragen naar de waarde van deze para meter als deze niet bekend is |
   | **Type**              | **Number**                            | Type para meter, zoals Number, String, DateTime of geografie   |

1. Voeg een validatie voor de para meter Tempe ratuur toe.

    1. Selecteer op de pagina **para meters** configureren voor de para meter Tempe ratuur **een validatie toevoegen** **in de sectie validaties** .

    1. Configureer de validatie op de volgende manier in het pop-upvenster **nieuwe validatie** :
  
       | Parameter configuratie         | Voorgestelde waarde                                          | Beschrijving                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **Minimumwaarde**        | **60**               | Voor numerieke para meters kan de minimum waarde van deze para meter aannemen |
       | **Maximumwaarde**        | **80**               | Voor numerieke para meters kan de maximum waarde van deze para meter aannemen |
       | **Fout bericht-eenvoudige editor**| **Eerste variatie-er kan alleen worden ingesteld tussen 60 en 80 graden**      | Vragen om een nieuwe waarde te vragen als de validatie is mislukt                                       |

       > [!div class="mx-imgBorder"]
       > ![Validatie van een bereik toevoegen](media/custom-speech-commands/validations-add-temperature.png)

1. Selecteer **Maken**.

1. Voeg enkele voorbeeld zinnen toe.

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Voeg een voltooiings regel met de volgende configuratie toe. Met deze regel wordt het resultaat bevestigd.

   | Instelling    | Voorgestelde waarde                                           |Beschrijving                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Naam       | Bevestigings bericht                                      |Een naam die het doel van de regel beschrijft |
   | **Voorwaarden** | **Vereiste para meters-Tempe ratuur**                       |Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |   
   | **Acties**    | **Spraak antwoord verzenden-OK, de Tempe ratuur instellen op {Tempe ratuur} graden** | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |

> [!TIP]
> In dit voor beeld wordt een spraak antwoord gebruikt om het resultaat te bevestigen. Voor voor beelden van het volt ooien van de opdracht met een client actie, Zie [How to: opdrachten op de client uitvoeren met de spraak-SDK](./how-to-custom-speech-commands-fulfill-sdk.md).

## <a name="try-it-out"></a>Beleid uitproberen

1. **Trein**selecteren.

1. Nadat de training is voltooid, selecteert u **testen**en probeert u de volgende interacties:

    - Invoer: Stel de Tempe ratuur in op 72 graden
    - Uitvoer: OK, Tempe ratuur instellen op 72 graden
    - Invoer: Stel de Tempe ratuur in op 45 graden
    - Uitvoer: Sorry, ik kan alleen tussen 60 en 80 graden instellen
    - Invoer: Maak in plaats daarvan 72 graden
    - Uitvoer: OK, Tempe ratuur instellen op 72 graden

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bevestigingen toevoegen aan een opdracht in een preview-app voor aangepaste opdrachten](./how-to-custom-speech-commands-confirmations.md)
