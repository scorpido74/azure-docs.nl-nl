---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: bb78a60b911823da96c52a104a3e06ecfc634da6
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210934"
---
In deze quickstart leert u basispatronen voor het ontwerp van Speaker Recognition met behulp van de Speech SDK, met inbegrip van:

* Tekstafhankelijke en tekstonafhankelijke verificatie
* Sprekeridentificatie voor het identificeren van een spraaksample in een groep stemmen
* Spraakprofielen verwijderen

Zie het artikel [Overzicht](../../../speaker-recognition-overview.md) voor een diepgaande bespreking van de concepten van spraakherkenning.

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [JavaScript-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Speaker Recognition wordt momenteel *alleen* ondersteund in Azure Speech-resources die in de regio `westus` zijn gemaakt.

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech-SDK installeren voor JavaScript<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Gebruik de volgende instructies, afhankelijk van uw platform:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbrowser <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Afhankelijk van de doelomgeving gebruikt u daarnaast een van de volgende opties:

# <a name="script"></a>[script](#tab/script)

Download en pak het <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech-SDK voor JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a>-bestand *microsoft.cognitiveservices.speech.sdk.bundle.js* uit en plaats het in een map die toegankelijk is voor uw HTML-bestand.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Als u een webbrowser als doel hebt en de tag `<script>` gebruikt, is het voorvoegsel `sdk` niet nodig. Het voorvoegsel `sdk` is een alias die wordt gebruikt om de `require`-module te benoemen.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Zie <a href="https://javascript.info/import-export" target="_blank">export en import<span class="docon docon-navigate-external x-hidden-focus"></span></a> voor meer informatie over `import`.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Zie <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Wat is require?<span class="docon docon-navigate-external x-hidden-focus"></span></a> voor meer informatie over `require`.

---

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voorbeelden in dit artikel wilt uitvoeren, voegt u boven aan het JS-bestand de volgende instructies toe.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

Met deze instructies worden de vereiste bibliotheken geïmporteerd, en worden uw abonnementssleutel en -regio voor de Speech-service opgehaald uit de omgevingsvariabelen. Ook worden paden naar audiobestanden opgeven die u gebruikt in de volgende taken.

## <a name="create-helper-function"></a>Hulpfuncties maken

Voeg de volgende hulpfuncties toe om audiobestanden te lezen voor gebruik met de Speech-service.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

In deze functie gebruikt u de methoden [AudioInputStream.createPushStream](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream?view=azure-node-latest&preserve-view=true#createpushstream-audiostreamformat-) en [AudioConfig.fromStreamInput](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) om een object [AudioConfig](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) te maken. Dit object `AudioConfig` vertegenwoordigt een audiostream. U gebruikt verschillende `AudioConfig`-objecten gedurende de volgende taken.

## <a name="text-dependent-verification"></a>Tekstafhankelijke verificatie

Met Speaker Verification wordt bevestigd dat een spreker overeenkomt met een bekende of **geregistreerde** stem. De eerste stap bestaat uit het **registreren** van een spraakprofiel, zodat dit met latere spraaksamples kan worden vergeleken. In dit voorbeeld moet u het profiel registreren door middel van een **tekstafhankelijke** strategie, waarvoor een specifieke wachtwoordzin is vereist voor zowel de registratie als de verificatie. Zie het [referentiemateriaal ](https://docs.microsoft.com/rest/api/speakerrecognition/) voor een lijst met ondersteunde wachtwoordzinnen.

### <a name="textdependentverification-function"></a>De functie TextDependentVerification

Begin door de functie `TextDependentVerification` te maken.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

Met deze functie wordt een object [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) gemaakt met de methode [VoiceProfileClient.createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-). Houd er rekening mee dat er drie [typen](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype?view=azure-node-latest&preserve-view=true) `VoiceProfile` zijn:

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

In dit geval geeft u `VoiceProfileType.TextDependentVerification` door aan `VoiceProfileClient.createProfileAsync`.

Vervolgens roept u twee hulpfuncties aan die u hierna definieert, `AddEnrollmentsToTextDependentProfile` en `SpeakerVerify`. Roep ten slotte [VoiceProfileClient.deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) aan om het profiel te verwijderen.

### <a name="addenrollmentstotextdependentprofile-function"></a>De functie AddEnrollmentsToTextDependentProfile

Definieer de volgende functie om een spraakprofiel in te schrijven.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

In deze functie roept u de functie `GetAudioConfigFromFile` aan die u eerder hebt gedefinieerd, om `AudioConfig`-objecten te maken uit audiobestanden. Deze audiovoorbeelden bevatten een wachtwoordzin zoals: Mijn stem is mijn paspoort, verifieer mij. U kunt deze audiovoorbeelden vervolgens inschrijven met behulp van de methode [VoiceProfileClient.enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-).

### <a name="speakerverify-function"></a>De functie SpeakerVerify

Definieer `SpeakerVerify` als volgt.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

In deze functie maakt u een object [SpeakerVerificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest&preserve-view=true) met de methode [SpeakerVerificationModel.FromProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest&preserve-view=true#fromprofile-voiceprofile-), waarbij u het object [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) doorgeeft dat u eerder hebt gemaakt.

Vervolgens roept u de methode [SpeechRecognizer.recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) aan om een audiovoorbeeld te valideren dat dezelfde wachtwoordzin bevat als de audiovoorbeelden die u eerder hebt ingeschreven. `SpeechRecognizer.recognizeOnceAsync` retourneert een object [SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest&preserve-view=true), waarvan eigenschap `score` een vergelijkbare score bevat, tussen 0.0-1.0. Het object `SpeakerRecognitionResult` bevat ook een eigenschap `reason` van het type [ResultReason](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason?view=azure-node-latest&preserve-view=true). Als de verificatie is geslaagd, moet de eigenschap `reason` de waarde `RecognizedSpeaker` hebben.

## <a name="text-independent-verification"></a>Tekstonafhankelijke verificatie

In tegenstelling tot **tekstafhankelijke** verificatie, geldt voor **tekstonafhankelijke** verificatie:

* Vereist niet dat een bepaalde wachtwoordzin wordt uitgesproken. Alles mag worden uitgesproken
* Vereist niet drie audiovoorbeelden, maar vereist in totaal *wel* twintig seconden aan audio

### <a name="textindependentverification-function"></a>De functie TextIndependentVerification

Begin door de functie `TextIndependentVerification` te maken.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

Net zoals met de functie `TextDependentVerification` wordt met deze functie een object [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) gemaakt met de methode [VoiceProfileClient.createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-).

In dit geval geeft u `VoiceProfileType.TextIndependentVerification` door aan `createProfileAsync`.

Vervolgens roept u twee hulpfuncties aan: `AddEnrollmentsToTextIndependentProfile`, die u hierna definieert, en `SpeakerVerify`, die u al hebt gedefinieerd. Roep ten slotte [VoiceProfileClient.deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) aan om het profiel te verwijderen.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Definieer de volgende functie om een spraakprofiel in te schrijven.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

In deze functie roept u de functie `GetAudioConfigFromFile` aan die u eerder hebt gedefinieerd, om `AudioConfig`-objecten te maken uit audiobestanden. U kunt deze audiovoorbeelden vervolgens inschrijven met behulp van de methode [VoiceProfileClient.enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-).

## <a name="speaker-identification"></a>Sprekeridentificatie

Speaker Identification wordt gebruikt om vast te stellen **wie** er spreekt uit een bepaalde groep geregistreerde stemmen. Het proces is vergelijkbaar met **tekstonafhankelijke verificatie**, waarbij het belangrijkste verschil is dat tegelijkertijd verificatie via meerdere spraakprofielen kan worden uitgevoerd in plaats van te verifiëren via één profiel.

### <a name="textindependentidentification-function"></a>De functie TextIndependentIdentification

Begin door de functie `TextIndependentIdentification` te maken.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

Net zoals met de functies `TextDependentVerification` en `TextIndependentVerification` wordt met deze functie een object [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) gemaakt met de methode [VoiceProfileClient.createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-).

In dit geval geeft u `VoiceProfileType.TextIndependentIdentification` door aan `VoiceProfileClient.createProfileAsync`.

Vervolgens roept u twee hulpfuncties aan: `AddEnrollmentsToTextIndependentProfile`, die u al hebt gedefinieerd, en `SpeakerIdentify`, die u hierna definieert. Roep ten slotte [VoiceProfileClient.deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) aan om het profiel te verwijderen.

### <a name="speakeridentify-function"></a>De functie SpeakerIdentify

Definieer de functie `SpeakerIdentify` als volgt.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

In deze functie maakt u een [SpeakerIdentificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest&preserve-view=true)-object met de methode [SpeakerIdentificationModel.fromProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest&preserve-view=true#fromprofiles-voiceprofile---), waarbij u het object [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) doorgeeft dat u eerder hebt gemaakt.

Vervolgens roept u de methode [SpeechRecognizer.recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) aan en geeft u een audiovoorbeeld door.
Met `SpeechRecognizer.recognizeOnceAsync` wordt geprobeerd om de spraak voor dit audiovoorbeeld te identificeren op basis van de `VoiceProfile`-objecten die u hebt gebruikt om de `SpeakerIdentificationModel` te maken. Het object [SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest&preserve-view=true) wordt geretourneerd, waarvan de eigenschap `profileId` de overeenkomende `VoiceProfile` identificeert, indien aanwezig, terwijl de eigenschap `score` een vergelijkbare score bevat, tussen 0.0-1.0.

## <a name="main-function"></a>Hoofdfunctie

Definieer tenslotte de functie `main` als volgt.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

Met deze functie wordt het object [VoiceProfileClient](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true) gemaakt, dat wordt gebruikt om spraakprofielen te maken, in te schrijven en te verwijderen. Vervolgens worden de functies aangeroepen die u eerder hebt gedefinieerd.
