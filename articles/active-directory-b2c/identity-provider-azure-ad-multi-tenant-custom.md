---
title: Aanmelden voor Azure AD met meerdere tenant's instellen op basis van aangepaste beleidsregels
titleSuffix: Azure AD B2C
description: Voeg een Azure AD-identiteitsprovider met meerdere tenant's toe met aangepaste beleidsregels in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 320723744e1366fdc73cd0593fb0ebece03367f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678111"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelden voor Azure Active Directory met meerdere tenant's instellen met aangepaste beleidsregels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel ziet u hoe u aanmelding inschakelt voor gebruikers die het eindpunt voor Azure Active Directory (Azure AD) met meerdere tenant's gebruiken met behulp van [aangepast beleid](custom-policy-overview.md) in Azure AD B2C. Hierdoor kunnen gebruikers van meerdere Azure AD-tenants zich aanmelden met Azure AD B2C, zonder dat u voor elke tenant een identiteitsprovider hoeft te configureren. Gasten van een van deze huurders kunnen zich echter **niet** aanmelden. Daarvoor moet u [elke tenant individueel configureren.](identity-provider-azure-ad-single-tenant-custom.md)

## <a name="prerequisites"></a>Vereisten

Voer de stappen uit in [Aan de slag met aangepast beleid in Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Een toepassing registreren

Als u aanmelding wilt inschakelen voor gebruikers van een specifieke Azure AD-organisatie, moet u een toepassing registreren binnen de azure AD-tenant van de organisatie Azure.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD-tenant voor de organisatie bevat (bijvoorbeeld contoso.com). Selecteer het **filter Directory + abonnement** in het bovenste menu en kies vervolgens de map met uw tenant.
1. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Voer een **naam** in voor uw toepassing. Bijvoorbeeld `Azure AD B2C App`.
1. Selecteer **Accounts in een organisatiemap** voor deze toepassing.
1. Accepteer voor de **URI-omleiding**de waarde van **web**en voer de `your-B2C-tenant-name` volgende URL in alle kleine letters in, waarbij de naam van uw Azure AD B2C-tenant wordt vervangen.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Bijvoorbeeld `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selecteer **Registreren**. Neem de **toepassings-id (client) op** voor gebruik in een latere stap.
1. Selecteer **Certificaten & geheimen**en selecteer Vervolgens Nieuw **clientgeheim**.
1. Voer een **beschrijving** voor het geheim in, selecteer een vervaldatum en selecteer **Toevoegen**. Noteer de **waarde** van het geheim voor gebruik in een latere stap.

## <a name="configuring-optional-claims"></a>Optionele claims configureren

Als u de `family_name` claims `given_name` en claims van Azure AD wilt ophalen, u optionele claims voor uw toepassing configureren in de gebruikersinterface of toepassingslijst van Azure portal. Zie [Optionele claims voor uw Azure AD-app voor](../active-directory/develop/active-directory-optional-claims.md)meer informatie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Zoek naar **Azure Active Directory** en selecteer deze optie.
1. Selecteer **app-registraties**in de sectie **Beheren** .
1. Selecteer de toepassing waarvoor u optionele claims wilt configureren in de lijst.
1. Selecteer **Tokenconfiguratie**in de sectie **Beheren** .
1. Selecteer **Optionele claim toevoegen**.
1. Selecteer **ID**voor het **type Token**.
1. Selecteer de optionele claims `family_name` `given_name`die u wilt toevoegen en .
1. Klik op **Add**.

## <a name="create-a-policy-key"></a>Een beleidssleutel maken

U moet de toepassingssleutel die u hebt gemaakt opslaan in uw Azure AD B2C-tenant.

1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies vervolgens de map met uw Azure AD B2C-tenant.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **onder Beleid**het Framework Voor **identiteitservaring**.
1. Selecteer **Beleidstoetsen** en selecteer **Toevoegen**.
1. Kies **Options**voor `Manual`Opties .
1. Voer een **naam** in voor de beleidssleutel. Bijvoorbeeld `AADAppSecret`.  Het voorvoegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel wanneer deze wordt gemaakt, zodat de verwijzing in de XML in de volgende sectie is om te *B2C_1A_AADAppSecret*.
1. Voer in **Secret**het geheim van uw klant in dat u eerder hebt opgenomen.
1. Selecteer **voor sleutelgebruik**. `Signature`
1. Selecteer **Maken**.

## <a name="add-a-claims-provider"></a>Een claimprovider toevoegen

Als u wilt dat gebruikers zich aanmelden met Azure AD, moet u Azure AD definiëren als een claimprovider waarmee Azure AD B2C via een eindpunt kan communiceren. Het eindpunt biedt een reeks claims die worden gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U Azure AD definiëren als een claimprovider door Azure AD toe te voegen aan het **element ClaimsProvider** in het extensiebestand van uw beleid.

1. Open het bestand *TrustFrameworkExtensions.xml.*
1. Zoek het element **Claimproviders.** Als het niet bestaat, voeg het onder het hoofdelement.
1. Voeg als volgt een nieuwe **ClaimProvider** toe:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. Werk onder het element **ClaimsProvider** de waarde voor **Domein** bij naar een unieke waarde die kan worden gebruikt om deze te onderscheiden van andere identiteitsproviders.
1. Werk onder het element **TechnicalProfile** de waarde voor `Contoso Employee` **DisplayName**bij, bijvoorbeeld . Deze waarde wordt weergegeven op de aanmeldingsknop op uw aanmeldingspagina.
1. Stel **client_id** in op de toepassings-id van de Azure AD-multitenanttoepassing die u eerder hebt geregistreerd.
1. Werk onder **CryptographicKeys**de waarde van **StorageReferenceId** bij naar de naam van de beleidssleutel die eerder is gemaakt. Bijvoorbeeld `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Toegang beperken

> [!NOTE]
> Als `https://login.microsoftonline.com/` u de waarde gebruikt voor **ValidTokenIssuerPrefixes** kunnen alle Azure AD-gebruikers zich aanmelden bij uw toepassing.

U moet de lijst met geldige token-emittenten bijwerken en de toegang beperken tot een specifieke lijst met Azure AD-tenantgebruikers die zich kunnen aanmelden.

Als u de waarden wilt verkrijgen, bekijkt u de metagegevens van de Detectie openID Connect voor elk van de Azure AD-tenants waarvan u wilt dat gebruikers zich aanmelden. De indeling van de URL `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`met `your-tenant` ametjes is vergelijkbaar met , waar is de naam van uw Azure AD-tenant. Bijvoorbeeld:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Voer deze stappen uit voor elke Azure AD-tenant die moet worden gebruikt om u aan te melden:

1. Open uw browser en ga naar de URL metagegevens van OpenID Connect voor de tenant. Zoek het **object van** de uitgever en noteer de waarde ervan. Het moet lijken `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`op .
1. Kopieer en plak de waarde in de key **ValidTokenIssuerPrefixes.** Scheid meerdere emittenten met een komma. Een voorbeeld met twee emittenten `ClaimsProvider` wordt weergegeven in het vorige XML-voorbeeld.

### <a name="upload-the-extension-file-for-verification"></a>Het extensiebestand uploaden ter verificatie

U hebt uw beleid inmiddels zo geconfigureerd dat Azure AD B2C weet hoe u met uw Azure AD-mappen communiceren. Probeer het extensiebestand van uw beleid te uploaden om te bevestigen dat het tot nu toe geen problemen heeft.

1. Selecteer Op de pagina **Aangepast beleid** in uw Azure AD B2C-tenant de optie **Beleid uploaden**.
2. Het **beleid overschrijven inschakelen als het bestaat,** en blader naar en selecteer het bestand *TrustFrameworkExtensions.xml.*
3. Selecteer **Uploaden**.

## <a name="register-the-claims-provider"></a>Registreer de schadeprovider

Op dit moment is de identiteitsprovider ingesteld, maar deze is niet beschikbaar in een van de aanmeldings-/aanmeldingsschermen. Als u deze beschikbaar wilt maken, maakt u een duplicaat van een bestaande sjabloongebruikersreis en wijzigt u deze vervolgens zodat deze ook de Azure AD-identiteitsprovider heeft.

1. Open het bestand *TrustFrameworkBase.xml* vanuit het startpakket.
2. De volledige inhoud van het **UserJourney-element,** dat het element UserJourney bevat, zoeken en kopiëren. `Id="SignUpOrSignIn"`
3. Open het *element TrustFrameworkExtensions.xml* en zoek het element **UserJourneys.** Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **Element UserJourney** die u als onderliggend element van het element **UserJourneys** hebt gekopieerd.
5. Wijzig de naam van de id van de gebruikersreis. Bijvoorbeeld `SignUpSignInContoso`.

### <a name="display-the-button"></a>De knop weergeven

Het **element ClaimsProviderSelection** is analoog aan een knop van identiteitsprovider op een aanmeldings-/aanmeldingsscherm. Als u een **Element ClaimsProviderSelection** toevoegt voor Azure AD, wordt er een nieuwe knop weergegeven wanneer een gebruiker op de pagina landt.

1. Zoek het element **OrchestrationStep** dat is bevat `Order="1"` in het gebruikerstraject dat u hebt gemaakt in *TrustFrameworkExtensions.xml.*
1. Voeg onder **ClaimsProviderSelects**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in `AzureADExchange`op een geschikte waarde, bijvoorbeeld:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop koppelen aan een actie

Nu je een knop hebt, moet je deze koppelen aan een actie. De actie is in dit geval dat Azure AD B2C communiceert met Azure AD om een token te ontvangen. Koppel de knop aan een actie door het technische profiel voor uw Azure AD-claimprovider te koppelen.

1. Zoek de **OrchestrationStep** die in het gebruikerstraject is meegemaakt. `Order="2"`
2. Voeg het volgende **ClaimExchange-element** toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor **id** die u hebt gebruikt voor **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId bij** naar de **id** van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `Common-AAD`.

3. Sla het *bestand TrustFrameworkExtensions.xml op* en upload het opnieuw voor verificatie.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-tenant. In deze sectie worden optionele stappen weergegeven die u voltooien om een testtoepassing te maken als u dit nog niet hebt gedaan.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Het bestand van de relying party bijwerken en testen

Werk het RP-bestand (relying party) bij waarmee u de gebruikersreis initieert die u hebt gemaakt:

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignContoso.xml*.
1. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** bij met een unieke waarde. Bijvoorbeeld `SignUpSignInContoso`.
1. Werk de waarde van **PublicPolicyUri bij** met de URI voor het beleid. Bijvoorbeeld `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Werk de waarde van het **kenmerk ReferenceId** bij in **DefaultUserJourney** zodat deze overeenkomt met de id van het gebruikerstraject dat u eerder hebt gemaakt. U zich bijvoorbeeld *aanmelden bij SignUpSignInContoso*.
1. Sla uw wijzigingen op en upload het bestand.
1. Selecteer **onder Aangepast beleid**het nieuwe beleid in de lijst.
1. Selecteer in de vervolgkeuzelijst **Selectie-toepassing** de Azure AD B2C-toepassing die u eerder hebt gemaakt. *Testapp1*bijvoorbeeld .
1. Kopieer het **eindpunt Nu uitvoeren** en open het in een privébrowservenster, bijvoorbeeld incognitomodus in Google Chrome of een InPrivate-venster in Microsoft Edge. Als u opent in een privébrowservenster, u het volledige gebruikerstraject testen door geen gebruik te maken van azure-AD-referenties in de cache.
1. Selecteer de aanmeldingsknop voor Azure AD, bijvoorbeeld *Contoso-werknemer,* en voer de referenties in voor een gebruiker in een van uw Azure AD-organisatietenants. U wordt gevraagd om de toepassing te autoriseren en vervolgens gegevens voor uw profiel in te voeren.

Als het aanmeldingsproces is geslaagd, `https://jwt.ms`wordt uw browser omgeleid naar , waarin de inhoud van het token wordt weergegeven dat is geretourneerd door Azure AD B2C.

Als u de aanmeldingsmogelijkheid voor meerdere tenant wilt testen, voert u de laatste twee stappen uit met de referenties voor een gebruiker die een andere Azure AD-tenant heeft.

## <a name="next-steps"></a>Volgende stappen

Wanneer u met aangepast beleid werkt, hebt u soms aanvullende informatie nodig wanneer u een probleem opgeeft met een beleid tijdens de ontwikkeling.

Om problemen te diagnosticeren, u het beleid tijdelijk in de ontwikkelaarsmodus plaatsen en logboeken verzamelen met Azure Application Insights. Ontdek hoe in [Azure Active Directory B2C: Logboeken verzamelen](troubleshoot-with-application-insights.md).
