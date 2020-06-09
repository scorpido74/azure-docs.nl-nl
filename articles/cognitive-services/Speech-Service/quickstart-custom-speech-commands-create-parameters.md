---
title: 'Quick Start: een preview-app voor aangepaste opdrachten maken met para meters-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel voegt u para meters toe aan een toepassing voor aangepaste opdrachten, zodat u meerdere apparaten kunt in-en uitschakelen.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: nitinme
ms.openlocfilehash: 940b3604487fbef7736c0d8f4f9299563b0b86d5
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509301"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>Quick Start: een preview-toepassing met para meters voor aangepaste opdrachten maken

In het [vorige artikel](./quickstart-custom-speech-commands-create-new.md)hebt u een eenvoudige toepassing voor aangepaste opdrachten gemaakt zonder para meters.

In dit artikel breidt u die toepassing uit met para meters zodat deze meerdere apparaten kan in-of uitschakelen.

## <a name="create-parameters"></a>Parameters maken

1. Open het project dat u in het [vorige artikel](./quickstart-custom-speech-commands-create-new.md)hebt gemaakt.

   We bewerken de bestaande opdracht, zodat deze kan worden gebruikt om meerdere apparaten in of uit te scha kelen.
1. Omdat de opdracht nu zowel in-als uit wordt verwerkt, wijzigt u de naam ervan in **TurnOnOff**.
   1. Selecteer de opdracht **TurnOn** in het linkerdeel venster en selecteer vervolgens de knop met het weglatings teken (**...**) naast **nieuwe opdracht** boven aan het deel venster.
   
   1. Selecteer **naam wijzigen**. Wijzig in het venster naam **wijzigen** de **naam** in **TurOnOff**. Selecteer **Opslaan**.

