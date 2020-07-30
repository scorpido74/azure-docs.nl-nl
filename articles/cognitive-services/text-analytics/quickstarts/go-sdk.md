---
title: 'Quickstart: Text Analytics-clientbibliotheek voor Go | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u de Go Text Analytics-clientbibliotheek van Azure Cognitive Services om taalt e detecteren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: 6849046211ae4216f181dd8ef0ca391c2876363c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291778"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Quickstart: De Text Analytics-clientbibliotheek voor Go gebruiken

- [Referentiedocumentatie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics)
- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-go/blob/090dc0ee4d8d2d60e2a9525774d967a4111a2b0c/services/cognitiveservices/v2.1/textanalytics/client.go)
- [Pakket (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)
- [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> Deze quickstart is alleen van toepassing op Text Analytics versie 2.1. Op dit moment is een v3-clientbibliotheek voor Go niet beschikbaar.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services)
* Nieuwste versie van [Go](https://golang.org/dl/)
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Een Text Analytics-resource maken"  target="_blank">maakt u een Text Analytics-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen. 
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Text Analytics-API. U doet dit later in de quickstart.
    * U kunt de gratis prijscategorie gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-go-project"></a>Een nieuw Go-project maken

Maak in een consolevenster (cmd, PowerShell, Terminal, Bash) een nieuwe werkruimte voor uw Go-project en navigeer er naartoe. Uw werkruimte bevat drie mappen: 

* **src** - Deze map bevat broncode en pakketten. Alle pakketten geïnstalleerd met de `go get`-opdracht bevinden zich hier.
* **pkg** - Deze map bevat de gecompileerde Go-pakketobjecten. Deze bestanden hebben allemaal een `.a`-extensie.
* **bin** - Deze map bevat de binaire uitvoerbare bestanden die worden gemaakt wanneer u `go install` uitvoert.

> [!TIP]
> Meer informatie over de structuur van een [Go-werkruimte](https://golang.org/doc/code.html#Workspaces). Deze handleiding bevat informatie om `$GOPATH` en `$GOROOT` in te stellen.

Maak een werkruimte met de naam `my-app` en de vereiste submappen voor `src`, `pkg` en `bin`:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>De Text Analytics-clientbibliotheek voor Go installeren

De clientbibliotheek installeren voor Go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Uw Go-toepassing maken

Maak vervolgens een bestand met de naam `src/quickstart.go`

```bash
$ cd src
$ touch quickstart.go
```

Open `quickstart.go` in uw favoriete IDE of teksteditor. Voeg vervolgens de pakketnaam toe en importeer de volgende bibliotheken:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Objectmodel 

De Text Analytics-client is een [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New)-object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De client biedt verschillende methoden voor het analyseren van tekst, zoals één tekenreeks of een batch. 

Tekst wordt naar de API verzonden als een lijst met `documents`. Dit zijn `dictionary`-objecten die een combinatie van de kenmerken `id`, `text` en `language` bevatten, afhankelijk van de gebruikte methode. Met het kenmerk `text` wordt de tekst die moet worden geanalyseerd, opgeslagen in de oorsprong `language`, en `id` kan elke waarde hebben. 

Het antwoordobject is een lijst met de analyse-informatie voor elk document. 

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Text Analytics-clientbibliotheek voor Go:

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis)
* [Taaldetectie](#language-detection)
* [Herkenning van entiteiten](#entity-recognition)
* [Sleuteltermextractie](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren


Maak in een nieuwe functie variabelen voor het Azure-eindpunt en de abonnementssleutel voor uw resource.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Maak een nieuw [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New)-object. Geef uw sleutel door aan de functie [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer), die vervolgens wordt doorgegeven aan de `authorizer`-eigenschap van de client.

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

Maak een nieuwe functie met de naam `SentimentAnalysis()` en maak een client met behulp van de `GetTextAnalyticsClient()`-methode die u eerder hebt gemaakt. Maak een lijst met [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput)-objecten die de documenten bevat die u wilt analyseren. Elk object bevat een `id`, `Language` en een `text`-kenmerk. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd, `language` is de taal van het document, en `id` kan elke waarde hebben. 

Roep de [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment)-functie van de client aan en haal het resultaat op. Herhaal vervolgens de lijst met resultaten en geef voor elk document de id en gevoelsscore weer. Een score dichter bij 0 wijst op een negatief gevoel, terwijl een score dichter bij 1 op een positief gevoel wijst.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

Roep `SentimentAnalysis()` aan in uw project.

### <a name="output"></a>Uitvoer

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Taaldetectie

Maak een nieuwe functie met de naam `LanguageDetection()` en maak een client met behulp van de `GetTextAnalyticsClient()`-methode die u eerder hebt gemaakt. Maak een lijst met [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput)-objecten die de documenten bevat die u wilt analyseren. Elk object bevat een `id` en een kenmerk `text`. Met het kenmerk `text` wordt de tekst opgeslagen die moet worden geanalyseerd. De `id` kan elke waarde zijn. 

Roep de [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) van de client aan en haal het resultaat op. Blader vervolgens door de resultaten en druk voor elk document de id en gedetecteerde taal af.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Roep `LanguageDetection()` aan in uw project.

### <a name="output"></a>Uitvoer

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Herkenning van entiteiten

Maak een nieuwe functie met de naam `ExtractEntities()` en maak een client met behulp van de `GetTextAnalyticsClient()`-methode die u eerder hebt gemaakt. Maak een lijst met [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput)-objecten die de documenten bevat die u wilt analyseren. Elk object bevat een `id`, `language` en een kenmerk `text`. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd, `language` is de taal van het document, en `id` kan elke waarde hebben. 

Roep de [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) van de client aan en haal het resultaat op. Herhaal vervolgens de resultaten en geef voor elk document de id en de opgehaalde entiteitsscore.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

Roep `ExtractEntities()` aan in uw project.

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

Maak een nieuwe functie met de naam `ExtractKeyPhrases()` en maak een client met behulp van de `GetTextAnalyticsClient()`-methode die u eerder hebt gemaakt. Maak een lijst met [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput)-objecten die de documenten bevat die u wilt analyseren. Elk object bevat een `id`, `language` en een kenmerk `text`. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd, `language` is de taal van het document, en `id` kan elke waarde hebben.

Roep de [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) van de client aan en haal het resultaat op. Blader vervolgens door de resultaten en druk voor elk document de id en opgehaalde sleuteltermen af.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Roep `ExtractKeyPhrases()` aan in uw project.

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
