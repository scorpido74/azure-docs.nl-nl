---
title: Gedeelde apparaatmodus gebruiken met MSAL Android | Azure
description: Meer informatie over hoe u een Android-apparaat voorbereidt om in de gedeelde modus te werken en een eerstelijns werknemer-app uit te voeren.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b2f74d2d441007f195abd38ca26ca7fa73605318
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886429"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Zelfstudie: De modus voor gedeelde apparaten gebruiken in uw Android-toepassing

> [!NOTE]
> Deze functie is beschikbaar voor openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="developer-guide"></a>Ontwikkelaarsgids

Deze handleiding biedt richtlijnen voor ontwikkelaars om de modus voor gedeelde apparaten in een Android-toepassing te implementeren met behulp van de Microsoft Authentication Library (MSAL). Bekijk de [MSAL Android-zelfstudie](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) om te zien hoe u MSAL integreert met uw Android-app, u aanmelden bij een gebruiker, een Grafiek van Microsoft bellen en een gebruiker afmelden.

### <a name="download-the-sample"></a>Het voorbeeld downloaden

Kloon de [voorbeeldtoepassing](https://github.com/Azure-Samples/ms-identity-android-java/) van GitHub. Het voorbeeld heeft de mogelijkheid om te werken in [de modus met één of meerdere account .](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account)

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Voeg de MSAL SDK toe aan uw lokale Maven-repository

Als u de voorbeeld-app niet gebruikt, voegt u de MSAL-bibliotheek toe als afhankelijkheid in uw build.gradle-bestand, zoals:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Uw app configureren om de modus gedeeld apparaat te gebruiken

Raadpleeg de [configuratiedocumentatie](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration) voor meer informatie over het instellen van uw config-bestand.

Ingesteld `"shared_device_mode_supported"` `true` op in uw MSAL-configuratiebestand.

Het is mogelijk dat u niet van plan bent om de modus met meerdere accounten te ondersteunen. Dat kan zijn als u geen gedeeld apparaat gebruikt en de gebruiker zich met meer dan één account tegelijk bij de app kan aanmelden. Zo ja, `"account_mode"` `"SINGLE"`stel dan in op . Dit garandeert dat uw `ISingleAccountPublicClientApplication`app altijd uw MSAL-integratie krijgt en aanzienlijk vereenvoudigt. De standaardwaarde `"account_mode"` `"MULTIPLE"`van is , dus het is belangrijk om deze waarde `"single account"` te wijzigen in het config-bestand als u de modus gebruikt.

Hier is een voorbeeld van het bestand auth_config.json in **de**>hoofdmap van de>**app-map** **main**>**van**de voorbeeld-app:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Modus voor gedeeld apparaat detecteren

Met de modus Voor gedeelde apparaten u Android-apparaten configureren die door meerdere werknemers worden gedeeld, terwijl u het beheer van het apparaat met microsoft-identiteit beheren. Werknemers kunnen zich aanmelden bij hun apparaten en snel toegang krijgen tot klantgegevens. Wanneer ze klaar zijn met hun shift of taak, kunnen ze zich met één klik afmelden voor alle apps op het gedeelde apparaat en is het apparaat onmiddellijk klaar voor de volgende werknemer om te gebruiken.

Gebruiken `isSharedDevice()` om te bepalen of een app wordt uitgevoerd op een apparaat dat zich in de modus voor gedeelde apparaten bevindt. Uw app kan deze vlag gebruiken om te bepalen of de UX dienovereenkomstig moet worden gewijzigd.

Hier is een codefragment dat laat `isSharedDevice()`zien hoe u .  Het is van `SingleAccountModeFragment` de klasse in de voorbeeld-app:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Het object PublicClientApplication initialiseren

Als u `"account_mode":"SINGLE"` het MSAL-config-bestand instelt, u `ISingleAccountPublicCLientApplication`het geretourneerde toepassingsobject veilig casten als een.

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>Eén versus meerdere accountmodus detecteren

Als u een app schrijft die alleen wordt gebruikt voor eerstelijnswerknemers op een gedeeld apparaat, raden we u aan uw app te schrijven om alleen de modus met één account te ondersteunen. Dit omvat de meeste toepassingen die taakgericht zijn, zoals apps voor medische dossiers, factuur-apps en de meeste zakelijke apps. Dit zal uw ontwikkeling vereenvoudigen omdat veel functies van de SDK niet hoeven te worden ondergebracht.

Als uw app meerdere accounts en de modus gedeeld apparaat ondersteunt, moet u een typecontrole uitvoeren en naar de juiste interface casten, zoals hieronder wordt weergegeven.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>De aangemelde gebruiker laten inloggen en bepalen of een gebruiker is gewijzigd op het apparaat

De `loadAccount` methode haalt het account van de aangemelde gebruiker op. De `onAccountChanged` methode bepaalt of de aangemelde gebruiker is gewijzigd en zo ja, ruimt op:

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>Meld je wereldwijd aan bij een gebruiker

De volgende tekens in een gebruiker op het apparaat naar andere apps die MSAL gebruiken met de Authenticator-app:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Een gebruiker wereldwijd uitloggen

Het volgende verwijdert het aangemelde account en verwijdert tokens in de cache, niet alleen uit de app, maar ook van het apparaat dat zich in de modus gedeeld apparaat bevindt:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Beheerdershandleiding

In de volgende stappen wordt beschreven dat u uw toepassing in de Azure-portal instelt en uw apparaat in de modus voor gedeelde apparaten plaatst.

### <a name="register-your-application-in-azure-active-directory"></a>Uw toepassing registreren in Azure Active Directory

Registreer eerst uw sollicitatie bij uw organisatiehuurder. Geef deze waarden hieronder op in auth_config.json om uw toepassing correct te laten werken.

Voor informatie over hoe dit te doen, verwijzen wij u naar [Registreer uw aanvraag](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application).

> [!NOTE]
> Wanneer u uw app registreert, gebruikt u de quickstart-gids aan de linkerkant en selecteert u **Android.** Dit leidt u naar een pagina waar u wordt gevraagd om de **packagename** en **signature hash** voor uw app op te geven. Deze zijn erg belangrijk om ervoor te zorgen dat uw app-configuratie werkt. U ontvangt dan een configuratieobject dat u voor uw app gebruiken en dat u in uw auth_config.json-bestand knippen en plakken.

![App-registratiescherm](media/tutorial-v2-shared-device-mode/register-app.png) U moet **Deze wijziging voor mij wijzigen** selecteren en vervolgens de waarden opgeven waar de quickstart om vraagt in de Azure-portal. Als dat klaar is, genereren we alle configuratiebestanden die u nodig hebt.

![App config info scherm](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Een tenant instellen

Stel voor testdoeleinden het volgende in uw tenant in: ten minste twee werknemers, één cloudapparaatbeheerder en één globale beheerder. Stel in de Azure-portal de beheerder van het cloudapparaat in door organisatierollen te wijzigen. In de Azure-portal u toegang krijgen tot uw organisatierollen door **Azure Active Directory** > **Rollen en Administrators** > **Cloud Device Administrator**te selecteren. Voeg de gebruikers toe die een apparaat in de gedeelde modus kunnen plaatsen.

## <a name="set-up-an-android-device-in-shared-mode"></a>Een Android-apparaat instellen in gedeelde modus

### <a name="download-the-authenticator-app"></a>Download de Authenticator App

Download de Microsoft Authenticator-app in de Google Play Store. Als u de app al hebt gedownload, moet u ervoor zorgen dat het de nieuwste versie is.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Instellingen voor authenticator-apps & het registreren van het apparaat in de cloud

Start de Authenticator-app en navigeer naar de hoofdaccountpagina. Zodra u de pagina **Account toevoegen** ziet, bent u klaar om het apparaat te laten delen.

![Authenticator accountscherm toevoegen](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Ga naar het deelvenster **Instellingen** met de rechtermenubalk. Selecteer **Apparaatregistratie** onder **Werk & School-accounts**.

 ![Authenticator accountscherm toevoegen](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Wanneer u op deze knop klikt, wordt u gevraagd om toegang tot apparaatcontacten te autoriseren. Dit is te wijten aan android's account integratie op het apparaat. Kies **Toestaan**.

 ![Authenticator accountscherm toevoegen](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

De beheerder van cloudapparaten moet zijn organisatie-e-mail invoeren onder **Of registreren als gedeeld apparaat**. Klik vervolgens op de **knop Register als gedeeld apparaat** en voer hun referenties in.

![scherm van het registerapparaat](media/tutorial-v2-shared-device-mode/register-device.png)

![aanmelden](media/tutorial-v2-shared-device-mode/sign-in.png)

Het apparaat bevindt zich nu in de gedeelde modus.

![scherm van het registerapparaat](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 Alle aanmeldingen en aanmeldingen op het apparaat zijn globaal, wat betekent dat ze van toepassing zijn op alle apps die zijn geïntegreerd met MSAL en Microsoft Authenticator op het apparaat. U nu toepassingen implementeren op het apparaat met functies voor de modus gedeelde apparaten.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Het gedeelde apparaat weergeven in de Azure-portal

Zodra u een apparaat in de gedeelde modus hebt geplaatst, wordt het bekend bij uw organisatie en wordt het bijgehouden in uw organisatietenant. U uw gedeelde apparaten bekijken door het **jointype** te bekijken in het Azure Active Directory-blad van uw Azure-portal.

![Alle apparaten blade in de Azure portal](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>De voorbeeld-app uitvoeren

De voorbeeldtoepassing is een eenvoudige app die de Graph API van uw organisatie aanroept. Bij de eerste run wordt u gevraagd om toestemming te geven omdat de toepassing nieuw is voor uw werknemersaccount.

![App config info scherm](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de gedeelde modus in [de modus Gedeeld apparaat voor Android-apparaten](msal-android-shared-devices.md)