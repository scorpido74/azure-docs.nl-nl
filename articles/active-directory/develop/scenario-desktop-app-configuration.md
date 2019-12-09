---
title: De bureau blad-app configureren die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het configureren van de code van een bureau blad-app die web-Api's aanroept
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d31a70ebc63a5e9a16e0da00623bd5855f0a7d1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920256"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Bureau blad-app die web-Api's aanroept-code configuratie

Nu u de toepassing hebt gemaakt, leert u hoe u de code kunt configureren met de coördinaten van de toepassing.

## <a name="msal-libraries"></a>MSAL-bibliotheken

De volgende micro soft-bibliotheken ondersteunen bureaublad toepassingen:

  MSAL-bibliotheek | Beschrijving
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Biedt ondersteuning voor het bouwen van een bureaublad toepassing in meerdere platforms-Linux, Windows en MacOS
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL python | Biedt ondersteuning voor het bouwen van een bureaublad toepassing op meerdere platforms. Ontwikkeling in uitvoering-in open bare preview
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Biedt ondersteuning voor het bouwen van een bureaublad toepassing op meerdere platforms. Ontwikkeling in uitvoering-in open bare preview
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Ondersteunt alleen bureaublad toepassingen die worden uitgevoerd op macOS

## <a name="public-client-application"></a>Open bare client toepassing

Vanuit een code point van de weer gave zijn desktop toepassingen open bare client toepassingen. De configuratie is een andere bit, afhankelijk van of u interactieve verificatie gebruikt of niet.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

U moet MSAL.NET-`IPublicClientApplication`bouwen en bewerken.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Uitsluitend op code

Met de volgende code wordt een open bare client toepassing geïnstantieerd, waarbij gebruikers zich aanmelden in de open bare Cloud Microsoft Azure, met een werk-en school account of een persoonlijke Microsoft-account.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Als u van plan bent interactieve verificatie of de code stroom van het apparaat te gebruiken, zoals hierboven wordt weer gegeven, wilt u de `.WithRedirectUri` modificator gebruiken:

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

- Zie de referentie documentatie [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) voor een lijst met alle wijzigingen die op `PublicClientApplicationBuilder`beschikbaar zijn.
- Zie [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)in de referentie documentatie voor de beschrijving van alle opties die worden weer gegeven in `PublicClientApplicationOptions`.

### <a name="complete-example-with-configuration-options"></a>Volledig voor beeld met configuratie opties

Stel dat u een .NET core-console toepassing hebt die het volgende `appsettings.json` configuratie bestand bevat:

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

en voordat u de `.Build()` methode aanroept, kunt u uw configuratie overschrijven met aanroepen naar `.WithXXX` methoden zoals eerder is weer gegeven.

# <a name="javatabjava"></a>[Java](#tab/java)

Dit is de klasse die wordt gebruikt in MSAL voor beelden van Java-Ontwikkel aars om de voor beelden te configureren: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

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
---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een token voor een bureau blad-app ophalen](scenario-desktop-acquire-token.md)
