---
title: 'Snelstart: Text Analytics-clientbibliotheek voor onderweg | Microsoft Documenten'
titleSuffix: Azure Cognitive Services
description: Detecteer in deze quickstart taal met behulp van de Go Text Analytics-clientbibliotheek van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0b4495616c750b2b3e8431e011d71ae8671af1ef
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77912634"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Snelstart: de text analytics-clientbibliotheek voor onderweg gebruiken

[Voorbeeld van](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [broncodevan](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | naslagdocumentatieBibliotheek[(GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> Deze quickstart is alleen van toepassing op Text Analytics versie 2.1. Momenteel is een v3-clientbibliotheek voor Go niet beschikbaar.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
* De nieuwste versie van [Go](https://golang.org/dl/)
* Zodra u uw Azure-abonnement <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="hebt,"  target="_blank">maakt u <span class="docon docon-navigate-external x-hidden-focus"></span> </a> een Text Analytics-bron en maakt u een Text Analytics-bron in de Azure-portal om uw sleutel en eindpunt op te halen. 
    * U hebt de sleutel en het eindpunt van de resource die u maakt nodig om uw toepassing te verbinden met de Text Analytics API. Je doet dit later in de snelle start.
    * U de gratis prijscategorie gebruiken om de service uit te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-go-project"></a>Een nieuw Go-project maken

Maak in een consolevenster (cmd, PowerShell, Terminal, Bash) een nieuwe werkruimte voor uw Go-project en navigeer ernaar. Uw werkruimte bevat drie mappen: 

* **src** - Deze map bevat broncode en pakketten. Alle pakketten die `go get` met de opdracht zijn geïnstalleerd, bevinden zich hier.
* **pkg** - Deze directory bevat de gecompileerde Go-pakketobjecten. Deze bestanden hebben `.a` allemaal een extensie.
* **opslaglocatie** - Deze map bevat de binaire uitvoerbare bestanden die worden gemaakt wanneer u deze uitvoert. `go install`

> [!TIP]
> Meer informatie over de structuur van een [Go-werkruimte](https://golang.org/doc/code.html#Workspaces). Deze handleiding bevat `$GOPATH` informatie `$GOROOT`voor het instellen en .

Maak een `my-app` aangeroepen werkruimte en `src`de `pkg`vereiste `bin`submappen voor , en :

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>De text analytics-clientbibliotheek voor onderweg installeren

Installeer de clientbibliotheek voor Onderweg: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Uw Go-toepassing maken

Maak vervolgens een `src/quickstart.go`bestand met de naam:

```bash
$ cd src
$ touch quickstart.go
```

Open `quickstart.go` in je favoriete IDE of teksteditor. Voeg vervolgens de pakketnaam toe en importeer de volgende bibliotheken:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Objectmodel 

De Text Analytics-client is een [BaseClient-object](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) dat met uw sleutel naar Azure wordt geverifieerd. De client biedt verschillende methoden voor het analyseren van tekst, als één tekenreeks of als batch. 

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


Maak in een nieuwe functie variabelen voor het Azure-eindpunt en de abonnementssleutel van uw bron.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Een nieuw [BaseClient-object](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) maken. Geef je sleutel door aan de [autorest. De functie NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) wordt vervolgens doorgegeven `authorizer` aan de eigenschap van de klant.

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>Sentimentanalyse

Maak een nieuwe `SentimentAnalysis()` functie aangeroepen `GetTextAnalyticsClient()` en maak een client met behulp van de methode die eerder is gemaakt. Maak een lijst met [multilanguageinput-objecten](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) met de documenten die u wilt analyseren. Elk object bevat `id` `Language` een `text` , en een attribuut. Het `text` kenmerk slaat de te `language` analyseren tekst op, is `id` de taal van het document en het kan elke waarde zijn. 

Bel de [functie Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) van de klant en krijg het resultaat. Vervolgens herhalen door de resultaten, en print elk document ID, en sentiment score. Een score dichter bij 0 duidt op een negatief sentiment, terwijl een score dichter bij 1 wijst op een positief sentiment.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

oproep `SentimentAnalysis()` in uw project.

### <a name="output"></a>Uitvoer

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Taaldetectie

Maak een nieuwe `LanguageDetection()` functie aangeroepen `GetTextAnalyticsClient()` en maak een client met behulp van de methode die eerder is gemaakt. Maak een lijst met [LanguageInput-objecten](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) met de documenten die u wilt analyseren. Elk object bevat `id` een `text` en een attribuut. Het `text` kenmerk slaat de te analyseren `id` tekst op en deze kan elke waarde zijn. 

Bel de [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) van de klant en krijg het resultaat. Vervolgens herhalen door de resultaten, en print de ID van elk document, en gedetecteerde taal.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Bel `LanguageDetection()` uw project.

### <a name="output"></a>Uitvoer

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Herkenning van entiteiten

Maak een nieuwe `ExtractEntities()` functie aangeroepen `GetTextAnalyticsClient()` en maak een client met behulp van de methode die eerder is gemaakt. Maak een lijst met [multilanguageinput-objecten](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) met de documenten die u wilt analyseren. Elk object bevat `id` `language`een , `text` , en een attribuut. Het `text` kenmerk slaat de te `language` analyseren tekst op, is `id` de taal van het document en het kan elke waarde zijn. 

Bel de [entiteiten() van](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) de klant en krijg het resultaat. Vervolgens herhalen door de resultaten, en print de ID van elk document, en geëxtraheerd entiteiten score.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

oproep `ExtractEntities()` in uw project.

### <a name="output"></a>Uitvoer

```console
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

## <a name="key-phrase-extraction"></a>Sleuteltermextractie

Maak een nieuwe `ExtractKeyPhrases()` functie aangeroepen `GetTextAnalyticsClient()` en maak een client met behulp van de methode die eerder is gemaakt. Maak een lijst met [multilanguageinput-objecten](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) met de documenten die u wilt analyseren. Elk object bevat `id` `language`een , `text` , en een attribuut. Het `text` kenmerk slaat de te `language` analyseren tekst op, is `id` de taal van het document en het kan elke waarde zijn.

Bel de [keyphrases() van](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) de klant en krijg het resultaat. Vervolgens herhalen door de resultaten, en print elk document ID, en haalde de belangrijkste zinnen.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Bel `ExtractKeyPhrases()` uw project.

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
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
