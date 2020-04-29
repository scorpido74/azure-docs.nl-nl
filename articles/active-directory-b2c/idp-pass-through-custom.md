---
title: Een toegangs token door geven via een aangepast beleid voor uw app
titleSuffix: Azure AD B2C
description: Meer informatie over hoe u een toegangs token kunt door geven voor OAuth 2,0-id-providers als een claim via een aangepast beleid voor uw toepassing in Azure Active Directory B2C.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78187783"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Geef een toegangs token door via een aangepast beleid voor uw toepassing in Azure Active Directory B2C

Een [aangepast beleid](custom-policy-get-started.md) in Azure Active Directory B2C (Azure AD B2C) biedt gebruikers van uw toepassing de mogelijkheid zich aan te melden of zich aan te melden met een id-provider. Als dit gebeurt, ontvangt Azure AD B2C een [toegangs token](tokens-overview.md) van de ID-provider. Azure AD B2C gebruikt dat token om informatie over de gebruiker op te halen. U voegt een claim type en uitvoer claim toe aan uw aangepaste beleid om het token door te geven aan de toepassingen die u registreert in Azure AD B2C.

Azure AD B2C ondersteunt het door geven van het toegangs token van [OAuth 2,0](authorization-code-flow.md) -en [OpenID Connect Connect](openid-connect.md) -id-providers. Voor alle andere id-providers wordt de claim leeg geretourneerd.

## <a name="prerequisites"></a>Vereisten

* Het aangepaste beleid is geconfigureerd met een OAuth 2,0-of OpenID Connect Connect-ID-provider.

## <a name="add-the-claim-elements"></a>De claim elementen toevoegen

1. Open uw *TrustframeworkExtensions. XML-* bestand en voeg het volgende **claim** type-element toe `identityProviderAccessToken` met een id van aan het **ClaimsSchema** -element:

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

Bij het testen van uw toepassingen in Azure AD B2C kan het nuttig zijn om het Azure AD B2C-token te `https://jwt.ms` retour neren om de claims daarin te kunnen bekijken.

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
2. Selecteer voor **toepassing**de toepassing die u eerder hebt geregistreerd. Voor een overzicht van het token in het onderstaande voor beeld moet de antwoord `https://jwt.ms`- **URL** worden weer gegeven.
3. Selecteer **nu uitvoeren**.

    Het volgende voor beeld ziet er ongeveer uit:

    ![Gedecodeer token in jwt.ms met idp_access_token blok gemarkeerd](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over tokens in de [Azure Active Directory B2C-token verwijzing](tokens-overview.md).
