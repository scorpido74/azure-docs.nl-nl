---
title: Push meldingen verzenden naar Android met behulp van Azure Notification Hubs en Firebase Cloud Messa ging | Microsoft Docs
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs en Google Cloud Messaging pushmeldingen verzendt naar Android-apparaten.
services: notification-hubs
documentationcenter: android
keywords: pushmeldingen,pushmelding,android-pushmelding, firebase cloud messaging
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/11/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: 1af109405118754daa08adf848f1da3f04534455
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387509"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-google-firebase-cloud-messaging"></a>Zelf studie: Push meldingen naar Android-apparaten verzenden met Google Firebase Cloud Messa ging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In deze zelfstudie wordt gedemonstreerd hoe u met Azure Notification Hubs en Firebase Cloud Messaging (FCM) meldingen kunt pushen naar een Android-toepassing. In deze zelfstudie gaat u een lege Android-app maken die pushmeldingen ontvangt via Firebase Cloud Messaging (FCM).

De voltooide code voor deze zelf studie kan worden gedownload [van github](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp).

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een Android Studio-project maken.
> * Een Firebase-project maken dat Firebase Cloud Messaging ondersteunt.
> * Maak een hub.
> * Verbind uw app met de hub.
> * De app testen.

## <a name="prerequisites"></a>Vereisten

U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/free/) voor meer informatie. 

U hebt ook de volgende items nodig: 

