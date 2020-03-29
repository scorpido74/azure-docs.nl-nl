---
title: Beveiligde pushmeldingen verzenden met Azure Notification Hubs
description: Meer informatie over het verzenden van beveiligde pushmeldingen naar een Android-app vanuit Azure. Code voorbeelden geschreven in Java en C#.
documentationcenter: android
keywords: pushnotificatie, pushmeldingen, pushberichten, android pushmeldingen
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 419a9f9b5ce698c7516edd55856cbea9891ba029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212178"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Beveiligde pushmeldingen verzenden met Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Overzicht

> [!IMPORTANT]
> U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started) voor meer informatie.

Pushnotificatie in Microsoft Azure stelt u in staat om toegang te krijgen tot een gebruiksvriendelijke, multi-platform, geschaalde push-message infrastructuur, wat de implementatie van pushmeldingen voor zowel consumenten- als bedrijfstoepassingen voor mobiele platforms.

Vanwege wettelijke of beveiligingsbeperkingen wil een toepassing soms iets opnemen in de melding dat niet kan worden verzonden via de standaard push-notificatie-infrastructuur. In deze zelfstudie wordt beschreven hoe u dezelfde ervaring bereiken door gevoelige informatie te verzenden via een beveiligde, geverifieerde verbinding tussen het Android-clientapparaat en de back-end van de app.

Op een hoog niveau is de stroom als volgt:

1. De back-end van de app:
   * Slaat veilige payload op in back-end database.
   * Hiermee wordt de ID van deze melding naar het Android-apparaat verzonden (er wordt geen beveiligde informatie verzonden).
2. De app op het apparaat, bij ontvangst van de melding:
   * Het Android-apparaat neemt contact op met de back-end met het verzoek om de veilige payload.
   * De app kan de payload weergeven als een melding op het apparaat.

Het is belangrijk op te merken dat in de voorafgaande stroom (en in deze zelfstudie), wordt aangenomen dat het apparaat een verificatietoken opslaat in de lokale opslag, nadat de gebruiker zich heeft aanmeldt. Deze aanpak garandeert een naadloze ervaring, omdat het apparaat de beveiligde payload van de melding kan ophalen met behulp van dit token. Als uw toepassing geen verificatietokens op het apparaat opslaat of als deze tokens kunnen worden verlopen, moet de apparaat-app, na ontvangst van de pushmelding, een algemene melding weergeven waarin de gebruiker wordt gevraagd de app te starten. De app verifieert vervolgens de gebruiker en toont de payload van de melding.

In deze zelfstudie ziet u hoe u beveiligde pushmeldingen verzendt. Het bouwt voort op de [tutorial Gebruikers melden,](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) dus je moet eerst de stappen in die zelfstudie voltooien als je dat nog niet hebt gedaan.

> [!NOTE]
> In deze zelfstudie wordt ervan uitgegaan dat u uw meldingshub hebt gemaakt en geconfigureerd zoals beschreven in [Aan de slag met Meldinghubs (Android).](notification-hubs-android-push-notification-google-gcm-get-started.md)

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Het Android-project wijzigen

Nu u uw back-end van uw app hebt gewijzigd om alleen de *id* van een pushmelding te verzenden, moet u uw Android-app wijzigen om die melding te verwerken en uw back-end terugbellen om het beveiligde bericht op te halen dat moet worden weergegeven.
Om dit doel te bereiken, moet u ervoor zorgen dat uw Android-app weet hoe u zichzelf verifiëren met uw back-end wanneer deze de pushmeldingen ontvangt.

Wijzig nu de *aanmeldingsstroom* om de waarde van de verificatiekopwaarde op te slaan in de gedeelde voorkeuren van uw app. Analoge mechanismen kunnen worden gebruikt om verificatietokens (bijvoorbeeld OAuth-tokens) op te slaan die de app moet gebruiken zonder dat er gebruikersreferenties nodig zijn.

1. Voeg in je Android-app-project de volgende `MainActivity` constanten toe aan de bovenkant van de les:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```
2. Werk de `MainActivity` methode bij `getAuthorizationHeader()` om de volgende code te bevatten:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```
3. Voeg de `import` volgende instructies boven `MainActivity` aan het bestand toe:

    ```java
    import android.content.SharedPreferences;
    ```

Wijzig nu de handler die wordt aangeroepen wanneer de melding wordt ontvangen.

1. In `MyHandler` de klasse `OnReceive()` wijzigen van de methode te bevatten:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. Voeg vervolgens `retrieveNotification()` de methode toe `{back-end endpoint}` en vervang de tijdelijke aanduiding door het back-end-eindpunt dat is verkregen tijdens het implementeren van uw back-end:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Met deze methode wordt uw app back-end aangeroepen om de meldingsinhoud op te halen met behulp van de referenties die zijn opgeslagen in de gedeelde voorkeuren en wordt deze weergegeven als een normale melding. De melding kijkt naar de app-gebruiker precies zoals elke andere push-melding.

Het is beter om de gevallen van ontbrekende authenticatie header eigenschap of afwijzing door de back-end te behandelen. De specifieke afhandeling van deze gevallen hangt meestal af van uw doelgebruikerservaring. Een optie is om een melding weer te geven met een algemene prompt voor de gebruiker om te verifiëren om de werkelijke melding op te halen.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de volgende acties uit om de toepassing uit te voeren:

1. Controleer of **AppBackend** is geïmplementeerd in Azure. Als u Visual Studio gebruikt, voert u de **API-toepassing AppBackend** uit. Er wordt een ASP.NET webpagina weergegeven.
2. Voer in Eclipse de app uit op een fysiek Android-apparaat of de emulator.
3. Voer in de gebruikersinterface van de Android-app een gebruikersnaam en wachtwoord in. Dit kan elke string zijn, maar ze moeten dezelfde waarde hebben.
4. Klik in de gebruikersinterface van de Android-app op **Aanmelden**. Klik vervolgens op **Push verzenden**.
