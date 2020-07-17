---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 7e2960adce028450fd3ccdb9eb11190629bf7bb8
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035643"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een resource voor de Azure Speech-service maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md)

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode

We gaan wat code toevoegen die als basis voor het project gaat dienen.

```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Microsoft Cognitive Services Speech SDK JavaScript Quickstart</title>
    <meta charset="utf-8" />
    </head>
    <body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
    </body>
    </html>
```
## <a name="add-ui-elements"></a>UI-elementen toevoegen

Nu gaan we enkele eenvoudige gebruikersinterface-elementen toevoegen (invoervakken), verwijzen naar de JavaScript van de Speech-SDK en een autorisatietoken ophalen, indien beschikbaar.

```html  
  <div id="content" style="display:none">
    <table width="100%">
      <tr>
        <td></td>
        <td><h1 style="font-weight:500;">Microsoft Cognitive Services Speech SDK JavaScript Quickstart</h1></td>
      </tr>
      <tr>
        <td align="right"><a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started" target="_blank">Subscription</a>:</td>
        <td><input id="subscriptionKey" type="text" size="40" value="subscription"></td>
      </tr>
      <tr>
        <td align="right">Region</td>
        <td><input id="serviceRegion" type="text" size="40" value="YourServiceRegion"></td>
      </tr>
      <tr>
        <td align="right">File</td>
        <td><input type="file" id="filePicker" accept=".wav" style="display:none" /></td>
      </tr>
      <tr>
        <td></td>
        <td><button id="startRecognizeOnceAsyncButton">Start recognition</button></td>
      </tr>
      <tr>
        <td align="right" valign="top">Results</td>
        <td><textarea id="phraseDiv" style="display: inline-block;width:500px;height:200px"></textarea></td>
      </tr>
    </table>
  </div>

  <script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>

   <script>
  // Note: Replace the URL with a valid endpoint to retrieve
  //       authorization tokens for your subscription.
  var authorizationEndpoint = "token.php";

  function RequestAuthorizationToken() {
    if (authorizationEndpoint) {
      var a = new XMLHttpRequest();
      a.open("GET", authorizationEndpoint);
      a.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
      a.send("");
      a.onload = function() {
          var token = JSON.parse(atob(this.responseText.split(".")[1]));
          serviceRegion.value = token.region;
          authorizationToken = this.responseText;
          subscriptionKey.disabled = true;
          subscriptionKey.value = "using authorization token (hit F5 to refresh)";
          console.log("Got an authorization token: " + token);
      }
    }
  }
  </script>
  
  <script>
    // status fields and start button in UI
    var phraseDiv;
    var startRecognizeOnceAsyncButton;

    // subscription key and region for speech services.
    var subscriptionKey, serviceRegion;
    var authorizationToken;
    var SpeechSDK;
    var recognizer;
    var filePicker;
    var audioFile;

    document.addEventListener("DOMContentLoaded", function () {
      startRecognizeOnceAsyncButton = document.getElementById("startRecognizeOnceAsyncButton");
      subscriptionKey = document.getElementById("subscriptionKey");
      serviceRegion = document.getElementById("serviceRegion");
      phraseDiv = document.getElementById("phraseDiv");
      filePicker = document.getElementById('filePicker');
      
      filePicker.addEventListener("change", function () {
                audioFile = filePicker.files[0];
            });

      startRecognizeOnceAsyncButton.addEventListener("click", function () {
        startRecognizeOnceAsyncButton.disabled = true;
        phraseDiv.innerHTML = "";

      });

      if (!!window.SpeechSDK) {
        SpeechSDK = window.SpeechSDK;
        startRecognizeOnceAsyncButton.disabled = false;

        document.getElementById('content').style.display = 'block';
        document.getElementById('warning').style.display = 'none';

        // in case we have a function for getting an authorization token, call it.
        if (typeof RequestAuthorizationToken === "function") {
            RequestAuthorizationToken();
        }
      }
    });
  </script>
```
 
## <a name="create-a-speech-configuration"></a>Een Speech-configuratie maken

