---
title: Spraaksynthese-opmaaktaal (SSML) - Spraakservice
titleSuffix: Azure Cognitive Services
description: Met behulp van de Speech Synthesis Markup Language om uitspraak en prosodie in tekst-naar-spraak te controleren.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: dc11d26c73c52b5e6c4d8e05cc27dd6ebce0c5d8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399824"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Synthese verbeteren met Speech Synthesis Markup Language (SSML)

Spraaksyntheseopmaaktaal (SSML) is een op XML gebaseerde opmaaktaal gebaseerde markeringstaal waarmee ontwikkelaars kunnen opgeven hoe invoertekst wordt omgezet in gesynthetiseerde spraak met behulp van de tekst-naar-spraakservice. In vergelijking met platte tekst stelt SSML ontwikkelaars in staat om de toonhoogte, uitspraak, spreeksnelheid, volume en meer van de tekst-naar-spraak-uitvoer te verfijnen. Normale interpunctie, zoals pauzeren na een bepaalde periode, of het gebruik van de juiste intonatie wanneer een zin eindigt met een vraagteken wordt automatisch verwerkt.

De implementatie van De dienst van de Toespraak van SSML is gebaseerd op [de Versie 1 1.0](https://www.w3.org/TR/speech-synthesis)van het Beeldvan de Synthese van het Consortium van het Wereld Brede Web Consortium.

> [!IMPORTANT]
> Chinese, Japanse en Koreaanse tekens tellen als twee tekens voor facturering. Zie [Prijzen voor](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)meer informatie.

## <a name="standard-neural-and-custom-voices"></a>Standaard-, neurale en aangepaste stemmen

Kies uit standaard- en neurale stemmen, of creëer je eigen aangepaste stem die uniek is voor je product of merk. 75+ standaardstemmen zijn beschikbaar in meer dan 45 talen en locaties, en 5 neurale stemmen zijn beschikbaar in vier talen en locaties. Zie [taalondersteuning](language-support.md)voor een volledige lijst met ondersteunde talen, landzetten en stemmen (neurale en standaard).

Zie Overzicht van tekst [naar spraak](text-to-speech.md)voor meer informatie over standaard-, neurale en aangepaste stemmen.

## <a name="special-characters"></a>Speciale tekens

Houd er tijdens het gebruik van SSML rekening mee dat speciale tekens, zoals aanhalingstekens, apostrofs en haakjes, moeten worden ontweken. Zie [Xml (XML) 1.0: Aanhangsel D voor](https://www.w3.org/TR/xml/#sec-entexpand)meer informatie.

## <a name="supported-ssml-elements"></a>Ondersteunde SSML-elementen

Elk SSML-document wordt gemaakt met SSML-elementen (of tags). Deze elementen worden gebruikt om toonhoogte, prosodie, volume en meer aan te passen. In de volgende secties wordt beschreven hoe elk element wordt gebruikt en wanneer een element vereist of optioneel is.  

> [!IMPORTANT]
> Vergeet niet om dubbele aanhalingstekens te gebruiken rond attribuutwaarden. Normen voor goed gevormde, geldige XML vereisen dat kenmerkwaarden worden ingesloten in dubbele aanhalingstekens. Bijvoorbeeld, `<prosody volume="90">` is een goed gevormd, geldig `<prosody volume=90>` element, maar is het niet. SSML herkent mogelijk geen attribuutwaarden die niet tussen aanhalingstekens staan.

## <a name="create-an-ssml-document"></a>Een SSML-document maken

`speak`is het hoofdelement en is **vereist** voor alle SSML-documenten. Het `speak` element bevat belangrijke informatie, zoals versie, taal en de definitie van de markeringswoordenschat.

**Syntaxis**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist / Optioneel |
|-----------|-------------|---------------------|
| `version` | Geeft de versie aan van de SSML-specificatie die wordt gebruikt om de documentmarkering te interpreteren. De huidige versie is 1.0. | Vereist |
| `xml:lang` | Hiermee geeft u de taal van het hoofddocument op. De waarde kan een kleine letters, tweeletterige `en`taalcode (bijvoorbeeld) of de taalcode en `en-US`het land/regio van hoofdletters (bijvoorbeeld ). | Vereist |
| `xmlns` | Hiermee geeft u de URI op aan het document dat de opmaakwoordenschat (de elementtypen en kenmerknamen) van het SSML-document definieert. De huidige http://www.w3.org/2001/10/synthesisURI is . | Vereist |

## <a name="choose-a-voice-for-text-to-speech"></a>Kies een stem voor tekst-naar-spraak

Het `voice` element is vereist. Het wordt gebruikt om de stem op te geven die wordt gebruikt voor tekst-naar-spraak.

**Syntaxis**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist / Optioneel |
|-----------|-------------|---------------------|
| `name` | Hiermee identificeert u de stem die wordt gebruikt voor de uitvoer van tekst naar spraak. Zie [Taalondersteuning](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen. | Vereist |

**Voorbeeld**

> [!NOTE]
> In dit `en-US-AriaRUS` voorbeeld wordt de stem gebruikt. Zie [Taalondersteuning](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen.

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Meerdere stemmen gebruiken

Binnen `speak` het element u meerdere stemmen opgeven voor tekst-naar-spraak-uitvoer. Deze stemmen kunnen in verschillende talen zijn. Voor elke stem moet de tekst `voice` in een element worden verpakt. 

**Kenmerken**

| Kenmerk | Beschrijving | Vereist / Optioneel |
|-----------|-------------|---------------------|
| `name` | Hiermee identificeert u de stem die wordt gebruikt voor de uitvoer van tekst naar spraak. Zie [Taalondersteuning](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen. | Vereist |

> [!IMPORTANT]
> Meerdere stemmen zijn onverenigbaar met de woordgrensfunctie. De functie woordgrens moet worden uitgeschakeld om meerdere stemmen te kunnen gebruiken.

### <a name="disable-word-boundary"></a>Woordgrens uitschakelen

Afhankelijk van de Spraak-SDK-taal stelt `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` u `false` de eigenschap `SpeechConfig` in op een instantie van het object.

# <a name="c"></a>[C#](#tab/csharp)

Zie voor meer <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Zie voor meer <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Zie voor meer <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Zie voor meer <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Zie voor meer <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Zie voor meer <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Zie voor meer <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Spreekstijlen aanpassen

> [!IMPORTANT]
> De aanpassing van spreekstijlen werkt alleen met neurale stemmen.

Standaard synthetiseert de tekst-naar-spraakservice tekst met behulp van een neutrale spreekstijl voor zowel standaard- als neurale stemmen. Met neurale stemmen kun je de spreekstijl aanpassen om vrolijkheid, empathie of sentiment met het `<mstts:express-as>` element uit te drukken. Dit is een optioneel element dat uniek is voor de Spraakservice.

Momenteel worden gesproken stijlaanpassingen ondersteund voor deze neurale stemmen:
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`

Wijzigingen worden toegepast op zinsniveau en de stijl verschilt per stem. Als een stijl niet wordt ondersteund, retourneert de service spraak in de standaardneutrale spreekstijl.

**Syntaxis**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist / Optioneel |
|-----------|-------------|---------------------|
| `style` | Hiermee geeft u de spreekstijl op. Momenteel zijn spreekstijlen stemspecifiek. | Vereist als het aanpassen van de spreekstijl voor een neurale stem. Bij `mstts:express-as`gebruik moet de stijl worden verstrekt. Als een ongeldige waarde wordt opgegeven, wordt dit element genegeerd. |

Gebruik deze tabel om te bepalen welke spreekstijlen worden ondersteund voor elke neurale stem.

| Spraak                   | Stijl                     | Beschrijving                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast"`        | Drukt een formele en professionele toon uit voor het vertellen van nieuws |
|                         | `style="customerservice"` | Drukt een vriendelijke en behulpzame toon uit voor klantenondersteuning  |
|                         | `style="chat"`            | Drukt een ongedwongen en ontspannen toon uit                         |
|                         | `style="cheerful"`        | Drukt een positieve en vrolijke toon uit                         |
|                         | `style="empathetic"`      | Drukt een gevoel van zorg en begrip uit               |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Drukt een formele en professionele toon uit voor het vertellen van nieuws |
|                         | `style="customerservice"` | Drukt een vriendelijke en behulpzame toon uit voor klantenondersteuning  |
|                         | `style="assistant"`       | Drukt een warme en ontspannen toon uit voor digitale assistenten    |
|                         | `style="lyrical"`         | Drukt emoties uit op een melodische en sentimentele manier         |

**Voorbeeld**

Dit SSML-fragment illustreert hoe het `<mstts:express-as>` element wordt `cheerful`gebruikt om de spreekstijl te wijzigen in .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Een einde/pauze toevoegen of verwijderen

Gebruik `break` het element om pauzes (of pauzes) tussen woorden in te voegen of voortevoorkomen dat pauzes automatisch worden toegevoegd door de tekst-naar-spraakservice.

> [!NOTE]
> Gebruik dit element om het standaardgedrag van tekst-naar-spraak (TTS) te overschrijven voor een woord of woordgroep als de gesynthetiseerde spraak voor dat woord of die zin onnatuurlijk klinkt. Ingesteld `strength` `none` om een prosodische onderbreking te voorkomen, die automatisch wordt ingevoegd door de tekst-naar-spraakservice.

**Syntaxis**

```xml
<break strength="string" />
<break time="string" />
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist / Optioneel |
|-----------|-------------|---------------------|
| `strength` | Hiermee geeft u de relatieve duur van een pauze op met een van de volgende waarden:<ul><li>geen</li><li>x-zwak</li><li>Zwakke</li><li>medium (standaard)</li><li>Sterke</li><li>x-sterk</li></ul> | Optioneel |
| `time` | Hiermee geeft u de absolute duur van een pauze in seconden of milliseconden op. Voorbeelden van geldige `2s` waarden zijn en`500` | Optioneel |

| Kracht                      | Beschrijving |
|-------------------------------|-------------|
| Geen, of als er geen waarde is opgegeven | 0 ms        |
| x-zwak                        | 250 ms      |
| Zwakke                          | 500 ms      |
| gemiddeld                        | 750 ms      |
| Sterke                        | 1000 ms     |
| x-sterk                      | 1250 ms     |

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Alinea's en zinnen opgeven

`p`en `s` elementen worden gebruikt om respectievelijk alinea's en zinnen aan te duiden. Bij afwezigheid van deze elementen bepaalt de tekst-naar-spraakservice automatisch de structuur van het SSML-document.

Het `p` element kan tekst en `audio`de `break` `phoneme`volgende `prosody` `say-as`elementen `sub` `mstts:express-as`bevatten: `s`, , , , , en .

Het `s` element kan tekst en `audio`de `break` `phoneme`volgende `prosody` `say-as`elementen `mstts:express-as`bevatten: , , , , en `sub`.

**Syntaxis**

```XML
<p></p>
<s></s>
```

**Voorbeeld**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Fonemen gebruiken om de uitspraak te verbeteren

Het `ph` element wordt gebruikt om voor fonetische uitspraak in SSML documenten. Het `ph` element kan alleen tekst bevatten, geen andere elementen. Geef altijd door de mens leesbare spraak als een terugval.

Fonetische alfabetten zijn samengesteld uit telefoons, die zijn opgebouwd uit letters, cijfers, of tekens, soms in combinatie. Elke telefoon beschrijft een uniek geluid van spraak. Dit in tegenstelling tot het Latijnse alfabet, waar elke letter meerdere gesproken geluiden kan vertegenwoordigen. Denk aan de verschillende uitspraken van de letter "c" in de woorden "snoep" en "ophouden", of de verschillende uitspraken van de lettercombinatie "th" in de woorden "ding" en "die".

**Syntaxis**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist / Optioneel |
|-----------|-------------|---------------------|
| `alphabet` | Hiermee geeft u het fonetische alfabet op dat moet `ph` worden gebruikt bij het synthetiseren van de uitspraak van de tekenreeks in het kenmerk. De tekenreeks die het alfabet opgeeft, moet in kleine letters worden opgegeven. De volgende zijn de mogelijke alfabetten die u opgeven.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Internationaal Fonetisch Alfabet <span class="docon docon-navigate-external x-hidden-focus"></span> </a></li><li>`sapi`&ndash; [De dienst fonetisch alfabet van de toespraakdienst](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; Universele telefoonset</li></ul><br>Het alfabet is `phoneme` alleen van toepassing op het in het element.. | Optioneel |
| `ph` | Een tekenreeks met telefoons die de uitspraak `phoneme` van het woord in het element opgeven. Als de opgegeven tekenreeks niet-herkende telefoons bevat, wijst de TTS-service (text-to-speech) het volledige SSML-document af en produceert de spraakuitvoer die in het document is opgegeven, niet. | Vereist bij het gebruik van fonemen. |

**Voorbeelden**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Gebruik aangepast lexicon om uitspraak te verbeteren

Soms kan TTS een woord niet nauwkeurig uitspreken, bijvoorbeeld een bedrijfsnaam of een buitenlandse naam. Ontwikkelaars kunnen het lezen van deze entiteiten `phoneme` in `sub` SSML definiëren met behulp van en tag, of `lexicon` het lezen van meerdere entiteiten definiëren door te verwijzen naar een aangepast lexiconbestand met behulp van tag.

**Syntaxis**

```XML
<lexicon uri="string"/>
```

**Kenmerken**

| Kenmerk | Beschrijving                               | Vereist / Optioneel |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Het adres van het externe PLS-document. | Vereist.           |

**Gebruik**

Stap 1: Aangepaste lexicon definiëren 

U het lezen van entiteiten definiëren aan de doorsnede van een lijst met aangepaste lexiconitems die zijn opgeslagen als een .xml- of .pls-bestand.

**Voorbeeld**

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

Elk `lexeme` element is een lexiconitem. `grapheme`bevat tekst die de `lexeme`orthograaf van . Uitleesformulier kan worden `alias`verstrekt als . Telefoon string kan `phoneme` worden verstrekt in element.

Het `lexicon` element bevat `lexeme` ten minste één element. Elk `lexeme` element bevat `grapheme` ten minste één `grapheme` `alais`element `phoneme` en een of meer , en elementen. Het `grapheme` element bevat tekst die de <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthografie <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>beschrijft. De `alias` elementen worden gebruikt om de uitspraak van een acroniem of een verkorte termijn aan te geven. Het `phoneme` element geeft tekst `lexeme` die beschrijft hoe de wordt uitgesproken.

Zie [Uitspraak Lexicon Specification (PLS) Versie 1.0](https://www.w3.org/TR/pronunciation-lexicon/) op de W3C-website voor meer informatie over het aangepaste lexiconbestand.

Stap 2: Upload aangepast lexiconbestand dat in stap 1 online is gemaakt, u het overal opslaan en we raden u aan het bestand op te slaan in Microsoft Azure, bijvoorbeeld [Azure Blob Storage.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

Stap 3: Raadpleeg het aangepaste lexiconbestand in SSML

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BTW" zal worden gelezen als "By the way". "Benigni" zal worden gelezen met de verstrekte IPA "bṭーniーnji".  

**Beperking**
- Bestandsgrootte: aangepaste maximumlimiet voor lexiconbestanden is 100 KB, als deze grootte verder gaat dan deze grootte, mislukt de syntheseaanvraag.
- Lexicon cache refresh: custom lexicon will be cached with URI as key on TTS Service when it's first loaded. Lexicon met dezelfde URI zal niet worden herladen binnen 15 minuten, dus aangepaste lexicon verandering moet wachten op maximaal 15 minuten van kracht te worden.

**Spraakservice fonetische sets**

In het bovenstaande voorbeeld gebruiken we het International Fonetische Alfabet, ook wel bekend als de IPA-telefoonset. We raden ontwikkelaars aan om de IPA te gebruiken, omdat het de internationale standaard is. Gezien het feit dat de IPA niet gemakkelijk te onthouden is,`en-US`definieert de Spraakservice een fonetische set voor zeven talen ( `fr-FR`, , , `de-DE` `es-ES`, `ja-JP`, `zh-CN`en `zh-TW`).

U `sapi` de als de `alphabet` vale voor het kenmerk met aangepaste lexicons gebruiken, zoals hieronder wordt aangetoond:

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Voor meer informatie over de gedetailleerde Spraak dienst fonetische alfabet, zie de [Spraak service fonetische sets](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Prosodie aanpassen

Het `prosody` element wordt gebruikt om wijzigingen op te geven in toonhoogte, contour, bereik, snelheid, duur en volume voor de tekst-naar-spraak-uitvoer. Het `prosody` element kan tekst en `audio`de `break` `p`volgende `phoneme` `prosody`elementen `say-as` `sub`bevatten: `s`, , , , , en .

Omdat prosodische attribuutwaarden over een breed bereik kunnen variëren, interpreteert de spraakherkenning de toegewezen waarden als een suggestie van wat de werkelijke prosodische waarden van de geselecteerde stem zouden moeten zijn. De tekst-naar-spraakservice beperkt of vervangt waarden die niet worden ondersteund. Voorbeelden van niet-ondersteunde waarden zijn een toonhoogte van 1 MHz of een volume van 120.

**Syntaxis**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist / Optioneel |
|-----------|-------------|---------------------|
| `pitch` | Geeft de basislijntoonhoogte voor de tekst aan. U de toonhoogte uitdrukken als:<ul><li>Een absolute waarde, uitgedrukt als een getal gevolgd door "Hz" (Hertz). Bijvoorbeeld 600 Hz.</li><li>Een relatieve waarde, uitgedrukt als een getal voorafgegaan door "+" of "-" en gevolgd door "Hz" of "st", dat een bedrag aangeeft dat de toonhoogte moet wijzigen. Bijvoorbeeld: +80 Hz of -2st. De "st" geeft aan dat de wisseleenheid is semitoon, dat is de helft van een toon (een halve stap) op de standaard diatonische schaal.</li><li>Een constante waarde:<ul><li>x-laag</li><li>Lage</li><li>gemiddeld</li><li>hoog</li><li>x-hoog</li><li>standaardinstelling</li></ul></li></ul>. | Optioneel |
| `contour` | Contour wordt niet ondersteund voor neurale stemmen. Contour staat voor veranderingen in toonhoogte. Deze wijzigingen worden weergegeven als een reeks doelen op opgegeven tijdposities in de spraakuitvoer. Elk doel wordt gedefinieerd door sets parameterparen. Bijvoorbeeld: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>De eerste waarde in elke set parameters geeft de locatie van de toonhoogtewijziging aan als percentage van de duur van de tekst. De tweede waarde geeft het bedrag aan dat de toonhoogte moet verhogen of verlagen, `pitch`met behulp van een relatieve waarde of een opsommingswaarde voor toonhoogte (zie ). | Optioneel |
| `range` | Een waarde die het bereik van de toonhoogte voor de tekst vertegenwoordigt. U kunt `range` uitdrukken met dezelfde absolute waarden, relatieve waarden of `pitch`opsommingswaarden die worden gebruikt om te beschrijven . | Optioneel |
| `rate` | Geeft de spreeksnelheid van de tekst aan. U kunt `rate` zich uitdrukken als:<ul><li>Een relatieve waarde, uitgedrukt als een getal dat fungeert als een vermenigvuldiger van de standaardwaarde. Een waarde van *1* resulteert bijvoorbeeld in geen wijziging in het tarief. Een waarde van *0,5* resulteert in een halvering van het tarief. Een waarde van *3* resulteert in een verdrievoudiging van het tarief.</li><li>Een constante waarde:<ul><li>x-langzaam</li><li>Langzaam</li><li>gemiddeld</li><li>snel</li><li>x-snel</li><li>standaardinstelling</li></ul></li></ul> | Optioneel |
| `duration` | De periode die moet verstrijken terwijl de spraaksyntheseservice (TTS) de tekst in seconden of milliseconden leest. Bijvoorbeeld, *2s* of *1800ms*. | Optioneel |
| `volume` | Geeft het volumeniveau van de sprekende stem aan. U mag het volume uitdrukken als:<ul><li>Een absolute waarde, uitgedrukt als een getal in het bereik van 0,0 tot 100,0, van *stilste* tot *luidste*. Bijvoorbeeld 75. De standaardinstelling is 100,0.</li><li>Een relatieve waarde, uitgedrukt als een getal voorafgegaan door '+' of '-' dat een bedrag aangeeft om het volume te wijzigen. Bijvoorbeeld +10 of -5,5.</li><li>Een constante waarde:<ul><li>Stille</li><li>x-zacht</li><li>Zachte</li><li>gemiddeld</li><li>Luid</li><li>x-luid</li><li>standaardinstelling</li></ul></li></ul> | Optioneel |

### <a name="change-speaking-rate"></a>Spreeksnelheid wijzigen

Spreeksnelheid kan worden toegepast op standaardstemmen op woord- of zinsniveau. Terwijl spreeksnelheid alleen kan worden toegepast op neurale stemmen op zinsniveau.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Volume wijzigen

Volumewijzigingen kunnen worden toegepast op standaardstemmen op woord- of zinsniveau. Terwijl volumeveranderingen alleen kunnen worden toegepast op neurale stemmen op zinsniveau.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Toonhoogte wijzigen

Toonhoogtewijzigingen kunnen worden toegepast op standaardstemmen op woord- of zinsniveau. Terwijl toonhoogteveranderingen alleen kunnen worden toegepast op neurale stemmen op zinsniveau.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Toonhoogtecontour wijzigen

> [!IMPORTANT]
> Toonhoogte contour veranderingen worden niet ondersteund met neurale stemmen.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>say-as element

`say-as`is een optioneel element dat het inhoudstype (zoals getal of datum) van de tekst van het element aangeeft. Dit geeft richtlijnen voor de spraaksynthese-engine over hoe de tekst uit te spreken.

**Syntaxis**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist / Optioneel |
|-----------|-------------|---------------------|
| `interpret-as` | Geeft het inhoudstype van de tekst van het element aan. Zie de onderstaande tabel voor een lijst met typen. | Vereist |
| `format` | Biedt aanvullende informatie over de precieze opmaak van de tekst van het element voor inhoudstypen die mogelijk dubbelzinnige indelingen hebben. SSML definieert indelingen voor inhoudstypen die ze gebruiken (zie onderstaande tabel). | Optioneel |
| `detail` | Geeft het detailniveau aan dat moet worden gesproken. Dit kenmerk kan bijvoorbeeld vragen dat de spraaksynthese-engine leestekens uitspreekt. Er zijn geen standaardwaarden `detail`gedefinieerd voor . | Optioneel |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Hieronder volgen de ondersteunde inhoudstypen voor de `interpret-as` en `format` kenmerken. Voeg `format` het kenmerk `interpret-as` alleen toe als het is ingesteld op datum en tijd.

| interpreteren-als | formaat | Interpretatie |
|--------------|--------|----------------|
| `address` | | De tekst wordt gesproken als een adres. De spraaksynthese-engine spreekt uit:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Zoals "Ik ben op de 150ste rechtbank in het noordoosten van Redmond Washington." |
| `cardinal`, `number` | | De tekst wordt gesproken als een kardinaalnummer. De spraaksynthese-engine spreekt uit:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Zoals "Er zijn drie alternatieven." |
| `characters`, `spell-out` | | De tekst wordt gesproken als afzonderlijke letters (gespeld). De spraaksynthese-engine spreekt uit:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Zoals 'T E S T'. |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | De tekst wordt gesproken als datum. Het `format` kenmerk geeft de notatie van de datum op (*d=day, m=month en y=year).* De spraaksynthese-engine spreekt uit:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Zoals "Vandaag is oktober negentiende tweeduizend zestien." |
| `digits`, `number_digit` | | De tekst wordt gesproken als een opeenvolging van afzonderlijke cijfers. De spraaksynthese-engine spreekt uit:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Zoals "1 2 3 4 5 6 7 8 9." |
| `fraction` | | De tekst wordt gesproken als een fractioneel getal. De spraaksynthese-engine spreekt uit:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Als 'drie achtste van een centimeter'. |
| `ordinal` | | De tekst wordt gesproken als een ordinaal getal. De spraaksynthese-engine spreekt uit:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Als "Selecteer de derde optie". |
| `telephone` | | De tekst wordt gesproken als telefoonnummer. Het `format` kenmerk kan cijfers bevatten die een landcode vertegenwoordigen. Bijvoorbeeld "1" voor de Verenigde Staten of "39" voor Italië. De spraaksynthese-engine kan deze informatie gebruiken om de uitspraak van een telefoonnummer te begeleiden. Het telefoonnummer kan ook de landcode bevatten, en zo ja, `format`heeft voorrang op de landcode in de . De spraaksynthese-engine spreekt uit:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Zoals "Mijn nummer is netnummer acht acht acht vijf vijf vijf vijf een twee een twee een twee." |
| `time` | hms12, hms24 | De tekst wordt gesproken als een tijd. Het `format` kenmerk geeft aan of de tijd is opgegeven met een klok van 12 uur (hms12) of een 24-uursklok (hms24). Gebruik een dubbele punt om getallen te scheiden die uren, minuten en seconden vertegenwoordigen. De volgende voorbeelden zijn geldige tijdvoorbeelden: 12:35, 1:14:32, 08:15 en 02:50:45. De spraaksynthese-engine spreekt uit:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Zoals "De trein vertrekt om vier A M." |

**Gebruik**

Het `say-as` element mag alleen tekst bevatten.

**Voorbeeld**

De spraaksynthese-engine spreekt het volgende voorbeeld als "Uw eerste verzoek was voor een kamer op oktober negentiende twintig tien met vroege aankomst om twaalf vijfendertig uur."
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Opgenomen audio toevoegen

`audio`is een optioneel element waarmee u MP3-audio invoegen in een SSML-document. De hoofdtekst van het audio-element kan platte tekst of SSML-markering bevatten die wordt gesproken als het audiobestand niet beschikbaar of onspeelbaar is. Bovendien kan `audio` het element tekst en de `audio` `break`volgende `p` `s`elementen `phoneme` `prosody`bevatten: , , , , , , `say-as`en `sub`.

Alle audio die in het SSML-document is opgenomen, moet aan deze vereisten voldoen:

* De MP3 moet worden gehost op een HTTPS-eindpunt dat toegankelijk is voor internet. HTTPS is vereist en het domein dat het MP3-bestand host, moet een geldig, vertrouwd TLS/SSL-certificaat tonen.
* De MP3 moet een geldig MP3-bestand (MPEG v2) zijn.
* De bitsnelheid moet 48 kbps zijn.
* De samplesnelheid moet 16.000 Hz zijn.
* De totale totale tijd voor alle tekst- en audiobestanden in één reactie mag niet meer bedragen dan negentig (90) seconden.
* De MP3 mag geen klantspecifieke of andere gevoelige informatie bevatten.

**Syntaxis**

```xml
<audio src="string"/></audio>
```

**Kenmerken**

| Kenmerk | Beschrijving                                   | Vereist / Optioneel                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Hiermee geeft u de locatie/URL van het audiobestand op. | Vereist als u het audio-element in uw SSML-document gebruikt. |

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Achtergrondaudio toevoegen

Met `mstts:backgroundaudio` het element u achtergrondaudio toevoegen aan uw SSML-documenten (of een audiobestand mengen met tekst-naar-spraak). Met `mstts:backgroundaudio` u een audiobestand op de achtergrond lopen, aan het begin van tekst-naar-spraak infaden en vervagen aan het einde van tekst-naar-spraak.

Als de opgegeven achtergrondaudio korter is dan de tekst-naar-spraak of de fade-out, wordt deze herhaald. Als het langer is dan de tekst-naar-spraak, zal het stoppen wanneer de fade-out is voltooid.

Per SSML-document is slechts één audiobestand op de achtergrond toegestaan. U echter `audio` tags `voice` in het element strooien om extra audio toe te voegen aan uw SSML-document.

**Syntaxis**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist / Optioneel |
|-----------|-------------|---------------------|
| `src` | Hiermee geeft u de locatie/URL van het achtergrondaudiobestand op. | Vereist als u achtergrondaudio gebruikt in uw SSML-document. |
| `volume` | Hiermee geeft u het volume van het achtergrondaudiobestand op. **Geaccepteerde** `0` waarden `100` : tot inclusiviteit. De standaardwaarde is `1`. | Optioneel |
| `fadein` | Hiermee geeft u de duur op van de achtergrondaudio "fade in" als milliseconden. De standaardwaarde `0`is , wat het equivalent is van geen fade-in. **Geaccepteerde** `0` waarden `10000` : tot inclusiviteit.  | Optioneel |
| `fadeout` | Hiermee geeft u de duur op van de achtergrondaudio die in milliseconden vervaagt. De standaardwaarde `0`is , wat het equivalent is van geen fade-out. **Geaccepteerde** `0` waarden `10000` : tot inclusiviteit.  | Optioneel |

**Voorbeeld**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Volgende stappen

* [Taalondersteuning: stemmen, landint, talen](language-support.md)
