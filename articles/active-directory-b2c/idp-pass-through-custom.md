---
title: Een toegangstoken doorgeven via een aangepast beleid aan uw app
titleSuffix: Azure AD B2C
description: Meer informatie over hoe u een toegangstoken voor OAuth 2.0-identiteitsproviders als claim doorgeven via een aangepast beleid aan uw toepassing in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff5ef8f742914129d868152814d84d2112267c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187783"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Een toegangstoken doorgeven via een aangepast beleid aan uw toepassing in Azure Active Directory B2C

Een [aangepast beleid](custom-policy-get-started.md) in Azure Active Directory B2C (Azure AD B2C) biedt gebruikers van uw toepassing de mogelijkheid om zich aan te melden of zich aan te melden bij een identiteitsprovider. Wanneer dit gebeurt, ontvangt Azure AD B2C een [toegangstoken](tokens-overview.md) van de identiteitsprovider. Azure AD B2C gebruikt dat token om informatie over de gebruiker op te halen. U voegt een claimtype en uitvoerclaim toe aan uw aangepaste beleid om het token door te geven aan de toepassingen die u registreert in Azure AD B2C.

Azure AD B2C ondersteunt het doorgeven van het toegangstoken van [OAuth 2.0-](authorization-code-flow.md) en [OpenID Connect-identiteitsproviders.](openid-connect.md) Voor alle andere identiteitsproviders wordt de claim leeg geretourneerd.

## <a name="prerequisites"></a>Vereisten

* Uw aangepaste beleid is geconfigureerd met een OAuth 2.0- of OpenID Connect-identiteitsprovider.

## <a name="add-the-claim-elements"></a>De claimelementen toevoegen

1. Open het bestand *TrustframeworkExtensions.xml* en voeg het volgende `identityProviderAccessToken` **Element ClaimType** toe met een id van het element **ClaimsSchema:**

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Voeg het **element OutputClaim** toe aan het element **TechnicalProfile** voor elke OAuth 2.0-identiteitsprovider waarvoor u het toegangstoken wilt hebben. In het volgende voorbeeld ziet u het element dat is toegevoegd aan het technische profiel van Facebook:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Sla het bestand *TrustframeworkExtensions.xml op.*
4. Open het beleidsbestand van uw relying party, zoals *SignUpOrSignIn.xml,* en voeg het element **OutputClaim** toe aan het **TechnicalProfile:**

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Sla het beleidsbestand op.

## <a name="test-your-policy"></a>Uw beleid testen

Wanneer u uw toepassingen in Azure AD B2C test, kan het handig `https://jwt.ms` zijn om het Azure AD B2C-token terug te laten keren om de claims in de toepassing ervan te kunnen bekijken.

### <a name="upload-the-files"></a>De bestanden uploaden

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door in het bovenste menu op het **filter Directory + abonnement** te klikken en de map te kiezen die uw tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Identity Experience Framework**.
5. Klik op de pagina Aangepast beleid op **Beleid uploaden**.
6. Selecteer **Het beleid overschrijven als het bestaat**en zoek en selecteer het bestand *TrustframeworkExtensions.xml.*
7. Selecteer **Uploaden**.
8. Herhaal stap 5 tot en met 7 voor het bestand van de relying party, zoals *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Voer het beleid uit

1. Open het beleid dat u hebt gewijzigd. Bijvoorbeeld, *B2C_1A_signup_signin*.
2. Selecteer **voor Toepassing**uw toepassing die u eerder hebt geregistreerd. Als u het token in het onderstaande `https://jwt.ms`voorbeeld wilt zien, moet de URL voor **beantwoorden** worden weergegeven .
3. Selecteer **Nu uitvoeren**.

    U ziet iets dat lijkt op het volgende voorbeeld:

    ![Gedecodeerd token in jwt.ms met idp_access_token blok gemarkeerd](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over tokens vindt u in de [naslaggids voor Azure Active Directory B2C-token](tokens-overview.md).
