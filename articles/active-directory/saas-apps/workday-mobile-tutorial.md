---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met mobiele apps van Workday | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en mobiele apps van Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754157"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met mobiele apps van Workday

In deze zelfstudie leert u hoe u Azure Active Directory (Azure AD), voorwaardelijke toegang en Intune integreert met de mobiele apps van Workday. Wanneer u de mobiele apps van Workday integreert met Microsoft, kunt u het volgende doen:

* Garanderen dat apparaten voldoen aan uw beleid voordat ze worden aangemeld.
* Besturingselementen toevoegen aan apps van Beautiful.ai om ervoor te zorgen dat gebruikers veilig toegang krijgen tot bedrijfsgegevens. 
* In Azure AD beheren wie toegang heeft tot Workday.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Workday.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Workday integreren met Azure AD
* Zelfstudie: [Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u het beleid voor voorwaardelijke toegang van Microsoft en Intune met de mobiele apps van Workday.

* Gefedereerde apps van Workday kunnen nu worden geconfigureerd met Azure AD om eenmalige aanmelding mogelijk te maken. Volg [deze](workday-tutorial.md) link voor meer informatie.

> [!NOTE] 
> Workday biedt geen ondersteuning voor beleid voor app-beveiliging van Intune. U moet Mobile Device Management gebruiken om gebruik te maken van voorwaardelijke toegang.


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>Zorgen dat gebruikers toegang hebben tot de mobiele Workday-app:

Configureer Workday om toegang tot hun mobiele apps toe te staan. U moet de onderstaande beleidsregels configureren voor mobiele apps.

U kunt dit doen door deze instructies te volgen:

1. Open het rapport Domain Security Policies for Functional Area.
2. Selecteer een beveiligingsbeleid.
    * Mobile Usage - Android
    * Mobile Usage - iPad
    * Mobile Usage - iPhone
3. Klik op Edit Permissions.
4. Schakel het selectievakje View of Modify in om de beveiligingsgroepen toegang te geven tot beveiligbare items van het rapport of de taak.
5. Schakel het selectievakje Get of Put in om de beveiligingsgroepen toegang te geven tot integratie en beveiligbare items van het rapport of de taak.

Activeer wijzigingen van het beveiligingsbeleid die in behandeling zijn door de taak **Activate Pending Security Policy Changes** uit te voeren.

## <a name="open-workday-login-page-in-mobile-browser"></a>Aanmeldingspagina van Workday openen in mobiele browser:

Als u voorwaardelijke toegang wilt toep assen op de mobiele app van Workday, moet de app worden geopend in een externe browser. U kunt dit doen door het selectievakje **Enable Mobile Browser SSO for Native Apps** in **Edit Tenant Setup - Security** in te schakelen. Hiervoor moet een door Intune goedgekeurde browser worden geïnstalleerd op het apparaat voor iOS en in het werkprofiel voor Android.

![Aanmelden bij mobiele browser](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang instellen:

Dit beleid is alleen van invloed op aanmelding bij een iOS- of Android-apparaat. Als u het beleid wilt uitbreiden naar alle platforms, selecteert u **Any Device.** Dit beleid vereist dat het apparaat compatibel is met het beleid. Dit wordt gecontroleerd via Microsoft Intune. Aangezien in Android werkprofielen worden gebruikt, moet dit ervoor zorgen dat gebruikers zich alleen kunnen aanmelden bij Workday (web of app) als ze zich aanmelden via hun werkprofiel en de app hebben geïnstalleerd via de Intune-bedrijfsportal. Er is één extra stap voor iOS nodig om ervoor te zorgen dat dezelfde situatie van toepassing is. Hieronder volgen enkele schermopnamen van de instellingen voor voorwaardelijke toegang.

**Beautiful.ai ondersteunt de volgende besturingselementen voor toegang:**
* Multi-Factor Authentication vereisen
* Vereisen dat het apparaat moet worden gemarkeerd als compatibel

**De Workday-app biedt geen ondersteuning voor het volgende:**
* Goedgekeurde client-apps vereisen
* Beleid voor app-beveiliging vereisen (preview)

Als u de **Workday** -app wilt instellen als een **beheerd apparaat** , voert u de volgende stappen uit:

![Beleid voor voorwaardelijke toegang instellen](./media/workday-tutorial/managed-devices-only.png)

1. Klik op **Startpagina > Microsoft Intune > Beleid voor voorwaardelijke toegang > Alleen beheerde apparaten**. 

1. Geef op de pagina **Alleen beheerde apparaten** de waarde `Managed Devices Only` op voor **Naam** en klik op **Cloud-apps of -acties**.

1. Voer de volgende stappen uit in **Cloud-apps of -acties**.

    a. Stel **Selecteer waarop dit beleid van toepassing is** in op **Cloud-apps**.

    b. Klik bij Opnemen op **Apps selecteren**.

    c. Kies **Workday** in de selectielijst.

    d. Klik op **Gereed**.

1. Schakel **Beleid inschakelen** in.

1. Klik op **Opslaan**.

Voer in het deelvenster **Verlenen** de volgende stappen uit:

![Beleid voor voorwaardelijke toegang tot Workday instellen](./media/workday-tutorial/managed-devices-only-2.png)

1. Klik op **Startpagina > Microsoft Intune > Beleid voor voorwaardelijke toegang > Alleen beheerde apparaten**. 

1. Geef op de pagina **Alleen beheerde apparaten** de waarde `Managed Devices Only` op voor **Naam** en klik op **Toegangsbeheer > Verlenen**.

1. Voer deze stappen uit op de pagina **Verlenen**.

    a. Selecteer de besturingselementen die moeten worden afgedwongen voor **Toegang verlenen**.

    b. Schakel het selectievakje **Vereisen dat het apparaat moet worden gemarkeerd als compatibel** in.

    c. Selecteer **Een van de geselecteerde besturingselementen vereisen**.

    d. Klik op **Selecteren**.

1. Schakel **Beleid inschakelen** in.

1. Klik op **Opslaan**.

## <a name="set-up-device-compliance-policy"></a>Nalevingsbeleid voor apparaten instellen:

Om ervoor te zorgen dat iOS-apparaten alleen kunnen worden aangemeld via een door MDM beheerde Workday-app, moet u de App Store-app blokkeren door **com.workday.workdayapp** toe te voegen aan de lijst met beperkte apps. Dit zorgt ervoor dat alleen apparaten toegang hebben tot Workday waarop de Workday-app is geïnstalleerd via de bedrijfsportal. Via een browser hebben ze alleen toegang tot Workday als het apparaat wordt beheerd door Intune en er een beheerde browser wordt gebruikt.

![Nalevingsbeleid van Workday instellen voor apparaten](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>Configuratiebeleidsregels voor Microsoft Intune-app instellen:

| Scenario | Sleutel-waardeparen |
|----------------------------------------------------------------------------------------   |-----------|
| Automatisch de velden Tenant en Webadres invullen voor:<br>● Workday op Android wanneer u Android inschakelt voor werkprofielen.<br>● Workday op iPad en iPhone.     | Gebruik deze waarden om uw tenant te configureren: <br>● Configuratiesleutel = UserGroupCode<br>●   Waardetype = Tekenreeks <br>●   Configuratiewaarde = Naam van uw tenant. Bijvoorbeeld: gms<br>Gebruik deze waarden om uw webadres te configureren:<br>●    Configuratiesleutel = AppServiceHost<br>● Waardetype = Tekenreeks<br>●    Configuratiewaarde = De basis-URL voor uw tenant. Voorbeeld: https://www.myworkday.com                              |   |
| Deze acties uitschakelen voor Workday op iPad en iPhone:<br>●    Knippen, kopiëren en plakken<br>●   Afdrukken                       | Stel de waarde (Booleaans) in op False voor deze sleutels om de functionaliteit uit te schakelen:<br>● AllowCutCopyPaste<br>●  AllowPrint  |
| Schermopnamen uitschakelen voor Workday op Android. |Stel de waarde (Booleaans) in op False voor de sleutel AllowScreenshots om de functionaliteit uit te schakelen.|
| Voorgestelde updates uitschakelen voor uw gebruikers.|Stel de waarde (Booleaans) in op False voor de sleutel AllowSuggestedUpdates om de functionaliteit uit te schakelen.|
|De URL van de App Store aanpassen om mobiele gebruikers naar de App Store van uw keuze te leiden.|Gebruik deze waarden om de URL van de App Store te wijzigen:<br>● Configuratiesleutel = AppUpdateURL<br>●   Waardetype = Tekenreeks<br> ●   Configuratiewaarde = URL van App Store|
|       |


## <a name="ios-configuration-policies"></a>iOS-configuratiebeleidsregels:

1. Ga naar https://portal.azure.com/ en meld u aan.
2. Zoek **Intune** of klik op de widget in de lijst.
3. Ga naar **Client-apps -> Apps -> App-configuratiebeleid -> + Toevoegen -> Beheerde apparaten**.
4. Voer een naam in.
5. Kies **iOS/iPadOS** onder **Platform**.
6. Kies onder **Gekoppelde app** de Workday for iOS-app die u hebt toegevoegd.
7. Klik op **Configuratie-instellingen** en kies **XML-gegevens invoeren** onder **Indeling configuratie-instellingen**.
8. Hier ziet u een voorbeeld van een XML-bestand. Voeg de configuraties toe die u wilt toepassen. Vervang **STRING_VALUE** door de tekenreeks die u wilt gebruiken. Vervang `<true />` of `<false />` door `<true />` of `<false />`. Als u geen configuratie toevoegt, werkt deze met de instelling True.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
9. Klik op Add.
10. Vernieuw de pagina en klik op het zojuist gemaakte beleid.
11. Klik op Toewijzingen en kies op wie u de app wilt toepassen.
12. Klik op Opslaan.

## <a name="android-configuration-policies"></a>Android-configuratiebeleidsregels:

1. Ga naar `https://portal.azure.com/` en meld u aan.
2. Zoek **Intune** of klik op de widget in de lijst.
3. Ga naar **Client-apps -> Apps -> App-configuratiebeleid -> + Toevoegen -> Beheerde apparaten**.
5. Voer een naam in. 
6. Kies **Android** onder **Platform**.
7. Kies onder **Gekoppelde app** de Workday for Android-app die u hebt toegevoegd.
8. Klik op **Configuratie-instellingen** en kies **JSON-gegevens invoeren** onder **Indeling configuratie-instellingen**.

