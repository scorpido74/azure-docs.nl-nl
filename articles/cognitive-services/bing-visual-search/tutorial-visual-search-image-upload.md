---
title: 'Zelf studie: afbeelding uploaden met behulp van de Bing Visual Search-API'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het uploaden van een installatie kopie naar Bing, het verkrijgen van inzichten over it, het antwoord weer geven.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: scottwhi
ms.openlocfilehash: ecd1ab5e613bb326b65f6aa50f3f85172bc334ac
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80477932"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Zelf studie: afbeeldingen uploaden naar de Bing Visual Search-API

Met de Bing Visual Search-API kunt u op internet zoeken naar afbeeldingen die overeenkomen met de afbeeldingen die u uploadt. Gebruik deze zelfstudie om een webtoepassing te maken waarmee een afbeelding naar de API kan worden verzonden en waarmee de inzichten die worden geretourneerd op de webpagina worden weergegeven. Deze toepassing voldoet niet aan alle [vereisten voor gebruik en weergave van Bing](../bing-web-search/use-display-requirements.md) voor het gebruik van de API.

U kunt de volledige bron code voor dit voor beeld vinden met extra fout afhandeling en annotaties op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html).

In de zelfstudie-app leert u het volgende:

> [!div class="checklist"]
> * Een afbeelding uploaden naar de Bing Visual Search-API
> * Zoekresultaten voor afbeeldingen weergeven in een webtoepassing
> * De verschillende inzichten verkennen die worden geleverd door de API

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>De webpagina maken en structureren

Maak een HTML-pagina die een afbeelding naar het Bing Visual Search-API stuurt, inzichten ontvangt en deze weergeeft. Maak in uw favoriete editor of IDE een bestand met de naam ' uploaddemo. html '. Voeg de volgende basis-HTML-structuur toe aan het bestand:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

Splits de pagina in een aanvraag sectie, waarbij de gebruiker alle informatie bevat die nodig is voor de aanvraag, en een antwoord gedeelte waarin de inzichten worden weer gegeven. Voeg de volgende `<div>`-tags toe aan de `<body>`. De `<hr>` tag scheidt de aanvraag sectie visueel van de sectie Response:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Voeg een `<script>` tag toe aan `<head>` de tag om Java script voor de toepassing te bevatten:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Het uploadbestand ophalen

De toepassing gebruikt de `<input>`-tag met het kenmerk 'type' ingesteld op `file` om de gebruiker de te uploaden afbeelding te laten selecteren. De gebruikersinterface moet duidelijk maken dat de toepassing Bing gebruikt om de zoekresultaten te krijgen.

Voeg het volgende `<div>` toe aan `requestSection` `<div>`de. De bestandsinvoer accepteert één bestand, van elk afbeeldingstype (bijvoorbeeld .jpg, .gif, .png). De `onchange`-gebeurtenis geeft de handler aan die wordt aangeroepen wanneer een gebruiker een bestand selecteert.

De `<output>` tag wordt gebruikt om een miniatuur van de geselecteerde afbeelding weer te geven:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Een bestandshandler maken

Maak een handlerfunctie die kan worden gelezen in de afbeelding die u wilt uploaden. Tijdens het doorlopen van de bestanden in het `FileList`-object moet de handler ervoor zorgen dat het geselecteerde bestand een afbeeldingsbestand is van 1 MB of minder. Als de afbeelding groter is, moet u de grootte ervan verkleinen voordat u deze uploadt. Ten slotte wordt in de handler een miniatuur van de afbeelding weer gegeven:

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Een abonnementssleutel toevoegen en opslaan

Voor de toepassing is een abonnements sleutel vereist voor het aanroepen van de Bing Visual Search-API. In deze zelfstudie geeft u deze op in de gebruikersinterface. Voeg de volgende `<input>` tag (met het kenmerk type ingesteld op text) toe aan `<body>` de zojuist onder het label `<output>` van het bestand:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Aan de hand van de afbeelding en de abonnementssleutel kunt u Bing Visual Search aanroepen om inzicht te krijgen in de afbeelding. In deze zelf studie gebruikt de aanroep de standaard markt (`en-us`) en veilige Zoek waarde (`moderate`).

