---
title: Een tenantmodel maken (voorbeeld) - Spraakservice
titleSuffix: Azure Cognitive Services
description: Genereer automatisch een veilig, compatibel tenantmodel (Aangepaste spraak met Office 365-gegevens) dat uw Office 365-gegevens gebruikt om optimale spraakherkenning te bieden voor organisatiespecifieke termen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: a83ed5c9cec994c1bc4cadd5cf6208c159823658
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469014"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Zelfstudie: Een tenantmodel maken (voorbeeld)

Tenantmodel (Aangepaste spraak met Office 365-gegevens) is een opt-in-service voor zakelijke Office 365-klanten die automatisch een aangepast spraakherkenningsmodel genereert op basis van de Office 365-gegevens van uw organisatie. Het model is geoptimaliseerd voor technische termen, jargon en namen van mensen, allemaal op een veilige en conforme manier.

> [!IMPORTANT]
> Als uw organisatie zich inschrijft met behulp van de tenantmodelservice, heeft Spraakservice toegang tot het taalmodel van uw organisatie. Het model wordt gegenereerd uit openbare e-mails en documenten van openbare office 365-groepen, die door iedereen in uw organisatie kunnen worden gezien. De Office 365-beheerder van uw organisatie kan het gebruik van het taalmodel voor de hele organisatie in- of uitschakelen via de Office 365-beheerportal.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Inschrijven voor het tenantmodel met het Microsoft 365-beheercentrum
> * Een spraakabonnementssleutel aanschaffen
> * Een tenantmodel maken
> * Een tenantmodel implementeren
> * Gebruik uw tenantmodel met de SpraakSDK

## <a name="enroll-in-the-tenant-model-service"></a>Inschrijven voor de tenantmodelservice

Voordat u uw tenantmodel implementeren, moet u zijn ingeschreven in de tenantmodelservice. De inschrijving is voltooid in het Microsoft 365-beheercentrum en kan alleen worden gedaan door uw Microsoft 365-beheerder.