Voordat u een `SpeechRecognizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van de abonnementssleutel en de regio van het abonnement. Voeg deze code toe in de methode `startRecognizeOnceAsyncButton.addEventListener()`.

> [!NOTE]
> De Speech-SDK probeert taal standaard te herkennen in en-US. Zie [De brontaal voor spraak-naar-tekst opgeven](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.


```JavaScript
        // if we got an authorization token, use the token. Otherwise use the provided subscription key
        var speechConfig;
        if (authorizationToken) {
          speechConfig = SpeechSDK.SpeechConfig.fromAuthorizationToken(authorizationToken, serviceRegion.value);
        } else {
          if (subscriptionKey.value === "" || subscriptionKey.value === "subscription") {
            alert("Please enter your Microsoft Cognitive Services Speech subscription key!");
            return;
          }
          speechConfig = SpeechSDK.SpeechConfig.fromSubscription(subscriptionKey.value, serviceRegion.value);
        }

        speechConfig.speechRecognitionLanguage = "en-US";
```

## <a name="create-an-audio-configuration"></a>Een audioconfiguratie maken

Nu moet u een `AudioConfig`-object maken dat verwijst naar het audiobestand. Voeg deze code toe in de methode `startRecognizeOnceAsyncButton.addEventListener()`, recht onder uw Speech-configuratie.

```JavaScript
        var audioConfig  = SpeechSDK.AudioConfig.fromWavFileInput(audioFile);
```

## <a name="initialize-a-speechrecognizer"></a>Een SpeechRecognizer initialiseren

Nu gaan we het `SpeechRecognizer`-object maken met behulp van de `SpeechConfig`- en `AudioConfig`-objecten die u eerder hebt gemaakt. Voeg deze code toe in de methode `startRecognizeOnceAsyncButton.addEventListener()`.

```JavaScript
        recognizer = new SpeechSDK.SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-a-phrase"></a>Een woordgroep herkennen

Vanuit het `SpeechRecognizer`-object roept u de methode `recognizeOnceAsync()` aan. Met deze methode laat u de Speech-service weten dat u één woordgroep verstuurt voor herkenning en dat er kan worden gestopt met het herkennen van spraak zodra de woordgroep is geïdentificeerd.

```JavaScript
recognizer.recognizeOnceAsync(
          function (result) {
            startRecognizeOnceAsyncButton.disabled = false;
            phraseDiv.innerHTML += result.text;
            window.console.log(result);

            recognizer.close();
            recognizer = undefined;
          },
          function (err) {
            startRecognizeOnceAsyncButton.disabled = false;
            phraseDiv.innerHTML += err;
            window.console.log(err);

            recognizer.close();
            recognizer = undefined;
          });
```

## <a name="check-your-code"></a>Uw code controleren

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-from-file.html)]

## <a name="create-the-token-source-optional"></a>De tokenbron maken (optioneel)

Als u de webpagina wilt hosten op een webserver, kunt u desgewenst een tokenbron opgeven voor uw voorbeeldtoepassing.
Op die manier verlaat uw abonnementssleutel nooit uw server en is het gebruikers toegestaan gebruik te maken van spraakmogelijkheden zonder zelf een autorisatiecode in te hoeven voeren.

Maak een nieuw bestand met de naam `token.php`. In dit voorbeeld gaan we ervan uit dat uw webserver de PHP-scripttaal ondersteunt als cURL is ingeschakeld. Voer de volgende code in:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Autorisatietokens hebben slechts een beperkte levensduur.
> In dit eenvoudige voorbeeld wordt niet weergegeven hoe autorisatietokens automatisch worden vernieuwd. Als een gebruiker kunt u de pagina handmatig opnieuw laden of op F5 drukken om te vernieuwen.

## <a name="build-and-run-the-sample-locally"></a>Het voorbeeld bouwen en lokaal uitvoeren

Om de app te starten, dubbelklikt u op het bestand index.html of opent u index.html met uw favoriete webbrowser. Er wordt een eenvoudige grafische gebruikersinterface weergegeven waarin u uw abonnementssleutel en [regio](../../../../regions.md) kunt invoeren spraakherkenning met behulp van de microfoon kunt activeren.

> [!NOTE]
> Deze methode werkt niet in de Safari-browser.
> In Safari moet de voorbeeldwebpagina worden gehost op een webserver. Met Safari is het gebruik van de microfoon niet mogelijk voor websites die zijn geladen vanuit een lokaal bestand.

## <a name="build-and-run-the-sample-via-a-web-server"></a>De voorbeeldtoepassing bouwen en uitvoeren via een webserver

Om uw app te starten, opent u uw favoriete webbrowser en laat u deze wijzen naar de openbare URL waarop u de map host, geeft u uw [regio](../../../../regions.md) op en activeert u spraakherkenning met behulp van de microfoon. Indien dit is geconfigureerd, wordt er een token uit uw tokenbron opgehaald.


## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]