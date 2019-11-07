---
title: Aan de slag met aangepaste beleids regels-Azure Active Directory B2C
description: Meer informatie over het aan de slag gaan met aangepaste beleids regels in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 411710280a631d341adeb55bc4b587a613ee3c4c
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643624"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Aan de slag met aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Aangepaste beleids regels](active-directory-b2c-overview-custom.md) zijn configuratie bestanden waarmee het gedrag van uw Azure Active Directory B2C (Azure AD B2C)-Tenant wordt gedefinieerd. In dit artikel maakt u een aangepast beleid dat ondersteuning biedt voor het registreren van lokale accounts of het aanmelden met behulp van een e-mail adres en wacht woord. U kunt ook uw omgeving voorbereiden voor het toevoegen van id-providers.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen account hebt, [maakt u een Azure AD B2C-Tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.
- [Registreer uw toepassing](tutorial-register-applications.md) in de Tenant die u hebt gemaakt, zodat deze kan communiceren met Azure AD B2C.
- Voer de stappen in [instellen registratie in en meld u aan met een Facebook-account](active-directory-b2c-setup-fb-app.md) om een Facebook-toepassing te configureren.

## <a name="add-signing-and-encryption-keys"></a>Ondertekenings-en versleutelings sleutels toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Gebruik het filter voor **adres lijst en abonnementen** in het bovenste menu om de map te selecteren die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.

### <a name="create-the-signing-key"></a>De handtekening sleutel maken

1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies `Generate`voor **Opties**.
1. Voer in **naam**`TokenSigningKeyContainer`in. De prefix `B2C_1A_` kan automatisch worden toegevoegd.
1. Selecteer voor **sleutel type** **RSA**.
1. Selecteer voor **sleutel gebruik** **hand tekening**.
1. Selecteer **Maken**.

### <a name="create-the-encryption-key"></a>De versleutelings sleutel maken

1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies `Generate`voor **Opties**.
1. Voer in **naam**`TokenEncryptionKeyContainer`in. Het voor voegsel `B2C_1A`_ kan automatisch worden toegevoegd.
1. Selecteer voor **sleutel type** **RSA**.
1. Voor **sleutel gebruik**selecteert u **versleuteling**.
1. Selecteer **Maken**.

### <a name="create-the-facebook-key"></a>De Facebook-sleutel maken

Voeg het [app-geheim](active-directory-b2c-setup-fb-app.md) van uw Facebook-toepassing toe als een beleids sleutel. U kunt het app-geheim gebruiken van de toepassing die u hebt gemaakt als onderdeel van de vereisten van dit artikel.

1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies `Manual`voor **Opties**.
1. Voer bij **naam**`FacebookSecret`in. De prefix `B2C_1A_` kan automatisch worden toegevoegd.
1. Voer in het **geheim**het *app-geheim* van uw Facebook-toepassing in vanuit developers.Facebook.com. Deze waarde is het geheim, niet de toepassings-ID.
1. Selecteer voor **sleutel gebruik** **hand tekening**.
1. Selecteer **Maken**.

## <a name="register-identity-experience-framework-applications"></a>Identiteits ervaring-Framework toepassingen registreren

Azure AD B2C moet u twee toepassingen registreren die worden gebruikt voor het aanmelden en aanmelden van gebruikers met lokale accounts: *IdentityExperienceFramework*, een web-API en *ProxyIdentityExperienceFramework*, een systeem eigen app met gedelegeerde machtigingen voor de IdentityExperienceFramework-app. Uw gebruikers kunnen zich aanmelden met een e-mail adres of gebruikers naam en een wacht woord voor toegang tot uw door tenants geregistreerde toepassingen, waardoor een ' lokaal account ' wordt gemaakt. Lokale accounts bestaan alleen in uw Azure AD B2C-Tenant.

U moet deze twee toepassingen in uw Azure AD B2C-Tenant slechts eenmaal registreren.

