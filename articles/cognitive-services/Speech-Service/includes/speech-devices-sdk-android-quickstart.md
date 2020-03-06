---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 2ee6b12923bfd0e06343e8f185226cb72280d806
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383961"
---
In deze Quick Start leert u hoe u de speech apparaten SDK voor Android kunt gebruiken om een product met spraak herkenning te bouwen of dit te gebruiken als een [conversatie transcriptie](../conversation-transcription-service.md) -apparaat.

Deze hand leiding vereist een [Azure Cognitive Services](../get-started.md) -account met een speech service-resource. Als u geen account hebt, kunt u de [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/) gebruiken om een abonnementssleutel op te halen.

De broncode voor de voorbeeldtoepassing is opgenomen in de SDK van de apparaten spraak. Het is ook [beschikbaar op github](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het gebruik van de speech apparaten SDK, moet u het volgende doen:

- Volg de instructies in uw [Development Kit](../get-speech-devices-sdk.md) om het apparaat in te scha kelen.

- Down load de nieuwste versie van de [Speech apparaten SDK](https://aka.ms/sdsdk-download)en pak het. zip-bestand uit in uw werkmap.

  > [!NOTE]
  > In deze Snelstartgids wordt ervan uitgegaan dat de app is geëxtraheerd naar C:\SDSDK\Android-Sample-Release

- Een Azure- [abonnements sleutel voor spraak service](../get-started.md) ophalen

- Als u van plan bent om de conversatie transcriptie te gebruiken, moet u een [circulair microfoon apparaat](../get-speech-devices-sdk.md) gebruiken en deze functie is momenteel alleen beschikbaar voor "en-US" en "zh-cn" in regio's, "centraalus" en "EastAsia". U moet een spraak sleutel hebben in een van deze regio's om conversatie transcriptie te kunnen gebruiken.

- Als u van plan bent de speech-service te gebruiken om de intenties (of acties) te identificeren van de gebruiker uitingen, hebt u een [Luis-abonnement (Language Understanding service)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) nodig. Zie voor meer informatie over LUIS en intentie herkenning [spraak intentie herkennen met Luis, C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  U kunt [een eenvoudig Luis-model maken](https://docs.microsoft.com/azure/cognitive-services/luis/) of het voor beeld-Luis model gebruiken, Luis-voorbeeld. json. Het voor beeld-LUIS-model is beschikbaar op de [download site van de speech-apparaten SDK](https://aka.ms/sdsdk-luis). Als u het JSON-bestand van uw model wilt uploaden naar de [Luis-Portal](https://www.luis.ai/home), selecteert u **nieuwe app importeren**en selecteert u vervolgens het JSON-bestand.

- Installeer [Android Studio](https://developer.android.com/studio/) en [VYSOR](https://vysor.io/download/) op uw PC.

## <a name="set-up-the-device"></a>Het apparaat instellen

1. Vysor op uw computer starten.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. Uw apparaat moet worden vermeld onder **Kies een apparaat**. Selecteer de knop **weer geven** naast het apparaat.

1. Maak verbinding met het draadloze netwerk door het mappictogram te selecteren en vervolgens **instellingen** > **WLAN**te selecteren.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Als uw bedrijf beleid heeft over het verbinden van apparaten met het Wi-Fi-systeem, moet u het MAC-adres verkrijgen en neem contact op met uw IT-afdeling over het verbinden van uw bedrijf Wi-Fi.
   >
   > Als u het MAC-adres van de dev kit zoekt, selecteer het mappictogram op het bureaublad van de dev kit.
   >
   > ![Bestandsmap Vysor](../media/speech-devices-sdk/qsg-10.png)
   >
   > **Instellingen**selecteren. Zoek naar Mac-adres en selecteer vervolgens Mac- **adres** > **Geavanceerde WLAN**. Noteer het MAC-adres dat wordt weergegeven aan de onderkant van het dialoogvenster.
   >
   > ![Vysor MAC-adres](../media/speech-devices-sdk/qsg-11.png)
   >
   > Sommige bedrijven hebben mogelijk een tijdslimiet op hoe lang een apparaat kan zijn verbonden met de Wi-Fi-systeem. Mogelijk moet u de registratie van de dev kit met uw Wi-Fi-systeem uitbreiden na een bepaald aantal dagen.

## <a name="run-the-sample-application"></a>De voorbeeldtoepassing uitvoeren

Als u de Setup van de Development Kit wilt valideren, bouwt en installeert u de voorbeeld toepassing:

1. Android Studio starten.

1. Selecteer **Open een bestaand Android Studio project**.

   ![Android Studio - een bestaand project openen](../media/speech-devices-sdk/qsg-5.png)

1. Ga naar C:\SDSDK\Android-Sample-Release\example. Selecteer **OK** om het voorbeeld project te openen.

1. Configureer gradle om te verwijzen naar de Speech SDK. De volgende bestanden zijn te vinden onder **Gradle-scripts** in Android Studio.

    Werk het **Build. gradle (project: voor beeld)** bij, het blok AllProjects moet hieronder overeenkomen door de Maven-regels toe te voegen.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    Werk de **Build. gradle (module: app)** bij door deze regel toe te voegen aan het gedeelte afhankelijkheden. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.10.0'
    ```
    
1. Voeg uw spraak abonnee sleutel toe aan de bron code. Als u de intentie herkenning wilt proberen, voegt u ook uw [Language Understanding service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) -abonnements sleutel en toepassings-id toe.

   Voor spraak-en LUIS gaat uw informatie over op MainActivity. java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Als u gesprek transcriptie gebruikt, zijn uw spraak sleutel en regio-informatie ook vereist in conversatie. java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Het sleutel woord default is "computer". U kunt ook een van de andere beschik bare tref woorden, zoals ' machine ' of ' assistent ', proberen. De bron bestanden voor deze alternatieve tref woorden bevinden zich in de speech-apparaten-SDK in de map tref woorden. C:\SDSDK\Android-Sample-Release\keyword\Computer bevat bijvoorbeeld de bestanden die worden gebruikt voor het tref woord "computer".

   > [!TIP]
   > U kunt ook [een aangepast tref woord maken](../speech-devices-sdk-create-kws.md).

   Als u een nieuw tref woord wilt gebruiken, werkt u de volgende twee regels bij in `MainActivity.java`en kopieert u het trefwoord pakket naar uw app. Als u bijvoorbeeld het tref woord ' machine ' uit het trefwoord pakket KWS-machine. zip wilt gebruiken:

   - Kopieer het trefwoord pakket naar de map "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   - Werk de `MainActivity.java` bij met het sleutel woord en de pakket naam:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. De volgende regels de microfoon matrix geometrie-instellingen bevatten bijwerken:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Deze tabel geeft een lijst van ondersteunde waarden:

   | Variabele | Betekenis | Beschikbare waarden |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Fysieke mic-configuratie | Voor een ronde dev kit: `Circular6+1` |
   |          |         | Voor een lineaire dev kit: `Linear4` |
   | `SelectedGeometry` | De softwareconfiguratie voor de mic | Voor een ronde dev kit die gebruikmaakt van alle mics: `Circular6+1` |
   |          |         | Voor een ronde dev kit die gebruikmaakt van vier mics: `Circular3+1` |
   |          |         | Voor een lineaire dev kit die gebruikmaakt van alle mics: `Linear4` |
   |          |         | Voor een lineaire dev kit die gebruikmaakt van twee mics: `Linear2` |

1. Als u de toepassing wilt bouwen, selecteert u in het menu **uitvoeren** de optie **app uitvoeren**. Het dialoog venster **implementatie doel selecteren** wordt weer gegeven.

1. Selecteer uw apparaat en selecteer vervolgens **OK** om de toepassing te implementeren op het apparaat.

   ![Het dialoogvenster implementatiedoel selecteren](../media/speech-devices-sdk/qsg-7.png)

1. De voorbeeldtoepassing met spraak Devices SDK wordt gestart en worden de volgende opties weergegeven:

   ![Voorbeeld van de voorbeeldtoepassing spraak Devices SDK en opties](../media/speech-devices-sdk/qsg-8.png)

1. Probeer de nieuwe transcriptie-demo van de conversatie. Begin met transcriberen met ' sessie starten '. Standaard is iedereen een gast. Als u echter spraak handtekeningen van een deel nemer hebt, kunnen ze in een bestand `/video/participants.properties` op het apparaat worden geplaatst. Als u de spraak handtekening wilt genereren, kijkt u naar [Transcribe-gesprekken (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Transcriptie-toepassing voor demo gesprekken](../media/speech-devices-sdk/qsg-15.png)

1. Experiment!

## <a name="troubleshooting"></a>Probleemoplossing

Als u geen verbinding kunt maken met het spraak apparaat. Typ de volgende opdracht in een opdracht prompt venster. Er wordt een lijst met apparaten geretourneerd:

```powershell
 adb devices
```

> [!NOTE]
> Deze opdracht maakt gebruik van de Android debug Bridge, `adb.exe`, die deel uitmaakt van de Android Studio-installatie. Dit hulp programma bevindt zich in C:\Users\[gebruikers naam] \AppData\Local\Android\Sdk\platform-tools. U kunt deze map toevoegen aan uw pad om het gemakkelijker te maken om `adb`aan te roepen. Anders moet u het volledige pad naar uw installatie van ADB. exe opgeven in elke opdracht die `adb`aanroept.
>
> Als er een fout `no devices/emulators found`, controleer dan of de USB-kabel is aangesloten en of er een kabel met een hoge kwaliteit wordt gebruikt.
