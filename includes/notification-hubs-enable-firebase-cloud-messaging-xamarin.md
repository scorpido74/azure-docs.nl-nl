---
title: bestand opnemen
description: bestand opnemen
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68728819"
---
1. Meld u aan bij de [Firebase-console](https://firebase.google.com/console/). Maak een nieuw Firebase-project als u er nog geen hebt.
2. Nadat u uw project hebt gemaakt, selecteert u **Firebase toevoegen aan uw Android-app**. 

    ![Firebase toevoegen aan uw Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Voer de volgende stappen uit op de pagina **Firebase toevoegen aan uw Android-app** : 
    1. Voer voor de naam van het **Android-pakket**een naam in voor uw pakket. Bijvoorbeeld: `tutorials.tutoria1.xamarinfcmapp`. 

        ![De pakket naam opgeven](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecteer **app registreren**.  
    1. Selecteer **Google-services. json downloaden**. Sla het bestand vervolgens op in de map van het project en selecteer **volgende**. Als u nog geen Visual Studio-project hebt gemaakt, kunt u deze stap uitvoeren nadat u het project hebt gemaakt. 

        ![Google-services. json downloaden](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Selecteer **Next**. 
    7. Selecteer **deze stap overs Laan**. 

        ![De laatste stap overs Laan](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Selecteer in de Firebase-console het tandwiel van uw project. Selecteer vervolgens **Projectinstellingen**.

    ![Selecteer Projectinstellingen](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Als u het bestand **Google-services. json** nog niet hebt gedownload, kunt u het downloaden op deze pagina. 

    ![Google-services. json downloaden van het tabblad Algemeen](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Schakel over naar het tabblad **Cloud Messa ging** bovenaan. Kopieer de **Server sleutel** en sla deze op voor later gebruik. U gebruikt deze waarde voor het configureren van uw notification hub.

    ![Server sleutel kopiëren](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