Deze waarden kunnen in deze toepassing worden gewijzigd. Voeg het volgende `<div>` onder de abonnements sleutel `<div>`toe. De toepassing gebruikt een `<select>`-tag om een ​​vervolgkeuzelijst te bieden voor de waarden voor de markt en Veilig Zoeken. In beide lijsten wordt de standaardwaarde weergegeven.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Zoekopties doorgeven aan de webpagina

De toepassing verbergt de lijsten in een `<div>` samenvouw bare lijst die wordt beheerd door de query opties-koppeling. Wanneer u op de koppeling query opties klikt, `<div>` wordt het uitgevouwen zodat u de query opties kunt zien en wijzigen. Als u nogmaals op de koppeling query opties klikt, `<div>` wordt het samen vouwen en verborgen. In het volgende code fragment wordt de handler van `onclick` de koppeling van de query opties weer gegeven. De handler bepaalt of de `<div>` is uitgevouwen of samengevouwen. Voeg deze handler toe aan de `<script>`-sectie. De handler wordt gebruikt door alle samenvouw `<div>` bare secties in de demo.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>De `onclick` handler aanroepen

Voeg de volgende `"Get insights"` knop onder de opties `<div>` in de hoofd tekst toe. Met deze knop kunt u de aanroep initiëren. Wanneer op de knop wordt geklikt, wordt de cursor gewijzigd in de draaiende wacht cursor `onclick` en wordt de handler aangeroepen.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Voeg de handler van `onclick` de knop `handleQuery()` toe aan `<script>` het label.

## <a name="handle-the-query"></a>De query verwerken

De handler `handleQuery()` zorgt ervoor dat de abonnements sleutel aanwezig is en 32 tekens lang is, en dat er een afbeelding is geselecteerd. Verder worden eventuele inzichten uit een eerdere query gewist. Daarna wordt de `sendRequest()`-functie aangeroepen om de aanroep uit te voeren.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>De zoekaanvraag verzenden

De `sendRequest()` functie formatteert de eind punt-URL `Ocp-Apim-Subscription-Key` , stelt de koptekst in op de abonnements sleutel, voegt het binaire bestand van de te uploaden afbeelding toe, geeft de reactie-handler op en maakt de aanroep:

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>De API-reactie ophalen en behandelen

De `handleResponse()`-functie behandelt het antwoord van de aanroep van Bing Visual Search. Als de aanroep slaagt, parseert deze functie de JSON-reactie in de afzonderlijke tags, die de inzichten bevatten. Vervolgens wordt de lijst met zoekresultaten toegevoegd aan de pagina. De toepassing maakt vervolgens een samenvouwing `<div>` voor elke tag om te beheren hoeveel gegevens er worden weer gegeven. Voeg de handler toe aan de `<script>`-sectie.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>Het antwoord parseren

De `parseResponse`-functie converteert het JSON-antwoord naar een woordenboekobject door `json.tags` te doorlopen.

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Een tagsectie bouwen

De `buildTagSections()` functie doorloopt de geparseerde JSON-Tags en roept `buildDiv()` de functie aan om `<div>` een voor elke tag te maken. Elke tag wordt weergegeven als een koppeling. Wanneer de gebruiker op de koppeling klikt, wordt de tag uitgevouwen en worden de inzichten weergegeven die aan de tag zijn gekoppeld. Als u nogmaals op de koppeling klikt, wordt de sectie samengevouwen.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>De zoekresultaten in de webpagina weergeven

Met `buildDiv()` de functie wordt `addDivContent` de functie aangeroepen om de inhoud van de samenvouw `<div>`bare code van elke tag samen te stellen.

