---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 3740158f7134d7f0317809a7e1e89a97cdfffd94
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83672996"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Stel uw ontwikkel omgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md)
> * Zorg ervoor dat u toegang tot een microfoon hebt voor het vastleggen van audio

## <a name="setup-your-environment"></a>Uw omgeving instellen

Werk het bestand go. rest met de nieuwste SDK-versie bij door deze regel toe te voegen
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.12.0-alpha1
)
```

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code
1. Vervang de inhoud van het bron bestand (bijvoorbeeld `sr-quickstart.go` ) door de onderstaande, waaronder:

- de hoofd definitie van het pakket
- de benodigde modules importeren uit de Speech SDK
- variabelen voor het opslaan van de abonnements gegevens die later in deze Snelstartgids zullen worden vervangen
- een eenvoudige implementatie met behulp van de microfoon voor audio-invoer
- gebeurtenis-handlers voor verschillende gebeurtenissen die plaatsvinden tijdens een spraak herkenning

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

Vervang de `YOUR_SUBSCRIPTION_KEY` waarden en door de `YOUR_SUBSCRIPTIONKEY_REGION` werkelijke waarden van de spraak resource.

- Ga naar het Azure Portal en open de spraak resource
- Er zijn twee beschik bare abonnements sleutels onder de **sleutels** aan de linkerkant.
    - Gebruik een van beide als `YOUR_SUBSCRIPTION_KEY` vervanging van de waarde
- Noteer de regio onder het **overzicht** aan de linkerkant en wijs deze toe aan de regio-id
- Gebruik de regio-id als `YOUR_SUBSCRIPTIONKEY_REGION` vervanging van de waarde, bijvoorbeeld: `"westus"` for **VS West**

## <a name="code-explanation"></a>Uitleg bij code
De sleutel en de regio voor spraak abonnementen zijn vereist voor het maken van een spraak configuratie object. Het configuratie object is vereist voor het instantiëren van een spraak herkennings object.

Het Recognizer-exemplaar biedt meerdere manieren om spraak te herkennen. In dit voor beeld wordt spraak continu herkend. Deze functionaliteit zorgt ervoor dat de spraak service weet dat u veel zinsdelen verzendt voor herkenning en wanneer het programma stopt met het herkennen van spraak. Als er resultaten worden verkregen, worden deze door de code naar de console geschreven.

## <a name="build-and-run"></a>Bouwen en uitvoeren
U kunt nu uw project bouwen en spraak herkenning testen met behulp van de speech-service.
1. Bouw uw project, bijvoorbeeld **' go build '**
2. Voer de module uit en spreek een zinsdeel of zin in op de microfoon van uw apparaat. Uw spraak wordt verzonden naar de speech-service en getranscribeerd naar tekst, die wordt weer gegeven in de uitvoer.


> [!NOTE]
> De spraak-SDK wordt standaard herkend door en-US voor de taal. Zie de [bron taal voor spraak opgeven](../../../../how-to-specify-source-language.md) voor de tekst voor informatie over het kiezen van de bron taal.


## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
