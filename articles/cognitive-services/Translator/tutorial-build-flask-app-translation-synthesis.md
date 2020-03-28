---
title: 'Zelfstudie: Een Flask-app bouwen om tekst te vertalen, synthetiseren en analyseren - Translator Text API'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie bouwt u een web-app op basis van een fles om tekst te vertalen, sentiment te analyseren en vertaalde tekst te synthetiseren in spraak.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: b41b68725b6747cbada13a9acc321724b3f89d67
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118572"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Zelfstudie: Een Flask-app bouwen met Azure Cognitive Services

In deze zelfstudie bouwt u een Web-app Flask die Azure Cognitive Services gebruikt om tekst te vertalen, sentiment te analyseren en vertaalde tekst in spraak te synthetiseren. Onze focus ligt op de Python-code en Flask-routes die onze toepassing mogelijk maken, maar we helpen je met de HTML en Javascript die de app samentrekt. Als je tegen problemen aanloopt, laat het ons dan weten via onderstaande feedbackknop.

Hier is wat deze tutorial heeft betrekking op:

> [!div class="checklist"]
> * Azure-abonnementssleutels ophalen
> * Uw ontwikkelomgeving instellen en afhankelijkheden installeren
> * Een flask-app maken
> * De api voor vertalertekst gebruiken om tekst te vertalen
> * Text Analytics gebruiken om het positieve/negatieve sentiment van invoertekst en vertalingen te analyseren
> * Spraakservices gebruiken om vertaalde tekst om te zetten in gesynthetiseerde spraak
> * Uw Flask-app lokaal uitvoeren

> [!TIP]
> Als u vooruit wilt gaan en alle code in één keer wilt bekijken, is het hele voorbeeld, samen met bouwinstructies beschikbaar op [GitHub.](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)

## <a name="what-is-flask"></a>Wat is Flask?

Flask is een microframework voor het maken van webapplicaties. Dit betekent dat Flask u tools, bibliotheken en technologieën biedt waarmee u een webtoepassing bouwen. Deze webapplicatie kan een aantal webpagina's, een blog, een wiki of gaan zo inhoudelijk als een web-based kalender applicatie of een commerciële website.

Voor degenen onder u die willen diep duiken na deze tutorial hier zijn een paar nuttige links:

