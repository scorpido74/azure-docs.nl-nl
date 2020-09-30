---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: a18ce7b7c28b99967668bc33c5a94cbb58bfbc34
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376940"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een resource voor de Azure-spraakservice maken](../../../../overview.md#try-the-speech-service-for-free)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md)
> * Ervoor zorgen dat u toegang tot een microfoon hebt voor het vastleggen van audio

## <a name="setup-your-environment"></a>Uw omgeving instellen

Werk het bestand go.mod bij met de nieuwste SDK-versie door deze regel toe te voegen
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode
1. Vervang de inhoud van het bronbestand (bijvoorbeeld `sr-quickstart.go`) door de onderstaande, die het volgende bevat:

- definitie van het 'main'-pakket
- de benodigde modules importeren uit de Spraak-SDK
- variabelen voor het opslaan van de abonnementsgegevens die later in deze quickstart worden vervangen
- een eenvoudige implementatie waarin de microfoon wordt gebruikt voor audio-invoer
- gebeurtenis-handlers voor verschillende gebeurtenissen die plaatsvinden tijdens spraakherkenning

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

Vervang de waarden `YOUR_SUBSCRIPTION_KEY` en `YOUR_SUBSCRIPTIONKEY_REGION` door de werkelijke waarden van de Spraak-resource.

- Ga naar de Azure-portal en open de Spraak-resource
- Onder de **Sleutels** aan de linkerkant bevinden zich twee beschikbare abonnementssleutels
    - Gebruik een van beide als vervanging van de waarde `YOUR_SUBSCRIPTION_KEY`
- Noteer de regio onder **Overzicht** aan de linkerkant en wijs deze toe aan de regio-id
- Gebruik de regio-id als vervanging van de waarde `YOUR_SUBSCRIPTIONKEY_REGION`, bijvoorbeeld `"westus"` voor **US - west**

## <a name="code-explanation"></a>Uitleg bij de code
De sleutel en de regio voor het Spraak-abonnement zijn vereist voor het maken van een spraakconfiguratieobject. Het configuratieobject is vereist voor het instantiëren van een spraakherkenningsobject.

Het Recognizer-exemplaar biedt meerdere manieren om spraak te herkennen. In dit voorbeeld wordt spraak continu herkend. Door deze functionaliteit weet de Spraak-service dat u veel frasen ter herkenning verzendt, en dat het herkennen van spraak moet stoppen wanneer het programma wordt beëindigd. Wanneer er resultaten worden verkregen, worden deze door de code naar de console geschreven.

## <a name="build-and-run"></a>Bouwen en uitvoeren
U bent nu klaar om uw app te bouwen en de spraakherkenning met behulp van de Spraak-service te testen.
1. Compileer uw project, bijvoorbeeld **"go-build"**
2. Voer de module uit en spreek enkele woorden of een zin in de microfoon van uw apparaat. Uw gesproken tekst wordt verzonden naar de Spraak-service en getranscribeerd naar tekst, die in de uitvoer wordt weergegeven.


> [!NOTE]
> De Speech-SDK probeert taal standaard te herkennen in en-US. Zie [De brontaal voor spraak-naar-tekst opgeven](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.


## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
