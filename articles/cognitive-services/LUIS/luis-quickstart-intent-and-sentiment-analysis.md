---
title: 'Zelfstudie: Sentiment-analyse-LUIS'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maakt u een app die laat zien hoe u positieve, negatieve en neutrale gevoelens kunt ophalen uit uitingen. Het gevoel wordt bepaald op basis van de gehele uiting.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: ed403e3d761b32c6837eb8e72edef3f3e6380217
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307576"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Zelfstudie:  Het gevoel achter een uiting ophalen

In deze zelfstudie maakt u een app die laat zien hoe u positieve, negatieve en neutrale gevoelens van uitingen kunt bepalen. Het gevoel wordt bepaald op basis van de gehele uiting.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Een nieuwe app maken
> * Sentimentanalyse toevoegen als publicatie-instelling
> * App trainen
> * App publiceren
> * Het gevoel achter een uiting ophalen van een eindpunt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Sentimentanalyse is een publicatie-instelling

De volgende utterances zijn voorbeelden van gevoelens:

|Sentiment|Score|Utterance|
|:--|:--|:--|
|positief|0,91 |John W. Smith heeft een geweldige presentatie gegeven in Parijs.|
|positief|0,84 |De engineers in Seattle hebben fantastisch werk gedaan om Parker als klant binnen te halen.|

Sentimentanalyse is een publicatie-instelling die voor elke uiting geldt. U hoeft niet de woorden te vinden die gevoel aangeven in een uiting en deze te labelen. 

Omdat dit een publicatie-instelling is, wordt deze niet weergegeven op de intentie- of entiteitenpagina. De instelling is te zien in het deelvenster [Interactieve test](luis-interactive-test.md#view-sentiment-results) of bij het uitvoeren van tests bij de eindpunt-URL. 


## <a name="create-a-new-app"></a>Een nieuwe app maken

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Vooraf gemaakte entiteit PersonName toevoegen 

1. Selecteer **Build** in het navigatie menu.

1. Selecteer **Entities** in het navigatiemenu aan de linkerkant.

1. Selecteer de knop **Add prebuilt entity**.

1. Selecteer de volgende entiteit in de lijst met vooraf gedefinieerde entiteiten en selecteer **Gereed**:

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Schermopname van het selecteren van een nummer in het dialoogvenster met de vooraf gedefinieerde entiteiten](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Een intentie maken om werknemersfeedback te bepalen

Voeg een nieuwe intentie toe om feedback van werknemers die tot het bedrijf behoren, vast te leggen. 

1. Selecteer **Intents** in het linkerpaneel.

1. Selecteer **Create new intent**.

1. Geef de nieuwe intentie de naam  `EmployeeFeedback`.

    ![Een nieuw dialoogvenster maken met de naam EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Voeg een aantal utterances toe die aanduiden op welk terrein een werknemer het goed doet of waar er ruimte is voor verbetering:

    |Utterances|
    |--|
    |John Smith heeft het goed gedaan toen hij een collega welkom heette die terugkwam van zwangerschapsverlof|
    |Jill Jones heeft het geweldig gedaan toen ze een collega tijdens een emotioneel moeilijke tijd troost bood.|
    |Bob Barnes had niet alle facturen die nodig waren voor de administratieve procedures.|
    |Todd Thomas leverde de benodigde formulieren een maand te laat en zonder handtekeningen aan|
    |Katherine Kelly was niet aanwezig op de belangrijke marketingbijeenkomst buiten de deur.|
    |Denise Dillard was niet aanwezig op het beoordelingsgesprek in juni.|
    |Mark Mathews deed het fantastisch tijdens zijn verkooppraatje voor Harvard|
    |Walter Williams heeft een geweldige presentatie gegeven voor Stanford|

    Selecteer de **weergave opties**, selecteer **entiteits waarden weer geven** om de namen weer te geven.

    [![Schermopname van de LUIS-app met voorbeelden van uitingen in de intentie EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Voorbeelduitingen toevoegen aan de intentie None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>De app trainen zodat de wijzigingen aan de intentie kunnen worden getest 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>App configureren voor gebruik van sentimentanalyse

1. Selecteer **Beheren** in het navigatievenster rechtsboven en selecteer in het menu links de optie **Publicatie-instellingen**.

1. Selecteer **sentiment analyse gebruiken om te bepalen of de utterance van een gebruiker positief, negatief of neutraal is.** om deze instelling in te scha kelen. 

    ![Sentimentanalyse als publicatie-instelling inschakelen](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>De app publiceren zodat op het getrainde model query's kunnen worden uitgevoerd vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Het gevoel van een uiting ophalen van het eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Ga naar het einde van de URL in het adres en voer de volgende utterance in:

    `Jill Jones work with the media team on the public portal was amazing` 

    De laatste parameter van de queryreeks is `q`, de utterance **query**. Deze utterance is niet hetzelfde als een van de gelabelde utterances en dit is dus een goede test die de intentie `EmployeeFeedback` als resultaat moet geven met de sentimentanalyse geëxtraheerd.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    SentimentAnalysis is positief met een score van 86%. 

    Probeer een andere utterance door de waarde voor `q` te verwijderen in de adres balk van de browser: `William Jones did a terrible job presenting his ideas.`De sentiment-score geeft een negatieve sentiment aan door een lage `0.18597582`score te retour neren.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* Sentimentanalyse wordt geleverd door de Cognitive Service [Text Analytics](../Text-Analytics/index.yml). De functie is beperkt tot de door Text Analytics [ondersteunde talen](luis-language-support.md##languages-supported).
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie voegt u Sentimentanalyse toe als publicatie-instelling om de gevoelswaarden uit gehele uitingen op te halen.

> [!div class="nextstepaction"] 
> [Eindpuntuitingen beoordelen in de HR-app](luis-tutorial-review-endpoint-utterances.md) 

