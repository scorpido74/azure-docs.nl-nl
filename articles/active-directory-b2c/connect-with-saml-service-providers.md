---
title: Azure AD B2C configureren als SAML IdP voor uw toepassingen
title-suffix: Azure AD B2C
description: Azure AD B2C configureren om SAML-protocolbeweringen te geven aan uw toepassingen (serviceproviders). Azure AD B2C fungeert als de enige identiteitsprovider (IdP) voor uw SAML-toepassing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a72b5b50daaae33336de9caab5202c2bf42f5c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051609"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Een SAML-toepassing registreren in Azure AD B2C

In dit artikel leert u hoe u Azure Active Directory B2C (Azure AD B2C) configureert om te fungeren als een SAML-identiteitsprovider (Security Assertion Markup Language) voor uw toepassingen.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Overzicht van scenario's

Organisaties die Azure AD B2C gebruiken als oplossing voor klantidentiteit en toegangsbeheer, hebben mogelijk interactie nodig met identiteitsproviders of toepassingen die zijn geconfigureerd om te verifiëren met behulp van het SAML-protocol.

Azure AD B2C bereikt SAML-interoperabiliteit op twee manieren:

* Door op te treden als *identiteitsprovider* (IdP) en single-sign-on (SSO) te bereiken met SAML-gebaseerde serviceproviders (uw applicaties)
* Door op te treden als *dienstverlener* (SP) en te communiceren met SAML-gebaseerde identiteitsproviders zoals Salesforce en ADFS

