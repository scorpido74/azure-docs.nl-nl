---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 19c054b72f7aa2ed6108d076a48856919a01462d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838939"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een resource voor de Azure Speech-service maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md)

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode

We gaan wat code toevoegen die als basis voor het project gaat dienen. Maak een index.js-bestand en voeg deze code toe.

Zorg ervoor dat u de waarden voor `subscriptionKey`, `servcieRegion` en `filename` invult.

```JavaScript
(function() {
  // <code>
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech synthesizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
 
}());
  
```
## <a name="load-the-file-into-an-pullaudiooutputstream"></a>Het bestand in een PullAudioOutputStream laden

Voor NodeJS biedt de Speech-SDK geen systeemeigen ondersteuning voor het openen van bestanden, dus openen we het bestand en schrijven we ernaar met een `PullAudioOutputStream`.


```JavaScript
// create the push stream we need for the speech sdk.
  var pullStream = sdk.AudioOutputStream.createPullStream();
  
  // open the file and push it to the push stream.
  fs.createWriteStream(filename).on('data', function(arrayBuffer) {
    pullStream.read(arrayBuffer.slice());
  }).on('end', function() {
    pullStream.close();
  });
```

## <a name="create-a-speech-configuration"></a>Een Speech-configuratie maken

Voordat u een `SpeechSynthesizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van de abonnementssleutel en de regio van het abonnement. Voeg daarna deze code toe.

> [!NOTE]
> De Speech-SDK probeert taal standaard te herkennen in en-US. Zie [De brontaal voor spraak-naar-tekst opgeven](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

 ```JavaScript
   // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
```
## <a name="create-an-audio-configuration"></a>Een audioconfiguratie maken

Nu moet u een `AudioConfig`-object maken dat verwijst naar uw `PullAudioOutputStream`. Voeg deze code toe onder uw Speech-configuratie.

```JavaScript
    var audioConfig = sdk.AudioConfig.fromStreamInput(pullStream);
```

## <a name="initialize-a-speechsynthesizer"></a>Een SpeechSynthesizer initialiseren

Nu gaan we het `SpeechSynthesizer`-object maken met behulp van de `SpeechConfig`- en `AudioConfig`-objecten die u eerder hebt gemaakt.

```JavaScript
  // create the speech synthesizer.
  var synthesizer = new sdk.SpeechSynthesizer(speechConfig, audioConfig);
  
```
## <a name="recognize-a-phrase-and-display-results"></a>Een woordgroep herkennen en resultaten weergeven

Vanuit het `SpeechSynthesizer`-object roept u de methode `speakTextAsync()` aan. Met deze methode laat u de Speech-service weten dat u tekst gaat opsturen voor synthese.

We schrijven ook het geretourneerde resultaat, of eventuele fouten, naar de console en sluiten tot slot de synthesizer.
```JavaScript
 // we are done with the setup
  var text = "Hello World"
  console.log("Now sending text '" + text + "' to: " + filename);
  
  // start the synthesizer and wait for a result.
  synthesizer.speakTextAsync(
    text,
    function (result) {
      console.log(result);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    filename);
```
## <a name="check-your-code"></a>Uw code controleren
```JavaScript
(function() {
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech synthesizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
  
  // create the pull stream we need for the speech sdk.
  var pullStream = sdk.AudioOutputStream.createPullStream();
  
  // open the file and write it to the pull stream.
  fs.createWriteStream(filename).on('data', function(arrayBuffer) {
    pullStream.read(arrayBuffer.slice());
  }).on('end', function() {
    pullStream.close();
  });
 
  // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
  var audioConfig = sdk.AudioConfig.fromStreamOutput(pullStream);
  
  // create the speech synthesizer.
  var synthesizer = new sdk.SpeechSynthesizer(speechConfig, audioConfig);
  
 // we are done with the setup
  var text = "Hello World"
  console.log("Now sending text '" + text + "' to: " + filename);
  
  // start the synthesizer and wait for a result.
  synthesizer.speakTextAsync(
    text,
    function (result) {
      console.log(result);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    filename);

}());
```
## <a name="run-the-sample-locally"></a>Het voorbeeld lokaal uitvoeren

De code uitvoeren met behulp van NodeJs
```bash
node index.js
```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]