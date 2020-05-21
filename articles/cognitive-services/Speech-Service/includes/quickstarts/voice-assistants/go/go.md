---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 9e41ed6bdc31ce70b2744fa9a87e10c51aaac15a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673200"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Stel uw ontwikkel omgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md)
> * Een bot maken die is verbonden met het [directe-lijn spraak kanaal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Zorg ervoor dat u toegang tot een microfoon hebt voor het vastleggen van audio
>
  > [!NOTE]
  > Raadpleeg [de lijst met ondersteunde regio's voor spraak assistenten](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) om ervoor te zorgen dat uw resources in een van deze regio's worden geïmplementeerd.

## <a name="setup-your-environment"></a>Uw omgeving instellen

Werk het bestand go. rest met de nieuwste SDK-versie bij door deze regel toe te voegen
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.12.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code
Vervang de inhoud van het bron bestand (bijvoorbeeld `quickstart.go` ) door de onderstaande, waaronder:

- de hoofd definitie van het pakket
- de benodigde modules importeren uit de Speech SDK
- variabelen voor het opslaan van de bot-informatie die later in deze Quick Start wordt vervangen
- een eenvoudige implementatie met behulp van de microfoon voor audio-invoer
- gebeurtenis-handlers voor verschillende gebeurtenissen die plaatsvinden tijdens een spraak interactie

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

Vervang de `YOUR_SUBSCRIPTION_KEY` waarden en door de `YOUR_BOT_REGION` werkelijke waarden van de spraak resource.

- Ga naar het Azure Portal en open uw spraak resource
- Er zijn twee beschik bare abonnements sleutels onder **sleutels en eind punt** aan de linkerkant.
    - Gebruik een van beide als `YOUR_SUBSCRIPTION_KEY` vervanging van de waarde
- Noteer de regio onder het **overzicht** aan de linkerkant en wijs deze toe aan de regio-id
    - Gebruik de regio-id als `YOUR_BOT_REGION` vervanging van de waarde, bijvoorbeeld: `"westus"` for **VS West**

   > [!NOTE]
   > Raadpleeg [de lijst met ondersteunde regio's voor spraak assistenten](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) om ervoor te zorgen dat uw resources in een van deze regio's worden geïmplementeerd.

   > [!NOTE]
   > Zie voor meer informatie over het configureren van uw bot de bot Framework-documentatie voor [het directe lijn spraak kanaal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="code-explanation"></a>Uitleg bij code
De sleutel en de regio voor spraak abonnementen zijn vereist voor het maken van een spraak configuratie object. Het configuratie object is vereist voor het instantiëren van een spraak herkennings object.

Het Recognizer-exemplaar biedt meerdere manieren om spraak te herkennen. In dit voor beeld wordt spraak continu herkend. Deze functionaliteit zorgt ervoor dat de spraak service weet dat u veel zinsdelen verzendt voor herkenning en wanneer het programma stopt met het herkennen van spraak. Als er resultaten worden verkregen, worden deze door de code naar de console geschreven.

## <a name="build-and-run"></a>Bouwen en uitvoeren
U bent nu ingesteld om uw project te bouwen en uw aangepaste spraak assistent te testen met behulp van de spraak service.
1. Bouw uw project, bijvoorbeeld **' go build '**
2. Voer de module uit en spreek een zinsdeel of zin in op de microfoon van uw apparaat. Uw spraak wordt verzonden naar het directe lijn spraak kanaal en naar tekst getranscribeerd, die als uitvoer wordt weer gegeven.


> [!NOTE]
> De spraak-SDK wordt standaard herkend door en-US voor de taal. Zie de [bron taal voor spraak opgeven](../../../../how-to-specify-source-language.md) voor de tekst voor informatie over het kiezen van de bron taal.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
