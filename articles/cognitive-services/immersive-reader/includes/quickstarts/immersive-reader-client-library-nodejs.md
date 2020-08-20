---
title: Quickstart voor de Node.js-clientbibliotheek voor Insluitende lezer
titleSuffix: Azure Cognitive Services
description: In deze quickstart maakt u een nieuwe web-app en voegt u de API-functionaliteit voor de Insluitende lezer toe.
services: cognitive-services
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/20/2020
ms.author: pasta
ms.custom: devx-track-javascript
ms.openlocfilehash: 91adff9a8330defb6c2b9f1e915c46886649fed3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602344"
---
De [Insluitende lezer](https://www.onenote.com/learningtools) is een inclusief ontworpen hulpprogramma waarmee bewezen technieken worden geïmplementeerd om de leesvaardigheid te verbeteren.

In deze Quickstart maakt u een nieuwe web-app en integreert u de Insluitende lezer door de clientbibliotheek voor de insluitende lezer te gebruiken. U vindt [hier](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs) een volledig werkend voorbeeld van deze quickstart.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](../../how-to-create-immersive-reader.md) om deze in te stellen. U hebt enkele waarden nodig die u hier hebt gemaakt bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekstbestand voor later gebruik.
* [Node.js](https://nodejs.org/) en [Yarn](https://yarnpkg.com)
* Een code-editor, zoals [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Een Node.js-web-app maken met Express

Maak een Node.js-web-app met het hulpprogramma `express-generator`.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Installeer Yarn-afhankelijkheden en voeg de afhankelijkheden `request` en `dotenv` toe. Deze worden verderop in de quickstart gebruikt.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Verificatie instellen

### <a name="configure-authentication-values"></a>Verificatietypewaarden configureren

Maak een nieuw bestand met de naam _. env_ in de hoofdmap van uw project. Plak de volgende code in het bestand, waarbij u de waarden opgeeft die zijn verstrekt bij het maken van de resource voor de Insluitende lezer.
Laat aanhalingstekens of accolades weg.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Zorg ervoor dat u dit bestand niet doorvoert in broncodebeheer, omdat het geheimen bevat die niet openbaar moeten worden gemaakt.

Open vervolgens _app.js_ en voeg het volgende toe aan het begin van het bestand. Hiermee worden de eigenschappen die in het ENV-bestand zijn gedefinieerd, geladen als omgevingsvariabelen in Node.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>De router bijwerken om het token te verkrijgen
Open het bestand _routes\index.js_ en vervang de automatisch gegenereerde code door de volgende code.

Met deze code wordt er een API-eindpunt gemaakt waarmee een Azure AD-verificatietoken wordt verkregen met behulp van uw wachtwoord voor de service-principal. Ook wordt hiermee het subdomein opgehaald. Vervolgens wordt een-object met het token en het subdomein geretourneerd.

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

Het API-eindpunt **GetTokenAndSubdomain** moet worden beveiligd met een vorm van verificatie (bijvoorbeeld [OAuth-](https://oauth.net/2/)) om te voorkomen dat niet-geautoriseerde gebruikers tokens verkrijgen die kunnen worden gebruikt voor uw Insluitende lezer-service en -facturering. Dit valt buiten het bereik van deze quickstart.

## <a name="add-sample-content"></a>Voorbeeldinhoud toevoegen

Nu gaan we voorbeeldinhoud toevoegen aan deze web-app. Open _views\index.pug_ en vervang de automatisch gegenereerde code door dit voorbeeld:

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


U ziet dat alle tekst een **taal**-kenmerk bevat waarmee de talen van de tekst worden beschreven. Dit kenmerk helpt de Insluitende lezer de relevante taal-en grammaticafuncties te bieden.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Onze web-app is nu klaar. Start de app met de volgende opdracht:

```bash
npm start
```

Open uw browser en ga naar _http://localhost:3000_ . U ziet nu het volgende:

![Voorbeeld-app](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>De Insluitende lezer starten

Wanneer u op de knop ‘Immersive Reader’ klikt, ziet u dat de Insluitende lezer wordt gestart met de inhoud van de pagina.

![Insluitende lezer](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Volgende stappen

* De [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](../../reference.md) verkennen