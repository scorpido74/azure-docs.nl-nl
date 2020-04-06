---
title: Wat is batchtranscriptie - Spraakservice
titleSuffix: Azure Cognitive Services
description: Batchtranscriptie is ideaal als u een grote hoeveelheid audio wilt transcriberen in opslag, zoals Azure Blobs. Met behulp van de speciale REST API u audiobestanden met een sas-uri (shared access signature) aanwijzen en asynchroon transcripties ontvangen.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: fb39f1ec83416ee8ab2a33b514971110db0c0b17
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668841"
---
# <a name="what-is-batch-transcription"></a>Wat is batchtranscriptie?

Batchtranscriptie is een set REST API-bewerkingen waarmee u een grote hoeveelheid audio in opslag transcriberen. U audiobestanden met een uri met gedeelde toegangshandtekening (SAS) aanwijzen en asynchroon transcriptieresultaten ontvangen.

Asynchrone spraak-naar-tekst transcriptie is slechts een van de functies. U API's voor batchtranscriptieREST gebruiken om de volgende methoden aan te roepen:



|    Batchtranscriptiebewerking                                             |    Methode    |    REST API-aanroep                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Hiermee maakt u een nieuwe transcriptie.                                              |    POST      |    api/speechtotext/v2.0/transcripties            |
|    Hiermee haalt u een lijst met transcripties op voor het geverifieerde abonnement.    |    GET       |    api/speechtotext/v2.0/transcripties            |
|    Krijgt een lijst met ondersteunde landlocaties voor offline transcripties.              |    GET       |    api/speechtotext/v2.0/transcripties/landheden    |
|    Hiermee worden de veranderlijke details van de transcriptie bijgewerkt die door de id wordt geïdentificeerd.    |    Patch     |    api/speechtotext/v2.0/transcripties/{id}       |
|    Hiermee verwijdert u de opgegeven transcriptietaak.                                 |    DELETE    |    api/speechtotext/v2.0/transcripties/{id}       |
|    Hiermee wordt de transcriptie geïdentificeerd door de opgegeven ID.                        |    GET       |    api/speechtotext/v2.0/transcripties/{id}       |




U de gedetailleerde API, die beschikbaar is als [Swagger-document,](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)onder het kopje `Custom Speech transcriptions`bekijken en testen.

Batch transcriptie taken zijn gepland op een best effort basis. Momenteel is er geen schatting voor wanneer een taak verandert in de lopende status. Onder normale systeembelasting moet dit binnen enkele minuten gebeuren. Eenmaal in de loopstatus wordt de werkelijke transcriptie sneller verwerkt dan de audioreal-time.

Naast de gebruiksvriendelijke API hoeft u geen aangepaste eindpunten te implementeren en hebt u geen gelijktijdigheidsvereisten om u in acht te nemen.

## <a name="prerequisites"></a>Vereisten

### <a name="subscription-key"></a>Abonnementssleutel

