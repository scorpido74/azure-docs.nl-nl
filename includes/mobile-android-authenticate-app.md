---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176722"
---
1. Open het project in Android Studio.

2. Open **Project Explorer** het `ToDoActivity.java` bestand in Project Explorer in Android Studio en voeg de volgende importinstructies toe:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Voeg de volgende methode toe aan de klasse **ToDoActivity:**

    ```java
    // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
    public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

    private void authenticate() {
        // Sign in using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

    Met deze code wordt een methode gemaakt om het Google-verificatieproces te verwerken. In een dialoogvenster wordt de id van de geverifieerde gebruiker weergegeven. U alleen doorgaan met een succesvolle verificatie.

    > [!NOTE]
    > Als u een andere identiteitsprovider dan Google gebruikt, wijzigt u de waarde die wordt doorgegeven aan de **inlogmethode** in een van de volgende waarden: _MicrosoftAccount,_ _Facebook,_ _Twitter_of _windowsazureactivedirectory_.

4. Voeg in de methode **onCreate** de volgende coderegel toe na `MobileServiceClient` de code die het object instantieert.

    ```java
    authenticate();
    ```

    Met deze oproep wordt het verificatieproces gestart.

5. Verplaats de resterende `authenticate();` code na in de **methode onCreate** naar een nieuwe **createTable-methode:**

    ```java
    private void createTable() {

        // Get the table instance to use.
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view.
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from Azure.
        refreshItemsFromTable();
    }
    ```

6. Als u wilt zorgen dat omleiding seinen zoals verwacht werken, voegt u het volgende fragment toe aan: `RedirectUrlActivity` `AndroidManifest.xml`

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}"
                android:host="easyauth.callback"/>
        </intent-filter>
    </activity>
    ```

7. Toevoegen `redirectUriScheme` `build.gradle` aan van uw Android-applicatie.

    ```gradle
    android {
        buildTypes {
            release {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
            debug {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
        }
    }
    ```

8. Toevoegen `com.android.support:customtabs:23.0.1` aan de afhankelijkheden in uw: `build.gradle`

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. Klik **in** het menu Uitvoeren op **App uitvoeren** om de app te starten en u aan te melden bij de door u gekozen identiteitsprovider.

> [!WARNING]
> Het genoemde URL-schema is hoofdlettergevoelig. Zorg ervoor dat `{url_scheme_of_you_app}` alle gevallen van gebruik hetzelfde geval gebruiken.

Wanneer u bent aangemeld, moet de app foutloos worden uitgevoerd en moet u de back-endservice kunnen opvragen en gegevens kunnen bijwerken.
