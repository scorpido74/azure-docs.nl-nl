---
title: Aanmelden gebruikers & Call Microsoft Graph (Android)-micro soft Identity-platform | Azure
description: Een toegangs Token ophalen en Microsoft Graph of Api's aanroepen waarvoor toegangs tokens zijn vereist van micro soft Identity platform (Android)
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7feefc368815b1bfe57b67db2cd94702db799d78
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961554"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Zelf studie: gebruikers aanmelden en de Microsoft Graph aanroepen vanuit een Android-app

> [!NOTE]
> Deze zelf studie is nog niet bijgewerkt voor gebruik met de MSAL-bibliotheek voor Android versie 1,0. Het werkt met een eerdere versie, zoals is geconfigureerd in deze zelf studie.

In deze zelf studie leert u hoe u een Android-app integreert met het micro soft-identiteits platform. Uw app meldt zich aan bij een gebruiker, haalt een toegangs token op om de Microsoft Graph-API aan te roepen en brengt een aanvraag naar de Microsoft Graph-API.  

> [!div class="checklist"]
> * Een Android-app integreren met het micro soft Identity-platform
> * Een gebruiker aanmelden
> * Een toegangs Token ophalen om de Microsoft Graph-API aan te roepen
> * Roep de Microsoft Graph-API aan.  

Wanneer u deze zelf studie hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke micro soft-accounts (waaronder outlook.com, live.com en anderen), evenals werk-of school accounts van een bedrijf of organisatie die gebruikmaakt van Azure Active Directory.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="how-this-tutorial-works"></a>Hoe deze zelf studie werkt

