---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176723"
---
In het vorige voorbeeld werd een standaardaanmelding getoond, waarbij de client contact moet opnemen met zowel de identiteitsprovider als de back-end Azure-service telkens wanneer de app wordt gestart. Deze methode is inefficiënt en u gebruiksgerelateerde problemen hebben als veel klanten tegelijkertijd proberen uw app te starten. Een betere aanpak is om het autorisatietoken dat door de Azure-service is geretourneerd, in de cache te bewaren en dit eerst te gebruiken voordat u een op de provider gebaseerde aanmelding gebruikt.

> [!NOTE]
> U het token dat is uitgegeven door de back-end Azure-service in de cache opslaan, ongeacht of u verificatie met clientbeheerof servicebeheer gebruikt. Deze zelfstudie maakt gebruik van servicebeheerverificatie.
>
>

1. Open het bestand ToDoActivity.java en voeg de volgende importinstructies toe:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Voeg de volgende `ToDoActivity` leden toe aan de klasse.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. Voeg in het bestand ToDoActivity.java de `cacheUserToken` volgende definitie voor de methode toe.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    Met deze methode worden de gebruikersnaam en het token opgeslagen in een voorkeursbestand dat als privé is gemarkeerd. Dit moet de toegang tot de cache beschermen, zodat andere apps op het apparaat geen toegang hebben tot het token. De voorkeur is sandboxed voor de app. Als iemand echter toegang krijgt tot het apparaat, is het mogelijk dat hij of zij via andere middelen toegang krijgt tot de tokencache.

   > [!NOTE]
   > U het token verder beveiligen met versleuteling, als tokentoegang tot uw gegevens als zeer gevoelig wordt beschouwd en iemand toegang tot het apparaat kan krijgen. Een volledig veilige oplossing valt echter buiten het bereik van deze zelfstudie en is afhankelijk van uw beveiligingsvereisten.
   >
   >

4. Voeg in het bestand ToDoActivity.java de `loadUserTokenCache` volgende definitie voor de methode toe.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. Vervang in het *Bestand ToDoActivity.java* de `authenticate` volgende `onActivityResult` methoden, die een tokencache gebruiken. Wijzig de aanmeldingsprovider als u een ander account dan Google wilt gebruiken.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, sign in and create a token cache
        else
        {
            // Sign in using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the sign-in request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
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

6. Bouw de app en test verificatie met een geldig account. Doe het minstens twee keer. Tijdens de eerste run moet u een prompt ontvangen om u aan te melden en de tokencache te maken. Daarna probeert elke run de tokencache voor verificatie te laden. U hoeft zich niet aan te melden.
