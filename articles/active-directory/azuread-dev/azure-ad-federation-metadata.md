---
title: Azure AD Federation Metadata | Microsoft Documenten
description: In dit artikel wordt het federatiemetagegevensdocument beschreven dat Azure Active Directory publiceert voor services die Azure Active Directory-tokens accepteren.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: bcc44f61ccb7b4a19e7df39ab979669c5aa37da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154896"
---
# <a name="federation-metadata"></a>Federatieve metagegevens

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) publiceert een federatiemetagegevensdocument voor services die is geconfigureerd om de beveiligingstokens te accepteren die Azure AD uitgeeft. De documentindeling van de federatiemetagegevens wordt beschreven in versie [1.2 (Web Services Federation Language) versie 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), die [metagegevens uitbreidt voor de OASIS Security Assertion Markup Language (SAML) v2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Tenantspecifieke en Tenant-onafhankelijke metagegevenseindpunten
Azure AD publiceert tenantspecifieke en tenantonafhankelijke eindpunten.

Tenant-specifieke eindpunten zijn ontworpen voor een bepaalde tenant. De tenantspecifieke federatiemetagegevens bevatten informatie over de tenant, inclusief tenantspecifieke uitgever en eindpuntinformatie. Toepassingen die de toegang tot één tenant beperken, gebruiken tenantspecifieke eindpunten.

Tenantonafhankelijke eindpunten bieden informatie die algemeen is voor alle Azure AD-tenants. Deze informatie is van toepassing op huurders die op *login.microsoftonline.com* worden gehost en wordt gedeeld tussen huurders. Tenantonafhankelijke eindpunten worden aanbevolen voor toepassingen met meerdere tenants, omdat ze niet zijn gekoppeld aan een bepaalde tenant.

## <a name="federation-metadata-endpoints"></a>Eindpunten met metagegevens van Federatie
Azure AD publiceert `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`federatiemetagegevens op .

Voor **tenant-specifieke eindpunten**kan het `TenantDomainName` een van de volgende typen zijn:

* Een geregistreerde domeinnaam van een Azure AD-tenant, zoals: `contoso.onmicrosoft.com`.
* De onveranderlijke tenant-id van `72f988bf-86f1-41af-91ab-2d7cd011db45`het domein, zoals .

Voor **tenant-onafhankelijke eindpunten**, de `TenantDomainName` is `common`. In dit document worden alleen de elementen metagegevens van federatie weergegeven die gemeenschappelijk zijn voor alle Azure AD-tenants die op login.microsoftonline.com worden gehost.

Een tenantspecifiek eindpunt kan bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`zijn . Het tenant-onafhankelijke eindpunt [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml)is . U het metagegevensdocument van de federatie bekijken door deze URL in een browser te typen.

## <a name="contents-of-federation-metadata"></a>Inhoud van federatiemetagegevens
In de volgende sectie vindt u informatie die nodig is voor services die de tokens gebruiken die zijn uitgegeven door Azure AD.

### <a name="entity-id"></a>Entiteits-id
Het `EntityDescriptor` element `EntityID` bevat een attribuut. De waarde `EntityID` van het kenmerk vertegenwoordigt de uitgever, dat wil zeggen de beveiligingstokenservice (STS) die het token heeft uitgegeven. Het is belangrijk om de uitgever te valideren wanneer u een token ontvangt.

Met de volgende metagegevens `EntityDescriptor` wordt `EntityID` een voorbeeldtenantspecifiek element met een element weergegeven.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
U de tenant-id in het tenant-onafhankelijke eindpunt vervangen door `EntityID` uw tenant-id om een tenantspecifieke waarde te maken. De resulterende waarde is gelijk aan die van de tokenuitgever. De strategie maakt het mogelijk een multi-tenant applicatie om de emittent te valideren voor een bepaalde tenant.

Met de volgende metagegevens `EntityID` wordt een voorbeeldtenantonafhankelijk element weergegeven. Houd er rekening `{tenant}` mee dat het een letterlijke, geen tijdelijke aanduiding is.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Tokenondertekeningscertificaten
Wanneer een service een token ontvangt dat wordt uitgegeven door een Azure AD-tenant, moet de handtekening van het token worden gevalideerd met een ondertekeningssleutel die wordt gepubliceerd in het metagegevensdocument van de federatie. De federatiemetagegevens bevatten het openbare gedeelte van de certificaten die de tenants gebruiken voor tokenondertekening. De onbewerkte bytes `KeyDescriptor` van het certificaat worden in het element weergegeven. Het tokenondertekeningscertificaat is alleen geldig voor `use` ondertekening `signing`wanneer de waarde van het kenmerk .

Een federatiemetagegevensdocument dat door Azure AD is gepubliceerd, kan meerdere ondertekeningssleutels hebben, zoals wanneer Azure AD zich voorbereidt op het bijwerken van het ondertekeningscertificaat. Wanneer een federatiemetagegevensdocument meer dan één certificaat bevat, moet een service die de tokens valideert, alle certificaten in het document ondersteunen.

Met de volgende `KeyDescriptor` metagegevens wordt een voorbeeldelement met een ondertekeningssleutel weergegeven.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

Het `KeyDescriptor` element wordt op twee plaatsen in het metagegevensdocument van de federatie weergegeven. in de ws-federatie-specifieke sectie en de SAML-specifieke sectie. De certificaten die in beide secties worden gepubliceerd, zijn hetzelfde.

In de ws-federatie-specifieke sectie, een WS-Federation metadata `RoleDescriptor` reader zou `SecurityTokenServiceType` lezen van de certificaten van een element met het type.

Met de volgende `RoleDescriptor` metagegevens wordt een voorbeeldelement weergegeven.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

In de saml-specifieke sectie zou een WS-Federation metagegevenslezer de certificaten uit een `IDPSSODescriptor` element lezen.

Met de volgende `IDPSSODescriptor` metagegevens wordt een voorbeeldelement weergegeven.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Er zijn geen verschillen in de indeling van tenant-specifieke en tenant-onafhankelijke certificaten.

### <a name="ws-federation-endpoint-url"></a>URL van WS-Federation-eindpunt
De federatiemetagegevens bevatten de URL die Azure AD gebruikt voor één aanmelding en één aanmelding in het WS-Federation-protocol. Dit eindpunt wordt `PassiveRequestorEndpoint` in het element weergegeven.

Met de volgende `PassiveRequestorEndpoint` metagegevens wordt een voorbeeldelement voor een tenantspecifiek eindpunt weergegeven.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Voor het tenantonafhankelijke eindpunt wordt de URL van de WS-Federatie weergegeven in het eindpunt WS-Federation, zoals weergegeven in het volgende voorbeeld.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL van het EINDPUNT VAN SAML-protocol
De federatiemetagegevens bevatten de URL die Azure AD gebruikt voor één aanmelding en één aanmelding in het SAML 2.0-protocol. Deze eindpunten worden `IDPSSODescriptor` weergegeven in het element.

De in- en aanmeldings-URL's worden weergegeven in de `SingleSignOnService` elementen en `SingleLogoutService` elementen.

Met de volgende `PassiveResistorEndpoint` metagegevens wordt een voorbeeld weergegeven voor een tenantspecifiek eindpunt.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Ook worden de eindpunten voor de algemene SAML 2.0-protocoleindpunten gepubliceerd in de metagegevens van de tenantonafhankelijke federatie, zoals weergegeven in de volgende steekproef.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
