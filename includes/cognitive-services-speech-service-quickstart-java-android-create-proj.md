---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: e1f9b561efb1fd88fe2dd00f8c175f1ebae848d8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391606"
---
1. Start Android Studio starten en kies **Start a new Android Studio project** in het welkomstscherm.

    ![Schermafbeelding van het welkomstscherm van Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. De wizard **uw project kiezen** wordt weer gegeven, selecteer **telefoon en Tablet** en **lege activiteit** in het selectie vakje voor de activiteit. Selecteer **Next**.

   ![Scherm opname van de wizard project kiezen](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Typ in het scherm **uw project configureren** de **naam** **Snelstartgids** , **samples.speech.cognitiveservices.Microsoft.com** als **pakket naam**en kies een projectmap. Voor **Mini maal API-niveau** Kies **API 23: Android 6,0 (Marshmallow)** , zorg ervoor dat alle overige selectie vakjes zijn uitgeschakeld en selecteer **volt ooien**.

   ![Scherm opname van de wizard uw project configureren](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio heeft even de tijd nodig om het nieuwe Android-project voor te bereiden. Configureer het project vervolgens voor gebruik van de Speech SDK en Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

De huidige versie van de Speech SDK van Cognitive Services is `1.7.0`.

De Speech SDK voor Android wordt aangeboden als een [AAR (Android Library)](https://developer.android.com/studio/projects/android-library) die de vereiste bibliotheken en Android-machtigingen bevat.
Deze wordt gehost in een Maven-opslag plaats op https: \//csspeechstorage. blob. core. Windows. net/maven/.

Stel uw project in voor gebruik van de Speech SDK. Open het venster Project Structure door **File** > **Project Structure** te kiezen in de menubalk van Android Studio. Breng in het venster Project Structure de volgende wijzigingen aan:

1. Selecteer **Project** in de lijst aan de linkerkant van het venster. Bewerk de instellingen voor **Default Library Repository** door een komma toe te voegen en dan de URL van onze Maven-opslagplaats tussen enkele aanhalingstekens. https: \//csspeechstorage. blob. core. Windows. net/maven/'

   ![Schermafbeelding van het venster Project Structure](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Selecteer in hetzelfde scherm aan de linkerkant **app**. Selecteer vervolgens bovenaan het venster het tabblad **Dependencies**. Selecteer het groen plusteken (+) en kies **Library dependency** in de vervolgkeuzelijst.

   ![Schermafbeelding van het venster Project Structure](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Voer in het venster dat verschijnt de naam en versie van onze Speech SDK voor Android in, `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`. Selecteer vervolgens **OK**.
   De Speech SDK moet nu worden toegevoegd aan de lijst met afhankelijkheden, zoals hieronder wordt weergegeven:

   ![Schermafbeelding van het venster Project Structure](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Selecteer het tabblad **Eigenschappen** . Voor compatibiliteit met de **bron** en compatibiliteit van het **doel**selecteert u **1,8**.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selecteer **OK** om het venster Project Structure te sluiten en wijzigingen toe te passen op het project.
