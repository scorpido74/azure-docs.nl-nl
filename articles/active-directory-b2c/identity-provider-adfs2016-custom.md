---
title: ADFS toevoegen als SAML-identiteitsprovider met behulp van aangepast beleid
titleSuffix: Azure AD B2C
description: ADFS 2016 instellen met het SAML-protocol en het aangepaste beleid in Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bfe39d9528927f995d14772e07e02b2a0528e5e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188525"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>ADFS toevoegen als SAML-identiteitsprovider met aangepaste beleidsregels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel ziet u hoe u aanmelding voor een ADFS-gebruikersaccount inschakelt met behulp van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C). U schakelt aanmelden in door een [SAML-technisch profiel](saml-technical-profile.md) toe te voegen aan een aangepast beleid.

## <a name="prerequisites"></a>Vereisten

- Voer de stappen uit in [Aan de slag met aangepast beleid in Azure Active Directory B2C](custom-policy-get-started.md).
- Zorg ervoor dat u toegang hebt tot een certificaat .pfx-bestand met een privésleutel. U uw eigen ondertekende certificaat genereren en uploaden naar Azure AD B2C. Azure AD B2C gebruikt dit certificaat om het SAML-verzoek te ondertekenen dat naar uw SAML-identiteitsprovider is verzonden.
- Om het wachtwoord van het PFX-bestand te accepteren, moet het wachtwoord worden versleuteld met de optie TripleDES-SHA1 in Windows Certificate Store Export utility in tegenstelling tot AES256-SHA256.

## <a name="create-a-policy-key"></a>Een beleidssleutel maken

U moet uw certificaat opslaan in uw Azure AD B2C-tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw tenant.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina Overzicht de optie **Identity Experience Framework**.
5. Selecteer **Beleidssleutels** en selecteer **Vervolgens Toevoegen**.
6. Kies **Options**voor `Upload`Opties .
7. Voer een **naam** in voor de beleidssleutel. Bijvoorbeeld `SamlCert`. Het voorvoegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Blader naar en selecteer uw certificaat .pfx-bestand met de privésleutel.
9. Klik **op Maken**.

## <a name="add-a-claims-provider"></a>Een claimprovider toevoegen

Als u wilt dat gebruikers zich aanmelden met een ADFS-account, moet u het account definiëren als een claimprovider waarmee Azure AD B2C via een eindpunt kan communiceren. Het eindpunt biedt een reeks claims die worden gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U een ADFS-account definiëren als claimprovider door het toe te voegen aan het element **ClaimsProviders** in het uitbreidingsbestand van uw beleid. Zie voor meer informatie [een SAML-technisch profiel definiëren.](saml-technical-profile.md)

1. Open de *TrustFrameworkExtensions.xml*.
1. Zoek het element **Claimproviders.** Als het niet bestaat, voeg het onder het hoofdelement.
1. Voeg als volgt een nieuwe **ClaimProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

1. Vervang `your-ADFS-domain` de naam van uw ADFS-domein en vervang de waarde van de **identiteitsclaim Van Provider** door uw DNS (willekeurige waarde die uw domein aangeeft).

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

U hebt uw beleid inmiddels zo geconfigureerd dat Azure AD B2C weet hoe u met adfs-account moet communiceren. Probeer het extensiebestand van uw beleid te uploaden om te bevestigen dat het tot nu toe geen problemen heeft.

1. Selecteer Op de pagina **Aangepast beleid** in uw Azure AD B2C-tenant de optie **Beleid uploaden**.
2. Het **beleid overschrijven inschakelen als het bestaat,** en blader naar en selecteer het bestand *TrustFrameworkExtensions.xml.*
3. Klik op **Uploaden**.

> [!NOTE]
> De Visual Studio-code B2C-extensie gebruikt 'socialIdpUserId'. Voor ADFS is ook een sociaal beleid vereist.
>

## <a name="register-the-claims-provider"></a>Registreer de schadeprovider

Op dit moment is de identiteitsprovider ingesteld, maar deze is niet beschikbaar in een van de aanmeldings- of aanmeldingsschermen. Als u deze beschikbaar wilt maken, maakt u een duplicaat van een bestaande sjabloongebruikersreis en wijzigt u deze vervolgens zodat deze ook de ADFS-identiteitsprovider heeft.

1. Open het bestand *TrustFrameworkBase.xml* vanuit het startpakket.
2. De volledige inhoud van het **UserJourney-element,** dat het element UserJourney bevat, zoeken en kopiëren. `Id="SignUpOrSignIn"`
3. Open het *element TrustFrameworkExtensions.xml* en zoek het element **UserJourneys.** Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **Element UserJourney** die u als onderliggend element van het element **UserJourneys** hebt gekopieerd.
5. Wijzig de naam van de id van de gebruikersreis. Bijvoorbeeld `SignUpSignInADFS`.

### <a name="display-the-button"></a>De knop weergeven

Het **element ClaimsProviderSelection** is analoog aan een knop van identiteitsprovider op een aanmeldings- of aanmeldingsscherm. Als u een **ClaimProviderSelection-element** toevoegt voor een ADFS-account, wordt er een nieuwe knop weergegeven wanneer een gebruiker op de pagina landt.

1. Zoek het element **OrchestrationStep** dat is onderdeel van `Order="1"` het gebruikerstraject dat u hebt gemaakt.
2. Voeg onder **ClaimsProviderSelections**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in `ContosoExchange`op een geschikte waarde, bijvoorbeeld:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop koppelen aan een actie

