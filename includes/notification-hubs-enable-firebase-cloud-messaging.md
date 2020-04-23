---
title: bestand opnemen
description: bestand opnemen
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70935121"
---
1. Meld u aan bij de [Firebase-console](https://firebase.google.com/console/). Maak een nieuw Firebase-project als u er nog geen hebt.
2. Nadat u uw project hebt gemaakt, selecteert u **Firebase toevoegen aan uw Android-app**. 

    ![Firebase toevoegen aan uw Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Voer de volgende stappen uit op de pagina **Firebase toevoegen aan uw Android-app** : 
    1. Voor de naam van het **Android-pakket**kopieert u de waarde van uw **applicationId** in het bestand build. gradle van uw toepassing. In dit voor beeld is `com.fabrikam.fcmtutorial1app`dat. 

        ![De pakket naam opgeven](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecteer **app registreren**. 
4. Selecteer **Google-services. json downloaden**, sla het bestand op in de map **app** van het project en selecteer vervolgens **volgende**. 

    ![Google-services. json downloaden](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Breng de volgende **configuratie wijzigingen** aan in uw project in Android Studio. 
    1.  Voeg in het bestand build. gradle van project niveau&lt;(&gt;project/build.gradle) de volgende instructie toe aan de sectie **dependencies** . 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Voeg in het bestand build. gradle van uw app&lt;-&gt;/&lt;niveau (project&gt;app-module/build.gradle) de volgende instructies toe aan de sectie **dependencies** . 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Voeg de volgende regel toe aan het einde van het build. gradle-bestand op app-niveau na de sectie dependencies. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Selecteer **Nu synchroniseren** op de werk balk. 
 
        ![wijzigingen in de configuratie van build. gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Selecteer **Next**. 
7. Selecteer **deze stap overs Laan**. 

    ![De laatste stap overs Laan](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Selecteer in de Firebase-console het tandwiel van uw project. Selecteer vervolgens **Projectinstellingen**.

    ![Selecteer Projectinstellingen](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Als u het bestand Google-services. json niet hebt gedownload naar de map **app** van uw Android Studio-project, kunt u dit doen op deze pagina. 
5. Schakel over naar het tabblad **Cloud Messa ging** bovenaan. 
6. Kopieer de **Server sleutel** en sla deze op voor later gebruik. U gebruikt deze waarde om uw hub te configureren.
