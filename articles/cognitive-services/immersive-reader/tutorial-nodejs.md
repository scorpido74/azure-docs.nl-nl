---
title: 'Zelfstudie: Start de meeslepende lezer met Behulp van Node.js'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maakt u een Node.js-toepassing die de meeslepende lezer lanceert.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 139dd2ebdabbc91a6de3b0a1eb921b110d47c3f3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842024"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Zelfstudie: Start de meeslepende lezer (Node.js)

In het [overzicht](./overview.md)leerde je over wat de Immersive Reader is en hoe het bewezen technieken implementeert om het begrijpend lezen te verbeteren voor taalstudenten, opkomende lezers en studenten met leerverschillen. Deze zelfstudie gaat over het maken van een Node.js-webtoepassing waarmee de immersive reader wordt gestart. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Node.js-web-app maken met Express
> * Een toegangstoken verkrijgen
> * Start de meeslepende lezer met voorbeeldinhoud
> * De taal van uw inhoud opgeven
> * De taal van de interface Immersive Reader opgeven
> * Start de meeslepende lezer met wiskundige inhoud

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Immersive Reader-bron die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](./how-to-create-immersive-reader.md) om je op te stellen. U hebt een aantal waarden nodig die hier zijn gemaakt bij het configureren van de omgevingseigenschappen. Sla de uitvoer van uw sessie op in een tekstbestand voor toekomstige verwijzingen.
* [Node.js](https://nodejs.org/) en [garen](https://yarnpkg.com)
* Een IDE zoals [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Een Node.js-web-app maken met Express

Maak een Web-app Node.js met de `express-generator` tool.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Installeer garen afhankelijkheden, en `request` `dotenv`voeg afhankelijkheden en , die later zal worden gebruikt in de tutorial.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Een Azure AD-verificatietoken aanschaffen

Schrijf vervolgens een backend-API om een Azure AD-verificatietoken op te halen.

U hebt een bepaalde waarden nodig uit de bovenstaande stap voor de configuratievereiste Azure AD Auth voor dit onderdeel. Ga terug naar het tekstbestand dat u van die sessie hebt opgeslagen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Zodra u deze waarden hebt, maakt u een nieuw bestand met de naam _.env_en plakt u de volgende code erin, met de levering van uw aangepaste eigenschapswaarden van bovenaf. Voeg geen aanhalingstekens of de tekens {" en "}" toe.

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

Open het bestand _routes\index.js_ en vervang de inhoud ervan door de volgende code.

Met deze code wordt een API-eindpunt gemaakt dat een Azure AD-verificatietoken verwerft met behulp van het hoofdwachtwoord van de service. Het haalt ook het subdomein op. Vervolgens wordt een object geretourneerd dat het token en subdomein bevat.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
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
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

Het **getimmersivereaderlaunchparams** API-eindpunt moet worden beveiligd achter een of andere vorm van authenticatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voorkomen dat onbevoegde gebruikers tokens verkrijgen om te gebruiken tegen uw Immersive Reader-service en facturering; dat werk valt buiten het bereik van deze tutorial.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Start de meeslepende lezer met voorbeeldinhoud

1. Open _weergaven\layout.pug_en voeg de `head` volgende code `body` toe onder de tag, vóór de tag. Deze `script` tags laden de [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) en jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Open _views\index.pug_en vervang de inhoud ervan door de volgende code. Deze code vult de pagina met een aantal voorbeeldinhoud en voegt een knop toe waarmee de meeslepende lezer wordt gestart.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. Onze web-app is nu klaar. Start de app door het uitvoeren van:

    ```bash
    npm start
    ```

4. Open uw browser _http://localhost:3000_en navigeer naar . U ziet de bovenstaande inhoud op de pagina. Klik op de knop **Meeslepende lezer** om de meeslepende lezer met uw inhoud te starten.

## <a name="specify-the-language-of-your-content"></a>De taal van uw inhoud opgeven

De Immersive Reader heeft ondersteuning voor veel verschillende talen. U de taal van uw inhoud opgeven door de onderstaande stappen te volgen.

1. Open _views\index.pug_ en voeg de `p(id=content)` volgende code toe onder de tag die u in de vorige stap hebt toegevoegd. Deze code voegt inhoud spaanse inhoud toe aan uw pagina.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. Voeg in de JavaScript-code het `ImmersiveReader.launchAsync`volgende boven de aanroep toe aan . Deze code geeft de Spaanse inhoud door in de meeslepende lezer.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Navigeer _http://localhost:3000_ opnieuw naar. U zou de Spaanse tekst op de pagina moeten zien, en wanneer u op **Meeslepende Lezer**klikt, zal het in de Meeslepende Lezer eveneens verschijnen.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>De taal van de interface Immersive Reader opgeven

Standaard komt de taal van de interface Immersive Reader overeen met de taalinstellingen van de browser. U ook de taal van de interface Immersive Reader opgeven met de volgende code.

1. Vervang in _views\index.pug_de `ImmersiveReader.launchAsync(token, subdomain, content)` aanroep door de onderstaande code.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Navigeer _http://localhost:3000_naar . Wanneer u de Immersive Reader start, wordt de interface in het Frans weergegeven.

## <a name="launch-the-immersive-reader-with-math-content"></a>Start de meeslepende lezer met wiskundige inhoud

U wiskundige inhoud opnemen in de meeslepende lezer met [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Wijzig _views\index.pug_ om de volgende code `ImmersiveReader.launchAsync`boven de aanroep op te nemen:

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Navigeer _http://localhost:3000_naar . Wanneer u de meeslepende lezer start en naar beneden schuift, ziet u de wiskundige formule.

## <a name="next-steps"></a>Volgende stappen

* Ontdek de [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) en de [Immersive Reader SDK Reference](./reference.md)
* Codevoorbeelden weergeven op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
