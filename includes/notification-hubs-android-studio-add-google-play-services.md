---
title: bestand opnemen
description: bestand opnemen
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd202c98ed605209f5600965ecdb6c0b4c03c17e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67509076"
---
1. Selecteer in Android Studio **Extra** in het menu en selecteer **vervolgens SDK Manager**. 
2. Selecteer de doelversie van de Android SDK die in uw project wordt gebruikt. Selecteer vervolgens **Pakketgegevens weergeven**. 

    ![Android SDK Manager - doelversie selecteren](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selecteer **Google API's**als deze nog niet is geïnstalleerd.

    ![Android SDK Manager - Google API’s geselecteerd](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Ga naar het tabblad **Hulpmiddelen voor SDK.** Als u Google Play Services nog niet hebt geïnstalleerd, selecteert u **Google Play Services** zoals weergegeven in de volgende afbeelding. Selecteer vervolgens **Toepassen** om te installeren. Noteer het SDK-pad om het in een later stadium te kunnen gebruiken.

    ![Android SDK Manager - Google Play Services geselecteerd](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Klik op **OK** zodra het dialoogvenster **Wijziging bevestigen** wordt weergegeven. De vereiste onderdelen worden geïnstalleerd met behulp van het installatieprogramma voor onderdelen. Selecteer **Voltooien** zodra de onderdelen zijn geïnstalleerd.
4. Selecteer **OK** om het dialoogvenster **Instellingen voor nieuwe projecten** te sluiten.  
5. Open het bestand build.gradle in de **app-map** `dependencies`en voeg de volgende regel toe onder . 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Selecteer **Nu synchroniseren** op de werkbalk.

    ![Synchroniseren met Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Open het bestand AndroidManifest.xml en voeg de volgende tag toe aan de *toepassingstag.*

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
