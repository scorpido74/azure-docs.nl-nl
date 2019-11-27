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
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 158a99b1691e59fa58207f3c9291ca9d37a6679c
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538121"
---
# <a name="why-use-batch-transcription"></a>Waarom Batch transcriptie gebruiken?

Batch transcriptie is ideaal als u wilt dat een grote hoeveelheid audio in opslag, zoals Azure Blobs transcriberen. U kunt met behulp van de toegewezen REST-API, wijst u audio-bestanden met een shared access signature (SAS) URI en asynchroon ontvangen transcripties.

## <a name="prerequisites"></a>Vereisten

### <a name="subscription-key"></a>Abonnements sleutel

Net als bij alle functies van de speech-service maakt u een abonnements sleutel van de [Azure Portal](https://portal.azure.com) door de [aan de slag-hand leiding](get-started.md)te volgen. Als u van plan bent om op te halen transcripties van onze basislijn-modellen, is het maken van een sleutel alles die wat u nodig om te doen.

>[!NOTE]
> Een standard-abonnement (S0) voor Speech Services is vereist voor het gebruik van batch transcriptie. Gratis abonnementssleutels (F0) werkt niet. Zie [prijzen en limieten](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)voor meer informatie.

### <a name="custom-models"></a>Aangepaste modellen

Volg de stappen in [akoestische modellen aanpassen](how-to-customize-acoustic-models.md) en [taal modellen](how-to-customize-language-model.md)aanpassen als u van plan bent om akoestische of taal modellen aan te passen. Als u de gemaakte modellen in batch transcriptie wilt gebruiken, hebt u hun model-Id's nodig. Deze ID is niet de eind punt-ID die u vindt in de detail weergave van het eind punt. Dit is de model-ID die u kunt ophalen wanneer u de details van de modellen selecteert.

## <a name="the-batch-transcription-api"></a>De Batch-transcriptie API

De Batch-API voor transcriptie biedt asynchrone transcriptie van spraak naar tekst, samen met extra functies. Er is een REST-API die methoden voor het beschrijft:

1. Het maken van batch verwerken van aanvragen
1. Querystatus
1. Transcripties downloaden

> [!NOTE]
> De Batch-API voor transcriptie is ideaal voor callcenters, die meestal worden verzameld duizenden uur aan audio. Zo kunt u eenvoudig grote volumes audio-opnames transcriberen.

### <a name="supported-formats"></a>Ondersteunde indelingen

De Batch-API voor transcriptie ondersteunt de volgende indelingen:

| Indeling | Codec | Bitrate | Samplefrequentie |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bits | 8 of 16 kHz, mono, stereo |
| MP3 | PCM | 16-bits | 8 of 16 kHz, mono, stereo |
| OGG | OPUS | 16-bits | 8 of 16 kHz, mono, stereo |

De Batch-transcriptie API wordt de linker- en -kanaal voor stereo audiostreams gesplitst tijdens de transcriptie. De twee JSON-bestanden met het resultaat zijn alle gemaakt van één kanaal. De tijdstempels per utterance bieden de ontwikkelaar te maken van een geordende definitieve transcriptie. Deze voorbeeld aanvraag bevat eigenschappen voor het filteren van woorden, interpunctie en woord niveau-time Stamps.

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
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> De Batch transcriptie-API maakt gebruik van een REST-service voor het aanvragen van transcripties, hun status en de bijbehorende resultaten. U kunt de API vanuit een willekeurige taal gebruiken. De volgende sectie wordt beschreven hoe de API wordt gebruikt.

### <a name="configuration-properties"></a>Configuratie-eigenschappen

Gebruik deze optionele eigenschappen om transcriptie te configureren:

| Parameter | Beschrijving |
|-----------|-------------|
| `ProfanityFilterMode` | Geeft aan hoe grof taalgebruik in herkenningsresultaten worden verwerkt. Geaccepteerde waarden zijn `None` waarmee de filter functie voor scheld woorden wordt uitgeschakeld, `masked` waardoor de woorden met sterretjes worden vervangen, `removed` waardoor alle scheld woorden uit het resultaat worden verwijderd, of `tags` waarmee de tags voor groveheid worden toegevoegd. De standaard instelling is `masked`. |
| `PunctuationMode` | Geeft aan hoe interpunctie in herkenningsresultaten worden verwerkt. Geaccepteerde waarden zijn `None` die interpunctie uitschakelt, `dictated` die expliciete interpunctie impliceert, `automatic` waarmee de decoder met interpunctie kan omgaan, of `dictatedandautomatic` die interpunctie tekens of automatisch aanduidt. |
 | `AddWordLevelTimestamps` | Hiermee wordt aangegeven of Time Stamps op woord niveau moeten worden toegevoegd aan de uitvoer. Geaccepteerde waarden zijn `true` die tijds tempels op woord niveau en `false` (de standaard waarde) in staat stelt om deze uit te scha kelen. |
 | `AddSentiment` | Hiermee geeft u sentiment moet worden toegevoegd aan de utterance. Geaccepteerde waarden zijn `true` die sentiment per utterance en `false` (de standaard waarde) in staat stelt om het uit te scha kelen. |
 | `AddDiarization` | Hiermee geeft u op dat diarization-analyse moet worden uitgevoerd op de invoer waarvan wordt verwacht dat deze een mono-kanaal met twee stemmen bevat. Geaccepteerde waarden zijn `true` die diarization en `false` (de standaard waarde) in staat stelt om het uit te scha kelen. Ook moet `AddWordLevelTimestamps` worden ingesteld op True.|

### <a name="storage"></a>Storage

Batch transcriptie ondersteunt [Azure Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) voor het lezen van audio en schrijven van transcripties naar opslag.

## <a name="speaker-separation-diarization"></a>Schei ding van de spreker (Diarization)

Diarization is het proces waarbij de luid sprekers in een audio fragment worden gescheiden. Onze batch pijplijn ondersteunt Diarization en kan twee luid sprekers herkennen aan mono-kanaal opnamen.

Als u wilt dat uw transcriptie-aanvraag wordt verwerkt voor diarization, hoeft u alleen de relevante para meter in de HTTP-aanvraag toe te voegen, zoals hieronder wordt weer gegeven.

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

De bijbehorende audio bevat de luid sprekers die worden geïdentificeerd met een nummer (momenteel worden er slechts twee stemmen ondersteund, zodat de luid sprekers worden aangeduid als ' spreker 1 ' en ' spreker 2 '), gevolgd door de transcriptie-uitvoer.

Houd er ook rekening mee dat Diarization niet beschikbaar is in stereo-opnamen. Daarnaast bevat alle JSON-uitvoer de luidspreker code. Als diarization niet wordt gebruikt, wordt ' luid spreker: null ' weer gegeven in de JSON-uitvoer.

> [!NOTE]
> Diarization is beschikbaar in alle regio's en voor alle land instellingen.

## <a name="sentiment"></a>Sentiment

Sentiment is een nieuwe functie in de batch transcriptie-API en is een belang rijk onderdeel van het Call Center-domein. Klanten kunnen de `AddSentiment`-para meters voor hun aanvragen gebruiken om

1.  Inzicht krijgen in klant tevredenheid
2.  Inzicht krijgen in de prestaties van de agents (team die de aanroepen nemen)
3.  Het precieze tijdstip waarop een gesprek een negatieve richting in een keer inschakelt, lokaliseren
4.  Zoek wat er goed is gegaan bij het omzetten van negatieve oproepen naar positieve
5.  Vaststellen wat klanten zijn en wat ze leuk vinden over een product of een service

Sentiment wordt beoordeeld per audio segment waarbij een audio segment is gedefinieerd als de tijd tussen het begin van de utterance (offset) en de stilte van het einde van de byte stroom. De volledige tekst in dat segment wordt gebruikt om sentiment te berekenen. We berekenen geen statistische sentiment-waarden voor de volledige aanroep of voor de hele spraak van elk kanaal. Deze aggregaties blijven van toepassing op de domein eigenaar om verder toe te passen.

Sentiment wordt toegepast op de lexicale vorm.

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
De functie maakt gebruik van een sentiment-model, dat zich momenteel in een bèta versie bevindt.

## <a name="sample-code"></a>Voorbeeldcode

Volledige voor beelden zijn beschikbaar in de [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples) in de submap `samples/batch`.

U moet de voorbeeld code aanpassen met uw abonnements gegevens, de service regio, de SAS-URI die verwijst naar het audio bestand en model-Id's voor het geval u een aangepast akoestische of taal model wilt gebruiken.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Met de voorbeeld code wordt de client ingesteld en wordt de transcriptie-aanvraag verzonden. Er wordt vervolgens gevraagd om status informatie en Details over de transcriptie-voortgang af te drukken.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Zie het [Swagger-document](https://westus.cris.ai/swagger/ui/index)voor volledige informatie over de voor gaande aanroepen. Voor het volledige voor beeld dat hier wordt weer gegeven, gaat u naar [github](https://aka.ms/csspeech/samples) in de submap `samples/batch`.

Noteer de asynchrone instellingen voor het plaatsen van audio en transcriptie status ontvangen. De client die u maakt, is een .NET-HTTP-client. Er is een `PostTranscriptions` methode voor het verzenden van de audio bestands Details en een `GetTranscriptions` methode voor het ontvangen van de resultaten. `PostTranscriptions` retourneert een handle en `GetTranscriptions` wordt gebruikt om een ingang te maken om de status van transcriptie op te halen.

De huidige voorbeeldcode opgeven niet een aangepast model. De service maakt gebruik van de basislijn-modellen voor te transcriberen van het bestand of de bestanden. U kunt doorgeven om op te geven de modellen, op dezelfde manier als de model-id voor de akoestische en het taalmodel.

> [!NOTE]
> Voor basislijn transcripties hoeft u de ID voor de basislijn modellen niet te declareren. Als u alleen een taal model-ID opgeeft (en geen akoestische model-ID), wordt er automatisch een overeenkomstig akoestische model geselecteerd. Als u alleen een akoestische model-ID opgeeft, wordt automatisch een overeenkomend taal model geselecteerd.

## <a name="download-the-sample"></a>Het voorbeeld downloaden

U kunt het voor beeld vinden in de map `samples/batch` in de [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples).

> [!NOTE]
> Batch transcriptie-taken worden gepland op basis van de beste inspanningen. er wordt geen tijd verwacht wanneer een taak wordt gewijzigd in de status actief. Zodra de status is uitgevoerd, wordt de daad werkelijke transcriptie sneller verwerkt dan de audio-real-time.

## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
