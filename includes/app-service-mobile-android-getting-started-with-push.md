---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: e66e7d4646b650d2d811d3807db04605dfddeded
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176549"
---
1. Open het bestand `AndroidManifest.xml`in uw **app-project** . Voeg de volgende `application` code toe na de openingstag:

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Open het `ToDoActivity.java`bestand en breng de volgende wijzigingen aan:

    - Voeg de importinstructie toe:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Verander de `MobileServiceClient` definitie van **privé** naar **privé statisch,** dus het ziet er nu als volgt uit:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Methode `registerPush` toevoegen:

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Werk de **methode onCreate** van de `ToDoActivity` klasse bij. Zorg ervoor dat u `MobileServiceClient` deze code toevoegt nadat deze is geinstantieerd.

        ```java
        registerPush();
        ```

3. Voeg een nieuwe klasse toe om meldingen te verwerken. Open in Project Explorer de**knooppunten Java** > **your-project-namespace** van de **app** > en klik met de rechtermuisknop op het knooppunt met de naam van het pakket. Klik **op Nieuw**en klik vervolgens op **Java-klasse**. Typ in `ToDoMessagingService`Naam en klik op OK. Vervang vervolgens de klassedeclaratie door:

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Voeg een andere klasse toe om token-updates af te handelen. Maak `ToDoInstanceIdService` javaklasse en vervang de klassedeclaratie door:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Uw app is nu bijgewerkt om pushmeldingen te ondersteunen.
