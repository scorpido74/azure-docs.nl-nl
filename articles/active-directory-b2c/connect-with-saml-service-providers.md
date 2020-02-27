---
title: Azure AD B2C als een SAML-IdP configureren voor uw toepassingen
title-suffix: Azure AD B2C
description: Azure AD B2C configureren om te voorzien in uw toepassingen (service providers) voor het SAML-protocol. Azure AD B2C fungeert als één ID-provider (IdP) voor uw SAML-toepassing.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5ec83857ebabc92bf86f9f84a43746a0e561218a
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77647588"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Een SAML-toepassing registreren in Azure AD B2C

In dit artikel leert u hoe u Azure Active Directory B2C (Azure AD B2C) kunt configureren om te fungeren als een Security Assertion Markup Language (SAML)-ID-provider (IdP) voor uw toepassingen.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Overzicht van scenario's

Organisaties die Azure AD B2C als klant identiteits-en toegangs beheer oplossing gebruiken, kunnen interactie vereisen met id-providers of toepassingen die zijn geconfigureerd voor verificatie met behulp van het SAML-protocol.

Azure AD B2C kan de SAML-interoperabiliteit op een van de volgende twee manieren worden gerealiseerd:

* Door te fungeren als een *ID-provider* (IDP) en te profiteren van eenmalige aanmelding (SSO) met op SAML gebaseerde service providers (uw toepassingen)
* Door als *service provider* (SP) te fungeren en te communiceren met id-providers op basis van SAML zoals Sales Force en ADFS

