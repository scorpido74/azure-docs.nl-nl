---
title: Methode voor vertalen van de vertaal-api voor vertalers
titleSuffix: Azure Cognitive Services
description: Begrijp de parameters, kopteksten en hoofdberichten voor de vertaaltekst-API-translate-methode azure Cognitive Services om tekst te vertalen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: 1821623fbe2a22234af649934ac06e72897a19cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052399"
---
# <a name="translator-text-api-30-translate"></a>Translator Text API 3.0: Vertalen

Vertaalt tekst.

## <a name="request-url"></a>Aanvraag-URL

Stuur `POST` een verzoek naar:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parameters aanvragen

De aangezochtparameters die op de querytekenreeks worden doorgegeven, zijn:

### <a name="required-parameters"></a>Vereiste parameters

<table width="100%">
  <th width="20%">Queryparameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>api-versie</td>
    <td><em>Vereiste parameter</em>.<br/>Versie van de API die door de client is aangevraagd. Waarde moet <code>3.0</code>zijn .</td>
  </tr>
  <tr>
    <td>tot</td>
    <td><em>Vereiste parameter</em>.<br/>Hiermee geeft u de taal van de uitvoertekst op. De doeltaal moet een van de ondersteunde <code>translation</code> <a href="./v3-0-languages.md">talen</a> zijn die in het toepassingsgebied zijn opgenomen. Bijvoorbeeld, gebruiken <code>to=de</code> om te vertalen naar het Duits.<br/>Het is mogelijk om naar meerdere talen tegelijk te vertalen door de parameter in de querytekenreeks te herhalen. Bijvoorbeeld, gebruiken <code>to=de&to=it</code> om te vertalen naar het Duits en Italiaans.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Optionele parameters

<table width="100%">
  <th width="20%">Queryparameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>from</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u de taal van de invoertekst op. Ontdek vanuit welke talen u vertalen door <code>translation</code> ondersteunde <a href="./v3-0-languages.md">talen</a> op te zoeken met behulp van het bereik. Als <code>from</code> de parameter niet is opgegeven, wordt automatische taaldetectie toegepast om de brontaal te bepalen. <br/><br/>U moet <code>from</code> de parameter gebruiken in plaats van autodetectie bij het gebruik van de <a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">functie dynamische woordenlijst.</a></td>
  </tr>  
  <tr>
    <td>textType</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee bepaalt u of de tekst die wordt vertaald platte tekst of HTML-tekst is. Elke HTML moet een goed gevormd, compleet element zijn. Mogelijke waarden <code>plain</code> zijn: (standaard) of <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Optionele parameter</em>.<br/>Een tekenreeks die de categorie (domein) van de vertaling opgeeft. Deze parameter wordt gebruikt om vertalingen te krijgen van een aangepast systeem gebouwd met <a href="../customization.md">Aangepaste Vertaler.</a> Voeg de categorie-id van uw <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projectgegevens</a> van aangepaste vertaler toe aan deze parameter om uw geïmplementeerde aangepaste systeem te gebruiken. Standaardwaarde is: <code>general</code>.</td>
  </tr>
  <tr>
    <td>godslasteringActie</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u aan hoe godslasteringen moeten worden behandeld in vertalingen. Mogelijke waarden <code>NoAction</code> zijn: <code>Marked</code> (standaard) of <code>Deleted</code>. Om manieren te begrijpen om godslastering te behandelen, zie <a href="#handle-profanity">Godslastering behandeling</a>.</td>
  </tr>
  <tr>
    <td>godslasteringMarker</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u op hoe godslasteringen moeten worden gemarkeerd in vertalingen. Mogelijke waarden <code>Asterisk</code> zijn: (standaard) of <code>Tag</code>. Om manieren te begrijpen om godslastering te behandelen, zie <a href="#handle-profanity">Godslastering behandeling</a>.</td>
  </tr>
  <tr>
    <td>inclusief Uitlijning</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u op of uitlijningsprojectie moet worden opgenomen van brontekst naar vertaalde tekst. Mogelijke waarden <code>true</code> zijn: of <code>false</code> (standaard). </td>
  </tr>
  <tr>
    <td>inclusief SentenceLength</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u op of de invoertekst en de vertaalde tekst moeten worden opgemaakt. Mogelijke waarden <code>true</code> zijn: of <code>false</code> (standaard).</td>
  </tr>
  <tr>
    <td>voorgesteldUit</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u een terugvaltaal op als de taal van de invoertekst niet kan worden geïdentificeerd. Taalautomatische detectie wordt toegepast <code>from</code> wanneer de parameter wordt weggelaten. Als detectie mislukt, wordt de <code>suggestedFrom</code> taal aangenomen.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u het script van de invoertekst op.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u het script van de vertaalde tekst op.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u op dat de service mag terugvallen op een algemeen systeem wanneer een aangepast systeem niet bestaat. Mogelijke waarden <code>true</code> zijn: (standaard) of <code>false</code>.<br/><br/><code>allowFallback=false</code>hiermee wordt aangegeven dat de vertaling <code>category</code> alleen systemen mag gebruiken die zijn opgeleid voor de door de aanvraag opgegeven. Als een vertaling voor taal X naar taal Y chaining via een draaitaal E vereist, moeten alle systemen in de keten (X->E en E->Y) op maat worden gemaakt en dezelfde categorie hebben. Als er geen systeem wordt gevonden met de specifieke categorie, wordt een statuscode van 400 keer teruggekoppeld. <code>allowFallback=true</code>hiermee wordt opgegeven dat de service mag terugvallen op een algemeen systeem wanneer een aangepast systeem niet bestaat.
