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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68728819"
---
1. Meld u aan bij de [Firebase-console](https://firebase.google.com/console/). Maak een nieuw Firebase-project als u er nog geen hebt.
2. Nadat u uw project hebt gemaakt, selecteert u **Firebase toevoegen aan uw Android-app**. 

    ![Firebase toevoegen aan uw Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Ga op de pagina **Firebase toevoegen aan uw Android-app** de volgende stappen uit: 
    1. Voer voor de naam van het **Android-pakket**een naam voor uw pakket in. Bijvoorbeeld: `tutorials.tutoria1.xamarinfcmapp`. 

        ![De naam van het pakket opgeven](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecteer **App registreren**.  
    1. Selecteer **Google-services.json downloaden**. Sla het bestand vervolgens op in de map van uw project en selecteer **Volgende**. Als u het Visual Studio-project nog niet hebt gemaakt, u deze stap doen nadat u het project hebt gemaakt. 

        ![Download google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Selecteer **Volgende**. 
    7. Selecteer **Deze stap overslaan**. 

        ![De laatste stap overslaan](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Selecteer in de Firebase-console het tandwiel van uw project. Selecteer vervolgens **Projectinstellingen**.

    ![Selecteer Projectinstellingen](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Als u het bestand **google-services.json** nog niet hebt gedownload, u het downloaden op deze pagina. 

    ![Download google-services.json vanaf het tabblad Algemeen](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Ga naar het tabblad **Cloudmessaging** bovenaan. Kopieer en sla de **serversleutel** op voor later gebruik. U gebruikt deze waarde om uw meldingshub te configureren.

    ![Serversleutel kopiëren](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
