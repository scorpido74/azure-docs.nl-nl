---
title: 'Snelstartgids: een aangepaste opdracht maken met para meters (preview)-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel voegt u para meters toe aan een toepassing voor aangepaste opdrachten.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853595"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>Snelstartgids: een aangepaste opdrachten toepassing maken met para meters (preview)

In het [vorige artikel](./quickstart-custom-speech-commands-create-new.md)hebt u een eenvoudige toepassing voor aangepaste opdrachten gemaakt zonder para meters.

In dit artikel breidt u deze toepassing uit om gebruik te maken van para meters zodat het mogelijk is om meerdere apparaten in te scha kelen en uit te scha kelen.

## <a name="create-parameters"></a>Para meters maken

1. Open het project [dat u eerder hebt gemaakt](./quickstart-custom-speech-commands-create-new.md)
1. Laten we de bestaande opdracht bewerken om meerdere apparaten in te scha kelen en uit te scha kelen.
1. Omdat de opdracht nu wordt verwerkt en uitgeschakeld, wijzigt u de naam van `TurnOnOff`de opdracht in.
   - Selecteer de `TurnOn` opdracht in het linkerdeel venster en klik vervolgens op het `...` pictogram naast `+ New command` boven aan het deel venster.
   
   - Pictogram `Rename` selecteren. Wijzig **naam** in in het pop-upvenster naam wijzigen `TurOnOff`in. **Rename command** Selecteer vervolgens **Opslaan**.