</td>
  </tr>
</table> 

Aanvraagkoppen zijn:

<table width="100%">
  <th width="20%">Headers</th>
  <th>Beschrijving</th>
  <tr>
    <td>Verificatiekop(en)</td>
    <td><em>Vereiste aanvraagheader</em>.<br/>Zie <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">beschikbare opties voor verificatie</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Vereiste aanvraagheader</em>.<br/>Hiermee geeft u het inhoudstype van de payload op.<br/> Geaccepteerde <code>application/json; charset=UTF-8</code>waarde is .</td>
  </tr>
  <tr>
    <td>Inhoudslengte</td>
    <td><em>Vereiste aanvraagheader</em>.<br/>De lengte van het verzoek lichaam.</td>
  </tr>
  <tr>
    <td>X-ClientTraceid</td>
    <td><em>Optioneel</em>.<br/>Een door de klant gegenereerde GUID om de aanvraag op unieke wijze te identificeren. U deze koptekst weglaten als u de traceid <code>ClientTraceId</code>in de querytekenreeks opneemt met een queryparameter met de naam.</td>
  </tr>
</table> 

## <a name="request-body"></a>Aanvraagbody

De instantie van het verzoek is een JSON array. Elk array-element is een JSON-object met een tekenreekseigenschap met de naam `Text`, die de tekenreeks vertegenwoordigt die moet worden vertaald.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

De volgende beperkingen zijn van toepassing:

* De array kan hoogstens 100 elementen hebben.
* De volledige tekst in de aanvraag mag niet groter zijn dan 5.000 tekens, inclusief spaties.

## <a name="response-body"></a>Hoofdtekst van de reactie

