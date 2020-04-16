---
title: Aan de slag met aangepaste beleidsregels
titleSuffix: Azure AD B2C
description: Meer informatie over hoe u aan de slag met aangepaste beleidsregels in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 856bd6c2a3546a438293e89a0b576e1392d9c6a5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407281"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Aan de slag met aangepaste beleidsregels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Aangepaste beleidsregels](custom-policy-overview.md) zijn configuratiebestanden die het gedrag van uw Azure Active Directory B2C -tenant (Azure AD B2C) definiëren. In dit artikel maakt u een aangepast beleid dat lokale aanmelding of aanmelding bij lokale accountondersteuning ondersteunt met behulp van een e-mailadres en wachtwoord. U bereidt ook uw omgeving voor op het toevoegen van identiteitsproviders.

## <a name="prerequisites"></a>Vereisten

- Als u er nog geen hebt, [maakt u een Azure AD B2C-tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.
- [Registreer uw toepassing](tutorial-register-applications.md) in de tenant die u hebt gemaakt, zodat deze kan communiceren met Azure AD B2C.
- Voer de stappen uit in [Aanmelden instellen en aanmelden met een Facebook-account](identity-provider-facebook.md) om een Facebook-toepassing te configureren. Hoewel een Facebook-toepassing niet vereist is voor het gebruik van aangepast beleid, wordt deze in deze walkthrough gebruikt om aan te tonen dat sociale aanmeldingen in een aangepast beleid mogelijk zijn.

## <a name="add-signing-and-encryption-keys"></a>Ondertekenings- en versleutelingssleutels toevoegen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Zoek en selecteer **Azure AD B2C**in de Azure-portal .
1. Selecteer op de overzichtspagina onder **Beleid**de optie **Identity Experience Framework**.

### <a name="create-the-signing-key"></a>De ondertekeningssleutel maken

1. Selecteer **Beleidssleutels** en selecteer **Vervolgens Toevoegen**.
1. Kies **Options**voor `Generate`Opties .
1. In **Naam** `TokenSigningKeyContainer`, voer . Het voorvoegsel `B2C_1A_` kan automatisch worden toegevoegd.
1. Selecteer **RSA** **voor sleuteltype**.
1. Selecteer **Handtekening**voor **Sleutelgebruik**.
1. Selecteer **Maken**.

### <a name="create-the-encryption-key"></a>De versleutelingssleutel maken

1. Selecteer **Beleidssleutels** en selecteer **Vervolgens Toevoegen**.
1. Kies **Options**voor `Generate`Opties .
1. In **Naam** `TokenEncryptionKeyContainer`, voer . Het voorvoegsel `B2C_1A`_ kan automatisch worden toegevoegd.
1. Selecteer **RSA** **voor sleuteltype**.
1. Selecteer **Versleuteling** **voor sleutelgebruik**.
1. Selecteer **Maken**.

### <a name="create-the-facebook-key"></a>De Facebook-toets maken

Voeg het [app-geheim](identity-provider-facebook.md) van je Facebook-applicatie toe als beleidssleutel. U het app-geheim van de toepassing die u hebt gemaakt gebruiken als onderdeel van de vereisten van dit artikel.

1. Selecteer **Beleidssleutels** en selecteer **Vervolgens Toevoegen**.
1. Kies **Options**voor `Manual`Opties .
1. Voer **Name**voor `FacebookSecret`Naam . Het voorvoegsel `B2C_1A_` kan automatisch worden toegevoegd.
1. Voer **in het geheim**het *app-geheim* van je Facebook-applicatie in van developers.facebook.com. Deze waarde is het geheim, niet de toepassings-ID.
1. Selecteer **Handtekening**voor **Sleutelgebruik**.
1. Selecteer **Maken**.

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework-toepassingen registreren

Azure AD B2C vereist dat u twee toepassingen registreert die worden gebruikt om gebruikers aan te melden en in te loggen met lokale accounts: *IdentityExperienceFramework,* een web-API en *ProxyIdentityExperienceFramework*, een native app met gedelegeerde toestemming voor de IdentityExperienceFramework-app. Uw gebruikers kunnen zich aanmelden met een e-mailadres of gebruikersnaam en een wachtwoord om toegang te krijgen tot uw tenant-geregistreerde toepassingen, waardoor een 'lokaal account' wordt gemaakt. Lokale accounts bestaan alleen in uw Azure AD B2C-tenant.

U hoeft deze twee toepassingen slechts één keer te registreren in uw Azure AD B2C-tenant.

### <a name="register-the-identityexperienceframework-application"></a>De IdentityExperienceFramework applicatie registreren

Als u een toepassing wilt registreren in uw Azure AD B2C-tenant, u de ervaring **App-registraties (Legacy)** of onze nieuwe uniforme **app-registratie (Preview)-ervaring** gebruiken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek en selecteer **Azure Active Directory**in de Azure-portal.
1. Selecteer in het menu **overzicht van Azure Active Directory** onder **Beheren**de optie **App-registraties (Legacy)**.
1. Selecteer **Nieuwe toepassing registreren**.
1. Voer **Name**voor `IdentityExperienceFramework`Naam .
1. Kies **Web-app/API**voor **Toepassingstype**.
1. Voer voor **Aanmeldings-URL**, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`waar `your-tenant-name` is uw Azure AD B2C-tenantdomeinnaam. Alle URL's moeten nu [b2clogin.com](b2clogin.md)gebruiken.
1. Selecteer **Maken**. Nadat deze is gemaakt, kopieert u de toepassings-id en slaat u deze op om deze later te gebruiken.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Vervolgens Nieuwe registratie**.
1. Voer **Name**voor `IdentityExperienceFramework`Naam .
1. Selecteer onder **Ondersteunde accounttypen** **alleen Accounts in deze organisatiemap**.
1. Selecteer **onder URI omleiden** **en**voer vervolgens de `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`domeinnaam van `your-tenant-name` uw Azure AD B2C-tenant in.
1. Schakel **onder Machtigingen**het selectievakje *Beheerderstoestemming verlenen voor openid en offline_access machtigingen* in.
1. Selecteer **Registreren**.
1. Neem de **toepassings-id (client) op** voor gebruik in een latere stap.

Stel vervolgens de API bloot door een bereik toe te voegen:

1. Selecteer **onder Beheren**de optie Een API **blootleggen**.
1. Selecteer **Een bereik toevoegen**en selecteer Opslaan en ga **door** met het accepteren van de standaard-toepassings-ID URI.
1. Voer de volgende waarden in om een bereik te maken waarmee aangepaste beleidsuitvoering in uw Azure AD B2C-tenant mogelijk is:
    * **Naam van het toepassingsgebied**:`user_impersonation`
    * **Weergavenaam beheerderstoestemming:**`Access IdentityExperienceFramework`
    * **Beschrijving van de toestemming van beheerders:**`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Bereik **toevoegen selecteren**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>De proxyidentityExperienceFramework-toepassing registreren

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Selecteer Nieuwe **toepassingsregistratie**selecteren in **App-registratie (Legacy).**
1. Voer **Name**voor `ProxyIdentityExperienceFramework`Naam .
1. Kies **Voor Toepassingstype** **Native**.
1. Voer voor Omleiding `myapp://auth` **URI**.
1. Selecteer **Maken**. Nadat deze is gemaakt, kopieert u de toepassings-id en slaat u deze op om deze later te gebruiken.
1. Selecteer **Instellingen**, selecteer vervolgens **Vereiste machtigingen**en selecteer Vervolgens **Toevoegen**.
1. Kies **Selecteer Een API**selecteren, zoek naar en selecteer **IdentityExperienceFramework**en klik op **Selecteren**.
1. Schakel het selectievakje naast **Access IdentityExperienceFramework**in , klik op **Selecteren**en klik vervolgens op **Gereed**.
1. Selecteer **Machtigingen verlenen**en bevestig vervolgens door **Ja**te selecteren .

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Vervolgens Nieuwe registratie**.
1. Voer **Name**voor `ProxyIdentityExperienceFramework`Naam .
1. Selecteer onder **Ondersteunde accounttypen** **alleen Accounts in deze organisatiemap**.
1. Gebruik onder **Uri omleiden**de vervolgkeuzelijst om **Openbare client/native (mobile & desktop)** te selecteren.
1. Voer voor Omleiding `myapp://auth` **URI**.
1. Schakel **onder Machtigingen**het selectievakje *Beheerderstoestemming verlenen voor openid en offline_access machtigingen* in.
1. Selecteer **Registreren**.
1. Neem de **toepassings-id (client) op** voor gebruik in een latere stap.

Geef vervolgens aan dat de toepassing moet worden behandeld als een openbare client:

1. Selecteer onder **Beheren**de optie **Verificatie**.
1. Selecteer **Probeer de nieuwe ervaring** uit (indien weergegeven).
1. Schakel **onder Geavanceerde instellingen** **Treat-toepassing in als een openbare client** (selecteer **Ja).**
1. Selecteer **Opslaan**.

Nu machtigingen verlenen aan de API-scope die u eerder in de *IdentityExperienceFramework-registratie* hebt weergegeven:

1. Selecteer **API-machtigingen** **onder Beheren**.
1. Selecteer **Onder Geconfigureerde machtigingen**de optie Een machtiging **toevoegen**.
1. Selecteer het tabblad **Mijn API's** en selecteer vervolgens de toepassing **IdentityExperienceFramework.**
1. Selecteer **onder Machtiging**het **user_impersonation** bereik dat u eerder hebt gedefinieerd.
1. Selecteer **Machtigingen toevoegen**. Zoals aangegeven, wacht een paar minuten voordat u doorgaat naar de volgende stap.
1. Selecteer **Toestemming voor beheerders verlenen voor (uw tenantnaam)**.
1. Selecteer uw momenteel aangemelde beheerdersaccount of meld u aan met een account in uw Azure AD B2C-tenant die ten minste de functie *voor cloudtoepassingsbeheerder* is toegewezen.
1. Selecteer **Accepteren**.
1. Selecteer **Vernieuwen**en controleer vervolgens of 'Verleend voor ..." wordt weergegeven onder **Status** voor beide scopes. Het kan enkele minuten duren voordat de machtigingen worden doorgegeven.

* * *

## <a name="custom-policy-starter-pack"></a>Startpakket voor aangepaste beleidsregels

Aangepaste beleidsregels zijn een set XML-bestanden die u uploadt naar uw Azure AD B2C-tenant om technische profielen en gebruikersreizen te definiëren. We bieden startpakketten met verschillende vooraf gebouwde beleidsregels om u snel op weg te helpen. Elk van deze startpakketten bevat het kleinste aantal technische profielen en gebruikersreizen die nodig zijn om de beschreven scenario's te bereiken:

- **LocalAccounts** - Hiermee u alleen lokale accounts gebruiken.
- **SocialAccounts** - Maakt het gebruik van sociale (of federatieve) accounts alleen mogelijk.
- **SocialAndLocalAccounts** - Maakt het gebruik van zowel lokale als sociale accounts mogelijk.
- **SocialAndLocalAccountsWithMFA** - Maakt sociale, lokale en multi-factor authenticatie opties mogelijk.

Elk startpakket bevat:

- **Basisbestand** - Er zijn weinig wijzigingen nodig aan de basis. Voorbeeld: *TrustFrameworkBase.xml*
- **Extensiebestand** - In dit bestand worden de meeste configuratiewijzigingen aangebracht. Voorbeeld: *TrustFrameworkExtensions.xml*
- **Relying party files** - Taakspecifieke bestanden die door uw toepassing worden aangeroepen. Voorbeelden: *signUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

In dit artikel bewerkt u de xml-aangepaste beleidsbestanden in het startpakket **SocialAndLocalAccounts.** Als u een XML-editor nodig hebt, probeert u [Visual Studio Code](https://code.visualstudio.com/download), een lichtgewicht cross-platform editor.

### <a name="get-the-starter-pack"></a>Haal het startpakket

Haal de aangepaste startpakketten voor beleid op van GitHub en werk de XML-bestanden in het startpakket socialandlocalaccounts bij met de naam van uw Azure AD B2C-tenant.

1. [Download het .zip-bestand](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) of kloon de repository:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Vervang de tekenreeks in `yourtenant` alle bestanden in de map **SocialAndLocalAccounts** door de naam van uw Azure AD B2C-tenant.

    Als bijvoorbeeld de naam van uw B2C-tenant *contosotenant*is, worden alle gevallen van `yourtenant.onmicrosoft.com` worden `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Toepassings-iD's toevoegen aan het aangepaste beleid

Voeg de toepassings-id's toe aan het extensiesbestand *TrustFrameworkExtensions.xml*.

1. Open `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** en vind `<TechnicalProfile Id="login-NonInteractive">`het element .
1. Vervang beide `IdentityExperienceFrameworkAppId` exemplaren van de toepassings-id van de IdentityExperienceFramework-toepassing die u eerder hebt gemaakt.
1. Vervang beide `ProxyIdentityExperienceFrameworkAppId` exemplaren van de toepassings-id van de ProxyIdentityExperienceFramework-toepassing die u eerder hebt gemaakt.
1. Sla het bestand op.

## <a name="upload-the-policies"></a>Het beleid uploaden

1. Selecteer het **menu-item Identity Experience Framework** in uw B2C-tenant in de Azure-portal.
1. Selecteer **Aangepast beleid uploaden**.
1. Upload in deze volgorde de beleidsbestanden:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfielBewerken.xml*
    1. *PasswordReset.xml*

Terwijl u de bestanden uploadt, `B2C_1A_` voegt Azure het voorvoegsel aan elk voorvoegsel toe.

> [!TIP]
> Als uw XML-editor validatie ondersteunt, `TrustFrameworkPolicy_0.3.0.0.xsd` valideert u de bestanden met het XML-schema dat zich in de hoofdmap van het startpakket bevindt. XML-schemavalidatie identificeert fouten voordat ze worden geüpload.

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Selecteer **onder Aangepast beleid** **B2C_1A_signup_signin**.
1. Selecteer voor **Toepassing selecteren** op de overzichtspagina van het aangepaste beleid de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd.
1. Controleer of de URL `https://jwt.ms`van het **antwoord** .
1. Selecteer **Nu uitvoeren**.
1. Meld je aan met een e-mailadres.
1. Selecteer Nu opnieuw **uitvoeren.**
1. Meld u aan met hetzelfde account om te bevestigen dat u de juiste configuratie hebt.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook toevoegen als identiteitsprovider

Zoals vermeld in [Voorwaarden,](#prerequisites)is Facebook *niet* vereist voor het gebruik van aangepast beleid, maar wordt hier gebruikt om aan te tonen hoe u federatieve sociale aanmelding in een aangepast beleid inschakelen.

1. Vervang `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** in het bestand `client_id` de waarde van de Facebook-toepassings-id:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Upload het bestand *TrustFrameworkExtensions.xml* naar uw tenant.
1. Selecteer **onder Aangepast beleid** **B2C_1A_signup_signin**.
1. Selecteer **Nu uitvoeren** en selecteer Facebook om u aan te melden bij Facebook en test het aangepaste beleid.

## <a name="next-steps"></a>Volgende stappen

Probeer vervolgens Azure Active Directory (Azure AD) toe te voegen als identiteitsprovider. Het basisbestand dat wordt gebruikt in deze handleiding voor het aan de slag bevat al een deel van de inhoud die u nodig hebt voor het toevoegen van andere identiteitsproviders zoals Azure AD.

Zie [Aanmelden en aanmelden instellen met een Azure Active Directory-account met het aangepaste active directory-beleid](identity-provider-azure-ad-single-tenant-custom.md)van Active Directory.
