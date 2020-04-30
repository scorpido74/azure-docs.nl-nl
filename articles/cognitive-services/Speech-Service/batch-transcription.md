---
title: Wat is batch transcriptie-Speech Service
titleSuffix: Azure Cognitive Services
description: Batch transcriptie is ideaal als u een grote hoeveelheid audio in de opslag ruimte wilt transcriberen, zoals Azure-blobs. U kunt met behulp van de toegewezen REST API naar audio bestanden verwijzen met een SAS-URI (Shared Access Signature) en transcripties asynchroon ontvangen.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 46bfabfb2ccf091fd5dc0fcf0e9b447bad7c34d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82208615"
---
# <a name="what-is-batch-transcription"></a>Wat is batch-transcriptie?

Batch-transcriptie is een reeks REST API bewerkingen waarmee u een grote hoeveelheid audio in de opslag kunt transcriberen. U kunt naar audio bestanden met een SAS-URI (Shared Access Signature) verwijzen en transcriptie-resultaten asynchroon ontvangen.

Asynchrone spraak-naar-tekst transcriptie is slechts een van de functies. U kunt batch-transcriptie REST-Api's gebruiken om de volgende methoden aan te roepen:



|    Batch transcriptie-bewerking                                             |    Methode    |    REST API-aanroep                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Hiermee maakt u een nieuwe transcriptie.                                              |    POST      |    API/speechtotext/v 2.0/transcripties            |
|    Hiermee wordt een lijst met transcripties voor het geverifieerde abonnement opgehaald.    |    GET       |    API/speechtotext/v 2.0/transcripties            |
|    Hiermee wordt een lijst met ondersteunde land instellingen voor offline-transcripties opgehaald.              |    GET       |    API/speechtotext/v 2.0/transcripties/land instellingen    |
|    Hiermee worden de onveranderlijke gegevens van de transcriptie die door de ID worden geïdentificeerd, bijgewerkt.    |    VERZENDEN     |    API/speechtotext/v 2.0/transcripties/{id}       |
|    Hiermee wordt de opgegeven transcriptie-taak verwijderd.                                 |    DELETE    |    API/speechtotext/v 2.0/transcripties/{id}       |
|    Hiermee wordt de transcriptie opgehaald die wordt geïdentificeerd door de opgegeven ID.                        |    GET       |    API/speechtotext/v 2.0/transcripties/{id}       |




U kunt de gedetailleerde API bekijken en testen, die beschikbaar is als [Swagger-document](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A), onder de kop `Custom Speech transcriptions`.

Batch transcriptie-taken worden gepland op basis van de beste inspanningen. Er is momenteel geen schatting voor wanneer de status van een taak wordt gewijzigd. Onder normale belasting van het systeem moet het binnen enkele minuten plaatsvinden. Zodra de status wordt uitgevoerd, wordt de daad werkelijke transcriptie sneller verwerkt dan de audio real-time.

Naast de gebruiks vriendelijke API hoeft u geen aangepaste eind punten te implementeren en hebt u geen gelijktijdigheids vereisten om te observeren.

## <a name="prerequisites"></a>Vereisten

### <a name="subscription-key"></a>Abonnementssleutel