1. Meld u aan bij het [Microsoft 365-beheercentrum](https://admin.microsoft.com).

1. Selecteer in het linkerdeelvenster **Instellingen**en selecteer **Vervolgens Instellingen** in het geneste menu en selecteer vervolgens Azure Speech **Services** in het hoofdvenster.

   ![Het deelvenster 'Services &-invoegtoepassing'](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Schakel het selectievakje **Het taalmodel voor de hele organisatie toestaan** in en selecteer Wijzigingen **opslaan**.

   ![Het deelvenster Azure Speech Services](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Ga als u de instantie tenantmodel uitschakelt:
1. Herhaal de voorgaande stappen 1 en 2.
1. Schakel het selectievakje **Het taalmodel voor de hele organisatie toestaan** uit en selecteer Wijzigingen **opslaan**.

## <a name="get-a-speech-subscription-key"></a>Een spraakabonnementssleutel aanschaffen

Als u uw tenantmodel wilt gebruiken met de Spraak-SDK, hebt u een Spraakbron en de bijbehorende abonnementssleutel nodig.

1. Meld u aan bij [Azure Portal](https://aka.ms/azureportal).
1. Selecteer **Een resource maken**.
1. Typ **Spraak**in het vak **Zoeken** .
1. Selecteer **Spraak**in de lijst met resultaten en selecteer **Vervolgens Maken**.
1. Volg de instructies op het scherm om uw resource te maken. Vereisten:
   * **Locatie** is ingesteld op **ofwel eastus** of **westus**.
   * **De prijscategorie** is ingesteld op **S0**.
1. Selecteer **Maken**.

   Na een paar minuten wordt uw resource gemaakt. De abonnementssleutel is beschikbaar in de sectie **Overzicht** voor uw resource.

## <a name="create-a-language-model"></a>Een taalmodel maken

Nadat uw beheerder TenantModel voor uw organisatie heeft ingeschakeld, u een taalmodel maken dat is gebaseerd op uw Office 365-gegevens.

1. Meld u aan bij [Speech Studio](https://speech.microsoft.com/).
1. Selecteer rechtsboven **Instellingen** (tandwielpictogram) en selecteer **tenantmodelinstellingen**.

   ![De koppeling 'Tenantmodel-instellingen'](media/tenant-language-model/tenant-language-settings.png)

   Speech Studio toont een bericht waarin u weet of u gekwalificeerd bent om een tenantmodel te maken.

   > [!NOTE]
   > Zakelijke Office 365-klanten in Noord-Amerika komen in aanmerking voor het maken van een tenantmodel (Engels). Als u een klantvergrendelingsbox, klantsleutel of klant van de overheid van Office 365 bent, is deze functie niet beschikbaar. Zie het belangrijkste om te bepalen of u klantvergrendeling of klantsleutelklant bent:
   > * [Klanten-lockbox](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Klantsleutel](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365-regering](https://www.microsoft.com/microsoft-365/government)

1. Selecteer **Aanmelden**.

   Wanneer uw tenantmodel klaar is, ontvangt u een bevestigingsbericht met verdere instructies.

## <a name="deploy-your-tenant-model"></a>Uw tenantmodel implementeren

Wanneer de instantie van uw tenantmodel gereed is, implementeert u deze als volgt:

1. Selecteer in uw bevestigingsbericht de knop **Model weergeven.** Of meld je aan bij [Speech Studio](https://speech.microsoft.com/).
1. Selecteer rechtsboven **Instellingen** (tandwielpictogram) en selecteer **tenantmodelinstellingen**.

   ![De koppeling 'Tenantmodel-instellingen'](media/tenant-language-model/tenant-language-settings.png)

1. Selecteer **Implementeren**.

   Wanneer uw model is geïmplementeerd, wordt de status *gewijzigd in Geïmplementeerd.*

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Gebruik uw tenantmodel met de SpraakSDK

Nu u uw model hebt geïmplementeerd, u het gebruiken met de Speech SDK. In deze sectie gebruikt u voorbeeldcode om spraakservice aan te roepen met Azure AD-verificatie (Azure AD).

Laten we eens kijken naar de code die u gebruikt om de Speech SDK in C#te bellen. In dit voorbeeld voert u spraakherkenning uit met behulp van uw tenantmodel. Deze handleiding gaat ervan uit dat uw platform al is ingesteld. Zie [Quickstart: Spraak herkennen, C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)als u installatiehulp nodig hebt.

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

Vervolgens moet u het project opnieuw opbouwen en uitvoeren vanaf de opdrachtregel. Voordat u de opdracht uitvoert, werkt u een aantal parameters bij door het volgende te doen:

1. Vervang `<Username>` `<Password>` en met de waarden voor een geldige tenantgebruiker.
1. Vervang `<Subscription-Key>` de abonnementssleutel voor uw spraakbron. Deze waarde is beschikbaar in de sectie **Overzicht** voor uw spraakbron in de [Azure-portal.](https://aka.ms/azureportal)
1. Vervang `<Endpoint-Uri>` door het volgende eindpunt. Zorg ervoor dat `{your region}` u de regio vervangt waar uw spraakbron is gemaakt. Deze regio's `westus`worden `westus2`ondersteund: , , en `eastus`. Uw regiogegevens zijn beschikbaar in het gedeelte **Overzicht** van uw spraakbron in de [Azure-portal.](https://aka.ms/azureportal)
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Voer de volgende opdracht uit:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

In deze zelfstudie hebt u geleerd hoe u Office 365-gegevens gebruiken om een aangepast spraakherkenningsmodel te maken, deze te implementeren en te gebruiken met de Speech SDK.

## <a name="next-steps"></a>Volgende stappen

* [Spraakstudio](https://speech.microsoft.com/)
* [Speech-SDK](speech-sdk.md)
