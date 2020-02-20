---
title: 'Een Tenant model maken (preview): spraak service'
titleSuffix: Azure Cognitive Services
description: Genereer automatisch een beveiligd, compatibel Tenant model (Custom Speech met Office 365-gegevens) dat uw Office 365-gegevens gebruikt voor het leveren van optimale spraak herkenning voor specifieke voor waarden van de organisatie.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: a83ed5c9cec994c1bc4cadd5cf6208c159823658
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77469014"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Zelf studie: een Tenant model maken (preview)

Tenant model (Custom Speech met Office 365-gegevens) is een opt-in-service voor Office 365 Enter prise-klanten die automatisch een aangepast spraakherkennings model genereren op basis van de Office 365-gegevens van uw organisatie. Het model is geoptimaliseerd voor technische termen, jargon en namen van personen, allemaal op een veilige en compatibele manier.

> [!IMPORTANT]
> Als uw organisatie zich registreert met behulp van de Tenant model-service, kan de spraak service toegang krijgen tot het taal model van uw organisatie. Het model wordt gegenereerd op basis van e-mail berichten en documenten van Office 365 open bare groep, die kunnen worden gezien door iedereen in uw organisatie. De Office 365-beheerder van uw organisatie kan het gebruik van het organisatiebrede taal model in-of uitschakelen vanuit de Office 365-beheer Portal.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Registreren in het Tenant model met behulp van het Microsoft 365-beheer centrum
> * Een spraak abonnement sleutel ophalen
> * Een Tenant model maken
> * Een Tenant model implementeren
> * Uw Tenant model gebruiken met de Speech SDK

## <a name="enroll-in-the-tenant-model-service"></a>Registreren in de Tenant model service

Voordat u uw Tenant model kunt implementeren, moet u worden inge schreven bij de service Tenant model. De inschrijving is voltooid in het beheer centrum van Microsoft 365 en kan alleen worden uitgevoerd door uw Microsoft 365-beheerder.

