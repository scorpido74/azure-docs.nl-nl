---
title: Een rustgevende service beveiligen in uw Azure AD B2C
titleSuffix: Azure AD B2C
description: Beveilig uw aangepaste REST API-claims uitwisselingen in uw Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d1c181c18f69c040040da2be138eaad3a61693
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396862"
---
# <a name="secure-your-restful-services"></a>Beveilig uw RESTful-services 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Wanneer u een REST API integreert in een Azure AD B2C-gebruikerstraject, moet u uw REST API-eindpunt beveiligen met verificatie. Dit zorgt ervoor dat alleen services met de juiste referenties, zoals Azure AD B2C, kunnen aanbellen naar uw REST API-eindpunt.

Meer informatie over het integreren van een REST API in uw Azure AD B2C-gebruikerstraject in de [validatie van gebruikersinvoer](custom-policy-rest-api-claims-validation.md) en [het toevoegen van REST API-claimsuitwisselingen aan aangepaste beleidsregels.](custom-policy-rest-api-claims-exchange.md)

In dit artikel wordt uitgelegd hoe u uw REST API beveiligen met HTTP basic, client certificate of OAuth2 authenticatie. 

## <a name="prerequisites"></a>Vereisten

Voer de stappen uit in een van de volgende handleidingen voor 'How to':

