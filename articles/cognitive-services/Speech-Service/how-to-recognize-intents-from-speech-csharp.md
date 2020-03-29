---
title: 'Intents herkennen van spraak met de SpraakSDK C #'
titleSuffix: Azure Cognitive Services
description: In deze handleiding leert u hoe u intents uit spraak herkent met behulp van de SpraakSDK voor C#.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 5d3c77c307739f9014010a592aa496a1cc83b333
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77120037"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Intents herkennen van spraak met de SpraakSDK voor C #

De Cognitive Services [Speech SDK](speech-sdk.md) integreert met de Language Understanding [service (LUIS)](https://www.luis.ai/home) om **intentieherkenning**te bieden. Een intentie is iets dat de gebruiker wil doen: een vlucht reserveren, de weersverwachting controleren of iemand bellen. De gebruiker kan elke term gebruiken die natuurlijk aanvoelt. Met behulp van machine learning brengt LUIS gebruikersverzoeken in kaart met de intenties die u hebt gedefinieerd.

> [!NOTE]
> Een LUIS-toepassing definieert de intenties en entiteiten die u wilt herkennen. Deze toepassing staat los van de C#-toepassing die gebruikmaakt van de Speech-service. In dit artikel verwijst 'app' naar de LUIS-app en 'toepassing' naar de C#-code.

In deze handleiding gebruikt u de Spraak-SDK om een C#-consoletoepassing te ontwikkelen die intents ontleent aan uitingen van gebruikers via de microfoon van uw apparaat. U leert het volgende:

> [!div class="checklist"]
>
> - Een Visual Studio-project maken dat verwijst naar het NuGet-pakket van de Speech SDK
> - Een spraakconfiguratie maken en een intent-herkenningkrijgen
> - Het model voor uw LUIS-app ophalen en de benodigde intenties toevoegen
> - De taal voor spraakherkenning opgeven
> - Spraak herkennen uit een bestand
> - Asynchrone, gebeurtenisgestuurde continue herkenning gebruiken

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat je de volgende items hebt voordat je met deze handleiding begint:

- Een LUIS-account. U kunt een gratis account aanvragen via het [LUIS portal](https://www.luis.ai/home).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (elke editie).

## <a name="luis-and-speech"></a>LUIS en spraakherkenning

LUIS kan worden geïntegreerd met de Speech-service voor het herkennen van intenties van spraak. U hebt geen abonnement op de Speech-service nodig, alleen op LUIS.

LUIS gebruikt drie soorten sleutels:

| Type sleutel  | Doel                                               |
| --------- | ----------------------------------------------------- |
| Ontwerpen | Hiermee u LUIS-apps programmatisch maken en wijzigen |
| Starter   | Hiermee u uw LUIS-toepassing testen met alleen tekst   |
| Eindpunt  | Geeft toegang tot een bepaalde LUIS-app een vergunning            |

Voor deze handleiding hebt u het type eindpuntsleutel nodig. Deze handleiding maakt gebruik van het voorbeeld Luis-app voor domotica, die u maken door de [vooraf gebouwde app Voor domotica](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) gebruiken snel te volgen. Als u zelf een LUIS-app hebt gemaakt, u deze gebruiken.

Wanneer u een LUIS-app maakt, genereert LUIS automatisch een startsleutel, zodat u de app testen met tekstquery's. Deze sleutel maakt de integratie van de spraakservice niet mogelijk en werkt niet met deze handleiding. Maak een LUIS-bron in het Azure-dashboard en wijs deze toe aan de LUIS-app. U de gratis abonnementslaag voor deze handleiding gebruiken.

Nadat u de LUIS-bron in het Azure-dashboard hebt gemaakt, logt u in op de [LUIS-portal,](https://www.luis.ai/home)kiest u uw toepassing op de pagina **Mijn apps** en schakelt u over naar de pagina **Beheren van** de app. Selecteer ten slotte **Toetsen en Eindpunten** in de zijbalk.

![Instellingen voor sleutels en eindpunten in de LUIS-portal](media/sdk/luis-keys-endpoints-page.png)

Ga als een op de pagina **Toetsen en Eindpuntinstellingen:**

1. Schuif omlaag naar de sectie **Resources en sleutels** en selecteer Resource **toewijzen**.
1. Breng in het dialoogvenster **Een sleutel toewijzen aan uw app** de volgende wijzigingen aan:

   - Kies **Onder Tenant**de optie **Microsoft**.
   - Kies **onder Abonnementsnaam**het Azure-abonnement dat de LUIS-bron bevat die u wilt gebruiken.
   - Kies **onder Sleutel**de LUIS-bron die u met de app wilt gebruiken.

   Na korte tijd wordt het nieuwe abonnement weergegeven in de tabel onderaan de pagina.

1. Selecteer het pictogram naast een sleutel om het naar het klembord te kopiëren. (U kunt beide sleutels gebruiken.)

![Abonnementssleutels voor LUIS-app](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Een spraakproject maken in Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Code toevoegen

Vervolgens voegt u code toe aan het project.

1. Open het bestand **Program.cs**vanuit **Solution Explorer.**

1. Vervang het `using` blok met instructies aan het begin van het bestand door de volgende verklaringen:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Vervang de `Main()` meegeleverde methode door het volgende asynchrone equivalent:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Maak een lege `RecognizeIntentAsync()`asynchrone methode, zoals hier wordt weergegeven:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. Voeg in de hoofdtekst van deze nieuwe methode deze code toe:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Vervang de tijdelijke aanduidingen in deze methode als volgt door de sleutel, regio en app-id van uw LUIS-abonnement.

   | Tijdelijke aanduiding | Vervangen door |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | Uw LUIS-endpoint-sleutel Nogmaals, u moet dit item uit uw Azure-dashboard halen, niet van een 'starter-toets'. U het vinden op de pagina **Sleutels en Eindpunten van** uw app (onder **Beheren)** in de [LUIS-portal.](https://www.luis.ai/home) |
   | `YourLanguageUnderstandingServiceRegion` | De korte id voor de regio van uw LUIS-abonnement, zoals `westus` voor VS - west. Zie [Regio's](regions.md). |
   | `YourLanguageUnderstandingAppId` | De id van de LUIS-app. U het vinden op de **pagina Instellingen van** uw app in de [LUIS-portal.](https://www.luis.ai/home) |

Met deze wijzigingen u de toepassing **(Control+Shift+B)** bouwen en **(F5)** uitvoeren. Wanneer u wordt gevraagd, probeert u 'Doe de lichten uit' te zeggen in de microfoon van uw pc. De toepassing geeft het resultaat weer in het consolevenster.

In de volgende secties wordt dieper ingegaan op de code.

## <a name="create-an-intent-recognizer"></a>Een mechanisme voor intentieherkenning maken

Eerst moet u een spraakconfiguratie maken vanuit uw LUIS-eindpuntsleutel en -regio. U spraakconfiguraties gebruiken om leveranciers te maken voor de verschillende mogelijkheden van de Speech SDK. De spraakconfiguratie heeft meerdere manieren om het abonnement op te geven dat u wilt gebruiken. hier gebruiken `FromSubscription`we , die de abonnementssleutel en regio neemt.

> [!NOTE]
> Gebruik de sleutel en het gebied van uw LUIS-abonnement, geen spraakserviceabonnement.

Maak vervolgens een mechanisme voor intentieherkenning met behulp van `new IntentRecognizer(config)`. Aangezien de configuratie al weet welk abonnement u moet gebruiken, hoeft u de abonnementssleutel en het eindpunt niet opnieuw op te geven bij het maken van de herkenningspunt.

## <a name="import-a-luis-model-and-add-intents"></a>Een LUIS-model importeren en intenties toevoegen

Importeer het model nu uit de LUIS-app met `LanguageUnderstandingModel.FromAppId()` en voeg de intenties van LUIS toe die u wilt herkennen via de methode `AddIntent()` van het mechanisme. Deze twee stappen verbeteren de nauwkeurigheid van spraakherkenning door woorden aan te geven die de gebruiker waarschijnlijk zal gebruiken in aanvragen. U hoeft niet alle intenties van de app toe te voegen als u ze niet allemaal in uw toepassing hoeft te herkennen.

Als u intenties wilt toevoegen, moet u drie argumenten opgeven: `model`het LUIS-model (dat is gemaakt en de naam is vernoemd), de intentienaam en een intentie-id. Het verschil tussen de id en de naam is als volgt.

| `AddIntent()`&nbsp;Argument | Doel |
| --------------------------- | ------- |
| `intentName` | De naam van de intentie zoals gedefinieerd in de LUIS-app. Deze waarde moet exact overeenkomen met de naam van de LUIS-intentie. |
| `intentID` | Een id die door de Speech SDK wordt toegewezen aan een herkende intentie. Deze waarde kan zijn wat je wilt; het hoeft niet overeen te komen met de intentienaam zoals gedefinieerd in de LUIS-app. Als in dezelfde code bijvoorbeeld meerdere intenties worden afgehandeld, kun u dezelfde id voor de intenties gebruiken. |

De LUIS-app Voor domotica heeft twee intenties: een voor het inschakelen van een apparaat en een andere voor het uitschakelen van een apparaat. Met de onderstaande regels worden deze intenties toegevoegd aan het mechanisme voor intentieherkenning. Vervang de drie regels `AddIntent` in de methode `RecognizeIntentAsync()` door deze code.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

In plaats van afzonderlijke intents toe `AddAllIntents` te voegen, u de methode ook gebruiken om alle intenties in een model toe te voegen aan de herkenningspunt.

## <a name="start-recognition"></a>Herkenning starten

Het mechanisme voor intentieherkenning is gemaakt en de intenties zijn toegevoegd. We kunnen dus het proces van herkenning starten. De Speech SDK biedt ondersteuning voor herkenning van zowel afzonderlijke opnamen als continue herkenning.

| Herkennings-modus | Methoden om aan te roepen | Resultaat |
| ---------------- | --------------- | ------ |
| Eén opname | `RecognizeOnceAsync()` | Retourneert de herkende intentie, indien aanwezig, na één utterance. |
| Continu | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Herkent meerdere uitingen; zendt gebeurtenissen uit `IntermediateResultReceived`(bijvoorbeeld) wanneer de resultaten beschikbaar zijn. |

De applicatie maakt gebruik van `RecognizeOnceAsync()` single-shot modus en dus oproepen om te beginnen met de erkenning. Het resultaat is een `IntentRecognitionResult`-object met informatie over de herkende intentie. U haalt het JSON-antwoord van LUIS uit met de volgende expressie:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

De toepassing ontleden het JSON-resultaat niet. Alleen de JSON-tekst wordt weergegeven in het consolevenster.

![Resultaten van één LUIS-herkenning](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Herkenningstaal opgeven

Standaard herkent LUIS intenties in het Amerikaans-Engels (`en-us`). U kunt een landinstellingscode toewijzen aan de eigenschap `SpeechRecognitionLanguage` van de spraakconfiguratie om intenties in andere talen te herkennen. Voeg bijvoorbeeld `config.SpeechRecognitionLanguage = "de-de";` onze toepassing toe voordat u de herkenningspunt maakt om intents in het Duits te herkennen. Zie [LUIS-taalondersteuning](../LUIS/luis-language-support.md#languages-supported)voor meer informatie.

## <a name="continuous-recognition-from-a-file"></a>Continue herkenning uit een bestand

De volgende code illustreert twee aanvullende mogelijkheden van intentieherkenning met behulp van de Speech SDK. De eerste mogelijkheid, die eerder is genoemd, is continue spraakherkenning. Hierbij verzendt het mechanisme voor intentieherkenning gebeurtenissen wanneer er resultaten beschikbaar zijn. Deze gebeurtenissen kunnen vervolgens worden verwerkt door gebeurtenis-handlers die u opgeeft. Met continue herkenning roept u de `StartContinuousRecognitionAsync()` methode van de `RecognizeOnceAsync()`herkenningsmethode aan om herkenning te starten in plaats van .

De andere mogelijkheid is het lezen van de audio met de spraak die moet worden verwerkt uit een WAV-bestand. Implementatie omvat het maken van een audioconfiguratie die kan worden gebruikt bij het maken van de intent recognizeer. Het bestand moet éénkanaals (mono) zijn met een samplefrequentie van 16 kHz.

Als u deze functies wilt uitproberen, verwijdert `RecognizeIntentAsync()` of becommentarieert u de hoofdtekst van de methode en voegt u de volgende code op zijn plaats toe.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Wijzig de code met uw LUIS-eindpuntsleutel, regio en app-id en voeg de intenties van Home Automation toe, zoals u eerder hebt gedaan. Wijzig `whatstheweatherlike.wav` de naam van uw opgenomen audiobestand. Kopieer vervolgens het audiobestand naar de buildmap en voer de toepassing uit.

Als u bijvoorbeeld 'De lichten uitschakelen' zegt en vervolgens 'De lichten inschakelen' in uw opgenomen audiobestand zegt, kan de console-uitvoer die vergelijkbaar is met het volgende, worden weergegeven:

![Resultaten van luis-herkenning voor audiobestand](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Zoek naar de code uit dit artikel in de **map samples/csharp/sharedcontent/console.**

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Snelstart: spraak herkennen vanuit een microfoon](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)
