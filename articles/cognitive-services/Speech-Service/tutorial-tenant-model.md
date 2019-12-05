---
title: 'Een Tenant model maken (preview): spraak service'
titleSuffix: Azure Cognitive Services
description: Genereer automatisch een Tenant model (Custom Speech met Office 365-gegevens) dat gebruikmaakt van uw Office 365-gegevens om optimale spraak herkenning te bieden voor de specifieke voor waarden van de organisatie die zowel veilig als compatibel zijn.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 8ca31dcadebf2dc47d5a4b4db715f26fb38e204e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816378"
---
# <a name="create-a-tenant-model-preview"></a>Een Tenant model maken (preview)

Tenant model (Custom Speech met Office 365-gegevens) is een opt-in-service voor Office 365 Enter prise-klanten die automatisch een aangepast spraakherkennings model genereren op basis van de Office365-gegevens van uw organisatie. Het model dat wordt gemaakt, is geoptimaliseerd voor technische termen, jargon en namen van personen, allemaal op een veilige en compatibele manier.

> [!IMPORTANT]
> Als uw organisatie zich aanmeldt met een Tenant model, kan de spraak service toegang krijgen tot het taal model van uw organisatie, dat wordt gegenereerd op basis van e-mail berichten en documenten van Office 365 open bare groep die door iedereen in uw organisatie kan worden gezien. De Office 365-beheerder van uw organisatie kan het gebruik van het organisatiebrede taal model in-of uitschakelen met behulp van de Office 365-beheer Portal.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Inschrijven voor het gebruik van een Tenant model in het Microsoft 365-beheer centrum
> * Een spraak abonnement sleutel ophalen
> * Een Tenant model maken
> * Een Tenant model implementeren
> * Een Tenant model gebruiken met de Speech SDK

## <a name="enroll-using-the-microsoft-365-admin-center"></a>Inschrijven met behulp van het Microsoft 365-beheer centrum

Voordat u uw Tenant model kunt implementeren, moet u eerst inschrijven met behulp van het Microsoft 365-beheer centrum. Deze taak kan alleen worden uitgevoerd door uw Microsoft 365-beheerder.

1. Meld u aan bij het [Microsoft 365-beheer centrum](https://admin.microsoft.com ).
2. Selecteer in het linkerdeel venster **instellingen** en vervolgens **apps**.

   ![Registratie van Tenant model](media/tenant-language-model/tenant-language-model-enrollment.png)

3. Zoek en selecteer **Azure speech Services**.

   ![Inschrijving van Tenant model 2](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. Klik op het selectie vakje en sla het op.

Als u het Tenant model wilt uitschakelen, gaat u terug naar dit scherm, schakelt u het selectie vakje uit en slaat u op.

## <a name="get-a-speech-subscription-key"></a>Een spraak abonnement sleutel ophalen

Als u een Tenant model met de Speech SDK wilt gebruiken, hebt u een spraak bron en de bijbehorende abonnements sleutel nodig.

1. Meld u aan bij de [Azure Portal](https://aka.ms/azureportal).
2. Selecteer **Een resource maken**.
3. Typ in de zoek balk: **spraak**.
4. Selecteer **spraak**en klik vervolgens op **maken**.
5. Volg de instructies op het scherm om de resource te maken. Zorg ervoor dat:
   * De **locatie** is ingesteld op **Oost** -of **westus**.
   * **Prijs categorie** wordt ingesteld op **s0**.
6. Klik op **Maken**.
7. Na een paar minuten wordt uw resource gemaakt. De abonnements sleutel is beschikbaar in het gedeelte **overzicht** van uw resource.

## <a name="create-a-model"></a>Een model maken

Nadat uw beheerder Tenant model heeft ingeschakeld voor uw organisatie, kunt u een taal model maken op basis van uw Office365-gegevens.

1. Meld u aan bij de [Speech Studio](https://speech.microsoft.com/).
2. Klik in de rechter bovenhoek op het tandwiel pictogram (instellingen) en selecteer vervolgens **Tenant model instellingen**.

   ![Menu Instellingen](media/tenant-language-model/tenant-language-settings.png)

3. Hier ziet u een bericht met de melding dat u een Tenant model wilt maken.
   > [!NOTE]
   > Office 365 Enter prise-klanten in Noord-Amerika komen in aanmerking voor het maken van een Tenant model (Engels). Als u een Klanten-lockbox (CLB), klant sleutel (verzonken) of Office 365 Government-klant bent, is deze functie niet beschikbaar. Volg de volgende instructies om te bepalen of u een klant van Klanten-lockbox of klant bent:
   > * [Klanten-lockbox](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Klant sleutel](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)
   > * [Office 365 Government](https://www.microsoft.com/microsoft-365/government)

4. Selecteer vervolgens **opt in**. U ontvangt een e-mail met instructies wanneer uw Tenant model klaar is.

## <a name="deploy-your-model"></a>Uw model implementeren

Wanneer uw Tenant model klaar is, voert u de volgende stappen uit om uw model te implementeren:

1. Klik op de knop **model weer geven** in de bevestigings-e-mail die u hebt ontvangen, of Meld u aan bij de [Speech Studio](https://speech.microsoft.com/).
2. Klik in de rechter bovenhoek op het tandwiel pictogram (instellingen) en selecteer vervolgens **Tenant model instellingen**.

   ![Menu Instellingen](media/tenant-language-model/tenant-language-settings.png)

3. Klik op **Implementeren**.
4. Wanneer het model is geïmplementeerd, wordt de status gewijzigd in **geïmplementeerd**.

## <a name="use-your-model-with-the-speech-sdk"></a>Uw model gebruiken met de Speech SDK

Nu u het model hebt geïmplementeerd, kunt u het gebruiken met de spraak-SDK. In deze sectie gebruikt u de voor beeld-code voor het aanroepen van de spraak service met Azure AD-verificatie.

Laten we eens kijken naar de code die u gaat gebruiken om de Speech SDK C#aan te roepen. In dit voor beeld voert u spraak herkenning uit met behulp van een Tenant model. In deze hand leiding wordt ervan uitgegaan dat uw platform al is ingesteld. Als u hulp nodig hebt bij het instellen, raadpleegt u [Quick Start C# : Speech herkennen, (.net core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

Kopieer deze code naar uw project:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // Note: ServiceApplicationId is a fixed value.  No need to change.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Vervolgens moet u het project opnieuw bouwen en uitvoeren vanaf de opdracht regel. U moet een aantal para meters bijwerken voordat u de opdracht uitvoert.

1. Vervang `<Username>` en `<Password>` door de waarden voor een geldige Tenant gebruiker.
2. Vervang `<Subscription-Key>` door de abonnements sleutel voor uw spraak resource. Deze waarde is beschikbaar in het gedeelte **overzicht** voor uw spraak resource in de [Azure Portal](https://aka.ms/azureportal).
3. Vervang `<Endpoint-Uri>` door het onderstaande eind punt. Zorg ervoor dat u `{your-region}` vervangt door de regio waarin uw spraak bron is gemaakt. Deze regio's worden ondersteund: `westus`, `westus2`en `eastus`. Uw regio-informatie is beschikbaar in het gedeelte **overzicht** van uw spraak resource in de [Azure Portal](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. Voer de volgende opdracht uit:
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>Volgende stappen

* [Speech Studio](https://speech.microsoft.com/)
* [Speech-SDK](speech-sdk.md)