Een succesvol antwoord is een JSON-array met één resultaat voor elke tekenreeks in de invoerarray. Een resultaatobject bevat de volgende eigenschappen:

  * `detectedLanguage`: Een object dat de gedetecteerde taal beschrijft via de volgende eigenschappen:

      * `language`: Een tekenreeks die de code van de gedetecteerde taal weergeeft.

      * `score`: Een floatwaarde die het vertrouwen in het resultaat aangeeft. De score is tussen nul en een en een lage score geeft een laag vertrouwen.

    De `detectedLanguage` eigenschap is alleen aanwezig in het resultaatobject wanneer taalautomatische detectie wordt gevraagd.

  * `translations`: Een scala aan vertaalresultaten. De grootte van de array komt overeen `to` met het aantal doeltalen dat is opgegeven via de queryparameter. Elk element in de array omvat:

    * `to`: Een tekenreeks die de taalcode van de doeltaal weergeeft.

    * `text`: Een tekenreeks met de vertaalde tekst.

    * `transliteration`: Een object dat de vertaalde `toScript` tekst in het script geeft dat door de parameter is opgegeven.

      * `script`: Een tekenreeks die het doelscript opgeeft.   

      * `text`: Een tekenreeks met de vertaalde tekst in het doelscript.

    Het `transliteration` object is niet inbegrepen als transliteratie niet plaatsvindt.

    * `alignment`: Een object met een `proj`eigenschap met één tekenreeks met de naam , waarmee invoertekst wordt toegewezen aan vertaalde tekst. De uitlijningsinformatie wordt alleen `includeAlignment` verstrekt `true`wanneer de parameter request . Uitlijning wordt geretourneerd als een tekenreekswaarde `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`van de volgende notatie: .  De dubbele punt scheidt begin- en eindindex, het streepje scheidt de talen en de ruimte scheidt de woorden. Een woord kan worden uitgelijnd met nul, een of meerdere woorden in de andere taal, en de uitgelijnde woorden kunnen niet-aaneengesloten zijn. Wanneer er geen uitlijningsinformatie beschikbaar is, is het uitlijningselement leeg. Zie [Uitlijningsinformatie verkrijgen](#obtain-alignment-information) voor een voorbeeld en beperkingen.

    * `sentLen`: Een object dat zinsgrenzen in de invoer- en uitvoerteksten teruggeeft.

      * `srcSentLen`: Een gehele array die de lengtes van de zinnen in de invoertekst weergeeft. De lengte van de array is het aantal zinnen en de waarden zijn de lengte van elke zin.

      * `transSentLen`: Een gehele array die de lengtes van de zinnen in de vertaalde tekst weergeeft. De lengte van de array is het aantal zinnen en de waarden zijn de lengte van elke zin.

    Zinsgrenzen worden alleen opgenomen `includeSentenceLength` `true`wanneer de parameter request .

  * `sourceText`: Een object met een `text`enkele tekenreekseigenschap met de naam , waardoor de invoertekst in het standaardscript van de brontaal wordt weergegeven. `sourceText`eigenschap is alleen aanwezig wanneer de invoer wordt uitgedrukt in een script dat niet het gebruikelijke script voor de taal is. Als de invoer bijvoorbeeld Arabisch geschreven in `sourceText.text` het Latijnse schrift was, zou dezelfde Arabische tekst worden omgezet in Arabisch schrift.

Voorbeeld van JSON-antwoorden wordt gegeven in de sectie [voorbeelden.](#examples)

## <a name="response-headers"></a>Antwoordheaders

<table width="100%">
  <th width="20%">Headers</th>
  <th>Beschrijving</th>
    <tr>
    <td>X-RequestId</td>
    <td>Waarde gegenereerd door de service om de aanvraag te identificeren. Het wordt gebruikt voor het oplossen van problemen.</td>
  </tr>
  <tr>
    <td>X-MT-systeem</td>
    <td>Hiermee geeft u het systeemtype op dat is gebruikt voor vertaling voor elke 'naar'-taal die voor vertaling is aangevraagd. De waarde is een door komma's gescheiden lijst met tekenreeksen. Elke tekenreeks geeft een type aan:<br/><ul><li>Aangepaste - Aanvraag bevat een aangepast systeem en ten minste één aangepast systeem werd gebruikt tijdens de vertaling.</li><li>Team - Alle andere verzoeken</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Statuscodes voor antwoord

Hieronder volgen de mogelijke HTTP-statuscodes die een aanvraag retourneert. 

<table width="100%">
  <th width="20%">Statuscode</th>
  <th>Beschrijving</th>
  <tr>
    <td>200</td>
    <td>Geslaagd.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Een van de queryparameters ontbreekt of is ongeldig. De aanvraagparameters corrigeren voordat u het opnieuw probeert.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>De aanvraag kan niet worden geverifieerd. Controleer of referenties zijn opgegeven en geldig zijn.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Het verzoek is niet geautoriseerd. Controleer de foutmelding details. Dit geeft vaak aan dat alle gratis vertalingen met een proefabonnement zijn opgebruikt.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>De aanvraag kan niet worden ingewilligd omdat er een resource ontbreekt. Controleer de foutmelding details. Bij gebruik <code>category</code>van een aangepaste, dit geeft vaak aan dat de aangepaste vertaling systeem is nog niet beschikbaar om verzoeken te serveren. Het verzoek moet opnieuw worden geprobeerd na een wachttijd (bijvoorbeeld 1 minuut).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>De server heeft de aanvraag afgewezen omdat de client de aanvraaglimieten heeft overschreden.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Er is een onverwachte fout opgetreden. Als de fout blijft bestaan, meldt u deze met: datum <code>X-RequestId</code>en tijd van de <code>X-ClientTraceId</code>fout, aanvraag-id van antwoordkop en client-id van de hoofdtekst van de aanvraag .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server tijdelijk niet beschikbaar. Probeer het verzoek opnieuw. Als de fout blijft bestaan, meldt u deze met: datum <code>X-RequestId</code>en tijd van de <code>X-ClientTraceId</code>fout, aanvraag-id van antwoordkop en client-id van de hoofdtekst van de aanvraag .</td>
  </tr>
</table> 

Als er een fout optreedt, retourneert de aanvraag ook een JSON-foutantwoord. De foutcode is een 6-cijferig getal dat de 3-cijferige HTTP-statuscode combineert, gevolgd door een 3-cijferig getal om de fout verder te categoriseren. Veelvoorkomende foutcodes zijn te vinden op de [referentiepagina v3 Translator Text API.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) 

## <a name="examples"></a>Voorbeelden

### <a name="translate-a-single-input"></a>Eén invoer vertalen

In dit voorbeeld ziet u hoe u een enkele zin vertaalt van Het Engels naar vereenvoudigd Chinees.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Het antwoordorgaan is:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

De `translations` array bevat één element, dat de vertaling van het enkele stuk tekst in de invoer biedt.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Eén invoer vertalen met automatische taaldetectie

In dit voorbeeld ziet u hoe u een enkele zin vertaalt van Het Engels naar vereenvoudigd Chinees. De aanvraag geeft de invoertaal niet op. In plaats daarvan wordt automatische detectie van de brontaal gebruikt.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Het antwoordorgaan is:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
Het antwoord is vergelijkbaar met het antwoord uit het vorige voorbeeld. Aangezien taalautomatische detectie is aangevraagd, bevat het antwoord ook informatie over de taal die voor de invoertekst wordt gedetecteerd. 

### <a name="translate-with-transliteration"></a>Vertalen met transliteratie

Laten we het vorige voorbeeld uitbreiden door transliteratie toe te voegen. Het volgende verzoek vraagt om een Chinese vertaling geschreven in latijns schrift.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Het antwoordorgaan is:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Het vertaalresultaat bevat `transliteration` nu een eigenschap, die de vertaalde tekst met Latijnse tekens geeft.

### <a name="translate-multiple-pieces-of-text"></a>Meerdere stukken tekst vertalen

Het vertalen van meerdere tekenreeksen tegelijk is gewoon een kwestie van het specificeren van een array van strings in de aanvraagbody.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Het antwoordorgaan is:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Vertalen naar meerdere talen

In dit voorbeeld ziet u hoe u dezelfde invoer naar meerdere talen in één aanvraag vertaalt.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Het antwoordorgaan is:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Omgaan met godslastering

Normaal gesproken behoudt de Translator-service godslastering die in de bron in de vertaling aanwezig is. De mate van godslastering en de context die woorden profane verschillen tussen culturen, en als gevolg daarvan de mate van godslastering in de doeltaal kan worden versterkt of verminderd.

Als u wilt voorkomen dat u godslastering in de vertaling krijgt, ongeacht de aanwezigheid van godslastering in de brontekst, u de optie voor godslastering filteren gebruiken. Met de optie u kiezen of u godslastering wilt verwijderen, of u godslastering wilt markeren met de juiste tags (zodat u de optie hebt om uw eigen nabewerking toe te voegen), of dat u geen actie wilt ondernemen. De geaccepteerde `ProfanityAction` `Deleted`waarden `Marked` `NoAction` van zijn en (standaard).

<table width="100%">
  <th width="20%">GodslasteringActie</th>
  <th>Actie</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Dit is de standaardinstelling. Godslastering gaat van bron naar doelwit.<br/><br/>
    <strong>Voorbeeld Bron (Japans)</strong>:<br/>
    <strong>Voorbeeld Vertaling (Engels)</strong>: Hij is een jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Profane woorden zullen worden verwijderd uit de output zonder vervanging.<br/><br/>
    <strong>Voorbeeld Bron (Japans)</strong>:<br/>
    <strong>Voorbeeld vertaling (Engels)</strong>: Hij is een.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Profane woorden worden vervangen door een markering in de uitvoer. De markering is <code>ProfanityMarker</code> afhankelijk van de parameter.<br/><br/>
Voor <code>ProfanityMarker=Asterisk</code>, worden de profane woorden vervangen door <code>***</code>:<br/>
    <strong>Voorbeeld Bron (Japans)</strong>:<br/>
    <strong>Voorbeeld Vertaling (Engels)</strong>: \* \* \*Hij is een .<br/><br/>
<code>ProfanityMarker=Tag</code>Want, profane woorden worden &lt;omringd door&gt; &lt;XML-tags&gt;godslastering en /godslastering:<br/>
    <strong>Voorbeeld Bron (Japans)</strong>:<br/>
    <strong>Voorbeeld Vertaling (Engels)</strong>: &lt;Hij&gt;is een&lt;godslastering jackass / godslastering&gt;.
  </tr>
</table> 

Bijvoorbeeld:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
Dit geeft als resultaat:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Vergelijk met:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

Dat laatste verzoek keert terug:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Inhoud vertalen met markeringen en beslissen wat er wordt vertaald

Het is gebruikelijk om inhoud te vertalen die markeringen bevat, zoals inhoud van een HTML-pagina of inhoud uit een XML-document. Voeg queryparameter toe `textType=html` bij het vertalen van inhoud met tags. Daarnaast is het soms handig om specifieke inhoud uit te sluiten van vertaling. U het `class=notranslate` kenmerk gebruiken om inhoud op te geven die in de oorspronkelijke taal moet blijven. In het volgende voorbeeld wordt `div` de inhoud in het eerste element `div` niet vertaald, terwijl de inhoud in het tweede element wordt vertaald.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Hier is een voorbeeld verzoek om te illustreren.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Het antwoord is:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Uitlijningsinformatie verkrijgen

Als u uitlijningsgegevens wilt ontvangen, geeft u op de querytekenreeks op. `includeAlignment=true`

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

Het antwoord is:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

De uitlijningsinformatie `0:2-0:1`begint met , wat betekent dat`The`de eerste drie tekens in de`La`brontekst ( ) toewijzen aan de eerste twee tekens in de vertaalde tekst ( ).

#### <a name="limitations"></a>Beperkingen
Het verkrijgen van uitlijningsinformatie is een experimentele functie die we hebben ingeschakeld voor prototyping-onderzoek en ervaringen met potentiële zinskaarten. We kunnen ervoor kiezen om dit in de toekomst niet meer te ondersteunen. Hier zijn enkele van de opmerkelijke beperkingen waarbij uitlijningen niet worden ondersteund:

* Uitlijning is niet beschikbaar voor tekst in HTML-indeling, d.w.z. textType=html
* Uitlijning wordt alleen geretourneerd voor een subset van de taalparen:
  - van Engels naar een andere taal;
  - van elke andere taal naar het Engels, behalve chinees vereenvoudigd, Chinees traditioneel en Lets naar Engels;
  - van Japans tot Koreaans of van Koreaans naar Japans.
* U ontvangt geen uitlijning als de zin een ingeblikte vertaling is. Voorbeeld van een ingeblikte vertaling is "Dit is een test", "Ik hou van je" en andere hoge frequentie zinnen.
* Uitlijning is niet beschikbaar wanneer u een van de benaderingen toepast om vertaling te voorkomen zoals [hier](../prevent-translation.md) beschreven

### <a name="obtain-sentence-boundaries"></a>Zinsgrenzen verkrijgen

Als u informatie wilt ontvangen over de lengte `includeSentenceLength=true` van de zin in de brontekst en de vertaalde tekst, geeft u op de querytekenreeks op.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

Het antwoord is:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Vertalen met dynamisch woordenboek

Als u de vertaling die u wilt toepassen op een woord of een woordaluist al weet, u deze als opmerking in de aanvraag opgeven. Het dynamische woordenboek is alleen veilig voor samengestelde naamwoorden zoals eigen namen en productnamen.

De markering die moet worden opgegeven, gebruikt de volgende syntaxis.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Denk bijvoorbeeld aan de Engelse zin 'Het woord wordomatisch is een woordenboekvermelding'. Als u het woord _woord woord woordmatisch_ in de vertaling wilt behouden, stuurt u het verzoek:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Het resultaat is:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Deze functie werkt op `textType=text` dezelfde `textType=html`manier met of met . De functie moet spaarzaam worden gebruikt. De juiste en veel betere manier om vertaling aan te passen is door aangepaste vertaler te gebruiken. Custom Translator maakt optimaal gebruik van context en statistische waarschijnlijkheden. Als u trainingsgegevens hebt of veroorloven die uw werk of woordgroep in context weergeven, krijgt u veel betere resultaten. [Meer informatie over aangepaste vertaler](../customization.md).
