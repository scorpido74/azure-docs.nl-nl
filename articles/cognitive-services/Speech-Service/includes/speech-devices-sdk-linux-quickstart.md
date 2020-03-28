---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 81c77b2f6ae0c4f8497716c168a937657ceb57dd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78383931"
---
In deze quickstart leert u hoe u de Spraakapparaten SDK voor Linux gebruiken om een spraakproduct te bouwen of te gebruiken als [een gesprekstranscriptieapparaat.](../conversation-transcription-service.md) Momenteel wordt alleen de [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) ondersteund.

De applicatie is gebouwd met het Speech SDK pakket en de Eclipse Java IDE (v4) op 64-bits Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

Deze handleiding vereist een [Azure Cognitive Services-account](../get-started.md) met een Spraakservicebron. Als u geen account hebt, kunt u de [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/) gebruiken om een abonnementssleutel op te halen.

De broncode voor de [voorbeeldtoepassing](https://aka.ms/sdsdk-download-JRE) is opgenomen in de Spraakapparaten SDK. Het is ook [beschikbaar op GitHub.](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* Besturingssysteem: 64-bits Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Alleen Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) of [JDK 8.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Een Azure-abonnementssleutel voor de Spraakservice. [Gratis downloaden](../get-started.md).
* Download de nieuwste versie van de [SpraakherkenningsSDK](https://aka.ms/sdsdk-download-JRE) voor Java en haal de .zip naar uw werkmap.
   > [!NOTE]
   > Deze quickstart gaat ervan uit dat de app wordt geëxtraheerd naar /home/wcaltest/JRE-Sample-Release

Zorg ervoor dat deze afhankelijkheden zijn geïnstalleerd voordat eclipse wordt gestart.

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

Op RHEL/CentOS 8:

  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

> [!NOTE]
> Volg op RHEL/CentOS 8 de instructies voor [het configureren van OpenSSL voor Linux.](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)

Conversation Transcription is momenteel alleen beschikbaar voor "en-US" en "zh-CN", in de "centralus" en "eastasia" regio's. U moet in een van die regio's een spraaksleutel hebben om gesprekstranscriptie te kunnen gebruiken.

Als u van plan bent de intenties te gebruiken, hebt u een [LUIS-abonnement (Language Understanding Service)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) nodig. Zie [Spraakintenties herkennen met LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)voor meer informatie over LUIS en intentieherkenning. Voor deze app is [een voorbeeld LUIS-model](https://aka.ms/sdsdk-luis) beschikbaar.

## <a name="create-and-configure-the-project"></a>Het project maken en configureren

1. Start Eclipse.

1. Voer in de **Eclipse IDE Launcher**in het veld **Werkruimte** de naam in van een nieuwe werkruimtemap. Selecteer vervolgens **Starten**.

   ![Schermopname van Eclipse Launcher](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Binnen enkele ogenblikken verschijnt het hoofdvenster van de Eclipse-IDE. Sluit het welkomstscherm als dit verschijnt.

1. Maak in de menubalk Eclipse een nieuw project door **Bestand** > **New** > **Java Project**te kiezen. Als niet beschikbaar kiest u **Project** en vervolgens **Java Project**.

1. De wizard **New Java Project** wordt gestart. **Blader naar** de locatie van het voorbeeldproject. Selecteer **Finish**.

   ![Schermopname van de wizard Nieuw Java-project](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. Klik in de **pakketverkenner**met de rechtermuisknop op uw project. Kies**Converteren naar Maven-project** **configureren** > in het contextmenu. Selecteer **Finish**.

   ![Schermopname van Package explorer](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Open het bestand pom.xml en bewerk dit.

    Aan het einde van het bestand, vóór de afsluitende tag, `</project>` `repositories` maken en `dependencies` elementen, zoals hier weergegeven, en zorg ervoor dat de `version` overeenkomt met uw huidige versie:
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
             <version>1.10.0</version>
        </dependency>
    </dependencies>
   ```

1. Klik in de **pakketverkenner**met de rechtermuisknop op uw project. Kies **Eigenschappen**en voer de instellingen > voor **uitvoeren/foutopsporing****nieuw uit...** > **Java-toepassing**. 

1. Het venster **Configuratie bewerken** wordt weergegeven. Voer in het veld **Naam** **Hoofd in**en gebruik Zoeken **naar** de **hoofdklasse** om **com.microsoft.cognitiveservices.speech.samples.FunctionsList**te zoeken en te selecteren.

   ![Schermafbeelding van Startconfiguratie bewerken](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Kopieer de audiobinaries voor uw doelarchitectuur, van **Linux-arm** of **Linux-x64**, naar de Locatie van het Java Project, bijvoorbeeld **/home/wcaltest/JRE-Sample-Release**

1. Selecteer in het venster **Configuratie bewerken** ook de pagina **Omgeving** en **Nieuw**. Het venster **Nieuwe omgevingsvariabele** wordt weergegeven. Voer in het veld **Naam** **LD_LIBRARY_PATH** in en voer in het **waardeveld** de map in met de *.so-bestanden, bijvoorbeeld **/home/wcaltest/JRE-Sample-Release**

1. Doel/klassen `kws.table` van de projectmap kopiëren `participants.properties` **en naar de projectmap**


## <a name="configure-the-sample-application"></a>De voorbeeldtoepassing configureren

1. Voeg uw spraakabonnementssleutel toe aan de broncode. Als u intentieherkenning wilt proberen, voegt u ook de abonnementssleutel [voor taalverstaanservice](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) en toepassings-id toe.

   Voor spraak en LUIS gaat `FunctionsList.java`uw informatie in:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Als u gesprekstranscriptie gebruikt, zijn uw spraaksleutel `Cts.java`en regio-informatie ook nodig in:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Het standaardtrefwoord (trefwoord) is 'Computer'. U ook een van de andere opgegeven zoekwoorden proberen, zoals 'Machine' of 'Assistent'. De bronbestanden voor deze alternatieve zoekwoorden bevinden zich in de SDK spraakapparaten in de trefwoordmap. Bevat bijvoorbeeld `/home/wcaltest/JRE-Sample-Release/keyword/Computer` de bestanden die worden gebruikt voor het zoekwoord "Computer".

   > [!TIP]
   > U ook [een aangepast zoekwoord maken.](../speech-devices-sdk-create-kws.md)

    Als u een nieuw zoekwoord `FunctionsList.java`wilt gebruiken, werkt u de volgende regel in en kopieert u het zoekwoord naar uw app. Bijvoorbeeld om het trefwoord 'Machine' uit `machine.zip`het trefwoordpakket te gebruiken:

   * Kopieer `kws.table` het bestand vanuit het zip-pakket naar het **doel/de doelklassen**van de projectmap.

   * Update `FunctionsList.java` de met de trefwoordnaam:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>De voorbeeldtoepassing uitvoeren vanuit Eclipse

1. In de menubalk Van de Eclipse **u** > **uitvoeren** 

1. De SDK-voorbeeldtoepassing Spraakapparaten start en geeft de volgende opties weer:

   ![Voorbeeld van voorbeeldspraakapparaten SDK-voorbeeldtoepassing en -opties](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Probeer de nieuwe **conversation transcription** demo. Begin met transcriberen met > **sessiestart**. **Session** Standaard is iedereen te gast. Als u echter de stemhandtekeningen van de deelnemer `participants.properties` hebt, kunnen ze worden geplaatst in de **doel/klassen**van de projectmap. Als u de spraakhandtekening wilt genereren, bekijkt u [Transcribe-gesprekken (SDK).](../how-to-use-conversation-transcription-service.md)

   ![Demo Conversation Transcriptie applicatie](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Zelfstandig de toepassing maken en uitvoeren

1. Klik in de **pakketverkenner**met de rechtermuisknop op uw project. Kies **Exporteren**. 
1. Het venster **Exporteren** wordt weergegeven. Vouw **Java** uit en selecteer **Het JAR-bestand voor uitbreidbaar** en selecteer **Volgende**.

   ![Schermafbeelding van het venster Exporteren](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. Het venster **Runnable JAR File Export** wordt weergegeven. Kies een **exportbestemming** voor de toepassing en selecteer **Voltooien**.
 
   ![Schermafbeelding van runnable JAR-bestandsexport](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Gelieve `kws.table` en `participants.properties` in de bovenstaande bestemming map als deze bestanden nodig zijn door de toepassing.

1. De LD_LIBRARY_LIB instellen op de map met de *.so-bestanden

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. De zelfstandige toepassing uitvoeren

     ```bash
     java -jar SpeechDemo.jar
     ```
