---
title: Uw eigen kenmerken toevoegen aan aangepast beleid
titleSuffix: Azure AD B2C
description: Een walkthrough over het gebruik van extensie-eigenschappen en aangepaste kenmerken en deze opnemen in de gebruikersinterface.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc5204518cb6e801ba661aecd5498a501122225f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473656"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: aangepaste kenmerken inschakelen in een aangepast profielbeleid

In het [artikel Claims toevoegen en gebruikersinvoer aanpassen met behulp van het](custom-policy-configure-user-input.md) aangepaste beleidsregels leert u hoe u ingebouwde [gebruikersprofielkenmerken gebruikt.](user-profile-attributes.md) In dit artikel schakelt u een aangepast kenmerk in in uw Azure Active Directory B2C (Azure AD B2C) directory. Later u het nieuwe kenmerk tegelijkertijd gebruiken als een aangepaste claim in [gebruikersstromen](user-flow-overview.md) of [aangepaste beleidsregels.](custom-policy-get-started.md)

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Vereisten

Volg de stappen in het artikel [Azure Active Directory B2C: Aan de slag met aangepast beleid](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Aangepaste kenmerken gebruiken om informatie over uw klanten te verzamelen 

Uw Azure AD B2C-map wordt geleverd met een [ingebouwde set kenmerken.](user-profile-attributes.md) U moet echter vaak uw eigen kenmerken maken om uw specifieke scenario te beheren, bijvoorbeeld wanneer:

* Een klantgerichte toepassing moet een **LoyaltyId-kenmerk** blijven gebruiken.
* Een identiteitsprovider heeft een unieke gebruikers-id, **uniqueUserGUID,** die moet worden gehandhaafd.
* Een aangepaste gebruikersreis moet de status van de gebruiker, **migratieStatus,** voorhouden om andere logica uit te werken.

Met Azure AD B2C u de set kenmerken die op elk gebruikersaccount zijn opgeslagen, uitbreiden. U deze kenmerken ook lezen en schrijven met behulp van de [Microsoft Graph API.](manage-user-accounts-graph-api.md)

## <a name="azure-ad-b2c-extensions-app"></a>App Azure AD B2C-extensies

Extensiekenmerken kunnen alleen worden geregistreerd op een toepassingsobject, ook al kunnen ze gegevens voor een gebruiker bevatten. Het extensiekenmerk is gekoppeld aan de toepassing b2c-extensions-app. Wijzig deze toepassing niet, omdat deze wordt gebruikt door Azure AD B2C voor het opslaan van gebruikersgegevens. U deze toepassing vinden onder Azure AD B2C, app-registraties.

De *eigenschap termenextensie,* *het aangepaste kenmerk*en de aangepaste *claim* verwijzen naar hetzelfde in de context van dit artikel. De naam is afhankelijk van de context, zoals toepassing, object of beleid.

## <a name="get-the-application-properties"></a>De toepassingseigenschappen ophalen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Alle toepassingen**.
1. Selecteer `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` de toepassing.
1. Kopieer de volgende id's naar het klembord en sla ze op:
    * **Toepassings-ID**. Bijvoorbeeld: `11111111-1111-1111-1111-111111111111`.
    * **Object-id**. Bijvoorbeeld: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Uw aangepaste beleid wijzigen

Als u aangepaste kenmerken in uw beleid wilt inschakelen, geeft u **toepassings-id** en **toepassingsobject-id** op in de metagegevens van aad-algemeen technisch profiel. Het *AAD-Common* technische profiel is te vinden in het technische profiel van Azure [Active Directory](active-directory-technical-profile.md) en biedt ondersteuning voor Azure AD-gebruikersbeheer. Andere technische azure ad-technische profielen omvatten de AAD-Common om gebruik te maken van de configuratie. Overschrijven van het AAD-Common technische profiel in het extensiebestand.

1. Open het extensiesbestand van uw beleid. Bijvoorbeeld. <em> `SocialAndLocalAccounts/` </em>
1. Zoek het element Claimproviders. Voeg een nieuwe ClaimProvider toe aan het element ClaimsProviders.
1. Vervang `ApplicationObjectId` de object-id die u eerder hebt opgenomen. Vervang `ClientId` vervolgens de toepassings-id die u eerder in het onderstaande fragment hebt opgenomen.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      <TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Uw aangepaste beleid uploaden

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw Azure AD B2C-tenant bevat.
3. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
4. Selecteer **Identity Experience Framework**.
5. Selecteer **Aangepast beleid uploaden**en upload vervolgens de beleidsbestanden TrustFrameworkExtensions.xml die u hebt gewijzigd.

> [!NOTE]
> De eerste keer dat het technische azure-profiel van AD de claim op de map blijft bestaan, wordt gecontroleerd of het aangepaste kenmerk bestaat. Als dit niet het andere is, wordt het aangepaste kenmerk gemaakt.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Een aangepast kenmerk maken via Azure-portal

Dezelfde extensiekenmerken worden gedeeld tussen ingebouwde en aangepaste beleidsregels. Wanneer u aangepaste kenmerken toevoegt via de portalervaring, worden deze kenmerken geregistreerd met behulp van de **b2c-extensies-app** die in elke B2C-tenant bestaat.

U deze kenmerken maken met behulp van de portal-gebruikersinterface voor of nadat u ze hebt gebruikt in uw aangepaste beleid. Volg de richtlijnen voor het [definiëren van aangepaste kenmerken in Azure Active Directory B2C](user-flow-custom-attributes.md). Wanneer u een **kenmerkloyaltyId** in de portal maakt, moet u het als volgt doorverwijzen:

|Name     |Gebruikt in |
|---------|---------|
|`extension_loyaltyId`  | Aangepast beleid|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

In het volgende voorbeeld wordt het gebruik van aangepaste kenmerken in een azure AD B2C aangepaste beleidsclaimdefinitie weergegeven.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

In het volgende voorbeeld wordt het gebruik van een aangepast kenmerk in het aangepaste azure AD B2C-beleid in een technisch profiel, invoer, uitvoer en aanhoudende claims weergegeven.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Een aangepast kenmerk gebruiken in een beleid

Volg de richtlijnen voor het [toevoegen van claims en het aanpassen van gebruikersinvoer met behulp van aangepast beleid](custom-policy-configure-user-input.md). Dit voorbeeld maakt gebruik van een ingebouwde claim 'stad'. Als u een aangepast kenmerk wilt gebruiken, vervangt u 'stad' door uw eigen aangepaste kenmerken.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

- [Azure AD B2C-gebruikersprofielkenmerken](user-profile-attributes.md)
- [Definitie van extensiekenmerken](user-profile-attributes.md#extension-attributes)
- [Azure AD B2C-gebruikersaccounts beheren met Microsoft Graph](manage-user-accounts-graph-api.md)
