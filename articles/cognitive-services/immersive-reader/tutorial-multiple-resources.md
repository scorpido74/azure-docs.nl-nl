---
title: 'Zelfstudie: Meerdere bronnen voor meeslepende lezers integreren'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maakt u een Node.js-toepassing die de immersive reader start met meerdere bronnen voor meeslepende lezers.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: 3912d55b13f3977818e8d898efa651ffeb1a798a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046273"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>Zelfstudie: Meerdere bronnen voor meeslepende lezers integreren

In het [overzicht](./overview.md)leerde je over wat de Immersive Reader is en hoe het bewezen technieken implementeert om het begrijpend lezen te verbeteren voor taalstudenten, opkomende lezers en studenten met leerverschillen. In de [Quickstart van Node.js](./quickstart-nodejs.md)heb je geleerd hoe je Immersive Reader met één resource gebruiken. Deze zelfstudie gaat over het integreren van meerdere bronnen voor meeslepende lezers in dezelfde toepassing. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Meerdere immersive reader-bronnen maken onder een bestaande resourcegroep
> * De immersive reader starten met meerdere bronnen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Volg de [quickstart](./quickstart-nodejs.md) om een web-app te maken die de Immersive Reader met NodeJS lanceert. In die quickstart configureert u één bron voor meeslepende lezers. We zullen bouwen op de top van dat in deze tutorial.

## <a name="create-the-immersive-reader-resources"></a>De bronnen voor meeslepende lezers maken

Volg [deze instructies](./how-to-create-immersive-reader.md) om elke immersive reader-bron te maken. Het script **Create-ImmersiveReaderResource** heeft `ResourceName`, `ResourceSubdomain`en `ResourceLocation` als parameters. Deze moeten uniek zijn voor elke resource die wordt gemaakt. De overige parameters moeten hetzelfde zijn als wat u hebt gebruikt bij het instellen van uw eerste meeslepende lezerbron. Op deze manier kan elke resource worden gekoppeld aan dezelfde Azure-brongroep en Azure AD-toepassing.

In het onderstaande voorbeeld ziet u hoe u twee resources maakt, een in WestUS en een in EastUS. Let op de `ResourceName` `ResourceSubdomain`unieke `ResourceLocation`waarden voor , en .

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Resources toevoegen aan omgevingsconfiguratie

In de quickstart hebt u een omgevingsconfiguratiebestand `ClientSecret`gemaakt `Subdomain` dat de `TenantId`, , `ClientId`en parameters bevat. Aangezien al uw resources dezelfde Azure AD-toepassing gebruiken, kunnen `TenantId` `ClientId`we `ClientSecret`dezelfde waarden gebruiken voor de , en . De enige wijziging die moet worden aangebracht, is om elk subdomein voor elke resource weer te geven.

Uw nieuwe __.env-bestand__ moet er nu als volgt uitzien:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Zorg ervoor dat u dit bestand niet in bronbeheer verbindt, omdat het geheimen bevat die niet openbaar mogen worden gemaakt.

Vervolgens wijzigen we het _bestand routes\index.js_ dat we hebben gemaakt om onze meerdere bronnen te ondersteunen. Vervang de inhoud ervan door de volgende code.

Net als voorheen maakt deze code een API-eindpunt dat een Azure AD-verificatietoken verwerft met behulp van uw serviceprincipal-wachtwoord. Deze keer kan de gebruiker een resourcelocatie opgeven en deze doorgeven als queryparameter. Vervolgens retourneert het object dat het token en het bijbehorende subdomein bevat.

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

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

Het **getimmersivereaderlaunchparams** API-eindpunt moet worden beveiligd achter een of andere vorm van authenticatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voorkomen dat onbevoegde gebruikers tokens verkrijgen om te gebruiken tegen uw Immersive Reader-service en facturering; dat werk valt buiten het bereik van deze tutorial.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Start de meeslepende lezer met voorbeeldinhoud

1. Open _views\index.pug_en vervang de inhoud ervan door de volgende code. Deze code vult de pagina met een aantal voorbeeldinhoud en voegt twee knoppen toe die de meeslepende lezer start. Een voor de lancering van Immersive Reader voor de EastUS bron, en een andere voor de WestUS bron.

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
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

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
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
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

3. Onze web-app is nu klaar. Start de app door het uitvoeren van:

    ```bash
    npm start
    ```

4. Open uw browser [http://localhost:3000](http://localhost:3000)en navigeer naar . U ziet de bovenstaande inhoud op de pagina. Klik op de **Knop EastUS Immersive Reader** of de **WestUS Immersive Reader-knop** om de Immersive Reader te starten met behulp van deze respectievelijke bronnen.

## <a name="next-steps"></a>Volgende stappen

* Ontdek de [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) en de [Immersive Reader SDK Reference](./reference.md)
* Codevoorbeelden weergeven op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)