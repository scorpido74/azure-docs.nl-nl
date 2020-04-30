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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67509076"
---
1. Selecteer in Android Studio **Hulpprogram ma's** in het menu en selecteer vervolgens **SDK-beheer**. 
2. Selecteer de doel versie van de Android SDK die in uw project wordt gebruikt. Selecteer vervolgens **pakket details weer geven**. 

    ![Android SDK Manager - doelversie selecteren](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selecteer **Google api's**, als deze nog niet is geïnstalleerd.

    ![Android SDK Manager - Google API’s geselecteerd](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Schakel over naar het tabblad **SDK-Hulpprogram ma's** . Als u Google Play-Services nog niet hebt geïnstalleerd, selecteert u **Google play services** , zoals in de volgende afbeelding wordt weer gegeven. Selecteer vervolgens **Toep assen** om te installeren. Noteer het SDK-pad om het in een later stadium te kunnen gebruiken.

    ![Android SDK Manager - Google Play Services geselecteerd](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Klik op **OK** zodra het dialoogvenster **Wijziging bevestigen** wordt weergegeven. De vereiste onderdelen worden geïnstalleerd met behulp van het installatieprogramma voor onderdelen. Selecteer **Voltooien** zodra de onderdelen zijn geïnstalleerd.
4. Selecteer **OK** om het dialoogvenster **Instellingen voor nieuwe projecten** te sluiten.  
5. Open het bestand build. gradle in de **app** -map en voeg de volgende regel toe onder `dependencies`. 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Selecteer in de werk balk op het pictogram **Nu synchroniseren** .

    ![Synchroniseren met Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Open het bestand AndroidManifest. XML en voeg de volgende tag toe aan het label *Application* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
