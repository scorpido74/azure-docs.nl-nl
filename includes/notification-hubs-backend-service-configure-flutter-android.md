---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 11bd05231aa3b5b336400d2434e63e57807a7e69
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156294"
---
### <a name="add-the-google-services-json-file"></a>Voeg het JSON-bestand van Google Services toe

1. **Beheer** + **Klik** in de map **Android** en kies **Openen in Android Studio**. Ga vervolgens naar de weergave **Project** (als deze nog niet wordt weergegeven).

1. Zoek het bestand *google-services.json* dat u eerder hebt gedownload toen u het project **PushDemo** in de [Firebase-console](https://console.firebase.google.com) instelde. Sleep het vervolgens naar de hoofdmap van de **app**-module (**Android** > **Android** > **app**).

### <a name="configure-build-settings-and-permissions"></a>Instellingen en machtigingen voor de build configureren

1. Zet de weergave **Project** op **Android**.

1. Open **AndroidManifest.xml** en voeg de machtigingen **INTERNET** en **READ_PHONE_STATE** toe aan het einde van het element **application** vóór de sluitende **</manifest>** -label.

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>De Firebase SDK's toevoegen

1. Open in **Android Studio** het bestand **build.gradle** op projectniveau (**Gradle-scripts** > **build.gradle (Project: Android)** ). en controleer of het klassepad "com.google.gms:google-services" voorkomt in het knooppunt ``buildscript`` > **dependencies**.

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > Controleer of u conform de instructies in de [Firebase-console](https://console.firebase.google.com) bij het maken van het **Android-project** naar de nieuwste versie verwijst.

1. Pas in het bestand **build.gradle** op app-niveau (**Gradle-scripts** > **build.gradle (Module: app)** ) de invoegtoepassing **Google Services Gradle** toe. Pas de invoegtoepassing direct boven het knooppunt **Android** toe.

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. Voeg in hetzelfde bestand, in het knooppunt **dependencies**, de afhankelijkheid toe voor de Android-bibliotheek **Cloud-berichten**.

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > Zorg ervoor dat u conform de [Android-clientdocumentatie voor Cloud-berichten](https://firebase.google.com/docs/cloud-messaging/android/client) naar de nieuwste versie verwijst.

1. Sla de wijzigingen op en klik vervolgens op de knop **Nu synchroniseren** (vanaf de prompt op de werkbalk) of **Project met Gradle-bestanden synchroniseren**.

### <a name="handle-push-notifications-for-android"></a>Pushmeldingen voor Android verwerken

1. In **Android Studio**, **Beheer** + **Klikt** u op de pakketmap **com.<your_organization>.pushdemo** (**app** > **src** > **main** > **kotlin**), kies **Pakket** in het menu **Nieuw**. Voer **Services** in als de naam en druk vervolgens op **Return**.

1. **Beheer** + **Klik** op de map **Services**, kies **Kotlin-bestand/klasse** in het menu **Nieuw**. Voer **DeviceInstallationService** in als naam en druk op **Return**.

1. Implementeer de **DeviceInstallationService** met de volgende code.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > Met deze klasse implementeert u het platformspecifieke equivalent voor het `com.<your_organization>.pushdemo/deviceinstallation`-kanaal. Dit is gedefinieerd in het Flutter-gedeelte van de app in **DeviceInstallationService.dart**. In dit geval worden de aanroepen vanuit de gemeenschappelijke code gedaan naar de systeemeigen host. Zorg ervoor dat u **<your_organization>** vervangt door de naam van uw eigen organisatie waar dit van toepassing is.
    >
    > Deze klasse biedt een unieke id (met [Secure.AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)) als onderdeel van de registratie-payload voor de meldingshub.

1. Voeg nog een **Kotlin-bestand/klasse** aan de map **Services** toe met de naam *NotificationRegistrationService* en voeg vervolgens de volgende code toe.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > Met deze klasse implementeert u het platformspecifieke equivalent voor het `com.<your_organization>.pushdemo/notificationregistration`-kanaal. Dit is gedefinieerd in het Flutter-gedeelte van de app in **NotificationRegistrationService.dart**. In dit geval worden de aanroepen vanuit de systeemeigen host gedaan naar de algemene code. Zorg er wederom voor dat u **<your_organization>** vervangt door de naam van uw eigen organisatie waar dit van toepassing is.

1. Voeg nog een **Kotlin-bestand/klasse** aan de map **Services** toe met de naam *NotificationActionService* en voeg vervolgens de volgende code toe.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Met deze klasse implementeert u het platformspecifieke equivalent voor het `com.<your_organization>.pushdemo/notificationaction`-kanaal. Dit is gedefinieerd in het Flutter-gedeelte van de app in **NotificationActionService.dart**. In dit geval vinden aanroepen in beide richtingen plaats. Zorg ervoor dat u **<your_organization>** vervangt door de naam van uw eigen organisatie waar dit van toepassing is.

1. Voeg een nieuw **Kotlin-bestand/klasse** toe aan het pakket**com.<your_organization>.pushdemo**. Geef het de naam *PushNotificationsFirebaseMessagingService* en implementeer het met de volgende code.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Deze klasse is verantwoordelijk voor het afhandelen van meldingen wanneer de app wordt uitgevoerd op de voorgrond. Deze roept conditioneel de **triggerAction** van de **NotificationActionService** aan als er een actie is opgenomen in de nettolading van de melding die in **onMessageReceived** wordt ontvangen. Hiermee wordt ook **refreshRegistration** aangeroepen voor de **NotificationRegistrationService-** wanneer het **Firebase**-token opnieuw wordt gegenereerd door de functie **onNewToken** te overschrijven.
    >
    > Zorg er wederom voor dat u **<your_organization>** vervangt door de naam van uw eigen organisatie waar deze wordt gebruikt.

1. Voeg in **AndroidManifest.xml** (**app** > **src** > **main**), de **PushNotificationsFirebaseMessagingService** onderaan het element **application** toe met het intentiefilter `com.google.firebase.MESSAGING_EVENT`.

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. In **DeviceInstallationService** controleert u of de volgende imports bovenaan het bestand worden weergegeven.

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > Vervang **<your_organization>** door de naam van uw eigen organisatie.

1. Wijzig de tekst van de tijdelijke aanduiding van *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation* om de tokenwaarde op te halen uit de **PushNotificationFirebaseMessagingService-** .

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. Controleer in **MainActivity** of de volgende imports bovenaan het bestand worden weergegeven.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > Vervang **<your_organization>** door de naam van uw eigen organisatie.

1. Voeg een variabele toe om een verwijzing naar de **DeviceInstallationService** op te slaan.

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. Voeg een functie toe met de naam **processNotificationActions** om te controleren of een **Intent** een extra waarde heeft met de naam **action**. Activeer die actie conditioneel of sla deze op voor later gebruik als de actie tijdens het starten van de app wordt verwerkt.

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. Overschrijf de functie **onNewIntent** om **processNotificationActions** aan te roepen.

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > Omdat de **LaunchMode** voor **MainActivity** is ingesteld op **SingleTop**, wordt er een **Intent** naar het bestaande **Activity**-exemplaar verzonden via de functie **onNewIntent** in plaats van de functie **onCreate**. Dat betekent dat u een inkomende **Intent** moet verwerken in zowel de functie **onCreate** als de functie **onNewIntent**.

1. Overschrijf de functie **onCreate** door de **deviceInstallationService** in te stellen op een nieuw exemplaar van **DeviceInstallationService**.

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. Stel de eigenschappen **notificationActionService** en **notificationRegistrationService** voor de **PushNotificationFirebaseMessagingServices** in.

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. In dezelfde functie roept u conditioneel **FirebaseInstanceId.getInstance().instanceId** aan. Implementeer de **OnCompleteListener** om de resulterende **token**waarde op **PushNotificationFirebaseMessagingService** in te stellen voordat **refreshRegistration** wordt aangeroepen.

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. Nog steeds in **onCreate** roept u aan het einde van de functie **processNotificationActions** aan. Gebruik *true* als *launchAction*-argument om aan te geven dat deze actie wordt verwerkt tijdens het starten van de app.

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> U moet de app steeds opnieuw registreren wanneer u deze uitvoert en foutopsporingssessies stoppen om pushmeldingen te blijven ontvangen.