![Diagram met B2C als id-provider links en B2C als service provider aan de rechter kant](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Samen vatting van de twee niet-exclusieve kern scenario's met SAML:

| Scenario | Azure AD B2C rol | Uitleg |
| -------- | ----------------- | ------- |
| Mijn toepassing verwacht een SAML-bevestiging om een verificatie te volt ooien. | **Azure AD B2C fungeert als de ID-provider (IdP)**<br />Azure AD B2C fungeert als een SAML-IdP voor de toepassingen. | Dit artikel. |
| Mijn gebruikers hebben eenmalige aanmelding nodig met een id-provider die SAML-compatibel is, zoals ADFS, Sales Force of shibboleth.  | **Azure AD B2C fungeert als service provider (SP)**<br />Azure AD B2C fungeert als een service provider bij het maken van verbinding met de SAML-ID-provider. Het is een Federatie proxy tussen uw toepassing en de SAML-ID-provider.  | <ul><li>[Aanmelden met behulp van aangepast beleid instellen met ADFS als een SAML-IdP](identity-provider-adfs2016-custom.md)</li><li>[Aanmelden met een Sales Force SAML-provider instellen met behulp van aangepast beleid](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Vereisten

* Voer de stappen in aan de [slag met aangepast beleid in azure AD B2C](custom-policy-get-started.md). U hebt het aangepaste beleid voor *SocialAndLocalAccounts* nodig van het aangepaste beleid dat wordt beschreven in het artikel.
* Basis informatie over het Security Assertion Markup Language (SAML)-protocol.
* Een webtoepassing die is geconfigureerd als een SAML-service provider (SP). Voor deze zelf studie kunt u een door ons verstrekte [SAML-test toepassing][samltest] gebruiken.

## <a name="components-of-the-solution"></a>Onderdelen van de oplossing

Er zijn drie belang rijke onderdelen vereist voor dit scenario:

* SAML- **service provider** met de mogelijkheid om SAML-aanvragen te verzenden en te ontvangen, te decoderen en te reageren op SAML-bevestigingen van Azure AD B2C. Dit wordt ook wel de Relying Party genoemd.
* Openbaar beschikbaar SAML- **eind punt voor meta gegevens** voor uw service provider.
* [Azure AD B2C Tenant](tutorial-create-tenant.md)

Als u nog geen SAML-service provider en een bijbehorend meta gegevens eindpunt hebt, kunt u deze voor beeld-SAML-toepassing gebruiken die we voor het testen beschikbaar hebben gesteld:

[SAML-test toepassing][samltest]

## <a name="1-set-up-certificates"></a>1. certificaten instellen

Als u een vertrouwens relatie tussen uw service provider en Azure AD B2C wilt maken, moet u x509-certificaten en de bijbehorende persoonlijke sleutels opgeven.

* **Certificaten van service provider**
  * Certificaat met een persoonlijke sleutel die is opgeslagen in uw web-app. Dit certificaat wordt door uw service provider gebruikt voor het ondertekenen van de SAML-aanvraag die naar Azure AD B2C is verzonden. Azure AD B2C leest de open bare sleutel van de meta gegevens van de service provider om de hand tekening te valideren.
  * Beschrijving Certificaat met een persoonlijke sleutel die is opgeslagen in uw web-app. Azure AD B2C leest de open bare sleutel van de meta gegevens van de service provider om de SAML-bewering te versleutelen. De service provider gebruikt vervolgens de persoonlijke sleutel om de bewering te ontsleutelen.
* **Azure AD B2C certificaten**
  * Certificaat met een persoonlijke sleutel in Azure AD B2C. Dit certificaat wordt door Azure AD B2C gebruikt voor het ondertekenen van het SAML-antwoord dat is verzonden naar uw service provider. Uw service provider leest de open bare sleutel van de Azure AD B2C meta gegevens om de hand tekening van het SAML-antwoord te valideren.

U kunt een certificaat gebruiken dat is uitgegeven door een open bare certificerings instantie of, voor deze zelf studie, een zelfondertekend certificaat.

### <a name="11-prepare-a-self-signed-certificate"></a>1,1 een zelfondertekend certificaat voorbereiden

Als u nog geen certificaat hebt, kunt u een zelfondertekend certificaat gebruiken voor deze zelf studie. In Windows kunt u de cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) van Power shell gebruiken voor het genereren van een certificaat.

1. Voer deze Power shell-opdracht uit om een zelfondertekend certificaat te genereren. Wijzig het argument `-Subject` naar wens voor uw toepassing en Azure AD B2C Tenant naam. U kunt ook de `-NotAfter` datum aanpassen om een andere verloop tijd voor het certificaat op te geven.

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

1. Open **gebruikers certificaten beheren** > **huidige gebruiker** > **persoonlijke** > **certificaten** > *yourappname.yourtenant.onmicrosoft.com*
1. Selecteer de **actie** certificaat > > **alle taken** > **exporteren**
1. Selecteer **ja** > **volgende** > **Ja, de persoonlijke sleutel exporteren** > **volgende**
1. De standaard instellingen voor de **export bestands indeling** accepteren
1. Geef een wacht woord op voor het certificaat

### <a name="12-upload-the-certificate"></a>1,2 het certificaat uploaden

Upload vervolgens de SAML-verklaring en het handtekening certificaat voor antwoorden naar Azure AD B2C.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en blader naar uw Azure AD B2C-Tenant.
1. Onder **beleids regels**selecteert u **identiteits ervaring-Framework** en vervolgens **beleids sleutels**.
1. Selecteer **toevoegen**en selecteer vervolgens **Opties** > **uploaden**.
1. Voer een **naam**in, bijvoorbeeld *SamlIdpCert*. De prefix *B2C_1A_* wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Upload uw certificaat met het besturings element voor het uploaden van bestanden.
1. Voer het wacht woord voor het certificaat in.
1. Selecteer **Maken**.
1. Controleer of de sleutel wordt weer gegeven zoals verwacht. Bijvoorbeeld *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. het beleid voorbereiden

### <a name="21-create-the-saml-token-issuer"></a>2,1 de uitgever van het SAML-token maken

Voeg nu de mogelijkheid voor uw Tenant toe om SAML-tokens te verlenen.

Open `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** in het aangepaste beleids Starter Pack.

Ga naar de sectie `<ClaimsProviders>` en voeg het volgende XML-fragment toe.

U kunt de waarde van de `IssuerUri` meta gegevens wijzigen. Dit is de URI van de verlener die wordt geretourneerd in het SAML-antwoord van Azure AD B2C. Uw Relying Party toepassing moet worden geconfigureerd om een uitgever-URI te accepteren tijdens de validatie van de SAML-bevestiging.

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
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. het SAML-Relying Party beleid toevoegen

Nu uw Tenant SAML-bevestigingen kan uitgeven, moet u het SAML-Relying Party beleid maken en de reis van de gebruiker wijzigen zodat deze een SAML-bevestiging uitgeeft in plaats van een JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3,1 aanmelden of aanmeldings beleid maken

1. Maak een kopie van het *SignUpOrSignin. XML-* bestand in de werkmap van uw starter pakket en sla het op met een nieuwe naam. Bijvoorbeeld *SignUpOrSigninSAML. XML*. Dit is uw Relying Party-beleids bestand.

1. Open het bestand *SignUpOrSigninSAML. XML* in de editor van uw voor keur.

1. Wijzig de `PolicyId` en `PublicPolicyUri` van het beleid in _B2C_1A_signup_signin_saml_ en `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` zoals hieronder wordt weer gegeven.

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

1. Voeg het volgende XML-fragment net vóór het `<RelyingParty>`-element toe. Met deze XML wordt de indelings stap nummer 7 van de _SignUpOrSignIn_ -gebruikers traject overschreven. Als u bent begonnen vanuit een andere map in het eerste pakket of als u uw gebruikers traject hebt aangepast door het toevoegen of verwijderen van Orchestration-stappen, moet u ervoor zorgen dat het aantal (in het element `order`) is afgestemd op het nummer dat is opgegeven in de gebruikers traject voor de `SocialAndLocalAccountsWithMfa``SocialAccounts` `LocalAccounts`stap van de token Uitgever

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Vervang het volledige `<TechnicalProfile>`-element in het `<RelyingParty>`-element door de volgende technische profiel-XML.

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

1. Werk `tenant-name` met de naam van uw Azure AD B2C Tenant bij.

Het uiteindelijke Relying Party-beleids bestand moet er als volgt uitzien:

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

### <a name="32-upload-and-test-your-policy-metadata"></a>3,2 de meta gegevens van uw beleid uploaden en testen

Sla de wijzigingen op en upload het nieuwe beleids bestand. Nadat u beide beleids regels (de extensie en de Relying Party bestanden) hebt geüpload, opent u een webbrowser en navigeert u naar de meta gegevens van het beleid.

Azure AD B2C IDP-beleid bevat informatie die wordt gebruikt in het SAML-protocol om de configuratie van een SAML-ID-provider beschikbaar te maken. Meta gegevens definiëren de locatie van de services, zoals aanmelden en afmelden, certificaten, aanmeldings methode en meer. De meta gegevens van het Azure AD B2C beleid zijn beschikbaar op de volgende URL. Vervang `tenant-name` door de naam van uw Azure AD B2C Tenant en `policy-name` met de naam (ID) van het beleid:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Uw aangepaste beleid en Azure AD B2C Tenant zijn nu gereed. Maak vervolgens een toepassings registratie in Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. toepassing instellen in de Azure AD B2C Directory

### <a name="41-register-your-application-in-azure-active-directory"></a>4,1 uw toepassing registreren in Azure Active Directory

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **app-registraties (preview)** en selecteer vervolgens **nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *SAMLApp1*.
1. Onder **ondersteunde account typen**selecteert u **alleen accounts in deze organisatie Directory**
1. Onder **omleidings-URI**selecteert u **Web**en voert u vervolgens `https://localhost`in. U wijzigt deze waarde later in het manifest van de toepassings registratie.
1. Selecteer **Registreren**.

### <a name="42-update-the-app-manifest"></a>4,2 het app-manifest bijwerken

Voor SAML-apps zijn er verschillende eigenschappen die u moet configureren in het manifest van de toepassings registratie.

1. Ga in het [Azure Portal](https://portal.azure.com)naar de registratie van de toepassing die u in de vorige sectie hebt gemaakt.
1. Onder **beheren**selecteert u **manifest** om de manifest editor te openen. U kunt verschillende eigenschappen in de volgende secties wijzigen.

#### <a name="identifieruris"></a>identifierUris

De `identifierUris` is een teken reeks verzameling met een door de gebruiker gedefinieerde URI (s) die een web-app binnen de Azure AD B2C Tenant uniek aanduidt. Uw service provider moet deze waarde instellen in het `Issuer`-element van een SAML-aanvraag.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Deze eigenschap vertegenwoordigt de openbaar beschik bare meta gegevens-URL van de service provider. De meta gegevens-URL kan verwijzen naar een meta gegevensbestand dat is geüpload naar elk anoniem toegankelijk eind punt, bijvoorbeeld Blob Storage.

De meta gegevens worden in het SAML-protocol gebruikt om de configuratie van een SAML-partij, zoals een service provider, beschikbaar te stellen. Meta gegevens definiëren de locatie van de services, zoals aanmelden en afmelden, certificaten, aanmeldings methode en meer. Azure AD B2C leest de meta gegevens van de service provider en reageert dienovereenkomstig. De meta gegevens zijn niet vereist. U kunt ook een aantal kenmerken opgeven, zoals de antwoord-URI en de afmeldings-URI rechtstreeks in het app-manifest.

Als er eigenschappen zijn opgegeven in *zowel* de URL voor SAML-meta gegevens en in het manifest van de toepassings registratie, worden deze **samengevoegd**. De eigenschappen die zijn opgegeven in de meta gegevens-URL worden eerst verwerkt en hebben voor rang.

Voor deze zelf studie waarbij de SAML-test toepassing wordt gebruikt, gebruikt u de volgende waarde voor `samlMetadataUrl`:

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (optioneel)

Als u geen URI voor meta gegevens opgeeft, kunt u de antwoord-URL expliciet opgeven. Deze optionele eigenschap vertegenwoordigt de `AssertionConsumerServiceUrl` (`SingleSignOnService` URL in de meta gegevens van de service provider) en er wordt van uitgegaan dat `BindingType` wordt `HTTP POST`.

Als u ervoor kiest om de antwoord-URL en afmeldings-URL in het manifest van de toepassing te configureren zonder de meta gegevens van de service provider te gebruiken, wordt de SAML-aanvraag hand tekening niet door Azure AD B2C gevalideerd en versleutelt het SAML-antwoord niet.

Voor deze zelf studie, waarin u de SAML-test toepassing gebruikt, stelt u de eigenschap `url` van `replyUrlsWithType` in op de waarde die wordt weer gegeven in het volgende JSON-code fragment.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (optioneel)

Deze optionele eigenschap vertegenwoordigt de `Logout` URL (`SingleLogoutService` URL in de Relying Party meta gegevens) en er wordt van uitgegaan dat de `BindingType` wordt `Http-Redirect`.

Voor deze zelf studie waarbij de SAML-test toepassing wordt gebruikt, houdt u `logoutUrl` ingesteld op `https://samltestapp2.azurewebsites.net/logout`:

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. de toepassings code bijwerken

De laatste stap is het inschakelen van Azure AD B2C als een SAML-IdP in uw SAML-Relying Party toepassing. Elke toepassing wijkt af en de stappen die u kunt uitvoeren, zijn afhankelijk van elkaar. Raadpleeg de documentatie van uw app voor meer informatie.

Enkele of alle volgende zijn doorgaans vereist:

* **Meta gegevens**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Verlener**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name`
* **Aanmeldings-URL/SAML-eind punt/SAML-URL**: Controleer de waarde in het bestand met meta gegevens
* **Certificaat**: dit is *B2C_1A_SamlIdpCert*, maar zonder de persoonlijke sleutel. De open bare sleutel van het certificaat ophalen:

    1. Ga naar de meta gegevens-URL die hierboven is opgegeven.
    1. Kopieer de waarde in het element `<X509Certificate>`.
    1. Plak deze in een tekst bestand.
    1. Sla het tekst bestand op als een *CER* -bestand.

### <a name="51-test-with-the-saml-test-app-optional"></a>5,1 test met de SAML-test-app (optioneel)

Als u deze zelf studie wilt volt ooien, gebruikt u de [SAML-test toepassing][samltest]:

* De Tenant naam bijwerken
* Update de naam van het beleid, bijvoorbeeld *B2C_1A_signup_signin_saml*
* Geef de URI voor de verlener op: `https://contoso.onmicrosoft.com/app-name`

Selecteer **Aanmelden** en u moet een aanmeldings scherm voor eind gebruikers weer gegeven. Bij het aanmelden wordt een SAML-bevestiging weer gegeven aan de voorbeeld toepassing.

## <a name="sample-policy"></a>Voorbeeldbeleid

We bieden een volledig voor beeld van een beleid dat u kunt gebruiken voor het testen met de app voor SAML-tests.

1. Het [voorbeeld beleid voor door SAML-SP geïnitieerde aanmelding](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated) downloaden
1. Werk `TenantId` bij zodat deze overeenkomt met de naam van uw Tenant, bijvoorbeeld *contoso.b2clogin.com*
1. Behoud de beleids naam van *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Ondersteunde en niet-ondersteunde SAML-modaliteiten

De volgende opties voor SAML-Relying Party (RP) worden ondersteund via uw eigen meta gegevens eindpunt:

* Meerdere afmeldings-Url's of POST-binding voor afmeldings-URL in het object Application/Service Principal.
* Geef de handtekening sleutel op voor het verifiëren van RP-aanvragen in het object Application/Service Principal.
* Geef een token versleutelings sleutel op in het object Application/Service Principal.
* Aanmeldingen die door een id-provider worden geïnitieerd, worden momenteel niet ondersteund in de preview-versie.

## <a name="next-steps"></a>Volgende stappen

[Op de Oasis-website](https://www.oasis-open.org/)vindt u meer informatie over het SAML-protocol.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
