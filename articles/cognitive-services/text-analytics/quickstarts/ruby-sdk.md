---
title: 'Quickstart: Text Analytics-clientbibliotheek voor Ruby | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u de Ruby Text Analytics-clientbibliotheek van Azure Cognitive Services om taal te detecteren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: 8afceb19af0d177415d0b68b5d38f19d18835af5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87291765"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Quickstart: De Text Analytics-clientbibliotheek voor Ruby gebruiken

Aan de slag met de Text Analytics-clientbibliotheek. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen.

Gebruik de Text Analytics-clientbibliotheek om het volgende uit te voeren:

* Sentimentanalyse
* Taaldetectie
* Herkenning van entiteiten
* Sleuteltermextractie

> [!NOTE]
> Deze quickstart is alleen van toepassing op Text Analytics versie 2.1. Op dit moment is een v3-clientbibliotheek voor Ruby niet beschikbaar.

[Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [pakket (RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services)
* De huidige versie van [Ruby](https://www.ruby-lang.org/)
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Een Text Analytics-resource maken"  target="_blank">maakt u een Text Analytics-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen. 
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Text Analytics-API. U doet dit later in de quickstart.
    * U kunt de gratis prijscategorie gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-ruby-application"></a>Een nieuwe Ruby-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. Maak vervolgens een bestand met de naam `GemFile` en een Ruby-bestand voor uw code.

```console
mkdir myapp && cd myapp
```

Voeg in uw `GemFile` de volgende regels toe om de clientbibliotheek als een afhankelijkheid toe te voegen.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

Importeer de volgende pakketten in uw Ruby-bestand.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Objectmodel 

De Text Analytics-client wordt met behulp van uw sleutel geverifieerd bij Azure. De client biedt verschillende methoden voor het analyseren van tekst, zoals één tekenreeks of een batch. 

Tekst wordt naar de API verzonden als een lijst met `documents`. Dit zijn `dictionary`-objecten die een combinatie van de kenmerken `id`, `text` en `language` bevatten, afhankelijk van de gebruikte methode. Met het kenmerk `text` wordt de tekst die moet worden geanalyseerd, opgeslagen in de oorsprong `language`, en `id` kan elke waarde hebben. 

Het antwoordobject is een lijst met de analyse-informatie voor elk document. 

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Text Analytics-clientbibliotheek voor Ruby:

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis)
* [Taaldetectie](#language-detection)
* [Herkenning van entiteiten](#entity-recognition)
* [Sleuteltermextractie](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een klasse met de naam `TextAnalyticsClient`. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

In deze klasse maakt u een functie met de naam `initialize` om de client te verifiëren met behulp van uw sleutel en eindpunt. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Gebruik de functie `new()` van de client buiten de klasse om deze te instantiëren.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Sentimentanalyse

Maak in het clientobject een functie met de naam `AnalyzeSentiment()` waardoor een lijst met invoerdocumenten die later worden gemaakt, wordt gebruikt. Roep de functie `sentiment()` van de client aan en haal het resultaat op. Herhaal vervolgens de lijst met resultaten en geef voor elk document de id en gevoelsscore weer. Een score dichter bij 0 wijst op een negatief gevoel, terwijl een score dichter bij 1 op een positief gevoel wijst.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Maak buiten de clientfunctie een nieuwe functie met de naam `SentimentAnalysisExample()` waardoor het eerder gemaakte `TextAnalyticsClient`-object wordt gebruikt. Maak een lijst met `MultiLanguageInput`-objecten die de documenten bevat die u wilt analyseren. Elk object bevat een `id`, `Language` en een `text`-kenmerk. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd, `language` is de taal van het document, en `id` kan elke waarde hebben. Roep vervolgens de functie `AnalyzeSentiment()` van de client aan.

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

Maak in het clientobject een functie met de naam `DetectLanguage()` waardoor een lijst met invoerdocumenten die later worden gemaakt, wordt gebruikt. Roep de functie `detect_language()` van de client aan en haal het resultaat op. Blader vervolgens door de resultaten en druk voor elk document de id en gedetecteerde taal af.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Maak buiten de clientfunctie een nieuwe functie met de naam `DetectLanguageExample()` waardoor het eerder gemaakte `TextAnalyticsClient`-object wordt gebruikt. Maak een lijst met `LanguageInput`-objecten die de documenten bevat die u wilt analyseren. Elk object bevat een `id` en een kenmerk `text`. Met het kenmerk `text` wordt de tekst opgeslagen die moet worden geanalyseerd. De `id` kan elke waarde zijn. Roep vervolgens de functie `DetectLanguage()` van de client aan.

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

Maak in het clientobject een functie met de naam `RecognizeEntities()` waardoor een lijst met invoerdocumenten die later worden gemaakt, wordt gebruikt. Roep de functie `entities()` van de client aan en haal het resultaat op. Herhaal vervolgens de resultaten en druk voor elk document de id en de opgehaalde entiteitsscore af.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Maak buiten de clientfunctie een nieuwe functie met de naam `RecognizeEntitiesExample()` waardoor het eerder gemaakte `TextAnalyticsClient`-object wordt gebruikt. Maak een lijst met `MultiLanguageInput`-objecten die de documenten bevat die u wilt analyseren. Elk object bevat een `id`, een `language` en een kenmerk `text`. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd, `language` is de taal van de tekst en `id` kan elke waarde hebben. Roep vervolgens de functie `RecognizeEntities()` van de client aan.

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

Maak in het clientobject een functie met de naam `ExtractKeyPhrases()` waardoor een lijst met invoerdocumenten die later worden gemaakt, wordt gebruikt. Roep de functie `key_phrases()` van de client aan en haal het resultaat op. Blader vervolgens door de resultaten en druk voor elk document de id en de opgehaalde sleuteltermen af.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Maak buiten de clientfunctie een nieuwe functie met de naam `KeyPhraseExtractionExample()` waardoor het eerder gemaakte `TextAnalyticsClient`-object wordt gebruikt. Maak een lijst met `MultiLanguageInput`-objecten die de documenten bevat die u wilt analyseren. Elk object bevat een `id`, een `language` en een kenmerk `text`. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd, `language` is de taal van de tekst en `id` kan elke waarde hebben. Roep vervolgens de functie `ExtractKeyPhrases()` van de client aan.

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