Nu je een knop hebt, moet je deze koppelen aan een actie. De actie is in dit geval dat Azure AD B2C communiceert met een ADFS-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die in het gebruikerstraject is meegemaakt. `Order="2"`
2. Voeg het volgende **Element ClaimsExchange** toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de id die u hebt gebruikt voor **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId bij** naar de ID van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `Contoso-SAML2`.

3. Sla het *bestand TrustFrameworkExtensions.xml op* en upload het opnieuw voor verificatie.


## <a name="configure-an-adfs-relying-party-trust"></a>Een aDFS-vertrouwensrelatie van de relying-partij configureren

Als u ADFS wilt gebruiken als identiteitsprovider in Azure AD B2C, moet u een ADFS Relying Party-vertrouwensrelatie maken met de Azure AD B2C SAML-metagegevens. In het volgende voorbeeld wordt een URL-adres weergegeven voor de SAML-metagegevens van een technisch azure AD B2C-profiel:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Vervang de volgende waarden:

- **uw huurder** met uw tenantnaam, zoals your-tenant.onmicrosoft.com.
- **uw polis** met uw polisnaam. Bijvoorbeeld, B2C_1A_signup_signin_adfs.
- **uw-technisch-profiel** met de naam van uw SAML-identiteitsprovider technisch profiel. Bijvoorbeeld Contoso-SAML2.

Open een browser en navigeer naar de URL. Zorg ervoor dat u de juiste URL typt en dat u toegang hebt tot het XML-metagegevensbestand. Als u een nieuwe vertrouwensrelatie van relying party wilt toevoegen door de module ADFS-beheer te gebruiken en de instellingen handmatig te configureren, voert u de volgende procedure uit op een federatieserver. Lidmaatschap **van beheerders** of equivalent op de lokale computer is het minimum dat nodig is om deze procedure te voltooien.

1. Selecteer in Serverbeheer **Extra**en selecteer **Vervolgens ADFS-beheer**.
2. Selecteer **Vertrouwensrelatie van relying party toevoegen**.
3. Kies **op** de pagina Welkom **de optie Claims bewust**en klik op **Start**.
4. Selecteer op de pagina **Gegevensbron selecteren** de optie **Gegevens importeren over de relying party die online of op een lokaal netwerk publiceert,** geef uw URL van Azure AD B2C-metagegevens op en klik vervolgens op **Volgende**.
5. Voer op de pagina **Weergavenaam opgeven** een **weergavenaam**in onder **Notities,** voer een beschrijving in voor deze vertrouwensrelatie van deze relying party en klik op **Volgende**.
6. Selecteer op de pagina **Beleid voor toegangsbeheer** kiezen een beleid en klik op **Volgende**.
7. Controleer op de pagina **Vertrouwensrelatie klaar om toe te voegen** en klik op **Volgende** om vertrouwensgegevens van uw relying party op te slaan.
8. Klik op de pagina **Voltooien** op **Sluiten**, in deze actie wordt automatisch het dialoogvenster **Claimregels bewerken** weergegeven.
9. Selecteer **Regel toevoegen**.
10. Selecteer **ldap-kenmerken verzenden als claims**in **de sjabloon Claimregel**.
11. Geef een **naam van de claimregel**op . Selecteer voor het **kenmerkarchief** **Active Directory selecteren,** voeg de volgende claims toe en klik vervolgens op **Voltooien** en **OK**.

    | LDAP-kenmerk | Type uitgaande claim |
    | -------------- | ------------------- |
    | Principal-naam van gebruiker | userPrincipalName |
    | Achternaam | family_name |
    | Given-Name | given_name |
    | E-mailadres | e-mail |
    | Weergavenaam | name |

    Houd er rekening mee dat deze namen niet worden weergegeven in de vervolgkeuzelijst van het uitgaande claimtype. Je moet ze handmatig intypen. (De dropdown is eigenlijk bewerkbaar).

12.  Op basis van uw certificaattype moet u mogelijk het HASH-algoritme instellen. Selecteer op het eigenschappenvenster relying party trust (B2C Demo) het `SHA-256`tabblad **Geavanceerd** en wijzig het **beveiligde hash-algoritme** in en klik op **Ok**.
13. Selecteer in Serverbeheer **Extra**en selecteer **Vervolgens ADFS-beheer**.
14. Selecteer de vertrouwensrelatie van de relying party die u hebt gemaakt, selecteer **Bijwerken in federatiemetagegevens**en klik op **Bijwerken**.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-tenant. In deze sectie worden optionele stappen weergegeven die u voltooien om een testtoepassing te maken als u dit nog niet hebt gedaan.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Het bestand van de relying party bijwerken en testen

Werk het RP-bestand (relying party) bij waarmee u de gebruikersreis initieert die u hebt gemaakt.

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInADFS.xml*.
2. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** bij met een unieke waarde. Bijvoorbeeld `SignUpSignInADFS`.
3. Werk de waarde van **PublicPolicyUri bij** met de URI voor het beleid. Bijvoorbeeld,`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Werk de waarde van het kenmerk **ReferenceId** bij in **DefaultUserJourney** zodat deze overeenkomt met de id van het nieuwe gebruikerstraject dat u hebt gemaakt (SignUpSignInADFS).
5. Sla uw wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
6. Controleer of de Azure AD B2C-toepassing die u hebt gemaakt, is geselecteerd in het veld **Toepassing selecteren** en test deze door nu op **Uitvoeren**te klikken .

