---
title: Speech-synthese Markup Language (SSML)-Speech Service
titleSuffix: Azure Cognitive Services
description: Met behulp van de spraakherkenning synthese Markup Language voor het beheren van uitspraak en prosody in tekst naar spraak.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 3791b2d60b84299fc3b646f7e6585002078b607f
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350164"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

SSML (Speech synthese Markup Language) is een op XML gebaseerde Markup-taal waarmee ontwikkel aars kunnen opgeven hoe invoer tekst wordt omgezet in gesynthesizerde spraak met behulp van de tekst-naar-spraak-service. Vergeleken met onbewerkte tekst kunnen ontwikkel aars met SSML de hoogte, de uitspraak, de spreek snelheid, het volume en meer van de tekst-naar-spraak-uitvoer nauw keuriger instellen. Normale Lees tekens, zoals het onderbreken na een periode, of het gebruik van de juiste intonation wanneer een zin eindigt met een vraag teken, worden automatisch afgehandeld.

De speech Services-implementatie van SSML is gebaseerd op de [opmaak taal versie 1,0](https://www.w3.org/TR/speech-synthesis)van de spraak-synthese van World Wide Web Consortium.

> [!IMPORTANT]
> Chinese, Japanse en Koreaanse tekens tellen als twee tekens voor facturering. Zie [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)voor meer informatie.

## <a name="standard-neural-and-custom-voices"></a>Standaard, Neural en aangepaste stemmen

Kies uit standaard-en Neural stemmen of maak uw eigen aangepaste spraak die uniek is voor uw product of merk. 75 + standaard stemmen zijn verkrijgbaar in meer dan 45 talen en land instellingen en vijf Neural stemmen zijn beschikbaar in vier talen en land instellingen. Zie [taal ondersteuning](language-support.md)voor een volledige lijst met ondersteunde talen, land instellingen en stemmen (Neural en Standard).

Zie [tekst-naar-spraak-overzicht](text-to-speech.md)voor meer informatie over standaard-, Neural-en aangepaste stemmen.

## <a name="special-characters"></a>Speciale tekens

Wanneer u SSML gebruikt om tekst-naar-gesynthesizerde spraak te converteren, moet u er rekening mee houdt dat net als met XML, speciale tekens, zoals aanhalings tekens, apostrofs en haakjes, moeten worden voorafgegaan. Zie [Extensible Markup Language (XML) 1,0: bijlage D](https://www.w3.org/TR/xml/#sec-entexpand)voor meer informatie.

## <a name="supported-ssml-elements"></a>Ondersteunde SSML-elementen

Elk SSML-document wordt gemaakt met SSML-elementen (of-Tags). Deze elementen worden gebruikt voor het aanpassen van de pitch, prosody, volume en meer. In de volgende secties wordt uitgelegd hoe elk-element wordt gebruikt en wanneer een element vereist of optioneel is.  

> [!IMPORTANT]
> Vergeet geen dubbele aanhalings tekens rond kenmerk waarden te gebruiken. Standaarden voor juist opgemaakte, geldige XML vereist dat kenmerk waarden tussen dubbele aanhalings tekens worden geplaatst. `<prosody volume="90">` is bijvoorbeeld een goed gevormd, geldig element, maar `<prosody volume=90>` niet. SSML kunnen kenmerk waarden die geen aanhalings tekens zijn niet herkennen.

## <a name="create-an-ssml-document"></a>Een SSML-document maken

`speak` is het hoofd element en is **vereist** voor alle SSML-documenten. Het `speak`-element bevat belang rijke informatie, zoals versie, taal en de definitie van de aantekeningen woordenlijst.

**Syntaxis**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Eigenschappen**

| Kenmerk | Beschrijving | Vereiste / optioneel |
|-----------|-------------|---------------------|
| version | Hiermee wordt de versie van de SSML-specificatie aangegeven die wordt gebruikt om de document markering te interpreteren. De huidige versie is 1,0. | Vereist |
| xml:lang | Hiermee geeft u de taal van het hoofd document. De waarde kan een kleine letter, een taal code van twee letters (bijvoorbeeld en **) of**de taal code en het hoofd land/regio (bijvoorbeeld **en-US**) bevatten. | Vereist |
| xmlns | Hiermee geeft u de URI op voor het document dat de aantekeningen woordenlijst (de element typen en kenmerk namen) van het SSML-document definieert. De huidige URI is https://www.w3.org/2001/10/synthesis. | Vereist |

## <a name="choose-a-voice-for-text-to-speech"></a>Kies een stem voor tekst naar spraak

Het `voice`-element is vereist. Dit wordt gebruikt om de stem op te geven die wordt gebruikt voor tekst naar spraak.

**Syntaxis**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Eigenschappen**

| Kenmerk | Beschrijving | Vereiste / optioneel |
|-----------|-------------|---------------------|
| name | Hiermee wordt de stem geïdentificeerd die wordt gebruikt voor de tekst-naar-spraak-uitvoer. Zie [taal ondersteuning](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen. | Vereist |

**Voorbeeld**

> [!NOTE]
> In dit voor beeld wordt de `en-US-Jessa24kRUS`e Voice gebruikt. Zie [taal ondersteuning](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen.

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Meerdere stemmen

Binnen het `speak`-element kunt u meerdere stemmen opgeven voor tekst-naar-spraak-uitvoer. Deze stemmen kunnen zich in verschillende talen bevindt. Voor elke stem moet de tekst worden ingepakt in een `voice`-element.

**Eigenschappen**

| Kenmerk | Beschrijving | Vereiste / optioneel |
|-----------|-------------|---------------------|
| name | Hiermee wordt de stem geïdentificeerd die wordt gebruikt voor de tekst-naar-spraak-uitvoer. Zie [taal ondersteuning](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen. | Vereist |

**Voorbeeld**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Spreek stijlen aanpassen

> [!IMPORTANT]
> Deze functie werkt alleen met Neural stemmen.

Standaard wordt tekst door de service tekst naar spraak gesynthesizerd met behulp van een neutrale spreek stijl voor de standaard-en Neural stemmen. Met Neural stemmen kunt u de spreek stijl aanpassen aan cheerfulness, empathie of sentiment met het element `<mstts:express-as>`. Dit is een optioneel element dat uniek is voor Azure speech Services.

Op dit moment worden de volgende Neural stemmen ondersteund:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

Wijzigingen worden toegepast op het niveau van de zin en de stijl varieert per stem. Als een stijl niet wordt ondersteund, retourneert de service spraak in de standaard stijl voor neutrale gesp roken tekst.

**Syntaxis**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Eigenschappen**

| Kenmerk | Beschrijving | Vereiste / optioneel |
|-----------|-------------|---------------------|
| type | Geeft de spreek stijl aan. Op dit moment zijn gesp roken stijlen specifiek voor spraak. | Vereist bij het aanpassen van de spreek stijl voor een Neural-stem. Als u `mstts:express-as`gebruikt, moet u type opgeven. Als er een ongeldige waarde wordt gegeven, wordt dit element genegeerd. |

Gebruik deze tabel om te bepalen welke spraak stijlen worden ondersteund voor elke Neural-stem.

| Spraak | Type | Beschrijving |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Een Emotion die positief en blij is |
| | type=`empathy` | Een idee van caring en inzicht |
| | type=`chat` | Spreken in een informeel, ongeforceerde Toon |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Een formele Toon, vergelijkbaar met nieuws verzendingen |
| | type=`sentiment` | Geeft een aanrakend bericht of verhaal |

**Voorbeeld**

Dit SSML-fragment laat zien hoe het `<mstts:express-as>`-element wordt gebruikt om de spraak stijl te wijzigen in `cheerful`.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Een onderbreking/pauze toevoegen of verwijderen

Gebruik het `break`-element om pauzes (of onderbrekingen) tussen woorden in te voegen of onderbrekingen die automatisch worden toegevoegd door de service tekst naar spraak te voor komen.

> [!NOTE]
> Gebruik dit element om het standaard gedrag van text-to-speech (TTS) voor een woord of woord groep te overschrijven als de gesynthesizerde spraak voor het woord of de woord groep onnatuurlijk klinkt. Stel `strength` in op `none` om te voor komen dat een Prosodic-verbreekt, die automatisch wordt ingevoegd door de service tekst naar spraak.

**Syntaxis**

```xml
<break strength="string" />
<break time="string" />
```

**Eigenschappen**

| Kenmerk | Beschrijving | Vereiste / optioneel |
|-----------|-------------|---------------------|
| hoger | Hiermee geeft u de relatieve duur van een onderbreking op met een van de volgende waarden:<ul><li>geen</li><li>x-zwak</li><li>Schakel</li><li>gemiddeld (standaard)</li><li>strakk</li><li>x-Strong</li></ul> | Optioneel |
| tijd | Hiermee geeft u de absolute duur van een onderbreking in seconden of milliseconden. Voor beelden van geldige waarden zijn 2s en 500 | Optioneel |

| hoger | Beschrijving |
|----------|-------------|
| Geen, of als er geen waarde wordt gegeven | 0 MS |
| x-zwak | 250 MS |
| Schakel | 500 ms |
| drager | 750 ms |
| strakk | 1000 MS |
| x-Strong | 1250 MS |


**Voorbeeld**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Alinea's en zinnen opgeven

`p` en `s` elementen worden gebruikt om respectievelijk alinea's en zinnen aan te duiden. Als deze elementen ontbreken, bepaalt de tekst naar spraak-service automatisch de structuur van het SSML-document.

Het `p`-element kan tekst en de volgende elementen bevatten: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`en `s`.

Het `s`-element kan tekst en de volgende elementen bevatten: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`en `sub`.

**Syntaxis**

```XML
<p></p>
<s></s>
```

**Voorbeeld**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
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

Het element `ph` wordt gebruikt voor fonetische uitspraak in SSML-documenten. Het `ph`-element kan alleen tekst bevatten, geen andere elementen. Bied altijd lees bare spraak als terugval.

Fonetische alfabetten bestaan uit telefoons, die bestaan uit letters, cijfers of tekens, soms in combi natie. Elke telefoon beschrijft een uniek geluid van spraak. Dit is in tegens telling tot het Latijnse alfabet, waarbij een wille keurige letter meerdere gesp roken geluiden kan vertegenwoordigen. Houd rekening met de verschillende uitspraak van de letter "c" in de woorden "snoep" en "Stop" of de verschillende uitspraak van de letter combinaties "th" in de woorden "ding" en "die".

**Syntaxis**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Eigenschappen**

| Kenmerk | Beschrijving | Vereiste / optioneel |
|-----------|-------------|---------------------|
| letters | Hiermee geeft u het fonetische alfabet op dat moet worden gebruikt wanneer de uitspraak van de teken reeks in het `ph` kenmerk wordt gesynthesizerd. De teken reeks die het alfabet opgeeft, moet worden opgegeven in kleine letters. Hier volgen de mogelijke alfabetten die u kunt opgeven.<ul><li>IPA &ndash; Internationaal Fonetisch alfabet</li><li>Telefoonset voor SAPI &ndash; Speech-API</li><li>Universele Telefoonset met ups &ndash;</li></ul>Het alfabet is alleen van toepassing op de foneem in het-element. Zie [verwijzing naar fonetische alfabet](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx)voor meer informatie. | Optioneel |
| pH | Een teken reeks met telefoons die de uitspraak van het woord in het `phoneme`-element opgeven. Als de opgegeven teken reeks niet-herkende telefoons bevat, weigert de TTS-Service (tekst naar spraak) het hele SSML-document en wordt er geen van de spraak uitvoer opgegeven in het document. | Vereist als u fonemen gebruikt. |

**Voorbeelden**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Prosody aanpassen

Het element `prosody` wordt gebruikt om wijzigingen op te geven in de waarden voor de hoogte, het Countour, het bereik, de frequentie, de duur en het volume voor de tekst-naar-spraak-uitvoer. Het `prosody`-element kan tekst en de volgende elementen bevatten: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`en `s`.

Omdat Prosodic kenmerk waarden kunnen variëren per breed bereik, interpreteert de spraak herkenner de toegewezen waarden als een suggestie van wat de werkelijke Prosodic-waarden van de geselecteerde stem moeten zijn. De service tekst naar spraak beperkt of vervangt waarden die niet worden ondersteund. Voor beelden van niet-ondersteunde waarden zijn een Toon hoogte van 1 MHz of een volume van 120.

**Syntaxis**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Eigenschappen**

| Kenmerk | Beschrijving | Vereiste / optioneel |
|-----------|-------------|---------------------|
| afstand | Geeft de hoogte van de basis lijn voor de tekst aan. U kunt de Toon hoogte uitdrukken als:<ul><li>Een absolute waarde, uitgedrukt als een getal gevolgd door ' Hz ' (Hertz). Bijvoorbeeld 600Hz.</li><li>Een relatieve waarde, uitgedrukt als een getal voorafgegaan door ' + ' of '-', gevolgd door ' Hz ' of ' St ', waarmee een hoeveelheid wordt opgegeven om de Toon hoogte te wijzigen. Bijvoorbeeld: + 80Hz of-2st. De ' St ' geeft aan dat de wijzigings eenheid semitone is. Dit is de helft van een Toon (een halve stap) op de standaard diatonic schaal.</li><li>Een constante waarde:<ul><li>x-laag</li><li>gebrek</li><li>drager</li><li>hogesnelheidsnet</li><li>x-high</li><li>standaardinstelling</li></ul></li></ul>. | Optioneel |
| Beschrijving | De contour wordt niet ondersteund voor Neural stemmen. Contour vertegenwoordigt wijzigingen in de hoogte van spraak inhoud als een matrix met doelen op bepaalde tijdstippen in de spraak uitvoer. Elk doel wordt gedefinieerd door sets van parameter paren. Bijvoorbeeld: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>De eerste waarde in elke set para meters geeft de locatie van de pitch wijziging aan als een percentage van de duur van de tekst. Met de tweede waarde geeft u de hoeveelheid op waarmee de Toon hoogte moet worden verhoogd of verlaagd, met behulp van een relatieve waarde of een opsommings waarde voor Pitch (Zie `pitch`). | Optioneel |
| bereik  | Een waarde die het bereik van de hoogte van de tekst aangeeft. U kunt `range` met dezelfde absolute waarden, relatieve waarden of opsommings waarden die worden gebruikt om `pitch`te beschrijven. | Optioneel |
| malen  | Geeft het spreek tempo van de tekst aan. U kunt `rate` als volgt uitdrukken:<ul><li>Een relatieve waarde, uitgedrukt als een getal dat fungeert als een vermenigvuldiger van de standaard instelling. Een waarde van *1* resulteert bijvoorbeeld in geen wijziging in het aantal. Een waarde van *0,5* resulteert in een helft van het aantal. Een waarde van *3* resulteert in een toename van het aantal.</li><li>Een constante waarde:<ul><li>x-langzaam</li><li>Trage</li><li>drager</li><li>hoog</li><li>x-snel</li><li>standaardinstelling</li></ul></li></ul> | Optioneel |
| duration  | De tijds duur die moet verstrijken terwijl de TTS-Service (spraak-synthese) de tekst leest, in seconden of in milliseconden. Bijvoorbeeld, *2s* of *1800ms*. | Optioneel |
| volume  | Hiermee wordt het volume niveau van de spreek spraak aangegeven. U kunt het volume uitdrukken als:<ul><li>Een absolute waarde, uitgedrukt als een getal in het bereik 0,0 tot en met 100,0, van *zacht* naar *hard*. Bijvoorbeeld 75. De standaard waarde is 100,0.</li><li>Een relatieve waarde, uitgedrukt als een getal voorafgegaan door ' + ' of '-', waarmee een hoeveelheid wordt opgegeven om het volume te wijzigen. Bijvoorbeeld + 10 of-5,5.</li><li>Een constante waarde:<ul><li>achtergrond</li><li>x-zacht</li><li>Proof</li><li>drager</li><li>hard</li><li>x-loud</li><li>standaardinstelling</li></ul></li></ul> | Optioneel |

### <a name="change-speaking-rate"></a>Snelheid van spreken wijzigen

Het spreek tempo kan worden toegepast op de standaard stemmen op het niveau van het woord of de zin. Dat de spreek snelheid alleen kan worden toegepast op Neural stemmen op het niveau van de zin.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Volume wijzigen

Volume wijzigingen kunnen worden toegepast op standaard stemmen op het niveau van het woord of de zin. Terwijl volume wijzigingen alleen kunnen worden toegepast op Neural stemmen op het niveau van de zin.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Breedte wijzigen

Wijzigingen in de hoogte kunnen worden toegepast op standaard stemmen op het niveau van het woord of de zin. Dat wijzigingen in de hoogte alleen kunnen worden toegepast op Neural stemmen op het niveau van de zin.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Wijziging inspiratie contour

> [!IMPORTANT]
> De hoogte van Contour wijzigingen wordt niet ondersteund met Neural stemmen.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>het element dict-as  

`say-as` is een optioneel element dat het inhouds type (zoals getal of datum) van de tekst van het element aangeeft. Dit biedt richt lijnen voor de engine voor spraak synthese over hoe u de tekst uitspreekt. 

**Syntaxis**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Eigenschappen**

| Kenmerk | Beschrijving | Vereiste / optioneel |
|-----------|-------------|---------------------|
| interpreteren als | Hiermee wordt het inhouds type van de tekst van het element aangegeven. Zie de onderstaande tabel voor een lijst met typen. | Vereist |
| format | Biedt aanvullende informatie over de exacte opmaak van de tekst van het element voor inhouds typen die mogelijk niet-eenduidige indelingen hebben. SSML definieert indelingen voor inhouds typen die deze gebruiken (Zie de tabel hieronder). | Optioneel |
| specificatie | Hiermee wordt het detail niveau aangegeven dat moet worden gesp roken. Dit kenmerk kan bijvoorbeeld vragen of de spraakherkennings engine Lees tekens uitspreekt. Er zijn geen standaard waarden gedefinieerd voor `detail`. | Optioneel |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Hieronder vindt u de ondersteunde inhouds typen voor de kenmerken `interpret-as` en `format`. Neem het `format` kenmerk alleen op als `interpret-as` is ingesteld op datum en tijd.

| interpreteren als | format | Tolk |
|--------------|--------|----------------|
| address | | De tekst wordt gesp roken als een adres. De engine voor spraak synthese uitspreekt:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Als "Ik ben bij 150the rechtbank voor het noordoosten van Redmond Washington" |
| Cardinal, getal | | De tekst wordt gesp roken als een hoofd getal. De engine voor spraak synthese uitspreekt:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Als "er zijn drie alternatieven." |
| tekens, spellen | | De tekst wordt gesp roken als afzonderlijke letters (gespeld). De engine voor spraak synthese uitspreekt:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Als T E S T. |
| date  | DMY, MDJ, YMD, JDM, YM, my, MD, DM, d, m, y | De tekst wordt als een datum gesp roken. Het kenmerk `format` geeft de notatie van de datum aan (*d = dag, m = maand en y = jaar*). De engine voor spraak synthese uitspreekt:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />"Vandaag is oktober nineteenth 2016." |
| cijfers, number_digit | | De tekst wordt gesp roken als een reeks afzonderlijke cijfers. De engine voor spraak synthese uitspreekt:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Als ' 1 2 3 4 5 6 7 8 9 '. |
| treding | | De tekst wordt gesp roken als een breuk getal. De engine voor spraak synthese uitspreekt:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Als "drie achtstes van een inch" |
| ordinal  | | De tekst wordt gesp roken als een rang nummer. De engine voor spraak synthese uitspreekt:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Als ' Selecteer de derde optie '. |
| telefoonnummer  | | De tekst wordt gesp roken als een telefoon nummer. Het kenmerk `format` mag cijfers bevatten die een land code vertegenwoordigen. Bijvoorbeeld ' 1 ' voor de Verenigde Staten of ' 39 ' voor Italië. De engine voor spraak synthese kan deze informatie gebruiken om de uitspraak van een telefoon nummer te hand leiding. Het telefoon nummer kan ook de land code bevatten, en als dit het geval is, heeft dat prioriteit boven de land code in de `format`. De engine voor spraak synthese uitspreekt:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Als ' mijn nummer is gebieds code 8 8 8 5 5 5 1 2 1 2 '. |
| tijd | hms12, hms24 | De tekst wordt als een tijd gesp roken. Het kenmerk `format` geeft aan of de tijd wordt opgegeven met een 12-uurs klok (hms12) of een 24-uurs klok (hms24). Gebruik een dubbele punt als scheidings waarde voor uren, minuten en seconden. Hier volgen enkele voor beelden van geldige tijd: 12:35, 1:14:32, 08:15 en 02:50:45. De engine voor spraak synthese uitspreekt:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Als "de trein maakt op vier A M." |

**Gebruik**

Het `say-as`-element mag alleen tekst bevatten.

**Voorbeeld**

De engine voor spraak synthese spreekt het volgende voor beeld uit als ' uw eerste aanvraag is voor één kamer op nineteenth oktober 20 10 met vroege aankomst bij 12 35 P M. '
 
```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
    <p>
    Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
    on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
    </p>
</speak>
```


## <a name="add-recorded-audio"></a>Opgenomen audio toevoegen

`audio` is een optioneel element waarmee u MP3-audio kunt invoegen in een SSML-document. De hoofd tekst van het audio-element kan onbewerkte tekst of SSML markeringen bevatten die worden gesp roken als het audio bestand niet beschikbaar is of niet kan worden afgespeeld. Daarnaast kan het `audio`-element tekst en de volgende elementen bevatten: `audio`, `break`, `p`, `s`, `phoneme`, `prosody`, `say-as`en `sub`.

Audio die is opgenomen in het SSML-document moet voldoen aan deze vereisten:

* De MP3 moet worden gehost op een HTTPS-eind punt dat toegankelijk is via internet. HTTPS is vereist en het domein dat als host fungeert voor het MP3-bestand moet een geldig, vertrouwd SSL-certificaat bevatten.
* MP3 moet een geldig MP3-bestand (MPEG v2) zijn.
* De bitsnelheid moet 48 kbps zijn.
* Het sample frequentie moet 16000 Hz zijn.
* De gecombineerde totale tijd voor alle tekst-en audio bestanden in één antwoord mag niet groter zijn dan 90 (90) seconden.
* De MP3 mag geen klantspecifieke of andere gevoelige informatie bevatten.

**Syntaxis**

```xml
<audio src="string"/></audio>
```

**Eigenschappen**

| Kenmerk | Beschrijving | Vereiste / optioneel |
|-----------|-------------|---------------------|
| src | Hiermee geeft u de locatie/URL van het audio bestand op. | Vereist als u het audio-element in uw SSML-document gebruikt. |

**Voorbeeld**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>Achtergrond geluid toevoegen

Met het `mstts:backgroundaudio`-element kunt u achtergrond geluid toevoegen aan uw SSML-documenten (of een audio bestand combi neren met tekst-naar-spraak). Met `mstts:backgroundaudio` kunt u een audio bestand op de achtergrond laten vervagen aan het begin van tekst-naar-spraak en uitfaden aan het einde van tekst-naar-spraak.

Als de achtergrond audio kleiner is dan de tekst-naar-spraak of de uitfaden, wordt er een lus weer gegeven. Als de tekst-naar-spraak langer is, wordt deze gestopt wanneer het uitfadepen is voltooid.

Er is slechts één achtergrond geluids bestand toegestaan per SSML-document. U kunt echter `audio`-Tags in het `voice`-element samenvoegen om extra audio aan uw SSML-document toe te staan.

**Syntaxis**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Eigenschappen**

| Kenmerk | Beschrijving | Vereiste / optioneel |
|-----------|-------------|---------------------|
| src | Hiermee geeft u de locatie/URL van het audio bestand op de achtergrond. | Vereist als u achtergrond geluid in uw SSML-document gebruikt. |
| volume | Hiermee geeft u het volume van het audio bestand op de achtergrond. **Geaccepteerde waarden**: `0` `100` inclusief. De standaardwaarde is `1`. | Optioneel |
| Infaden | Hiermee geeft u de duur van de achtergrond audio infaden in milliseconden. De standaard waarde is `0`, die gelijk is aan niet vervagen in. **Geaccepteerde waarden**: `0` `10000` inclusief.  | Optioneel |
| fadeout | Hiermee geeft u de duur van de achtergrond audio vervagen in milliseconden. De standaard waarde is `0`, die gelijk is aan geen uitfaden. **Geaccepteerde waarden**: `0` `10000` inclusief.  | Optioneel |

**Voorbeeld**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Volgende stappen

* [Taal ondersteuning: stemmen, land instellingen, talen](language-support.md)