Net als bij alle functies van de speech-service maakt u een abonnements sleutel van de [Azure Portal](https://portal.azure.com) door de [aan de slag-hand leiding](get-started.md)te volgen.

>[!NOTE]
> Voor de speech-service is een Standard-abonnement (S0) vereist om batch-transcriptie te gebruiken. Sleutels voor gratis abonnementen (F0) werken niet. Zie [prijzen en limieten](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)voor meer informatie.

### <a name="custom-models"></a>Aangepaste modellen

Als u van plan bent om akoestische of taal modellen aan te passen, volgt u de stappen in [Customize akoestische modellen](how-to-customize-acoustic-models.md) en [ontwerp aanpassing taal modellen](how-to-customize-language-model.md). Als u de gemaakte modellen in batch transcriptie wilt gebruiken, moet u hun model-Id's hebben. U kunt de model-ID ophalen wanneer u de details van het model inspecteert. Een geïmplementeerd aangepast eind punt is niet nodig voor de batch transcriptie-service.

## <a name="the-batch-transcription-api"></a>De batch-transcriptie-API

### <a name="supported-formats"></a>Ondersteunde indelingen

De batch transcriptie-API ondersteunt de volgende indelingen:

| Indeling | Videocodec | Bitsnelheid | Sample frequentie                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-bits  | 8 kHz of 16 kHz, mono of stereo |
| MP3    | PCM   | 16-bits  | 8 kHz of 16 kHz, mono of stereo |
| OGG    | OPUS  | 16-bits  | 8 kHz of 16 kHz, mono of stereo |

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
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Configuratie-eigenschappen

Gebruik deze optionele eigenschappen om transcriptie te configureren:

:::row:::
   :::column span="1":::
      **Bepaalde**
   :::column-end:::
   :::column span="2":::
      **Beschrijving**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Hiermee geeft u op hoe scheld woorden in de herkennings resultaten moet worden afgehandeld. Geaccepteerde waarden `None` zijn het uitschakelen van het filteren `Masked` van grove woorden, het vervangen van woorden `Removed` met sterretjes, om alle woorden van het resultaat `Tags` te verwijderen, of om "Gods gang" tags toe te voegen. De standaardinstelling is `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Hiermee wordt aangegeven hoe interpunctie in herkennings resultaten moet worden afgehandeld. Geaccepteerde waarden `None` zijn om Lees tekens uit `Dictated` te scha kelen, zodat er expliciete ( `Automatic` gesp roken) Lees tekens worden gebruikt, zodat de `DictatedAndAutomatic` decoder met interpunctie kan omgaan of gedicteerde en automatische interpunctie mag gebruiken. De standaardinstelling is `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Hiermee wordt aangegeven of Time Stamps op woord niveau moeten worden toegevoegd aan de uitvoer. Geaccepteerde waarden `true` zijn het inschakelen van tijds tempels op `false` woord niveau en (de standaard waarde) om deze uit te scha kelen.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Hiermee wordt aangegeven of sentiment analyse moet worden toegepast op de utterance. Geaccepteerde waarden `true` moeten worden ingeschakeld `false` en (de standaard waarde) om deze uit te scha kelen. Zie [sentimentanalyse](#sentiment-analysis) voor meer informatie.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Hiermee geeft u op dat diarization analyse moet worden uitgevoerd op de invoer, die naar verwachting mono-kanaal met twee stemmen is. Geaccepteerde waarden `true` zijn diarization en `false` (de standaard waarde) om deze optie uit te scha kelen. Het moet `AddWordLevelTimestamps` ook worden ingesteld op True.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Optionele URL met [service-sa's](../../storage/common/storage-sas-overview.md) naar een Beschrijf bare container in Azure. Het resultaat wordt opgeslagen in deze container.
:::row-end:::

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
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
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
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
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

:::row:::
   :::column span="1":::
      **Formulier**
   :::column-end:::
   :::column span="2":::
      **Inhoud**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      De werkelijke woorden die worden herkend.
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      Inverse-text-genormaliseerde vorm van de herkende tekst. Afkortingen ("Doctor Smith" naar "Dr Smith"), telefoon nummers en andere trans formaties worden toegepast.
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      Het formulier ITN waarin de maskering voor scheld woorden is toegepast.
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      De weergave vorm van de herkende tekst. Er zijn toegevoegde interpunctie en hoofdletter gebruik opgenomen.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Schei ding van de spreker (Diarization)

Diarization is het proces waarbij de luid sprekers in een audio fragment worden gescheiden. Onze batch pijplijn ondersteunt diarization en kan twee luid sprekers herkennen aan mono-kanaal opnamen. De functie is niet beschikbaar op stereo-opnamen.

Alle transcriptie-uitvoer bevat `SpeakerId`een. Als diarization niet wordt gebruikt, wordt dit `"SpeakerId": null` weer gegeven in de JSON-uitvoer. Voor diarization ondersteunen we twee stemmen, waardoor de luid sprekers worden aangeduid `"1"` als `"2"`of.

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

Sentiment wordt per audio segment beoordeeld op basis van de lexicale vorm. De volledige tekst in dat audio segment wordt gebruikt om sentiment te berekenen. Er wordt geen geaggregeerde sentiment berekend voor de gehele transcriptie. Sentiment-analyse is momenteel alleen beschikbaar in de Engelse taal.

> [!NOTE]
> U wordt aangeraden de micro soft-Text Analytics-API te gebruiken. Het biedt meer geavanceerde functies dan sentiment analyse, zoals extractie van sleutel zinnen, automatische taal detectie en nog veel meer. U vindt informatie en voor beelden in de [Text Analytics documentatie](https://azure.microsoft.com/services/cognitive-services/text-analytics/).
>

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

De transcriptie-service kan een groot aantal verzonden transcripties verwerken. U kunt de status van uw transcripties opvragen via a `GET` op de [methode transcripties](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Bewaar de gegevens naar een redelijke grootte door de `take` para meter op te geven (enkele honderden). [Verwijder transcripties](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regel matig van de service zodra u de resultaten hebt opgehaald. Dit garandeert snelle antwoorden van de transcriptie-beheer aanroepen.

## <a name="sample-code"></a>Voorbeeldcode

Volledige voor beelden zijn beschikbaar in de [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples) in de `samples/batch` submap.

U moet de voorbeeld code aanpassen met uw abonnements gegevens, de service regio, de SAS-URI die verwijst naar het audio bestand en model-Id's voor het geval u een aangepast akoestische of taal model wilt gebruiken.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

De voorbeeld code stelt de-client in en verzendt de transcriptie-aanvraag. Vervolgens wordt een poll uitgevoerd voor de status informatie en worden details over de voortgang van de transcriptie afgedrukt.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Zie het [Swagger-document](https://westus.cris.ai/swagger/ui/index)voor volledige informatie over de voor gaande aanroepen. Voor het volledige voor beeld dat hier wordt weer [GitHub](https://aka.ms/csspeech/samples) gegeven, gaat `samples/batch` u naar github in de submap.

Noteer de asynchrone instellingen voor het posten van audio en het ontvangen van de transcriptie-status. De client die u maakt, is een .NET HTTP-client. Er is een `PostTranscriptions` methode voor het verzenden van de details van het `GetTranscriptions` audio bestand en een methode voor het ontvangen van de resultaten. `PostTranscriptions`retourneert een ingang en `GetTranscriptions` gebruikt deze om een ingang te maken om de status van transcriptie op te halen.

In de huidige voorbeeld code is geen aangepast model opgegeven. De service maakt gebruik van de basis modellen voor het transcriberen van het bestand of de bestanden. Als u de modellen wilt opgeven, kunt u op dezelfde manier door geven als de model-Id's voor de akoestische en het taal model.

> [!NOTE]
> Voor basislijn transcripties hoeft u de ID voor de basislijn modellen niet te declareren. Als u alleen een taal model-ID opgeeft (en geen akoestische model-ID), wordt er automatisch een overeenkomstig akoestische model geselecteerd. Als u alleen een akoestische model-ID opgeeft, wordt automatisch een overeenkomend taal model geselecteerd.

## <a name="download-the-sample"></a>Het voorbeeld downloaden

U kunt het voor beeld in de `samples/batch` directory vinden in de [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