- [Integratie van REST API-claims uitwisselingen in uw Azure AD B2C-gebruikersreis om gebruikersinvoer te valideren.](custom-policy-rest-api-claims-validation.md)
- [Rest API-claims uitwisselingen toevoegen aan aangepaste beleidsregels](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP-basisverificatie

HTTP basisverificatie wordt gedefinieerd in [RFC 2617](https://tools.ietf.org/html/rfc2617). Basisverificatie werkt als volgt: Azure AD B2C verzendt een HTTP-aanvraag met de clientreferenties in de kopautorisatie. De referenties zijn opgemaakt als de base64-gecodeerde tekenreeks "name:password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Rest API-gebruikersnaam- en wachtwoordbeleidssleutels toevoegen

Als u een REST API-technisch profiel wilt configureren met HTTP-basisverificatie, maakt u de volgende cryptografische sleutels om de gebruikersnaam en het wachtwoord op te slaan:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies uw Azure AD B2C-map.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina Overzicht de optie **Identity Experience Framework**.
1. Selecteer **Beleidssleutels**en selecteer **Toevoegen**.
1. Selecteer **Handmatig** **voor opties**.
1. Typ **RestApiUsername**voor **naam**.
    Het voorvoegsel *B2C_1A_* kan automatisch worden toegevoegd.
1. Voer **in** het vak Geheim de gebruikersnaam van de REST API in.
1. Selecteer **Versleuteling** **voor sleutelgebruik**.
1. Selecteer **Maken**.
1. Selecteer opnieuw **Beleidssleutels.**
1. Selecteer **Toevoegen**.
1. Selecteer **Handmatig** **voor opties**.
1. Typ **RestApiPassword**voor **Naam**.
    Het voorvoegsel *B2C_1A_* kan automatisch worden toegevoegd.
1. Voer **in** het vak Geheim het WACHTWOORD VAN DE REST API in.
1. Selecteer **Versleuteling** **voor sleutelgebruik**.
1. Selecteer **Maken**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Uw REST API-technisch profiel configureren om HTTP-basisverificatie te gebruiken

Nadat u de benodigde sleutels hebt gemaakt, configureert u de metagegevens van uw REST API-technische profielom verwijzing naar de referenties.

1. Open in uw werkmap het extensiebeleidsbestand (TrustFrameworkExtensions.xml).
1. Zoek naar het technische profiel VAN de REST API. Bijvoorbeeld `REST-ValidateProfile`, `REST-GetProfile`of .
1. Zoek `<Metadata>` het element.
1. Wijzig het *AuthenticationType* in `Basic`.
1. Wijzig de *AllowInsecureAuthInProduction* in `false`.
1. Voeg onmiddellijk `</Metadata>` na het slotelement het volgende XML-fragment toe:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Het volgende is een voorbeeld van een RESTful technisch profiel geconfigureerd met HTTP basisverificatie:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>HTTPS-clientcertificaatverificatie

Clientcertificaatverificatie is een verificatie op basis van wederzijdse certificaten, waarbij de client, Azure AD B2C, zijn clientcertificaat aan de server verstrekt om zijn identiteit te bewijzen. Dit gebeurt als onderdeel van de SSL-handshake. Alleen services met de juiste certificaten, zoals Azure AD B2C, hebben toegang tot uw REST API-service. Het clientcertificaat is een X.509 digitaal certificaat. In productieomgevingen moet het worden ondertekend door een certificaatautoriteit.

### <a name="prepare-a-self-signed-certificate-optional"></a>Een zelfondertekend certificaat voorbereiden (optioneel)

Voor niet-productieomgevingen u, als u nog geen certificaat hebt, een zelfondertekend certificaat gebruiken. In Windows u de cmdlet [Nieuw-Zelfondertekendcertificaat](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) van PowerShell gebruiken om een certificaat te genereren.

1. Voer deze PowerShell-opdracht uit om een zelfondertekend certificaat te genereren. Wijzig `-Subject` het argument waar nodig voor uw toepassing en de naam van Azure AD B2C-tenant. U ook `-NotAfter` de datum aanpassen om een andere vervaldatum voor het certificaat op te geven.
    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Open **Gebruikerscertificaten beheren** > Huidige**persoonlijke** > **gebruikerscertificaten** > **Current User** > *yourappname.yourtenant.onmicrosoft.com*.
1. Selecteer het certificaat > **Action** > **All Tasks** > **Export**.
1. Selecteer **Ja** > **volgende** > **ja, exporteer de privésleutel** > **Volgende**.
1. Accepteer de standaardinstellingen voor **Bestandsindeling exporteren**.
1. Geef een wachtwoord op voor het certificaat.

### <a name="add-a-client-certificate-policy-key"></a>Een beleidssleutel clientcertificaat toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies uw Azure AD B2C-map.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina Overzicht de optie **Identity Experience Framework**.
1. Selecteer **Beleidssleutels**en selecteer **Toevoegen**.
1. Selecteer **In** het vak Opties de optie **Uploaden**.
1. Typ **RestApiClientCertificate**in het vak **Naam** .
    Het voorvoegsel *B2C_1A_* wordt automatisch toegevoegd.
1. Selecteer in het vak **Bestand uploaden** het PFX-bestand van uw certificaat met een privésleutel.
1. Typ het wachtwoord van het certificaat in het vak **Wachtwoord.**
1. Selecteer **Maken**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Uw REST API-technisch profiel configureren om clientcertificaatverificatie te gebruiken

Nadat u de benodigde sleutel hebt gemaakt, configureert u de metagegevens van uw REST API-technische profielom verwijzing naar het clientcertificaat.

1. Open in uw werkmap het extensiebeleidsbestand (TrustFrameworkExtensions.xml).
1. Zoek naar het technische profiel VAN de REST API. Bijvoorbeeld `REST-ValidateProfile`, `REST-GetProfile`of .
1. Zoek `<Metadata>` het element.
1. Wijzig het *AuthenticationType* in `ClientCertificate`.
1. Wijzig de *AllowInsecureAuthInProduction* in `false`.
1. Voeg onmiddellijk `</Metadata>` na het slotelement het volgende XML-fragment toe:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Het volgende is een voorbeeld van een RESTful technisch profiel dat is geconfigureerd met een HTTP-clientcertificaat:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2 verificatie aan toonder 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Tokenverificatie aan toonder wordt gedefinieerd in [OAuth2.0 Authorization Framework: Toondertokengebruik (RFC 6750).](https://www.rfc-editor.org/rfc/rfc6750.txt) Bij verificatie van token aan toonder verzendt Azure AD B2C een HTTP-aanvraag met een token in de autorisatiekoptekst.

```http
Authorization: Bearer <token>
```

Een token aan toonder is een ondoorzichtige tekenreeks. Het kan een JWT-toegangstoken zijn of een tekenreeks waarvan de REST API verwacht dat Azure AD B2C de autorisatiekop verzendt. Azure AD B2C ondersteunt de volgende typen:

- **Toondertoken**. Om het token aan toonder in het technische profiel van Restful te kunnen verzenden, moet uw beleid eerst het token aan toonder verwerven en vervolgens gebruiken in het technische profiel van RESTful.  
- **Statisch token aan de drager**. Gebruik deze aanpak wanneer uw REST API een langdurend toegangstoken uitgeeft. Als u een statisch token aan toonder wilt gebruiken, maakt u een beleidssleutel en verwijst u vanuit het technische profiel RESTful naar uw beleidssleutel. 


## <a name="using-oauth2-bearer"></a>OAuth2-drager gebruiken  

De volgende stappen laten zien hoe u clientreferenties gebruiken om een token aan toonder te verkrijgen en door te geven aan de autorisatiekop van de REST API-aanroepen.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Een claim definiëren om het token aan toonder op te slaan

Een claim biedt tijdelijke opslag van gegevens tijdens een Azure AD B2C-beleidsuitvoering. Het [schadeschema](claimsschema.md) is de plaats waar u uw claims declareert. Het toegangstoken moet worden opgeslagen in een claim die later moet worden gebruikt. 

1. Open het extensiesbestand van uw beleid. Bijvoorbeeld. <em> `SocialAndLocalAccounts/` </em>
1. Zoek naar het element [Bouwstenen.](buildingblocks.md) Als het element niet bestaat, voeg je het toe.
1. Zoek het element [ClaimSchema.](claimsschema.md) Als het element niet bestaat, voeg je het toe.
1. Voeg de city bearerToken toe aan het element **ClaimSchema.**  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Een toegangstoken aanschaffen 

U een toegangstoken op verschillende manieren verkrijgen: door het te verkrijgen [bij een federatieve identiteitsprovider,](idp-pass-through-custom.md)door een REST-API aan te roepen die een toegangstoken retourneert, met behulp van een [ROPC-stroom](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)of door de [stroom van clientreferenties te gebruiken.](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)  

In het volgende voorbeeld wordt een technisch rest-apiprofiel gebruikt om een aanvraag te doen voor het Azure AD-tokeneindpunt met behulp van de clientreferenties die worden doorgegeven als HTTP-basisverificatie. Zie [Microsoft-identiteitsplatform en de oauth 2.0-clientreferentiesstroom](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)om dit te configureren in Azure AD. Mogelijk moet u dit wijzigen om te communiceren met uw identiteitsprovider. 

Vervang voor de ServiceUrl de naam van uw tenant door de naam van uw Azure AD-tenant. Zie de [RESTful technische profiel](restful-technical-profile.md) referentie voor alle beschikbare opties.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Het technische profiel VAN REST wijzigen om tokenverificatie aan toonder te gebruiken

Als u tokenverificatie aan toonder wilt ondersteunen in uw aangepaste beleid, wijzigt u het technische profiel VAN de REST API met het volgende:

1. Open in uw werkmap het beleidsbestand *voor verlengingsbestanden van TrustFrameworkExtensions.xml.*
1. Zoek naar `<TechnicalProfile>` het knooppunt `Id="REST-API-SignUp"`dat omvat .
1. Zoek `<Metadata>` het element.
1. Wijzig het *Verificatietype* als volgt in *Drager:*
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Wijzig of voeg de *UseClaimAsBearerToken* als volgt toe aan *bearerToken.* De *bearerToken* is de naam van de claim dat het token `SecureREST-AccessToken`aan toonder wordt opgehaald (de uitvoerclaim van ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Zorg ervoor dat u de bovenstaande claim toevoegt als invoerclaim:

    ```xml
    <InputClaim ClaimTyeReferenceId="bearerToken"/>
    ```    

Nadat u de bovenstaande fragmenten hebt toegevoegd, moet uw technische profiel er uitzien als de volgende XML-code:

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTyeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Met behulp van een statische OAuth2 drager 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>De beleidssleutel oAuth2-token aan toonder toevoegen

Maak een beleidssleutel om de tokenwaarde aan toonder op te slaan.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies uw Azure AD B2C-map.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina Overzicht de optie **Identity Experience Framework**.
1. Selecteer **Beleidssleutels**en selecteer **Toevoegen**.
1. Kies **Options**voor `Manual`Opties .
1. Voer een **naam** in voor de beleidssleutel. Bijvoorbeeld `RestApiBearerToken`. Het voorvoegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer in **Secret**het geheim van uw klant in dat u eerder hebt opgenomen.
1. Selecteer **voor sleutelgebruik**. `Encryption`
1. Selecteer **Maken**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Uw REST API-technisch profiel configureren om de beleidssleutel voor token aan toonder te gebruiken

Nadat u de benodigde sleutel hebt gemaakt, configureert u de metagegevens van uw REST API-profiel om naar het token aan de drager te verwijzen.

1. Open in uw werkmap het extensiebeleidsbestand (TrustFrameworkExtensions.xml).
1. Zoek naar het technische profiel VAN de REST API. Bijvoorbeeld `REST-ValidateProfile`, `REST-GetProfile`of .
1. Zoek `<Metadata>` het element.
1. Wijzig het *AuthenticationType* in `Bearer`.
1. Wijzig de *AllowInsecureAuthInProduction* in `false`.
1. Voeg onmiddellijk `</Metadata>` na het slotelement het volgende XML-fragment toe:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Het volgende is een voorbeeld van een RESTful technisch profiel geconfigureerd met verificatie van tokens aan toonder:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Volgende stappen

- Lees meer over het [restful technische profielelement](restful-technical-profile.md) in de IEF-referentie. 
