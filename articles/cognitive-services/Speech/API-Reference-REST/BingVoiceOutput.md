---
title: Text to Speech-API van micro soft Speech Service | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Gebruik de tekst-naar-spraak-API om realtime conversie van tekst naar spraak te bieden in verschillende stemmen en talen
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ee9b0b47fb88cba948bc06db6eb83fe9c076fe40
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966860"
---
# <a name="bing-text-to-speech-api"></a>Bing tekst-naar-spraak-API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Inleiding

Met de Bing tekst-naar-spraak-API kan uw toepassing HTTP-aanvragen verzenden naar een Cloud Server, waarbij tekst onmiddellijk wordt gesynthesizerd in menselijke klank spraak en wordt geretourneerd als een audio bestand. Deze API kan in veel verschillende contexten worden gebruikt voor het bieden van realtime conversie van tekst naar spraak in verschillende stemmen en talen.

## <a name="VoiceSynReq"></a>Voice synthese-aanvraag

### <a name="Subscription"></a>Autorisatie token

Voor elke voice Synthesis-aanvraag is een JSON Web Token (JWT)-toegangs token vereist. Het JWT-toegangs token wordt door gegeven in de header van de spraak aanvraag. Het token heeft een verloop tijd van 10 minuten. Zie [Cognitive Services-abonnement](https://azure.microsoft.com/try/cognitive-services/)voor meer informatie over het abonneren en verkrijgen van API-sleutels die worden gebruikt om geldige JWT-toegangs tokens op te halen.

De API-sleutel wordt door gegeven aan de token service. Bijvoorbeeld:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

De vereiste header-informatie voor token toegang is als volgt.

Name| Indeling | Description
----|----|----
OCP-Apim-Subscription-Key | ASCII | Uw abonnementssleutel

De token service retourneert het JWT-toegangs token `text/plain`als. Vervolgens wordt de JWT als een `Base64 access_token` aan het spraak eindpunt door gegeven als een autorisatie header die met de teken reeks `Bearer`wordt voorafgegaan. Bijvoorbeeld:

`Authorization: Bearer [Base64 access_token]`

Clients moeten het volgende eind punt gebruiken om toegang te krijgen tot de tekst-naar-spraak-service:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Totdat u een toegangs token hebt verkregen met uw abonnements sleutel zoals eerder beschreven, genereert deze koppeling een `403 Forbidden` reactie fout.

### <a name="Http"></a>HTTP-headers

De volgende tabel bevat de HTTP-headers die worden gebruikt voor aanvragen voor spraak synthese.

Header |Value |Opmerkingen
----|----|----
Content-Type | Application/SSML + XML | Het type invoer inhoud.
X-Microsoft-OutputFormat | **1.** SSML-16khz-16-mono-TTS <br> **2.** onbewerkte-16khz-16-mono-PCM <br>**3.** audio-16khz-16kbps-mono-Siren <br> **4.** RIFF-16khz-16kbps-mono-Siren <br> **5.** RIFF-16khz-16-mono-PCM <br> **6.** audio-16khz-128kbitrate-mono-mp3 <br> **7.** audio-16khz-64kbitrate-mono-mp3 <br> **8.** audio-16khz-32kbitrate-mono-mp3 | De uitvoer audio-indeling.
X-Search-AppId | Een GUID (alleen hex, geen streepjes) | Een ID waarmee de client toepassing uniek wordt geïdentificeerd. Dit kan de Store-ID voor apps zijn. Als er geen beschikbaar is, kan de ID door de gebruiker worden gegenereerd voor een toepassing.
X-Search-ClientID | Een GUID (alleen hex, geen streepjes) | Een ID waarmee een toepassings exemplaar voor elke installatie uniek wordt geïdentificeerd.
User-Agent | Toepassingsnaam | De naam van de toepassing is vereist en moet korter zijn dan 255 tekens.
Authorization | Autorisatietoken |  Zie de sectie <a href="#Subscription">autorisatie token</a> .

### <a name="InputParam"></a>Invoer parameters

Aanvragen voor de Bing tekst naar de spraak-API worden gemaakt met behulp van HTTP POST-aanroepen. De headers zijn opgegeven in de vorige sectie. De hoofd tekst bevat SSML-invoer (Speech synthese Markup Language) die de tekst vertegenwoordigt die moet worden gesynthesizerd. Zie de [SSML W3C-specificatie](https://www.w3.org/TR/speech-synthesis/)voor een beschrijving van de opmaak die wordt gebruikt voor het beheren van aspecten van spraak, zoals de taal en het geslacht van de spreker.

>[!NOTE]
>De maximale grootte van de SSML-invoer die wordt ondersteund, is 1.024 tekens, inclusief alle tags.

###  <a name="SampleVoiceOR"></a>Voor beeld: aanvraag voor spraak uitvoer

Een voor beeld van een aanvraag voor spraak uitvoer is als volgt:

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Antwoord op spraak uitvoer

De Bing tekst-naar-spraak-API maakt gebruik van HTTP POST om audio terug te sturen naar de client. Het API-antwoord bevat de audio stroom en de codec en komt overeen met de aangevraagde uitvoer indeling. Het geluid dat voor een bepaalde aanvraag wordt geretourneerd, mag niet langer zijn dan 15 seconden.

### <a name="SuccessfulRecResponse"></a>Voor beeld: geslaagde synthese reactie

De volgende code is een voor beeld van een JSON-antwoord op een geslaagde Voice Synthesis-aanvraag. De opmerkingen en opmaak van de code zijn alleen bedoeld voor dit voor beeld en worden wegge laten uit de daad werkelijke reactie.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Voor beeld: synthese-fout

In de volgende voorbeeld code ziet u een JSON-antwoord op een fout in de query voor een spraak synthese:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Fout berichten

Fout | Description
----|----
HTTP/400-ongeldige aanvraag | Een vereiste para meter ontbreekt, is leeg of null of de waarde die is door gegeven aan een vereiste of optionele para meter is ongeldig. Een van de redenen voor het ophalen van de reactie ' ongeldig ' is een teken reeks waarde door gegeven die langer is dan de toegestane lengte. Een korte beschrijving van de problematische para meter is opgenomen.
HTTP/401 niet toegestaan | De aanvraag is niet gemachtigd.
HTTP/413 RequestEntityTooLarge  | De SSML-invoer is groter dan wat wordt ondersteund.
HTTP/502 BadGateway | Er is een probleem met het netwerk of een probleem aan de server zijde.

Een voor beeld van een fout bericht is als volgt:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>De spraak uitvoer wijzigen via SSML

De API voor tekst naar spraak van micro soft ondersteunt SSML 1,0 zoals gedefinieerd in de W3C [-versie 1,0 (Speech synthese Markup Language)](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). In deze sectie vindt u voor beelden van het wijzigen van bepaalde kenmerken van gegenereerde spraak uitvoer, zoals de spreek snelheid, de uitspraak, enzovoort. met behulp van SSML-Tags.

1. Breek punt toevoegen

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
   ```

2. Snelheid van spreken wijzigen

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

3. Uitspraak van

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
   ```

4. Volume wijzigen

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

5. Breedte wijzigen

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

6. Prosody-contour wijzigen

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> Houd er rekening mee dat de audio gegevens in de volgende indeling moeten worden 8k of een 16-KB WAV-bestand: **CRC-code** (CRC-32): 4 bytes (DWORD) met geldig bereik 0x00000000 ~ 0xFFFFFFFF; **Audio-indelings markering**: 4 bytes (DWORD) met geldig bereik 0x00000000 ~ 0xFFFFFFFF; **Aantal voor beelden**: 4 bytes (DWORD) met geldig bereik 0x00000000 ~ 0x7FFFFFFF; **Grootte van binaire hoofd tekst**: 4 bytes (DWORD) met geldig bereik 0x00000000 ~ 0x7FFFFFFF; **Binaire hoofd tekst**: n bytes.

## <a name="SampleApp"></a>Voorbeeld toepassing

Zie voor meer informatie over de implementatie de [Visual C#.net-voorbeeld toepassing tekst-naar-spraak](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Ondersteunde land instellingen en spraak lettertypen

De volgende tabel bevat een aantal ondersteunde land instellingen en verwante spraak lettertypen.

Landinstelling | Geslacht | De toewijzing van service
---------|--------|------------
AR-bijvoorbeeld * | Vrouw | "Microsoft Server spraak tekst en spraak spraak (ar-bijvoorbeeld Hoda)"
ar-SA | Man | "Microsoft Server spraak tekst en spraak, spraak (ar-SA, Naayf)"
bg-BG | Man | "Microsoft Server tekst naar spraak stem (bg-BG, Ivan)"
CA-ES | Vrouw | "Microsoft Server tekst naar spraak stem (ca-ES, HerenaRUS)"
cs-CZ | Man | "Microsoft Server spraak tekst en spraak spraak (cs-CZ, Jakub)"
da-DK | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (da-DK, HelleRUS)"
de-AT | Man | "Microsoft Server spraak tekst en spraak, spraak (de-AT, Michael)"
de CH | Man | "Microsoft Server spraak tekst en spraak, spraak (de-h, Karsten)"
de-DE | Vrouw | "Micro soft server Speech Text to Speech Voice (de-DE, Hedda)"
de-DE | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (nl-nl, HeddaRUS)"
de-DE | Man | "Micro soft server Speech Text to Speech Voice (de-DE, Stefan, Apollo)"
el GR | Man | "Microsoft Server spraak tekst en spraak, spraak (el-GR, Stefanos)"
en-AU | Vrouw | "Micro soft server Speech Text to Speech Voice (en-AU, Catherine)"
en-AU | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-AU, HayleyRUS)"
NL-CA | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-CA, Linda)"
NL-CA | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-CA, HeatherRUS)"
en-GB | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-GB, Susan, Apollo)"
en-GB | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-GB, HazelRUS)"
en-GB | Man | "Microsoft Server spraak tekst en spraak, spraak (en-GB, George, Apollo)"
NL-Internet Explorer | Man | "Microsoft Server spraak tekst en spraak, spraak (en Internet Explorer, Jan)"
NL-IN | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-IN, Heera, Apollo)"
NL-IN | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-IN, PriyaRUS)"
NL-IN | Man | "Microsoft Server spraak tekst en spraak, spraak (en-IN, Ravi, Apollo)"
en-US | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-US, ZiraRUS)"
en-US | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-US, JessaRUS)"
en-US | Man | "Microsoft Server spraak tekst en spraak, spraak (en-US, BenjaminRUS)"
es-ES | Vrouw | "Microsoft Server tekst naar spraak stem (es-ES, Laura, Apollo)"
es-ES | Vrouw | "Microsoft Server tekst naar spraak stem (es-ES, HelenaRUS)"
es-ES | Man | "Microsoft Server tekst naar spraak stem (es-ES, Pablo, Apollo)"
es-MX | Vrouw | "Microsoft Server-stem tekst naar spraak (es-MX, HildaRUS)"
es-MX | Man | "Microsoft Server-stem tekst naar spraak (es-MX, Raul, Apollo)"
fi-FI | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (fi-FI, HeidiRUS)"
fr-CA | Vrouw | "Microsoft Server tekst naar spraak stem (fr-CA, Caroline)"
fr-CA | Vrouw | "Microsoft Server tekst naar spraak stem (fr-CA, HarmonieRUS)"
FR-h | Man | "Microsoft Server tekst naar spraak stem (fr-h, Guillaume)"
fr-FR | Vrouw | "Microsoft Server tekst naar spraak stem (fr-FR, Julia, Apollo)"
fr-FR | Vrouw | "Microsoft Server tekst naar spraak stem (fr-FR, HortenseRUS)"
fr-FR | Man | "Microsoft Server tekst naar spraak stem (fr-FR, Paul, Apollo)"
hij IL| Man| "Microsoft Server spraak tekst en spraak, spraak (hij-IL, Asaf)"
hi-IN | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (High-IN, Kalpana, Apollo)"
hi-IN | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (High-IN, Kalpana)"
hi-IN | Man | "Microsoft Server spraak tekst en spraak, spraak (High-IN, Hemant)"
hr-HR | Man | "Microsoft Server tekst naar spraak stem (hr-HR, Matej)"
hu-HU | Man | "Microsoft Server spraak tekst en spraak, spraak (hu-HU, Szabolcs)"
id-ID | Man | "Microsoft Server spraak tekst en spraak, spraak (id-ID, Andika)"
IT-IT | Man | "Microsoft Server spraak tekst en spraak, spraak (it-IT, Cosimo, Apollo)"
IT-IT | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (it-IT, LuciaRUS)"
ja-JP | Vrouw | "Microsoft Server tekst naar spraak stem (ja-JP, Ayumi, Apollo)"
ja-JP | Man | "Microsoft Server tekst naar spraak stem (ja-JP, Ichiro, Apollo)"
ja-JP | Vrouw | "Microsoft Server tekst naar spraak stem (ja-JP, HarukaRUS)"
ko-KR | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (ko-KR, HeamiRUS)"
ms-MY | Man | "Microsoft Server-stem tekst naar spraak (ms-mijn Rizwan)"
nb-NO | Vrouw | "Microsoft Server-stem tekst naar spraak (nb-NO HuldaRUS)"
NL-NL | Vrouw | "Microsoft Server tekst naar spraak stem (nl-NL, HannaRUS)"
pl-PL | Vrouw | "Microsoft Server tekst naar spraak stem (pl-PL, PaulinaRUS)"
pt-BR | Vrouw | "Microsoft Server tekst naar spraak stem (pt-BR, HeloisaRUS)"
pt-BR | Man | "Microsoft Server tekst naar spraak stem (pt-BR, Daniel, Apollo)"
pt-PT | Vrouw | "Microsoft Server tekst naar spraak stem (pt-PT, HeliaRUS)"
ro-RO | Man | "Microsoft Server spraak tekst en spraak, spraak (ro-RO, Andrei)"
ru-RU | Vrouw | "Microsoft Server tekst naar spraak stem (ru-RU, Irina, Apollo)"
ru-RU | Man | "Microsoft Server tekst naar spraak stem (ru-RU, Pavel, Apollo)"
ru-RU | Vrouw | "Microsoft Server tekst naar spraak stem (ru-RU, EkaterinaRUS)"
sk-SK | Man | "Microsoft Server tekst naar spraak stem (sk-SK, Filip)"
sl-SI | Man | "Microsoft Server tekst naar spraak stem (sl-SI, Lado)"
SV-SE | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (sv-SE, HedvigRUS)"
TA-IN | Man | "Microsoft Server spraak tekst en spraak, spraak (ta-IN, Valluvar)"
e-e | Man | "Microsoft Server tekst naar spraak stem (th-TH, Pattara)"
tr-TR | Vrouw | "Microsoft Server tekst naar spraak stem (tr-TR, SedaRUS)"
vi-VN | Man | "Microsoft Server-stem tekst naar spraak (vi-VN, een)"
zh-CN | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-CN, HuihuiRUS)"
zh-CN | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-CN, Yaoyao, Apollo)"
zh-CN | Man | "Microsoft Server spraak tekst en spraak, spraak (zh-CN, Kangkang, Apollo)"
zh-HK | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-HK Tracy, Apollo)"
zh-HK | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-HK TracyRUS)"
zh-HK | Man | "Microsoft Server spraak tekst en spraak, spraak (zh-HK Danny, Apollo)"
zh-TW | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-TW, Yating, Apollo)"
zh-TW | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-TW, HanHanRUS)"
zh-TW | Man | "Microsoft Server spraak tekst en spraak, spraak (zh-TW, Zhiwei, Apollo)"

 \* AR-BIJV ondersteunt de moderne standaard Arabische (MSA).

> [!NOTE]
> Houd er rekening mee dat de vorige service namen van **micro soft server speech text to speech Voice (cs-cz, vit)** en **micro soft server Speech Text to speech Voice (en-IE, Shaun)** worden afgeschaft na 3/31/2018, om de Bing Speech-API te optimaliseren bieden. Werk uw code bij met de bijgewerkte namen.

## <a name="TrouNSupport"></a>Probleem oplossing en ondersteuning

U kunt alle vragen en problemen naar het MSDN-forum van de [Bing Speech-Service](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) posten. Volledige details toevoegen, zoals:

* Een voor beeld van de volledige aanvraag reeks.
* Indien van toepassing, de volledige uitvoer van een mislukte aanvraag, waaronder logboek-Id's.
* Het percentage mislukte aanvragen.
