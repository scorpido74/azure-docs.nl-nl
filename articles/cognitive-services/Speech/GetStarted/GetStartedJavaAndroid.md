---
title: Aan de slag met de micro soft Speech Recognition API in java in Android | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Gebruik de micro soft Speech API om Android-toepassingen te ontwikkelen die gesp roken audio naar tekst converteren.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 77fee2ecee9cfabe3fad9c1df2c41c7803c3367e
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966832"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Quickstart: De Bing Speech Recognition API in java in Android gebruiken

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Met de Bing Speech Recognition API kunt u Android-toepassingen ontwikkelen die gebruikmaken van de Cloud Bing Speech Service om gesp roken audio naar tekst te converteren. De API ondersteunt realtime streaming, zodat uw toepassing tegelijkertijd gedeeltelijke herkennings resultaten kan ontvangen op het moment dat de audio naar de service wordt verzonden.

In dit artikel wordt een voorbeeld toepassing gebruikt om te laten zien hoe u de spraakclientbibliotheek voor Android kunt gebruiken om spraak-naar-tekst toepassingen te ontwikkelen in Java voor Android-apparaten.

## <a name="prerequisites"></a>Vereisten

### <a name="platform-requirements"></a>Platformvereisten

Het voor beeld is ontwikkeld door [Android Studio](https://developer.android.com/sdk/index.html) voor Windows in Java.

### <a name="get-the-client-library-and-sample-application"></a>De client bibliotheek en voorbeeld toepassing ophalen

De spraakclientbibliotheek en voor beelden voor Android zijn beschikbaar in de [Speech client SDK voor Android](https://github.com/microsoft/cognitive-speech-stt-android). U vindt het voor beeld dat u kunt bouwen in de map Samples/SpeechRecoExample. U kunt de twee bibliotheken vinden die u nodig hebt om te gebruiken in uw eigen apps in SpeechSDK/bibliotheken in de map armeabi en x86. De grootte van het libandroid_platform. het bestand is dus 22 MB, maar is beperkt tot 4 MB tijdens de implementatie.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Abonneer u op de spraak-API en ontvang een gratis proef abonnement sleutel

De Speech-API maakt deel uit van Cognitive Services (voorheen project Oxford). U kunt abonnements sleutels voor een gratis proef versie downloaden van de pagina [Cognitive Services abonnement](https://azure.microsoft.com/try/cognitive-services/) . Nadat u de spraak-API hebt geselecteerd, selecteert u **API-sleutel ophalen** om de sleutel op te halen. Er wordt een primaire en secundaire sleutel geretourneerd. Beide sleutels zijn gekoppeld aan hetzelfde quotum, dus u kunt beide sleutels gebruiken.

Als u *herkenning met opzet*wilt gebruiken, moet u zich ook registreren voor de [Language Understanding intelligent service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Een abonnements sleutel ophalen. Voordat u Speech client-bibliotheken kunt gebruiken, moet u een [abonnements sleutel](https://azure.microsoft.com/try/cognitive-services/)hebben.
>
>* Gebruik uw abonnements sleutel. Werk met de meegeleverde Android-voorbeeld toepassing de bestands voorbeelden/SpeechRecoExample/res/values/strings. XML bij met uw abonnements sleutels. Zie voor [beelden bouwen en uitvoeren](#build-and-run-samples)voor meer informatie.

## <a name="use-the-speech-client-library"></a>De spraakclientbibliotheek gebruiken

Volg de [instructies](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library)om de client bibliotheek in uw toepassing te gebruiken.

De naslag informatie voor de client bibliotheek voor Android vindt u in de map docs van de [Speech client SDK voor Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Voor beelden bouwen en uitvoeren

Zie deze [Leesmij-pagina](https://github.com/microsoft/cognitive-speech-stt-android#the-sample)voor meer informatie over het maken en uitvoeren van voor beelden.

## <a name="samples-explained"></a>Voor beelden beschreven

### <a name="create-recognition-clients"></a>Herkennings-clients maken

De code in het volgende voor beeld laat zien hoe u herkennings-client klassen maakt op basis van gebruikers scenario's:

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

De client bibliotheek biedt vooraf geïmplementeerde client klassen voor herkenning voor typische scenario's in spraak herkenning:

* `DataRecognitionClient`: Spraak herkenning met PCM-gegevens (bijvoorbeeld van een bestand of audio bron). De gegevens worden opgedeeld in buffers en elke buffer wordt naar de speech-service verzonden. Er worden geen wijzigingen aangebracht in de buffers, zodat de gebruiker desgewenst een eigen stilte detectie kan Toep assen. Als de gegevens afkomstig zijn uit WAV-bestanden, kunt u gegevens van het bestand naar de speech-service verzenden. Als u onbewerkte gegevens hebt, bijvoorbeeld audio die via Bluetooth komt, verzendt u eerst een indelings header naar de spraak service, gevolgd door de gegevens.
* `MicrophoneRecognitionClient`: Spraak herkenning met audio afkomstig van de microfoon. Zorg ervoor dat de microfoon is ingeschakeld en dat de gegevens van de microfoon worden verzonden naar de service voor spraak herkenning. Er wordt een ingebouwde "stilte detector" toegepast op de microfoon gegevens voordat deze naar de herkennings service worden verzonden.
* `DataRecognitionClientWithIntent` en `MicrophoneRecognitionClientWithIntent`: Deze clients retour neren, naast de herkennings tekst, gestructureerde informatie over de bedoeling van de spreker, die kan worden gebruikt om verdere acties door uw toepassingen uit te voeren. Als u ' intentie ' wilt gebruiken, moet u eerst een model trainen met behulp van [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Herkennings taal

Wanneer u gebruikt `SpeechRecognitionServiceFactory` om de-client te maken, moet u een taal selecteren. Zie [ondersteunde talen](../API-Reference-REST/supportedlanguages.md)voor de volledige lijst met talen die door de spraak service worden ondersteund.

### `SpeechRecognitionMode`

U moet ook opgeven `SpeechRecognitionMode` wanneer u de-client maakt met: `SpeechRecognitionServiceFactory`

* `ShortPhrase`: Een utterance van Maxi maal 15 seconden lang. Wanneer gegevens naar de service worden verzonden, ontvangt de client meerdere gedeeltelijke resultaten en één eind resultaat met meerdere n-beste keuzes.
* `LongDictation`: Een utterance van Maxi maal twee minuten lang. Naarmate er gegevens naar de service worden verzonden, ontvangt de client meerdere gedeeltelijke resultaten en meerdere eind resultaten, op basis van waar de service zinnen van zin aanduidt.

### <a name="attach-event-handlers"></a>Gebeurtenis-handlers koppelen

U kunt verschillende gebeurtenis-handlers koppelen aan de client die u hebt gemaakt:

* **Gedeeltelijke resultaten gebeurtenissen**: Deze gebeurtenis wordt elke keer dat de spraak service wordt opgeroepen, voor speld wat u kunt zeggen, zelfs voordat u klaar bent met `MicrophoneRecognitionClient`het spreken (als u gebruikt) of het `DataRecognitionClient`verzenden van gegevens (als u gebruikt).
* **Fout gebeurtenissen**: Wordt aangeroepen wanneer er een fout wordt gedetecteerd door de service.
* **Intentie gebeurtenissen**: Aangeroepen op ' WithIntent-clients (alleen in `ShortPhrase` de modus) nadat het uiteindelijke herkennings resultaat is geparseerd in een Structured JSON-doel.
* **Resultaat gebeurtenissen**:
  * In `ShortPhrase` de modus wordt deze gebeurtenis aangeroepen en retourneert n-beste resultaten nadat u hebt gesp roken.
  * In `LongDictation` de modus wordt de gebeurtenis-handler meerdere keren aangeroepen, op basis van waar de service zin-pauzes aanduidt.
  * **Voor elk van de n-beste keuzes**wordt een betrouw bare waarde en enkele andere vormen van de herkende tekst geretourneerd. Zie [output format](../Concepts.md#output-format)(Engelstalig) voor meer informatie.

## <a name="related-topics"></a>Verwante onderwerpen

* [Naslag informatie voor de client bibliotheek voor Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Aan de slag met de micro soft speech C# API in voor Windows in .net](GetStartedCSharpDesktop.md)
* [Aan de slag met de micro soft Speech API in doelstelling-C op iOS](Get-Started-ObjectiveC-iOS.md)
* [Aan de slag met de micro soft Speech API in Java script](GetStartedJSWebsockets.md)
* [Aan de slag met de micro soft Speech API via REST](GetStartedREST.md)
