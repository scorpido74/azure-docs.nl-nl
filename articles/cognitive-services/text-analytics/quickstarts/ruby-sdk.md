---
title: 'Snelstart: Text Analytics-clientbibliotheek voor Ruby | Microsoft Documenten'
titleSuffix: Azure Cognitive Services
description: Detecteer in deze quickstart taal met behulp van de Ruby Text Analytics-clientbibliotheek van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0d4d32a413dd22c55f1b2f01dce3a3df81f5f729
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77919665"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Snelstart: gebruik de text analytics-clientbibliotheek voor Ruby

Ga aan de slag met de text analytics-clientbibliotheek. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit.

Gebruik de clientbibliotheek Text Analytics om uit te voeren:

* Sentimentanalyse
* Taaldetectie
* Herkenning van entiteiten
* Sleuteltermextractie

> [!NOTE]
> Deze quickstart is alleen van toepassing op Text Analytics versie 2.1. Momenteel is een v3-clientbibliotheek voor Ruby niet beschikbaar.

[Referentiedocumentatie](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [(RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [Ruby](https://www.ruby-lang.org/)
* Zodra u uw Azure-abonnement <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="hebt,"  target="_blank">maakt u <span class="docon docon-navigate-external x-hidden-focus"></span> </a> een Text Analytics-bron en maakt u een Text Analytics-bron in de Azure-portal om uw sleutel en eindpunt op te halen. 
    * U hebt de sleutel en het eindpunt van de resource die u maakt nodig om uw toepassing te verbinden met de Text Analytics API. Je doet dit later in de snelle start.
    * U de gratis prijscategorie gebruiken om de service uit te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-ruby-application"></a>Een nieuwe Ruby-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor uw app en navigeer ernaar. Maak vervolgens een `GemFile`bestand met de naam en een Ruby-bestand voor uw code.

```console
mkdir myapp && cd myapp
```

Voeg `GemFile`in uw, voeg de volgende regels toe om de clientbibliotheek toe te voegen als een afhankelijkheid.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

Importeer in uw Ruby-bestand de volgende pakketten.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Maak variabelen voor het Azure-eindpunt en de sleutel van uw resource. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Objectmodel 

De Text Analytics-client verifieert naar Azure met behulp van uw sleutel. De client biedt verschillende methoden voor het analyseren van tekst, als één tekenreeks of als batch. 

Tekst wordt naar de API `documents`verzonden `dictionary` als een lijst `id`van `text`, `language` die objecten zijn die een combinatie van , en kenmerken bevatten, afhankelijk van de gebruikte methode. Het `text` kenmerk slaat de tekst op `language`die moet `id` worden geanalyseerd in de oorsprong en deze kan elke waarde zijn. 

Het antwoordobject is een lijst met de analysegegevens voor elk document. 

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de Text Analytics-clientbibliotheek voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis)
* [Taaldetectie](#language-detection)
* [Herkenning van entiteiten](#entity-recognition)
* [Trefwoordextractie](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

Een klasse `TextAnalyticsClient`maken met de naam . 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

Maak in deze klasse `initialize` een functie die wordt aangeroepen om de client te verifiëren met behulp van uw sleutel en eindpunt. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Buiten de klasse gebruikt u `new()` de functie van de client om deze te instantiëren.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Sentimentanalyse

Maak in het clientobject `AnalyzeSentiment()` een functie die wordt aangeroepen met een lijst met invoerdocumenten die later worden gemaakt. Bel de functie `sentiment()` van de klant en krijg het resultaat. Vervolgens herhalen door de resultaten, en print elk document ID, en sentiment score. Een score dichter bij 0 duidt op een negatief sentiment, terwijl een score dichter bij 1 wijst op een positief sentiment.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Maak buiten de clientfunctie een `SentimentAnalysisExample()` nieuwe functie `TextAnalyticsClient` die wordt aangeroepen om het eerder gemaakte object te maken. Maak een `MultiLanguageInput` lijst met objecten met de documenten die u wilt analyseren. Elk object bevat `id` `Language` een `text` , en een attribuut. Het `text` kenmerk slaat de te `language` analyseren tekst op, is `id` de taal van het document en het kan elke waarde zijn. Bel dan de `AnalyzeSentiment()` functie van de klant.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Roep de functie `SentimentAnalysisExample()` aan.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Uitvoer

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Taaldetectie

Maak in het clientobject `DetectLanguage()` een functie die wordt aangeroepen met een lijst met invoerdocumenten die later worden gemaakt. Bel de functie `detect_language()` van de klant en krijg het resultaat. Vervolgens herhalen door de resultaten, en print de ID van elk document, en gedetecteerde taal.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Maak buiten de clientfunctie een `DetectLanguageExample()` nieuwe functie `TextAnalyticsClient` die wordt aangeroepen om het eerder gemaakte object te maken. Maak een `LanguageInput` lijst met objecten met de documenten die u wilt analyseren. Elk object bevat `id`een `text` , en een attribuut. Het `text` kenmerk slaat de te analyseren `id` tekst op en deze kan elke waarde zijn. Bel dan de `DetectLanguage()` functie van de klant.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Roep de functie `DetectLanguageExample()` aan.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Uitvoer

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Herkenning van entiteiten

Maak in het clientobject `RecognizeEntities()` een functie die wordt aangeroepen met een lijst met invoerdocumenten die later worden gemaakt. Bel de functie `entities()` van de klant en krijg het resultaat. Vervolgens doordeeren van de resultaten, en druk de ID van elk document, en de erkende entiteiten af.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Maak buiten de clientfunctie een `RecognizeEntitiesExample()` nieuwe functie `TextAnalyticsClient` die wordt aangeroepen om het eerder gemaakte object te maken. Maak een `MultiLanguageInput` lijst met objecten met de documenten die u wilt analyseren. Elk object bevat `id`een `language`, `text` een , en een attribuut. Het `text` kenmerk slaat de te `language` analyseren tekst op, is `id` de taal van de tekst en de tekst kan elke waarde zijn. Bel dan de `RecognizeEntities()` functie van de klant.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Roep de functie `RecognizeEntitiesExample()` aan.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Uitvoer

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Sleuteltermextractie

Maak in het clientobject `ExtractKeyPhrases()` een functie die wordt aangeroepen met een lijst met invoerdocumenten die later worden gemaakt. Bel de functie `key_phrases()` van de klant en krijg het resultaat. Vervolgens herhalen door de resultaten, en druk elk document ID, en de uitgepakte sleutelzinnen.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Maak buiten de clientfunctie een `KeyPhraseExtractionExample()` nieuwe functie `TextAnalyticsClient` die wordt aangeroepen om het eerder gemaakte object te maken. Maak een `MultiLanguageInput` lijst met objecten met de documenten die u wilt analyseren. Elk object bevat `id`een `language`, `text` een , en een attribuut. Het `text` kenmerk slaat de te `language` analyseren tekst op, is `id` de taal van de tekst en de tekst kan elke waarde zijn. Bel dan de `ExtractKeyPhrases()` functie van de klant.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Roep de functie `KeyPhraseExtractionExample()` aan.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

### <a name="output"></a>Uitvoer

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```
