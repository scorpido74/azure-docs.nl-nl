---
title: Een SAML-technisch profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een SAML-technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 83a13e0b1bb4d55b889d96e42c8f3f18ce0f2b73
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408928"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een SAML-technisch profiel definiëren in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor de SAML 2.0-identiteitsprovider. In dit artikel worden de specifieke kenmerken beschreven van een technisch profiel voor interactie met een claimprovider die dit gestandaardiseerde protocol ondersteunt. Met een SAML-technisch profiel u zich gedragen bij een saml-gebaseerde identiteitsprovider, zoals [ADFS](identity-provider-adfs2016-custom.md) en [Salesforce.](identity-provider-salesforce-custom.md) Met deze federatie kunnen uw gebruikers zich aanmelden met hun bestaande sociale of bedrijfsidentiteiten.

## <a name="metadata-exchange"></a>Uitwisseling van metagegevens

Metagegevens zijn informatie die wordt gebruikt in het SAML-protocol om de configuratie van een SAML-partij bloot te leggen, zoals een serviceprovider of identiteitsprovider. Metagegevens definieert de locatie van de services, zoals aanmelding en afmelding, certificaten, aanmeldingsmethode en meer. De identiteitsprovider gebruikt de metagegevens om te weten hoe te communiceren met Azure AD B2C. De metagegevens zijn geconfigureerd in XML-indeling en kunnen worden ondertekend met een digitale handtekening, zodat de andere partij de integriteit van de metagegevens kan valideren. Wanneer Azure AD B2C wordt gefederd met een SAML-identiteitsprovider, fungeert het als een serviceprovider die een SAML-aanvraag initieert en wacht op een SAML-antwoord. En, in sommige gevallen, accepteert ongevraagde SAML authenticatie, die ook bekend staat als identity provider geïnitieerd.

De metadata kan in beide partijen worden geconfigureerd als "Statische metadata" of "Dynamische metadata". In de statische modus kopieert u de volledige metagegevens van de ene partij en stelt u deze in de andere partij in. In de dynamische modus stelt u de URL in op de metagegevens, terwijl de andere partij de configuratie dynamisch leest. De principes zijn hetzelfde, u stelt de metagegevens van het technische azure AD B2C-profiel in uw identiteitsprovider in en stelt de metagegevens van de identiteitsprovider in Azure AD B2C in.

Elke SAML-identiteitsprovider heeft verschillende stappen om de serviceprovider bloot te leggen en in te stellen, in dit geval Azure AD B2C, en stelt de Azure AD B2C-metagegevens in de identiteitsprovider in. Kijk in de documentatie van uw identiteitsprovider voor advies over hoe u dit doen.

In het volgende voorbeeld wordt een URL-adres weergegeven voor de SAML-metagegevens van een technisch azure AD B2C-profiel:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Vervang de volgende waarden:

- **uw tenant-naam** met uw tenantnaam, zoals fabrikam.b2clogin.com.
- **uw polis** met uw polisnaam. Gebruik het beleid waarbij u het technische profiel van de SAML-provider configureert of een beleid dat overneemt van dat beleid.
- **uw-technisch-profiel** met uw SAML identity provider technische profielnaam.

## <a name="digital-signing-certificates-exchange"></a>Uitwisseling van digitale ondertekeningscertificaten

Als u een vertrouwensrelatie wilt opbouwen tussen Azure AD B2C en uw SAML-identiteitsprovider, moet u een geldig X509-certificaat met de privésleutel opgeven. U uploadt het certificaat met de privésleutel (.pfx-bestand) naar het Azure AD B2C-beleidssleutelarchief. Azure AD B2C ondertekent de SAML-aanmeldingsaanvraag digitaal met behulp van het certificaat dat u verstrekt.

Het certificaat wordt op de volgende manieren gebruikt:

- Azure AD B2C genereert en ondertekent een SAML-aanvraag met behulp van de azure AD B2C-privésleutel van het certificaat. De SAML-aanvraag wordt verzonden naar de identiteitsprovider, die de aanvraag valideert met de openbare atoomsleutel van het certificaat met Azure AD B2C. Het azure AD B2C-openbare certificaat is toegankelijk via metagegevens van technische profielen. U het .cer-bestand ook handmatig uploaden naar uw SAML-identiteitsprovider.
- De identiteitsprovider ondertekent de gegevens die naar Azure AD B2C worden verzonden met behulp van de privésleutel van het certificaat van de identiteitsprovider. Azure AD B2C valideert de gegevens met behulp van het openbare certificaat van de identiteitsprovider. Elke identiteitsprovider heeft verschillende stappen voor het instellen, kijk op de documentatie van uw identiteitsprovider voor richtlijnen over hoe dit te doen. In Azure AD B2C heeft uw beleid toegang nodig tot de openbare certificaatsleutel met behulp van de metagegevens van de identiteitsprovider.

