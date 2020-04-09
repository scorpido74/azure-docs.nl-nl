---
title: Gebruikers in-/uitloggen & Microsoft Graph (Android) bellen - Microsoft-identiteitsplatform | Azure
description: Ontvang een toegangstoken en bel Microsoft Graph of API's waarvoor toegangstokens van Microsoft-identiteitsplatform (Android) nodig zijn
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5c8bd5accefceee042601c3cf7d71f5e9131e04e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880819"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Zelfstudie: Meld u aan voor gebruikers en bel de Microsoft Graph vanuit een Android-toepassing 

>[!NOTE]
>Deze zelfstudie toont vereenvoudigde voorbeelden van hoe te werken met MSAL voor Android. Voor de eenvoud maakt deze zelfstudie alleen gebruik van single account-modus. U de repo bekijken en [de vooraf geconfigureerde voorbeeld-app](https://github.com/Azure-Samples/ms-identity-android-java/) klonen om complexere scenario's te verkennen. Bekijk de [Quickstart](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android) voor meer informatie over de voorbeeld-app, -configuratie en -registratie. 

In deze zelfstudie leert u hoe u uw Android-app integreren met het Microsoft-identiteitsplatform met behulp van de Microsoft-verificatiebibliotheek voor Android. U leert hoe u zich aanmelden en een gebruiker afmelden, een toegangstoken krijgen om de Microsoft Graph-API aan te roepen en een verzoek indienen bij de Graph API. 

> [!div class="checklist"]
> * Uw Android-app integreren met het Microsoft Identity Platform 
> * Aanmelden voor een gebruiker 
> * Een toegangstoken krijgen om de Microsoft Graph API aan te roepen 
> * De Microsoft Graph-API aanroepen 
> * Een gebruiker afmelden 

Wanneer u deze zelfstudie hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke Microsoft-accounts (waaronder outlook.com, live.com en anderen) en werk- of schoolaccounts van een bedrijf of organisatie die Azure Active Directory gebruikt.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="how-this-tutorial-works"></a>Hoe deze zelfstudie werkt

![Laat zien hoe de voorbeeld-app die door deze zelfstudie wordt gegenereerd, werkt](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

De app in deze zelfstudie zal gebruikers aanmelden en namens hen gegevens krijgen. Deze gegevens worden geopend via een beveiligde API (Microsoft Graph API) waarvoor autorisatie vereist is en wordt beschermd door het Microsoft-identiteitsplatform.

Met name:

* Uw app meldt zich aan bij de gebruiker via een browser of de Microsoft Authenticator en Intune Company Portal.
* De eindgebruiker accepteert de machtigingen die uw aanvraag heeft aangevraagd.
* Uw app krijgt een toegangstoken voor de Microsoft Graph-API.
* Het toegangstoken wordt opgenomen in het HTTP-verzoek tot de web-API.
* De Reactie van Microsoft Graph verwerken.

In dit voorbeeld wordt de Microsoft Authentication-bibliotheek voor Android (MSAL) gebruikt om Verificatie te implementeren: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 MSAL verlengt automatisch tokens, levert single sign-on (SSO) tussen andere apps op het apparaat en beheert het account(s).

### <a name="prerequisites"></a>Vereisten

* Deze tutorial vereist Android Studio versie 3.5+

## <a name="create-a-project"></a>Een project maken
Als u nog geen Android-toepassing hebt, volgt u deze stappen om een nieuw project in te stellen. 

1. Open Android Studio en selecteer **Een nieuw Android Studio-project starten**.
2. Selecteer **Basisactiviteit** en selecteer **Volgende**.
3. Geef uw toepassing een naam.
4. Sla de naam van het pakket op. U voert het later in in de Azure-portal.
5. Verander de taal van **Kotlin** naar **Java.**
6. Stel het **minimumAPI-niveau** in op **API 19** of hoger en klik op **Voltooien**.
7. Kies in de projectweergave **Project** in de vervolgkeuzelijst om bron- en niet-bronprojectbestanden `28`weer te geven, open **app/build.gradle** en stel in op `targetSdkVersion` .

## <a name="integrate-with-microsoft-authentication-library"></a>Integreren met Microsoft-verificatiebibliotheek 

### <a name="register-your-application"></a>Uw toepassing registreren

1. Ga naar de [Azure-portal.](https://aka.ms/MobileAppReg)
2. Open het [mes app-registraties](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) en klik op **+Nieuwe registratie**.
3. Voer een **naam** in voor uw app en klik **vervolgens, zonder** uri omleiden, op **Registreren**.
4. Selecteer **verificatie** > **+ Een platform** > **Toevoegen in**het gedeelte **Beheren** van het deelvenster dat wordt weergegeven. (Mogelijk moet u 'Overschakelen naar de nieuwe ervaring' bovenaan het blad selecteren om deze sectie te bekijken)
5. Voer de pakketnaam van uw project in. Als u de code hebt `com.azuresamples.msalandroidapp`gedownload, is deze waarde .
6. Klik in de sectie **Hash** van handtekening van de pagina **Uw Android-app configureren** op Een hash voor **ontwikkelingshandtekeningen genereren.** en kopieer de opdracht KeyTool om te gebruiken voor uw platform.

   > [!Note]
   > KeyTool.exe is geïnstalleerd als onderdeel van de Java Development Kit (JDK). U moet ook het openssl-gereedschap installeren om de opdracht KeyTool uit te voeren. Raadpleeg de [Android-documentatie over het genereren van een sleutel](https://developer.android.com/studio/publish/app-signing#generate-key) voor meer informatie. 

7. Voer de **hash van Signature** in die door KeyTool is gegenereerd.
8. Klik `Configure` op de **MSAL-configuratie** die op de **Android-configuratiepagina** wordt weergegeven en sla deze op, zodat u deze invoeren wanneer u uw app later configureert.  Klik op **Gereed**.

### <a name="configure-your-application"></a>Uw toepassing configureren 

1. Navigeer in het projectvenster van Android Studio naar **app\src\main\res.**
2. Klik met de rechtermuisknop op **res** en kies **Nieuwe** > **map**. Voer `raw` de nieuwe mapnaam in en klik op **OK**.
3. Maak in **app** > **src** > **main** > **res** > **raw** `auth_configbn_single_account.json` een nieuw JSON-bestand genaamd en plak de MSAL-configuratie die u eerder hebt opgeslagen. 

    Onder de omleiding URI, plakken: 
    ```json
      "account_mode" : "SINGLE",
    ```
    Uw config-bestand moet op dit voorbeeld lijken: 
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
   >In deze zelfstudie wordt alleen uitgelegd hoe u een app configureert in de modus Eén account. Bekijk de documentatie voor meer informatie over [één versus meerdere accountmodus](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) en [het configureren van uw app](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)
   
4. In **app** > **src** > **main** > **AndroidManifest.xml**, voeg de `BrowserTabActivity` activiteit hieronder toe aan de toepassingstekst. Met dit item kan Microsoft terugbellen naar uw toepassing nadat de verificatie is voltooid:

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

    Vervang de pakketnaam die u hebt `android:host=` geregistreerd in de Azure-portal voor de waarde.
    Vervang de sleutelhash die u hebt `android:path=` geregistreerd in de Azure-portal voor de waarde. De handtekeninghash mag **niet** worden gecodeerd met url's. Zorg ervoor dat `/` er een voorloop is aan het begin van uw handtekeninghash. 
    >[!NOTE]
    >De Package Name waarmee `android:host` u de waarde vervangt, moet er hetzelfde uitzien als: com.azuresamples.msalandroidapp De Signature Hash waarmee u uw `android:path` waarde vervangt, moet er hetzelfde uitzien als: /1wIqXSqBj7w+h11ZifsnqwgyKrY= U deze waarden ook vinden in het authenticatieblad van uw app-registratie. Houd er rekening mee dat uw omleiding URI er hetzelfde uitziet als: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Hoewel de handtekeninghash url is gecodeerd aan het einde **not** van deze waarde, `android:path` mag de handtekeninghash geen URL zijn die is gecodeerd in uw waarde. 

## <a name="use-msal"></a>MSAL gebruiken 

### <a name="add-msal-to-your-project"></a>MSAL toevoegen aan uw project

1. Navigeer in het android studio-projectvenster naar **app** > **src** > **build.gradle** en voeg het volgende toe: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version") 
    }
    ```
    [Meer over de Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Vereiste invoer 

Voeg het volgende toe aan de bovenkant van **app** > **src** > **main**> **java** > **com.example(yourapp)** > **MainActivity.java** 

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

## <a name="instantiate-publicclientapplication"></a>Instantiate PublicClientApplication
#### <a name="initialize-variables"></a>Variabelen initialiseren 
```java
private final static String[] SCOPES = {"File.Read"};
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

### <a name="oncreate"></a>opMaken
Raadpleeg `MainActivity` in de klasse de volgende methode onCreate() om MSAL te instantiëren met behulp van de `SingleAccountPublicClientApplication`.

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

### <a name="initializeui"></a>initialiserenUI
Luister naar knoppen en aanroepmethoden of log fouten dienovereenkomstig. 
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
> Als u zich afmeldt bij MSAL, worden alle bekende informatie over een gebruiker uit de toepassing verwijderd, maar de gebruiker heeft nog steeds een actieve sessie op zijn apparaat. Als de gebruiker zich opnieuw probeert aan te melden, ziet hij mogelijk een aanmeldings-gebruikersinterface, maar hoeft hij mogelijk zijn of haar referenties niet opnieuw in te voeren omdat de apparaatsessie nog steeds actief is. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Callback gebruikt voor interactieve verzoeken.

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
Callback gebruikt voor stille verzoeken 
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

De volgende code laat zien hoe u de GraphAPI aanroept met behulp van de Graph SDK. 

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
Als u uw gebruikersinterface wilt modelleren buiten deze zelfstudie, bieden de volgende methoden een handleiding voor het bijwerken van tekst en het luisteren naar knoppen.

#### <a name="updateui"></a>updateUI
Knoppen inschakelen/uitschakelen op basis van aanmeldingsstatus en ingestelde tekst.  
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
#### <a name="displayerror"></a>displayError
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
#### <a name="performoperationonsignout"></a>operationonsignout uitvoeren
Methode om tekst in de gebruikersinterface bij te werken om afmelding weer te geven. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Indeling 

Voorbeeldbestand `activity_main.xml` om knoppen en tekstvakken weer te geven. 

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

Bouw en implementeer de app op een testapparaat of emulator. U moet zich kunnen aanmelden en tokens voor Azure AD of persoonlijke Microsoft-accounts kunnen ontvangen.

Nadat u zich hebt aangemeld, geeft de app `/me` de gegevens weer die zijn geretourneerd vanuit het eindpunt van Microsoft Graph.

### <a name="consent"></a>Toestemming

De eerste keer dat een gebruiker zich aanmeldt bij uw app, wordt deze door de Identiteit van Microsoft gevraagd om in te stemmen met de gevraagde machtigingen. Sommige Azure AD-tenants hebben gebruikerstoestemming uitgeschakeld, waardoor beheerders toestemming moeten geven namens alle gebruikers. Om dit scenario te ondersteunen, moet u uw eigen tenant maken of toestemming van de beheerder ontvangen. 

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder, wanneer u niet meer nodig bent, het app-object dat u hebt gemaakt in de stap [Uw toepassing registreren.](#register-your-application)

## <a name="get-help"></a>Help opvragen

Ga naar [Help en ondersteuning](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) als u problemen hebt met deze zelfstudie of met het Microsoft-identiteitsplatform.

Help ons het Microsoft-identiteitsplatform te verbeteren. Vertel ons wat u ervan vindt door een korte enquête met twee vragen in te vullen.

> [!div class="nextstepaction"]
> [Enquête van microsoft-identiteitsplatform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