Zoals bij alle functies van de Spraakservice maakt u een abonnementssleutel van de [Azure-portal](https://portal.azure.com) door onze [gids Aan de slag te volgen.](get-started.md)

>[!NOTE]
> Een standaardabonnement (S0) voor spraakservice is vereist om batchtranscriptie te gebruiken. Gratis abonnementssleutels (F0) werken niet. Zie [prijzen en limieten voor](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)meer informatie .

### <a name="custom-models"></a>Aangepaste modellen

Als u akoestische of taalmodellen wilt aanpassen, volgt u de stappen in [Akoestische modellen aanpassen](how-to-customize-acoustic-models.md) en [taalmodellen voor aanpassen van het aanpassen](how-to-customize-language-model.md)van afbeeldingen . Als u de gemaakte modellen wilt gebruiken in batchtranscriptie, hebt u hun model-id's nodig. U de model-id ophalen wanneer u de details van het model inspecteert. Een geïmplementeerd aangepast eindpunt is niet nodig voor de batchtranscriptieservice.

## <a name="the-batch-transcription-api"></a>De Batch Transcription API

### <a name="supported-formats"></a>Ondersteunde indelingen

De Batch Transcription API ondersteunt de volgende indelingen:

| Indeling | Codec | Bitrate | Voorbeeldsnelheid                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-bits  | 8 kHz of 16 kHz, mono of stereo |
| Mp3    | PCM   | 16-bits  | 8 kHz of 16 kHz, mono of stereo |
| Ogg    | Opus  | 16-bits  | 8 kHz of 16 kHz, mono of stereo |

Voor stereoaudiostreams worden de linker- en rechterkanalen tijdens de transcriptie gesplitst. Voor elk kanaal wordt een JSON-resultaatbestand gemaakt. Met de tijdstempels die per utterance worden gegenereerd, kan de ontwikkelaar een geordend definitief transcript maken.

### <a name="configuration"></a>Configuratie

Configuratieparameters worden geleverd als JSON:

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
      **Parameter**
   :::column-end:::
   :::column span="2":::
      **Beschrijving**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Hiermee geeft u op hoe met godslastering in herkenningsresultaten moet worden omgaat. Geaccepteerde `None` waarden zijn het uitschakelen `Masked` van godslastering filteren, om godslastering `Removed` te vervangen door sterretjes, `Tags` om alle godslastering te verwijderen uit het resultaat, of om "godslastering" tags toe te voegen. De standaardinstelling is `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Hiermee geeft u op hoe u interpunctie verwerkt in herkenningsresultaten. Geaccepteerde `None` waarden zijn het `Dictated` uitschakelen van interpunctie, het `Automatic` impliceren van expliciete (gesproken) interpunctie, het laten behandelen van de decoder met interpunctie, of `DictatedAndAutomatic` het gebruik van gedicteerde en automatische interpunctie. De standaardinstelling is `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Hiermee geeft u op of woordniveautijdstempels aan de uitvoer moeten worden toegevoegd. Geaccepteerde `true` waarden moeten woordniveautijdstempels inschakelen en `false` (de standaardwaarde) deze uitschakelen.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Hiermee geeft u op of sentimentanalyse moet worden toegepast op de utterance. Geaccepteerde `true` waarden moeten `false` deze inschakelen en (de standaardwaarde) uitschakelen.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Hiermee geeft u aan dat diarisatieanalyse moet worden uitgevoerd op de invoer, die naar verwachting monokanaal is met twee stemmen. Geaccepteerde `true` waarden maken diarisatie mogelijk en `false` (de standaardwaarde) om deze uit te schakelen. Het moet `AddWordLevelTimestamps` ook worden ingesteld op waar.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Optionele URL met [service SAS](../../storage/common/storage-sas-overview.md) naar een schrijfbare container in Azure. Het resultaat wordt opgeslagen in deze container.
:::row-end:::

### <a name="storage"></a>Storage

Batchtranscriptie ondersteunt [Azure Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) voor het lezen van audio en het schrijven van transcripties naar opslag.

## <a name="the-batch-transcription-result"></a>Het resultaat van de batchtranscriptie

Voor mono-invoeraudio wordt één transcriptieresultaatbestand gemaakt. Voor stereo-invoeraudio worden twee transcriptieresultatenbestanden gemaakt. Elk heeft deze structuur:

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

| Formulier        | Inhoud                                                                                                                                                  |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Lexical`   | De werkelijke woorden herkend.                                                                                                                             |
| `ITN`       | Omgekeerde tekst-genormaliseerde vorm van de erkende tekst. Afkortingen ("dokter Smith" naar "dr Smith"), telefoonnummers en andere transformaties worden toegepast. |
| `MaskedITN` | De ITN-vorm met godslastering maskeren toegepast.                                                                                                             |
| `Display`   | De weergavevorm van de herkende tekst. Toegevoegde interpunctie en hoofdletters zijn inbegrepen.                                                             |

## <a name="speaker-separation-diarization"></a>Luidsprekerscheiding (Diarisatie)

Diarisatie is het proces van het scheiden van luidsprekers in een stukje audio. Onze Batch-pijplijn ondersteunt diarisatie en is in staat om twee luidsprekers op mono-kanaalopnames te herkennen. De functie is niet beschikbaar op stereo-opnamen.

Alle transcriptie-uitvoer `SpeakerId`bevat een . Als diarisatie niet wordt `"SpeakerId": null` gebruikt, wordt dit weergegeven in de JSON-uitvoer. Voor diarisatie ondersteunen we twee stemmen, zodat `"1"` `"2"`de sprekers worden geïdentificeerd als of .

Om diarisatie aan te vragen, hoeft u alleen de relevante parameter toe te voegen aan de HTTP-aanvraag zoals hieronder weergegeven.

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

Word-level tijdstempels zouden ook moeten worden 'ingeschakeld' zoals de parameters in de bovenstaande aanvraag aangeven.

## <a name="sentiment-analysis"></a>Sentimentanalyse

De sentimentfunctie schat het sentiment dat in de audio wordt uitgedrukt. Het sentiment wordt uitgedrukt door een `Negative`waarde `Neutral`tussen `Positive` 0 en 1 voor , en sentiment. Sentimentanalyse kan bijvoorbeeld worden gebruikt in callcenterscenario's:

- Krijg inzicht in klanttevredenheid
- Krijg inzicht in de prestaties van de agenten (team dat de gesprekken aanneemt)
- Het exacte tijdstip vinden waarop een oproep een negatieve richting heeft ingeslagen
- Wat ging goed bij het draaien van een negatieve oproep in een positieve richting
- Identificeren wat klanten leuk vinden en wat ze niet leuk vinden aan een product of een service

Sentiment wordt gescoord per audiosegment op basis van de lexicale vorm. De volledige tekst binnen dat audiosegment wordt gebruikt om het sentiment te berekenen. Er wordt geen geaggregeerd sentiment berekend voor de volledige transcriptie. Momenteel is sentimentanalyse alleen beschikbaar voor de Engelse taal.

Een JSON-uitvoervoorbeeld ziet er hieronder uit:

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

De transcriptieservice kan een groot aantal ingediende transcripties verwerken. U de status van uw `GET` transcripties opvragen via een op de [transcriptiemethode.](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions) Houd de geretourneerde informatie op een `take` redelijke grootte door de parameter (een paar honderd) op te geven. [Verwijder regelmatig transcripties](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) uit de service zodra u de resultaten hebt opgehaald. Dit garandeert snelle antwoorden van de transcriptie beheer oproepen.

## <a name="sample-code"></a>Voorbeeldcode

Volledige voorbeelden zijn beschikbaar in de [GitHub-monsteropslagplaats](https://aka.ms/csspeech/samples) in de `samples/batch` submap.

U moet de voorbeeldcode aanpassen met uw abonnementsgegevens, het servicegebied, de SAS URI die naar het audiobestand wijst dat u wilt transcriberen en id's modelleren voor het geval u een aangepast akoestisch of taalmodel wilt gebruiken.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

De voorbeeldcode stelt de client in en dient de transcriptieaanvraag in. Vervolgens wordt de statusinformatie en de afdrukgegevens over de voortgang van de transcriptie afgedrukt.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Zie ons [Swagger-document](https://westus.cris.ai/swagger/ui/index)voor meer informatie over de voorgaande gesprekken. Ga voor het volledige voorbeeld dat hier `samples/batch` wordt getoond naar [GitHub](https://aka.ms/csspeech/samples) in de submap.

Let op de asynchrone installatie voor het plaatsen van audio en het ontvangen van transcriptiestatus. De client die u maakt, is een .NET HTTP-client. Er is `PostTranscriptions` een methode voor het verzenden `GetTranscriptions` van de audiobestandsgegevens en een methode voor het ontvangen van de resultaten. `PostTranscriptions`retourneert een `GetTranscriptions` handgreep en gebruikt deze om een greep te maken om de transcriptiestatus te krijgen.

De huidige voorbeeldcode geeft geen aangepast model op. De service gebruikt de basislijnmodellen voor het transcriberen van het bestand of bestanden. Als u de modellen wilt opgeven, u dezelfde methode doorgeven als de model-id's voor de akoestische en het taalmodel.

> [!NOTE]
> Voor basislijntranscripties hoeft u de ID voor de basislijnmodellen niet te declareren. Als u alleen een taalmodel-ID (en geen akoestische model-ID) opgeeft, wordt automatisch een bijpassend akoestisch model geselecteerd. Als u alleen een akoestische model-ID opgeeft, wordt automatisch een overeenkomend taalmodel geselecteerd.

## <a name="download-the-sample"></a>Het voorbeeld downloaden

U het voorbeeld `samples/batch` vinden in de map in de [GitHub-voorbeeldopslagplaats.](https://aka.ms/csspeech/samples)

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
