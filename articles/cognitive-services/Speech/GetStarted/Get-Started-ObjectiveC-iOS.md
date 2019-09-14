---
title: Aan de slag met de Bing Speech Recognition API in doelstelling-C op iOS | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Gebruik de Bing Speech Recognition API om iOS-toepassingen te ontwikkelen die gesp roken audio naar tekst converteren.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e42784e6d2751f7e76aec8caf1d6e1f9f09a9fd1
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965937"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Quickstart: De Bing Speech Recognition API in doel-C op iOS gebruiken

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Met de API voor spraak herkenning kunt u iOS-toepassingen ontwikkelen die gebruikmaken van de spraak service op basis van de cloud om gesp roken audio naar tekst te converteren. De API ondersteunt realtime streaming, zodat uw toepassing tegelijkertijd gedeeltelijke herkennings resultaten kan ontvangen op het moment dat de audio naar de service wordt verzonden.

In dit artikel wordt een voorbeeld toepassing gebruikt om te demonstreren hoe u aan de slag gaat met de API voor spraak herkenning voor het ontwikkelen van een iOS-toepassing. Zie [Naslag informatie voor de Speech SDK-client bibliotheek](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)voor een volledige API-verwijzing.

## <a name="prerequisites"></a>Vereisten

### <a name="platform-requirements"></a>Platformvereisten

Controleer of de Mac XCode IDE is geïnstalleerd.

### <a name="get-the-client-library-and-examples"></a>De client bibliotheek en voor beelden ophalen

