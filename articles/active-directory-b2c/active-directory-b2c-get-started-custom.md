---
title: Aan de slag met aangepaste beleids regels-Azure Active Directory B2C
description: Meer informatie over het aan de slag gaan met aangepaste beleids regels in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b7eb004dbeba499e6f67f98165b72d7ec8615f1b
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065850"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Aan de slag met aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Aangepaste beleids regels](active-directory-b2c-overview-custom.md) zijn configuratie bestanden waarmee het gedrag van uw Azure Active Directory B2C (Azure AD B2C)-Tenant wordt gedefinieerd. In dit artikel maakt u een aangepast beleid dat ondersteuning biedt voor het registreren van lokale accounts of het aanmelden met behulp van een e-mail adres en wacht woord. U kunt ook uw omgeving voorbereiden voor het toevoegen van id-providers.

## <a name="prerequisites"></a>Vereisten

- Als u er nog geen hebt, moet u [een Azure AD B2C-Tenant maken](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.
- [Registreer uw toepassing](tutorial-register-applications.md) in de Tenant die u hebt gemaakt, zodat deze kan communiceren met Azure AD B2C.

## <a name="add-signing-and-encryption-keys"></a>Ondertekenings-en versleutelings sleutels toevoegen

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.

### <a name="create-the-signing-key"></a>De handtekening sleutel maken

1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
2. Kies`Generate`voor **Opties**.
3. Voer`TokenSigningKeyContainer`bij **naam**in. Het voor `B2C_1A_` voegsel kan automatisch worden toegevoegd.
4. Selecteer voor **sleutel type** **RSA**.
5. Selecteer voor **sleutel gebruik** **hand tekening**.
6. Klik op **Create**.

### <a name="create-the-encryption-key"></a>De versleutelings sleutel maken

1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
2. Kies`Generate`voor **Opties**.
3. Voer`TokenEncryptionKeyContainer`bij **naam**in. Het voor `B2C_1A`voegsel _ kan automatisch worden toegevoegd.
4. Selecteer voor **sleutel type** **RSA**.
5. Voor **sleutel gebruik**selecteert u **versleuteling**.
6. Klik op **Create**.

### <a name="create-the-facebook-key"></a>De Facebook-sleutel maken

Als u al een [Facebook-toepassings geheim](active-directory-b2c-setup-fb-app.md)hebt, voegt u dit toe als een beleids sleutel aan uw Tenant. Als dat niet het geval is, moet u de sleutel met een tijdelijke aanduiding waarde maken zodat uw beleid validatie doorgeeft.

1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
2. Kies`Manual`voor **Opties**.
3. Voer`FacebookSecret`in bij **naam**. Het voor `B2C_1A_` voegsel kan automatisch worden toegevoegd.
4. Voer in het **geheim**uw Facebook-geheim in vanuit `0` developers.Facebook.com of als tijdelijke aanduiding. Deze waarde is het geheim, niet de toepassings-ID.
5. Selecteer voor **sleutel gebruik** **hand tekening**.
6. Klik op **Create**.

## <a name="register-identity-experience-framework-applications"></a>Identiteits ervaring-Framework toepassingen registreren

Azure AD B2C moet u twee toepassingen registreren die worden gebruikt om gebruikers aan te melden en aan te melden: IdentityExperienceFramework (een web-app) en ProxyIdentityExperienceFramework (een systeem eigen app) met gedelegeerde machtigingen van de IdentityExperienceFramework-app. Lokale accounts bestaan alleen in uw Tenant. Uw gebruikers kunnen zich aanmelden met een unieke combi natie van e-mail adres en wacht woord om toegang te krijgen tot uw door Tenant geregistreerde toepassingen.

### <a name="register-the-identityexperienceframework-application"></a>De IdentityExperienceFramework-toepassing registreren

1. Selecteer **alle services** in de linkerbovenhoek van de Azure Portal.
1. Typ `Azure Active Directory` in het zoekvak.
1. Selecteer **Azure Active Directory** in de zoekresultaten.
1. Onder **beheren** in het linkermenu selecteert u **app-registraties (verouderd)** .
1. Selecteer **Nieuwe toepassing registreren**.
1. Voer`IdentityExperienceFramework`in bij **naam**.
1. Kies voor **toepassings type** **Web-app/API**.
1. Voer`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`bij **aanmeldings-URL**in, waarbij `your-tenant-name` de naam van uw Azure AD B2C Tenant domein. Alle Url's moeten nu gebruikmaken van [b2clogin.com](b2clogin.md).
1. Klik op **Create**. Nadat de app is gemaakt, kopieert u de toepassings-ID en slaat u deze op voor later gebruik.

### <a name="register-the-proxyidentityexperienceframework-application"></a>De ProxyIdentityExperienceFramework-toepassing registreren

1. Selecteer in **app-registraties (verouderd)** de optie **nieuwe toepassing registreren**.
2. Voer`ProxyIdentityExperienceFramework`in bij **naam**.
3. Kies voor **toepassings type** **systeem eigen**.
4. `your-tenant-name` Voer`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`voor omleidings-URI, waarbij uw Azure AD B2C Tenant.
5. Klik op **Create**. Nadat de app is gemaakt, kopieert u de toepassings-ID en slaat u deze op voor later gebruik.
6. Selecteer op de pagina instellingen de optie **vereiste machtigingen**en selecteer vervolgens **toevoegen**.
7. Kies **een API selecteren**, zoek naar en selecteer **IdentityExperienceFramework**, en klik vervolgens op **selecteren**.
9. Schakel het selectie vakje naast **toegangs IdentityExperienceFramework**in, klik op **selecteren**en klik vervolgens op **gereed**.
10. Selecteer **machtigingen verlenen**en Bevestig door **Ja**te selecteren.

## <a name="custom-policy-starter-pack"></a>Aangepast beleids Starter Pack

Aangepaste beleids regels zijn een set XML-bestanden die u uploadt naar uw Azure AD B2C-Tenant om technische profielen en gebruikers trajecten te definiëren. We bieden Starter Packs met verschillende vooraf ontwikkelde beleids regels om u snel aan de slag te gaan. Elk van deze Starter Packs bevat het kleinste aantal technische profielen en gebruikers ritten dat nodig is voor het uitvoeren van de beschreven scenario's:

- **LocalAccounts** : Hiermee schakelt u het gebruik van alleen lokale accounts in.
- **SocialAccounts** : Hiermee schakelt u het gebruik van sociale (of federatieve) accounts in.
- **SocialAndLocalAccounts** : Hiermee schakelt u het gebruik van zowel lokale als sociale accounts in.
- **SocialAndLocalAccountsWithMFA** : Hiermee schakelt u de opties sociale, lokale en multi-factor Authentication in.

Elk Starter Pack bevat:

- **Basis bestand** -weinig wijzigingen zijn vereist voor de basis. Voorbeeld: *TrustFrameworkBase. XML*
- **Extensie bestand** : in dit bestand worden de meeste configuratie wijzigingen aangebracht. Voorbeeld: *TrustFrameworkExtensions. XML*
- Relying Party- **bestanden** -taak gerichte bestanden die door uw toepassing worden aangeroepen. Voorbeelden: *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset. XML*

In dit artikel bewerkt u de aangepaste XML-beleids bestanden in het **SocialAndLocalAccounts** Starter Pack. Als u een XML-editor nodig hebt, kunt u [Visual Studio code](https://code.visualstudio.com/download), een licht gewicht platform editor uitproberen.

### <a name="get-the-starter-pack"></a>Het Starter Pack ophalen

Down load de aangepaste beleids Starter Packs van GitHub en werk vervolgens de XML-bestanden in het SocialAndLocalAccounts Starter Pack bij met de naam van uw Azure AD B2C-Tenant.

1. [Down load het zip-bestand](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) of kloon de opslag plaats:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Vervang in alle bestanden in de map **SocialAndLocalAccounts** de teken reeks `yourtenant` door de naam van uw Azure AD B2C-Tenant.

    Als de naam van uw B2C-Tenant bijvoorbeeld *contosotenant*is, worden `yourtenant.onmicrosoft.com` `contosotenant.onmicrosoft.com`alle exemplaren van.

### <a name="add-application-ids-to-the-custom-policy"></a>Toepassings-Id's toevoegen aan het aangepaste beleid

Voeg de toepassings-Id's toe aan het extensie bestand *TrustFrameworkExtensions. XML*.

1. Open `SocialAndLocalAccounts/` **het`TrustFrameworkExtensions.xml`** element`<TechnicalProfile Id="login-NonInteractive">`en zoek het.
1. Vervang beide exemplaren van `IdentityExperienceFrameworkAppId` door de toepassings-id van de IdentityExperienceFramework-toepassing die u eerder hebt gemaakt.
1. Vervang beide exemplaren van `ProxyIdentityExperienceFrameworkAppId` door de toepassings-id van de ProxyIdentityExperienceFramework-toepassing die u eerder hebt gemaakt.
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

Wanneer u de bestanden uploadt, voegt Azure het `B2C_1A_` voor voegsel toe aan elke.

> [!TIP]
> Als uw XML-editor validatie ondersteunt, valideert u de `TrustFrameworkPolicy_0.3.0.0.xsd` bestanden op basis van het XML-schema dat zich in de hoofdmap van het Starter Pack bevindt. Validatie van XML-schema identificeert fouten voordat ze worden geüpload.

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Onder **aangepast beleid**selecteert u **B2C_1A_signup_signin**.
1. Selecteer voor **Select-toepassing** op de pagina overzicht van het aangepaste beleid de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd.
1. Zorg ervoor dat de **antwoord** -URL `https://jwt.ms`is.
1. Selecteer **nu uitvoeren**.
1. Meld u aan met een e-mail adres.
1. Selecteer **nu uitvoeren** .
1. Meld u aan met hetzelfde account om te controleren of u de juiste configuratie hebt.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook toevoegen als een id-provider

1. Voer de stappen in [instellen registratie in en meld u aan met een Facebook-account](active-directory-b2c-setup-fb-app.md) om een Facebook-toepassing te configureren.
1. Vervang in `SocialAndLocalAccounts/` het **`TrustFrameworkExtensions.xml`** bestand de waarde van met `client_id` de id van de Facebook-toepassing:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Upload het bestand *TrustFrameworkExtensions. XML* naar uw Tenant.
1. Onder **aangepast beleid**selecteert u **B2C_1A_signup_signin**.
1. Selecteer **nu uitvoeren** en selecteer Facebook om u aan te melden met Facebook en het aangepaste beleid te testen. Of roep het beleid rechtstreeks vanuit uw geregistreerde toepassing aan.

## <a name="next-steps"></a>Volgende stappen

Probeer vervolgens Azure Active Directory (Azure AD) toe te voegen als een id-provider. Het basis bestand dat wordt gebruikt in deze aan de slag-hand leiding bevat al een deel van de inhoud die u nodig hebt om andere id-providers, zoals Azure AD, toe te voegen.

Zie voor meer informatie over het instellen van Azure AD als id-provider, [aanmelden instellen en aanmelden met een Azure Active Directory account met behulp van Active Directory B2C aangepast beleid](active-directory-b2c-setup-aad-custom.md).
