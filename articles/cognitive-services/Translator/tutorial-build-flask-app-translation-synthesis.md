---
title: 'Zelf studie: een kolf-app bouwen om tekst-Translator Text-API te vertalen, samen te stellen en te analyseren'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie bouwt u een op een kolf gebaseerde web-app om tekst te vertalen, sentiment te analyseren en vertaalde tekst in spraak te verenigen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 12/09/2019
ms.author: swmachan
ms.openlocfilehash: 0075862e198ce67cc7367efe94d624ad18e6eb3b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984164"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Zelf studie: een kolf-app bouwen met Azure Cognitive Services

In deze zelf studie bouwt u een kolf-web-app die gebruikmaakt van Azure Cognitive Services voor het vertalen van tekst, het analyseren van sentiment en het samen stellen van vertaalde tekst in spraak. Onze nadruk ligt op de python-code en kolven die onze toepassing inschakelen, maar we helpen u echter met de HTML en Java script die de app samen haalt. Als u problemen ondervindt, laat het ons weten met de feedback knop hieronder.

Deze zelf studie bevat de volgende onderwerpen:

> [!div class="checklist"]
> * Sleutels voor Azure-abonnementen ophalen
> * Uw ontwikkel omgeving instellen en afhankelijkheden installeren
> * Een kolf-app maken
> * De Translator Text-API gebruiken om tekst te vertalen
> * Gebruik Text Analytics om positieve/negatieve sentiment van invoer tekst en vertalingen te analyseren
> * Spraak Services gebruiken om vertaalde tekst te converteren naar gesynthesizerde spraak
> * Uw erlenmeyer-app lokaal uitvoeren

