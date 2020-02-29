---
title: Uw eigen kenmerken toevoegen aan aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Een overzicht van het gebruik van extensie-eigenschappen en aangepaste kenmerken en het opnemen ervan in de gebruikers interface.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c145b0efa2f1c06710e1d41f606f918a7439820a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189494"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: aangepaste kenmerken gebruiken in een aangepast profiel beleid bewerken

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel maakt u een aangepast kenmerk in de map Azure Active Directory B2C (Azure AD B2C). U gebruikt dit nieuwe kenmerk als een aangepaste claim in het profiel bewerkings traject van de gebruiker.

## <a name="prerequisites"></a>Vereisten

Volg de stappen in het artikel [Azure Active Directory B2C: aan de slag met aangepast beleid](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Aangepaste kenmerken gebruiken voor het verzamelen van informatie over uw klanten in Azure AD B2C met behulp van aangepast beleid
Uw Azure AD B2C Directory wordt geleverd met een ingebouwde set kenmerken. Voor beelden zijn namen, **Achternaam**, **plaats**, **Post code**en **userPrincipalName**. U moet vaak uw eigen kenmerken maken, zoals deze voor beelden:
* Een klant gerichte toepassing moet persistent blijven voor een kenmerk zoals **LoyaltyNumber.**
* Een id-provider heeft een unieke gebruikers-id, zoals **uniqueUserGUID** , die moeten worden opgeslagen.
* Een aangepaste gebruikers reis moet persistent worden gemaakt voor een status van een gebruiker, zoals **migrationStatus**.

Azure AD B2C breidt de set kenmerken uit die zijn opgeslagen op elk gebruikers account. U kunt deze kenmerken ook lezen en schrijven met behulp van de [Microsoft Graph-API](manage-user-accounts-graph-api.md).

Extensie-eigenschappen breiden het schema van de gebruikers objecten in de map uit. De *extensie-eigenschap*, het *aangepaste kenmerk*en de *aangepaste claim* verwijzen naar hetzelfde item in de context van dit artikel. De naam kan variëren, afhankelijk van de context, zoals toepassing, object of beleid.

Extensie-eigenschappen kunnen alleen worden geregistreerd voor een toepassings object, zelfs als ze mogelijk gegevens voor een gebruiker bevatten. De eigenschap is gekoppeld aan de toepassing. Het toepassings object moet schrijf toegang hebben voor het registreren van een extensie-eigenschap. Een honderd uitbrei ding van eigenschappen, in alle typen en alle toepassingen, kan naar een enkel object worden geschreven. Extensie-eigenschappen worden toegevoegd aan het doel directory type en worden direct toegankelijk in de Azure AD B2C Directory-Tenant.
Als de toepassing wordt verwijderd, worden ook de extensie-eigenschappen, samen met alle gegevens in deze toepassingen, verwijderd. Als een extensie-eigenschap door de toepassing wordt verwijderd, wordt deze verwijderd uit de doel directory-objecten en worden de waarden verwijderd.

Extensie-eigenschappen bestaan alleen in de context van een geregistreerde toepassing in de Tenant. De object-ID van de toepassing moet worden opgenomen in de **TechnicalProfile** die gebruikt.

>[!NOTE]
>De Azure AD B2C Directory bevat doorgaans een web-app met de naam `b2c-extensions-app`. Deze toepassing wordt voornamelijk gebruikt door het ingebouwde B2C-beleid voor de aangepaste claims die zijn gemaakt via de Azure Portal. Het is raadzaam dat alleen geavanceerde gebruikers uitbrei dingen voor B2C aangepaste beleids regels registreren met behulp van deze toepassing.
Instructies zijn opgenomen in de sectie **volgende stappen** in dit artikel.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Een nieuwe toepassing maken voor het opslaan van de extensie-eigenschappen

1. Open een browser sessie en navigeer naar het [Azure Portal](https://portal.azure.com). Meld u aan met de beheerders referenties van de B2C-map die u wilt configureren.
2. Selecteer **Azure Active Directory** in het navigatie menu aan de linkerkant. Mogelijk moet u deze vinden door **meer services**te selecteren.
3. Selecteer **App-registraties**. Selecteer **Nieuwe toepassing registreren**.
4. Geef de volgende gegevens op:
    * Een naam voor de webtoepassing: **webapp-GraphAPI-DirectoryExtensions**.
    * Het toepassings type: **Web-app/API**.
    * De aanmeldings-URL: **https://{tenantnaam}. onmicrosoft. com/webapp-GraphAPI-DirectoryExtensions**.
5. Selecteer **Maken**.
6. Selecteer de zojuist gemaakte webtoepassing.
7. Selecteer **instellingen** > **vereiste machtigingen**.
8. Selecteer de API- **Windows-Azure Active Directory**.
9. Voer een vinkje in bij toepassings machtigingen: **Directory gegevens lezen en schrijven**. Selecteer vervolgens **Opslaan**.
10. Kies **machtigingen verlenen** en bevestig **Ja**.
11. Kopieer de volgende id's naar het klem bord en sla ze op:
    * **Toepassings-id**. Voor beeld: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **Object-id**. Voor beeld: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Wijzig uw aangepaste beleid om de **ApplicationObjectId** toe te voegen

Wanneer u de stappen in [Azure Active Directory B2C: aan de slag met aangepast beleid](custom-policy-get-started.md), hebt u [voorbeeld bestanden](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) gedownload en gewijzigd met de naam **TrustFrameworkBase. XML**, **TrustFrameworkExtensions. XML**, **SignUpOrSignin. XML**, **ProfileEdit. XML**en **PasswordReset. XML**. In deze stap brengt u meer wijzigingen aan die bestanden aan.

* Open het bestand **TrustFrameworkBase. XML** en voeg de sectie `Metadata` toe, zoals in het volgende voor beeld wordt weer gegeven. Voeg de object-ID in die u eerder hebt geregistreerd voor de `ApplicationObjectId` waarde en de toepassings-ID die u hebt genoteerd voor de `ClientId` waarde:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->

          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> Wanneer de **TechnicalProfile** voor de eerste keer naar de zojuist gemaakte extensie-eigenschap schrijft, kan er een eenmalige fout optreden. De eigenschap Extension wordt gemaakt wanneer deze voor het eerst wordt gebruikt.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>De nieuwe extensie-eigenschap of aangepast kenmerk in een gebruikers traject gebruiken

1. Open het bestand **ProfileEdit. XML** .
2. Voeg een aangepaste claim toe `loyaltyId`. Door de aangepaste claim op te nemen in het `<RelyingParty>`-element, wordt deze opgenomen in het token voor de toepassing.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. Open het bestand **TrustFrameworkExtensions. XML** en voeg het`<ClaimsSchema>`-element en de onderliggende elementen toe aan het `BuildingBlocks`-element:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Voeg dezelfde `ClaimType` definitie toe aan **TrustFrameworkBase. XML**. Het is niet nodig om een `ClaimType` definitie toe te voegen in de bestanden basis en extensies. Met de volgende stappen wordt de `extension_loyaltyId` echter toegevoegd aan **TechnicalProfiles** in het basis bestand. De beleids validatie van het beleid weigert de upload van het basis bestand zonder dat. Het kan handig zijn om de uitvoering van de gebruikers traject met de naam **ProfileEdit** te traceren in het bestand **TrustFrameworkBase. XML** . Zoek naar de gebruikers traject met dezelfde naam in de editor. Houd er rekening mee dat met de Orchestration Step 5 de **TechnicalProfileReferenceID = "SelfAsserted-ProfileUpdate**. Zoek en Inspecteer deze **TechnicalProfile** om vertrouwd te raken met de stroom.

5. Open het bestand **TrustFrameworkBase. XML** en voeg `loyaltyId` toe als een invoer-en uitvoer claim in de **TechnicalProfile SelfAsserted-ProfileUpdate**:

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. Voeg in het bestand **TrustFrameworkBase. XML** de `loyaltyId` claim toe aan **TechnicalProfile Aad-UserWriteProfileUsingObjectId**. De waarde van de claim wordt door deze toevoeging opgeslagen in de extensie-eigenschap voor de huidige gebruiker in de map:

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. Voeg in het bestand **TrustFrameworkBase. XML** de `loyaltyId` claim toe aan **TechnicalProfile Aad-UserReadUsingObjectId** om de waarde van het uitbreidings kenmerk te lezen wanneer een gebruiker zich aanmeldt. Tot nu toe zijn de **TechnicalProfiles** gewijzigd in de stroom van lokale accounts. Als u het nieuwe kenmerk in de stroom van een sociaal of federatief account wilt, moet u een andere set **TechnicalProfiles** wijzigen. Zie de sectie **volgende stappen** .

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Open de Blade Azure AD B2C en navigeer naar **Identity experience Framework** > **aangepast beleid**.
1. Selecteer het aangepaste beleid dat u hebt geüpload. Selecteer **nu uitvoeren**.
1. Meld u aan met een e-mail adres.

Het ID-token dat teruggestuurd naar uw toepassing bevat de nieuwe extensie-eigenschap als aangepaste claim voorafgegaan door **extension_loyaltyId**. Zie het volgende voorbeeld:

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Volgende stappen

1. Voeg de nieuwe claim toe aan de stromen om u aan te melden bij sociale accounts door de volgende **TechnicalProfiles**te wijzigen. Sociale en federatieve accounts gebruiken deze twee **TechnicalProfiles** om u aan te melden. Ze schrijven en lezen gebruikers gegevens met behulp van de **alternativeSecurityId** als de Locator van het gebruikers object.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Gebruik dezelfde extensie kenmerken tussen ingebouwde en aangepaste beleids regels. Wanneer u een extensie, of aangepaste, kenmerken via de portal-ervaring toevoegt, worden deze kenmerken geregistreerd met behulp van de **B2C-extensies-app** die in elke B2C-Tenant bestaat. Voer de volgende stappen uit om extensie kenmerken in uw aangepaste beleid te gebruiken:

   a. Ga in de B2C-Tenant in portal.azure.com naar **Azure Active Directory** en selecteer **app-registraties**.
   b. Zoek uw **B2C-extensies-app** en selecteer deze.
   c. Onder **Essentials**voert u de **toepassings-id** en de **object-id**in.
   d. Neem deze op in uw **Aad-common TechnicalProfile-** meta gegevens:

   ```xml
      <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
          <TechnicalProfile Id="AAD-Common">
            <DisplayName>Azure Active Directory</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <!-- Provide objectId and appId before using extension properties. -->
            <Metadata>
              <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
              <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
            </Metadata>
   ```

3. Blijf consistent met de portal-ervaring. Maak deze kenmerken met behulp van de portal-gebruikers interface voordat u ze in uw aangepaste beleids regels gebruikt. Wanneer u een kenmerk **ActivationStatus** in de portal maakt, moet u dit als volgt:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Naslaginformatie

Zie voor meer informatie over uitbreidings eigenschappen het artikel [aangepaste gegevens toevoegen aan resources met behulp van extensies](https://docs.microsoft.com/graph/extensibility-overview).

> [!NOTE]
> * Een **TechnicalProfile** is een element type, of functie, waarmee de naam, meta gegevens en het Protocol van een eind punt worden gedefinieerd. De **TechnicalProfile** geeft een overzicht van de uitwisseling van claims die het Framework voor identiteits ervaring uitvoert. Wanneer deze functie wordt aangeroepen in een indelings stap of vanuit een andere **TechnicalProfile**, worden de **InputClaims** en **OutputClaims** als para meters door de aanroeper opgegeven.
> * Extensie kenmerken in de Graph API worden genoemd met behulp van de Conventie `extension_ApplicationObjectID_attributename`.
> * Aangepaste beleids regels verwijzen naar extensie kenmerken als **extension_attributename**. Met deze verwijzing wordt de **ApplicationObjectId** in XML wegge laten.
> * U moet de kenmerk-ID opgeven in de volgende indeling **extension_attributename** waar hiernaar naar wordt verwezen.
