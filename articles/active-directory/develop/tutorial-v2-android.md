---
title: Gebruikers in-en uitloggen &-oproep Microsoft Graph (Android)-micro soft Identity-platform | Azure
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
ms.date: 11/26/2019
ms.author: hahamil
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4c4c9bc025e8fd506b298ed676674899e318481
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689348"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Zelf studie: gebruikers aanmelden en de Microsoft Graph aanroepen vanuit een Android-toepassing 

>[!NOTE]
>In deze zelf studie worden vereenvoudigde voor beelden van het werken met MSAL voor Android beschreven. Ter vereenvoudiging maakt deze zelf studie alleen gebruik van de modus voor één account. U kunt ook de opslag plaats bekijken en [de vooraf geconfigureerde voor beeld-app](https://github.com/Azure-Samples/ms-identity-android-java/) klonen om complexere scenario's te verkennen. Bekijk de [Quick](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android) start voor meer informatie over de voor beeld-app, configuratie en registratie. 

In deze zelf studie leert u hoe u uw Android-app integreert met het micro soft-identiteits platform met behulp van de micro soft-verificatie bibliotheek voor Android. U leert hoe u zich kunt aanmelden en een gebruiker kunt afmelden, een toegangs token krijgt om de Microsoft Graph-API aan te roepen en een aanvraag naar de Graph API te verzenden. 

> [!div class="checklist"]
> * Uw Android-app integreren met het micro soft Identity-platform 
> * Een gebruiker aanmelden 
> * Een toegangs Token ophalen om de Microsoft Graph-API aan te roepen 
> * De Microsoft Graph-API aanroepen 
> * Een gebruiker afmelden 

Wanneer u deze zelf studie hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke micro soft-accounts (waaronder outlook.com, live.com en anderen), evenals werk-of school accounts van een bedrijf of organisatie die gebruikmaakt van Azure Active Directory.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="how-this-tutorial-works"></a>Hoe deze zelf studie werkt

![Toont hoe de voor beeld-app die door deze zelf studie wordt gegenereerd, werkt](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

De app in deze zelf studie meldt zich aan bij gebruikers en haalt gegevens op uit hun naam. Deze gegevens worden geopend via een beveiligde API (Microsoft Graph-API) waarvoor autorisatie is vereist en wordt beveiligd door het micro soft Identity-platform.

Meer details:

* Uw app meldt zich aan bij de gebruiker via een browser of de Microsoft Authenticator en Intune-bedrijfsportal.
* De eind gebruiker accepteert de machtigingen die uw toepassing heeft aangevraagd.
* Uw app krijgt een toegangs token voor de Microsoft Graph-API.
* Het toegangs token wordt opgenomen in de HTTP-aanvraag voor de Web-API.
* Het Microsoft Graph-antwoord verwerken.

In dit voor beeld wordt de micro soft-verificatie bibliotheek voor Android (MSAL) gebruikt voor het implementeren van verificatie: [com. micro soft. Identity. client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 MSAL zal tokens automatisch vernieuwen, eenmalige aanmelding (SSO) leveren tussen andere apps op het apparaat en de account (s) beheren.

### <a name="prerequisites"></a>Vereisten

* Voor deze zelf studie is Android Studio versie 3.5 + vereist

## <a name="create-a-project"></a>Een project maken
Als u nog geen Android-toepassing hebt, voert u de volgende stappen uit om een nieuw project in te stellen. 

1. Open Android Studio en selecteer **een nieuw Android Studio-project starten**.
2. Selecteer **basis activiteit** en selecteer **volgende**.
3. Geef uw toepassing een naam.
4. Sla de naam van het pakket op. U gaat deze later in het Azure Portal invoeren.
5. Wijzig de taal van **Kotlin** in **Java**.
6. Stel het **minimale API-niveau** in op **API 19** of hoger en klik op **volt ooien**.
7. Kies in de Project weergave **project** in de vervolg keuzelijst om bron-en niet-bron project bestanden weer te geven, open **app/build. gradle** en stel `targetSdkVersion` in op `28`.

## <a name="integrate-with-microsoft-authentication-library"></a>Integreren met micro soft-verificatie bibliotheek 

### <a name="register-your-application"></a>Uw toepassing registreren

1. Ga naar de [Azure Portal](https://aka.ms/MobileAppReg).
2. Open de [blade app-registraties](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) en klik op **+ nieuwe registratie**.
3. Voer een **naam** in voor uw app en klik vervolgens **zonder** een OMleidings-URI in te stellen op **registreren**.
4. Selecteer in de sectie **beheren** van het deel venster dat wordt weer gegeven **verificatie** >  **+ een platform toevoegen** > **Android**. (Mogelijk moet u ' overschakelen naar de nieuwe ervaring ' boven aan de Blade selecteren om deze sectie weer te geven.
5. Voer de pakket naam van uw project in. Als u de code hebt gedownload, is deze waarde `com.azuresamples.msalandroidapp`.
6. Klik in de sectie **hash van hand tekening** van de pagina **uw Android-app configureren** op **een hash voor ontwikkel handtekeningen genereren.** Kopieer de opdracht voor het hulp programma om te gebruiken voor uw platform.

   > [!Note]
   > Het hulp programma voor het maken van de functie is geïnstalleerd als onderdeel van de JDK (Java Development Kit). U moet ook het hulp programma OpenSSL installeren om de opdracht voor het uitvoeren van het programma uit te voeren.

7. Voer de **hand tekening-hash** gegenereerd door het hulp programma.
8. Klik op `Configure` en sla de **MSAL-configuratie** op die wordt weer gegeven op de pagina Android- **configuratie** zodat u deze kunt invoeren wanneer u de app later configureert.  Klik op **Gereed**.

### <a name="configure-your-application"></a>Uw toepassing configureren 

1. Ga in het deel venster Project van Android Studio naar **app\src\main\res**.
2. Klik met de rechter muisknop op **Res** en kies **nieuwe** > **Directory**. Voer `raw` in als de naam van de nieuwe map en klik op **OK**.
3. Maak in **app** > **src** > **hoofd** > **Res** > **raw**een nieuw JSON-bestand met de naam `auth_configbn_single_account.json` en plak de MSAL-configuratie die u eerder hebt opgeslagen. 

    Plak onder de omleidings-URI: 
    ```json
      "account_mode" : "SINGLE",
    ```
    Het configuratie bestand moet er als volgt uitzien: 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```
    
   >[!NOTE]
   >In deze zelf studie wordt alleen gedemonstreerd hoe u een app kunt configureren in de modus voor één account. Raadpleeg de documentatie voor meer informatie over [één versus meerdere account modus](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) en [het configureren van uw app](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)
   
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
    Vervang de sleutel-hash die u hebt geregistreerd in het Azure Portal voor de `android:path=` waarde. De hash van de hand tekening mag **geen** URL-code ring zijn. Zorg ervoor dat er een toonaangevende `/` aan het begin van uw hand tekening-hash. 
    >[!NOTE]
    >De "pakket naam" u vervangt de `android:host` waarde met moet er ongeveer als volgt uitzien: "com. azuresamples. msalandroidapp" de "Signature hash" u vervangt uw `android:path` waarde met moet er ongeveer als volgt uitzien: "/1wIqXSqBj7w + h11ZifsnqwgyKrY =" u kunt deze waarden ook vinden in de Blade verificatie van de app-registratie. Houd er rekening mee dat de omleidings-URI er ongeveer als volgt uitziet: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Hoewel de hash van de hand tekening een URL-code ring aan het einde van deze waarde heeft, mag de hand tekening-hash **geen** URL-code ring in uw `android:path` waarde zijn. 

## <a name="use-msal"></a>MSAL gebruiken 

### <a name="add-msal-to-your-project"></a>MSAL toevoegen aan uw project

1. Ga in het venster Android Studio project naar **app** > **src** > **Build. gradle** en voeg het volgende toe: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.0.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    ```
    [Meer informatie over de Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Vereiste Imports 

Voeg het volgende toe boven aan de **app** > **src** > **Main**> **Java** > **com. voor beeld (yourapp)**  > **MainActivity. java** 

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>PublicClientApplication instantiëren
#### <a name="initialize-variables"></a>Variabelen initialiseren 
```java
private final static String[] SCOPES = {"User.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
In de klasse `MainActivity` raadpleegt u de volgende onCreate ()-methode om MSAL te instantiëren met behulp van de `SingleAccountPublicClientApplication`.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount 

```java
//When app comes to the foreground, load existing account to determine if user is signed in 
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }
        
        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
Luister naar knoppen en oproep methoden of logboek fouten dienovereenkomstig. 
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);
        
        //Sign in user 
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });
        
        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });
        
        //Interactive 
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> Als u zich afmeldt bij MSAL, worden alle bekende gegevens van een gebruiker uit de toepassing verwijderd, maar de gebruiker heeft nog steeds een actieve sessie op het apparaat. Als de gebruiker zich opnieuw probeert aan te melden, zien ze mogelijk de gebruikers interface voor aanmelden, maar hoeven ze hun referenties mogelijk niet opnieuw op te geven omdat de sessie van het apparaat nog steeds actief is. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Call back die wordt gebruikt voor interactieve aanvragen.