> [!TIP]
> Als u wilt overs Laan en alle code in één keer wilt zien, kunt u het volledige voor beeld samen met de instructies voor builds beschikbaar op [github](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Wat is fles?

De kolf is een microframework voor het maken van webtoepassingen. Dit houdt in dat de kolf voorziet in hulpprogram ma's, Bibliotheken en technologieën waarmee u een webtoepassing kunt bouwen. Deze webtoepassing kan sommige webpagina's, een blog, een wiki of als een webgebaseerde agenda toepassing of een commerciële website zijn.

Voor degenen die zich willen uitbrengen na deze zelf studie, zijn er een paar handige koppelingen:

* [Documentatie over de kolf](http://flask.pocoo.org/)
* [Kolf voor Dummies: de hand leiding van een beginner](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Vereisten

Laten we de software en abonnements sleutels bekijken die u nodig hebt voor deze zelf studie.

* [Python 3.5.2 of hoger](https://www.python.org/downloads/)
* [Git-hulpprogram ma's](https://git-scm.com/downloads)
* Een IDE-of tekst editor, zoals [Visual Studio code](https://code.visualstudio.com/) of [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) of [Firefox](https://www.mozilla.org/firefox)
* Een **Translator text** -abonnements sleutel (Houd er rekening mee dat u geen regio hoeft te selecteren.)
* Een **Text Analytics** -abonnements sleutel in de regio **VS-West** .
* Een abonnements sleutel voor **spraak Services** in de regio **VS-West** .

## <a name="create-an-account-and-subscribe-to-resources"></a>Een account maken en een abonnement nemen op resources

Zoals eerder vermeld, hebt u drie abonnements sleutels nodig voor deze zelf studie. Dit betekent dat u in uw Azure-account een resource moet maken voor:
* Translator Text
* Tekstanalyse
* Spraakservices

Gebruik [in de Azure Portal een Cognitive Services account maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) voor stapsgewijze instructies voor het maken van resources.

> [!IMPORTANT]
> Voor deze zelf studie maakt u uw resources in de regio vs-West. Als u een andere regio gebruikt, moet u de basis-URL in elk van uw python-bestanden aanpassen.

## <a name="set-up-your-dev-environment"></a>Uw ontwikkelaarsomgeving instellen

Voordat u uw erlenmeyer-web-app bouwt, moet u een werkmap voor het project maken en enkele Python-pakketten installeren.

### <a name="create-a-working-directory"></a>Een werkmap maken

1. Open de opdracht regel (Windows) of de Terminal (macOS/Linux). Maak vervolgens een werkmap en onderliggende mappen voor uw project:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Ga naar de werkmap van uw project:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Uw virtuele omgeving maken en activeren met `virtualenv`

Laten we een virtuele omgeving maken voor onze kolf-app met behulp van `virtualenv`. Als u een virtuele omgeving gebruikt, zorgt u ervoor dat u beschikt over een schone omgeving waarmee u kunt werken.

1. Voer in uw werkmap de volgende opdracht uit om een virtuele omgeving te maken: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   We hebben expliciet aangegeven dat de virtuele omgeving python 3 moet gebruiken. Dit zorgt ervoor dat gebruikers met meerdere python-installaties de juiste versie gebruiken.

   **Windows CMD/Windows bash:**
   ```
   virtualenv venv
   ```
   We noemen uw virtuele omgeving venv om alles eenvoudig te kunnen blijven gebruiken.

2. De opdrachten voor het activeren van uw virtuele omgeving variëren, afhankelijk van uw platform/shell:   

   | Platform | Shell | Opdracht |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Opdrachtregel | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Nadat u deze opdracht hebt uitgevoerd, moet u de opdracht regel of terminal sessie voorzien van `venv`.

3. U kunt de sessie op elk gewenst moment deactiveren door deze te typen op de opdracht regel of de terminal: `deactivate`.

> [!NOTE]
> Python bevat uitgebreide documentatie voor het maken en beheren van virtuele omgevingen, Zie [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Installatie aanvragen

Aanvragen is een populaire module die wordt gebruikt voor het verzenden van HTTP 1,1-aanvragen. U hoeft geen query teken reeksen hand matig toe te voegen aan uw Url's of u kunt uw POST-gegevens coderen.

1. Als u aanvragen wilt installeren, voert u de volgende handelingen uit:

   ```
   pip install requests
   ```

> [!NOTE]
> Als u meer wilt weten over aanvragen, raadpleegt u [aanvragen: http voor mensen](https://2.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Fles installeren en configureren

Daarna moet de kolf worden geïnstalleerd. De kolf verwerkt de route ring voor onze web-app en stelt ons in staat om server-naar-server-aanroepen te maken die de abonnements sleutels van de eind gebruiker verbergen.

1. Voer de volgende handelingen uit om de kolf te installeren:
   ```
   pip install Flask
   ```
   Laten we controleren of de kolf is geïnstalleerd. Uitvoeren:
   ```
   flask --version
   ```
   De versie moet worden afgedrukt op Terminal. Niets anders betekent er iets fout gegaan.

2. Als u de kolf-app wilt uitvoeren, kunt u de kolf opdracht of de schakel optie-m van python met fles gebruiken. Voordat u dit kunt doen, moet u de Terminal waarmee u de app wilt laten werken door de `FLASK_APP` omgevings variabele te exporteren:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Uw erlenmeyer-app maken

In deze sectie gaat u een Barebones-kolf-app maken die een HTML-bestand retourneert wanneer gebruikers de hoofdmap van uw app aanraken. U hoeft niet te veel tijd te best Eden aan het uit elkaar halen van de code, maar u kunt het later opnieuw proberen.

### <a name="what-is-a-flask-route"></a>Wat is een kolf route?

We nemen even de tijd om te praten over '[routes](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)'. Route ring wordt gebruikt om een URL aan een specifieke functie te koppelen. Fles maakt gebruik van Routeer voor het registreren van functies voor specifieke url's. Wanneer een gebruiker bijvoorbeeld navigeert naar de hoofdmap (`/`) van de web-app, wordt `index.html` weer gegeven.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Laten we een voor beeld bekijken om deze start te hameren.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Deze code zorgt ervoor dat wanneer een gebruiker navigeert naar `http://your-web-app.com/about` dat het `about.html` bestand wordt gerenderd.

Hoewel deze voor beelden laten zien hoe HTML-pagina's voor een gebruiker worden weer gegeven, kunnen routes ook worden gebruikt voor het aanroepen van Api's wanneer een knop wordt ingedrukt of een wille keurig aantal acties uitvoeren zonder dat u van de start pagina hoeft te navigeren. U ziet dit in actie wanneer u routes maakt voor vertaal-, sentiment-en spraak-synthese.

### <a name="get-started"></a>Aan de slag

1. Open het project in uw IDE en maak een bestand met de naam `app.py` in de hoofdmap van uw werkmap. Kopieer vervolgens deze code naar `app.py` en sla het volgende op:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Met dit code blok wordt aangegeven dat de app `index.html` wanneer een gebruiker naar de hoofdmap van uw web-app (`/`) navigeert.

2. Vervolgens gaan we de front-end maken voor onze web-app. Maak een bestand met de naam `index.html` in de map `templates`. Kopieer deze code vervolgens naar `templates/index.html`.

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. We gaan de kolf-app testen. Voer de volgende handelingen uit vanaf de terminal:

   ```
   flask run
   ```

4. Open een browser en navigeer naar de beschik bare URL. U ziet nu de app met één pagina. Druk op **CTRL + c** om de app af te breken.

## <a name="translate-text"></a>Tekst vertalen

Nu u een idee hebt van de werking van een eenvoudige fles-app, kunt u het volgende doen:

* Schrijf een aantal python om de Translator Text-API aan te roepen en een antwoord te retour neren
* Een kolf route maken om uw Python-code aan te roepen
* De HTML bijwerken met een gebied voor tekst invoer en-omzetting, een taal kiezer en de knop vertalen
* Schrijf java script waarmee gebruikers vanuit de HTML kunnen communiceren met uw erlenmeyer-app

### <a name="call-the-translator-text-api"></a>De Translator Text-API aanroepen

Het eerste wat u moet doen, is een functie schrijven om de Translator Text-API aan te roepen. Deze functie moet twee argumenten hebben: `text_input` en `language_output`. Deze functie wordt aangeroepen wanneer een gebruiker op de knop vertalen in uw app drukt. Het tekst gebied in de HTML wordt als `text_input`verzonden en de taal selectie waarde in de HTML wordt als `language_output`verzonden.

1. Laten we beginnen met het maken van een bestand met de naam `translate.py` in de hoofdmap van uw werkmap.
2. Voeg vervolgens deze code toe aan `translate.py`. Deze functie heeft twee argumenten: `text_input` en `language_output`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': 'location',
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Voeg uw Translator Text-abonnements sleutel toe en sla deze op.

### <a name="add-a-route-to-apppy"></a>Een route toevoegen aan `app.py`

Vervolgens moet u een route maken in uw erlenmeyer-app die `translate.py`aanroept. Deze route wordt aangeroepen telkens wanneer een gebruiker op de knop vertalen in uw app drukt.

Voor deze app gaat uw route `POST` aanvragen accepteren. Dit komt doordat de functie verwacht dat de tekst wordt vertaald en een uitvoer taal voor de vertaling.

Kolven bieden hulp functies waarmee u elke aanvraag kunt parseren en beheren. In de door gegeven code retourneert `get_json()` de gegevens van de `POST` aanvraag als JSON. Met `data['text']` en `data['to']`worden de waarden voor tekst en uitvoer taal door gegeven aan de `get_translation()` functie die beschikbaar is via `translate.py`. De laatste stap is het retour neren van het antwoord als JSON, omdat u deze gegevens moet weer geven in uw web-app.

In de volgende secties herhaalt u dit proces tijdens het maken van routes voor sentiment analyse en spraak synthese.

1. Open `app.py` en zoek de instructie import aan de bovenkant van `app.py` en voeg de volgende regel toe:

   ```python
   import translate
   ```
   Onze kolf-app kan nu gebruikmaken van de methode die beschikbaar is via `translate.py`.

2. Kopieer deze code naar het einde van `app.py` en sla het volgende op:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>`index.html` bijwerken

Nu u een functie hebt om tekst te vertalen en een route in uw erlenmeyer-app om deze aan te roepen, is de volgende stap het maken van de HTML-code voor uw app. De onderstaande HTML bevat enkele dingen:

* Voorziet in een tekst gebied waar gebruikers tekst kunnen invoeren die moet worden vertaald.
* Bevat een taal kiezer.
* Bevat HTML-elementen om de gedetecteerde taal en betrouwbaarheids scores weer te geven die tijdens de vertaling worden geretourneerd.
* Biedt een alleen-lezen tekst gebied waarin de Vertaal uitvoer wordt weer gegeven.
* Bevat tijdelijke aanduidingen voor sentiment analyse en spraak synthese code die u later in de zelf studie gaat toevoegen aan dit bestand.

We gaan `index.html`bijwerken.

1. Open `index.html` en zoek de volgende code opmerkingen:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. De code opmerkingen vervangen door dit HTML-blok:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

De volgende stap is het schrijven van een Java script. Dit is de brug tussen uw HTML-en fles route.

### <a name="create-mainjs"></a>`main.js` maken  

Het `main.js` bestand is de brug tussen uw HTML-en fles route. Uw app gebruikt een combi natie van jQuery, Ajax en XMLHttpRequest voor het weer geven van inhoud en het maken van `POST` aanvragen aan uw erlenmeyer routes.

In de onderstaande code wordt inhoud van de HTML gebruikt om een aanvraag voor uw maat kolf te maken. Met name de inhoud van het tekst gebied en de taal kiezer worden toegewezen aan variabelen en vervolgens door gegeven aan de aanvraag om `translate-text`.

De code herhaalt vervolgens de reactie en werkt de HTML bij met de vertaling, de gedetecteerde taal en de betrouwbaarheids Score.

1. Maak vanuit uw IDE een bestand met de naam `main.js` in de map `static/scripts`.
2. Kopieer deze code naar `static/scripts/main.js`:
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Vertaling testen

Laten we de vertaling testen in de app.

```
flask run
```

Navigeer naar het gegeven server adres. Typ tekst in het invoer gebied, selecteer een taal en klik op vertalen. U moet een vertaling ontvangen. Als het niet werkt, controleert u of u uw abonnements sleutel hebt toegevoegd.

> [!TIP]
> Als de wijzigingen die u hebt aangebracht, niet worden weer gegeven of als de app niet op de verwachte manier werkt, probeert u de cache te wissen of een persoonlijk/incognito-venster te openen.

Druk op **CTRL + c** om de app te beëindigen en vervolgens de kop naar de volgende sectie.

## <a name="analyze-sentiment"></a>Stemming analyseren

De [Text Analytics-API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) kan worden gebruikt voor het uitvoeren van sentiment-analyse, het uitpakken van sleutel zinnen uit tekst of het detecteren van de bron taal. In deze app gaan we sentiment-analyse gebruiken om te bepalen of de gegeven tekst positief, neutraal of negatief is. De API retourneert een numerieke score tussen 0 en 1. Scores dicht bij 1 geven een positieve stemming aan en scores dicht bij 0 geven een negatieve stemming aan.

In deze sectie gaat u een aantal dingen doen:

* Een aantal python schrijven om de Text Analytics-API aan te roepen om de sentiment-analyse uit te voeren en een antwoord te retour neren
* Een kolf route maken om uw Python-code aan te roepen
* De HTML bijwerken met een gebied voor sentiment-scores en een knop voor het uitvoeren van analyses
* Schrijf java script waarmee gebruikers vanuit de HTML kunnen communiceren met uw erlenmeyer-app

### <a name="call-the-text-analytics-api"></a>De Tekstanalyse-API aanroepen

We gaan een functie schrijven om de Text Analytics-API aan te roepen. Deze functie kan vier argumenten hebben: `input_text`, `input_language`, `output_text`en `output_language`. Deze functie wordt aangeroepen wanneer een gebruiker op de knop sentiment-analyse uitvoeren in uw app drukt. Gegevens die door de gebruiker worden verschaft vanuit het tekst gebied en de taal kiezer, evenals de gedetecteerde taal en de Vertaal uitvoer worden bij elke aanvraag meegeleverd. Het antwoord object bevat sentiment-scores voor de bron en vertaling. In de volgende secties gaat u een Java script schrijven om het antwoord te parseren en te gebruiken in uw app. We richten ons nu op het aanroepen van de Text Analytics-API.

1. We gaan een bestand met de naam `sentiment.py` maken in de hoofdmap van uw werkmap.
2. Voeg vervolgens deze code toe aan `sentiment.py`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Voeg uw Text Analytics-abonnements sleutel toe en sla deze op.

### <a name="add-a-route-to-apppy"></a>Een route toevoegen aan `app.py`

We gaan een route maken in uw erlenmeyer-app die `sentiment.py`aanroept. Deze route wordt aangeroepen telkens wanneer een gebruiker op de knop voor het uitvoeren van sentiment-analyse in uw app drukt. Net als de route voor vertaling accepteert deze route `POST` aanvragen, omdat de functie argumenten verwacht.

1. Open `app.py` en zoek de instructie import boven aan `app.py` en werk deze bij:

   ```python
   import translate, sentiment
   ```
   Onze kolf-app kan nu gebruikmaken van de methode die beschikbaar is via `sentiment.py`.

2. Kopieer deze code naar het einde van `app.py` en sla het volgende op:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>`index.html` bijwerken

Nu u een functie hebt voor het uitvoeren van sentiment analyse en een route in uw erlenmeyer-app om deze aan te roepen, is de volgende stap het schrijven van de HTML-code voor uw app. De onderstaande HTML bevat enkele dingen:

* Hiermee wordt een knop aan uw app toegevoegd voor het uitvoeren van sentiment-analyse
* Voegt een-element toe dat uitleg geeft over de sentiment-Score
* Hiermee wordt een element toegevoegd om de sentiment-scores weer te geven

1. Open `index.html` en zoek de volgende code opmerkingen:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. De code opmerkingen vervangen door dit HTML-blok:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>`main.js` bijwerken

In de onderstaande code wordt inhoud van de HTML gebruikt om een aanvraag voor uw maat kolf te maken. Met name de inhoud van het tekst gebied en de taal kiezer worden toegewezen aan variabelen en vervolgens door gegeven aan de aanvraag voor de `sentiment-analysis` route.

De code herhaalt vervolgens de reactie en werkt de HTML bij met de sentiment-scores.

1. Maak vanuit uw IDE een bestand met de naam `main.js` in de map `static`.

2. Kopieer deze code naar `static/scripts/main.js`:
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Sentiment analyse testen

We gaan sentiment analyse testen in de app.

```
flask run
```

Navigeer naar het gegeven server adres. Typ tekst in het invoer gebied, selecteer een taal en klik op vertalen. U moet een vertaling ontvangen. Klik vervolgens op de knop sentiment-analyse uitvoeren. U ziet nu twee scores. Als het niet werkt, controleert u of u uw abonnements sleutel hebt toegevoegd.

> [!TIP]
> Als de wijzigingen die u hebt aangebracht, niet worden weer gegeven of als de app niet op de verwachte manier werkt, probeert u de cache te wissen of een persoonlijk/incognito-venster te openen.

Druk op **CTRL + c** om de app te beëindigen en vervolgens de kop naar de volgende sectie.

## <a name="convert-text-to-speech"></a>Tekst naar spraak converteren

Met de [tekst-naar-spraak-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) kan uw app tekst converteren naar natuurlijke menselijke-achtige gesynthesizerde spraak. De service biedt ondersteuning voor standaard-, Neural-en aangepaste stemmen. De voor beeld-app gebruikt een aantal van de beschik bare stemmen voor een volledige lijst. Zie [ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech).

In deze sectie gaat u een aantal dingen doen:

* Schrijf enkele python om tekst naar spraak te converteren met de tekst-naar-spraak-API
* Een kolf route maken om uw Python-code aan te roepen
* De HTML bijwerken met een knop om tekst naar spraak te converteren en een element voor het afspelen van audio
* Schrijf java script waarmee gebruikers kunnen communiceren met uw erlenmeyer-app

### <a name="call-the-text-to-speech-api"></a>De API voor tekst naar spraak aanroepen

We gaan een functie schrijven om tekst naar spraak te converteren. Deze functie moet twee argumenten hebben: `input_text` en `voice_font`. Deze functie wordt aangeroepen wanneer een gebruiker op de knop tekst-naar-spraak omzetten in uw app drukt. `input_text` is de Vertaal uitvoer die wordt geretourneerd door de aanroep voor het vertalen van tekst. `voice_font` is de waarde van de letter type-selector voor spraak in de HTML.

1. We gaan een bestand met de naam `synthesize.py` maken in de hoofdmap van uw werkmap.

2. Voeg vervolgens deze code toe aan `synthesize.py`.
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Voeg uw abonnement sleutel voor spraak Services toe en sla het bestand op.

### <a name="add-a-route-to-apppy"></a>Een route toevoegen aan `app.py`

We gaan een route maken in uw erlenmeyer-app die `synthesize.py`aanroept. Deze route wordt aangeroepen telkens wanneer een gebruiker op de knop tekst-naar-spraak omzetten in uw app drukt. Net als de routes voor de analyse van vertalingen en sentiment gaat deze route `POST` aanvragen, aangezien de functie twee argumenten verwacht: de tekst die moet worden gesynthesizerd en het letter type voor afspelen.

1. Open `app.py` en zoek de instructie import boven aan `app.py` en werk deze bij:

   ```python
   import translate, sentiment, synthesize
   ```
   Onze kolf-app kan nu gebruikmaken van de methode die beschikbaar is via `synthesize.py`.

2. Kopieer deze code naar het einde van `app.py` en sla het volgende op:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>`index.html` bijwerken

Nu u een functie hebt om tekst naar spraak te converteren en een route in uw erlenmeyer-app om deze aan te roepen, is de volgende stap het schrijven van de HTML-code voor uw app. De onderstaande HTML bevat enkele dingen:

* Biedt een vervolg keuzelijst voor spraak selectie
* Hiermee wordt een knop toegevoegd voor het converteren van tekst naar spraak
* Hiermee wordt een audio-element toegevoegd, dat wordt gebruikt voor het afspelen van de gesynthesizerde spraak

1. Open `index.html` en zoek de volgende code opmerkingen:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. De code opmerkingen vervangen door dit HTML-blok:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, Jessa24kRUS)">English (US) | Female | Jessa24kRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Zoek vervolgens naar de volgende code opmerkingen:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. De code opmerkingen vervangen door dit HTML-blok:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Zorg ervoor dat u uw werk opslaat.

### <a name="update-mainjs"></a>`main.js` bijwerken

In de onderstaande code wordt inhoud van de HTML gebruikt om een aanvraag voor uw maat kolf te maken. Met name de vertaling en het spraak lettertype worden toegewezen aan variabelen en vervolgens door gegeven aan de aanvraag voor de `text-to-speech` route.

De code herhaalt vervolgens de reactie en werkt de HTML bij met de sentiment-scores.

1. Maak vanuit uw IDE een bestand met de naam `main.js` in de map `static`.
2. Kopieer deze code naar `static/scripts/main.js`:
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. U bent bijna klaar. Het laatste wat u gaat doen, is een code toevoegen aan `main.js` om automatisch een gesp roken letter type te selecteren op basis van de taal die voor de vertaling is geselecteerd. Voeg dit code blok toe aan `main.js`:
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Uw app testen

Laten we de spraak-synthese testen in de app.

```
flask run
```

Navigeer naar het gegeven server adres. Typ tekst in het invoer gebied, selecteer een taal en klik op vertalen. U moet een vertaling ontvangen. Selecteer vervolgens een stem en klik vervolgens op de knop tekst-naar-spraak omzetten. de vertaling moet worden afgespeeld als een gesynthesizerde spraak. Als het niet werkt, controleert u of u uw abonnements sleutel hebt toegevoegd.

> [!TIP]
> Als de wijzigingen die u hebt aangebracht, niet worden weer gegeven of als de app niet op de verwachte manier werkt, probeert u de cache te wissen of een persoonlijk/incognito-venster te openen.

Dat is alles. u hebt een werkende app waarmee vertalingen worden uitgevoerd, sentiment en gesynthesizerde spraak worden geanalyseerd. Druk op **CTRL + c** om de app af te breken. Zorg ervoor dat u de andere [Azure-Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)bekijkt.

## <a name="get-the-source-code"></a>De broncode ophalen

De bron code voor dit project is beschikbaar op [github](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Volgende stappen

* [ Naslaginformatie Translator Text-API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Naslaginformatie over de Text Analytics-API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Naslaginformatie voor de Text to Speech-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