### <a name="register-the-identityexperienceframework-application"></a>De IdentityExperienceFramework-toepassing registreren

Als u een toepassing in uw Azure AD B2C-Tenant wilt registreren, kunt u de huidige **toepassingen** gebruiken of onze nieuwe **Preview-ervaring (Unified app-registraties)** . [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Toepassingen](#tab/applications/)

1. Selecteer **alle services** in de linkerbovenhoek van de Azure Portal.
1. Typ `Azure Active Directory` in het zoekvak.
1. Selecteer **Azure Active Directory** in de zoekresultaten.
1. Onder **beheren** in het linkermenu selecteert u **app-registraties (verouderd)** .
1. Selecteer **Nieuwe toepassing registreren**.
1. Voer bij **naam**`IdentityExperienceFramework`in.
1. Kies voor **toepassings type** **Web-app/API**.
1. Voer voor **aanmeldings-URL**`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`in, waarbij `your-tenant-name` de domein naam van de Azure AD B2C Tenant is. Alle Url's moeten nu gebruikmaken van [b2clogin.com](b2clogin.md).
1. Selecteer **Maken**. Nadat de app is gemaakt, kopieert u de toepassings-ID en slaat u deze op voor later gebruik.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Selecteer **app-registraties (preview)** en selecteer vervolgens **nieuwe registratie**.
1. Voer bij **naam**`IdentityExperienceFramework`in.
1. Onder **ondersteunde account typen**selecteert u **alleen accounts in deze organisatie Directory**.
1. Onder **omleidings-URI**selecteert u **Web**en voert u `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`in, waarbij `your-tenant-name` de domein naam van de Azure AD B2C Tenant is.
1. Schakel onder **machtigingen**het selectie vakje *verlenen beheerders toestemming voor OpenID Connect-en offline_access-machtigingen* in.
1. Selecteer **Registreren**.
1. Noteer de **id van de toepassing (client)** voor gebruik in een latere stap.

Vervolgens maakt u de API zichtbaar door een bereik toe te voegen:

1. Onder **beheren**selecteert u **een API zichtbaar**maken.
1. Selecteer **een bereik toevoegen**en selecteer vervolgens **opslaan en ga door met** het accepteren van de standaard toepassings-id-URI.
1. Voer de volgende waarden in om een bereik te maken waarmee aangepaste beleids regels kunnen worden uitgevoerd in uw Azure AD B2C-Tenant:
    * **Scope naam**: `user_impersonation`
    * **Weergave naam van beheerders toestemming**: `Access IdentityExperienceFramework`
    * **Beschrijving van beheerders toestemming**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. **Bereik toevoegen** selecteren

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>De ProxyIdentityExperienceFramework-toepassing registreren

#### <a name="applicationstabapplications"></a>[Toepassingen](#tab/applications/)

1. Selecteer in **app-registraties (verouderd)** de optie **nieuwe toepassing registreren**.
1. Voer bij **naam**`ProxyIdentityExperienceFramework`in.
1. Kies voor **toepassings type** **systeem eigen**.
1. Voer voor **omleidings-URI**`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`in, waarbij `your-tenant-name` uw Azure AD B2C Tenant is.
1. Selecteer **Maken**. Nadat de app is gemaakt, kopieert u de toepassings-ID en slaat u deze op voor later gebruik.
1. Selecteer **instellingen**, selecteer de **gewenste machtigingen**en selecteer vervolgens **toevoegen**.
1. Kies **een API selecteren**, zoek naar en selecteer **IdentityExperienceFramework**, en klik vervolgens op **selecteren**.
1. Schakel het selectie vakje naast **toegangs IdentityExperienceFramework**in, klik op **selecteren**en klik vervolgens op **gereed**.
1. Selecteer **machtigingen verlenen**en Bevestig door **Ja**te selecteren.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Selecteer **app-registraties (preview)** en selecteer vervolgens **nieuwe registratie**.
1. Voer bij **naam**`ProxyIdentityExperienceFramework`in.
1. Onder **ondersteunde account typen**selecteert u **alleen accounts in deze organisatie Directory**.
1. Gebruik onder **omleidings-URI**de vervolg keuzelijst om een **open bare client/systeem eigen (mobiele & bureau blad)** te selecteren.
1. Voer voor **omleidings-URI**`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`in, waarbij `your-tenant-name` uw Azure AD B2C Tenant is.
1. Schakel onder **machtigingen**het selectie vakje *verlenen beheerders toestemming voor OpenID Connect-en offline_access-machtigingen* in.
1. Selecteer **Registreren**.
1. Noteer de **id van de toepassing (client)** voor gebruik in een latere stap.

Geef vervolgens op dat de toepassing moet worden behandeld als een open bare client:

1. Selecteer onder **beheren**de optie **verificatie**.
1. Selecteer **de nieuwe ervaring uitproberen** (indien weer gegeven).
1. Schakel onder **Geavanceerde instellingen** **de optie toepassing behandelen als een open bare client** in (Selecteer **Ja**).
1. Selecteer **Opslaan**.

Ken nu machtigingen toe aan het API-bereik dat u eerder hebt weer gegeven in de *IdentityExperienceFramework* -registratie:

1. Selecteer onder **beheren**de optie **API-machtigingen**.
1. Selecteer onder **geconfigureerde machtigingen** **de optie een machtiging toevoegen**.
1. Selecteer het tabblad **mijn api's** en selecteer vervolgens de toepassing **IdentityExperienceFramework** .
1. Selecteer onder **machtiging**het **user_impersonation** -bereik dat u eerder hebt gedefinieerd.
1. Selecteer **machtigingen toevoegen**. Wacht een paar minuten voordat u verdergaat met de volgende stap.
1. Selecteer **beheerder toestemming geven voor (uw Tenant naam)** .
1. Selecteer het momenteel aangemelde Administrator-account of Meld u aan met een account in uw Azure AD B2C-Tenant waaraan ten minste de rol van *Cloud toepassings beheerder* is toegewezen.
1. Selecteer **Accepteren**.
1. Selecteer **vernieuwen**en controleer vervolgens of ' verleend voor... ' wordt onder **status** weer gegeven voor beide bereiken. Het kan enkele minuten duren voordat de machtigingen zijn door gegeven.

* * *

## <a name="custom-policy-starter-pack"></a>Aangepast beleids Starter Pack

Aangepaste beleids regels zijn een set XML-bestanden die u uploadt naar uw Azure AD B2C-Tenant om technische profielen en gebruikers trajecten te definiëren. We bieden Starter Packs met verschillende vooraf ontwikkelde beleids regels om u snel aan de slag te gaan. Elk van deze Starter Packs bevat het kleinste aantal technische profielen en gebruikers ritten dat nodig is voor het uitvoeren van de beschreven scenario's:

- **LocalAccounts** : Hiermee schakelt u het gebruik van alleen lokale accounts in.
- **SocialAccounts** : Hiermee schakelt u het gebruik van sociale (of federatieve) accounts in.
- **SocialAndLocalAccounts** : Hiermee schakelt u het gebruik van zowel lokale als sociale accounts in.
- **SocialAndLocalAccountsWithMFA** : Hiermee schakelt u de opties sociale, lokale en multi-factor Authentication in.

Elk Starter Pack bevat:

- **Basis bestand** -weinig wijzigingen zijn vereist voor de basis. Voor beeld: *TrustFrameworkBase. XML*
- **Extensie bestand** : in dit bestand worden de meeste configuratie wijzigingen aangebracht. Voor beeld: *TrustFrameworkExtensions. XML*
- Relying Party- **bestanden** -taak gerichte bestanden die door uw toepassing worden aangeroepen. Voor beelden: *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset. XML*

In dit artikel bewerkt u de aangepaste XML-beleids bestanden in het **SocialAndLocalAccounts** Starter Pack. Als u een XML-editor nodig hebt, kunt u [Visual Studio code](https://code.visualstudio.com/download), een licht gewicht platform editor uitproberen.

### <a name="get-the-starter-pack"></a>Het Starter Pack ophalen

Down load de aangepaste beleids Starter Packs van GitHub en werk vervolgens de XML-bestanden in het SocialAndLocalAccounts Starter Pack bij met de naam van uw Azure AD B2C-Tenant.

1. [Down load het zip-bestand](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) of kloon de opslag plaats:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Vervang in alle bestanden in de map **SocialAndLocalAccounts** de teken reeks `yourtenant` door de naam van uw Azure AD B2C Tenant.

    Als de naam van uw B2C-Tenant bijvoorbeeld *contosotenant*is, worden alle exemplaren van `yourtenant.onmicrosoft.com` `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Toepassings-Id's toevoegen aan het aangepaste beleid

Voeg de toepassings-Id's toe aan het extensie bestand *TrustFrameworkExtensions. XML*.

1. Open `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** en zoek het element `<TechnicalProfile Id="login-NonInteractive">`.
1. Vervang beide exemplaren van `IdentityExperienceFrameworkAppId` door de toepassings-ID van de IdentityExperienceFramework-toepassing die u eerder hebt gemaakt.
1. Vervang beide exemplaren van `ProxyIdentityExperienceFrameworkAppId` door de toepassings-ID van de ProxyIdentityExperienceFramework-toepassing die u eerder hebt gemaakt.
1. Sla het bestand op.

## <a name="upload-the-policies"></a>Het beleid uploaden

1. Selecteer het menu-item **identiteits ervaring** in uw B2C-Tenant in de Azure Portal.
1. Selecteer **aangepast beleid uploaden**.
1. Upload de beleids bestanden in deze volg orde:
    1. *TrustFrameworkBase. XML*
    1. *TrustFrameworkExtensions. XML*
    1. *SignUpOrSignin. XML*
    1. *ProfileEdit. XML*
    1. *PasswordReset. XML*

Wanneer u de bestanden uploadt, voegt Azure het voor voegsel toe `B2C_1A_`.

> [!TIP]
> Als uw XML-editor validatie ondersteunt, valideert u de bestanden op basis van het `TrustFrameworkPolicy_0.3.0.0.xsd` XML-schema dat zich in de hoofdmap van het Starter Pack bevindt. Validatie van XML-schema identificeert fouten voordat ze worden geüpload.

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Onder **aangepast beleid**selecteert u **B2C_1A_signup_signin**.
1. Selecteer voor **Select-toepassing** op de pagina overzicht van het aangepaste beleid de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd.
1. Zorg ervoor dat de **antwoord-URL** is `https://jwt.ms`.
1. Selecteer **nu uitvoeren**.
1. Meld u aan met een e-mail adres.
1. Selecteer **nu uitvoeren** .
1. Meld u aan met hetzelfde account om te controleren of u de juiste configuratie hebt.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook toevoegen als een id-provider

1. Vervang in het bestand `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** de waarde van `client_id` door de id van de Facebook-toepassing:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Upload het bestand *TrustFrameworkExtensions. XML* naar uw Tenant.
1. Onder **aangepast beleid**selecteert u **B2C_1A_signup_signin**.
1. Selecteer **nu uitvoeren** en selecteer Facebook om u aan te melden met Facebook en het aangepaste beleid te testen.

## <a name="next-steps"></a>Volgende stappen

Probeer vervolgens Azure Active Directory (Azure AD) toe te voegen als een id-provider. Het basis bestand dat wordt gebruikt in deze aan de slag-hand leiding bevat al een deel van de inhoud die u nodig hebt om andere id-providers, zoals Azure AD, toe te voegen.

Zie voor meer informatie over het instellen van Azure AD als id-provider, [aanmelden instellen en aanmelden met een Azure Active Directory account met behulp van Active Directory B2C aangepast beleid](active-directory-b2c-setup-aad-custom.md).