![Toont hoe de voor beeld-app die door deze zelf studie wordt gegenereerd, werkt](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

De app in deze zelf studie meldt zich aan bij gebruikers en haalt gegevens op uit hun naam.  Deze gegevens worden geopend via een beveiligde API (Microsoft Graph-API) waarvoor autorisatie is vereist en wordt beveiligd door het micro soft Identity-platform.

Meer details:

* Uw app meldt zich aan bij de gebruiker via een browser of de Microsoft Authenticator en Intune-bedrijfsportal.
* De eind gebruiker accepteert de machtigingen die uw toepassing heeft aangevraagd.
* Uw app krijgt een toegangs token voor de Microsoft Graph-API.
* Het toegangs token wordt opgenomen in de HTTP-aanvraag voor de Web-API.
* Het Microsoft Graph-antwoord verwerken.

In dit voor beeld wordt de micro soft-verificatie bibliotheek voor Android (MSAL) gebruikt voor het implementeren van verificatie: [com. micro soft. Identity. client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 MSAL zal tokens automatisch vernieuwen, eenmalige aanmelding (SSO) leveren tussen andere apps op het apparaat en de account (s) beheren.

## <a name="prerequisites"></a>Vereisten

* Voor deze zelf studie is Android Studio versie 3,5 vereist.

## <a name="create-a-project"></a>Een project maken

In deze zelf studie wordt een nieuw project gemaakt. Als u in plaats daarvan de voltooide zelf studie wilt downloaden, [downloadt u de code](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).

1. Open Android Studio en selecteer **een nieuw Android Studio-project starten**.
2. Selecteer **basis activiteit** en selecteer **volgende**.
3. Geef uw toepassing een naam.
4. Sla de naam van het pakket op. U gaat deze later in het Azure Portal invoeren.
5. Wijzig de taal van **Kotlin** in **Java**.
6. Stel het **minimale API-niveau** in op **API 19** of hoger en klik op **volt ooien**.
7. Kies in de Project weergave **project** in de vervolg keuzelijst om bron-en niet-bron project bestanden weer te geven, open **app/build. gradle** en stel `targetSdkVersion` in op `28`.

## <a name="register-your-application"></a>Uw toepassing registreren

1. Ga naar de [Azure Portal](https://aka.ms/MobileAppReg).
2. Open de [blade app-registraties](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) en klik op **+ nieuwe registratie**.
3. Voer een **naam** in voor uw app en klik vervolgens zonder een omleidings-URI in te stellen op **registreren**.
4. Selecteer in de sectie **beheren** van het deel venster dat wordt weer gegeven **verificatie** >  **+ een platform toevoegen** > **Android**. (Mogelijk moet u ' overschakelen naar de nieuwe ervaring ' boven aan de Blade selecteren om deze sectie weer te geven.
5. Voer de pakket naam van uw project in. Als u de code hebt gedownload, is deze waarde `com.azuresamples.msalandroidapp`.
6. Klik in de sectie **hash van hand tekening** van de pagina **uw Android-app configureren** op **een hash voor ontwikkel handtekeningen genereren.** Kopieer de opdracht voor het hulp programma om te gebruiken voor uw platform.

   > [!Note]
   > Het hulp programma voor het maken van de functie is geïnstalleerd als onderdeel van de JDK (Java Development Kit). U moet ook het hulp programma OpenSSL installeren om de opdracht voor het uitvoeren van het programma uit te voeren.

7. Voer de **hand tekening-hash** gegenereerd door het hulp programma.
8. Klik op `Configure` en sla de **MSAL-configuratie** op die wordt weer gegeven op de pagina Android- **configuratie** zodat u deze kunt invoeren wanneer u de app later configureert.  Klik op **Gereed**.

## <a name="build-your-app"></a>Uw app ontwikkelen

### <a name="add-your-app-registration"></a>Uw app-registratie toevoegen

1. Ga in het deel venster Project van Android Studio naar **app\src\main\res**.
2. Klik met de rechter muisknop op **Res** en kies **nieuwe** > **Directory**. Voer `raw` in als de naam van de nieuwe map en klik op **OK**.
3. Maak in **app** > **src** > **hoofd** > **Res** > **raw**een nieuw JSON-bestand met de naam `auth_config.json` en plak de MSAL-configuratie die u eerder hebt opgeslagen. Zie [MSAL-configuratie voor meer informatie](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
4. In **app** > **src** > **Main** > **AndroidManifest. xml**, voegt u de onderstaande activiteit `BrowserTabActivity` toe aan de hoofd tekst van de toepassing. Met deze vermelding kan micro soft terugbellen naar uw toepassing nadat de verificatie is voltooid:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Vervang de naam van het pakket die u hebt geregistreerd in het Azure Portal voor de `android:host=` waarde.
    Vervang de sleutel-hash die u hebt geregistreerd in het Azure Portal voor de `android:path=` waarde. De hash van de hand tekening mag geen URL-code ring zijn.

5. Voeg in het **AndroidManifest. XML-bestand**de volgende machtigingen toe, net boven de `<application>`-tag:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>De gebruikers interface van de app maken

1. Navigeer in het venster Android Studio project naar **app** > **src** > **Main** > **Res** > **Layout** en open **activity_main. XML** en open de **tekst** weergave.
2. Wijzig de indeling van de activiteit, bijvoorbeeld: `<androidx.coordinatorlayout.widget.CoordinatorLayout` aan `<androidx.coordinatorlayout.widget.DrawerLayout`. 
3. Voeg de eigenschap `android:orientation="vertical"` toe aan het knoop punt `LinearLayout`.
4. Plak de volgende code in het knoop punt `LinearLayout` en vervang de huidige inhoud:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>MSAL toevoegen aan uw project

1. Navigeer in het venster Android Studio project naar **app** > **src** > **Build. gradle**.
2. Plak het volgende onder **afhankelijkheden**:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3+'
    ```

### <a name="use-msal"></a>MSAL gebruiken

Breng nu wijzigingen aan in `MainActivity.java` om MSAL toe te voegen aan en te gebruiken in uw app.
Navigeer in het venster Android Studio project naar **app** > **src** > **Main** > **Java** > **com. example. ( uw app)** en open `MainActivity.java`.

#### <a name="required-imports"></a>Vereiste Imports

Voeg de volgende invoer toe aan de bovenkant van `MainActivity.java`:

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiate-msal"></a>MSAL instantiëren

In de `MainActivity`-klasse moeten we MSAL maken, samen met enkele configuraties over wat de app doet, met inbegrip van de bereiken en Web-API die we willen gebruiken.

Kopieer de volgende variabelen in de klasse `MainActivity`:

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Vervang de inhoud van `onCreate()` door de volgende code om MSAL te instantiëren:

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

De code hierboven probeert gebruikers op de achtergrond te registreren wanneer ze uw toepassing openen via `getAccounts()` en, als dit is gelukt, `acquireTokenSilentAsync()`.  In de volgende gedeelten implementeren we de call back-handler voor het geval er geen accounts zijn aangemeld.

#### <a name="use-msal-to-get-tokens"></a>MSAL gebruiken om tokens op te halen

Nu kunnen we de UI-verwerkings logica van de app implementeren en tokens interactief ophalen via MSAL.

MSAL beschrijft twee primaire methoden voor het ophalen van tokens: `acquireTokenSilentAsync()` en `acquireToken()`.  

`acquireTokenSilentAsync()` zich bij een gebruiker aanmeldt en tokens ophalen zonder tussen komst van de gebruiker als er een account aanwezig is. Als de service slaagt, worden de tokens door MSAL naar uw app gestuurt. als dit mislukt, wordt er een `MsalUiRequiredException`gegenereerd.  Als deze uitzonde ring wordt gegenereerd of als u wilt dat de gebruiker een interactieve aanmeldings ervaring (referenties, MFA of een ander beleid voor voorwaardelijke toegang mogelijk niet vereist), gebruikt u `acquireToken()`.  

`acquireToken()` de gebruikers interface wordt weer gegeven wanneer wordt geprobeerd de gebruiker aan te melden en tokens op te halen. Het kan echter gebruik maken van sessie cookies in de browser of een account in micro soft Authenticator om de interactieve-SSO-ervaring te bieden.

Maak de volgende drie UI-methoden in de klasse `MainActivity`:

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

Voeg de volgende methoden toe om de huidige activiteit en het stil & van interactieve retour aanroepen te verkrijgen:

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>MSAL gebruiken voor afmelden

Voeg vervolgens ondersteuning toe voor afmelden.

> [!Important]
> Als u zich afmeldt bij MSAL, worden alle bekende gegevens van een gebruiker uit de toepassing verwijderd, maar de gebruiker heeft nog steeds een actieve sessie op het apparaat. Als de gebruiker zich opnieuw probeert aan te melden, zien ze mogelijk de gebruikers interface voor aanmelden, maar hoeven ze hun referenties mogelijk niet opnieuw op te geven omdat de sessie van het apparaat nog steeds actief is.

Voeg de volgende methode toe aan de klasse `MainActivity` om de afmeldings functie toe te voegen. Deze methode doorloopt alle accounts en verwijdert deze:

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>De Microsoft Graph-API aanroepen

Zodra we een token hebben ontvangen, kunnen we een aanvraag indienen voor de [Microsoft Graph-API](https://graph.microsoft.com) . het toegangs token bevindt zich in de `AuthenticationResult` in de methode `onSuccess()` van de auth-call back. Als u een geautoriseerde aanvraag wilt maken, moet uw app het toegangs token toevoegen aan de HTTP-header:

| header sleutel    | waarde                 |
| ------------- | --------------------- |
| Autorisatie | Toegang tot \<-token van Bearer-> |

Voeg de volgende twee methoden toe aan de klasse `MainActivity` om Graph aan te roepen en de gebruikers interface bij te werken:

```java
/* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

#### <a name="multi-account-applications"></a>Toepassingen met meerdere accounts

Deze app is gebouwd voor een enkel account scenario. MSAL biedt ook ondersteuning voor scenario's met meerdere accounts, maar hiervoor is extra werk van apps vereist. U moet een gebruikers interface maken om te helpen bij het selecteren van het account dat u wilt gebruiken voor elke actie waarvoor tokens vereist zijn. Uw app kan ook een heuristiek implementeren om te selecteren welk account u wilt gebruiken via de `getAccounts()` methode.

## <a name="test-your-app"></a>Uw app testen

### <a name="run-locally"></a>Lokaal uitvoeren

Bouw en implementeer de app op een test apparaat of emulator. U moet zich kunnen aanmelden en tokens ontvangen voor Azure AD of persoonlijke micro soft-accounts.

Nadat u zich hebt aangemeld, worden in de app de gegevens weer gegeven die door de Microsoft Graph `/me` eind punt zijn geretourneerd.

### <a name="consent"></a>vergunning

De eerste keer dat een gebruiker zich bij uw app aanmeldt, wordt de micro soft-identiteit gevraagd om toestemming te geven voor de aangevraagde machtigingen.  Hoewel de meeste gebruikers kunnen worden gemachtigd, hebben sommige Azure AD-tenants de toestemming van de gebruiker uitgeschakeld. hiervoor moeten beheerders toestemming geven namens alle gebruikers. Als u dit scenario wilt ondersteunen, moet u de scopes van uw app registreren in de Azure Portal.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u het app-object dat u hebt gemaakt in de stap [uw toepassing registreren](#register-your-application) .

## <a name="get-help"></a>Hulp krijgen

Ga naar [Help en ondersteuning](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) als u problemen ondervindt met deze zelf studie of met het micro soft Identity-platform.

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
