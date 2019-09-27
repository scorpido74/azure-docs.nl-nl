---
title: 'Zelfstudie: De insluitende lezer starten met node. js'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie maakt u een node. js-toepassing waarmee de insluitende lezer wordt gestart.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 2a07e392170fb9e6993f4c560a4896a468d90820
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338505"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Zelfstudie: Start de Insluitende lezer (Node.js)

In het [overzicht](./overview.md)hebt u geleerd wat de insluitende lezer is en hoe deze de bewezen technieken implementeert om de Lees vaardigheid te verbeteren voor taal kennis, opkomende lezers en studenten met meer informatie. In deze zelf studie wordt beschreven hoe u een node. js-webtoepassing maakt waarmee de insluitende lezer wordt gestart. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een node. js-web-app maken met Express
> * Een toegangstoken verkrijgen
> * De insluitende lezer starten met voorbeeld inhoud
> * De taal van uw inhoud opgeven
> * De taal van de insluitende Reader-interface opgeven
> * De insluitende lezer starten met wiskundige inhoud

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie (Azure AD). Volg [deze instructies om de](./azure-active-directory-authentication.md) instellingen op te halen. U hebt enkele van de waarden nodig die u hier hebt gemaakt bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekst bestand voor toekomstig naslag doeleinden.
* [Node. js](https://nodejs.org/) en [garens](https://yarnpkg.com)
* Een IDE zoals [Visual Studio code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Een node. js-web-app maken met Express

Maak een node. js-web-app `express-generator` met het hulp programma.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Installeer garen afhankelijkheden en voeg `request` afhankelijkheden `dotenv`toe en, die later in de zelf studie worden gebruikt.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Een Azure AD-verificatie token verkrijgen

Schrijf vervolgens een back-end-API om een Azure AD-verificatie token op te halen.

Voor dit onderdeel hebt u enkele waarden nodig uit de hierboven beschreven Azure AD-verificatie configuratie. Ga terug naar het tekst bestand dat u van deze sessie hebt opgeslagen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Zodra u deze waarden hebt, maakt u een nieuw bestand met de naam _. env_en plakt u de volgende code in de gegevens bron, waarbij u de waarden van de aangepaste eigenschappen kunt opgeven. Neem geen aanhalings tekens of de {en} op.

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

Open het _routes\index.js_ -bestand en vervang de inhoud door de volgende code.

Met deze code wordt een API-eind punt gemaakt waarmee een Azure AD-verificatie token wordt verkregen met behulp van het wacht woord voor de Service-Principal. Ook wordt het subdomein opgehaald. Vervolgens wordt een object geretourneerd dat het token en subdomein bevat.

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

Het **getimmersivereaderlaunchparams** -API-eind punt moet worden beveiligd achter een vorm van verificatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voor komen dat onbevoegde gebruikers tokens verkrijgen om te gebruiken voor uw insluitende lezer-service en facturering; Dit werk valt buiten het bereik van deze zelf studie.

## <a name="launch-the-immersive-reader-with-sample-content"></a>De insluitende lezer starten met voorbeeld inhoud

1. Open _views\layout.Pug_en voeg de volgende code toe onder het `head` label vóór de `body` tag. Met `script` deze tags worden de [insluitende lezer-SDK](https://github.com/microsoft/immersive-reader-sdk) en jQuery geladen.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Open _views\index.Pug_en vervang de inhoud door de volgende code. Met deze code wordt de pagina met enkele voorbeeld inhoud gevuld en wordt een knop toegevoegd waarmee de insluitende lezer wordt gestart.

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

3. Onze web-app is nu klaar. Start de app door uit te voeren:

    ```bash
    npm start
    ```

4. Open uw browser en ga naar _http://localhost:3000_ . De bovenstaande inhoud wordt weer geven op de pagina. Klik op de knop **insluitende lezer** om de insluitende lezer te starten met de inhoud.

## <a name="specify-the-language-of-your-content"></a>De taal van uw inhoud opgeven

De insluitende lezer biedt ondersteuning voor veel verschillende talen. U kunt de taal van uw inhoud opgeven door de volgende stappen uit te voeren.

1. Open _views\index.Pug_ en voeg de volgende code toe onder `p(id=content)` het label dat u in de vorige stap hebt toegevoegd. Met deze code wordt de Engelse inhoud van inhoud aan uw pagina toegevoegd.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. Voeg in de Java script-code het volgende toe boven de `ImmersiveReader.launchAsync`aanroep van. Met deze code wordt de Spaanse inhoud door gegeven aan de insluitende lezer.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Ga opnieuw _http://localhost:3000_ naar opnieuw. De Spaanse tekst op de pagina wordt weer gegeven, en wanneer u op **insluitende lezer**klikt, wordt deze ook in de insluitende lezer getoond.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>De taal van de insluitende Reader-interface opgeven

De taal van de insluitende Reader-interface komt standaard overeen met de taal instellingen van de browser. U kunt ook de taal van de insluitende Reader-interface met de volgende code opgeven.

1. Vervang in _views\index.Pug_de aanroep door door `ImmersiveReader.launchAsync(token, subdomain, content)` de onderstaande code in te voeren.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Navigeer naar _http://localhost:3000_ . Wanneer u de insluitende lezer start, wordt de interface weer gegeven in het Frans.

## <a name="launch-the-immersive-reader-with-math-content"></a>De insluitende lezer starten met wiskundige inhoud

U kunt wiskundige inhoud in de insluitende lezer toevoegen met behulp van [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Wijzig _views\index.Pug_ zodat de volgende code wordt toegevoegd aan de aanroep `ImmersiveReader.launchAsync`:

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

2. Navigeer naar _http://localhost:3000_ . Wanneer u de insluitende lezer start en naar beneden schuift, ziet u de formule math.

## <a name="next-steps"></a>Volgende stappen

* Verken de [insluitende lezer SDK](https://github.com/microsoft/immersive-reader-sdk) en de referentie voor de [insluitende lezer SDK](./reference.md)
* Code voorbeelden weer geven op [github](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
