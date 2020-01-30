---
title: ADFS toevoegen als een SAML-ID-provider met behulp van aangepast beleid
titleSuffix: Azure AD B2C
description: ADFS 2016 instellen met het SAML-protocol en aangepaste beleids regels in Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 684e4a410ac8624066c897b4078ea4044a965357
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848913"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Voeg ADFS toe als een SAML-ID-provider met behulp van aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel wordt beschreven hoe u aanmelden voor een AD FS-gebruikers account inschakelt met [aangepaste beleids regels](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C). U schakelt aanmelden in door een SAML- [technische profiel](saml-technical-profile.md) toe te voegen aan een aangepast beleid.

## <a name="prerequisites"></a>Vereisten

- Voer de stappen in aan de [slag met aangepast beleid in azure Active Directory B2C](custom-policy-get-started.md).
- Zorg ervoor dat u toegang hebt tot een pfx-bestand van het certificaat met een persoonlijke sleutel. U kunt een eigen ondertekend certificaat genereren en dit uploaden naar Azure AD B2C. Azure AD B2C gebruikt dit certificaat voor het ondertekenen van de SAML-aanvraag die is verzonden naar uw SAML-ID-provider.
- Om Azure het wacht woord voor het pfx-bestand te accepteren, moet het wacht woord worden versleuteld met de optie TripleDES-SHA1 in het export hulpprogramma voor Windows-certificaat archief in plaats van AES256-SHA256.

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet uw certificaat opslaan in uw Azure AD B2C-Tenant.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
5. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
6. Kies `Upload`voor **Opties**.
7. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `SamlCert`. De prefix `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Blader naar en selecteer het pfx-bestand van het certificaat met de persoonlijke sleutel.
9. Klik op **Maken**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat gebruikers zich aanmelden met een ADFS-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een ADFS-account definiëren als een claim provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid.

1. Open *TrustFrameworkExtensions. XML*.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:

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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Vervang `your-ADFS-domain` door de naam van het ADFS-domein en vervang de waarde van de **Identity provider** -uitvoer claim door uw DNS (wille keurige waarde die uw domein aangeeft).
5. Sla het bestand op.

### <a name="upload-the-extension-file-for-verification"></a>Upload het extensie bestand voor verificatie

Nu hebt u uw beleid zodanig geconfigureerd dat Azure AD B2C weet hoe u kunt communiceren met ADFS-account. Upload het extensie bestand van uw beleid alleen om te bevestigen dat er tot nu toe geen problemen zijn.

1. Selecteer op de pagina **aangepaste beleids regels** in uw Azure AD B2C-Tenant de optie **beleid uploaden**.
2. Schakel **het beleid overschrijven als dit bestaat**in en selecteer vervolgens het *TrustFrameworkExtensions. XML-* bestand.
3. Klik op **Uploaden**.

> [!NOTE]
> De B2C-extensie van Visual Studio code maakt gebruik van ' socialIdpUserId '. Er is ook een sociaal beleid vereist voor ADFS.
>

## <a name="register-the-claims-provider"></a>De claim provider registreren

Op dit moment is de ID-provider ingesteld, maar is deze niet beschikbaar in de beschik bare registratie-of aanmeldings schermen. Om het beschikbaar te maken, maakt u een kopie van een bestaande sjabloon gebruiker en wijzigt u deze zo dat deze ook de ADFS-ID-provider heeft.

1. Open het bestand *TrustFrameworkBase. XML* van het Starter Pack.
2. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat `Id="SignUpOrSignIn"`bevat.
3. Open *TrustFrameworkExtensions. XML* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **UserJourney** -element dat u hebt gekopieerd als onderliggend element van het onderdeel **UserJourneys** .
5. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `SignUpSignInADFS`.

### <a name="display-the-button"></a>De knop weer geven

Het element **ClaimsProviderSelection** is vergelijkbaar met een id-provider knop op een registratie-of aanmeldings scherm. Als u een **ClaimsProviderSelection** -element toevoegt voor een ADFS-account, wordt een nieuwe knop weer gegeven wanneer een gebruiker op de pagina terechtkomt.

1. Zoek het **OrchestrationStep** -element dat `Order="1"` bevat in de gebruikers traject die u hebt gemaakt.
2. Voeg onder **ClaimsProviderSelections**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in op een geschikte waarde, bijvoorbeeld `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop aan een actie koppelen

Nu er een knop aanwezig is, moet u deze koppelen aan een actie. De actie in dit geval is voor Azure AD B2C om te communiceren met een ADFS-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die `Order="2"` bevat in de gebruikers reis.
2. Voeg het volgende **ClaimsExchange** -element toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de id die u hebt gebruikt voor **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId** bij naar de id van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `Contoso-SAML2`.

3. Sla het bestand *TrustFrameworkExtensions. XML* op en upload het opnieuw voor verificatie.