![Diagram met B2C als identiteitsprovider links en B2C als dienstverlener rechts](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Een samenvatting van de twee niet-exclusieve kernscenario's met SAML:

| Scenario | Azure AD B2C-rol | Uitleg |
| -------- | ----------------- | ------- |
| Mijn applicatie verwacht dat een SAML-bewering een verificatie voltooit. | **Azure AD B2C fungeert als identiteitsprovider (IdP)**<br />Azure AD B2C fungeert als een SAML IdP voor de toepassingen. | Dit artikel. |
| Mijn gebruikers hebben single-sign-on nodig bij een SAML-compatibele identiteitsprovider zoals ADFS, Salesforce of Shibboleth.  | **Azure AD B2C fungeert als serviceprovider (SP)**<br />Azure AD B2C fungeert als serviceprovider wanneer u verbinding maakt met de SAML-identiteitsprovider. Het is een federatie proxy tussen uw aanvraag en de SAML identiteit provider.  | <ul><li>[Aanmelden bij ADFS instellen als SAML IdP met aangepaste beleidsregels](identity-provider-adfs2016-custom.md)</li><li>[Aanmelden bij een Salesforce SAML-provider instellen met aangepaste beleidsregels](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Vereisten

* Voer de stappen uit in [Aan de slag met aangepast beleid in Azure AD B2C](custom-policy-get-started.md). U hebt het aangepaste beleid *socialAndLocalAccounts* nodig van het aangepaste beleidsstartpakket dat in het artikel wordt besproken.
* Basisbegrip van het SAML-protocol (Security Assertion Markup Language).
* Een webtoepassing die is geconfigureerd als SAML-serviceprovider (SP). Voor deze zelfstudie u een [SAML-testtoepassing][samltest] gebruiken die wij leveren.

## <a name="components-of-the-solution"></a>Componenten van de oplossing

Er zijn drie belangrijke onderdelen die nodig zijn voor dit scenario:

* **SAML-serviceprovider** met de mogelijkheid om SAML-verzoeken te verzenden en SAML-beweringen van Azure AD B2C te ontvangen, te decoderen en erop te reageren. Dit wordt ook wel de relying party genoemd.
* Openbaar beschikbaar **SAML-metagegevenseindpunt** voor uw serviceprovider.
* [Azure AD B2C-tenant](tutorial-create-tenant.md)

Als u nog geen SAML-serviceprovider en een bijbehorend eindpunt voor metagegevens hebt, u deze saml-toepassing gebruiken die we beschikbaar hebben gesteld voor het testen:

[SAML-testtoepassing][samltest]

## <a name="1-set-up-certificates"></a>1. Certificaten instellen

Als u een vertrouwensrelatie wilt opbouwen tussen uw serviceprovider en Azure AD B2C, moet u de X509-certificaten van de webapp leveren.

* **Servicesprovidercertificaten**
  * Certificaat met een privésleutel die is opgeslagen in uw web-app. Dit certificaat wordt door uw serviceprovider gebruikt om de SAML-aanvraag te ondertekenen die naar Azure AD B2C is verzonden. Azure AD B2C leest de openbare sleutel van de metagegevens van de serviceprovider om de handtekening te valideren.
  * (Optioneel) Certificaat met een privésleutel die is opgeslagen in uw web-app. Azure AD B2C leest de openbare sleutel van de metagegevens van de serviceprovider om de SAML-bewering te versleutelen. De serviceprovider gebruikt vervolgens de privésleutel om de bewering te decoderen.
* **Azure AD B2C-certificaten**
  * Certificaat met een privésleutel in Azure AD B2C. Dit certificaat wordt door Azure AD B2C gebruikt om het SAML-antwoord te ondertekenen dat naar uw serviceprovider is verzonden. Uw serviceprovider leest de openbare sleutel voor Azure AD B2C-metagegevens om de handtekening van het SAML-antwoord te valideren.

U een certificaat gebruiken dat is uitgegeven door een openbare certificeringsinstantie of, voor deze zelfstudie, een zelfondertekend certificaat.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Een zelfondertekend certificaat opstellen

Als u nog geen certificaat hebt, u een zelfondertekend certificaat gebruiken voor deze zelfstudie. In Windows u de cmdlet [Nieuw-Zelfondertekendcertificaat](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) van PowerShell gebruiken om een certificaat te genereren.

1. Voer deze PowerShell-opdracht uit om een zelfondertekend certificaat te genereren. Wijzig `-Subject` het argument waar nodig voor uw toepassing en de naam van Azure AD B2C-tenant. U ook `-NotAfter` de datum aanpassen om een andere vervaldatum voor het certificaat op te geven.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Gebruikerscertificaten > **huidige gebruikerspersoonlijke** > **Personal** > **certificaten** > beheren **openen***yourappname.yourtenant.onmicrosoft.com*
1. Het certificaat > **Action** > **All Tasks** > **exporteren selecteren**
1. Selecteer **Ja** > **volgende** > **ja, exporteer de privésleutel** > **Volgende**
1. De standaardinstellingen voor **bestandsindeling exporteren accepteren**
1. Een wachtwoord opgeven voor het certificaat

### <a name="12-upload-the-certificate"></a>1.2 Het certificaat uploaden

Upload vervolgens het SAML-certificaat voor het ondertekenen van bevestiging en antwoord naar Azure AD B2C.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en blader naar uw Azure AD B2C-tenant.
1. Selecteer **onder Beleid**het framework voor **identiteitservaring** en vervolgens **Beleidssleutels**.
1. Selecteer **Toevoegen**en selecteer **Vervolgens Opties** > **uploaden**.
1. Voer een **naam in,** bijvoorbeeld *SamlIdpCert*. Het voorvoegsel *B2C_1A_* wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Upload uw certificaat met het besturingselement voor uploadbestanden.
1. Voer het wachtwoord van het certificaat in.
1. Selecteer **Maken**.
1. Controleer of de sleutel wordt weergegeven zoals verwacht. Bijvoorbeeld *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Uw beleid voorbereiden

### <a name="21-create-the-saml-token-issuer"></a>2.1 De SAML-tokenuitgever maken

Voeg nu de mogelijkheid toe voor uw tenant om SAML-tokens uit te geven, met behulp van [SAML-tokenuitgever](saml-issuer-technical-profile.md) en [SAML-sessieprovider](custom-policy-reference-sso.md#samlssosessionprovider) technische profielen.

Open `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** in het startpakket voor aangepaste beleidsregels.

Zoek `<ClaimsProviders>` de sectie en voeg het volgende XML-fragment toe.

U de waarde `IssuerUri` van de metagegevens wijzigen. Dit is de uitgever URI die wordt geretourneerd in de SAML-reactie van Azure AD B2C. Uw relying party-toepassing moet worden geconfigureerd om een uitgever URI te accepteren tijdens de validatie van SAML-bevestiging.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Voeg het SAML-beleid van relying party toe

Nu uw tenant SAML-beweringen kan geven, moet u het SAML-beleid van relying party maken en de gebruikersreis wijzigen zodat deze een SAML-bewering uitgeeft in plaats van een JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Aanmeldings- of aanmeldingsbeleid maken

1. Maak een kopie van het bestand *SignUpOrSignin.xml* in uw werkmap voor starterpack en sla deze op met een nieuwe naam. U bijvoorbeeld *SignUpOrSigninSAML.xml .* Dit is uw beleidsbestand van relying party.

1. Open het bestand *SignUpOrSigninSAML.xml* in de voorkeurseditor.

1. Wijzig `PolicyId` het `PublicPolicyUri` beleid in _B2C_1A_signup_signin_saml_ B2C_1A_signup_signin_saml `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` en zoals hieronder te zien.

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Voeg het volgende XML-fragment toe vlak voor het `<RelyingParty>` element. Deze XML overschrijft orkestratiestap 7 van de _signuporsignin-gebruikersreis._ Als u bent gestart vanuit een andere map in het startpakket of uw gebruikersreis hebt `order` aangepast door orchestration-stappen toe te voegen of te verwijderen, moet u ervoor zorgen dat het getal `LocalAccounts`(in `SocialAccounts` het element) is uitgelijnd met het nummer dat is opgegeven in de gebruikersreis voor de stap van de tokenuitgevende instelling (bijvoorbeeld in de andere startpakketmappen is het stapnummer 4 voor , 6 voor en 9 voor `SocialAndLocalAccountsWithMfa`).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Vervang het `<TechnicalProfile>` hele `<RelyingParty>` element in het element door de volgende XML voor technisch profiel.

    ```XML
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. Bijwerken `tenant-name` met de naam van uw Azure AD B2C-tenant.

Het beleidsbestand van uw uiteindelijke relying party moet er als volgt uitzien:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Uw beleidsmetagegevens uploaden en testen

Sla uw wijzigingen op en upload het nieuwe beleidsbestand. Nadat u beide beleidsregels (de extensie en de bestanden van de relying party) hebt geüpload, opent u een webbrowser en navigeert u naar de beleidsmetagegevens.

Azure AD B2C-beleid IDP-metagegevens zijn gegevens die worden gebruikt in het SAML-protocol om de configuratie van een SAML-identiteitsprovider bloot te leggen. Metagegevens definieert de locatie van de services, zoals aanmelding en afmelding, certificaten, aanmeldingsmethode en meer. De metagegevens van het Azure AD B2C-beleid zijn beschikbaar op de volgende URL. Vervang `tenant-name` de naam van uw Azure AD `policy-name` B2C-tenant en de naam (ID) van het beleid:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Uw aangepaste beleid en Azure AD B2C-tenant zijn nu gereed. Maak vervolgens een toepassingsregistratie in Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Installatietoepassing in de Azure AD B2C-map

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 Uw toepassing registreren in Azure Active Directory

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Vervolgens Nieuwe registratie**.
1. Voer een **naam** voor de toepassing in. Bijvoorbeeld *SAMLApp1*.
1. Selecteer onder **Ondersteunde accounttypen** **alleen Accounts in deze organisatiemap**
1. Selecteer **onder URI omleiden** **en**voer . `https://localhost` U wijzigt deze waarde later in het manifest van de toepassingsregistratie.
1. Selecteer **Registreren**.

### <a name="42-update-the-app-manifest"></a>4.2 Het app-manifest bijwerken

Voor SAML-apps zijn er verschillende eigenschappen die u moet configureren in het manifest van de toepassingsregistratie.

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar de toepassingsregistratie die u in de vorige sectie hebt gemaakt.
1. Selecteer **Onder Beheren**de optie **Manifest** om de manifesteditor te openen. U wijzigt verschillende eigenschappen in de volgende secties.

#### <a name="identifieruris"></a>identifierUris

Het `identifierUris` is een tekenreeksverzameling die door de gebruiker gedefinieerde URI(s) bevat die een web-app op unieke wijze identificeert in de Azure AD B2C-tenant. Uw serviceprovider moet deze waarde `Issuer` instellen in het element van een SAML-aanvraag.

#### <a name="samlmetadataurl"></a>samlMetadataUrl samlMetadataUrl

Deze eigenschap vertegenwoordigt de openbaar beschikbare URL van de serviceprovider. De URL met ametagegevens kan wijzen op een metagegevensbestand dat is geüpload naar een anoniem toegankelijk eindpunt, bijvoorbeeld blob-opslag.

De metagegevens zijn informatie die wordt gebruikt in het SAML-protocol om de configuratie van een SAML-partij, zoals een serviceprovider, bloot te leggen. Metagegevens definieert de locatie van de services zoals aanmelding en afmelding, certificaten, aanmeldingsmethode en meer. Azure AD B2C leest de metagegevens van de serviceprovider en handelt dienovereenkomstig. De metadata is niet vereist. U ook een aantal kenmerken opgeven, zoals het antwoord URI en afmelden URI rechtstreeks in het app-manifest.

Als er eigenschappen zijn opgegeven in *zowel* de URL van saml-metagegevens als in het manifest van de toepassingsregistratie, worden ze **samengevoegd.** De eigenschappen die zijn opgegeven in de URL met ametjes worden eerst verwerkt en hebben voorrang.

Gebruik voor deze zelfstudie, die de SAML-testtoepassing gebruikt, de volgende waarde voor: `samlMetadataUrl`

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (optioneel)

Als u geen URI metagegevens opgeeft, u de url van het antwoord expliciet opgeven. Deze optionele eigenschap `AssertionConsumerServiceUrl` `SingleSignOnService` vertegenwoordigt de ( URL in `BindingType` de metagegevens van de serviceprovider) en de wordt verondersteld te zijn `HTTP POST`.

Als u ervoor kiest om de URL van het antwoord en de uitlog-URL in het toepassingsmanifest te configureren zonder de metagegevens van de serviceprovider te gebruiken, valideert Azure AD B2C de SAML-aanvraaghandtekening niet en versleutelt het SAML-antwoord niet.

Stel voor deze zelfstudie, waarin u de `url` SAML-testtoepassing gebruikt, de eigenschap in op `replyUrlsWithType` de waarde die wordt weergegeven in het volgende JSON-fragment.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>uitloggenURL (optioneel)

Deze optionele eigenschap `Logout` vertegenwoordigt`SingleLogoutService` de URL (URL in `BindingType` de metagegevens `Http-Redirect`van de relying party) en de optie wordt verondersteld te zijn .

Voor deze zelfstudie, die de SAML-testtoepassing gebruikt, laat u `logoutUrl` instellen op: `https://samltestapp2.azurewebsites.net/logout`

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Uw toepassingscode bijwerken

De laatste stap is het inschakelen van Azure AD B2C als SAML IdP in uw SAML relying party-toepassing. Elke toepassing is anders en de stappen om dit te doen variëren. Raadpleeg de documentatie van uw app voor meer informatie.

Sommige of alle volgende zijn meestal vereist:

* **Metagegevens**:`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Uitgever**: De entityID gebruiken in het metagegevensbestand
* **Inlogurl/SAML-eindpunt/SAML-url:** controleer de waarde in het metagegevensbestand
* **Certificaat**: Dit is *B2C_1A_SamlIdpCert,* maar zonder de privésleutel. Ga als het gaat om de openbare sleutel van het certificaat:

    1. Ga naar de hierboven opgegeven URL met ametjes.
    1. Kopieer de waarde `<X509Certificate>` in het element.
    1. Plak het in een tekstbestand.
    1. Sla het tekstbestand op als een *.cer-bestand.*

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Test met de SAML Test App (optioneel)

Om deze tutorial te voltooien met behulp van onze [SAML Test Application:][samltest]

* De tenantnaam bijwerken
* Beleidsnaam bijwerken, bijvoorbeeld *B2C_1A_signup_signin_saml*
* Geef deze uitgever URI op:`https://contoso.onmicrosoft.com/app-name`

Selecteer **Aanmelden** en u moet worden weergegeven met een aanmeldingsscherm van de gebruiker. Bij het aanmelden wordt een SAML-bewering afgegeven aan de voorbeeldaanvraag.

## <a name="sample-policy"></a>Voorbeeldbeleid

We bieden een compleet voorbeeldbeleid dat u gebruiken voor het testen met de SAML-testapp.

1. Het [met SAML-SP geïnitieerde inlogvoorbeeldbeleid downloaden](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Update `TenantId` om uw tenantnaam te matchen, bijvoorbeeld *contoso.b2clogin.com*
1. De beleidsnaam van *B2C_1A_SAML2_signup_signin* behouden

## <a name="supported-and-unsupported-saml-modalities"></a>Ondersteunde en niet-ondersteunde SAML-modaliteiten

De volgende SAML relying party (RP) scenario's worden ondersteund via uw eigen metadata eindpunt:

* Meerdere aanmeldings-URL's of POST-binding voor de URL van de aanmelding in het hoofdobject toepassing/service.
* Geef de ondertekeningssleutel op om RP-aanvragen te verifiëren in het hoofdobject toepassing/service.
* Tokencoderingssleutel opgeven in het hoofdobject toepassing/service.
* Aanmeldingen die door de identiteitsprovider zijn gestart, worden momenteel niet ondersteund in de preview-versie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [SAML-protocol](https://www.oasis-open.org/)vindt u op de OASIS website.
- Download de SAML-testweb-app van [Azure AD B2C GitHub-communityrepo.](https://github.com/azure-ad-b2c/saml-sp-tester)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
