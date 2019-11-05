---
title: 'Zelf studie: bedoelingen voors pellen-Luis'
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
ms.openlocfilehash: 83ecf0767f2b21065c698421e3ad8f07f31d5b16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465283"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Zelf studie: LUIS-app bouwen om de bedoelingen van de gebruiker te bepalen

In deze zelfstudie maakt u een aangepaste HR-app (Human Resources) waarmee de intentie van een gebruiker wordt voorspeld op basis van de utterance (tekst). 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

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
|`ApplyForJob`|Bepaal of de gebruiker solliciteert naar een functie.|
|`GetJobInformation`|Bepaal of de gebruiker op zoek is naar informatie over vacatures in het algemeen of over een specifieke vacature.|
|`None`|Bepaal of de gebruiker een vraag stelt die niet met de app mag worden beantwoord. Deze intent is geleverd als onderdeel van het maken van de app en kan niet worden verwijderd. |

## <a name="create-a-new-app"></a>Een nieuwe app maken

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Intent maken voor informatie over vacature.

1. Selecteer **Create new intent**. Voer de naam `GetJobInformation` in voor de nieuwe intent. Deze intent wordt voorspeld wanneer een gebruiker informatie wil over openstaande vacatures in het bedrijf. 

    ![Scherm opname van Language Understanding (LUIS) nieuw intentie dialoog venster](media/luis-quickstart-intents-only/create-intent.png "Scherm opname van Language Understanding (LUIS) nieuw intentie dialoog venster")

1. Selecteer **Done**.

2. Voeg verschillende voorbeelden van utterances toe aan deze intent waarnaar een gebruiker waarschijnlijk zal vragen:

    | Voorbeelden van utterances|
    |--|
    |`Any new jobs posted today?`|
    |`Are there any new positions in the Seattle office?`|
    |`Are there any remote worker or telecommute jobs open for engineers?`|
    |`Is there any work with databases?`|
    |`I'm looking for a co-working situation in the tampa office.`|
    |`Is there an internship in the san francisco office?`|
    |`Is there any part-time work for people in college?`|
    |`Looking for a new situation with responsibilities in accounting`|
    |`Looking for a job in new york city for bilingual speakers.`|
    |`Looking for a new situation with responsibilities in accounting.`|
    |`New jobs?`|
    |`Show me all the jobs for engineers that were added in the last 2 days.`|
    |`Today's job postings?`|
    |`What accounting positions are open in the london office?`|
    |`What positions are available for Senior Engineers?`|
    |`Where is the job listings`|

    [![Scherm opname van het invoeren van nieuwe uitingen voor de opslag intentie](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Scherm opname van het invoeren van nieuwe uitingen voor de opslag intentie")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

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
    |`Fill out application for Job 123456`|
    |`Here is my c.v. for position 654234`|
    |`Here is my resume for the part-time receptionist post.`|
    |`I'm applying for the art desk job with this paperwork.`|
    |`I'm applying for the summer college internship in Research and Development in San Diego`|
    |`I'm requesting to submit my resume to the temporary position in the cafeteria.`|
    |`I'm submitting my resume for the new Autocar team in Columbus, OH`|
    |`I want to apply for the new accounting job`|
    |`Job 456789 accounting internship paperwork is here`|
    |`Job 567890 and my paperwork`|
    |`My papers for the tulsa accounting internship are attached.`|
    |`My paperwork for the holiday delivery position`|
    |`Please send my resume for the new accounting job in seattle`|
    |`Submit resume for engineering position`|
    |`This is my c.v. for post 234123 in Tampa.`|


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
