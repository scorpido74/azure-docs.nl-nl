---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 71e4b0b3b7bf84938123acf70ac18a93c3015a4d
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095316"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>Een Firebase-project maken en Firebase Cloud Messaging inschakelen voor Android

1. Meld u aan bij de [Firebase-console](https://firebase.google.com/console/). Maak een nieuw Firebase-project en geef **PushDemo** als **Projectnaam**op.

    > [!NOTE]
    > Er wordt een unieke naam voor u gegenereerd. Dit bestaat standaard uit een variant van de naam die u hebt ingevoerd in kleine letters plus een gegenereerd nummer, gescheiden door een streepje. U kunt dit wijzigen als u wilt dat het nog wereldwijd uniek is.

1. Nadat u uw project hebt gemaakt, selecteert u **Firebase toevoegen aan uw Android-app**.

    ![Firebase toevoegen aan uw Android-app](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

1. Voer de volgende stappen uit op de pagina **Firebase toevoegen aan uw Android-app**.
    1. Voer voor de **Android-pakketnaam** een naam in voor uw pakket. Bijvoorbeeld: `com.<organization_identifier>.<package_name>`.

        ![Specificeer de pakketnaam](./media/notification-hubs-common-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    1. Selecteer **App registreren**.  
    1. Selecteer **google services.json downloaden**. Sla het bestand vervolgens op in een lokale map voor later gebruik en selecteer **Volgende**.  

        ![Download google-services.json](./media/notification-hubs-common-enable-firebase-cloud-messaging/download-google-service-button.png)
    1. Selecteer **Next**.
    1. Selecteer **Doorgaan naar de console**

        > [!NOTE]
        > Als de knop **Doorgaan naar de console** niet is ingeschakeld, als gevolg van de controle *installatie verifiëren*, kiest u **Deze stap overslaan**.

1. Selecteer in de Firebase-console het tandwiel van uw project. Selecteer vervolgens **Projectinstellingen**.

    ![Selecteer Projectinstellingen](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > Als u het bestand **google-services.json** niet hebt gedownload, kunt u het downloaden op deze pagina.

1. Ga naar **Cloud Messaging** bovenaan op het tabblad. Kopieer de **Serversleutel** en sla deze op voor later gebruik. U gebruikt deze waarde om uw Notification Hub te configureren.

    ![Serversleutel kopiëren](./media/notification-hubs-common-enable-firebase-cloud-messaging/server-key.png)