* De nieuwste versie van [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2,3 of hoger voor Firebase Cloud Messa ging
* Google repository revisie 27 of hoger voor Firebase Cloud Messa ging
* Google Play Services 9.0.2 of hoger voor Firebase Cloud Messa ging

Het volt ooien van deze zelf studie is een vereiste voor het uitvoeren van alle andere Notification Hubs zelf studies voor Android-apps.

## <a name="create-an-android-studio-project"></a>Een Android Studio-project maken

1. Start Android Studio.
2. Selecteer **bestand**, wijs **Nieuw**aan en selecteer vervolgens **Nieuw project**. 
2. Selecteer op de pagina **uw project kiezen** de optie **lege activiteit**en selecteer vervolgens **volgende**. 
3. Voer de volgende stappen uit op de pagina **uw project configureren** : 
    1. Voer een naam in voor de toepassing.
    2. Geef een locatie op waar de project bestanden moeten worden opgeslagen. 
    3. Selecteer **Voltooien**. 

        ![Het project configureren](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Een Firebase-project maken dat FCM ondersteunt

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Een hub configureren

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Firebase Cloud Messaging-instellingen voor de hub configureren

1. Selecteer in het linkerdeel venster onder **instellingen** **Google (GCM/FCM)** . 
2. Voer de **Server sleutel** in voor het FCM-project dat u eerder hebt opgeslagen. 
3. Selecteer op de werkbalk **opslaan**. 

    ![Azure notification hub-Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. In het Azure Portal wordt een bericht weer gegeven in waarschuwingen dat de hub is bijgewerkt. De knop **Opslaan** kan niet worden gekozen. 

Uw hub is nu geconfigureerd om te werken met Firebase Cloud Messa ging. U hebt ook de verbindings reeksen die nodig zijn om meldingen naar een apparaat te verzenden en een app te registreren voor het ontvangen van meldingen.

## <a id="connecting-app"></a>Uw app verbinden met de Notification Hub

### <a name="add-google-play-services-to-the-project"></a>Google Play-services aan het project toevoegen

1. Selecteer in Android Studio **Hulpprogram ma's** in het menu en selecteer vervolgens **SDK-beheer**. 
2. Selecteer de doel versie van de Android SDK die in uw project wordt gebruikt. Selecteer vervolgens **pakket details weer geven**. 

    ![Android SDK Manager - doelversie selecteren](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selecteer **Google api's**, als deze nog niet is geïnstalleerd.

    ![Android SDK Manager - Google API’s geselecteerd](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Schakel over naar het tabblad **SDK-Hulpprogram ma's** . Als u Google Play-Services nog niet hebt geïnstalleerd, selecteert u **Google play services** , zoals in de volgende afbeelding wordt weer gegeven. Selecteer vervolgens **Toep assen** om te installeren. Noteer het SDK-pad om het in een later stadium te kunnen gebruiken.

    ![Android SDK Manager - Google Play Services geselecteerd](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Klik op **OK** zodra het dialoogvenster **Wijziging bevestigen** wordt weergegeven. De vereiste onderdelen worden geïnstalleerd met behulp van het installatieprogramma voor onderdelen. Selecteer **Voltooien** zodra de onderdelen zijn geïnstalleerd.
4. Selecteer **OK** om het dialoogvenster **Instellingen voor nieuwe projecten** te sluiten.  
1. Open het bestand AndroidManifest. XML en voeg de volgende tag toe aan het label *Application* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Azure Notification Hubs-bibliotheken toevoegen

1. Voeg in het bestand build. Gradle voor de app de volgende regels toe aan de sectie dependencies.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    ```

2. Voeg de volgende opslagplaats toe na het gedeelte met afhankelijkheden.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Ondersteuning voor Google Firebase toevoegen

1. Voeg in het bestand build. Gradle voor de app de volgende regels toe aan de sectie **dependencies** als deze nog niet bestaan. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Voeg de volgende invoeg toepassing aan het einde van het bestand toe als dit nog niet is gebeurd. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Selecteer **Nu synchroniseren** op de werkbalk.

### <a name="update-the-androidmanifestxml-file"></a>Het bestand AndroidManifest. XML bijwerken

1. Nadat u uw FCM-registratie token hebt ontvangen, gebruikt u dit om u te [registreren bij Azure notification hubs](notification-hubs-push-notification-registration-management.md). U kunt deze registratie op de achtergrond ondersteunen met behulp van een `IntentService` met de naam `RegistrationIntentService`. Met deze service wordt ook uw FCM-registratie token vernieuwd. U maakt ook een klasse met de naam `FirebaseService` als subklasse van `FirebaseMessagingService` en overschrijft de `onMessageReceived`-methode om meldingen te ontvangen en af te handelen. 

    Voeg de volgende servicedefinitie toe aan het bestand AndroidManifest.xml in de `<application>`-tag.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. Voeg de volgende benodigde FCM-machtigingen toe onder het label `</application>`.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Code toevoegen

1. Vouw in de Project-weergave **app** > **src** > **main** > **java** uit. Klik met de rechter muisknop op de pakketmap onder **Java**, selecteer **Nieuw**en selecteer vervolgens **Java-klasse**. Voer **NotificationSettings** in als naam en selecteer **OK**.

    Zorg ervoor dat u de volgende drie tijdelijke aanduidingen in de volgende code bijwerkt voor de klasse `NotificationSettings`:

   * **HubListenConnectionString**: de verbindingsreeks **DefaultListenAccessSignature** voor de hub. U kunt deze connection string kopiëren door te klikken op **toegangs beleid** in uw hub in de [Azure Portal].
   * **HubName**: gebruik de naam van uw hub die wordt weer gegeven op de hub-pagina in het [Azure Portal].

     `NotificationSettings`-code:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Voer de **naam** en het **DefaultListenSharedAccessSignature** van uw hub in voordat u verder gaat. 

2. Voeg een andere nieuwe klasse toe aan uw project met de naam `RegistrationIntentService`. Deze klasse implementeert de `IntentService`-interface. Ook wordt [het vernieuwen van het FCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) en [de registratie bij de notification hub](notification-hubs-push-notification-registration-management.md)afgehandeld.

    Gebruik de volgende code voor deze klasse.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check to see if the token has been compromised and needs refreshing.
                else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

3. Voeg in de klasse `MainActivity` de volgende `import`-instructies toe boven de klassen declaratie.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Voeg de volgende leden toe bovenaan de klasse. U gebruikt deze velden [om de beschikbaarheid van Google Play Services te controleren, zoals aanbevolen door Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. Voeg in de klasse `MainActivity` de volgende methode toe om de beschik baarheid van Google Play services te controleren.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
    * the Google Play Store or enable it in the device's system settings.
    */

    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

6. Voeg in de klasse `MainActivity` de volgende code toe om te controleren of er Google Play Services zijn voordat u de `IntentService` aanroept om uw FCM-registratie token op te halen en te registreren bij uw hub:

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

7. Voeg in de methode `OnCreate` van de klasse `MainActivity` de volgende code toe om het registratie proces te starten wanneer de activiteit wordt gemaakt:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. Als u de status van de app en het rapport in uw app wilt controleren, voegt u deze extra methoden toe aan `MainActivity`:

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

9. Voor de methode `ToastNotify` wordt het besturingselement *Hello World* gebruikt `TextView` om de status en kennisgevingen permanent in de app te melden. Voeg in de indeling **res** > **layout** > **activity_main.xml** de volgende id toe voor het besturingselement.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Azure Notification Hubs - Verzenden testen](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Vervolgens voegt u een subklasse toe voor de ontvanger die u hebt gedefinieerd in AndroidManifest. XML. Voeg een andere nieuwe klasse toe aan uw project met de naam `FirebaseService`.

11. Voeg boven in `FirebaseService.java` de volgende importinstructie toe:

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. Voeg de volgende code toe voor de klasse `FirebaseService`, waardoor er een subklasse van `FirebaseMessagingService`.

    Deze code overschrijft de `onMessageReceived` methode en rapport meldingen die worden ontvangen. Er wordt ook een push melding naar de Android Notification Manager verzonden met behulp van de `sendNotification()` methode. Roep de `sendNotification()` methode aan wanneer de app niet wordt uitgevoerd en er een melding wordt ontvangen.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. In Android Studio klikt u op de menu balk op **bouwen** > **opnieuw samen stellen** om ervoor te zorgen dat uw code geen fouten bevat. Als er een fout bericht over het `ic_launcher` pictogram wordt weer gegeven, verwijdert u de volgende instructie uit het bestand AndroidManifest. XML: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Zorg ervoor dat u een virtueel apparaat hebt voor het uitvoeren van de app. Als u er geen hebt, voegt u een als volgt toe:
    1. ![Apparaatbeheer openen](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Virtueel apparaat maken](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Voer de app uit op het geselecteerde apparaat en controleer of deze is geregistreerd met de hub.

    > [!NOTE]
    > Registratie kan mislukken tijdens de eerste keer starten totdat de `onTokenRefresh()` methode van de exemplaar-ID-service wordt aangeroepen. De vernieuwing moet een succesvolle registratie met de Notification Hub tot stand brengen.

    ![De registratie van het apparaat is voltooid](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Testen van melding verzenden vanuit de Notification Hub

U kunt push meldingen verzenden via de [Azure Portal] door de volgende stappen uit te voeren:

1. Selecteer in de Azure Portal op de pagina notification hub voor uw hub **testen verzenden** in de sectie **probleem oplossing** .
3. Selecteer voor **Platforms** de optie **Android**.
4. Selecteer **Verzenden**.  Er wordt nog geen melding op het Android-apparaat weer geven omdat u de mobiele app niet op de computer hebt uitgevoerd. Nadat u de mobiele app hebt uitgevoerd, selecteert u de knop **verzenden** opnieuw om het meldings bericht weer te geven.
5. Bekijk het resultaat van de bewerking in de lijst onderaan.

    ![Azure Notification Hubs - Verzenden testen](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. U ziet het meldings bericht op het apparaat. 

    ![Melding op apparaat](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>De mobiele app uitvoeren in een emulator
Voordat u push meldingen in een emulator test, moet u ervoor zorgen dat uw emulator-installatie kopie het Google API-niveau ondersteunt dat u voor uw app hebt gekozen. Als uw installatie kopie geen ondersteuning biedt voor systeem eigen Google Api's, kan het zijn dat de **SERVICE\_niet\_beschik bare** uitzonde ring.

Zorg er ook voor dat u uw Google-account hebt toegevoegd aan de actieve emulator onder **instellingen** > **accounts**. Anders kan het zijn dat uw pogingen om te registreren bij FCM leiden tot de MISLUKTE uitzonde ring voor **verificatie\_** .

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u Firebase Cloud Messa ging gebruikt om meldingen te verzenden naar alle Android-apparaten die zijn geregistreerd bij de service. Ga verder met de volgende zelfstudie als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke apparaten:

> [!div class="nextstepaction"]
>[Zelf studie: Push meldingen naar specifieke Android-apparaten](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure Portal]: https://portal.azure.com
