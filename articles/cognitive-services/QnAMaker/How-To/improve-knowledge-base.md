---
title: Verbeter kennisbank - QnA Maker
description: Verbeter de kwaliteit van je kennisbank met actief leren. Bestaande vragen controleren, accepteren of weigeren, toevoegen zonder bestaande vragen te verwijderen of te wijzigen.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 7fafc23eaf21099ebb974da226d07c351fa19699
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756742"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Accepteer actieve leervragen in de kennisbank


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Active Learning wijzigt de Knowledge Base of Search Service nadat u de suggestie hebt goedgekeurd en vervolgens opslaat en traint. Als u de suggestie goedkeurt, wordt deze als alternatieve vraag toegevoegd.

## <a name="turn-on-active-learning"></a>Actief leren inschakelen

Als u voorgestelde vragen wilt zien, moet u [actief leren inschakelen](use-active-learning.md) voor uw QnA Maker-bron.

## <a name="view-suggested-questions"></a>Voorgestelde vragen weergeven

1. Als u de voorgestelde vragen wilt zien, selecteert u op de pagina Kennisbasis **bewerken** de optie **Opties weergeven**en selecteert u **Actieve leersuggesties weergeven**.

    [![Selecteer in het gedeelte Bewerken van de portal Suggesties weergeven om de nieuwe vraagalternatieven voor actieve leer te bekijken.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filter de kennisbank met vraag- en antwoordparen om alleen suggesties weer te geven door **Filter op suggesties**te selecteren.

    [![Gebruik de optie Filter op suggesties om alleen de voorgestelde vraagalternatieven voor actieve leer te bekijken.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Elk QnA-paar stelt de nieuwe vraagalternatieven voor met een vinkje, `✔` om de vraag of een `x` om de suggesties af te wijzen te accepteren. Selecteer het vinkje om de vraag toe te voegen.

    [![De voorgestelde vraagalternatieven voor actief leren selecteren of afwijzen door het groene vinkje of het rode verwijdermerk te selecteren.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    U _alle suggesties_ toevoegen of verwijderen door **Alles toevoegen** of Alles **afwijzen op** de contextuele werkbalk te selecteren.

1. Selecteer **Opslaan en trainen** om de wijzigingen in de kennisbank op te slaan.

1. Selecteer **Publiceren** om toe te staan dat de wijzigingen beschikbaar zijn in de [GenerateAnswer API.](metadata-generateanswer-usage.md#generateanswer-request-configuration)

    Wanneer 5 of meer vergelijkbare query's worden geclusterd, stelt QnA Maker elke 30 minuten de alternatieve vragen voor die u accepteren of weigeren.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Architecturale stroom voor het gebruik van GenerateAnswer en Train API's van een bot

Een bot of andere clienttoepassing moet de volgende architecturale stroom gebruiken om actief leren te gebruiken:

* Bot [krijgt het antwoord van de kennisbank](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) met `top` de GenerateAnswer API, waarbij de eigenschap wordt gebruikt om een aantal antwoorden te krijgen.
* Bot bepaalt expliciete feedback:
    * Met behulp van uw eigen [aangepaste bedrijfslogica,](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)filter lage scores.
    * Geef in de bot- of client-applicatie een lijst met mogelijke antwoorden aan de gebruiker weer en krijg het geselecteerde antwoord van de gebruiker.
* Bot [stuurt geselecteerd antwoord terug naar QnA Maker](#bot-framework-sample-code) met de Train [API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Gebruik de bovenste eigenschap in het verzoek Voor antwoord genereren om verschillende overeenkomende antwoorden te krijgen

Bij het indienen van een vraag aan QnA Maker voor een antwoord, de `top` eigenschap van de JSON lichaam stelt het aantal antwoorden terug te keren.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Gebruik de eigenschap score samen met bedrijfslogica om een lijst met antwoorden te krijgen om de gebruiker weer te geven

Wanneer de clienttoepassing (zoals een chatbot) het antwoord ontvangt, worden de top 3-vragen geretourneerd. Gebruik `score` de eigenschap om de nabijheid tussen scores te analyseren. Dit nabijheidsbereik wordt bepaald door uw eigen bedrijfslogica.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Follow-up van clienttoepassingen wanneer vragen vergelijkbare scores hebben

Uw clienttoepassing geeft de vragen weer met een optie voor de gebruiker om _de enkele vraag_ te selecteren die de meeste hun intentie vertegenwoordigt.

Zodra de gebruiker een van de bestaande vragen selecteert, stuurt de clienttoepassing de keuze van de gebruiker als feedback met behulp van de Train API van QnA Maker. Deze feedback maakt de actieve leerfeedbacklus compleet.

## <a name="train-api"></a>API trainen

Actieve leerfeedback wordt naar QnA Maker verzonden met de treinAPI POST-aanvraag. De API-handtekening is:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP-aanvraageigenschap|Name|Type|Doel|
|--|--|--|--|
|PARAMETER URL-route|Knowledge base ID|tekenreeks|De GUID voor uw kennisbank.|
|Aangepast subdomein|QnAMaker-bronnaam|tekenreeks|De bronnaam wordt gebruikt als het aangepaste subdomein voor uw QnA Maker. Dit is beschikbaar op de pagina Instellingen nadat u de kennisbank hebt gepubliceerd. Het staat vermeld `host`als de .|
|Header|Content-Type|tekenreeks|Het mediatype van de body dat naar de API wordt verzonden. Standaardwaarde is:`application/json`|
|Header|Autorisatie|tekenreeks|Uw eindpuntsleutel (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Post Lichaam|JSON-object|JSON|De feedback over de training|

De JSON body heeft verschillende instellingen:

|Json carrosseriebezit|Type|Doel|
|--|--|--|--|
|`feedbackRecords`|matrix|Lijst met feedback.|
|`userId`|tekenreeks|De gebruikersnaam van de persoon die de voorgestelde vragen accepteert. De indeling van de gebruikersnaam is aan u. Een e-mailadres kan bijvoorbeeld een geldige gebruikersnaam in uw architectuur zijn. Optioneel.|
|`userQuestion`|tekenreeks|Exacte tekst van de query van de gebruiker. Vereist.|
|`qnaID`|getal|ID van de vraag, gevonden in het [antwoord GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Een voorbeeld JSON lichaam ziet eruit als:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Een succesvolle reactie retourneert een status van 204 en geen JSON-responslichaam.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Veel feedbackrecords in één gesprek batchen

In de client-side applicatie, zoals een bot, u de gegevens opslaan en `feedbackRecords` vervolgens veel records verzenden in één JSON-body in de array.

Een voorbeeld JSON lichaam ziet eruit als:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Voorbeeldcode van botframework

Uw botframeworkcode moet de Trein-API aanroepen, als de query van de gebruiker moet worden gebruikt voor actief leren. Er zijn twee stukken code te schrijven:

* Bepalen of query moet worden gebruikt voor actief leren
* Query's terugsturen naar de Train API van QnA Maker voor actief leren

In de [azure bot-steekproef](https://aka.ms/activelearningsamplebot)zijn beide activiteiten geprogrammeerd.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Voorbeeld C#-code voor Trein-API met Bot Framework 4.x

De volgende code illustreert hoe u informatie terugsturen naar QnA Maker met de Train API.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Voorbeeld Node.js-code voor Trein-API met Bot Framework 4.x

De volgende code illustreert hoe u informatie terugsturen naar QnA Maker met de Train API.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Actief leren wordt opgeslagen in de geëxporteerde kennisbank

Wanneer uw app actief leren heeft ingeschakeld en `SuggestedQuestions` u de app exporteert, behoudt de kolom in het tsv-bestand de actieve leergegevens.

De `SuggestedQuestions` kolom is een JSON-object `autosuggested`met informatie `usersuggested` over impliciete en expliciete feedback. Een voorbeeld van dit JSON-object voor `help` een enkele door de gebruiker ingediende vraag is:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

U de API voor downloadwijzigingen ook gebruiken om deze wijzigingen te bekijken met BEHULP van REST of een van de op taal gebaseerde SDK's:
* [REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Wanneer u deze app opnieuw importeert, blijft het actieve leren informatie verzamelen en suggesties aanbevelen voor uw kennisbank.



## <a name="best-practices"></a>Aanbevolen procedures

Zie [Aanbevolen](../Concepts/best-practices.md#active-learning)procedures voor aanbevolen procedures bij het gebruik van actief leren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Metagegevens gebruiken met GenerateAnswer-API](metadata-generateanswer-usage.md)