1. Meld u aan bij het [Microsoft 365-beheer centrum](https://admin.microsoft.com).

1. Selecteer in het linkerdeel venster **instellingen**, selecteer **instellingen** in het geneste menu en selecteer vervolgens **Azure speech Services** in het hoofd venster.

   ![Het deel venster Services &-invoeg toepassingen](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Schakel het selectie vakje **het taal model voor de hele organisatie toestaan** in en selecteer vervolgens **wijzigingen opslaan**.

   ![Het deel venster spraak services van Azure](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Het Tenant model exemplaar uitschakelen:
1. Herhaal de voor gaande stappen 1 en 2.
1. Schakel het selectie vakje **het taal model voor de hele organisatie toestaan** uit en selecteer vervolgens **wijzigingen opslaan**.

## <a name="get-a-speech-subscription-key"></a>Een spraak abonnement sleutel ophalen

Als u uw Tenant model wilt gebruiken met de Speech SDK, hebt u een spraak bron en de bijbehorende abonnements sleutel nodig.

1. Meld u aan bij de [Azure-portal](https://aka.ms/azureportal).
1. Selecteer **Een resource maken**.
1. Typ in het **zoekvak** **spraak**.
1. Selecteer in de lijst met resultaten **spraak**en selecteer vervolgens **maken**.
1. Volg de instructies op het scherm om de resource te maken. Zorg ervoor dat:
   * De **locatie** is ingesteld op **Oost** -of **westus**.
   * **Prijs categorie** wordt ingesteld op **s0**.
1. Selecteer **Maken**.

   Na een paar minuten wordt uw resource gemaakt. De abonnements sleutel is beschikbaar in het gedeelte **overzicht** van uw resource.

## <a name="create-a-language-model"></a>Een taal model maken

Nadat de beheerder Tenant model heeft ingeschakeld voor uw organisatie, kunt u een taal model maken dat is gebaseerd op uw Office 365-gegevens.

1. Meld u aan bij [Speech Studio](https://speech.microsoft.com/).
1. Selecteer in de rechter bovenhoek de optie **instellingen** (tandwiel pictogram) en selecteer vervolgens **Tenant model instellingen**.

   ![De koppeling Tenant model instellingen](media/tenant-language-model/tenant-language-settings.png)

   In speech Studio wordt een bericht weer gegeven waarin u kunt zien of u een Tenant model wilt maken.

   > [!NOTE]
   > Office 365 Enter prise-klanten in Noord-Amerika komen in aanmerking voor het maken van een Tenant model (Engels). Als u een Klanten-lockbox, klant sleutel of Office 365 Government-klant bent, is deze functie niet beschikbaar. Ga als volgt te werk om te bepalen of u een klant van Klanten-lockbox of klant code bent?
   > * [Klanten-lockbox](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Klant sleutel](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365 Government](https://www.microsoft.com/microsoft-365/government)

1. Selecteer **Aanmelden**.

   Wanneer uw Tenant model klaar is, ontvangt u een bevestigings e-mail bericht met verdere instructies.

## <a name="deploy-your-tenant-model"></a>Uw Tenant model implementeren

Wanneer uw Tenant model exemplaar gereed is, implementeert u dit door het volgende te doen:

1. Selecteer in uw bevestigings e-mail bericht de knop **model weer geven** . Of Meld u aan bij [Speech Studio](https://speech.microsoft.com/).
1. Selecteer in de rechter bovenhoek de optie **instellingen** (tandwiel pictogram) en selecteer vervolgens **Tenant model instellingen**.

   ![De koppeling Tenant model instellingen](media/tenant-language-model/tenant-language-settings.png)

1. Selecteer **Implementeren**.

   Wanneer het model is geïmplementeerd, wordt de status gewijzigd in *geïmplementeerd*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Uw Tenant model gebruiken met de Speech SDK

Nu u het model hebt geïmplementeerd, kunt u het gebruiken met de spraak-SDK. In deze sectie gebruikt u voorbeeld code om de spraak service aan te roepen met behulp van Azure Active Directory-verificatie (Azure AD).

Laten we eens kijken naar de code die u gaat gebruiken om de Speech SDK C#aan te roepen. In dit voor beeld voert u spraak herkenning uit met behulp van uw Tenant model. In deze hand leiding wordt ervan uitgegaan dat uw platform al is ingesteld. Als u hulp nodig hebt bij de installatie, raadpleegt u [Quick Start: Speech herkennen, C# (.net core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

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

    // ServiceApplicationId is a fixed value. No need to change it.

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

Vervolgens moet u het project opnieuw opbouwen en uitvoeren vanaf de opdracht regel. Voordat u de opdracht uitvoert, moet u een aantal para meters bijwerken door het volgende te doen:

1. Vervang `<Username>` en `<Password>` door de waarden voor een geldige Tenant gebruiker.
1. Vervang `<Subscription-Key>` door de abonnements sleutel voor uw spraak resource. Deze waarde is beschikbaar in het gedeelte **overzicht** voor uw spraak resource in de [Azure Portal](https://aka.ms/azureportal).
1. Vervang `<Endpoint-Uri>` door het volgende eind punt. Zorg ervoor dat u `{your region}` vervangt door de regio waarin uw spraak bron is gemaakt. Deze regio's worden ondersteund: `westus`, `westus2`en `eastus`. Uw regio gegevens zijn beschikbaar in de sectie **overzicht** van uw spraak resource in de [Azure Portal](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Voer de volgende opdracht uit:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

In deze zelf studie hebt u geleerd hoe u Office 365-gegevens kunt gebruiken om een aangepast spraakherkennings model te maken, dit te implementeren en te gebruiken met de spraak-SDK.

## <a name="next-steps"></a>Volgende stappen

* [Speech Studio](https://speech.microsoft.com/)
* [Speech-SDK](speech-sdk.md)