De spraakclientbibliotheek en voor beelden voor iOS zijn beschikbaar in de [Speech client SDK voor IOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Abonneer u op de spraakherkennings-API en ontvang een gratis proef abonnement sleutel

De Speech-API maakt deel uit van Cognitive Services (voorheen project Oxford). U kunt abonnements sleutels voor een gratis proef versie downloaden van de pagina [Cognitive Services abonnement](https://azure.microsoft.com/try/cognitive-services/) . Nadat u de spraak-API hebt geselecteerd, selecteert u **API-sleutel ophalen** om de sleutel op te halen. Er wordt een primaire en secundaire sleutel geretourneerd. Beide sleutels zijn gekoppeld aan hetzelfde quotum, dus u kunt beide sleutels gebruiken.

Als u *herkenning met opzet*wilt gebruiken, moet u zich ook registreren voor de [Language Understanding intelligent service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Een abonnements sleutel ophalen. Voordat u Speech client-bibliotheken kunt gebruiken, moet u een [abonnements sleutel](https://azure.microsoft.com/try/cognitive-services/)hebben.
>
> * Gebruik uw abonnements sleutel. Met de meegeleverde iOS-voorbeeld toepassing moet u de bestands voorbeelden/SpeechRecognitionServerExample/Settings. plist bijwerken met uw abonnements sleutel. Zie voor [beelden bouwen en uitvoeren](#build-and-run-samples)voor meer informatie.

## <a name="use-the-speech-client-library"></a>De spraakclientbibliotheek gebruiken

Volg deze [instructies](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library)om de client bibliotheek toe te voegen aan een Xcode-project.

Zie deze [webpagina](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)om de referentie van de client bibliotheek voor IOS te vinden.

## <a name="build-and-run-samples"></a>Voor beelden bouwen en uitvoeren

Zie deze [Leesmij-pagina](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample)voor meer informatie over het maken en uitvoeren van voor beelden.

## <a name="samples-explained"></a>Voor beelden beschreven

### <a name="create-recognition-clients"></a>Herkennings-clients maken

De volgende code in het voor beeld laat zien hoe u herkennings-client klassen maakt op basis van gebruikers scenario's:

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

De client bibliotheek biedt vooraf geïmplementeerde client klassen voor herkenning voor typische scenario's in spraak herkenning:

* `DataRecognitionClient`: Spraak herkenning met PCM-gegevens (bijvoorbeeld van een bestand of audio bron). De gegevens worden opgedeeld in buffers en elke buffer wordt naar de speech-service verzonden. Er worden geen wijzigingen aangebracht in de buffers, zodat gebruikers hun eigen stilte detectie kunnen Toep assen, indien gewenst. Als de gegevens afkomstig zijn uit WAV-bestanden, kunt u gegevens van het bestand rechtstreeks naar de server verzenden. Als u onbewerkte gegevens hebt, bijvoorbeeld audio die via Bluetooth komt, verzendt u eerst een indelings header naar de server, gevolgd door de gegevens.
* `MicrophoneRecognitionClient`: Spraak herkenning met audio afkomstig van de microfoon. Zorg ervoor dat de microfoon is ingeschakeld en dat de gegevens van de microfoon worden verzonden naar de service voor spraak herkenning. Er wordt een ingebouwde "stilte detector" toegepast op de microfoon gegevens voordat deze naar de herkennings service worden verzonden.
* `DataRecognitionClientWithIntent` en `MicrophoneRecognitionClientWithIntent`: Deze clients geven naast herkennings tekst gestructureerde informatie over de bedoeling van de spreker, die uw toepassingen kunnen gebruiken om verdere acties uit te voeren. Als u ' intentie ' wilt gebruiken, moet u eerst een model trainen met behulp van [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Herkennings taal

Wanneer u gebruikt `SpeechRecognitionServiceFactory` om de-client te maken, moet u een taal selecteren. Zie [ondersteunde talen](../API-Reference-REST/supportedlanguages.md)voor de volledige lijst met talen die door de spraak service worden ondersteund.

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

U moet ook opgeven `SpeechRecognitionMode` wanneer u de-client maakt met: `SpeechRecognitionServiceFactory`

* `SpeechRecognitionMode_ShortPhrase`: Een utterance van Maxi maal 15 seconden lang. Wanneer gegevens naar de service worden verzonden, ontvangt de client meerdere gedeeltelijke resultaten en één eind resultaat met meerdere n-beste keuzes.
* `SpeechRecognitionMode_LongDictation`: Een utterance van Maxi maal twee minuten lang. Naarmate er gegevens naar de service worden verzonden, ontvangt de client meerdere gedeeltelijke resultaten en meerdere eind resultaten, op basis van de locatie van de server die zin pauzeert.

### <a name="attach-event-handlers"></a>Gebeurtenis-handlers koppelen

U kunt verschillende gebeurtenis-handlers koppelen aan de client die u hebt gemaakt:

* **Gedeeltelijke resultaten gebeurtenissen**: Deze gebeurtenis wordt aangeroepen telkens wanneer de speech-service voor spelt wat u kunt zeggen, zelfs voordat u met het spreken stopt (als `MicrophoneRecognitionClient`u gebruikt) of het verzenden van gegevens ( `DataRecognitionClient`als u gebruikt).
* **Fout gebeurtenissen**: Wordt aangeroepen wanneer er een fout wordt gedetecteerd door de service.
* **Intentie gebeurtenissen**: Wordt aangeroepen op ' WithIntent-clients (alleen in de ShortPhrase-modus) nadat het uiteindelijke herkennings resultaat is geparseerd in een Structured JSON-doel.
* **Resultaat gebeurtenissen**:
  * In `SpeechRecognitionMode_ShortPhrase` de modus wordt deze gebeurtenis aangeroepen en retourneert n-beste resultaten nadat u hebt gesp roken.
  * In `SpeechRecognitionMode_LongDictation` de modus wordt de gebeurtenis-handler meerdere keren aangeroepen, op basis van waar de service zin-pauzes aanduidt.
  * **Voor elk van de n-beste keuzes**wordt een betrouw bare waarde en enkele andere vormen van de herkende tekst geretourneerd. Zie [output format](../Concepts.md#output-format)(Engelstalig) voor meer informatie.

## <a name="related-topics"></a>Verwante onderwerpen

* [Naslag informatie voor de client bibliotheek voor iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Aan de slag met spraak herkenning van micro soft en/of intentie in Java op Android](GetStartedJavaAndroid.md)
* [Aan de slag met de micro soft Speech API in Java script](GetStartedJSWebsockets.md)
* [Aan de slag met de micro soft Speech API via REST](GetStartedREST.md)
