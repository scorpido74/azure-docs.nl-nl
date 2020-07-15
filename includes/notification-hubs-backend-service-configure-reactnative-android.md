---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9330488debb0b7611fdd63656e4c555566c749dd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060414"
---
### <a name="configure-required-android-packages"></a>Vereiste Android-pakketten configureren

Het pakket wordt [automatisch gekoppeld](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) bij het compileren van de app. U moet hieronder nog een paar stappen volgen om het configuratieproces te voltooien.

### <a name="configure-android-manifest"></a>Android-manifest configureren

Controleer in 'android/app/src/main/AndroidManifest.xml' de pakketnaam, machtigingen en vereiste services. Zorg ervoor dat u `RNPushNotificationPublisher`- en `RNPushNotificationBootEventReceiver`-ontvangers en de `RNPushNotificationListenerService`-service hebt geregistreerd. De metagegevens van de meldingen kunnen worden gebruikt om het uiterlijk van uw pushmeldingen aan te passen.

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="YOUR_PACKAGE_NAME">

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

        <application
          android:name=".MainApplication"
          android:label="@string/app_name"
          android:usesCleartextTraffic="true"
          android:icon="@mipmap/ic_launcher"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:allowBackup="false"
          android:theme="@style/AppTheme">

          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_name"
                      android:value="PushDemo Channel"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_description"
                      android:value="PushDemo Channel Description"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_foreground"
                      android:value="true"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_color"
                      android:resource="@android:color/white"/>

          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
              <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
              </intent-filter>
          </receiver>

          <service
              android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
              android:exported="false" >
              <intent-filter>
                  <action android:name="com.google.firebase.MESSAGING_EVENT" />
              </intent-filter>
          </service>

          <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
            android:launchMode="singleTask"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
          <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
        </application>

  </manifest>
  ```

### <a name="configure-google-services"></a>Google Services configureren

Registreer Google Services in 'android/app/build.gradle':

```gradle
dependencies {
  ...
  implementation 'com.google.firebase:firebase-analytics:17.3.0'
  ...
}

apply plugin: 'com.google.gms.google-services'
```

Kopieer het bestand 'google-services.json' dat u bij het installeren van FCM hebt gedownload naar de projectmap 'android/app/'.

### <a name="handle-push-notifications-for-android"></a>Pushmeldingen voor Android verwerken

U hebt de bestaande `RNPushNotificationListenerService`-service geconfigureerd om inkomende Android-pushmeldingen af te handelen. Deze service is eerder in het manifest van de toepassing geregistreerd. De service verwerkt inkomende meldingen en verzendt ze via een proxy naar het React Native-onderdeel. Er zijn geen extra stappen nodig.
