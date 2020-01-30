---
title: Batch transcriptie-Speech Service gebruiken
titleSuffix: Azure Cognitive Services
description: Batch transcriptie is ideaal als u wilt dat een grote hoeveelheid audio in opslag, zoals Azure Blobs transcriberen. U kunt met behulp van de toegewezen REST-API, wijst u audio-bestanden met een shared access signature (SAS) URI en asynchroon ontvangen transcripties.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: panosper
ms.openlocfilehash: 5732df2551eafa74b81f9a918a1cb7cf5ac1395c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768030"
---
# <a name="how-to-use-batch-transcription"></a>Batch-transcriptie gebruiken

Batch transcriptie is ideaal voor het overzetten van een grote hoeveelheid audio in de opslag. U kunt met behulp van de toegewezen REST API naar audio bestanden verwijzen met een SAS-URI (Shared Access Signature) en transcriptie-resultaten asynchroon ontvangen.

De API biedt asynchrone spraak-naar-tekst transcriptie en andere functies. U kunt REST API gebruiken om methoden beschikbaar te maken voor het volgende:

- Een batch-verwerkings aanvraag maken
- Query uitvoeren op de status
- Transcriptie-resultaten downloaden
- Transcriptie-informatie uit de service verwijderen

De gedetailleerde API is beschikbaar als [Swagger-document](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)onder de kop `Custom Speech transcriptions`.

Batch transcriptie-taken worden gepland op basis van de beste inspanningen. Er is momenteel geen schatting voor wanneer een taak wordt gewijzigd in de actieve status. Onder normale belasting van het systeem moet het binnen enkele minuten plaatsvinden. Zodra de status wordt uitgevoerd, wordt de daad werkelijke transcriptie sneller verwerkt dan de audio real-time.

Naast de gebruiks vriendelijke API hoeft u geen aangepaste eind punten te implementeren en hebt u geen gelijktijdigheids vereisten om te observeren.

## <a name="prerequisites"></a>Vereisten

### <a name="subscription-key"></a>Abonnements sleutel