De inhoud van een tag omvat de JSON uit het antwoord voor de tag. In eerste instantie worden alleen de eerste 100 tekens van de JSON weer gegeven, maar u kunt op de JSON-teken reeks klikken om alle JSON weer te geven. Als u hier nogmaals op klikt, wordt de JSON-tekenreeks weer samengevouwen tot 100 tekens.

Voeg vervolgens de in de tag gevonden actietypen toe. Voor elk actie type roept u de juiste functies aan om zijn inzichten toe te voegen:

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Inzichten voor verschillende acties weergeven

De volgende functies geven inzichten voor verschillende acties weer. De functies bieden een afbeelding of koppeling waarop kan worden geklikt om naar een webpagina te gaan met meer informatie over de afbeelding. Deze pagina wordt gehost door Bing.com of de oorspronkelijke website van de afbeelding. Niet alle gegevens van de inzichten worden in deze toepassing weergegeven. Zie de naslag informatie over [installatie kopieën-Visual Search](https://aka.ms/bingvisualsearchreferencedoc) voor een overzicht van alle beschik bare velden voor een inzicht.

> [!NOTE]
> Op de pagina moet een minimale hoeveelheid inzichtgegevens worden weergegeven. Raadpleeg het [Bing Search-API-gebruik en de weergave vereisten](../bing-web-search/use-display-requirements.md) voor meer informatie.

### <a name="relatedimages-insights"></a>RelatedImages-inzichten

De `addRelatedImages()` functie maakt een titel voor elke website die als host fungeert voor de gerelateerde installatie kopie door de lijst `RelatedImages` met acties te herhalen en een `<img>` tag toe te voegen aan de buiten kant `<div>` voor elk:

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>PagesIncluding-inzichten

De `addPagesIncluding()` functie maakt een koppeling voor elke website die als host fungeert voor de geüploade installatie kopie door de `PagesIncluding` lijst met acties te herhalen en `<img>` een tag toe te `<div>` voegen aan de buiten kant voor elk:

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>RelatedSearches-inzichten

De `addRelatedSearches()` functie maakt een koppeling voor de website die als host fungeert voor de installatie kopie door de `RelatedSearches` lijst met acties te herhalen `<img>` en een tag toe `<div>` te voegen aan de buiten kant voor elk:

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Recepten-inzichten

De `addRecipes()` functie maakt een koppeling voor elke geretourneerde recepten door de lijst met `Recipes` acties te herhalen en een `<img>` tag toe te voegen aan de `<div>` buiten kant voor elk:

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Winkel-inzichten

De `addShopping()` functie maakt een koppeling voor alle geretourneerde resultaten van de winkel door de lijst `RelatedImages` met acties te herhalen en een `<img>` tag toe te voegen aan de buitenste `<div>` voor elk:

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Product-inzichten

De `addProducts()` functie maakt een koppeling voor de resultaten van geretourneerde producten door de lijst met `Products` acties te herhalen en een `<img>` tag toe te voegen `<div>` aan de buiten kant voor elk:

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>TextResult-inzichten

De `addTextResult()` functie geeft een tekst weer die in de afbeelding is herkend:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

Met `addEntity()` de functie wordt een koppeling weer gegeven waarmee de gebruiker naar Bing.com wordt geleid, waar ze Details over het entiteits type in de installatie kopie kunnen ophalen, indien aanwezig.

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

Met `addImageWithWebSearchUrl()` de functie wordt een klik bare afbeelding `<div>` weer gegeven waarmee de gebruiker de resultaten van Bing.com kan zoeken:

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>Een CSS-stijl toevoegen

Voeg de volgende `<style>` sectie toe aan `<head>` het label om de lay-out van de webpagina te organiseren:

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>Volgende stappen

>[!div class="nextstepaction"]
> [Zelf studie: vergelijk bare afbeeldingen uit eerdere Zoek opdrachten zoeken met ImageInsightsToken](./tutorial-visual-search-insights-token.md)
