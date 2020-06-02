---
title: Snelstartgids voor insluitende Reader node. js-client bibliotheek
titleSuffix: Azure Cognitive Services
description: In deze Quick Start bouwt u een volledig nieuwe web-app en voegt u de functionaliteit van de insluitende Reader API toe.
services: cognitive-services
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/20/2020
ms.author: pasta
ms.openlocfilehash: 2092ccbedd95ee13ac4ad929f052afd1d85729ec
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268719"
---
De [insluitende lezer](https://www.onenote.com/learningtools) is een inclusief ontworpen hulp programma waarmee bewezen technieken worden geïmplementeerd om de Lees vaardigheid te verbeteren.

In deze Snelstartgids bouwt u een volledig nieuwe web-app en integreert u de insluitende lezer met behulp van de insluitende lezer-client bibliotheek. [Hier](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs)vindt u een volledig werkend voor beeld van deze Quick Start.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een resource voor insluitende lezer die is geconfigureerd voor Azure Active Directory authenticatie. Volg [deze instructies om de](../../how-to-create-immersive-reader.md) instellingen op te halen. U hebt enkele van de waarden nodig die u hier hebt gemaakt bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekst bestand voor toekomstig naslag doeleinden.
* [Node. js](https://nodejs.org/) en [garens](https://yarnpkg.com)
* Een IDE zoals [Visual Studio code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Een node. js-web-app maken met Express

Maak een node. js-web-app met het `express-generator` hulp programma.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Installeer garen afhankelijkheden en voeg afhankelijkheden toe `request` en `dotenv` , die later worden gebruikt in de Quick Start.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Verificatie instellen

### <a name="configure-authentication-values"></a>Verificatie waarden configureren

Maak een nieuw bestand met de naam _. env_ in de hoofdmap van uw project. Plak de volgende code in het bestand en geef de waarden op die zijn opgegeven bij het maken van uw insluitende lezer-resource.
Neem geen aanhalings tekens of de {en} op.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Zorg ervoor dat dit bestand niet wordt door gegeven aan broncode beheer, omdat het geheimen bevat dat niet openbaar mag worden gemaakt.

Open vervolgens _app. js_ en voeg het volgende toe boven aan het bestand. Hiermee worden de eigenschappen die in het. env-bestand zijn gedefinieerd, geladen als omgevings variabelen in een knoop punt.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>De router bijwerken om het token te verkrijgen
Open het _routes\index.js_ -bestand en vervang de automatisch gegenereerde code door de volgende code.

Met deze code wordt een API-eind punt gemaakt waarmee een Azure AD-verificatie token wordt verkregen met behulp van het wacht woord voor de Service-Principal. Ook wordt het subdomein opgehaald. Vervolgens wordt een object geretourneerd dat het token en subdomein bevat.

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

Het **GetTokenAndSubdomain** -API-eind punt moet worden beveiligd achter een vorm van verificatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voor komen dat onbevoegde gebruikers tokens verkrijgen om te gebruiken voor uw insluitende lezer-service en facturering; Dit werk valt buiten het bereik van deze Quick Start.

## <a name="add-sample-content"></a>Voorbeeld inhoud toevoegen

Nu gaan we voorbeeld inhoud toevoegen aan deze web-app. Open _views\index.Pug_ en vervang de automatisch gegenereerde code door dit voor beeld:

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


U ziet dat alle tekst een kenmerk **lang** heeft, waarin de talen van de tekst worden beschreven. Dit kenmerk helpt de insluitende lezer de relevante taal-en grammatica functies te bieden.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Onze web-app is nu klaar. Start de app door uit te voeren:

```bash
npm start
```

Open uw browser en ga naar _http://localhost:3000_ . U ziet nu het volgende:

![Voorbeeldapp](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>De insluitende lezer starten

Wanneer u op de knop ' insluitende lezer ' klikt, ziet u dat de insluitende lezer wordt gestart met de inhoud op de pagina.

![Immersive Reader](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Volgende stappen

* Verken de [insluitende lezer SDK](https://github.com/microsoft/immersive-reader-sdk) en de referentie voor de [insluitende lezer SDK](../../reference.md)