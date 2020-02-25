---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 87aa9f8201b924c1719f3ac805f79fb82631ecfd
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77563204"
---
In deze Quick Start leert u hoe u de speech apparaten SDK voor Linux kunt gebruiken om een product met spraak herkenning te bouwen of dit te gebruiken als een [conversatie transcriptie](../conversation-transcription-service.md) -apparaat. Momenteel wordt alleen [Azure KINECT DK](https://azure.microsoft.com/services/kinect-dk/) ondersteund.

De toepassing is gebouwd met het Speech SDK-pakket en de eclips Java IDE (v4) op 64-bits Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

Deze hand leiding vereist een [Azure Cognitive Services](../get-started.md) -account met een speech service-resource. Als u geen account hebt, kunt u de [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/) gebruiken om een abonnementssleutel op te halen.

De bron code voor de [voorbeeld toepassing](https://aka.ms/sdsdk-download-JRE) is opgenomen in de speech-apparaten SDK. Het is ook [beschikbaar op github](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* Besturings systeem: 64-bits Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* Alleen [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) of [jdk 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* Een Azure-abonnementssleutel voor de Spraakservice. [Gratis downloaden](../get-started.md).
* Down load de nieuwste versie van de [Speech apparaten SDK](https://aka.ms/sdsdk-download-JRE) voor Java en pak het. zip-bestand uit in uw werkmap.
   > [!NOTE]
   > In deze Snelstartgids wordt ervan uitgegaan dat de app is geëxtraheerd naar/home/wcaltest/JRE-Sample-Release

Zorg ervoor dat deze afhankelijkheden zijn geïnstalleerd voordat u de intereclips start.

* Op Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Op Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

De conversatie transcriptie is momenteel alleen beschikbaar voor ' nl-US ' en ' zh-CN ' in de regio's ' midden ' en ' EastAsia '. U moet een spraak sleutel hebben in een van deze regio's om conversatie transcriptie te kunnen gebruiken.

Als u van plan bent om de doel stellingen te gebruiken, hebt u een [Luis-abonnement (Language Understanding service)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) nodig. Zie voor meer informatie over LUIS en intentie herkenning [spraak intentie herkennen met Luis, C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Er is een voor beeld van een [Luis-model](https://aka.ms/sdsdk-luis) beschikbaar voor deze app.

## <a name="create-and-configure-the-project"></a>Het project maken en configureren

1. Start Eclipse.

1. Voer in het **Start programma voor de eclips IDE**in het veld **werk ruimte** de naam in van een nieuwe map voor de werk ruimte. Selecteer vervolgens **Starten**.

   ![Schermopname van Eclipse Launcher](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Binnen enkele ogenblikken verschijnt het hoofdvenster van de Eclipse-IDE. Sluit het welkomstscherm als dit verschijnt.

1. Maak in de menu balk voor eclips een nieuw project door **bestand** > **Nieuw** > **Java-project**te kiezen. Als deze niet beschikbaar is, kiest u **project** en vervolgens **Java project**.

1. De wizard **nieuw Java-project** wordt gestart. **Blader** naar de locatie van het voorbeeld project. Selecteer **Voltooien**.

   ![Schermopname van de wizard Nieuw Java-project](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. Klik in de **pakket Verkenner**met de rechter muisknop op uw project. Kies in het contextmenu **Configureren** > **Naar Maven-project converteren**. Selecteer **Voltooien**.

   ![Schermopname van Package explorer](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Open het bestand pom. XML en bewerk dit.

    Maak aan het einde van het bestand, vóór de afsluit code `</project>`, `repositories` en `dependencies` elementen, zoals hier wordt weer gegeven, en zorg ervoor dat de `version` overeenkomt met uw huidige versie:
    ```xml    
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.9.0</version>
        </dependency>
    </dependencies>
   ```

1. Klik in de **pakket Verkenner**met de rechter muisknop op uw project. Kies **Eigenschappen**en **Voer de instellingen voor uitvoeren/fout opsporing** > **nieuwe...** > **Java-toepassing**. 

1. Het venster **configuratie bewerken** wordt weer gegeven. In het veld **naam** voert u **Main**in en gebruikt u **zoeken** naar de **hoofd klasse** om **com. micro soft. cognitiveservices. speech. samples. FunctionsList**te zoeken en te selecteren.

   ![Scherm afbeelding van de configuratie voor het starten van bewerkingen](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Kopieer de binaire audio bestanden voor uw doel architectuur, van **Linux-arm** of **Linux-x64**naar de locatie van het Java-project, bijvoorbeeld **/Home/wcaltest/jre-sample-release**

1. Selecteer ook in het venster **configuratie bewerken** de pagina **omgeving** en **Nieuw**. Het venster **nieuwe omgevings variabele** wordt weer gegeven. Voer in het veld naam **LD_LIBRARY_PATH** in en voer in het veld **waarde** de map met de bestanden *. so in, bijvoorbeeld **/Home/wcaltest/jre-sample-release**

1. `kws.table` en `participants.properties` naar de projectmap kopiëren **doel/klassen** van het project


## <a name="configure-the-sample-application"></a>De voorbeeldtoepassing configureren

1. Voeg uw spraak abonnee sleutel toe aan de bron code. Als u de intentie herkenning wilt proberen, voegt u ook uw [Language Understanding service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) -abonnements sleutel en toepassings-id toe.

   Voor spraak-en LUIS-informatie gaat u naar `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Als u gesprek transcriptie gebruikt, zijn uw spraak sleutel en de regio gegevens ook nodig in `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Het sleutel woord default (sleutel woord) is "computer". U kunt ook een van de andere beschik bare tref woorden, zoals ' machine ' of ' assistent ', proberen. De bron bestanden voor deze alternatieve tref woorden bevinden zich in de speech-apparaten-SDK in de map tref woorden. `/home/wcaltest/JRE-Sample-Release/keyword/Computer` bevat bijvoorbeeld de bestanden die worden gebruikt voor het tref woord "computer".

   > [!TIP]
   > U kunt ook [een aangepast tref woord maken](../speech-devices-sdk-create-kws.md).

    Als u een nieuw tref woord wilt gebruiken, werkt u de volgende regel in `FunctionsList.java`en kopieert u het tref woord naar uw app. Als u bijvoorbeeld het tref woord ' machine ' van het trefwoord pakket `machine.zip`wilt gebruiken:

   * Kopieer het `kws.table`-bestand vanuit het zip-pakket naar de projectmap **/klassen**van het project.

   * Werk de `FunctionsList.java` bij met de naam van het tref woord:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>De voorbeeld toepassing vanuit een eclips uitvoeren

1. **Voer** in de menu balk voor eclips > **uitvoeren** uit 

1. De voorbeeldtoepassing met spraak Devices SDK wordt gestart en worden de volgende opties weergegeven:

   ![Voorbeeld van de voorbeeldtoepassing spraak Devices SDK en opties](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Probeer de nieuwe **transcriptie** -demo van de conversatie. Begin met transcriberen met **sessie** > **starten**. Standaard is iedereen een gast. Als u echter spraak handtekeningen van deel nemers hebt, kunnen ze in `participants.properties` worden geplaatst in de **projectmap van het project.** Als u de spraak handtekening wilt genereren, kijkt u naar [Transcribe-gesprekken (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Transcriptie-toepassing voor demo gesprekken](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Zelfstandige toepassing maken en uitvoeren

1. Klik in de **pakket Verkenner**met de rechter muisknop op uw project. Kies **exporteren**. 
1. Het venster **exporteren** wordt weer gegeven. Vouw **Java** uit en selecteer het **uitvoer bare jar-bestand** en selecteer vervolgens **volgende**.

   ![Scherm opname van het venster exporteren](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. Het venster **uitvoer bare JAR file export** wordt weer gegeven. Kies een **export doel** voor de toepassing en selecteer vervolgens **volt ooien**.
 
   ![Scherm opname van uitvoer bare JAR-bestand exporteren](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Plaats `kws.table` en `participants.properties` in de hierboven gekozen doelmap, omdat deze bestanden nodig zijn voor de toepassing.

1. Stel de LD_LIBRARY_LIB in op de map met de bestanden *. so

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. De zelfstandige toepassing uitvoeren

     ```bash
     java -jar SpeechDemo.jar
     ```
