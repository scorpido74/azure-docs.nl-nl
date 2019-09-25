---
title: Bureau blad-app die web-Api's aanroept (code configuratie)-micro soft Identity-platform
description: Meer informatie over het bouwen van een bureau blad-app die web-Api's aanroept (de code configuratie van de app)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0926e6800dbcd81d2e542e27afe3afb1240cff22
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268421"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Bureau blad-app die web-Api's aanroept-code configuratie

Nu u de toepassing hebt gemaakt, leert u hoe u de code kunt configureren met de coördinaten van de toepassing.

## <a name="msal-libraries"></a>MSAL-bibliotheken

De enige MSAL-bibliotheek die desktop toepassingen op meerdere platforms ondersteunt, is momenteel MSAL.NET.

MSAL voor iOS en macOS ondersteunt alleen bureaublad toepassingen die worden uitgevoerd op macOS. 

## <a name="public-client-application-with-msalnet"></a>Open bare client toepassing met MSAL.NET

Vanuit een code point van de weer gave zijn desktop toepassingen open bare client toepassingen. dat is de reden waarom u MSAL.NET `IPublicClientApplication`bouwt en bewerkt. Het is ook iets anders, ongeacht of u interactieve verificatie gebruikt of niet.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Uitsluitend op code

Met de volgende code wordt een open bare client toepassing geïnstantieerd, waarbij gebruikers zich aanmelden in de open bare Cloud Microsoft Azure, met een werk-en school account of een persoonlijke Microsoft-account.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Als u een interactieve verificatie of een apparaatcode stroom wilt gebruiken, zoals hierboven wordt weer gegeven, wilt u `.WithRedirectUri` de modificator gebruiken:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Configuratie bestanden gebruiken

Met de volgende code wordt een open bare client toepassing geïnstantieerd vanuit een configuratie object, die via een programma kan worden ingevuld of gelezen vanuit een configuratie bestand

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Meer uitgebreide configuratie

U kunt het bouwen van toepassingen uitwerken door een aantal para meters toe te voegen. Als u bijvoorbeeld wilt dat uw toepassing een multi tenant toepassing in een nationale Cloud (hier de Amerikaanse overheid) is, kunt u het volgende schrijven:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET bevat ook een modificator voor ADFS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Ten slotte kunt u, als u tokens voor een Azure AD B2C Tenant wilt verkrijgen, uw Tenant opgeven, zoals wordt weer gegeven in het volgende code fragment:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Meer informatie

Meer informatie over het configureren van een MSAL.NET-bureaublad toepassing:

- Zie de naslag documentatie [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) voor de lijst met `PublicClientApplicationBuilder`alle wijzigingen die beschikbaar zijn op.
- Voor de beschrijving van alle opties die beschikbaar zijn `PublicClientApplicationOptions` in Zie [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), in de referentie documentatie

## <a name="complete-example-with-configuration-options"></a>Volledig voor beeld met configuratie opties

Stel dat u een .net core-console toepassing hebt `appsettings.json` die het volgende configuratie bestand bevat:

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

U hebt weinig code om dit bestand te lezen met behulp van het .NET-configuratie raamwerk dat u hebt geconfigureerd.

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint config
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Als u de toepassing nu wilt maken, hoeft u alleen de volgende code te schrijven:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

en voordat u de `.Build()` methode aanroept, kunt u uw configuratie overschrijven met aanroepen naar `.WithXXX` methoden die eerder zijn weer gegeven.

## <a name="public-client-application-with-msal-for-ios-and-macos"></a>Open bare client toepassing met MSAL voor iOS en macOS

Met de volgende code wordt een open bare client toepassing geïnstantieerd, waarbij gebruikers zich aanmelden in de open bare Cloud Microsoft Azure, met een werk-en school account of een persoonlijke Microsoft-account.

### <a name="quick-configuration"></a>Snelle configuratie

Doel-C:

```objc
NSError *msalError = nil;
    
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Meer uitgebreide configuratie

U kunt het bouwen van toepassingen uitwerken door een aantal para meters toe te voegen. Als u bijvoorbeeld wilt dat uw toepassing een multi tenant toepassing in een nationale Cloud (hier de Amerikaanse overheid) is, kunt u het volgende schrijven:

Doel-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een token voor een bureau blad-app ophalen](scenario-desktop-acquire-token.md)
