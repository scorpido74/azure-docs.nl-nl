---
title: 'Zelfstudie: Patroon rollen-LUIS'
titleSuffix: Azure Cognitive Services
description: Patronen halen gegevens op uit goed opgemaakte sjabloon uitingen. De sjabloon-utterance maakt gebruik van een enkele entiteit en rollen om verwante gegevens te extraheren, zoals de locatie van de oorsprong en die van het doel.
ms.custom: seodec18
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 7b95dcf6a93c9abdeab9520f0a0fd80eb17dccff
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387644"
---
# <a name="tutorial-extract-contextually-related-patterns-using-roles"></a>Zelfstudie: Context afhankelijke gerelateerde patronen extra heren met behulp van rollen

In deze zelfstudie gebruikt u een patroon om gegevens te extraheren uit een correct opgemaakte sjabloon-utterance. De sjabloon utterance maakt gebruik van een [eenvoudige entiteit](luis-concept-entity-types.md#simple-entity) en [rollen](luis-concept-roles.md) voor het extra heren van gerelateerde gegevens, zoals de bron locatie en de doel locatie.  Met patronen zijn er minder voorbeeld-utterances nodig voor de intentie.


**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Nieuwe entiteiten maken
> * Nieuwe intentie maken
> * Trainen
> * Publiceren
> * Intenties en entiteiten ophalen van eindpunt
> * Patroon maken met rollen
> * Frasenlijst van steden maken
> * Intenties en entiteiten ophalen van eindpunt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-roles-in-patterns"></a>Met behulp van rollen in patronen

Het doel van rollen is het extra heren van context afhankelijke gerelateerde entiteiten in een utterance. In de utterance `Move new employee Robert Williams from Sacramento and San Francisco` zijn de waarden voor de plaats van de oorsprong en de plaats van de bestemming aan elkaar gerelateerd en wordt algemene taal gebruikt om elke locatie op te geven. 


De naam van de nieuwe werknemer, Billy Patterson, maakt nog geen deel uit van de lijstentiteit **Werknemer**. De naam van de nieuwe werknemer wordt eerst geëxtraheerd om de naam te kunnen verzenden naar een extern systeem dat de bedrijfsreferenties maakt. Nadat de bedrijfsreferenties zijn gemaakt, worden de referenties van de werknemer toegevoegd aan de lijstentiteit **Werknemer**.

De nieuwe werknemer en zijn gezin moeten verhuizen van hun huidige woonplaats naar een plaats waar het fictieve bedrijf is gevestigd. Omdat een nieuwe medewerker uit willekeurig welke plaats kan komen, moeten de locaties worden gedetecteerd. Een ingestelde lijst, zoals een lijstentititeit, werkt in dit geval niet omdat alleen de plaatsen in de lijst zouden worden geëxtraheerd.

De rolnamen die zijn gekoppeld aan de plaats van oorsprong en de plaats van bestemming moeten uniek zijn binnen alle entiteiten. Een eenvoudige manier om ervoor te zorgen dat de rollen uniek zijn, is door ze met behulp van een naamgevingsstrategie binden aan de containerentiteit. De entiteit **NewEmployeeRelocation** is een eenvoudige entiteit met twee rollen: **NewEmployeeReloOrigin** en **NewEmployeeReloDestination**. Relo is de afkorting van relocation (verhuizing).

Omdat de voorbeeld-utterance `Move new employee Robert Williams from Sacramento and San Francisco` alleen door de computer aangeleerde entiteiten bevat, is het belangrijk om voldoende utterances voor de intentie te verstrekken, zodat de entiteiten worden gedetecteerd.  

**Hoewel u voor patronen minder voorbeeld-utterances hoeft op te geven, komt het patroon niet overeen als de entiteiten niet worden gedetecteerd.**

Als u problemen ondervindt met de detectie van een enkele entiteit omdat het een naam van bijvoorbeeld een stad is, kunt u overwegen om een frasenlijst met vergelijkbare waarden toe te voegen. Dit bevordert de detectie van de plaatsnaam door LUIS (Language Understanding-API) een extra signaal te geven over dat type woord of die frasen. Frasen zijn alleen bevorderlijk voor het patroon omdat ze helpen met de entiteitsdetectie, die nodig is om een overeenkomstig patroon te vinden. 

## <a name="import-example-app"></a>Voorbeeld-app importeren
Ga door met de in de laatste zelfstudie gemaakt app, **Human Resources**. 

Voer de volgende stappen uit:

1.  Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json) en sla het op.

