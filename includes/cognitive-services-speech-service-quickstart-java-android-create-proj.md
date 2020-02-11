---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: travisw
ms.openlocfilehash: a7ce6bc2b6fa272c8dd3c4bf95bc06a37d1fe28f
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119747"
---
1. Start Android Studio en selecteer **een nieuw Android Studio-project starten** in het **welkomst** venster.

    ![Schermafbeelding van het welkomstscherm van Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. De wizard **uw project kiezen** wordt weer gegeven. Selecteer **telefoon en Tablet** en **lege activiteit** in het selectie vakje voor de activiteit. Selecteer **Next**.

   ![Scherm opname van de wizard project kiezen](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Voer in het scherm **uw project configureren** de **naam** *Snelstartgids* in en voer *samples.speech.cognitiveservices.Microsoft.com* in als **pakket naam**. Selecteer vervolgens een projectmap. Selecteer voor **Mini maal API-niveau** **API 23: Android 6,0 (Marshmallow)** . Zorg ervoor dat alle andere selectie vakjes zijn uitgeschakeld en selecteer **volt ooien**.

   ![Scherm opname van de wizard uw project configureren](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio heeft even de tijd nodig om het nieuwe Android-project voor te bereiden. Configureer vervolgens het project zodanig dat u weet wat de Azure Cognitive Services Speech-SDK is en om Java 8 te gebruiken.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

De huidige versie van de Cognitive Services Speech SDK is 1.9.0.

De Speech SDK voor Android wordt aangeboden als een [AAR (Android Library)](https://developer.android.com/studio/projects/android-library) die de vereiste bibliotheken en Android-machtigingen bevat.
Het wordt gehost in een Maven-opslag plaats op https:\//csspeechstorage.blob.core.windows.net/maven/.

Stel uw project in voor gebruik van de Speech SDK. Open het venster **project structuur** door **bestand** > **project structuur** te selecteren in de menu balk van Android Studio. Breng de volgende wijzigingen aan in het venster **project structuur** :

1. Selecteer **Project** in de lijst aan de linkerkant van het venster. Bewerk de standaard instellingen voor de **bibliotheek opslagplaats** door een komma en de URL van de Maven-opslag plaats tussen enkele aanhalings tekens toe te voegen: https:\//csspeechstorage.blob.core.Windows.net/maven/

   ![Schermafbeelding van het venster Project Structure](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Selecteer op hetzelfde scherm aan de linkerkant de optie **app**. Selecteer vervolgens bovenaan het venster het tabblad **Dependencies**. Selecteer het groene plus teken ( **+** ) en selecteer **afhankelijkheid van tape wisselaar** in de vervolg keuzelijst.

   ![Scherm afbeelding van afhankelijkheid van tape wisselaar](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Voer in het venster dat wordt weer gegeven de naam en versie van de Speech SDK voor Android, *com. micro soft. cognitiveservices. speech: Client-SDK: 1.9.0*. Selecteer vervolgens **OK**.
   De spraak-SDK moet nu worden toegevoegd aan de lijst met afhankelijkheden, zoals wordt weer gegeven:

   ![Scherm opname van de SDK voor spraak in de lijst met afhankelijkheden](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Selecteer het tabblad **Eigenschappen** . Voor compatibiliteit met de **bron** en compatibiliteit van het **doel**selecteert u **1,9**.

   ![Scherm afbeelding van compatibiliteit van bronnen en doel compatibiliteit](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selecteer **OK** om het venster **project structuur** te sluiten en uw wijzigingen toe te passen op het project.
