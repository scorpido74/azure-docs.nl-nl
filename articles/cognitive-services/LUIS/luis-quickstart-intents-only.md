---
title: 'Zelfstudie: Voor spel bedoelingen-LUIS'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maakt u een aangepaste app waarmee de intentie van een gebruiker wordt voorspeld. Deze app is het eenvoudigste type LUIS-app omdat de app geen verschillende gegevenselementen uit de utterancetekst extraheert, zoals e-mailadressen of datums.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 7139876f64841a877e688ec6faf03597c527d1f2
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375824"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Zelfstudie: LUIS-app bouwen om gebruikersintenties te bepalen

In deze zelfstudie maakt u een aangepaste HR-app (Human Resources) waarmee de intentie van een gebruiker wordt voorspeld op basis van de utterance (tekst). 

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een nieuwe app maken 
> * Intents maken
> * Voorbeelden van utterances toevoegen
> * App trainen
> * App publiceren
> * Intent van eindpunt ophalen


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>Gebruikersintenties als intents

Het doel van de app is om de intentie van natuurlijke omgangstaal in een tekst te bepalen: 

`Are there any new positions in the Seattle office?`

Deze intenties worden onderverdeeld in **Intents**. 

Deze app heeft enkele intents. 

|Intentie|Doel|
|--|--|
|ApplyForJob|Bepaal of de gebruiker solliciteert naar een functie.|
|GetJobInformation|Bepaal of de gebruiker op zoek is naar informatie over vacatures in het algemeen of over een specifieke vacature.|
|Geen|Bepaal of de gebruiker een vraag stelt die niet met de app mag worden beantwoord. Deze intent is geleverd als onderdeel van het maken van de app en kan niet worden verwijderd. |

## <a name="create-a-new-app"></a>Een nieuwe app maken

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Intent maken voor informatie over vacature.

1. Selecteer **Create new intent**. Voer de naam `GetJobInformation` in voor de nieuwe intent. Deze intent wordt voorspeld wanneer een gebruiker informatie wil over openstaande vacatures in het bedrijf. 

    ![Schermafbeelding van het dialoogvenster Nieuwe intentie in Language Understanding (LUIS)](media/luis-quickstart-intents-only/create-intent.png "Schermafbeelding van het dialoogvenster Nieuwe intentie in Language Understanding (LUIS)")

1. Selecteer **Done**.