1. Maak een para meter om aan te geven of de gebruiker het apparaat wil omzetten in-of uitschakelen.
   1. Selecteer **toevoegen** boven aan het middelste deel venster. Selecteer in de vervolg keuzelijst de optie **para meter**.
   1. In het rechterdeel venster in het gedeelte **para meters** voegt u een waarde toe in het vak **naam** .
   1. Selecteer **vereist**. Selecteer in het venster **reactie voor een vereiste para meter toevoegen** de optie **eenvoudige editor**. Voer in het **eerste vak variatie** de volgende tekst in:
        ```
        On or Off?
        ```
   1. Selecteer **Bijwerken**.

       > [!div class="mx-imgBorder"]
       > ![Vereist parameter antwoord maken](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. Configureer de overige eigenschappen van de para meter als volgt:
       

    | Configuratie      | Voorgestelde waarde     | Beschrijving                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **Naam**               | **OnOff**           | Een beschrijvende naam voor de para meter.                                                                  |
    | **Is wereld wijd**          | Gewist       | Een selectie vakje dat aangeeft of een waarde voor de para meter globaal wordt toegepast op alle opdrachten in de toepassing.|
    | **Vereist**           | Geselecteerd         | Een selectie vakje dat aangeeft of een waarde voor de para meter is vereist.  |
    | **Antwoord voor vereiste para meter**      |**Eenvoudige editor-> in-of uitschakelen?**      | Een prompt om te vragen naar de waarde van de para meter wanneer deze niet bekend is. |
    | **Type**               | **Tekenreeks**          | Het type para meter. Bijvoorbeeld numeriek, teken reeks, datum en tijd, geografie.   |
    | **Configuratie**      | **Vooraf gedefinieerde invoer waarden accepteren van interne catalogus** | Voor teken reeksen beperkt deze instelling invoer tot een reeks mogelijke waarden. |
    | **Vooraf gedefinieerde invoer waarden**     | **aan**, **uit**             | Een reeks mogelijke waarden en aliassen.         |
       


    > [!div class="mx-imgBorder"]
    > ![Para meter maken](media/custom-speech-commands/create-on-off-parameter.png)

1. Selecteer **Opslaan** om de instellingen op te slaan.

 1. Selecteer opnieuw **toevoegen** om een tweede para meter toe te voegen. Deze para meter vertegenwoordigt de naam van het apparaat. Gebruik deze instellingen:
   

       | Instelling            | Voorgestelde waarde       | Beschrijving                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **Naam**               | **SubjectDevice**         | Een beschrijvende naam voor de para meter.                                                                     |
       | **Is wereld wijd**          | Gewist             | Een selectie vakje dat aangeeft of een waarde voor de para meter globaal wordt toegepast op alle opdrachten in de toepassing. |
       | **Vereist**           | Geselecteerd               | Een selectie vakje dat aangeeft of een waarde voor de para meter is vereist.          |
       | **Eenvoudige editor**      | **Welk apparaat?**    | Een prompt om te vragen naar de waarde van de para meter wanneer deze niet bekend is.                                       |
       | **Type**               | **Tekenreeks**                | Het type para meter. Bijvoorbeeld numeriek, teken reeks, datum en tijd, geografie.                                                |
       | **Configuratie**      | **Vooraf gedefinieerde invoer waarden accepteren van interne catalogus** | Voor teken reeksen beperkt deze instelling invoer tot een reeks mogelijke waarden.       |
       | **Vooraf gedefinieerde invoer waarden** | **TV**, **ventilator**               | Een reeks mogelijke waarden en aliassen.                               |
       | **Aliassen** (TV)      | **televisie**, **informatie**     | Optionele aliassen voor elk van de vooraf gedefinieerde invoer waarden.                                 |

## <a name="add-example-sentences"></a>Voorbeeld zinnen toevoegen

Voor opdrachten met para meters is het handig om voorbeeld zinnen toe te voegen die betrekking hebben op alle mogelijke combi Naties. Bijvoorbeeld:

- Volledige parameter informatie:`turn {OnOff} the {SubjectDevice}`
- Gedeeltelijke parameter informatie:`turn it {OnOff}`
- Geen parameter informatie:`turn something`

Met een voor beeld van zinnen met verschillende hoeveel heden gegevens kan de toepassing voor aangepaste opdrachten zowel oplossingen met één afronding als multi-turn-resoluties met gedeeltelijke informatie oplossen.

Bewerk in dat geval de voorbeeld zinnen om de para meters te gebruiken, zoals hier wordt voorgesteld:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> Gebruik accolades in de editor voor beelden om de para meters te raadplegen: `turn {OnOff} the {SubjectDevice}` .
>
> Tabblad gebruiken voor automatisch aanvullen gedefinieerd door eerder gemaakte para meters.

## <a name="add-parameters-to-completion-rules"></a>Para meters toevoegen aan voltooiings regels

Wijzig de voltooiings regel die u hebt gemaakt in de [vorige Snelstartgids](./quickstart-custom-speech-commands-create-new.md).

1. Selecteer **een voor waarde toevoegen**in de sectie **voor waarden** .
1. Selecteer in het venster **nieuwe voor waarde** in de lijst **type** de **vereiste para meters**. Selecteer in de lijst zowel **ONOFF** als **SubjectDevice**.
1. Selecteer **Maken**.
1. Bewerk de bestaande actie voor het verzenden van een **antwoord** in de sectie **acties** door over de actie te bewegen en de knop bewerken te selecteren. Gebruik deze keer de nieuwe `OnOff` `SubjectDevice` para meters en:

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Beleid uitproberen
1. Selecteer **trein** bovenaan in het rechterdeel venster.

1. Wanneer de training is voltooid, selecteert u **testen**.
    
    Er wordt een **test-app** -venster weer gegeven.

1. Probeer enkele interacties.

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Snelstartgids: aangepaste opdrachten gebruiken met aangepaste spraak (preview-versie)](./quickstart-custom-speech-commands-select-custom-voice.md)
