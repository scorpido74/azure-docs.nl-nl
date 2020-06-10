---
title: Speaker Recognition-basis beginselen-spraak service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de Speech SDK voor het beantwoorden van de vraag ' wie is ' spreekt '. In dit artikel vindt u informatie over veelgebruikte ontwerp patronen voor het werken met zowel de verificatie van de spreker als de identificatie.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/05/2020
ms.author: trbye
ms.openlocfilehash: b8c38847e3228c54091b84218a5d72c79e3e41a8
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632315"
---
# <a name="learn-the-basics-of-speaker-recognition"></a>Meer informatie over de basis beginselen van Speaker Recognition

In dit artikel leert u basis patronen voor het ontwerpen van Speaker Recognition met behulp van de Speech SDK, met inbegrip van:

* Tekst afhankelijke en tekst onafhankelijke verificatie
* Identificatie van de spreker voor het identificeren van een spraak voorbeeld tussen een groep stemmen
* Spraak profielen verwijderen

Zie het artikel [overzicht](speaker-recognition-overview.md) voor een hoog niveau van de concepten van spraak herkenning.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een spraak service-abonnement hebt. Als u geen account en abonnement hebt, [kunt u de spraak service gratis uitproberen](get-started.md).

> [!IMPORTANT]
> Speaker Recognition wordt momenteel *alleen* ondersteund in azure-spraak bronnen die zijn gemaakt in de `westus` regio.

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Gebruik de volgende instructies, afhankelijk van uw platform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET core<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voor beelden in dit artikel wilt uitvoeren, moet u de volgende `using` instructies toevoegen boven aan het script.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Als u de spraak service wilt aanroepen met behulp van de Speech SDK, moet u een maken [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) . In dit voor beeld maakt u een [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) met een abonnements sleutel en-regio. U maakt ook een algemene standaard code voor gebruik voor de rest van dit artikel, die u voor verschillende aanpassingen wijzigt.

Houd er rekening mee dat de regio is ingesteld op `westus` , omdat dit de enige ondersteunde regio voor de service is.

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>Tekst afhankelijke verificatie