2. Voeg verschillende voorbeelden van utterances toe aan deze intent waarnaar een gebruiker waarschijnlijk zal vragen:

    | Voorbeelden van utterances|
    |--|
    |Zijn er vandaag nieuwe vacatures geplaatst?|
    |Zijn er nieuwe vacatures in het kantoor in Seattle?|
    |Zijn er openstaande vacatures voor externe werknemers of telewerkers beschikbaar voor technici?|
    |Zijn er banen beschikbaar voor het werken met databases?|
    |Ik ben op zoek naar een co-workingvacature voor het kantoor in Tampa.|
    |Is er een stageplaats beschikbaar voor het kantoor in San Francisco?|
    |Is er parttimewerk beschikbaar voor mensen die nog studeren?|
    |Op zoek naar een nieuwe werkomgeving met verantwoordelijkheden als boekhouder|
    |Op zoek naar een vacature in New York voor tweetalige werknemers.|
    |Op zoek naar een nieuwe werkomgeving met verantwoordelijkheden als boekhouder.|
    |Nieuwe vacatures?|
    |Geef alle vacatures voor technici weer die de afgelopen 2 dagen zijn toegevoegd.|
    |Vandaag geplaatste vacatures?|
    |Welke openstaande vacatures voor boekhouder zijn er voor het kantoor in Londen?|
    |Welke vacatures bestaan er voor hoofdtechnici?|
    |Waar is de vacaturelijst|

    [![Schermafbeelding van het invoeren van nieuwe uitingen voor MyStore-intentie](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Schermafbeelding van het invoeren van nieuwe uitingen voor MyStore-intentie")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Door _voorbeelden van utterances_ op te geven traint u LUIS in het soort utterances dat voor deze intent kan worden voorspeld. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Voorbeelduitingen toevoegen aan de intentie None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>De app trainen voordat u gaat testen of publiceren

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>De app publiceren om query's uit te voeren vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Intentvoorspelling ophalen vanaf het eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Ga naar het einde van de URL in de adresbalk en voer `I'm looking for a job with Natural Language Processing` in. De laatste parameter van de queryreeks is `q`, de utterance**query**. Deze utterance is niet hetzelfde als een van de voorbeeldutterances. Dit is een goede test die de `GetJobInformation`-intent als de best scorende intent moet retourneren. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    De parameter `verbose=true` voor de queryreeks betekent dat **alle intents** moeten worden opgenomen in de queryresultaten van de app. De entiteitenmatrix is leeg omdat deze app momenteel geen entiteiten bevat. 

    Het JSON-resultaat identificeert de hoogst scorende intent als **`topScoringIntent`** -eigenschap. Alle scores liggen tussen 1 en 0, met de betere score dichterbij 1. 

## <a name="create-intent-for-job-applications"></a>Intent maken voor vacatures

Ga terug naar de LUIS-portal en maak een nieuwe intent om te bepalen of de gebruikersutterance over het solliciteren naar een functie gaat.

1. Selecteer **Build** in het menu rechtsboven om terug te keren naar het compileren van apps.

1. Selecteer in het linkermenu **intents** om de lijst met intents op te halen.

1. Selecteer **Create new intent** en voer de naam `ApplyForJob` in. 

    ![LUIS-dialoogvenster om nieuwe intent te maken](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Voeg verschillende utterances toe aan deze intent waarnaar een gebruiker waarschijnlijk zal vragen, zoals:

    | Voorbeelden van utterances|
    |--|
    |Sollicitatieformulier invullen voor vacature 123456|
    |Hier is mijn cv voor vacature 654234|
    |Hier is mijn cv voor de parttimefunctie van receptionist.|
    |Met dit papierwerk solliciteer ik naar de functie van bureaumedewerker op de kunstafdeling.|
    |Ik solliciteer naar de stageplaats bij de zomercursussen op de afdeling Onderzoek en ontwikkeling in San Diego|
    |Ik wil mijn cv graag indienen voor de tijdelijke functie in de kantine.|
    |Ik dien mijn cv in voor het nieuwe Autocar-team in Columbus, Ohio|
    |Ik wil solliciteren op de nieuwe functie van boekhouder|
    |Dit is het papierwerk voor vacature 456789 voor de stageplaats van boekhouder|
    |Vacature 567890 en mijn papierwerk|
    |Mijn papieren voor de stageplaats van boekhouder in Tulsa zijn bijgevoegd.|
    |Mijn papierwerk voor de functie van bezorger tijdens de feestdagen|
    |Kunt u mijn cv indienen voor de nieuwe functie van boekhouder in Seattle|
    |Cv indienen voor vacature van engineer|
    |Dit is mijn cv voor vacature 234123 in Tampa?|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

## <a name="train-again"></a>Opnieuw trainen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Opnieuw publiceren

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Opnieuw intentvoorspelling ophalen

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Voer in het nieuwe browservenster `Can I submit my resume for job 235986` in aan het einde van de URL. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    De resultaten bevatten de nieuwe intent **ApplyForJob** samen met de bestaande intents. 

## <a name="client-application-next-steps"></a>Volgende stappen voor clienttoepassing

Nadat LUIS de JSON-reactie heeft geretourneerd, is LUIS klaar met deze aanvraag. LUIS geeft geen antwoord op deze utterances van gebruikers, maar stelt alleen vast om wat voor soort informatie er wordt gevraagd in natuurlijke taal. De gespreksopvolging wordt uitgevoerd met de clienttoepassing, bijvoorbeeld een Azure-bot. 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Typen entiteiten](luis-concept-entity-types.md)
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de app Human Resources (HR) gemaakt, hebt u 2 intents gemaakt, voorbeeldutterances aan elke intent toegevoegd, voorbeeldutterances aan de None-intent toegevoegd, de app getraind, gepubliceerd en getest op het eindpunt. Dit zijn de basisstappen voor het compileren van een LUIS-model. 

Ga door met deze app, [waarbij u een eenvoudige entiteit en lijst met woordgroepen toevoegt](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Vooraf gemaakte intents en entiteiten aan deze app toevoegen](luis-tutorial-prebuilt-intents-entities.md)
