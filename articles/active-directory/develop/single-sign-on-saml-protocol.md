---
title: Azure Single Sign On SAML-protocol
description: In dit artikel wordt het SAML-protocol voor één teken in Azure Active Directory beschreven
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: f1437ec5d9c3fd0ff69be0c884c340cb857ee181
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881279"
---
# <a name="single-sign-on-saml-protocol"></a>Enkel aanmeldingsSAML-protocol

In dit artikel worden de SAML 2.0-verificatieaanvragen en -antwoorden behandeld die Azure Active Directory (Azure AD) ondersteunt voor single sign-on.

Het protocoldiagram hieronder beschrijft de enkele aanmeldingsvolgorde. De cloudservice (de serviceprovider) gebruikt een HTTP `AuthnRequest` Redirect-binding om een element (verificatieverzoek) door te geven aan Azure AD (de identiteitsprovider). Azure AD gebruikt vervolgens een HTTP-berichtbinding om een `Response` element op de cloudservice te plaatsen.

![Eén teken op werkstroom](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest (AuthnRequest)

Als u een gebruikersverificatie wilt `AuthnRequest` aanvragen, verzenden cloudservices een element naar Azure AD. Een voorbeeld van SAML 2.0 `AuthnRequest` kan er als volgt uitzien:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parameter |  | Beschrijving |
| --- | --- | --- |
| Id | Vereist | Azure AD gebruikt dit `InResponseTo` kenmerk om het kenmerk van de geretourneerde reactie in te vullen. ID mag niet beginnen met een getal, dus een gemeenschappelijke strategie is om een tekenreeks als "id" voor te bereiden op de tekenreeksweergave van een GUID. Bijvoorbeeld, `id6c1c178c166d486687be4aaf5e482730` is een geldig identiteitsbewijs. |
| Versie | Vereist | Deze parameter moet worden ingesteld op **2.0**. |
| IssueInstant (ProbleemDirect) | Vereist | Dit is een DateTime-tekenreeks met een [UTC-waarde en een retourindeling ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD verwacht een DatumTime-waarde van dit type, maar evalueert of gebruikt de waarde niet. |
| BeweringConsumentServiceUrl | Optioneel | Indien aanwezig, moet deze `RedirectUri` parameter overeenkomen met de cloudservice in Azure AD. |
| ForceAuthn (ForceAuthn) | Optioneel | Dit is een booleaanse waarde. Als dit waar is, betekent dit dat de gebruiker gedwongen wordt zich opnieuw te verifiëren, zelfs als hij een geldige sessie heeft met Azure AD. |
| IsPassief | Optioneel | Dit is een booleaanse waarde die aangeeft of Azure AD de gebruiker in stilte moet verifiëren, zonder interactie van de gebruiker, met behulp van de sessiecookie als deze bestaat. Als dit waar is, probeert Azure AD de gebruiker te verifiëren met behulp van de sessiecookie. |

Alle `AuthnRequest` andere kenmerken, zoals Toestemming, Bestemming, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex en ProviderName worden **genegeerd.**

Azure AD negeert `Conditions` ook `AuthnRequest`het element in .

### <a name="issuer"></a>Verlener

Het `Issuer` element `AuthnRequest` in een element moet exact overeenkomen met een van de **ServicePrincipalNames** in de cloudservice in Azure AD. Dit is meestal ingesteld op de URI van de **app-id** die is opgegeven tijdens de registratie van toepassingen.

Een SAML-fragment `Issuer` met het element lijkt op het volgende voorbeeld:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameID-beleid

Met dit element wordt een bepaalde naam-id-indeling in het antwoord opgevraagd en is het optioneel in `AuthnRequest` elementen die naar Azure AD worden verzonden.

Een `NameIdPolicy` element lijkt op het volgende voorbeeld:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Als `NameIDPolicy` dit is voorzien, `Format` kunt u het optionele kenmerk opnemen. Het `Format` kenmerk kan slechts één van de volgende waarden hebben. elke andere waarde resulteert in een fout.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory geeft de NameID-claim uit als een pairwise-id.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory geeft de NameID-claim op in de e-mailadresindeling.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Met deze waarde kan Azure Active Directory de claimindeling selecteren. Azure Active Directory geeft de NameID op als een pairwise-id.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory geeft de NameID-claim af als een willekeurig gegenereerde waarde die uniek is voor de huidige SSO-bewerking. Dit betekent dat de waarde tijdelijk is en niet kan worden gebruikt om de authenticerende gebruiker te identificeren.

Azure AD negeert het `AllowCreate` kenmerk.

### <a name="requestauthncontext"></a>RequestAuthnContext
Het `RequestedAuthnContext` element geeft de gewenste verificatiemethoden aan. Het is `AuthnRequest` optioneel in elementen die naar Azure AD worden verzonden. Azure AD `AuthnContextClassRef` ondersteunt `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`waarden zoals .

### <a name="scoping"></a>Scoping
Het `Scoping` element, dat een lijst met identiteitsproviders bevat, is optioneel in `AuthnRequest` elementen die naar Azure AD zijn verzonden.

Vermeld indien aanwezig het `ProxyCount` kenmerk `IDPListOption` of `RequesterID` element niet, omdat deze niet worden ondersteund.

### <a name="signature"></a>Handtekening
Neem geen `Signature` element in `AuthnRequest` elementen op, omdat Azure AD geen ondertekende verificatieaanvragen ondersteunt.

### <a name="subject"></a>Onderwerp
Azure AD negeert `Subject` `AuthnRequest` het element van elementen.

## <a name="response"></a>Antwoord
Wanneer een aangevraagde aanmelding is voltooid, plaatst Azure AD een reactie op de cloudservice. Een reactie op een geslaagde aanmeldingspoging lijkt op het volgende voorbeeld:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Antwoord

Het `Response` element bevat het resultaat van de autorisatieaanvraag. Azure AD `ID`stelt `Version` `IssueInstant` de waarden `Response` en waarden in het element in. Er worden ook de volgende kenmerken ingesteld:

* `Destination`: Wanneer sign-on succesvol is voltooid, `RedirectUri` wordt dit ingesteld op de serviceprovider (cloudservice).
* `InResponseTo`: Dit is `ID` ingesteld op `AuthnRequest` het kenmerk van het element dat het antwoord heeft gestart.

### <a name="issuer"></a>Verlener

Azure AD `Issuer` stelt `https://login.microsoftonline.com/<TenantIDGUID>/` het \<element in op de plaats waar TenantIDGUID> de tenant-id van de Azure AD-tenant is.

Een antwoord met het element Uitgever kan er bijvoorbeeld uitzien als de volgende steekproef:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

Het `Status` element brengt het succes of falen van sign-on. Het bevat `StatusCode` het element, dat een code of een set geneste codes bevat die de status van het verzoek vertegenwoordigt. Het bevat `StatusMessage` ook het element, dat aangepaste foutberichten bevat die worden gegenereerd tijdens het aanmeldingsproces.

<!-- TODO: Add an authentication protocol error reference -->

Het volgende voorbeeld is een SAML-antwoord op een mislukte aanmeldingspoging.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Bewering

`ID`Naast het `IssueInstant` azuur- en `Version`Azure AD worden `Assertion` de volgende elementen in het element van het antwoord ingesteld.

#### <a name="issuer"></a>Verlener

Dit is `https://sts.windows.net/<TenantIDGUID>/`ingesteld \<op de plaats waar TenantIDGUID> de tenant-id van de Azure AD-tenant is.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Handtekening

Azure AD ondertekent de bewering in reactie op een succesvolle aanmelding. Het `Signature` element bevat een digitale handtekening die de cloudservice kan gebruiken om de bron te verifiëren om de integriteit van de bewering te verifiëren.

Om deze digitale handtekening te genereren, gebruikt `IDPSSODescriptor` Azure AD de ondertekeningssleutel in het element van het metagegevensdocument.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Onderwerp

Hiermee wordt het principe opgegeven dat het onderwerp is van de verklaringen in de bewering. Het bevat `NameID` een element, dat de geverifieerde gebruiker vertegenwoordigt. De `NameID` waarde is een gerichte id die alleen wordt gericht op de serviceprovider die het publiek voor het token is. Het is persistent - het kan worden ingetrokken, maar wordt nooit opnieuw toegewezen. Het is ook ondoorzichtig, in die zin dat het niets over de gebruiker onthult en niet kan worden gebruikt als een id voor attribuutquery's.

Het `Method` kenmerk `SubjectConfirmation` van het element `urn:oasis:names:tc:SAML:2.0:cm:bearer`is altijd ingesteld op .

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Voorwaarden

Dit element specificeert voorwaarden die het aanvaardbare gebruik van SAML-beweringen definiëren.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

De `NotBefore` `NotOnOrAfter` kenmerken en kenmerken geven het interval op waarin de bewering geldig is.

* De waarde `NotBefore` van het kenmerk is gelijk aan of iets (minder `IssueInstant` dan `Assertion` een seconde) later dan de waarde van het kenmerk van het element. Azure AD houdt geen rekening met enig tijdsverschil tussen zichzelf en de cloudservice (serviceprovider) en voegt geen buffer toe aan deze tijd.
* De waarde `NotOnOrAfter` van het kenmerk is 70 `NotBefore` minuten later dan de waarde van het kenmerk.

#### <a name="audience"></a>Doelgroep

Dit bevat een URI die een beoogde doelgroep identificeert. Azure AD stelt de waarde van `Issuer` dit element `AuthnRequest` in op de waarde van het element dat de aanmelding heeft geïnitieerd. Als u `Audience` de waarde wilt `App ID URI` beoordelen, gebruikt u de waarde van de waarde die is opgegeven tijdens de registratie van de toepassing.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Net `Issuer` als de `Audience` waarde moet de waarde exact overeenkomen met een van de hoofdnamen van de service die de cloudservice in Azure AD vertegenwoordigt. Als de waarde van `Issuer` het element echter geen `Audience` URI-waarde is, is de waarde in het antwoord de `Issuer` waarde die vooraf is vastgesteld met `spn:`.

#### <a name="attributestatement"></a>KenmerkVerklaring

Dit bevat claims over het onderwerp of de gebruiker. Het volgende fragment `AttributeStatement` bevat een voorbeeldelement. De ellips geeft aan dat het element meerdere kenmerken en kenmerkwaarden kan bevatten.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Naamclaim** - De `Name` waarde`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`van het kenmerk ( ) is de `testuser@managedtenant.com`hoofdnaam van de gebruiker van de geverifieerde gebruiker, zoals .
* **ObjectIdentifier Claim** - De `ObjectIdentifier` waarde`http://schemas.microsoft.com/identity/claims/objectidentifier`van `ObjectId` het kenmerk ( ) is de van het mapobject dat de geverifieerde gebruiker in Azure AD vertegenwoordigt. `ObjectId`is een onveranderlijke, wereldwijd unieke en herbruikbare veilige id van de geverifieerde gebruiker.

#### <a name="authnstatement"></a>AuthnStatement

Dit element stelt dat het beweringsonderwerp op een bepaald tijdstip op een bepaald tijdstip met een bepaald middel is geverifieerd.

* Het `AuthnInstant` kenmerk geeft het tijdstip op waarop de gebruiker is geverifieerd met Azure AD.
* Het `AuthnContext` element geeft de verificatiecontext op die wordt gebruikt om de gebruiker te verifiëren.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