2. Importeer de JSON in een nieuwe app.

3. Ga naar het gedeelte **Beheren**, open het tabblad **Versies**, kloon de versie en noem deze `roles`. Klonen is een uitstekende manier om te experimenteren met verschillende functies van LUIS zonder dat de oorspronkelijke versie wordt gewijzigd. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

## <a name="create-new-entities"></a>Nieuwe entiteiten maken

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecteer **Entities** in de linkernavigatie. 

3. Selecteer **Create new intent**.

4. Voer in het pop-upvenster `NewEmployee` in als een **Enkele** entiteit.

5. Selecteer **Create new intent**.

6. Voer in het pop-upvenster `NewEmployeeRelocation` in als een **Enkele** entiteit.

7. Selecteer **NewEmployeeRelocation** in de lijst met entiteiten. 

8. Voer `NewEmployeeReloOrigin` in als de eerste rol en selecteer Enter.

9. Voer `NewEmployeeReloDestination` in als de tweede rol en selecteer Enter.

## <a name="create-new-intent"></a>Nieuwe intentie maken
Het labelen van de entiteiten in deze stappen is misschien eenvoudiger als u de vooraf gedefinieerde keyPhrase-entiteit verwijdert voordat u begint en vervolgens weer toevoegt wanneer u klaar bent met de stappen in deze sectie. 

1. Selecteer **Intents** in de linkernavigatie.

2. Selecteer **Create new intent**. 

3. Voer in het pop-updialoogvenster `NewEmployeeRelocationProcess` in als de naam van de intentie.

4. Voer de volgende voorbeeld-utterances in om de nieuwe entiteiten te labelen. De entiteits- en rolwaarden worden vet weergegeven. Denk eraan dat u naar de **tokenweergave** kunt schakelen als u het gemakkelijker vindt om tekst te labelen. 

    U geeft tijdens het labelen van de intentie niet de rol van de entiteit op. Dat doet u later bij het maken van het patroon. 

    |Utterance|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Verhuis **Bob Jones** van **Seattle** naar **Los Colinas**|Bob Jones|Seattle, Los Colinas|
    |Verhuis **Dave C. Cooper** van **Redmond** naar **New York City**|Dave C. Cooper|Redmond, New York City|
    |Verhuis **Jim Paul Smith** van **Toronto** naar **West Vancouver**|Jim Paul Smith|Toronto, West Vancouver|
    |Verhuis **J. Benson** van **Boston** naar **Staines-upon-Thames**|J. Benson|Boston, Staines-upon-Thames|
    |Verhuis **Travis "Trav" Hinton** van **Castelo Branco** naar **Orlando**|Travis "Trav" Hinton|Castelo Branco, Orlando|
    |Verhuis **Trevor Nottington III** van **Aranda de Duero** naar **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |Verhuis **Dr. Greg Williams** van **Orlando** naar **Ellicott City**|Dr. Greg Williams|Orlando, Ellicott City|
    |Verhuis **Robert "Bobby" Gregson** van **Kansas City** naar **San Juan Capistrano**|Robert "Bobby" Gregson|Kansas City, San Juan Capistrano|
    |Verhuis **Patti Owens** van **Bellevue** naar **Rockford**|Patti Owens|Bellevue, Rockford|
    |Verhuis **Janet Bartlet** van **Toscane** naar **Santa Fe**|Janet Bartlet|Toscane, Santa Fe|

    Het voorvoegsel, het aantal woorden, de syntaxis en het achtervoegsel van een werknemer kunnen verschillen. Dit is belangrijk voor LUIS om de variaties van de naam van een nieuwe werknemer te kunnen begrijpen. Het aantal woorden en de syntaxis van plaatsnamen kunnen ook verschillen. Deze variëteit is belangrijk om LUIS te leren hoe deze entiteiten kunnen voorkomen in de utterance van een gebruiker. 
    
    Als een van de entiteiten eenzelfde aantal woorden heeft, zonder variaties, moet u LUIS leren dat deze entiteit alleen dat aantal woorden heeft, zonder mogelijke variaties. LUIS is niet in staat om correct een bredere set van variaties te voorspellen, omdat deze nergens is weergegeven. 

    Als u de entiteit keyPhrase hebt verwijderd, voegt u deze nu weer toe aan de app.

