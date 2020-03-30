---
title: Aanmelden bij een Salesforce SAML-provider instellen met behulp van aangepast beleid
titleSuffix: Azure AD B2C
description: Stel aanmelding in bij een Salesforce SAML-provider met behulp van aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67fe9ef4ad2b025d11f88976973658c9cd8ae693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187947"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelden bij een Salesforce SAML-provider instellen met behulp van aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel ziet u hoe u aanmelding inschakelt voor gebruikers van een Salesforce-organisatie met behulp van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C). U schakelt aanmelden in door een [SAML-technisch profiel](saml-technical-profile.md) toe te voegen aan een aangepast beleid.

## <a name="prerequisites"></a>Vereisten

- Voer de stappen uit in [Aan de slag met aangepast beleid in Azure Active Directory B2C](custom-policy-get-started.md).
- Als je dit nog niet hebt gedaan, meld je je aan voor een [gratis Developer Edition-account.](https://developer.salesforce.com/signup) In dit artikel wordt gebruik gemaakt van de [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Stel een Mijn domein](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) in voor uw Salesforce-organisatie.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Salesforce instellen als identiteitsprovider

1. [Meld u aan bij Salesforce](https://login.salesforce.com/).
2. Vouw in het linkermenu onder **Instellingen** **identiteit**uit en selecteer **vervolgens Identiteitsprovider**.
3. Selecteer **Identiteitsprovider inschakelen**.
4. Selecteer **onder Selecteer het certificaat**selecteer het certificaat waarmee Salesforce moet communiceren met Azure AD B2C. U het standaardcertificaat gebruiken.
5. Klik op **Opslaan**.

### <a name="create-a-connected-app-in-salesforce"></a>Een verbonden app maken in Salesforce

1. Op de pagina **Identity Provider** worden nu bepaalde **serviceproviders gemaakt via Verbonden Apps. Klik hier.**
2. Voer **onder Basisgegevens**de vereiste waarden in voor uw verbonden app.
3. Schakel **onder Web App-instellingen**het selectievakje **SAML inschakelen in.**
4. Voer in het veld **Entiteit-id** de volgende URL in. Zorg ervoor dat u `your-tenant` de waarde vervangt door de naam van uw Azure AD B2C-tenant.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Voer in het **veld ACS URL** de volgende URL in. Zorg ervoor dat u `your-tenant` de waarde vervangt door de naam van uw Azure AD B2C-tenant.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Schuif naar de onderkant van de lijst en klik op **Opslaan**.

### <a name="get-the-metadata-url"></a>De URL met ametjes ophalen

1. Klik op de overzichtspagina van uw verbonden app op **Beheren**.
2. Kopieer de waarde voor **het detectieeindpunt met metagegevens**en sla deze op. Je gebruikt het later in dit artikel.

### <a name="set-up-salesforce-users-to-federate"></a>Salesforce-gebruikers zo instellen dat ze zich gedeisd maken

1. Klik **op** de pagina Beheren van uw verbonden app op **Profielen beheren**.
2. Selecteer de profielen (of groepen gebruikers) die u wilt gebruiken voor Azure AD B2C. Schakel als systeembeheerder het selectievakje **Systeembeheerder** in, zodat u beoordelen met uw Salesforce-account.

## <a name="generate-a-signing-certificate"></a>Een ondertekeningscertificaat genereren

Aanvragen die naar Salesforce worden verzonden, moeten worden ondertekend door Azure AD B2C. Als u een ondertekeningscertificaat wilt genereren, opent u Azure PowerShell en voert u de volgende opdrachten uit.

> [!NOTE]
> Zorg ervoor dat u de tenantnaam en het wachtwoord in de bovenste twee regels bijwerkt.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Een beleidssleutel maken

U moet het certificaat opslaan dat u hebt gemaakt in uw Azure AD B2C-tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina Overzicht de optie **Identity Experience Framework**.
5. Selecteer **Beleidssleutels** en selecteer **Vervolgens Toevoegen**.
6. Kies **Options**voor `Upload`Opties .
7. Voer een **naam** in voor het beleid. Bijvoorbeeld SAMLSigningCert. Het voorvoegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Blader naar en selecteer het B2CSigningCert.pfx-certificaat dat u hebt gemaakt.
9. Voer het **wachtwoord** voor het certificaat in.
3. Klik **op Maken**.

## <a name="add-a-claims-provider"></a>Een claimprovider toevoegen

Als u wilt dat gebruikers zich aanmelden met een Salesforce-account, moet u het account definiëren als een claimprovider waarmee Azure AD B2C via een eindpunt kan communiceren. Het eindpunt biedt een reeks claims die worden gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U een Salesforce-account definiëren als claimprovider door het toe te voegen aan het element **ClaimsProviders** in het uitbreidingsbestand van uw beleid. Zie voor meer informatie [een SAML-technisch profiel definiëren.](saml-technical-profile.md)

1. Open de *TrustFrameworkExtensions.xml*.
1. Zoek het element **Claimproviders.** Als het niet bestaat, voeg het onder het hoofdelement.
1. Voeg als volgt een nieuwe **ClaimProvider** toe:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Werk de waarde van **PartnerEntity bij** met de URL van salesforce-metagegevens die u eerder hebt gekopieerd.
1. Werk de waarde van beide exemplaren van **StorageReferenceId bij** naar de naam van de sleutel van uw ondertekeningscertificaat. Bijvoorbeeld B2C_1A_SAMLSigningCert.
1. Zoek `<ClaimsProviders>` de sectie en voeg het volgende XML-fragment toe. Als uw beleid `SM-Saml-idp` al het technische profiel bevat, gaat u naar de volgende stap. Zie voor meer informatie [het beheer van eenmalige aanmeldingssessie](custom-policy-reference-sso.md).

    ```XML
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Sla het bestand op.

### <a name="upload-the-extension-file-for-verification"></a>Het extensiebestand uploaden ter verificatie

U hebt uw beleid inmiddels zo geconfigureerd dat Azure AD B2C weet hoe u met uw Salesforce-account moet communiceren. Probeer het extensiebestand van uw beleid te uploaden om te bevestigen dat het tot nu toe geen problemen heeft.

1. Selecteer Op de pagina **Aangepast beleid** in uw Azure AD B2C-tenant de optie **Beleid uploaden**.
2. Het **beleid overschrijven inschakelen als het bestaat,** en blader naar en selecteer het bestand *TrustFrameworkExtensions.xml.*
3. Klik op **Uploaden**.

## <a name="register-the-claims-provider"></a>Registreer de schadeprovider

Op dit moment is de identiteitsprovider ingesteld, maar deze is niet beschikbaar in een van de aanmeldings- of aanmeldingsschermen. Als u deze beschikbaar wilt maken, maakt u een duplicaat van een bestaande sjabloongebruikersreis en wijzigt u deze vervolgens zodat deze ook de Salesforce-identiteitsprovider heeft.

1. Open het bestand *TrustFrameworkBase.xml* vanuit het startpakket.
2. De volledige inhoud van het **UserJourney-element,** dat het element UserJourney bevat, zoeken en kopiëren. `Id="SignUpOrSignIn"`
3. Open het *element TrustFrameworkExtensions.xml* en zoek het element **UserJourneys.** Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **Element UserJourney** die u als onderliggend element van het element **UserJourneys** hebt gekopieerd.
5. Wijzig de naam van de id van de gebruikersreis. Bijvoorbeeld `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>De knop weergeven

Het **element ClaimsProviderSelection** is analoog aan een knop van identiteitsprovider op een aanmeldings- of aanmeldingsscherm. Als u een **ClaimProviderSelection-element** toevoegt voor een LinkedIn-account, wordt er een nieuwe knop weergegeven wanneer een gebruiker op de pagina landt.

1. Zoek het **element OrchestrationStep** dat in het gebruikerstraject zit `Order="1"` dat u zojuist hebt gemaakt.
2. Voeg onder **ClaimsProviderSelects**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in `SalesforceExchange`op een geschikte waarde, bijvoorbeeld:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop koppelen aan een actie

Nu je een knop hebt, moet je deze koppelen aan een actie. De actie is in dit geval dat Azure AD B2C communiceert met een Salesforce-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die in het gebruikerstraject is meegemaakt. `Order="2"`
2. Voeg het volgende **ClaimExchange-element** toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor **id** die u hebt gebruikt voor **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId bij** naar de **id** van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `LinkedIn-OAUTH`.

3. Sla het *bestand TrustFrameworkExtensions.xml op* en upload het opnieuw voor verificatie.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-tenant. In deze sectie worden optionele stappen weergegeven die u voltooien om een testtoepassing te maken als u dit nog niet hebt gedaan.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Het bestand van de relying party bijwerken en testen

Werk het RP-bestand (relying party) bij waarmee u de gebruikersreis initieert die u zojuist hebt gemaakt:

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInSalesforce.xml*.
2. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** bij met een unieke waarde. Bijvoorbeeld `SignUpSignInSalesforce`.
3. Werk de waarde van **PublicPolicyUri bij** met de URI voor het beleid. Bijvoorbeeld,`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Werk de waarde van het **kenmerk ReferenceId** bij in **DefaultUserJourney** zodat deze overeenkomt met de id van het nieuwe gebruikerstraject dat u hebt gemaakt (SignUpSignInSalesforce).
5. Sla uw wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
6. Controleer of de Azure AD B2C-toepassing die u hebt gemaakt, is geselecteerd in het veld **Toepassing selecteren** en test deze door nu op **Uitvoeren**te klikken .