## <a name="configure-an-adfs-relying-party-trust"></a>Een ADFS Relying Party-vertrouwens relatie configureren

Als u ADFS als een id-provider in Azure AD B2C wilt gebruiken, moet u een AD FS-Relying Party vertrouwens relatie met de Azure AD B2C SAML-meta gegevens maken. In het volgende voor beeld ziet u een URL-adres naar de SAML-meta gegevens van een Azure AD B2C technisch profiel:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Vervang de volgende waarden:

- **uw-Tenant** met de naam van uw Tenant, zoals your-Tenant.onmicrosoft.com.
- **uw-beleid** met de naam van uw beleid. Bijvoorbeeld B2C_1A_signup_signin_adfs.
- **uw technische profiel** met de naam van uw technische profiel voor de SAML-identiteits provider. Bijvoorbeeld contoso-SAML2.

Open een browser en navigeer naar de URL. Zorg ervoor dat u de juiste URL typt en dat u toegang hebt tot het XML-bestand met meta gegevens. Als u een nieuwe Relying Party-vertrouwens relatie wilt toevoegen met behulp van de AD FS-beheer module en de instellingen hand matig wilt configureren, voert u de volgende procedure uit op een Federatie server. Lidmaatschap van **Administrators** of gelijkwaardig op de lokale computer is de minimale vereiste om deze procedure te volt ooien.

1. Selecteer in Serverbeheer **extra**en selecteer vervolgens ADFS- **beheer**.
2. Selecteer **vertrouwens relatie van Relying Party toevoegen**.
3. Kies op de pagina **Welkom** de optie **claims herkennen**en klik vervolgens op **starten**.
4. Selecteer op de pagina **gegevens bron selecteren** de optie **gegevens importeren over de relying party online publiceren of op een lokaal netwerk**, geef uw Azure AD B2C meta gegevens-URL op en klik op **volgende**.
5. Voer op de pagina **weergave naam opgeven** een **weergave naam**in, onder **notities**, voer een beschrijving in voor deze Relying Party-vertrouwens relatie en klik vervolgens op **volgende**.
6. Selecteer op de pagina **Access Control beleid kiezen** een beleid en klik vervolgens op **volgende**.
7. Controleer de instellingen op de pagina **gereed om een vertrouwens relatie toe te voegen** en klik vervolgens op **volgende** om uw Relying Party vertrouwens gegevens op te slaan.
8. Klik op de pagina **volt ooien** op **sluiten**. met deze actie wordt automatisch het dialoog venster **claim regels bewerken** weer gegeven.
9. Selecteer **regel toevoegen**.
10. Selecteer in **claim regel sjabloon** **LDAP-kenmerken als claims verzenden**.
11. Geef een **claim regel naam**op. Selecteer voor het **kenmerk archief** **Active Directory selecteren**, voeg de volgende claims toe en klik vervolgens op **volt ooien** en **OK**.

    | LDAP-kenmerk | Uitgaand claim type |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Achternaam | family_name |
    | Opgegeven naam | given_name |
    | E-mail adres | e-mail |
    | Weergave naam | name |

    Houd er rekening mee dat deze namen niet worden weer gegeven in de vervolg keuzelijst uitgaand claim type. U moet deze hand matig invoeren in. (De vervolg keuzelijst kan feitelijk worden bewerkt).

12.  Op basis van uw certificaat type moet u mogelijk het HASH-algoritme instellen. Selecteer in het venster Eigenschappen van Relying Party Trust (B2C demonstratie) het tabblad **Geavanceerd** en wijzig het **algoritme voor beveiligde hashes** in `SHA-256`en klik op **OK**.
13. Selecteer in Serverbeheer **extra**en selecteer vervolgens ADFS- **beheer**.
14. Selecteer de Relying Party-vertrouwens relatie die u hebt gemaakt, selecteer **bijwerken uit federatieve meta gegevens**en klik vervolgens op **bijwerken**.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-Tenant. In deze sectie vindt u optionele stappen die u kunt uitvoeren om een test toepassing te maken als u dit nog niet hebt gedaan.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Het Relying Party bestand bijwerken en testen

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Maak een kopie van *SignUpOrSignIn. XML* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInADFS. XML*.
2. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** met een unieke waarde bij. Bijvoorbeeld `SignUpSignInADFS`.
3. Werk de waarde van **PublicPolicyUri** bij met de URI voor het beleid. Bijvoorbeeld`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Werk de waarde van het kenmerk **ReferenceId** in **DefaultUserJourney** bij zodat dit overeenkomt met de id van de nieuwe gebruikers traject die u hebt gemaakt (SignUpSignInADFS).
5. Sla de wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
6. Zorg ervoor dat Azure AD B2C toepassing die u hebt gemaakt, is geselecteerd in het veld **toepassing selecteren** en test deze door op **nu uitvoeren**te klikken.

