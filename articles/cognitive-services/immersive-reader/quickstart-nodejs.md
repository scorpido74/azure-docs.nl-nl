---
title: 'Snelstart: maak een web-app die de meeslepende lezer met Node.js lanceert'
titleSuffix: Azure Cognitive Services
description: In deze quickstart bouwt u vanaf nul een web-app en voegt u de Immersive Reader API-functionaliteit toe.
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: 749e75fed409632c613713a49154e4cd8dc265b3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75946325"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-nodejs"></a>Snelstart: maak een web-app die de Immersive Reader (Node.js) lanceert

De [Immersive Reader](https://www.onenote.com/learningtools) is een inclusief ontworpen tool die beproefde technieken implementeert om het begrijpen van lezen te verbeteren.

In deze quickstart bouw je een web-app vanaf nul en integreer je de Immersive Reader met behulp van de Immersive Reader SDK. Een volledig werkend voorbeeld van deze quickstart is [hier](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs)beschikbaar.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Immersive Reader-bron die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](./how-to-create-immersive-reader.md) om je op te stellen. U hebt een aantal waarden nodig die hier zijn gemaakt bij het configureren van de omgevingseigenschappen. Sla de uitvoer van uw sessie op in een tekstbestand voor toekomstige verwijzingen.
* [Node.js](https://nodejs.org/) en [garen](https://yarnpkg.com)
* Een IDE zoals [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Een Node.js-web-app maken met Express

Maak een Web-app Node.js met de `express-generator` tool.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Installeer garen afhankelijkheden, en `request` `dotenv`voeg afhankelijkheden en , die later zal worden gebruikt in de quickstart.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Verificatie instellen

### <a name="configure-authentication-values"></a>Verificatiewaarden configureren

Maak een nieuw bestand genaamd _.env_ in de hoofdmap van uw project. Plak de volgende code erin en geef de waarden die zijn opgegeven toen u uw Immersive Reader-bron maakte.
Voeg geen aanhalingstekens of de tekens {" en "}" toe.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Zorg ervoor dat u dit bestand niet in bronbeheer verbindt, omdat het geheimen bevat die niet openbaar mogen worden gemaakt.

Open vervolgens _app.js_ en voeg het volgende toe aan de bovenkant van het bestand. Hiermee worden de eigenschappen die in het .env-bestand zijn gedefinieerd, als omgevingsvariabelen in Knooppunt geladen.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>De router bijwerken om het token te verkrijgen
Open het bestand _routes\index.js_ en vervang de automatisch gegenereerde code door de volgende code.

Met deze code wordt een API-eindpunt gemaakt dat een Azure AD-verificatietoken verwerft met behulp van het hoofdwachtwoord van de service. Het haalt ook het subdomein op. Vervolgens wordt een object geretourneerd dat het token en subdomein bevat.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            },
            url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: process.env.CLIENT_ID,
                client_secret: process.env.CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

Het **GetTokenAndSubdomain** API-eindpunt moet worden beveiligd achter een of andere vorm van authenticatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voorkomen dat onbevoegde gebruikers tokens verkrijgen om te gebruiken tegen uw Immersive Reader-service en facturering; dat werk buiten het bereik van deze quickstart valt.

## <a name="add-sample-content"></a>Voorbeeldinhoud toevoegen

Nu voegen we voorbeeldinhoud toe aan deze web-app. Open _weergaven\index.pug_ en vervang de automatisch gegenereerde code door dit voorbeeld:

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```


Merk op dat alle tekst een **lang** attribuut heeft, dat de talen van de tekst beschrijft. Met dit kenmerk biedt de immersive reader relevante taal- en grammaticafuncties.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Onze web-app is nu klaar. Start de app door het uitvoeren van:

```bash
npm start
```

Open uw browser _http://localhost:3000_en navigeer naar . U ziet nu het volgende:

![Voorbeeldapp](./media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Start de meeslepende lezer

Wanneer u op de knop 'Meeslepende lezer' klikt, ziet u de meeslepende lezer die is gestart met de inhoud op de pagina.

![Insluitende lezer](./media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Volgende stappen

* Ontdek de [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) en de [Immersive Reader SDK Reference](./reference.md)