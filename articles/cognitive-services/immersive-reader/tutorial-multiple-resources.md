---
title: 'Zelf studie: meerdere bronnen voor insluitende lezer integreren'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie maakt u een node. js-toepassing waarmee de insluitende lezer wordt gestart met meerdere bronnen voor insluitende lezer.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: 3912d55b13f3977818e8d898efa651ffeb1a798a
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046273"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>Zelf studie: meerdere bronnen voor insluitende lezer integreren

In het [overzicht](./overview.md)hebt u geleerd wat de insluitende lezer is en hoe deze de bewezen technieken implementeert om de Lees vaardigheid te verbeteren voor taal kennis, opkomende lezers en studenten met meer informatie. In de [Snelstartgids voor node. js](./quickstart-nodejs.md)hebt u geleerd hoe u insluitende lezer met één resource kunt gebruiken. In deze zelf studie wordt uitgelegd hoe u meerdere insluitende lezers bronnen in dezelfde toepassing kunt integreren. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Meerdere resources van de insluitende lezer maken onder een bestaande resource groep
> * De insluitende lezer starten met meerdere resources

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Volg de [Quick](./quickstart-nodejs.md) start om een web-app te maken waarmee de insluitende lezer met NodeJS wordt gestart. In die Snelstartgids configureert u één insluitende lezer-resource. In deze zelf studie gaan we hier op bouwen.

## <a name="create-the-immersive-reader-resources"></a>De resources voor insluitende lezer maken

Volg [deze instructies](./how-to-create-immersive-reader.md) voor het maken van elke insluitende lezer-resource. Het script **Create-ImmersiveReaderResource** heeft `ResourceName`, `ResourceSubdomain`en `ResourceLocation` als para meters. Deze moeten uniek zijn voor elke resource die wordt gemaakt. De overige para meters moeten hetzelfde zijn als de waarde die u hebt gebruikt bij het instellen van uw eerste insluitende lezer-resource. Op deze manier kan elke resource worden gekoppeld aan dezelfde Azure-resource groep en Azure AD-toepassing.

In het onderstaande voor beeld ziet u hoe u twee resources maakt, een in Westus en een andere in Timor-Leste. Let op de unieke waarden voor `ResourceName`, `ResourceSubdomain`en `ResourceLocation`.

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

## <a name="add-resources-to-environment-configuration"></a>Resources toevoegen aan de omgevings configuratie

In de Quick Start hebt u een omgevings configuratie bestand gemaakt dat de para meters `TenantId`, `ClientId`, `ClientSecret`en `Subdomain` bevat. Omdat al uw resources dezelfde Azure AD-toepassing gebruiken, kunnen we dezelfde waarden gebruiken voor de `TenantId`, `ClientId`en `ClientSecret`. De enige wijziging die moet worden aangebracht, is om een lijst te maken van elk subdomein voor elke resource.

Het nieuwe __. env__ -bestand ziet er nu ongeveer als volgt uit:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Zorg ervoor dat dit bestand niet wordt door gegeven aan broncode beheer, omdat het geheimen bevat dat niet openbaar mag worden gemaakt.

We gaan nu het _routes\index.js_ -bestand wijzigen dat we hebben gemaakt ter ondersteuning van onze meerdere resources. Vervang de inhoud door de volgende code.

Net als voorheen maakt deze code een API-eind punt waarmee een Azure AD-verificatie token wordt verkregen met behulp van het wacht woord voor de Service-Principal. Op deze manier kan de gebruiker een resource locatie opgeven en deze door geven als een query parameter. Vervolgens wordt een-object met het token en het bijbehorende subdomein geretourneerd.

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

Het **getimmersivereaderlaunchparams** -API-eind punt moet worden beveiligd achter een vorm van verificatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voor komen dat onbevoegde gebruikers tokens verkrijgen om te gebruiken voor uw insluitende lezer-service en facturering; Dit werk valt buiten het bereik van deze zelf studie.

## <a name="launch-the-immersive-reader-with-sample-content"></a>De insluitende lezer starten met voorbeeld inhoud

1. Open _views\index.Pug_en vervang de inhoud door de volgende code. Met deze code wordt de pagina met enkele voorbeeld inhoud gevuld en worden twee knoppen toegevoegd waarmee de insluitende lezer wordt gestart. Een voor het starten van insluitende lezer voor de Oost-bron en een andere voor de Westus-resource.

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

3. Onze web-app is nu klaar. Start de app door uit te voeren:

    ```bash
    npm start
    ```

4. Open uw browser en ga naar [http://localhost:3000](http://localhost:3000). De bovenstaande inhoud wordt weer geven op de pagina. Klik op de knop **Oost-US insluitende lezer** of de knop **Westus insluitende lezer** om de insluitende lezer te starten met behulp van die respectieve bronnen.

## <a name="next-steps"></a>Volgende stappen

* Verken de [insluitende lezer SDK](https://github.com/microsoft/immersive-reader-sdk) en de referentie voor de [insluitende lezer SDK](./reference.md)
* Code voorbeelden weer geven op [github](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)