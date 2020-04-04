---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: travisw
ms.openlocfilehash: 8b187e058299f8aa8b762231c0ed1e708e5ad9d1
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658824"
---
1. Start Android Studio en selecteer **Start een nieuw Android Studio-project** in het venster **Welkom.**

    ![Schermafbeelding van het welkomstscherm van Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. De wizard **Uw project kiezen** wordt weergegeven. Selecteer **Telefoon en tablet** en Activiteit leeg **maken** in het vak activiteitsselectie. Selecteer **Volgende**.

   ![Schermafbeelding van De wizard Uw project kiezen](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Voer in **het scherm Uw project configureren** *snelstart* als **naam** in en voer *samples.speech.cognitiveservices.microsoft.com* in als **pakketnaam.** Selecteer vervolgens een projectmap. Selecteer **API 23: Android 6.0 (Marshmallow)** voor **minimaal API-niveau.** Laat alle andere selectievakjes uit en selecteer **Voltooien**.

   ![Schermafbeelding van De wizard Project configureren](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio heeft even de tijd nodig om het nieuwe Android-project voor te bereiden. Configureer vervolgens het project om meer te weten te komen over de Azure Cognitive Services Speech SDK en java 8 te gebruiken.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

De huidige versie van de Cognitive Services Speech SDK is 1.11.0.

De Speech SDK voor Android wordt aangeboden als een [AAR (Android Library)](https://developer.android.com/studio/projects/android-library) die de vereiste bibliotheken en Android-machtigingen bevat.
Het wordt gehost in een Maven\/repository op https: /csspeechstorage.blob.core.windows.net/maven/.

Stel uw project in voor gebruik van de Speech SDK. Open het venster **Projectstructuur** door > **Bestandsprojectstructuur te** selecteren in de menubalk van Android Studio. **File** Breng in het venster **Projectstructuur** de volgende wijzigingen aan:

1. Selecteer **Project** in de lijst aan de linkerkant van het venster. Bewerk de instellingen van de **Standaardbibliotheekrepository** door een komma toe te passen en\/onze URL van onze Maven-repository in enkele aanhalingstekens: 'https: /csspeechstorage.blob.core.windows.net/maven/'

   ![Schermafbeelding van het venster Project Structure](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Selecteer **app**op hetzelfde scherm aan de linkerkant. Selecteer vervolgens bovenaan het venster het tabblad **Dependencies**. Selecteer het groene**+** plusteken ( ) en selecteer **Bibliotheekafhankelijkheid** in het vervolgkeuzemenu.

   ![Schermafbeelding van de afhankelijkheid van de bibliotheek](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Voer in het venster dat wordt weergegeven de naam en versie van de SpraakSDK voor Android in, *com.microsoft.cognitiveservices.speech:client-sdk:1.11.0*. Selecteer vervolgens **OK**.
   De SpraakSDK moet nu worden toegevoegd aan de lijst met afhankelijkheden, zoals wordt weergegeven:

   ![Schermafbeelding van SpraakSDK in de lijst met afhankelijkheden](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Selecteer het tabblad **Eigenschappen.** Selecteer **1.9**voor zowel **broncompatibiliteit** als **doelcompatibiliteit**.

   ![Schermafbeelding van broncompatibiliteit en doelcompatibiliteit](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selecteer **OK** om het venster **Projectstructuur** te sluiten en uw wijzigingen toe te passen op het project.
