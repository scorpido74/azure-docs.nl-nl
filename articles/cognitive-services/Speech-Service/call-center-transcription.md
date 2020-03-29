---
title: Call Center Transcriptie - Spraakservice
titleSuffix: Azure Cognitive Services
description: Een veelvoorkomend scenario voor spraak-naar-tekst is het transcriberen van grote hoeveelheden telefoniegegevens die afkomstig zijn van verschillende systemen, zoals Interactive Voice Response (IVR). Met behulp van spraakservice en het Unified speech-model kan een bedrijf transcripties van hoge kwaliteit krijgen met audio-opnamesystemen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d959f4948d6b848f3b399c1310add06991d72012
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806317"
---
# <a name="speech-service-for-telephony-data"></a>Spraakservice voor telefoniegegevens

Telefoniegegevens die worden gegenereerd via vaste lijnen, mobiele telefoons en radio's zijn doorgaans van lage kwaliteit en een smalle band in het bereik van 8 KHz, wat uitdagingen creëert bij het converteren van spraak naar tekst. De nieuwste spraakherkenningsmodellen van de Spraakdienst blinken uit in het transcriberen van deze telefoniegegevens, zelfs in gevallen waarin de gegevens voor een mens moeilijk te begrijpen zijn. Deze modellen zijn getraind met grote hoeveelheden telefoniegegevens en hebben de beste nauwkeurigheid van de marktherkenning, zelfs in rumoerige omgevingen.

Een veelvoorkomend scenario voor spraak-naar-tekst is het transcriberen van grote hoeveelheden telefoniegegevens die afkomstig kunnen zijn van verschillende systemen, zoals Interactive Voice Response (IVR). De audio die deze systemen bieden kan stereo of mono, en rauw met weinig-tot-geen nabewerking gedaan op het signaal. Met behulp van de Spraakservice en het Unified speech-model kan een bedrijf transcripties van hoge kwaliteit krijgen, ongeacht systemen die worden gebruikt om audio vast te leggen.

Telefoniegegevens kunnen worden gebruikt om de behoeften van uw klanten beter te begrijpen, nieuwe marketingkansen te identificeren of de prestaties van callcentermedewerkers te evalueren. Nadat de gegevens zijn getranscribeerd, kan een bedrijf de uitvoer gebruiken voor doeleinden zoals verbeterde telemetrie, het identificeren van sleutelzinnen of het analyseren van het sentiment van klanten.

De technologieën die op deze pagina worden beschreven, zijn intern door Microsoft voor verschillende ondersteuningsoproepverwerkingsservices, zowel in realtime als in batchmodus.

Laten we eens kijken naar een aantal van de technologie en aanverwante functies die de Speech-service biedt.

> [!IMPORTANT]
> Het Unified-model van spraakservice is getraind met diverse gegevens en biedt een oplossing voor één model voor een aantal scenario's, van dicteren tot telefonie-analyse.

## <a name="azure-technology-for-call-centers"></a>Azure-technologie voor callcenters

Naast het functionele aspect van de Spraakservicefuncties is hun primaire doel – wanneer ze worden toegepast op het callcenter – het verbeteren van de klantervaring. Er bestaan in dit verband drie duidelijke domeinen:

- Post-call analytics, dat is in wezen batch verwerking van gesprekopnames na het gesprek.
- Real-time analytics, dat is de verwerking van het audiosignaal om verschillende inzichten te extraheren als het gesprek plaatsvindt (met sentiment wordt een prominente use case).
- Spraakassistenten (bots), ofwel het besturen van de dialoog tussen de klant en de bot in een poging om het probleem van de klant op te lossen zonder agent deelname, of wordt de toepassing van kunstmatige intelligentie (AI) protocollen om de agent te helpen.