Als met alle functies van de spraak-service die u een abonnementssleutel van maakt de [Azure-portal](https://portal.azure.com) door onze [introductiehandleiding](get-started.md).

>[!NOTE]
> Voor de speech-service is een Standard-abonnement (S0) vereist om batch-transcriptie te gebruiken. Gratis abonnementssleutels (F0) werkt niet. Zie [prijzen en limieten](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)voor meer informatie.

### <a name="custom-models"></a>Aangepaste modellen

Als u van plan bent om akoestische of taal modellen aan te passen, volgt u de stappen in [Customize akoestische modellen](how-to-customize-acoustic-models.md) en [ontwerp aanpassing taal modellen](how-to-customize-language-model.md). Als u de gemaakte modellen in batch transcriptie wilt gebruiken, moet u hun model-Id's hebben. U kunt de model-ID ophalen wanneer u de details van het model inspecteert. Een geïmplementeerd aangepast eind punt is niet nodig voor de batch transcriptie-service.

## <a name="the-batch-transcription-api"></a>De Batch-transcriptie API

### <a name="supported-formats"></a>Ondersteunde indelingen

De Batch-API voor transcriptie ondersteunt de volgende indelingen:

| Indeling | Codec | Bitrate | Samplefrequentie |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bits | 8 kHz of 16 kHz, mono of stereo |
| MP3 | PCM | 16-bits | 8 kHz of 16 kHz, mono of stereo |
| OGG | OPUS | 16-bits | 8 kHz of 16 kHz, mono of stereo |

Voor audio stromen met stereo worden de linker-en rechter kanalen gesplitst tijdens de transcriptie. Voor elk kanaal wordt een JSON-resultaat bestand gemaakt. Met de tijds tempels die per utterance worden gegenereerd, kunnen ontwikkel aars een geordende definitieve transcript maken.

### <a name="configuration"></a>Configuratie

Configuratie parameters worden als JSON opgegeven:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<SAS to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Configuratie-eigenschappen

Gebruik deze optionele eigenschappen om transcriptie te configureren:

| Parameter | Beschrijving |
|-----------|-------------|
| `ProfanityFilterMode` | Geeft aan hoe grof taalgebruik in herkenningsresultaten worden verwerkt. Geaccepteerde waarden zijn `None` die wordt uitgeschakeld grof taalgebruik filteren, `Masked` die grof taalgebruik vervangen door sterretjes, `Removed` waarbij alle scheldwoorden worden verwijderd uit het resultaat, of `Tags` zodat 'grof taalgebruik' tags aan wordt toegevoegd. De standaardinstelling is `Masked`. |
| `PunctuationMode` | Geeft aan hoe interpunctie in herkenningsresultaten worden verwerkt. Geaccepteerde waarden zijn `None` die wordt uitgeschakeld interpunctie, `Dictated` dit expliciete interpunctie houdt `Automatic` waarmee de decoder interpunctie, behandelt of `DictatedAndAutomatic` dit houdt bepaald leestekens of automatisch. |
| `AddWordLevelTimestamps` | Hiermee wordt aangegeven of Time Stamps op woord niveau moeten worden toegevoegd aan de uitvoer. Geaccepteerde waarden zijn `true` die tijds tempels op woord niveau en `false` (de standaard waarde) in staat stelt om deze uit te scha kelen. |
| `AddSentiment` | Hiermee geeft u sentiment moet worden toegevoegd aan de utterance. Geaccepteerde waarden zijn `true` die sentiment per utterance en `false` (de standaard waarde) in staat stelt om het uit te scha kelen. |
| `AddDiarization` | Hiermee geeft u op dat diarization-analyse moet worden uitgevoerd op de invoer waarvan wordt verwacht dat deze een mono-kanaal met twee stemmen bevat. Geaccepteerde waarden zijn `true` die diarization en `false` (de standaard waarde) in staat stelt om het uit te scha kelen. Ook moet `AddWordLevelTimestamps` worden ingesteld op True.|
|`TranscriptionResultsContainerUrl`|Optionele SAS-token naar een Beschrijf bare container in Azure. Het resultaat wordt opgeslagen in deze container.

### <a name="storage"></a>Storage

Batch transcriptie ondersteunt [Azure Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) voor het lezen van audio en schrijven van transcripties naar opslag.

## <a name="the-batch-transcription-result"></a>Het batch-transcriptie resultaat

Voor mono-invoer audio wordt één transcriptie-resultaat bestand gemaakt. Voor stereo-invoer audio worden er twee transcriptie gegenereerd. Elk heeft deze structuur:

```json
{
  "AudioFileResults":[ 
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in milliseconds'
          "Duration": number                                'time in milliseconds'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in milliseconds'
                  "Duration": number                        'time in milliseconds'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

Het resultaat bevat de volgende formulieren:

|Formulieren|Inhoud|
|-|-|
|`Lexical`|De werkelijke woorden die worden herkend.
|`ITN`|Inverse-text-genormaliseerde vorm van de herkende tekst. Afkortingen ("Doctor Smith" naar "Dr Smith"), telefoon nummers en andere trans formaties worden toegepast.
|`MaskedITN`|Het formulier ITN waarin de maskering voor scheld woorden is toegepast.
|`Display`|De weergave vorm van de herkende tekst. Dit omvat toegevoegde interpunctie en hoofdletter gebruik.

## <a name="speaker-separation-diarization"></a>Schei ding van de spreker (Diarization)

Diarization is het proces waarbij de luid sprekers in een audio fragment worden gescheiden. Onze batch pijplijn ondersteunt diarization en kan twee luid sprekers herkennen aan mono-kanaal opnamen. De functie is niet beschikbaar op stereo-opnamen.

Alle transcriptie-uitvoer bevat een `SpeakerId`. Als diarization niet wordt gebruikt, wordt er `"SpeakerId": null` weer gegeven in de JSON-uitvoer. Voor diarization ondersteunen we twee stemmen, zodat de luid sprekers worden aangeduid als `"1"` of `"2"`.

Als u diarization wilt aanvragen, hoeft u alleen de relevante para meter in de HTTP-aanvraag toe te voegen, zoals hieronder wordt weer gegeven.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Tijds tempels op woord niveau moeten ook ' ingeschakeld ' zijn als de para meters in de bovenstaande aanvraag aangeven.

## <a name="sentiment-analysis"></a>Sentimentanalyse

Met de functie sentiment wordt de sentiment die wordt weer gegeven in de audio geschat. De sentiment wordt uitgedrukt door een waarde tussen 0 en 1 voor `Negative`, `Neutral`en `Positive` sentiment. Zo kan sentiment analyse worden gebruikt in aanroep centrum scenario's:

- Inzicht krijgen in klant tevredenheid
- Inzicht krijgen in de prestaties van de agents (team die de aanroepen nemen)
- Het exacte tijdstip van een gesprek met een inschakeling in een negatieve richting zoeken
- Wat is er goed geworden bij het omzetten van een negatieve aanroep naar een positieve richting
- Vaststellen wat klanten zijn en wat ze leuk vinden over een product of een service

Sentiment wordt per audio segment beoordeeld op basis van de lexicale vorm. De volledige tekst in dat audio segment wordt gebruikt om sentiment te berekenen. Er wordt geen geaggregeerde sentiment berekend voor de gehele transcriptie.

Een voor beeld van een JSON-uitvoer ziet er als volgt uit:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="best-practices"></a>Aanbevolen procedures

De transcriptie-service kan een groot aantal verzonden transcripties verwerken. U kunt de status van uw transcripties opvragen via een `GET` in de [methode transcripties](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Bewaar de gegevens naar een redelijke grootte door de para meter `take` (honderden) op te geven. [Verwijder transcripties](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regel matig van de service zodra u de resultaten hebt opgehaald. Hiermee worden snelle antwoorden van de transcriptie-beheer aanroepen gegarandeerd.

## <a name="sample-code"></a>Voorbeeldcode

Volledige voor beelden zijn beschikbaar in de [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples) in de submap `samples/batch`.

U moet de voorbeeld code aanpassen met uw abonnements gegevens, de service regio, de SAS-URI die verwijst naar het audio bestand en model-Id's voor het geval u een aangepast akoestische of taal model wilt gebruiken.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Met de voorbeeld code wordt de client ingesteld en wordt de transcriptie-aanvraag verzonden. Er wordt vervolgens gevraagd om status informatie en Details over de transcriptie-voortgang af te drukken.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Zie het [Swagger-document](https://westus.cris.ai/swagger/ui/index)voor volledige informatie over de voor gaande aanroepen. Voor het volledige voor beeld dat hier wordt weer gegeven, gaat u naar [github](https://aka.ms/csspeech/samples) in de submap `samples/batch`.

Noteer de asynchrone instellingen voor het plaatsen van audio en transcriptie status ontvangen. De client die u maakt, is een .NET-HTTP-client. Er is een `PostTranscriptions` methode voor het verzenden van de details van de audio-bestand en een `GetTranscriptions` methode voor het ontvangen van de resultaten. `PostTranscriptions` retourneert een ingang en `GetTranscriptions` gebruikt voor het maken van een greep om de status van de transcriptie.

De huidige voorbeeldcode opgeven niet een aangepast model. De service maakt gebruik van de basislijn-modellen voor te transcriberen van het bestand of de bestanden. U kunt doorgeven om op te geven de modellen, op dezelfde manier als de model-id voor de akoestische en het taalmodel.

> [!NOTE]
> Voor basislijn transcripties hoeft u de ID voor de basislijn modellen niet te declareren. Als u alleen een taal model-ID opgeeft (en geen akoestische model-ID), wordt er automatisch een overeenkomstig akoestische model geselecteerd. Als u alleen een akoestische model-ID opgeeft, wordt automatisch een overeenkomend taal model geselecteerd.

## <a name="download-the-sample"></a>Het voorbeeld downloaden

U kunt het voor beeld vinden in de map `samples/batch` in de [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