## <a name="train"></a>Trainen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publiceren

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Ga naar het einde van de URL in het adres en voer `Move Wayne Berry from Miami to Mount Vernon` in. De laatste parameter van de queryreeks is `q`, de utterance **query**. 

    ```json
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

De intentievoorspellingsscore is slechts ongeveer 50%. Als uw clienttoepassing een hogere score vereist, moet dit worden hersteld. De entiteiten zijn eveneens niet voorspeld.

Een van de locaties is geëxtraheerd, maar de andere locatie niet. 

Patronen verbeteren de voorspellingsscore, maar entiteiten moeten correct worden voorspeld voordat het patroon overeenkomt met de utterance. 

## <a name="pattern-with-roles"></a>Patroon met rollen

1. Selecteer **Build** in de bovenste navigatie.

2. Selecteer **Patterns** in de linkernavigatie.

3. Selecteer **NewEmployeeRelocationProcess** in de vervolgkeuzelijst **Een intentie selecteren**. 

4. Voer het volgende patroon in: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Als u het eindpunt traint, publiceert en opvraagt, bent u mogelijk teleurgesteld wanneer u ziet dat er geen entiteiten worden gevonden. Het patroon kwam dus niet overeen en de voorspelling werd niet verbeterd. Dat komt omdat er onvoldoende voorbeeld-utterances zijn met gelabelde entiteiten. In plaats van meer voorbeelden toe te voegen, kunt u een frasenlijst toevoegen om dit probleem op te lossen.

## <a name="cities-phrase-list"></a>Frasenlijst van plaatsnamen
Plaatsnamen zijn net als persoonsnamen erg lastig omdat ze uit een combinatie van woorden en leestekens kunnen bestaan. De plaatsen uit de regio en de wereld zijn bekend, dus LUIS heeft een frasenlijst van plaatsnamen nodig om te beginnen met leren. 

1. Selecteer **Phrase list** in de sectie **Improve app performance** van het menu aan de linkerkant. 

2. Noem de lijst `Cities` en voeg de volgende `values` toe voor de lijst:

    |Waarden van frasenlijst|
    |--|
    |Seattle|
    |San Diego|
    |New York City|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Voeg niet elke plaats in de hele wereld en zelfs niet elke plaats in de regio toe. LUIS moet op basis van de lijst kunnen generaliseren wat een plaats is. Zorg dat **These values are interchangeable** (Deze waarden zijn onderling uitwisselbaar) blijft ingeschakeld. Deze instelling betekent dat de woorden in de lijst worden behandeld als synoniemen. 

3. Train en publiceer de app.

## <a name="get-intent-and-entities-from-endpoint"></a>Intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Ga naar het einde van de URL in het adres en voer `Move wayne berry from miami to mount vernon` in. De laatste parameter van de queryreeks is `q`, de utterance **query**. 

    ```json
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
   }
    ```

De intentiescore is nu veel hoger en de rolnamen maken deel uit van het antwoord van de entiteit.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn een entiteit met rollen en een intentie met voorbeeld-utterances toegevoegd. De eerste eindpuntvoorspelling met behulp van de entiteit heeft de intentie correct voorspeld, maar met een lage betrouwbaarheidsscore. Slechts een van de twee entiteiten is gedetecteerd. Vervolgens is in de zelfstudie een patroon toegevoegd dat de entiteitrollen heeft gebruikt, en is een frasenlijst toegevoegd om de waarde van de plaatsnamen in de utterances te verbeteren. De tweede eindpuntvoorspelling heeft een hoge betrouwbaarheidsscore geretourneerd en beide entiteitsrollen zijn gevonden. 

> [!div class="nextstepaction"]
> [Best practices voor LUIS-apps](luis-concept-best-practices.md)