Een typisch architectuurdiagram van de implementatie van een batchscenario wordt weergegeven in de afbeelding hieronder ![De transcriptiearchitectuur van het callcenter](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Spraakanalyse-technologiecomponenten

Of het domein nu post-call of real-time is, Azure biedt een reeks volwassen en opkomende technologieën om de klantervaring te verbeteren.

### <a name="speech-to-text-stt"></a>Toespraak tot tekst (STT)

[Spraak-naar-tekst](speech-to-text.md) is de meest gewilde functie in elke call center-oplossing. Omdat veel van de downstream analytics processen afhankelijk zijn van getranscribeerde tekst, is het woord error rate _(WER)_ van het grootste belang. Een van de belangrijkste uitdagingen in call center transcriptie is het geluid dat heerst in het call center (bijvoorbeeld andere agenten spreken op de achtergrond), de rijke verscheidenheid aan taal locales en dialecten, alsmede de lage kwaliteit van de werkelijke telefoonsignaal. WER is sterk gecorreleerd met hoe goed de akoestische en taalmodellen zijn opgeleid voor een bepaalde locale, dus de mogelijkheid om het model aan te passen aan uw landis belangrijk. Onze nieuwste Unified versie 4.x modellen zijn de oplossing voor zowel transcriptie nauwkeurigheid en latentie. Opgeleid met tienduizenden uren aan akoestische gegevens en miljarden lexicale informatie, zijn Unified-modellen de meest nauwkeurige modellen in de markt om callcentergegevens te transcriberen.

### <a name="sentiment"></a>Sentiment

Het meten of de klant een goede ervaring had, is een van de belangrijkste gebieden van spraakanalyse wanneer deze wordt toegepast op de callcenterruimte. Onze [Batch Transcription API](batch-transcription.md) biedt sentimentanalyse per utterance. U de set waarden die zijn verkregen als onderdeel van een gesprekstranscript samenvoegen om het gevoel van de oproep voor zowel uw medewerkers als de klant te bepalen.

### <a name="silence-non-talk"></a>Stilte (non-talk)

Het is niet ongewoon dat 35 procent van een ondersteuningsoproep is wat we non-talktijd noemen. Sommige scenario's waarvoor niet-praten plaatsvindt zijn: agenten die eerdere casegeschiedenis opzoeken met een klant, agenten die tools gebruiken waarmee ze toegang hebben tot het bureaublad van de klant en functies kunnen uitvoeren, klanten die in de wacht zitten te wachten op een overdracht, enzovoort. Het is uiterst belangrijk om te meten wanneer stilte optreedt in een gesprek, omdat er een aantal belangrijke klantgevoeligheden zijn die zich voordoen rond dit soort scenario's en waar ze optreden in de oproep.

### <a name="translation"></a>Omzetting

Sommige bedrijven experimenteren met het verstrekken van vertaalde transcripties van ondersteuning seinen in vreemde talen, zodat bezorgers de wereldwijde ervaring van hun klanten kunnen begrijpen. Onze [vertaalmogelijkheden](translation.md) zijn onovertroffen. We kunnen audio-naar-audio of audio-naar-tekst vertalen voor een groot aantal landinstellingen.

### <a name="text-to-speech"></a>Tekst naar spraak

[Text-to-speech](text-to-speech.md) is een ander belangrijk gebied bij het implementeren van bots die communiceren met de klanten. Het typische pad is dat de klant spreekt, zijn stem wordt getranscribeerd naar tekst, de tekst wordt geanalyseerd op intents, een reactie wordt gesynthetiseerd op basis van de erkende intentie en vervolgens wordt een asset opgedoken aan de klant of een gesynthetiseerde stemrespons is Gegenereerd. Natuurlijk moet dit alles snel gebeuren – dus low-latency is een belangrijk onderdeel van het succes van deze systemen.

Onze end-to-end latentie is aanzienlijk laag voor de verschillende betrokken technologieën, zoals [Speech-to-text](speech-to-text.md), [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [Bot Framework](https://dev.botframework.com/), [Text-to-speech](text-to-speech.md).

Onze nieuwe stemmen zijn ook niet te onderscheiden van menselijke stemmen. U onze stemmen gebruiken om uw bot zijn unieke persoonlijkheid te geven.

### <a name="search"></a>Search

Een ander onderdeel van analytics is het identificeren van interacties waar een specifieke gebeurtenis of ervaring heeft plaatsgevonden. Dit wordt meestal gedaan met een van de twee benaderingen; ofwel een ad hoc zoekopdracht waarbij de gebruiker gewoon een woordgroep typt en het systeem reageert, of een meer gestructureerde query waarbij een analist een set logische instructies kan maken die een scenario in een gesprek identificeren, en vervolgens kan elke oproep worden geïndexeerd aan de hand van die set query's. Een goed zoekvoorbeeld is de alomtegenwoordige nalevingsverklaring "deze oproep wordt opgenomen voor kwaliteitsdoeleinden... ". Veel bedrijven willen ervoor zorgen dat hun agenten deze disclaimer aan klanten verstrekken voordat het gesprek daadwerkelijk wordt opgenomen. De meeste analysesystemen hebben de mogelijkheid om het gedrag van query-/zoekalgoritmen te trenden, en deze rapportage van trends is uiteindelijk een van de belangrijkste functies van een analysesysteem. Via [Cognitive services directory](https://azure.microsoft.com/services/cognitive-services/directory/search/) kan uw end-to-end oplossing aanzienlijk worden verbeterd met indexering en zoekmogelijkheden.

### <a name="key-phrase-extraction"></a>Sleuteltermextractie

Dit gebied is een van de meer uitdagende analytics-toepassingen en een die profiteert van de toepassing van AI en machine learning. Het primaire scenario in dit geval is om de intentie van de klant af te leiden. Waarom belt de klant? Wat is het probleem van de klant? Waarom had de klant een negatieve ervaring? Onze [tekstanalyseservice](https://azure.microsoft.com/services/cognitive-services/text-analytics/) biedt een set analyses uit de doos voor het snel upgraden van uw end-to-end oplossing voor het extraheren van die belangrijke zoekwoorden of zinnen.

Laten we nu eens kijken naar de batch verwerking en de real-time pijplijnen voor spraakherkenning in een beetje meer detail.

## <a name="batch-transcription-of-call-center-data"></a>Batch transcriptie van call center data

Voor het transcriberen van bulkaudio hebben we de [Batch Transcription API](batch-transcription.md)ontwikkeld. De Batch Transcription API is ontwikkeld om grote hoeveelheden audiogegevens asynchroon te transcriberen. Met betrekking tot het transcriberen van callcentergegevens is onze oplossing gebaseerd op deze pijlers:

- **Nauwkeurigheid** - Met de vierde generatie Unified-modellen bieden we ongeëvenaarde transcriptiekwaliteit.
- **Latentie** - We begrijpen dat bij het doen van bulk transcripties, de transcripties snel nodig zijn. De transcriptietaken die via de [Batch Transcription API](batch-transcription.md) worden gestart, worden onmiddellijk in de wachtrij geplaatst en zodra de taak wordt uitgevoerd, wordt deze sneller uitgevoerd dan realtime transcriptie.
- **Beveiliging** - We begrijpen dat oproepen gevoelige gegevens kunnen bevatten. Wees gerust dat veiligheid een van onze hoogste prioriteiten is. Onze service heeft ISO, SOC, HIPAA, PCI certificeringen verkregen.

Call centers genereren dagelijks grote hoeveelheden audiodata. Als uw bedrijf telefoniegegevens opslaat op een centrale locatie, zoals Azure Storage, u de [Batch Transcription API](batch-transcription.md) gebruiken om asynchroon transcripties op te vragen en te ontvangen.

Een typische oplossing maakt gebruik van deze services:

- De spraakservice wordt gebruikt om spraak-naar-tekst te transcriberen. Een standaardabonnement (S0) voor de Spraakservice is vereist om de Batch Transcription API te gebruiken. Gratis abonnementen (F0) werken niet.
- [Azure Storage](https://azure.microsoft.com/services/storage/) wordt gebruikt om telefoniegegevens op te slaan en de transcripties die worden geretourneerd door de Batch Transcription API. Dit opslagaccount moet meldingen gebruiken, specifiek voor wanneer nieuwe bestanden worden toegevoegd. Deze meldingen worden gebruikt om het transcriptieproces te activeren.
- [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) wordt gebruikt om de SAS-uri (Shared Access Signatures) voor elke opname te maken en het HTTP POST-verzoek te activeren om een transcriptie te starten. Daarnaast wordt Azure Functions gebruikt om aanvragen te maken voor het ophalen en verwijderen van transcripties met behulp van de Batch Transcription API.

Intern gebruiken we de bovenstaande technologieën om oproepen van Microsoft-klanten te ondersteunen in de batchmodus.
![Batcharchitectuur](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Real-time transcriptie voor callcentergegevens

Sommige bedrijven zijn verplicht om gesprekken in realtime te transcriberen. Real-time transcriptie kan worden gebruikt om trefwoorden te identificeren en zoekopdrachten te activeren naar inhoud en bronnen die relevant zijn voor het gesprek, om sentiment te monitoren, om de toegankelijkheid te verbeteren of om vertalingen te leveren aan klanten en agenten die niet native zijn Sprekers.

Voor scenario's waarvoor realtime transcriptie vereist is, raden we u aan de [SpraakSDK te](speech-sdk.md)gebruiken. Momenteel is spraak-naar-tekst beschikbaar in [meer dan 20 talen](language-support.md)en is de SDK beschikbaar in C++, C#, Java, Python, Node.js, Objective-C en JavaScript. Voorbeelden zijn beschikbaar in elke taal op [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk) Zie [Release notes voor](releasenotes.md)het laatste nieuws en updates.

Intern gebruiken we de bovenstaande technologieën om in realtime microsoft-klantoproepen te analyseren, zoals geïllustreerd in het volgende diagram.

![Batcharchitectuur](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Een woord over IVRs

De Spraakservice kan eenvoudig in elke oplossing worden geïntegreerd met behulp van de [Speech SDK](speech-sdk.md) of de [REST API.](rest-apis.md) De transcriptie van het callcenter vereist echter mogelijk aanvullende technologieën. Doorgaans is een verbinding tussen een IVR-systeem en Azure vereist. Hoewel we dergelijke componenten niet aanbieden, is hier een beschrijving wat een verbinding met een IVR inhoudt.

Verschillende IVR- of telefonieserviceproducten (zoals Genesys of AudioCodes) bieden integratiemogelijkheden die kunnen worden benut om inkomende en uitgaande audiodoorvoer naar een Azure-service mogelijk te maken. In principe kan een aangepaste Azure-service een specifieke interface bieden om telefoongesprekkensessies te definiëren (zoals Start van oproepen of oproepeinde) en een WebSocket-API bloot te stellen om binnenkomende streamaudio te ontvangen die wordt gebruikt met de spraakservice. Uitgaande antwoorden, zoals gesprekstranscriptie of verbindingen met het Bot Framework, kunnen worden gesynthetiseerd met de tekst-naar-spraakservice van Microsoft en worden teruggestuurd naar de IVR voor afspelen.

Een ander scenario is directe integratie met Session Initiation Protocol (SIP). Een Azure-service maakt verbinding met een SIP-server, waardoor een binnenkomende stream en een uitgaande stream worden uitgevoerd, die wordt gebruikt voor de spraak-naar-tekst- en tekst-naar-spraak-fasen. Om verbinding te maken met een SIP Server zijn er commerciële softwareaanbiedingen, zoals Ozeki SDK, of [de Teams calling and meetings API](/graph/api/resources/communications-api-overview) (momenteel in bèta), die zijn ontworpen om dit type scenario voor audiogesprekken te ondersteunen.

## <a name="customize-existing-experiences"></a>Bestaande ervaringen aanpassen

 De Spraakservice werkt goed met ingebouwde modellen. Het is echter mogelijk dat u de ervaring verder wilt aanpassen en afstemmen op uw product of omgeving. Aanpassingsopties variëren van akoestische modeltuning tot unieke spraaklettertypen voor uw merk. Nadat u een aangepast model hebt gemaakt, u het gebruiken met een van de spraakservicefuncties in realtime of batchmodus.

| Speech Service | Model | Beschrijving |
| -------------- | ----- | ----------- |
| Spraak naar tekst | [Akoestisch model](how-to-customize-acoustic-models.md) | Maak een aangepast akoestisch model voor toepassingen, gereedschappen of apparaten die worden gebruikt in bepaalde omgevingen, zoals in een auto of op een fabrieksvloer, elk met specifieke opnameomstandigheden. Voorbeelden hiervan zijn geaccentueerde spraak, specifieke achtergrondgeluiden of het gebruik van een specifieke microfoon voor opname. |
|                | [Taalmodel](how-to-customize-language-model.md) | Maak een aangepast taalmodel om de transcriptie van branchespecifieke woordenschat en grammatica, zoals medische terminologie of IT-jargon, te verbeteren. |
|                | [Uitspraakmodel](how-to-customize-pronunciation.md) | Met een aangepast uitspraakmodel u de fonetische vorm definiëren en voor een woord of term weergeven. Het is handig voor het verwerken van aangepaste termen, zoals productnamen of afkortingen. Alles wat je nodig hebt om te beginnen `.txt` is een uitspraak bestand, dat is een eenvoudig bestand. |
| Tekst naar spraak | [Spraakstijl](how-to-customize-voice-font.md) | Met aangepaste spraaklettertypen u een herkenbare, unieke stem voor uw merk maken. Er is slechts een kleine hoeveelheid gegevens nodig om aan de slag te gaan. Hoe meer gegevens u verstrekt, hoe natuurlijker en menselijker uw spraaklettertype klinkt. |

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeldcode is beschikbaar op GitHub voor elk van de spraakservicefuncties. Deze voorbeelden hebben betrekking op veelvoorkomende scenario's zoals het lezen van audio uit een bestand of stream, continue en single-shot herkenning en het werken met aangepaste modellen. Gebruik deze koppelingen om SDK- en REST-voorbeelden weer te geven:

- [Voorbeelden van spraak-naar-tekst- en spraakvertaling (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch transcriptie monsters (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Tekst-naar-spraakvoorbeelden (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referentiedocumenten

- [Speech-SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST-API: spraak-naar-tekst](rest-speech-to-text.md)
- [REST API: Tekst-naar-spraak](rest-text-to-speech.md)
- [REST API: Batch transcriptie en aanpassing](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ontvang gratis een abonnementssleutel voor spraakservice](get-started.md)