* [Kolfdocumentatie](http://flask.pocoo.org/)
* [Kolf voor dummies - een beginnersgids voor kolf](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Vereisten

Laten we de software- en abonnementssleutels bekijken die u nodig hebt voor deze zelfstudie.

* [Python 3.5.2 of hoger](https://www.python.org/downloads/)
* [Git-gereedschappen](https://git-scm.com/downloads)
* Een IDE- of teksteditor, zoals [Visual Studio Code](https://code.visualstudio.com/) of [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) of [Firefox](https://www.mozilla.org/firefox)
* Een abonnementssleutel **voor vertalertekst** (houd er rekening mee dat u geen regio hoeft te selecteren.)
* Een **Text Analytics-abonnementssleutel** in de regio **West-VS.**
* Een **abonnementssleutel voor Spraakservices** in de **regio West-VS.**

## <a name="create-an-account-and-subscribe-to-resources"></a>Een account aanmaken en u abonneren op bronnen

Zoals eerder vermeld, heb je drie abonnementssleutels nodig voor deze zelfstudie. Dit betekent dat u een bron moet maken binnen uw Azure-account voor:
* Translator Text
* Tekstanalyse
* Spraakservices

Gebruik [Een Cognitive Services-account maken in de Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) voor stapsgewijze instructies om resources te maken.

> [!IMPORTANT]
> Maak voor deze zelfstudie uw bronnen in de regio West-VS. Als u een ander gebied gebruikt, moet u de basis-URL in elk van uw Python-bestanden aanpassen.

## <a name="set-up-your-dev-environment"></a>Uw ontwikkelaarsomgeving instellen

Voordat u uw Flask-web-app bouwt, moet u een werkmap voor uw project maken en een paar Python-pakketten installeren.

### <a name="create-a-working-directory"></a>Een werkmap maken

1. Open opdrachtregel (Windows) of terminal (macOS/Linux). Maak vervolgens een werkmap en submappen voor uw project:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Wijzigen in de werkmap van uw project:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Uw virtuele omgeving maken en activeren met`virtualenv`

Laten we een virtuele omgeving maken `virtualenv`voor onze Flask-app met behulp van . Het gebruik van een virtuele omgeving zorgt ervoor dat u een schone omgeving hebt om vanuit te werken.

1. Voer deze opdracht uit in uw werkmap om een virtuele omgeving te maken: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   We hebben expliciet aangegeven dat de virtuele omgeving Python 3 moet gebruiken. Dit zorgt ervoor dat gebruikers met meerdere Python-installaties de juiste versie gebruiken.

   **Windows CMD / Windows Bash:**
   ```
   virtualenv venv
   ```
   Om het simpel te houden, noemen we uw virtuele omgeving venv.

2. De opdrachten om uw virtuele omgeving te activeren, zijn afhankelijk van uw platform/shell:   

   | Platform | Shell | Opdracht |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Opdrachtregel | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Na het uitvoeren van deze opdracht moet uw `venv`opdrachtregel of terminalsessie worden voorafgegaan met .

3. U de sessie op elk gewenst moment deactiveren `deactivate`door deze in de opdrachtregel of terminal te typen:.

> [!NOTE]
> Python heeft uitgebreide documentatie voor het maken en beheren van virtuele [omgevingen, zie virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Installatieverzoeken

Aanvragen is een populaire module die wordt gebruikt om HTTP 1.1-aanvragen te verzenden. Het is niet nodig om handmatig querytekenreeksen toe te voegen aan uw URL's of om uw POST-gegevens te coderen.

1. Voer het als nodig is om aanvragen te installeren:

   ```
   pip install requests
   ```

> [!NOTE]
> Zie [Verzoeken: HTTP voor mensen als](https://2.python-requests.org/en/master/)u meer wilt weten over aanvragen.

### <a name="install-and-configure-flask"></a>Flask installeren en configureren

Vervolgens moeten we Flask installeren. Flask verzorgt de routering voor onze web-app en stelt ons in staat om server-to-server-gesprekken te voeren die onze abonnementssleutels verbergen voor de eindgebruiker.

1. Voer het als nodig om Flask te installeren:
   ```
   pip install Flask
   ```
   Laten we ervoor zorgen dat Flask is geïnstalleerd. Uitvoeren:
   ```
   flask --version
   ```
   De versie moet worden afgedrukt op terminal. Al het andere betekent dat er iets mis is gegaan.

2. Als u de Flask-app wilt uitvoeren, u de opdracht kolf of python's -m-schakelaar met Flask gebruiken. Voordat u dat doen, moet u uw terminal vertellen `FLASK_APP` met welke app u moet werken door de omgevingsvariabele te exporteren:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Uw Flask-app maken

In deze sectie maakt u een barebones Flask-app die een HTML-bestand retourneert wanneer gebruikers de hoofdmap van uw app raken. Besteed niet te veel tijd aan het uit elkaar halen van de code, we komen later terug om dit bestand bij te werken.

### <a name="what-is-a-flask-route"></a>Wat is een Flask route?

Laten we even de tijd nemen om over[" routes](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)" te praten. Routering wordt gebruikt om een URL aan een specifieke functie te binden. Flask gebruikt routedecorateurs om functies te registreren voor specifieke URL's. Wanneer een gebruiker bijvoorbeeld naar de`/`hoofdmap () `index.html` van onze web-app navigeert, wordt deze weergegeven.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Laten we nog eens een voorbeeld nemen om dit huis te hameren.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Deze code zorgt ervoor dat `http://your-web-app.com/about` wanneer `about.html` een gebruiker naar dat navigeert het bestand wordt gerenderd.

Hoewel deze voorbeelden illustreren hoe html-pagina's voor een gebruiker kunnen worden weergegeven, kunnen routes ook worden gebruikt om API's te bellen wanneer een knop wordt ingedrukt of een willekeurig aantal acties te ondernemen zonder dat u van de startpagina hoeft weg te navigeren. U ziet dit in actie wanneer u routes maakt voor vertaling, sentiment en spraaksynthese.

### <a name="get-started"></a>Aan de slag

1. Open het project in uw IDE `app.py` en maak vervolgens een bestand met de naam in de hoofdmap van uw werkmap. Kopieer vervolgens deze `app.py` code in en sla op:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Met dit codeblok wordt `index.html` de app weergegeven wanneer een gebruiker naar`/`de hoofdmap van uw web-app navigeert ( ).

2. Laten we vervolgens de front-end voor onze web-app maken. Maak een `index.html` bestand `templates` met de naam in de map. Kopieer deze code `templates/index.html`vervolgens naar .

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

3. Laten we de Flask-app testen. Vanaf de terminal, lopen:

   ```
   flask run
   ```

4. Open een browser en navigeer naar de opgegeven URL. U ziet uw app met één pagina. Druk op **Ctrl + c** om de app te doden.

## <a name="translate-text"></a>Tekst vertalen

Nu u een idee hebt van hoe een eenvoudige Flask-app werkt, laten we:

* Schrijf een python om de Translator Text API aan te roepen en een antwoord terug te sturen
* Een flaskroute maken om uw Python-code aan te roepen
* De HTML bijwerken met een gebied voor tekstinvoer en -vertaling, een taalkiezer en knop Vertalen
* Javascript schrijven waarmee gebruikers vanuit de HTML kunnen communiceren met uw Flask-app

### <a name="call-the-translator-text-api"></a>De API voor vertalertekst aanroepen

Het eerste wat u hoeft te doen is het schrijven van een functie om de Translator Text API aan te roepen. Deze functie neemt twee `text_input` `language_output`argumenten: en . Deze functie wordt aangeroepen wanneer een gebruiker op de vertaalknop in uw app drukt. Het tekstgebied in de HTML `text_input`wordt verzonden als de waarde `language_output`van de taalselectie in de HTML als .

1. Laten we beginnen met het `translate.py` maken van een bestand dat in de hoofdmap van uw werkmap wordt genoemd.
2. Voeg vervolgens deze `translate.py`code toe aan . Deze functie heeft `text_input` twee `language_output`argumenten: en .
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
3. Voeg uw abonnementssleutel voor vertalerstekst toe en sla deze op.

### <a name="add-a-route-to-apppy"></a>Een route toevoegen aan`app.py`

Vervolgens moet u een route maken in uw `translate.py`Flask-app die aanroept. Deze route wordt elke keer aangeroepen wanneer een gebruiker op de vertaalknop in uw app drukt.

Voor deze app accepteert je `POST` route aanvragen. Dit komt omdat de functie verwacht dat de tekst te vertalen en een uitvoertaal voor de vertaling.

Flask biedt helperfuncties om u te helpen bij het ontsteken en beheren van elk verzoek. `get_json()` Retourneert de gegevens van `POST` de aanvraag als JSON in de opgegeven code. Vervolgens `data['text']` worden `data['to']`de tekst- en uitvoertaalwaarden gebruikt en doorgegeven aan `get_translation()` de functie die beschikbaar is vanaf `translate.py`. De laatste stap is om het antwoord als JSON terug te sturen, omdat u deze gegevens in uw web-app moet weergeven.

In de volgende secties herhaalt u dit proces terwijl u routes maakt voor sentimentanalyse en spraaksynthese.

1. Open `app.py` en zoek de importinstructie `app.py` bovenaan en voeg de volgende regel toe:

   ```python
   import translate
   ```
   Nu kan onze Flask app `translate.py`gebruik maken van de methode die beschikbaar is via .

2. Kopieer deze code naar `app.py` het einde van en sla op:

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

Nu u een functie hebt om tekst te vertalen en een route in uw Flask-app om deze te noemen, is de volgende stap om te beginnen met het bouwen van de HTML voor uw app. De HTML hieronder doet een paar dingen:

* Biedt een tekstgebied waar gebruikers tekst kunnen invoeren om te vertalen.
* Bevat een taalkiezer.
* Bevat HTML-elementen om de gedetecteerde taal- en betrouwbaarheidsscores tijdens de vertaling weer te geven.
* Biedt een alleen-lezen tekstgebied waar de vertaaluitvoer wordt weergegeven.
* Bevat tijdelijke aanduidingen voor sentimentanalyse en spraaksynthesecode die u later in de zelfstudie aan dit bestand toevoegt.

Laten we `index.html`updaten.

1. Open `index.html` en vind deze codeopmerkingen:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Vervang de codeopmerkingen door dit HTML-blok:
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

De volgende stap is het schrijven van een aantal Javascript. Dit is de brug tussen uw HTML- en Flaskroute.

### <a name="create-mainjs"></a>Maken`main.js`  

Het `main.js` bestand is de brug tussen uw HTML- en Flaskroute. Uw app gebruikt een combinatie van jQuery, Ajax en XMLHttpRequest om inhoud weer te geven en `POST` aanvragen in te dienen voor uw Flask-routes.

In de onderstaande code wordt inhoud uit de HTML gebruikt om een aanvraag voor uw flaskroute te construeren. In het bijzonder worden de inhoud van het tekstgebied en de taalkiezer toegewezen `translate-text`aan variabelen en vervolgens doorgegeven in het verzoek aan .

De code doorloopt vervolgens het antwoord en werkt de HTML bij met de vertaling, gedetecteerde taal en betrouwbaarheidsscore.

1. Maak vanuit uw IDE `main.js` een `static/scripts` bestand met de naam in de map.
2. Kopieer deze `static/scripts/main.js`code in:
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

Navigeer naar het opgegeven serveradres. Typ tekst in het invoergebied, selecteer een taal en druk op Vertalen. Je zou een vertaling moeten krijgen. Als dit niet werkt, moet u ervoor zorgen dat u uw abonnementssleutel hebt toegevoegd.

> [!TIP]
> Als de wijzigingen die u hebt aangebracht niet worden weergegeven of als de app niet werkt zoals u dat verwacht, probeert u uw cache te wissen of een privé-/incognitovenster te openen.

Druk op **Ctrl + c** om de app te doden en ga vervolgens naar de volgende sectie.

## <a name="analyze-sentiment"></a>Stemming analyseren

De [Text Analytics API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) kan worden gebruikt om sentimentanalyse uit te voeren, belangrijke zinnen uit tekst te extraheren of de brontaal te detecteren. In deze app gaan we sentimentanalyse gebruiken om te bepalen of de meegeleverde tekst positief, neutraal of negatief is. De API retourneert een numerieke score tussen 0 en 1. Scores dicht bij 1 geven een positief sentiment aan en scores dicht bij 0 geven een negatief sentiment aan.

In deze sectie ga je een paar dingen doen:

* Schrijf een python om de Text Analytics API aan te roepen om sentimentanalyse uit te voeren en een reactie te retourneren
* Een flaskroute maken om uw Python-code aan te roepen
* De HTML bijwerken met een gebied voor sentimentscores en een knop om analyses uit te voeren
* Javascript schrijven waarmee gebruikers vanuit de HTML kunnen communiceren met uw Flask-app

### <a name="call-the-text-analytics-api"></a>De Tekstanalyse-API aanroepen

Laten we een functie schrijven om de Text Analytics API aan te roepen. Deze functie heeft vier `input_text` `input_language`argumenten `output_text`nodig: , , en `output_language`. Deze functie wordt aangeroepen wanneer een gebruiker op de knop gevoelsanalyse uitvoeren in uw app drukt. Bij elke aanvraag worden gegevens verstrekt die door de gebruiker zijn verstrekt uit het tekstgebied en de taalkiezer, evenals de gedetecteerde taal- en vertaaluitvoer. Het antwoordobject bevat sentimentscores voor de bron en vertaling. In de volgende secties gaat u een aantal Javascript schrijven om het antwoord te ontken en te gebruiken in uw app. Voor nu, laten we ons richten op de tekst Analytics API bellen.

1. Laten we een bestand `sentiment.py` maken dat in de hoofdmap van uw werkmap wordt aangeroepen.
2. Voeg vervolgens deze `sentiment.py`code toe aan .
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
3. Voeg uw Text Analytics-abonnementssleutel toe en sla deze op.

### <a name="add-a-route-to-apppy"></a>Een route toevoegen aan`app.py`

Laten we een route maken in `sentiment.py`uw Flask-app die aanroept. Deze route wordt aangeroepen telkens wanneer een gebruiker op de knop gevoelsanalyse uitvoeren in uw app drukt. Net als de route voor vertaling, `POST` deze route gaat om aanvragen te accepteren, omdat de functie verwacht argumenten.

1. Open `app.py` en zoek de importinstructie `app.py` bovenaan en werk deze bij:

   ```python
   import translate, sentiment
   ```
   Nu kan onze Flask app `sentiment.py`gebruik maken van de methode die beschikbaar is via .

2. Kopieer deze code naar `app.py` het einde van en sla op:
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

Nu u een functie hebt om sentimentanalyse uit te voeren en een route in uw Flask-app om deze te bellen, is de volgende stap om de HTML voor uw app te schrijven. De HTML hieronder doet een paar dingen:

* Voegt een knop toe aan uw app om sentimentanalyse uit te voeren
* Voegt een element toe dat sentimentscore verklaart
* Voegt een element toe om de sentimentscores weer te geven

1. Open `index.html` en vind deze codeopmerkingen:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Vervang de codeopmerkingen door dit HTML-blok:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>`main.js` bijwerken

In de onderstaande code wordt inhoud uit de HTML gebruikt om een aanvraag voor uw flaskroute te construeren. In het bijzonder worden de inhoud van het tekstgebied en de taalkiezer toegewezen `sentiment-analysis` aan variabelen en vervolgens doorgegeven in het verzoek aan de route.

De code doorloopt vervolgens de reactie en werkt de HTML bij met de sentimentscores.

1. Maak vanuit uw IDE `main.js` een `static` bestand met de naam in de map.

2. Kopieer deze `static/scripts/main.js`code in:
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

### <a name="test-sentiment-analysis"></a>Test sentimentanalyse

Laten we de sentimentanalyse testen in de app.

```
flask run
```

Navigeer naar het opgegeven serveradres. Typ tekst in het invoergebied, selecteer een taal en druk op Vertalen. Je zou een vertaling moeten krijgen. Druk vervolgens op de knop Gevoelanalyse uitvoeren. Je zou twee scores moeten zien. Als dit niet werkt, moet u ervoor zorgen dat u uw abonnementssleutel hebt toegevoegd.

> [!TIP]
> Als de wijzigingen die u hebt aangebracht niet worden weergegeven of als de app niet werkt zoals u dat verwacht, probeert u uw cache te wissen of een privé-/incognitovenster te openen.

Druk op **Ctrl + c** om de app te doden en ga vervolgens naar de volgende sectie.

## <a name="convert-text-to-speech"></a>Tekst naar spraak converteren

Met [de Text-to-speech API](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) kan uw app tekst omzetten in natuurlijke door de mens gesynthetiseerde spraak. De service ondersteunt standaard-, neurale en aangepaste stemmen. Onze voorbeeld-app maakt gebruik van een handvol van de beschikbare stemmen, voor een volledige lijst, zie [ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech).

In deze sectie ga je een paar dingen doen:

* Schrijf een python om tekst-naar-spraak te converteren met de API tekst naar spraak
* Een flaskroute maken om uw Python-code aan te roepen
* De HTML bijwerken met een knop om tekst-naar-spraak te converteren en een element voor het afspelen van audio
* Javascript schrijven waarmee gebruikers kunnen communiceren met uw Flask-app

### <a name="call-the-text-to-speech-api"></a>De API voor tekst naar spraak aanroepen

Laten we een functie schrijven om tekst-naar-spraak om te zetten. Deze functie neemt twee `input_text` `voice_font`argumenten: en . Deze functie wordt aangeroepen wanneer een gebruiker op de knop tekst-naar-spraak converteren in uw app drukt. `input_text`is de vertaaluitvoer die door de `voice_font` aanroep wordt geretourneerd om tekst te vertalen, is de waarde van de kiezer voor spraaklettertypen in de HTML.

1. Laten we een bestand `synthesize.py` maken dat in de hoofdmap van uw werkmap wordt aangeroepen.

2. Voeg vervolgens deze `synthesize.py`code toe aan .
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
3. Voeg uw abonnementssleutel van Speech Services toe en sla deze op.

### <a name="add-a-route-to-apppy"></a>Een route toevoegen aan`app.py`

Laten we een route maken in `synthesize.py`uw Flask-app die aanroept. Deze route wordt aangeroepen telkens wanneer een gebruiker op de knop tekst-naar-spraak converteren in uw app drukt. Net als de routes voor vertaling en sentimentanalyse, zal deze route aanvragen accepteren, `POST` omdat de functie twee argumenten verwacht: de tekst die moet worden gesynthetiseerd en het spraaklettertype voor afspelen.

1. Open `app.py` en zoek de importinstructie `app.py` bovenaan en werk deze bij:

   ```python
   import translate, sentiment, synthesize
   ```
   Nu kan onze Flask app `synthesize.py`gebruik maken van de methode die beschikbaar is via .

2. Kopieer deze code naar `app.py` het einde van en sla op:

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

Nu u een functie hebt om tekst-naar-spraak om te zetten en een route in uw Flask-app om deze te bellen, is de volgende stap om de HTML voor uw app te schrijven. De HTML hieronder doet een paar dingen:

* Biedt een vervolgkeuzelijst voor spraakselectie
* Voegt een knop toe om tekst naar spraak om te zetten
* Voegt een audio-element toe, dat wordt gebruikt om de gesynthetiseerde spraak af te spelen

1. Open `index.html` en vind deze codeopmerkingen:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Vervang de codeopmerkingen door dit HTML-blok:
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

3. Zoek vervolgens de volgende codeopmerkingen:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Vervang de codeopmerkingen door dit HTML-blok:

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

In de onderstaande code wordt inhoud uit de HTML gebruikt om een aanvraag voor uw flaskroute te construeren. In het bijzonder worden de vertaling en het spraaklettertype toegewezen aan `text-to-speech` variabelen en vervolgens doorgegeven in het verzoek aan de route.

De code doorloopt vervolgens de reactie en werkt de HTML bij met de sentimentscores.

1. Maak vanuit uw IDE `main.js` een `static` bestand met de naam in de map.
2. Kopieer deze `static/scripts/main.js`code in:
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
3. U bent bijna klaar. Het laatste wat u gaat doen, is `main.js` code toevoegen om automatisch een spraaklettertype te selecteren op basis van de taal die is geselecteerd voor vertaling. Voeg dit codeblok toe aan: `main.js`
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

Laten we de spraaksynthese testen in de app.

```
flask run
```

Navigeer naar het opgegeven serveradres. Typ tekst in het invoergebied, selecteer een taal en druk op Vertalen. Je zou een vertaling moeten krijgen. Selecteer vervolgens een stem en druk vervolgens op de knop tekst-naar-spraak converteren. de vertaling moet worden afgespeeld als gesynthetiseerde spraak. Als dit niet werkt, moet u ervoor zorgen dat u uw abonnementssleutel hebt toegevoegd.

> [!TIP]
> Als de wijzigingen die u hebt aangebracht niet worden weergegeven of als de app niet werkt zoals u dat verwacht, probeert u uw cache te wissen of een privé-/incognitovenster te openen.

Dat is het, je hebt een werkende app die vertalingen uitvoert, sentiment analyseert en spraak synthetiseerde. Druk op **Ctrl + c** om de app te doden. Zorg ervoor dat u de andere [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)bekijkt.

## <a name="get-the-source-code"></a>De broncode ophalen

De broncode van dit project is beschikbaar op [GitHub.](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)

## <a name="next-steps"></a>Volgende stappen

* [ Naslaginformatie Translator Text-API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Naslaginformatie over de Text Analytics-API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [API-verwijzing naar tekst naar spraak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