1. Vervolgens maakt u een nieuwe para meter om aan te geven of de gebruiker het apparaat wil omzetten in-of uitschakelen.
   - Selecteer `+ Add` het pictogram dat boven in het middelste deel venster wordt weer gegeven. Selecteer in de vervolg keuzelijst de **para meter**.
   - In het rechter deel venster ziet u de **para meter** configuratie sectie.
   - Voeg een waarde toe aan de **naam**.
   - Schakel het selectie vakje **vereist** in. Selecteer in het venster **reactie voor een vereiste para meter toevoegen** de optie **eenvoudige editor** en de **eerste variant**toevoegen
        ```
        On or Off?
        ```
   - Selecteer **Update**.

       > [!div class="mx-imgBorder"]
       > ![Vereist parameter antwoord maken](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - Vervolgens configureert u de overige eigenschappen van de para meter als volgt en selecteert `Save` u configuratie alle configuraties opslaan in de para meter.
       

       | Configuratie      | Voorgestelde waarde     | Beschrijving                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Naam               | OnOff           | Een beschrijvende naam voor de para meter                                                                           |
       | Is wereld wijd          | uitgeschakeld       | Selectie vakje dat aangeeft of een waarde voor deze para meter globaal wordt toegepast op alle opdrachten in de toepassing|
       | Vereist           | wel         | Selectie vakje dat aangeeft of een waarde voor deze para meter vereist is voordat de opdracht wordt voltooid |
       | Antwoord voor vereiste para meter      |Eenvoudige editor-> in-of uitschakelen?      | Een prompt om te vragen naar de waarde van deze para meter als deze niet bekend is |
       | Type               | Tekenreeks          | Het type para meter, zoals getal, teken reeks, datum/tijd of geografie   |
       | Configuratie      | Vooraf gedefinieerde invoer waarden accepteren van interne catalogus | Voor teken reeksen beperkt de invoer tot een reeks mogelijke waarden |
       | Vooraf gedefinieerde invoer waarden     | aan, uit             | Set mogelijke waarden en hun aliassen         |
       
        > [!div class="mx-imgBorder"]
        > ![Para meter maken](media/custom-speech-commands/create-on-off-parameter.png)

   - Selecteer vervolgens het `+ Add` pictogram opnieuw om een tweede para meter toe te voegen om de naam van de apparaten met de volgende configuratie weer te geven.
   

       | Instelling            | Voorgestelde waarde       | Beschrijving                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | Naam               | SubjectDevice         | Een beschrijvende naam voor de para meter                                                                     |
       | Is wereld wijd          | uitgeschakeld             | Selectie vakje dat aangeeft of een waarde voor deze para meter globaal wordt toegepast op alle opdrachten in de toepassing |
       | Vereist           | wel               | Selectie vakje dat aangeeft of een waarde voor deze para meter vereist is voordat de opdracht wordt voltooid          |
       | Eenvoudige editor      | Welk apparaat?    | Een prompt om te vragen naar de waarde van deze para meter als deze niet bekend is                                       |
       | Type               | Tekenreeks                | Het type para meter, zoals getal, teken reeks, datum/tijd of geografie                                                |
       | Configuratie      | Vooraf gedefinieerde invoer waarden accepteren van interne catalogus | Voor teken reeksen beperkt een reeks lijst invoer naar een reeks mogelijke waarden       |
       | Vooraf gedefinieerde invoer waarden | TV, ventilator               | Set mogelijke waarden en hun aliassen                               |
       | Aliassen (TV)      | televisie, informatie     | Optionele aliassen voor elke mogelijke waarde van vooraf gedefinieerde invoer waarden                                 |

## <a name="add-example-sentences"></a>Voorbeeld zinnen toevoegen

Met opdrachten met para meters is het handig om voorbeeld zinnen toe te voegen die betrekking hebben op alle mogelijke combi Naties. Bijvoorbeeld:

1. Volledige parameter informatie-`turn {OnOff} the {SubjectDevice}`
1. Gedeeltelijke parameter informatie-`turn it {OnOff}`
1. Geen parameter informatie-`turn something`

Met een voor beeld van zinnen met een andere mate van informatie kan de toepassing van de aangepaste opdrachten zowel oplossingen met één afronding als multi-turn oplossen met gedeeltelijke informatie.

In dat geval bewerkt u de voorbeeld zinnen voor het gebruik van de para meters zoals hieronder wordt voorgesteld.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> Gebruik accolades in de editor voor beelden om de para meters te raadplegen. - `turn {OnOff} the {SubjectDevice}`Tabblad gebruiken voor het automatisch volt ooien van de back-up door eerder gemaakte para meters.

## <a name="add-parameters-to-completion-rules"></a>Para meters toevoegen aan voltooiings regels

Wijzig de voltooiings regel die we in [de vorige Snelstartgids](./quickstart-custom-speech-commands-create-new.md)hebben gemaakt.

1. Voeg in de sectie **voor waarden** een nieuwe voor waarde toe door **+ een voor waarde toevoegen** te selecteren
1. Selecteer `Required parameters` in de nieuwe pop-up **Nieuw voor waarde**in de vervolg keuzelijst **type** . In de onderstaande check list moet u zowel `OnOff` als `SubjectDevice`controleren.
1. Klik op **Maken**.
1. Bewerk de bestaande actie voor het verzenden van een antwoord in de sectie **acties** door over de actie te bewegen en op het bewerkings pictogram te klikken. Laten we nu het gebruik van nieuwe `OnOff` en `SubjectDevice` para meters maken

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Beleid uitproberen
1. Selecteer `Train` het pictogram dat boven in het rechterdeel venster wordt weer gegeven.

1. Zodra de training is voltooid, selecteert `Test`u.
    - Er wordt een nieuw test venster voor **uw toepassing** weer gegeven.
    - Probeer enkele interacties.

        - Invoer: de TV uitschakelen
        - Uitvoer: OK, de TV uitschakelen        
        - Invoer: de televisie uitschakelen
        - Uitvoer: OK, de TV uitschakelen
        - Invoer: uitschakelen
        - Uitvoer: welk apparaat?
        - Invoer: de TV
        - Uitvoer: OK, de TV uitschakelen

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Snelstartgids: aangepaste opdrachten gebruiken met aangepaste spraak (preview-versie)](./quickstart-custom-speech-commands-select-custom-voice.md)