Een zelfondertekend certificaat is acceptabel voor de meeste scenario's. Voor productieomgevingen wordt aanbevolen om een X509-certificaat te gebruiken dat is uitgegeven door een certificaatautoriteit. Zoals later in dit document wordt beschreven, u voor een niet-productieomgeving ook de SAML-ondertekening aan beide zijden uitschakelen.

In het volgende diagram worden de metagegevens en certificaatuitwisseling weergegeven:

![metagegevens en certificaatuitwisseling](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Digitale versleuteling

Om de saml-reactiebewering te versleutelen, gebruikt de identiteitsprovider altijd een openbare sleutel van een versleutelingscertificaat in een technisch azure AD B2C-profiel. Wanneer Azure AD B2C de gegevens moet decoderen, wordt het privégedeelte van het versleutelingscertificaat gebruikt.

Ga als gevolg van het versleutelen van de bewering van de SAML-reactie:

1. Upload een geldig X509-certificaat met de privésleutel (.pfx-bestand) naar het Azure AD B2C-beleidssleutelarchief.
2. Voeg een **CryptographicKey-element** `SamlAssertionDecryption` toe met een id van aan de verzameling **CryptographicKeys** in het technische profiel. Stel de **StorageReferenceId** in op de naam van de beleidssleutel die u in stap 1 hebt gemaakt.
3. Stel de technische profielmetadata **WilEncryptedAssertions** in op `true`.
4. Werk de identiteitsprovider bij met de nieuwe azure AD B2C-metagegevens voor technische profielen. U ziet de **KeyDescriptor** met de `encryption` eigenschap **gebruik** ingesteld op het bevatten van de openbare sleutel van uw certificaat.

In het volgende voorbeeld wordt het gedeelte azure AD B2C-versleuteling voor technische profielen van de metagegevens weergegeven:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `SAML2`Protocol moet worden ingesteld op .

## <a name="output-claims"></a>Outputclaims

Het element **OutputClaims** bevat een lijst met claims die `AttributeStatement` zijn geretourneerd door de SAML-identiteitsprovider onder de sectie. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de identiteitsprovider. U ook claims opnemen die niet worden geretourneerd door `DefaultValue` de identiteitsprovider, zolang u het kenmerk instelt.

### <a name="subject-name-output-claim"></a>Claim voor uitvoer van onderwerpnaam

Als u de SAML-bewering **NameId** in het **onderwerp** wilt lezen als een `SPNameQualifier` genormaliseerde claim, stelt u de claim **PartnerClaimType** in op de waarde van het kenmerk. Als `SPNameQualifier`het kenmerk niet wordt weergegeven, stelt u `NameQualifier` de claim **PartnerClaimType** in op waarde van het kenmerk. 


SAML bewering: 

```XML
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
      <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Output claim:

```XML
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Als `SPNameQualifier` beide `NameQualifier` of kenmerken niet worden weergegeven in de SAML-bewering, stelt u de **claimPartnerClaimType** in op `assertionSubjectName`. Controleer of **de NameId** de eerste waarde is in beweringXML. Wanneer u meer dan één bewering definieert, kiest Azure AD B2C de onderwerpwaarde uit de laatste bewering.

In het volgende voorbeeld worden de claims weergegeven die zijn geretourneerd door een SAML-identiteitsprovider:

- De **claim van de issuerUserId** is toegewezen aan de claim **SubjectName.**
- De **first_name** claim is toegewezen aan de **givenName claim.**
- De **last_name** claim is toegewezen aan de **achternaam** claim.
- De **claim displayName** zonder naamtoewijzing.
- De **e-mailclaim** zonder naamtoewijzing.

Het technische profiel retourneert ook claims die niet worden geretourneerd door de identiteitsprovider:

- De **identityProvider** claim die de naam van de identiteitsprovider bevat.
- De **claim authenticationSource** met een standaardwaarde van **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

Het element **OutputClaimsTransformations** kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| PartnerEntiteit | Ja | URL van de metagegevens van de SAML-identiteitsprovider. De metagegevens van de identiteitsprovider kopiëren en toevoegen in het CDATA-element`<![CDATA[Your IDP metadata]]>` |
| WilSignedRequests | Nee | Geeft aan of voor het technische profiel alle uitgaande verificatieaanvragen moeten worden ondertekend. Mogelijke `true` waarden: `false`of . De standaardwaarde is `true`. Wanneer de waarde `true`is ingesteld op , moet de Cryptografische sleutel **samlMessageSigning** worden opgegeven en worden alle uitgaande verificatieaanvragen ondertekend. Als de waarde `false`is ingesteld op , worden de parameters **SigAlg** en **Signature** (querytekenreeks of postparameter) weggelaten uit de aanvraag. Met deze metagegevens wordt ook het kenmerk **AuthnRequestsSigned** met gegevens gebruikt, dat wordt uitgevoerd in de metagegevens van het technische azure AD B2C-profiel dat wordt gedeeld met de identiteitsprovider. Azure AD B2C ondertekent de aanvraag niet als de waarde van **WilSignedRequests** in de metagegevens van het technische profiel is ingesteld `false` en de metagegevens van de identiteitsprovider **WantAuthnRequestsSigned** is ingesteld op `false` of niet is opgegeven. |
| XmlSignatureAlgoritme | Nee | De methode die Azure AD B2C gebruikt om de SAML-aanvraag te ondertekenen. Met deze metagegevens bepaalt de waarde van de parameter **SigAlg** (querytekenreeks of postparameter) in de SAML-aanvraag. Mogelijke `Sha256`waarden: `Sha384` `Sha512`, `Sha1`, of . Zorg ervoor dat u het handtekeningalgoritme aan beide zijden met dezelfde waarde configureert. Gebruik alleen het algoritme dat uw certificaat ondersteunt. |
| WilSignedAssertions | Nee | Geeft aan of voor het technische profiel alle binnenkomende beweringen moeten worden ondertekend. Mogelijke `true` waarden: `false`of . De standaardwaarde is `true`. Als de waarde `true`is ingesteld op `saml:Assertion` , moeten alle beweringensectie die door de identiteitsprovider naar Azure AD B2C is verzonden, worden ondertekend. Als de waarde `false`is ingesteld op , moet de identiteitsprovider de beweringen niet ondertekenen, maar zelfs als dit het wel is, valideert Azure AD B2C de handtekening niet. Met deze metagegevens wordt ook de metagegevensvlag **WilAssertionsSigned**, die wordt uitgevoerd in de metagegevens van het technische azure AD B2C-profiel dat wordt gedeeld met de identiteitsprovider. Als u de validatie van beweringen uitschakelt, u ook de validatie van de antwoordhandtekening uitschakelen (zie **Reacties ondertekend).** |
| ReactiesOndertekend | Nee | Mogelijke `true` waarden: `false`of . De standaardwaarde is `true`. Als de waarde `false`is ingesteld op , moet de identiteitsprovider het SAML-antwoord niet ondertekenen, maar zelfs als dit het wel gebeurt, valideert Azure AD B2C de handtekening niet. Als de waarde `true`is ingesteld op , wordt het SAML-antwoord dat door de identiteitsprovider naar Azure AD B2C wordt verzonden, ondertekend en moet deze worden gevalideerd. Als u de SAML-antwoordvalidatie uitschakelt, u ook de validatie van de bevestigingshandtekening uitschakelen (zie **WilSignedAssertions**voor meer informatie). |
| WilEncryptedAssertions | Nee | Geeft aan of voor het technische profiel alle binnenkomende beweringen moeten worden versleuteld. Mogelijke `true` waarden: `false`of . De standaardwaarde is `false`. Als de waarde `true`is ingesteld op , moeten beweringen die door de identiteitsprovider naar Azure AD B2C worden verzonden, worden ondertekend en moet de cryptografische sleutel **SamlAssertionDecryption** worden opgegeven. Als de waarde `true`is ingesteld op , bevatten de metagegevens van het technische azure AD B2C-profiel de **sectie versleuteling.** De identiteitsprovider leest de metagegevens en versleutelt de SAML-reactiebewering met de openbare sleutel die wordt geleverd in de metagegevens van het technische azure b2c-profiel. Als u de bevestigingsversleuteling inschakelt, moet u mogelijk ook de validatie van de antwoordhandtekening uitschakelen (zie **Reactiesondertekend**voor meer informatie). |
| IdpInitiatedProfileIngeschakeld | Nee | Geeft aan of één aanmeldingssessieprofiel is ingeschakeld dat is gestart door een SAML-identiteitsproviderprofiel. Mogelijke `true` waarden: `false`of . De standaardwaarde is `false`. In de stroom die door de identiteitsprovider wordt geïnitieerd, wordt de gebruiker extern geverifieerd en wordt een ongevraagd antwoord verzonden naar Azure AD B2C, dat vervolgens het token verbruikt, orkestratiestappen uitvoert en vervolgens een antwoord verzendt naar de toepassing van de relying party. |
| NameIdPolicyFormat | Nee | Hiermee geeft u beperkingen op voor de naam-id die moet worden gebruikt om het gevraagde onderwerp weer te geven. Indien weggelaten, kan elk type id dat door de identiteitsprovider voor het gevraagde onderwerp wordt ondersteund, worden gebruikt. Bijvoorbeeld `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** kan worden gebruikt met **NameIdPolicyAllowCreate**. Bekijk de documentatie van uw identiteitsprovider voor meer informatie over welke naam-ID-beleidsregels worden ondersteund. |
| NameIdPolicyAllowCreate | Nee | Wanneer u **NameIdPolicyFormat gebruikt,** `AllowCreate` kunt u ook de eigenschap **NameIDPolicy**opgeven. De waarde van `true` deze `false` metagegevens is of om aan te geven of de identiteitsprovider een nieuw account mag maken tijdens de aanmeldingsstroom. Kijk in de documentatie van uw identiteitsprovider voor advies over hoe u dit doen. |
| AuthenticationRequestExtensions | Nee | Optionele protocolberichtextensie-elementen die zijn overeengekomen tussen Azure AD BC en de identiteitsprovider. De extensie wordt gepresenteerd in XML-formaat. U voegt de XML-gegevens `<![CDATA[Your IDP metadata]]>`toe in het CDATA-element . Controleer de documentatie van uw identiteitsprovider om te zien of het uitbreidingselement wordt ondersteund. |
| Referentie's voor AuthnContextClass | Nee | Hiermee geeft u een of meer URI-verwijzingen op die de contextklassen van verificatie identificeren. Als u bijvoorbeeld wilt toestaan dat een gebruiker zich alleen `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`met gebruikersnaam en wachtwoord aanmeldt, stelt u de waarde in op . Als u aanmelden via gebruikersnaam en wachtwoord via een beveiligde `PasswordProtectedTransport`sessie (SSL/TLS) wilt toestaan, geeft u op . Bekijk de documentatie van uw identiteitsprovider voor meer informatie over de **AuthnContextClassRef** URI's die worden ondersteund. Geef meerdere URI's op als een lijst met komma's. |
| IncludeKeyInfo | Nee | Geeft aan of de SAML-verificatieaanvraag de openbare sleutel `HTTP-POST`van het certificaat bevat wanneer de binding is ingesteld op . Mogelijke `true` waarden: `false`of . |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer- en uitvoerclaims geeft u aan of [schadeafhandeling](claim-resolver-overview.md) is opgenomen in het technische profiel. Mogelijke waarden: `true` `false`  , of (standaard). Als u een claimresolver in het technische profiel `true`wilt gebruiken, stelt u dit in op . |

## <a name="cryptographic-keys"></a>Cryptografische toetsen

Het element **CryptographicKeys** bevat de volgende kenmerken:

| Kenmerk |Vereist | Beschrijving |
| --------- | ----------- | ----------- |
| SamlMessageSigning SamlMessageSigning |Ja | De X509-certificaat (RSA-toetsset) die moet worden gebruikt om SAML-berichten te ondertekenen. Azure AD B2C gebruikt deze sleutel om de aanvragen te ondertekenen en naar de identiteitsprovider te verzenden. |
| SamlAssertionDecryptie |Ja | De X509-certificaat (RSA-toetsset) die moet worden gebruikt om SAML-berichten te decoderen. Dit certificaat moet worden verstrekt door de identiteitsprovider. Azure AD B2C gebruikt dit certificaat om de gegevens te decoderen die door de identiteitsprovider worden verzonden. |
| Metagegevenstekenen |Nee | De X509-certificaat (RSA-toetsset) die moet worden gebruikt om SAML-metagegevens te ondertekenen. Azure AD B2C gebruikt deze sleutel om de metagegevens te ondertekenen.  |

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor voorbeelden van het werken met SAML-identiteitsproviders in Azure AD B2C:

- [ADFS toevoegen als SAML-identiteitsprovider met aangepaste beleidsregels](identity-provider-adfs2016-custom.md)
- [Meld u aan met Salesforce-accounts via SAML](identity-provider-salesforce-custom.md)
