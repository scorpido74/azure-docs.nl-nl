---
title: Een technisch profiel definiëren voor een SAML-uitgever in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een technisch profiel voor een SAML-token (Security Assertion Markup Language) in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c35f85b9ec5d86d1cd61f165b891c576c06a03db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967274"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel definiëren voor een SAML-tokenuitgever in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zendt verschillende soorten beveiligingstokens uit terwijl elke verificatiestroom wordt verwerkt. Een technisch profiel voor een SAML-tokenuitgever zendt een SAML-token uit dat wordt teruggestuurd naar de toepassing van de relying party (serviceprovider). Meestal is dit technische profiel de laatste orkestratiestap in de gebruikersreis.

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `None` **Protocol** moet worden ingesteld op . Stel het element **OutputTokenFormat** in op `SAML2`.

In het volgende voorbeeld `Saml2AssertionIssuer`ziet u een technisch profiel voor :

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Beweringen voor invoer, uitvoer en aanhouden

De elementen **InputClaims,** **OutputClaims**en **PersistClaims** zijn leeg of afwezig. De **Elementen InutputClaimsTransformations** en **OutputClaimsTransformations** ontbreken ook.

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| IssuerUri | Nee | De naam van de uitgever die wordt weergegeven in het SAML-antwoord. De waarde moet dezelfde naam hebben als geconfigureerd in de toepassing van de relying party. |

## <a name="cryptographic-keys"></a>Cryptografische toetsen

Het element CryptographicKeys bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Metagegevenstekenen | Ja | De X509-certificaat (RSA-toetsset) die moet worden gebruikt om SAML-metagegevens te ondertekenen. Azure AD B2C gebruikt deze sleutel om de metagegevens te ondertekenen. |
| SamlMessageSigning SamlMessageSigning| Ja| Geef het X509-certificaat (RSA-sleutelset) op dat moet worden gebruikt om SAML-berichten te ondertekenen. Azure AD B2C gebruikt deze `<samlp:Response>` sleutel om het antwoord te ondertekenen dat naar de relying party wordt verzonden.|

## <a name="session-management"></a>Sessiebeheer

Als u de Azure AD B2C SAML-sessies wilt configureren `UseTechnicalProfileForSessionManagement` tussen een toepassing van een relying party, het kenmerk van het element, verwijzing naar [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO-sessie.

## <a name="next-steps"></a>Volgende stappen

Zie bijvoorbeeld het volgende artikel over het gebruik van een technisch profiel van de SAML-uitgever:

- [Een SAML-toepassing registreren in Azure AD B2C](connect-with-saml-service-providers.md)












