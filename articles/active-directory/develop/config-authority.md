---
title: Identiteitsproviders configureren (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het gebruik van verschillende autoriteiten, zoals B2C, sovereign clouds en gastgebruikers, met MSAL voor iOS en macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085206"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>How to: MSAL configureren voor iOS en macOS om verschillende identiteitsproviders te gebruiken

In dit artikel ziet u hoe u uw Microsoft-verificatiebibliotheek-app voor iOS en macOS (MSAL) configureert voor verschillende autoriteiten, zoals Azure Active Directory (Azure AD), Business-to-Consumer (B2C), soevereine clouds en gastgebruikers.  In dit artikel u over het algemeen denken aan een autoriteit als een identiteitsprovider.

## <a name="default-authority-configuration"></a>Standaardconfiguratie van de autoriteit

`MSALPublicClientApplication`is geconfigureerd met een URL `https://login.microsoftonline.com/common`van de standaardautoriteit van , die geschikt is voor de meeste Azure Active Directory (AAD)-scenario's. Tenzij u geavanceerde scenario's zoals nationale clouds implementeert of met B2C werkt, hoeft u deze niet te wijzigen.

> [!NOTE]
> Moderne verificatie met Active Directory Federation Services als identiteitsprovider (ADFS) wordt niet ondersteund (zie [ADFS voor ontwikkelaars](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) voor meer informatie). ADFS wordt ondersteund door federatie.

## <a name="change-the-default-authority"></a>De standaardautoriteit wijzigen

In sommige scenario's, zoals business-to-consumer (B2C), moet u mogelijk de standaardautoriteit wijzigen.

### <a name="b2c"></a>B2C

Om met B2C te werken, vereist de [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) een configuratie van een andere autoriteit. MSAL herkent één URL-indeling van de autoriteit als B2C op zichzelf. De erkende B2C-autoriteitsindeling `https://<host>/tfp/<tenant>/<policy>` `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`is bijvoorbeeld . U echter ook alle andere ondersteunde URL's van de B2C-autoriteit gebruiken door de autoriteit expliciet als B2C-autoriteit aan te zeggen.

Als u een willekeurige URL-indeling `MSALB2CAuthority` voor B2C wilt ondersteunen, u worden ingesteld met een willekeurige URL, zoals deze:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Alle B2C-autoriteiten die geen gebruik maken van de standaard B2C-instantiemoeten als bekende autoriteiten worden aangegeven.

Voeg elke verschillende B2C-bevoegdheid toe aan de lijst van bekende autoriteiten, zelfs als de autoriteiten alleen verschillen in beleid.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Wanneer uw app een nieuw beleid aanvraagt, moet de URL van de autoriteit worden gewijzigd omdat de URL van de autoriteit voor elk beleid verschilt. 

Als u een B2C-toepassing wilt configureren, stelt u in `@property MSALAuthority *authority` met een instantie van `MSALB2CAuthority` in `MSALPublicClientApplicationConfig` voordat u deze `MSALPublicClientApplication`maakt, zoals:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Soevereine wolken

Als uw app in een soevereine cloud wordt uitgevoerd, `MSALPublicClientApplication`moet u mogelijk de url van de autoriteit wijzigen in de . In het volgende voorbeeld wordt de URL van de autoriteit ingesteld om te werken met de Duitse AAD-cloud:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Het kan nodig zijn om verschillende scopes door te geven aan elke soevereine cloud. Welke scopes u wilt verzenden, is afhankelijk van de resource die u gebruikt. U bijvoorbeeld `"https://graph.microsoft.com/user.read"` gebruiken in de `"https://graph.microsoft.de/user.read"` wereldwijde cloud en in de Duitse cloud.

### <a name="signing-a-user-into-a-specific-tenant"></a>Een gebruiker aanmelden bij een specifieke tenant

Wanneer de URL van `"login.microsoftonline.com/common"`de autoriteit is ingesteld op , wordt de gebruiker aangemeld bij zijn woonplaatstenant. Het kan echter zijn dat sommige apps de gebruiker moeten aanmelden bij een andere tenant en sommige apps werken alleen met één tenant.

Als u de gebruiker wilt `MSALPublicClientApplication` aanmelden bij een specifieke tenant, configureert u met een specifieke autoriteit. Bijvoorbeeld:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Het volgende laat zien hoe u een gebruiker aaneentekent bij een specifieke tenant:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Ondersteunde autoriteiten

### <a name="msalauthority"></a>MSALAutoriteit

De `MSALAuthority` klasse is de basis abstracte klasse voor de MSAL autoriteit klassen. Probeer er geen instantie van `alloc` te `new`maken met of . Maak in plaats daarvan een van`MSALAADAuthority`de `MSALB2CAuthority`subklassen rechtstreeks `authorityWithURL:error:` (, ) of gebruik de fabrieksmethode om subklassen te maken met behulp van een autoriteits-URL.

Gebruik `url` de eigenschap om een genormaliseerde autoriteit URL te krijgen. Extra parameters en padcomponenten of fragmenten die geen deel uitmaken van de autoriteit, bevinden zich niet in de geretourneerde genormaliseerde autoriteits-URL.

De volgende zijn subklassen van `MSALAuthority` die u instantiate afhankelijk van de autoriteit wilt gebruiken.

### <a name="msalaadauthority"></a>MSALAADAutoriteit

`MSALAADAuthority`vertegenwoordigt een AAD-autoriteit. De url van de autoriteit moet `<port>` in de volgende indeling zijn, waar is optioneel:`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAutoriteit

`MSALB2CAuthority`vertegenwoordigt een B2C-autoriteit. Standaard moet de url van de B2C-autoriteit `<port>` in `https://<host>:<port>/tfp/<tenant>/<policy>`de volgende indeling staan, waar is optioneel: . MSAL ondersteunt echter ook andere willekeurige B2C-autoriteitsformaten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatiestromen en toepassingsscenario's](authentication-flows-app-scenarios.md)
