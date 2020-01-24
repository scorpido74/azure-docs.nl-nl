---
title: Modus gedeeld apparaat gebruiken met MSAL Android | Azure
description: Meer informatie over het voorbereiden van een Android-apparaat voor uitvoering in de gedeelde modus en het uitvoeren van een firstline worker-app.
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
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 98882ad115ff977cfd8222c6055a436855f50c04
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701243"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Zelf studie: modus gedeeld apparaat gebruiken in uw Android-toepassing

> [!NOTE]
> Deze functie is beschikbaar voor openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="developer-guide"></a>Ontwikkelaarsgids

Deze hand leiding bevat ontwikkelaars richtlijnen voor het implementeren van de modus gedeeld apparaat in een Android-toepassing met behulp van de micro soft Authentication Library (MSAL). Zie de [MSAL Android-zelf studie](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) voor informatie over het integreren van MSAL met uw Android-app, het aanmelden van een gebruiker, het aanroepen van micro soft Graph en het afmelden van een gebruiker.

### <a name="download-the-sample"></a>Het voorbeeld downloaden

De [voorbeeld toepassing](https://github.com/Azure-Samples/ms-identity-android-java/) klonen vanuit github. Het voor beeld heeft de mogelijkheid om te werken in de [modus voor één of meerdere accounts](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>De MSAL-SDK toevoegen aan uw lokale maven-opslag plaats

Als u de voor beeld-app niet gebruikt, voegt u de MSAL-bibliotheek als afhankelijkheid toe in uw build. gradle-bestand, bijvoorbeeld:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Uw app configureren voor het gebruik van de modus gedeeld apparaat

Raadpleeg de [configuratie documentatie](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration) voor meer informatie over het instellen van het configuratie bestand.

Stel `"shared_device_mode_supported"` in op `true` in uw MSAL-configuratie bestand.

Mogelijk bent u niet van plan om de modus voor meerdere accounts te ondersteunen. Als u geen gedeeld apparaat gebruikt, kan de gebruiker zich bij de app aanmelden met meer dan één account tegelijk. Als dit het geval is, stelt u `"account_mode"` in op `"SINGLE"`. Dit zorgt ervoor dat uw app altijd `ISingleAccountPublicClientApplication`ontvangt en uw MSAL-integratie aanzienlijk vereenvoudigt. De standaard waarde van `"account_mode"` is `"MULTIPLE"`. het is dus belang rijk om deze waarde in het configuratie bestand te wijzigen als u de modus `"single account"` gebruikt.

Hier volgt een voor beeld van het bestand auth_config. json dat is opgenomen in de **app**>**main**>**Res**>**onbewerkte** map van de voor beeld-app:

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

### <a name="detect-shared-device-mode"></a>Modus gedeelde apparaten detecteren

Met de modus voor gedeelde apparaten kunt u Android-apparaten configureren die worden gedeeld door meerdere werk nemers, terwijl u een micro soft-identiteits beheer van het apparaat verstrekt. Werk nemers kunnen zich snel aanmelden bij hun apparaten en de klant gegevens bekijken. Wanneer de taken zijn voltooid met hun verschuiving of taak, kunnen ze zich met één klik afmelden bij alle apps op het gedeelde apparaat. het apparaat zal onmiddellijk gereed zijn voor gebruik door de volgende werk nemer.

Gebruik `isSharedDevice()` om te bepalen of een app wordt uitgevoerd op een apparaat dat zich in de modus voor gedeeld apparaat bevindt. Uw app kan deze vlag gebruiken om te bepalen of de UX dienovereenkomstig moet worden gewijzigd.

Hier volgt een code fragment dat laat zien hoe u `isSharedDevice()`kunt gebruiken.  Het is afkomstig uit de klasse `SingleAccountModeFragment` in de voor beeld-app:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Het PublicClientApplication-object initialiseren

Als u `"account_mode":"SINGLE"` instelt in het MSAL-configuratie bestand, kunt u het geretourneerde toepassings object veilig als een `ISingleAccountPublicCLientApplication`casten.

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

### <a name="detect-single-vs-multiple-account-mode"></a>Enkelvoudige versus meerdere account modus detecteren

Als u een app schrijft die alleen wordt gebruikt voor firstline-werk rollen op een gedeeld apparaat, raden we u aan om uw app te schrijven zodat deze alleen ondersteuning biedt voor de modus met één account. Dit omvat de meeste toepassingen die op de taak zijn gericht, zoals medische record-apps, factuur-apps en de meeste line-of-Business-Apps. Dit vereenvoudigt de ontwikkeling, omdat veel functies van de SDK niet hoeven te worden aangepast.

Als uw app meerdere accounts en een modus voor gedeelde apparaten ondersteunt, moet u een type controle en cast-conversie uitvoeren naar de juiste interface, zoals hieronder wordt weer gegeven.

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

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>De aangemelde gebruiker ophalen en bepalen of een gebruiker op het apparaat is gewijzigd

Met de methode `loadAccount` wordt het account van de aangemelde gebruiker opgehaald. De methode `onAccountChanged` bepaalt of de aangemelde gebruiker is gewijzigd en zo ja, opschonen:

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

### <a name="globally-sign-in-a-user"></a>Wereld wijd aanmelden bij een gebruiker

De volgende tekens in een gebruiker op het apparaat naar andere apps die gebruikmaken van MSAL met de verificator-app:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Een gebruiker wereld wijd afmelden

Het volgende wordt het aangemelde account verwijderd en de tokens in de cache gewist van niet alleen de app, maar ook van het apparaat dat zich in de modus voor gedeeld apparaat bevindt:

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

In de volgende stappen wordt beschreven hoe u uw toepassing instelt in de Azure Portal en hoe u uw apparaat in de modus voor gedeeld apparaat plaatst.

### <a name="register-your-application-in-azure-active-directory"></a>Uw toepassing registreren in Azure Active Directory

Registreer eerst uw toepassing in de Tenant van uw organisatie. Geef deze waarden hieronder op in auth_config. json zodat uw toepassing correct kan worden uitgevoerd.

Raadpleeg [uw toepassing registreren](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application)voor meer informatie over hoe u dit doet.

> [!NOTE]
> Wanneer u uw app registreert, gebruikt u de Snelstartgids aan de linkerkant en selecteert u **Android**. Hiermee wordt u naar een pagina geleid waar u wordt gevraagd de **pakket naam** en **hand tekening-hash** voor uw app op te geven. Dit is zeer belang rijk om ervoor te zorgen dat uw app-configuratie werkt. U ontvangt vervolgens een configuratie object dat u kunt gebruiken voor uw app en die u wilt knippen en plakken in uw auth_config. JSON-bestand.

![app-registratie scherm](media/tutorial-v2-shared-device-mode/register-app.png) moet u **deze wijziging voor mij maken** en vervolgens de waarden opgeven die door de Snelstartgids worden gevraagd in de Azure Portal. Als dat is gebeurd, worden alle configuratie bestanden gegenereerd die u nodig hebt.

![Venster app-configuratie gegevens](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Een tenant instellen

Voor test doeleinden stelt u het volgende in uw Tenant in: ten minste twee werk nemers, een Cloud apparaat beheerder en één globale beheerder. Stel in de Azure Portal de beheerder van de Cloud apparaat in door de organisatie rollen te wijzigen. Open in de Azure Portal uw organisatie rollen door **Azure Active Directory** > **rollen en beheerders** te selecteren > beheerder van het **Cloud apparaat**. Voeg de gebruikers toe die een apparaat in de gedeelde modus kunnen plaatsen.

## <a name="set-up-an-android-device-in-shared-mode"></a>Een Android-apparaat instellen in de gedeelde modus

### <a name="download-the-authenticator-app"></a>De verificator-app downloaden

Down load de Microsoft Authenticator-app uit de Google Play Store. Als u de app al hebt gedownload, controleert u of dit de meest recente versie is.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Verificator-app-instellingen & het apparaat in de Cloud registreren

Start de verificator-app en navigeer naar de pagina met het hoofd account. Zodra u de pagina **account toevoegen** ziet, bent u klaar om het apparaat te delen.

![Account scherm verificator toevoegen](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Ga naar het deel venster **instellingen** en gebruik de rechter menu balk. Selecteer **apparaatregistratie** onder **werk & School accounts**.
 
 ![Account scherm verificator toevoegen](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Wanneer u op deze knop klikt, wordt u gevraagd toegang te verlenen tot contact personen van het apparaat. Dit wordt veroorzaakt door de account integratie van Android op het apparaat. Kies **toestaan**.

 ![Account scherm verificator toevoegen](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

De beheerder van het Cloud apparaat moet hun e-mail adres van de organisatie invoeren onder **of registreren als een gedeeld apparaat**. Klik vervolgens op de knop **registreren als gedeeld apparaat** en voer de referenties in.

![scherm van apparaat registreren](media/tutorial-v2-shared-device-mode/register-device.png)

![aanmelden](media/tutorial-v2-shared-device-mode/sign-in.png)

Het apparaat bevindt zich nu in de gedeelde modus.

![scherm van apparaat registreren](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 Eventuele aanmeldingen en aanmeldingen op het apparaat zijn globaal, wat betekent dat ze van toepassing zijn op alle apps die zijn geïntegreerd met MSAL en Microsoft Authenticator op het apparaat. U kunt nu toepassingen implementeren op het apparaat waarop functies van de modus gedeelde apparaten worden gebruikt.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Het gedeelde apparaat in de Azure Portal weer geven

Zodra u een apparaat in de gedeelde modus hebt geplaatst, wordt dit in uw organisatie bekend en wordt het in de Tenant van uw organisatie bijgehouden. U kunt uw gedeelde apparaten weer geven door te kijken naar het **jointype** op de blade Azure Active Directory van uw Azure Portal.

![De Blade alle apparaten in de Azure Portal](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>De voor beeld-app uitvoeren

De voorbeeld toepassing is een eenvoudige app waarmee de Graph API van uw organisatie wordt gebeld. n eerste keer uitvoeren u wordt gevraagd om toestemming te geven, omdat de toepassing nieuw is voor uw werknemers account.

![Venster app-configuratie gegevens](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over gedeelde modus op de [modus gedeeld apparaat voor Android-apparaten](shared-device-mode.md)