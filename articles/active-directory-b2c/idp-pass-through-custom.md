---
title: Een toegangs token door geven via een aangepast beleid voor uw app
titleSuffix: Azure AD B2C
description: Meer informatie over hoe u een toegangs token kunt door geven voor OAuth 2,0-id-providers als een claim via een aangepast beleid voor uw toepassing in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8f7122035f8d70cb91f4ec4f64e1dd4f7b2842b8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949837"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Geef een toegangs token door via een aangepast beleid voor uw toepassing in Azure Active Directory B2C

Een [aangepast beleid](active-directory-b2c-get-started-custom.md) in Azure Active Directory B2C (Azure AD B2C) biedt gebruikers van uw toepassing de mogelijkheid zich aan te melden of zich aan te melden met een id-provider. Als dit gebeurt, ontvangt Azure AD B2C een [toegangs token](active-directory-b2c-reference-tokens.md) van de ID-provider. Azure AD B2C gebruikt dat token om informatie over de gebruiker op te halen. U voegt een claim type en uitvoer claim toe aan uw aangepaste beleid om het token door te geven aan de toepassingen die u registreert in Azure AD B2C.

Azure AD B2C ondersteunt het door geven van het toegangs token van [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) -en [OpenID Connect Connect](active-directory-b2c-reference-oidc.md) -id-providers. Voor alle andere id-providers wordt de claim leeg geretourneerd.

## <a name="prerequisites"></a>Vereisten

* Het aangepaste beleid is geconfigureerd met een OAuth 2,0-of OpenID Connect Connect-ID-provider.

## <a name="add-the-claim-elements"></a>De claim elementen toevoegen

1. Open uw *TrustframeworkExtensions. XML-* bestand en voeg het volgende **claim** type-element met de id `identityProviderAccessToken` toe aan het **ClaimsSchema** -element:

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

2. Voeg het **output claim** -element toe aan het **TechnicalProfile** -element voor elke OAuth 2,0-ID-provider waarvoor u het toegangs token voor wilt gebruiken. In het volgende voor beeld ziet u het element dat is toegevoegd aan het technische profiel voor Facebook:

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

3. Sla het bestand *TrustframeworkExtensions. XML* op.
4. Open uw Relying Party-beleids bestand, zoals *SignUpOrSignIn. XML*, en voeg het element **output claim** toe aan de **TechnicalProfile**:

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

5. Sla het beleids bestand op.

## <a name="test-your-policy"></a>Uw beleid testen

Bij het testen van uw toepassingen in Azure AD B2C kan het nuttig zijn om het Azure AD B2C-token terug te krijgen naar `https://jwt.ms` om de claims in de toepassing te kunnen bekijken.

### <a name="upload-the-files"></a>De bestanden uploaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C Tenant bevat door te klikken op het filter **Directory + abonnement** in het bovenste menu en de map te kiezen die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer een **Framework voor identiteits ervaring**.
5. Klik op het tabblad Aangepaste beleids regels op **beleid uploaden**.
6. Selecteer **het beleid overschrijven als dit bestaat**, en zoek en selecteer het bestand *TrustframeworkExtensions. XML* .
7. Selecteer **Uploaden**.
8. Herhaal stap 5 tot en met 7 voor het Relying Party bestand, zoals *SignUpOrSignIn. XML*.

### <a name="run-the-policy"></a>Het beleid uitvoeren

1. Open het beleid dat u hebt gewijzigd. Bijvoorbeeld *B2C_1A_signup_signin*.
2. Selecteer voor **toepassing**de toepassing die u eerder hebt geregistreerd. Voor een overzicht van het token in het onderstaande voor beeld moet de **antwoord-URL** `https://jwt.ms`weer geven.
3. Selecteer **nu uitvoeren**.

    Het volgende voor beeld ziet er ongeveer uit:

    ![Gedecodeer token in jwt.ms met idp_access_token blok gemarkeerd](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over tokens in de [Azure Active Directory B2C-token verwijzing](active-directory-b2c-reference-tokens.md).
