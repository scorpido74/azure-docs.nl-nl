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
3. Ga op de pagina **Firebase toevoegen aan uw Android-app** de volgende stappen uit: 
    1. Kopieer voor de naam van het **Android-pakket**de waarde van uw **applicatieId** in het build.gradle-bestand van uw toepassing. In dit voorbeeld is `com.fabrikam.fcmtutorial1app`het. 

        ![De naam van het pakket opgeven](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecteer **App registreren**. 
4. Selecteer **Google-services.json downloaden,** sla het bestand op in de **app-map** van uw project en selecteer **Volgende**. 

    ![Download google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Breng de volgende **configuratiewijzigingen** aan in uw project in Android Studio. 
    1.  Voeg in uw bestand build.gradle&gt;op projectniveau (project&lt;/build.gradle) de volgende instructie toe aan de sectie **afhankelijkheden.** 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Voeg in uw bestand build.gradle&gt;/&lt;op&gt;app-niveau (project&lt;app-module /build.gradle) de volgende instructies toe aan de sectie **afhankelijkheden.** 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Voeg de volgende regel toe aan het einde van het build.gradle-bestand op app-niveau na de sectie afhankelijkheden. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Selecteer **Nu synchroniseren** op de werkbalk. 
 
        ![build.gradle configuratiewijzigingen](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Selecteer **Volgende**. 
7. Selecteer **Deze stap overslaan**. 

    ![De laatste stap overslaan](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Selecteer in de Firebase-console het tandwiel van uw project. Selecteer vervolgens **Projectinstellingen**.

    ![Selecteer Projectinstellingen](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Als u het bestand google-services.json nog niet hebt gedownload in de **app-map** van uw Android Studio-project, u dit op deze pagina doen. 
5. Ga naar het tabblad **Cloudmessaging** bovenaan. 
6. Kopieer en sla de **serversleutel** op voor later gebruik. U gebruikt deze waarde om uw hub te configureren.
