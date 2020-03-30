---
title: Aanmelden met een Azure AD-account instellen met aangepaste beleidsregels
titleSuffix: Azure AD B2C
description: Aanmelden met een Azure Active Directory-account in Azure Active Directory B2C met behulp van aangepast beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5a6c85ebed7271655745de45694542fb359836e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188407"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelding instellen met een Azure Active Directory-account met aangepaste beleidsregels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel ziet u hoe u aanmelding inschakelt voor gebruikers uit een Azure Active Directory -organisatie (Azure AD) met behulp van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Vereisten

Voer de stappen uit in [Aan de slag met aangepast beleid in Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Een toepassing registreren

Als u aanmelding wilt inschakelen voor gebruikers van een specifieke Azure AD-organisatie, moet u een toepassing registreren binnen de azure AD-tenant van de organisatie Azure.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD-tenant voor de organisatie bevat (bijvoorbeeld contoso.com). Selecteer het **filter Directory + abonnement** in het bovenste menu en kies vervolgens de map met uw Azure AD-tenant.
1. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Voer een **naam** in voor uw toepassing. Bijvoorbeeld `Azure AD B2C App`.
1. Accepteer de standaardselectie van **accounts in deze organisatiemap alleen** voor deze toepassing.
1. Accepteer voor de **URI-omleiding**de waarde van **web**en voer de `your-B2C-tenant-name` volgende URL in alle kleine letters in, waarbij de naam van uw Azure AD B2C-tenant wordt vervangen.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Bijvoorbeeld `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Selecteer **Registreren**. Neem de **toepassings-id (client) op** voor gebruik in een latere stap.
1. Selecteer **Certificaten & geheimen**en selecteer Vervolgens Nieuw **clientgeheim**.
1. Voer een **beschrijving** voor het geheim in, selecteer een vervaldatum en selecteer **Toevoegen**. Noteer de **waarde** van het geheim voor gebruik in een latere stap.

## <a name="configuring-optional-claims"></a>Optionele claims configureren

Als u de `family_name` claims `given_name` en claims van Azure AD wilt ophalen, u optionele claims voor uw toepassing configureren in de gebruikersinterface of toepassingslijst van Azure portal. Zie [Optionele claims voor uw Azure AD-app voor](../active-directory/develop/active-directory-optional-claims.md)meer informatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Zoek naar **Azure Active Directory** en selecteer deze optie.
1. Selecteer **app-registraties**in de sectie **Beheren** .
1. Selecteer de toepassing waarvoor u optionele claims wilt configureren in de lijst.
1. Selecteer **tokenconfiguratie (voorbeeld)** in de sectie **Beheren** .
1. Selecteer **Optionele claim toevoegen**.
1. Selecteer het tokentype dat u wilt configureren.
1. Selecteer de optionele claims die u wilt toevoegen.
1. Klik op**toevoegen**.

## <a name="create-a-policy-key"></a>Een beleidssleutel maken

U moet de toepassingssleutel die u hebt gemaakt opslaan in uw Azure AD B2C-tenant.

1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies vervolgens de map met uw Azure AD B2C-tenant.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **onder Beleid**het Framework Voor **identiteitservaring**.
1. Selecteer **Beleidstoetsen** en selecteer **Toevoegen**.
1. Kies **Options**voor `Manual`Opties .
1. Voer een **naam** in voor de beleidssleutel. Bijvoorbeeld `ContosoAppSecret`.  Het voorvoegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel wanneer deze wordt gemaakt, zodat de verwijzing in de XML in de volgende sectie is om *te B2C_1A_ContosoAppSecret*.
1. Voer in **Secret**het geheim van uw klant in dat u eerder hebt opgenomen.
1. Selecteer **voor sleutelgebruik**. `Signature`
1. Selecteer **Maken**.

## <a name="add-a-claims-provider"></a>Een claimprovider toevoegen

Als u wilt dat gebruikers zich aanmelden met Azure AD, moet u Azure AD definiëren als een claimprovider waarmee Azure AD B2C via een eindpunt kan communiceren. Het eindpunt biedt een reeks claims die worden gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U Azure AD definiëren als een claimprovider door Azure AD toe te voegen aan het **element ClaimsProvider** in het extensiebestand van uw beleid.

1. Open het bestand *TrustFrameworkExtensions.xml.*
2. Zoek het element **Claimproviders.** Als het niet bestaat, voeg het onder het hoofdelement.
3. Voeg als volgt een nieuwe **ClaimProvider** toe:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Werk onder het element **ClaimsProvider** de waarde voor **Domein** bij naar een unieke waarde die kan worden gebruikt om deze te onderscheiden van andere identiteitsproviders. Bijvoorbeeld `Contoso`. U plaatst geen `.com` instelling aan het einde van deze domeininstelling.
5. Werk onder het element **ClaimsProvider** de waarde voor **DisplayName** bij naar een vriendelijke naam voor de claimprovider. Deze waarde wordt momenteel niet gebruikt.

### <a name="update-the-technical-profile"></a>Het technische profiel bijwerken

Als u een token wilt ophalen uit het AZURE AD-eindpunt, moet u de protocollen definiëren die Azure AD B2C moet gebruiken om te communiceren met Azure AD. Dit gebeurt binnen het **TechnicalProfile** element van **ClaimsProvider**.

1. Werk de ID van het element **TechnicalProfile bij.** Deze ID wordt gebruikt om te verwijzen naar dit technische `OIDC-Contoso`profiel uit andere delen van het beleid, bijvoorbeeld .
1. De waarde voor **DisplayName**bijwerken . Deze waarde wordt weergegeven op de aanmeldingsknop op uw aanmeldingsscherm.
1. De waarde voor **Beschrijving**bijwerken .
1. Azure AD maakt gebruik van het OpenID Connect-protocol, dus zorg ervoor dat de waarde voor **Protocol** is `OpenIdConnect`.
1. Stel de waarde `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`van `tenant-name` de **metagegevens** in op , waar is de naam van uw Azure AD-tenant. Bijvoorbeeld: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Stel **client_id** in op de toepassings-ID van de registratie van de toepassing.
1. Werk onder **CryptographicKeys**de waarde van **StorageReferenceId** bij naar de naam van de beleidssleutel die u eerder hebt gemaakt. Bijvoorbeeld `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Het extensiebestand uploaden ter verificatie

U hebt uw beleid inmiddels zo geconfigureerd dat Azure AD B2C weet hoe u met uw Azure AD-map moet communiceren. Probeer het extensiebestand van uw beleid te uploaden om te bevestigen dat het tot nu toe geen problemen heeft.

1. Selecteer Op de pagina **Aangepast beleid** in uw Azure AD B2C-tenant de optie **Beleid uploaden**.
1. Het **beleid overschrijven inschakelen als het bestaat,** en blader naar en selecteer het bestand *TrustFrameworkExtensions.xml.*
1. Klik op **Uploaden**.

## <a name="register-the-claims-provider"></a>Registreer de schadeprovider

Op dit moment is de identiteitsprovider ingesteld, maar deze is nog niet beschikbaar in een van de aanmeldings-/aanmeldingspagina's. Als u deze beschikbaar wilt maken, maakt u een duplicaat van een bestaande gebruikersreis van een bestaande sjabloon en wijzigt u deze vervolgens zodat deze ook de Azure AD-identiteitsprovider heeft:

1. Open het bestand *TrustFrameworkBase.xml* vanuit het startpakket.
1. De volledige inhoud van het **UserJourney-element,** dat het element UserJourney bevat, zoeken en kopiëren. `Id="SignUpOrSignIn"`
1. Open het *element TrustFrameworkExtensions.xml* en zoek het element **UserJourneys.** Als het element niet bestaat, voegt u er een toe.
1. Plak de volledige inhoud van het **Element UserJourney** die u als onderliggend element van het element **UserJourneys** hebt gekopieerd.
1. Wijzig de naam van de id van de gebruikersreis. Bijvoorbeeld `SignUpSignInContoso`.

### <a name="display-the-button"></a>De knop weergeven

Het element **ClaimsProviderSelection** is analoog aan een knop van identiteitsprovider op een aanmeldings-/aanmeldingspagina. Als u een **Element ClaimsProviderSelection** toevoegt voor Azure AD, wordt er een nieuwe knop weergegeven wanneer een gebruiker op de pagina landt.

1. Zoek het element **OrchestrationStep** dat is bevat `Order="1"` in het gebruikerstraject dat u hebt gemaakt in *TrustFrameworkExtensions.xml.*
1. Voeg onder **ClaimsProviderSelections**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in `ContosoExchange`op een geschikte waarde, bijvoorbeeld:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop koppelen aan een actie

Nu je een knop hebt, moet je deze koppelen aan een actie. De actie is in dit geval dat Azure AD B2C communiceert met Azure AD om een token te ontvangen. Koppel de knop aan een actie door het technische profiel voor uw Azure AD-claimprovider te koppelen:

1. Zoek de **OrchestrationStep** die in het gebruikerstraject is meegemaakt. `Order="2"`
1. Voeg het volgende **ClaimExchange-element** toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor **id** die u hebt gebruikt voor **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId bij** naar de **id** van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `OIDC-Contoso`.

1. Sla het *bestand TrustFrameworkExtensions.xml op* en upload het opnieuw voor verificatie.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-tenant. In deze sectie worden optionele stappen weergegeven die u voltooien om een testtoepassing te maken als u dit nog niet hebt gedaan.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Het bestand van de relying party bijwerken en testen

Werk het RP-bestand (relying party) bij waarmee u de gebruikersreis initieert die u hebt gemaakt.

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInContoso.xml*.
1. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** bij met een unieke waarde. Bijvoorbeeld `SignUpSignInContoso`.
1. Werk de waarde van **PublicPolicyUri bij** met de URI voor het beleid. Bijvoorbeeld `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Werk de waarde van het **kenmerk ReferenceId** bij in **DefaultUserJourney** zodat deze overeenkomt met de id van het gebruikerstraject dat u eerder hebt gemaakt. U zich bijvoorbeeld *aanmelden bij SignUpSignInContoso*.
1. Sla uw wijzigingen op en upload het bestand.
1. Selecteer **onder Aangepast beleid**het nieuwe beleid in de lijst.
1. Selecteer in de vervolgkeuzelijst **Selectie-toepassing** de Azure AD B2C-toepassing die u eerder hebt gemaakt. *Testapp1*bijvoorbeeld .
1. Kopieer het **eindpunt Nu uitvoeren** en open het in een privébrowservenster, bijvoorbeeld incognitomodus in Google Chrome of een InPrivate-venster in Microsoft Edge. Als u opent in een privébrowservenster, u het volledige gebruikerstraject testen door geen gebruik te maken van azure-AD-referenties in de cache.
1. Selecteer de aanmeldingsknop voor Azure AD, bijvoorbeeld *Contoso-werknemer,* en voer de referenties in voor een gebruiker in uw Azure AD-organisatietenant. U wordt gevraagd om de toepassing te autoriseren en vervolgens gegevens voor uw profiel in te voeren.

Als het aanmeldingsproces is geslaagd, `https://jwt.ms`wordt uw browser omgeleid naar , waarin de inhoud van het token wordt weergegeven dat is geretourneerd door Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

Wanneer u met aangepast beleid werkt, hebt u soms aanvullende informatie nodig wanneer u een probleem opgeeft met een beleid tijdens de ontwikkeling.

Om problemen te diagnosticeren, u het beleid tijdelijk in de ontwikkelaarsmodus plaatsen en logboeken verzamelen met Azure Application Insights. Ontdek hoe in [Azure Active Directory B2C: Logboeken verzamelen](troubleshoot-with-application-insights.md).
