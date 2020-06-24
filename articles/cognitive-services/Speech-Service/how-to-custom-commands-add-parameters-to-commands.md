---
title: 'Procedure: eenvoudige opdrachten toevoegen aan aangepaste opdrachten toepassings spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u para meters kunt toevoegen aan aangepaste opdrachten
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 1d74bf089f3e5bc1fd04232b58ce95c649a170e1
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307492"
---
# <a name="add-parameters-to-commands"></a>Para meters aan opdrachten toevoegen

In dit artikel leert u hoe u para meters kunt toevoegen aan aangepaste opdrachten. Para meters zijn de vereiste gegevens voor de opdrachten voor het volt ooien van een taak. In complexe scenario's kunnen para meters ook worden gebruikt om voor waarden te definiëren waarmee aangepaste acties worden geactiveerd.

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * [Procedure: een toepassing maken met eenvoudige opdrachten](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>Para meters voor de opdracht TurnOn configureren

Bewerk de bestaande **TurnOn** -opdracht om meerdere apparaten in te scha kelen en uit te scha kelen.

1. Omdat de opdracht nu zowel op als uit scenario wordt verwerkt, wijzigt u de naam van de opdracht in **TurnOnOff**.
   1. Selecteer de opdracht **TurnOn** in het linkerdeel venster en selecteer vervolgens de knop met het weglatings teken (...) naast **nieuwe opdracht** boven aan het deel venster.
   
   1. Selecteer **naam wijzigen**. Wijzig in het venster naam wijzigen de **optie** **naam** in **TurnOnOff**.

1. Vervolgens voegt u een nieuwe para meter toe aan deze opdracht die aangeeft of de gebruiker het apparaat wil in-of uitschakelen.
   1. Selecteer boven in het middelste deel venster de optie **toevoegen** . Selecteer in de vervolg keuzelijst de **para meter**.
   1. In het rechterdeel venster, in het gedeelte **para meters** , voegt u waarde in het vak **naam** toe als **ONOFF**.
   1. Selecteer **vereist**. Selecteer in het venster **reactie voor een vereiste para meter toevoegen** de optie **eenvoudige editor**. In de **eerste variant**toevoegen
        ```
        On or Off?
        ```
   1. Selecteer **Update**.

       > [!div class="mx-imgBorder"]
       > ![Vereist parameter antwoord maken](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Nu gaan we de eigenschappen van de para meters configureren. Ga naar [verwijzingen](./custom-commands-references.md)voor uitleg van alle configuratie-eigenschappen van een opdracht. Configureer de overige eigenschappen van de para meter als volgt:
      

       | Configuratie      | Voorgestelde waarde     | Beschrijving                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Naam               | `OnOff`           | Een beschrijvende naam voor de para meter                                                                           |
       | Is wereld wijd          | uitgeschakeld       | Selectie vakje dat aangeeft of een waarde voor deze para meter globaal wordt toegepast op alle opdrachten in de toepassing|
       | Vereist           | wel         | Selectie vakje dat aangeeft of een waarde voor deze para meter vereist is voordat de opdracht wordt voltooid |
       | Antwoord voor vereiste para meter      |Eenvoudige editor >`On or Off?`      | Een prompt om te vragen naar de waarde van deze para meter als deze niet bekend is |
       | Type               | Tekenreeks          | Het type para meter, zoals getal, teken reeks, datum/tijd of geografie   |
       | Configuratie      | Vooraf gedefinieerde invoer waarden accepteren van interne catalogus | Voor teken reeksen beperkt de invoer tot een reeks mogelijke waarden |
       | Vooraf gedefinieerde invoer waarden     | `on`, `off`           | Set mogelijke waarden en hun aliassen         |
       
        > [!div class="mx-imgBorder"]
        > ![Para meter maken](media/custom-commands/create-on-off-parameter.png)

   1. Selecteer **Opslaan** om alle configuraties van de para meter op te slaan.
 
 ### <a name="add-subjectdevice-parameter"></a>De para meter SubjectDevice toevoegen 

   1. Selecteer vervolgens opnieuw **toevoegen** om een tweede para meter toe te voegen om de naam weer te geven van de apparaten die kunnen worden beheerd met behulp van deze opdracht. Gebruik de volgende configuratie.
   

       | Instelling            | Voorgestelde waarde       |
       | ------------------ | --------------------- |
       | Name               | `SubjectDevice`         |
       | Is wereld wijd          | uitgeschakeld             |
       | Vereist           | wel               |
       | Antwoord voor vereiste para meter     | Eenvoudige editor >`Which device do you want to control?`    | 
       | Type               | Tekenreeks                |          |
       | Configuratie      | Vooraf gedefinieerde invoer waarden accepteren van interne catalogus | 
       | Vooraf gedefinieerde invoer waarden | `tv`, `fan`               |
       | Aliassen ( `tv` )      | `television`, `telly`     |

   1. Selecteer **Opslaan**

### <a name="modify-example-sentences"></a>Voorbeeld zinnen wijzigen

Voor opdrachten met para meters is het handig om voorbeeld zinnen toe te voegen die betrekking hebben op alle mogelijke combi Naties. Bijvoorbeeld:

* Volledige parameter informatie-`turn {OnOff} the {SubjectDevice}`
* Gedeeltelijke parameter informatie-`turn it {OnOff}`
* Geen parameter informatie-`turn something`

Met een voor beeld van zinnen met een andere mate van informatie kan de toepassing van de aangepaste opdrachten zowel oplossingen met één afronding als multi-turn oplossen met gedeeltelijke informatie.

Bewerk in dat geval de voorbeeld zinnen voor het gebruik van de para meters zoals hieronder wordt voorgesteld:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Selecteer **Opslaan**.

> [!TIP]
> Gebruik accolades in de editor voor beelden om de para meters te raadplegen. - `turn {OnOff} the {SubjectDevice}`Tabblad gebruiken voor het automatisch volt ooien van de back-up door eerder gemaakte para meters.

### <a name="modify-completion-rules-to-include-parameters"></a>Voltooiings regels voor het toevoegen van para meters wijzigen

Wijzig de bestaande **ConfirmationResponse**van de voltooiings regel.

1. Selecteer **een voor waarde toevoegen**in de sectie **voor waarden** .
1. Selecteer in het venster **nieuwe voor waarde** in de lijst **type** de **vereiste para meters**. Controleer in de onderstaande check list zowel **ONOFF** als **SubjectDevice**.
1. Selecteer **Maken**.
1. Bewerk de bestaande actie voor het verzenden van een **antwoord** in de sectie **acties** door over de actie te bewegen en de knop bewerken te selecteren. Gebruik de zojuist gemaakte para meters **ONOFF** en **SubjectDevice** om deze tijd te gebruiken

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Selecteer **Opslaan**.

### <a name="try-it-out"></a>Uitproberen
1. Selecteer een **trein** pictogram boven in het rechterdeel venster.

1. Wanneer de training is voltooid, selecteert u **testen**. Er wordt een **test-app** -venster weer gegeven.
 Probeer enkele interacties.

    - Invoer: de TV uitschakelen
    - Uitvoer: OK, de TV uitschakelen
    - Invoer: de televisie uitschakelen
    - Uitvoer: OK, de TV uitschakelen
    - Invoer: uitschakelen
    - Uitvoer: welk apparaat wilt u beheren?
    - Invoer: de TV
    - Uitvoer: OK, de TV uitschakelen

## <a name="configure-parameters-for-settemperature-command"></a>Para meters voor de opdracht SetTemperature configureren

Wijzig de **SetTemperature** -opdracht om deze in te scha kelen, zodat de Tempe ratuur kan worden ingesteld door de gebruiker.

Nieuwe parameter **temperatuur** toevoegen met de volgende configuratie

| Configuratie      | Voorgestelde waarde     |
| ------------------ | ----------------|
| Name               | `Temperature`           |
| Vereist           | wel         |
| Antwoord voor vereiste para meter      | Eenvoudige editor >`What temperature would you like?`
| Type               | Getal          |


Bewerk het voor beeld uitingen in de volgende waarden.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Bewerk de bestaande voltooiings regels volgens de volgende configuratie.

| Configuratie      | Voorgestelde waarde     |
| ------------------ | ----------------|
| Voorwaarden         | Vereiste para meter > temperatuur           |
| Acties           | > spraak antwoord verzenden`Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>Uitproberen

**Train** en **test** de wijzigingen met enkele interacties.

- Invoer: Tempe ratuur instellen
- Output: welke Tempe ratuur wilt u graag?
- Invoer: 50 graden
- Uitvoer: OK, Tempe ratuur instellen op 50 graden

## <a name="configure-parameters-to-the-setalarm-command"></a>Para meters configureren voor de SetAlarm-opdracht

Voeg een para meter met de naam **DateTime** toe met de volgende configuratie.

   | Instelling                           | Voorgestelde waarde                     | 
   | --------------------------------- | ----------------------------------------|
   | Name                              | `DateTime`                               |
   | Vereist                          | wel                                 |
   | Antwoord voor vereiste para meter   | Eenvoudige editor >`For what time?`            | 
   | Type                              | DateTime                                |
   | Standaard waarden                     | Als datum ontbreekt, gebruikt u vandaag            |
   | Standaard waarden voor tijd                     | Gebruik begin van dag als tijd ontbreekt     |


> [!NOTE]
> In dit artikel hebben we voornamelijk gebruik gemaakt van teken reeks-, getal-en datum parameter typen. Ga naar [verwijzingen](./custom-commands-references.md)voor een lijst met alle ondersteunde parameter typen en de bijbehorende eigenschappen.


Bewerk voor beeld uitingen naar de volgende waarden.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Bewerk de bestaande voltooiings regels volgens de volgende configuratie.

   | Instelling    | Voorgestelde waarde                               |
   | ---------- | ------------------------------------------------------- |
   | Acties    | Spraak antwoord verzenden-`Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>Uitproberen

**Train** en **test** de wijzigingen.
- Invoer: alarm instellen voor morgen om 12:00 uur
- Uitvoer: OK, alarm ingesteld voor 2020-05-02 12:00:00
- Invoer: een alarm instellen
- Uitvoer: welk tijdstip?
- Invoer: tot 17:00 uur
- Uitvoer: OK, alarm ingesteld voor 2020-05-01 17:00:00


## <a name="try-out-all-the-commands"></a>Alle opdrachten uitproberen

U kunt alle drie de opdrachten tegelijk testen met uitingen die betrekking hebben op verschillende opdrachten. Houd er rekening mee dat u kunt scha kelen tussen de verschillende opdrachten.

- Invoer: een alarm instellen
- Uitvoer: voor welk tijdstip?
- Invoer: de TV inschakelen
- Uitvoer: OK, TV in-of uitschakelen
- Invoer: een alarm instellen
- Uitvoer: voor welk tijdstip?
- Invoer: tot 17:00 uur
- Uitvoer: OK, alarm ingesteld voor 2020-05-01 17:00:00

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: configuraties toevoegen aan opdracht parameters](./how-to-custom-commands-add-parameter-configuration.md)