```java 
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Call back gebruikt voor Silent-aanvragen 
```java 
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Microsoft Graph API aanroepen 

De volgende code laat zien hoe u de GraphAPI aanroept met de Graph SDK. 

### <a name="callgraphapi"></a>callGraphAPI 

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Gebruikersinterface toevoegen
### <a name="activity"></a>Activiteit 
Als u uw gebruikers interface wilt model leren van deze zelf studie, bieden de volgende methoden een hand leiding voor het bijwerken van tekst en het Luis teren naar knoppen.

#### <a name="updateui"></a>updateUI
Knoppen in-of uitschakelen op basis van de aanmeldings status en tekst instellen.  
```java 
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>Display error
```java 
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Methode voor het bijwerken van de tekst in de gebruikers interface om afmelden weer te geven. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout 

Voor beeld `activity_main.xml`-bestand om knoppen en tekst vakken weer te geven. 

```xml 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Uw app testen

### <a name="run-locally"></a>Lokaal uitvoeren

Bouw en implementeer de app op een test apparaat of emulator. U moet zich kunnen aanmelden en tokens ontvangen voor Azure AD of persoonlijke micro soft-accounts.

Nadat u zich hebt aangemeld, worden in de app de gegevens weer gegeven die door de Microsoft Graph `/me` eind punt zijn geretourneerd.

### <a name="consent"></a>Vergunning

De eerste keer dat een gebruiker zich bij uw app aanmeldt, wordt de micro soft-identiteit gevraagd om toestemming te geven voor de aangevraagde machtigingen. Sommige Azure AD-tenants hebben de toestemming van de gebruiker uitgeschakeld. hiervoor moeten beheerders toestemming geven namens alle gebruikers. Ter ondersteuning van dit scenario moet u uw eigen Tenant maken of toestemming van de beheerder ontvangen. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u het app-object dat u hebt gemaakt in de stap [uw toepassing registreren](#register-your-application) .

## <a name="get-help"></a>Hulp krijgen

Ga naar [Help en ondersteuning](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) als u problemen ondervindt met deze zelf studie of met het micro soft Identity-platform.

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
