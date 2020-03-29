---
title: SSO- en tokenaanpassingen beheren met behulp van aangepast beleid
titleSuffix: Azure AD B2C
description: Meer informatie over het beheren van SSO- en tokenaanpassingen met aangepaste beleidsregels in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff9f57af92c50c0df6f628113bd9490ca83e1310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189290"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>SSO- en tokenaanpassing beheren met aangepaste beleidsregels in Azure Active Directory B2C

In dit artikel vindt u informatie over hoe u uw token-, sessie- en enkele aanmeldingsconfiguraties (SSO) beheren met behulp van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="token-lifetimes-and-claims-configuration"></a>Tokenlevensduur en claimconfiguratie

Als u de instellingen voor uw tokenlevensduur wilt wijzigen, voegt u een [Element ClaimsProviders](claimsproviders.md) toe in het bestand van de relying party van het beleid dat u wilt beïnvloeden.  Het element **ClaimsProviders** is een onderliggend element van het element [TrustFrameworkPolicy.](trustframeworkpolicy.md)

Voeg het element ClaimsProviders in tussen het element BasePolicy en het element RelyingParty van het bestand van de relying party.

Binnenin moet je de informatie plaatsen die van invloed is op je tokenlevensduur. De XML ziet er als volgt uit:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

De volgende waarden zijn ingesteld in het vorige voorbeeld:

- **Levensduur van access-token -** De lifetime value van toegangstoken wordt ingesteld met **token_lifetime_secs** metagegevensitem. De standaardwaarde is 3600 seconden (60 minuten).
- **Levensduur van id-token** - De levensduurwaarde van id-token wordt ingesteld met het **id_token_lifetime_secs** metagegevensitem. De standaardwaarde is 3600 seconden (60 minuten).
- **Tokenlevensduur vernieuwen** - De levensduurwaarde van het vernieuwen van token wordt ingesteld met het **item refresh_token_lifetime_secs** metagegevens. De standaardwaarde is 1209600 seconden (14 dagen).
- **Tokenschuivenschuiven schuiven gedurende de periode vernieuwen** - Als u een schuifvensterlevensduur wilt instellen op uw vernieuwingstoken, stelt u de waarde in van **rolling_refresh_token_lifetime_secs** metagegevensitem. De standaardwaarde is 7776000 (90 dagen). Als u de levensduur van een schuifvenster niet `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`wilt afdwingen, vervangt u het item door .
- **Issuer (iss) claim** - De Issuer (iss) claim is ingesteld met het **IssuanceClaimPattern** metadata item. De toepasselijke waarden `AuthorityAndTenantGuid` `AuthorityWithTfp`zijn en .
- **Claim instellen die beleids-ID vertegenwoordigt** - `TFP` De opties voor `ACR` het instellen van deze waarde zijn (vertrouwenskaderbeleid) en (verificatiecontextverwijzing). `TFP`is de aanbevolen waarde. **AuthenticationContextReferenceClaimPattern** instellen met `None`de waarde van .

    Voeg in het element **ClaimsSchema** het volgtte element toe:

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Voeg in het element **OutputClaims** dit element toe:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Verwijder voor ACR het item **AuthenticationContextReferenceClaimPattern.**

- **Object (sub)claim** - Deze optie wordt standaard ingesteld op ObjectID, als u deze instelling wilt overschakelen naar `Not Supported`, vervang deze regel:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    met deze regel:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Sessiegedrag en SSO

Als u uw sessiegedrag en SSO-configuraties wilt wijzigen, voegt u een **element UserJourneyBehaviors** toe in het element [RelyingParty.](relyingparty.md)  Het element **UserJourneyBehaviors** moet onmiddellijk de **DefaultUserJourney**volgen. De binnenkant van uw **UserJourneyBehavors** element moet er uitzien als volgt voorbeeld:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

De volgende waarden zijn geconfigureerd in het vorige voorbeeld:

- **Enkelaanmelding (SSO)** - Single sign-on is geconfigureerd met de **SingleSignOn**. De toepasselijke waarden `Tenant` `Application`zijn `Policy`, `Suppressed`, en .
- **Time-out van de web-app-sessie** - De time-out van de web-app-sessiesessie is ingesteld met het element **SessionExpiryType.** De toepasselijke waarden `Absolute` `Rolling`zijn en .
- **Levensduur van de web-app-sessie** - De levensduur van de web-app-sessie wordt ingesteld met het element **SessionExpiryInSeconds.** De standaardwaarde is 86400 seconden (1440 minuten).