Speaker Verification wordt bevestigd dat een spreker overeenkomt met een bekende of **Inge schreven** stem. De eerste stap is het **Inschrijven** van een spraak profiel, zodat de service iets kan vergelijken met toekomstige spraak voorbeelden. In dit voor beeld moet u het profiel inschrijven met behulp van een strategie voor **tekst afhankelijke** , waarvoor een specifieke wachtwoordzin moet worden gebruikt voor inschrijving en verificatie. Zie de [naslag documentatie](https://docs.microsoft.com/rest/api/speakerrecognition/) voor een lijst met ondersteunde wacht woorden.

Maak eerst de volgende functie in uw `Program` klasse om een spraak profiel in te schrijven.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

In deze functie maakt u in `await client.CreateProfileAsync()` feite het nieuwe spraak profiel. Nadat deze is gemaakt, geeft u op hoe u audio voorbeelden wilt invoeren, `AudioConfig.FromDefaultMicrophoneInput()` in dit voor beeld om audio op te nemen van uw standaard invoer apparaat. Vervolgens registreert u audio-voor beelden in een `while` lus die het aantal voor beelden restert, en is vereist voor inschrijving. In elke iteratie `client.EnrollProfileAsync(profile, audioInput)` wordt u gevraagd om de wachtwoordzin te spreken in uw microfoon en het voor beeld toe te voegen aan het spraak profiel.

Nadat de registratie is voltooid, neemt u contact `await SpeakerVerify(config, profile, profileMapping)` op met het profiel dat u zojuist hebt gemaakt. Voeg een andere functie toe om te definiëren `SpeakerVerify` .

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

In deze functie geeft u het object op dat `VoiceProfile` u zojuist hebt gemaakt voor het initialiseren van een model om te verifiëren. Vervolgens `await speakerRecognizer.RecognizeOnceAsync(model)` wordt u gevraagd om de wachtwoordzin opnieuw te spreken, maar deze keer dat deze wordt gevalideerd voor uw spraak profiel en een vergelijk bare Score van 0,0 1.0 retourneert. Het `result` object retourneert ook `Accept` of `Reject` , afhankelijk van het feit of de wachtwoordzin overeenkomt.

Wijzig vervolgens de `Main()` functie om de nieuwe functies aan te roepen die u hebt gemaakt. Houd er bovendien rekening mee dat u een `Dictionary<string, string>` moet door geven met behulp van uw functie aanroepen. De reden hiervoor is dat de service geen door de mens lees bare naam mag opslaan met een gemaakt `VoiceProfile` en alleen een ID-nummer voor privacy-doel einden opslaat. In de `VerificationEnroll` functie voegt u aan deze woorden lijst een item toe met de zojuist gemaakte id, samen met een tekst naam. In scenario's voor het ontwikkelen van toepassingen waarbij u een lees bare naam moet kunnen weer geven, **moet u deze toewijzing ergens opslaan. de service kan deze niet opslaan**.

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

Voer het script uit en u wordt gevraagd om de woord groep *mijn stem te spreken, mijn Pass Port, ik* heb drie keer voor inschrijving en één extra tijd om te controleren. Het resultaat dat wordt geretourneerd, is de vergelijk bare Score, die u kunt gebruiken om uw eigen aangepaste drempel waarden voor verificatie te maken.

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>Tekst onafhankelijke verificatie

In tegens telling tot **tekst afhankelijke** verificatie, de **tekst onafhankelijke** verificatie:

* Vereist niet dat een bepaalde wachtwoordzin wordt gesp roken, wat kan worden gesp roken.
* Vereist geen drie audio voorbeelden *, maar er is wel 20* seconden aan audio van het totale aantal vereist

Maak een paar eenvoudige wijzigingen aan uw `VerificationEnroll` functie om over te scha kelen naar **tekst onafhankelijke** verificatie. Eerst wijzigt u het verificatie type in `VoiceProfileType.TextIndependentVerification` . Wijzig vervolgens de `while` lus in track `result.RemainingEnrollmentsSpeechLength` , waarna u wordt gevraagd om te spreken tot 20 seconden aan audio is vastgelegd.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Voer het programma opnieuw uit en spreek iets tijdens de verificatie fase uit omdat een wachtwoordzin niet is vereist. De score voor het soort gelijkeheid wordt geretourneerd.

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>Sprekeridentificatie

Speaker Identification wordt gebruikt om te bepalen **wie** van een bepaalde groep geregistreerde stemmen spreekt. Het proces is vergelijkbaar met **tekst onafhankelijke verificatie**, met het belangrijkste verschil dat tegelijkertijd kan worden gecontroleerd op meerdere spraak profielen, in plaats van te controleren op basis van één profiel.

Een functie maken `IdentificationEnroll` om meerdere spraak profielen in te schrijven. Het inschrijvings proces voor elk profiel is hetzelfde als het inschrijvings proces voor **tekst onafhankelijke verificatie**en vereist 20 seconden aan audio voor elk profiel. Deze functie accepteert een lijst met teken reeksen `profileNames` en maakt een nieuw spraak profiel voor elke naam in de lijst. De functie retourneert een lijst met `VoiceProfile` objecten, die u in de volgende functie gebruikt om een spreker te identificeren.

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

Maak de volgende functie `SpeakerIdentification` om een identificatie aanvraag in te dienen. Het belangrijkste verschil in deze functie vergeleken met een aanvraag voor een **luidspreker verificatie** is het gebruik van `SpeakerIdentificationModel.FromProfiles()` , waarmee een lijst met objecten wordt geaccepteerd `VoiceProfile` . 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similiar voice profile is {profileMapping[result.ProfileId]} with similiarity score {result.Score}");
}
```

Wijzig uw `Main()` functie in het volgende. U maakt een lijst met teken reeksen `profileNames` die u doorgeeft aan uw `IdentificationEnroll()` functie. Hiermee wordt u gevraagd om voor elke naam in deze lijst een nieuw spraak profiel te maken, zodat u meer namen kunt toevoegen om meer profielen voor vrienden of collega's te maken.

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

Voer het script uit en u wordt gevraagd om te praten met het registreren van spraak voorbeelden voor het eerste profiel. Nadat de registratie is voltooid, wordt u gevraagd dit proces te herhalen voor elke naam in de lijst `profileNames` . Nadat elke inschrijving is voltooid, wordt u gevraagd om **iedereen** te laten spreken. de service probeert deze persoon te identificeren uit uw Inge schreven spraak profielen.

In dit voor beeld wordt alleen het meest overeenkomende resultaat en de Score van het soort gelijke overeenkomst geretourneerd, maar u kunt het volledige antwoord krijgen met de vijf meest gelijkende scores door `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` aan uw functie toe te voegen `SpeakerIdentification` .

## <a name="changing-audio-input-type"></a>Type audio-invoer wijzigen

In de voor beelden in dit artikel wordt de standaard microfoon van het apparaat gebruikt als invoer voor audio voorbeelden. In scenario's waarin u echter audio bestanden moet gebruiken in plaats van de microfoon invoer, wijzigt u gewoon alle exemplaren van `AudioConfig.FromDefaultMicrophoneInput()` naar om `AudioConfig.FromWavFileInput(path/to/your/file.wav)` over te scha kelen naar een bestands invoer. U kunt ook gemengde invoer gebruiken met behulp van een microfoon voor inschrijving en bestanden voor verificatie, bijvoorbeeld.

## <a name="deleting-voice-profile-enrollments"></a>Inschrijvingen voor spraak profielen verwijderen

Als u een Inge schreven profiel wilt verwijderen, gebruikt u de `DeleteProfileAsync()` functie voor het `VoiceProfileClient` object. In de volgende voorbeeld functie ziet u hoe u een spraak Profiel van een bekende spraak profiel-ID verwijdert.

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie de documentatie van Speaker Recognition [referentie](https://docs.microsoft.com/rest/api/speakerrecognition/) voor meer informatie over klassen en functies.

* Zie [C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) -en [C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) -voor beelden op github.

