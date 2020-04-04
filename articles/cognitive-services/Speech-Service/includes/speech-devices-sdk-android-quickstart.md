---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 4dbaaad4b75e6d706d77522f45a2d596c49bea79
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658949"
---
In deze quickstart leert u hoe u de Spraakapparaten SDK voor Android gebruiken om een spraakproduct te bouwen of te gebruiken als [een gesprekstranscriptieapparaat.](../conversation-transcription-service.md)

Deze handleiding vereist een [Azure Cognitive Services-account](../get-started.md) met een Spraakservicebron. Als u geen account hebt, kunt u de [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/) gebruiken om een abonnementssleutel op te halen.

De broncode voor de voorbeeldtoepassing is opgenomen in de Spraakapparaten SDK. Het is ook [beschikbaar op GitHub.](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)

## <a name="prerequisites"></a>Vereisten

Voordat u de Spraakapparaten SDK gaat gebruiken, moet u het:

- Volg de instructies die bij uw [ontwikkelingskit](../get-speech-devices-sdk.md) zijn gegeven om het apparaat aan te zetten.

- Download de nieuwste versie van de [Speech Devices SDK](https://aka.ms/sdsdk-download)en haal de .zip naar uw werkmap.

  > [!NOTE]
  > Deze quickstart gaat ervan uit dat de app wordt geëxtraheerd naar C:\SDSDK\Android-Sample-Release

- Een [Azure-abonnementssleutel voor spraakservice aanschaffen](../get-started.md)

- Als u van plan bent de gesprekstranscriptie te gebruiken, moet u een [cirkelvormig microfoonapparaat](../get-speech-devices-sdk.md) gebruiken en deze functie is momenteel alleen beschikbaar voor "en-US" en "zh-CN" in regio's , "centralus" en "eastasia". U moet in een van die regio's een spraaksleutel hebben om gesprekstranscriptie te kunnen gebruiken.

- Als u van plan bent de spraakservice te gebruiken om intents (of acties) te identificeren op basis van uitingen van gebruikers, hebt u een [LUIS-abonnement (Language Understanding Service)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) nodig. Zie [Spraakintenties herkennen met LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)voor meer informatie over LUIS en intentieherkenning.

  U [een eenvoudig LUIS-model maken](https://docs.microsoft.com/azure/cognitive-services/luis/) of het voorbeeld LUIS-model luis-example.json gebruiken. Het voorbeeld LUIS-model is beschikbaar op de [downloadsite van Spraakapparaten SDK.](https://aka.ms/sdsdk-luis) Als u het JSON-bestand van uw model wilt uploaden naar de [LUIS-portal,](https://www.luis.ai/home)selecteert u **Nieuwe app importeren**en selecteert u vervolgens het JSON-bestand.

- Installeer [Android Studio](https://developer.android.com/studio/) en [Vysor](https://vysor.io/download/) op je pc.

## <a name="set-up-the-device"></a>Het apparaat instellen

1. Start Vysor op je computer.

   ![Vysor Vysor](../media/speech-devices-sdk/qsg-3.png)

1. Uw apparaat moet worden vermeld onder **Een apparaat kiezen**. Selecteer de knop **Weergave** naast het apparaat.

1. Maak verbinding met uw draadloze netwerk door het mappictogram te selecteren en selecteer Vervolgens**WlAN** **instellingen** > .

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Als uw bedrijf beleid heeft over het verbinden van apparaten met zijn Wi-Fi-systeem, moet u het MAC-adres verkrijgen en contact opnemen met uw IT-afdeling over hoe u het verbinden met de wi-fi van uw bedrijf.
   >
   > Als u het MAC-adres van de dev-kit wilt vinden, selecteert u het pictogram van de bestandsmap op het bureaublad van de dev-kit.
   >
   > ![Vysor-bestandsmap](../media/speech-devices-sdk/qsg-10.png)
   >
   > Selecteer **Instellingen**. Zoek naar 'mac-adres' en selecteer **vervolgens Mac-adres** > **Advanced WLAN**. Noteer het MAC-adres dat onder aan het dialoogvenster wordt weergegeven.
   >
   > ![Vysor MAC-adres](../media/speech-devices-sdk/qsg-11.png)
   >
   > Sommige bedrijven hebben mogelijk een tijdslimiet voor hoe lang een apparaat kan worden aangesloten op hun Wi-Fi-systeem. Mogelijk moet u de registratie van de dev kit na een bepaald aantal dagen uitbreiden met uw Wi-Fi-systeem.

## <a name="run-the-sample-application"></a>De voorbeeldtoepassing uitvoeren

Als u de installatie van uw ontwikkelingskit wilt valideren, bouwt en installeert u de voorbeeldtoepassing:

1. Start Android Studio.

1. Selecteer **Open een bestaand Android Studio project**.

   ![Android Studio - Een bestaand project openen](../media/speech-devices-sdk/qsg-5.png)

1. Ga naar C:\SDSDK\Android-Sample-Release\voorbeeld. Selecteer **OK** om het voorbeeldproject te openen.

1. Configureer gradle om naar de Spraak-SDK te verwijzen. De volgende bestanden zijn te vinden onder **Gradle Scripts** in Android Studio.

    Update de **build.gradle (Project:voorbeeld)**, het blok alle projecten moet hieronder overeenkomen, door het toevoegen van de maven lijnen.

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

    Werk de **build.gradle(Module:app)** bij door deze regel toe te voegen aan de sectie afhankelijkheden. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
    ```
    
1. Voeg uw spraakabonnementssleutel toe aan de broncode. Als u intentieherkenning wilt proberen, voegt u ook de abonnementssleutel [voor taalverstaanservice](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) en toepassings-id toe.

   Voor spraak en LUIS gaat uw informatie naar MainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Als u gesprekstranscriptie gebruikt, zijn uw spraaksleutel en regio-informatie ook nodig in conversation.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Het standaardzoekwoord is "Computer". U ook een van de andere opgegeven zoekwoorden proberen, zoals 'Machine' of 'Assistent'. De bronbestanden voor deze alternatieve zoekwoorden bevinden zich in de SDK spraakapparaten in de trefwoordmap. C:\SDSDK\Android-Sample-Release\keyword\Computer bevat bijvoorbeeld de bestanden die worden gebruikt voor het zoekwoord "Computer".

   > [!TIP]
   > U ook [een aangepast zoekwoord maken.](../speech-devices-sdk-create-kws.md)

   Als u een nieuw zoekwoord wilt `MainActivity.java`gebruiken, werkt u de volgende twee regels in en kopieert u het trefwoordpakket naar uw app. Bijvoorbeeld, om het trefwoord 'Machine' te gebruiken uit het trefwoord pakket kws-machine.zip:

   - Kopieer het trefwoordpakket naar de map "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   - Werk `MainActivity.java` de met het trefwoord en de naam van het pakket:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Werk de volgende regels bij, die de geometrie-instellingen voor de microfoonarray bevatten:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   In deze tabel worden ondersteunde waarden weergegeven:

   | Variabele | Betekenis | Beschikbare waarden |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Fysieke microfoonconfiguratie | Voor een circulaire dev kit:`Circular6+1` |
   |          |         | Voor een lineaire dev kit:`Linear4` |
   | `SelectedGeometry` | Softwaremicrofoonconfiguratie | Voor een circulaire dev kit die alle microfoons gebruikt:`Circular6+1` |
   |          |         | Voor een circulaire dev kit die gebruik maakt van vier microfoons:`Circular3+1` |
   |          |         | Voor een lineaire dev kit die alle microfoons gebruikt:`Linear4` |
   |          |         | Voor een lineaire dev kit die twee microfoons gebruikt:`Linear2` |

1. Als u de toepassing wilt samenstellen, selecteert u in het menu **Uitvoeren** de optie **'app' uitvoeren.** Het dialoogvenster **Implementatiedoel selecteren** wordt weergegeven.

1. Selecteer uw apparaat en selecteer **OK** om de toepassing op het apparaat te implementeren.

   ![Dialoogvenster Implementatiedoel selecteren](../media/speech-devices-sdk/qsg-7.png)

1. De SDK-voorbeeldtoepassing Spraakapparaten start en geeft de volgende opties weer:

   ![Voorbeeld van voorbeeldspraakapparaten SDK-voorbeeldtoepassing en -opties](../media/speech-devices-sdk/qsg-8.png)

1. Probeer de nieuwe conversation transcription demo. Begin met transcriberen met 'Start Sessie'. Standaard is iedereen te gast. Als u echter de stemhandtekeningen van de deelnemer hebt, kunnen ze in een bestand `/video/participants.properties` op het apparaat worden geplaatst. Als u de spraakhandtekening wilt genereren, bekijkt u [Transcribe-gesprekken (SDK).](../how-to-use-conversation-transcription-service.md)

   ![Demo Conversation Transcriptie applicatie](../media/speech-devices-sdk/qsg-15.png)

1. Experiment!

## <a name="troubleshooting"></a>Problemen oplossen

Als u geen verbinding maken met het spraakapparaat. Typ de volgende opdracht in een opdrachtpromptvenster. Het zal een lijst van apparaten retourneren:

```powershell
 adb devices
```

> [!NOTE]
> Deze opdracht maakt gebruik van `adb.exe`de Android Debug Bridge, die deel uitmaakt van de Android Studio-installatie. Deze tool bevindt zich\[in C:\Gebruikers gebruikersnaam]\AppData\Local\Android\Sdk\platform-tools. U deze map aan uw pad toevoegen `adb`om het gemakkelijker te maken om een beroep te doen. Anders moet u het volledige pad naar uw installatie van adb.exe opgeven in elke opdracht die aanroept. `adb`
>
> Als u een `no devices/emulators found` fout ziet, controleert u of uw USB-kabel is aangesloten en moet u ervoor zorgen dat er een kabel van hoge kwaliteit wordt gebruikt.
