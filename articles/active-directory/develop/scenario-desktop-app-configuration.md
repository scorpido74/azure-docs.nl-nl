---
title: Bureaublad-apps configureren die web-API's aanroepen - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het configureren van de code van een bureaublad-app die web-API's aanroept
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f6cd9e17045fc400d32e9822cef870f1763179ab
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885259"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Bureaublad-app die web-API's aanroept: codeconfiguratie

Nu u uw toepassing hebt gemaakt, leert u hoe u de code configureren met de coördinaten van de toepassing.

## <a name="microsoft-authentication-libraries"></a>Microsoft-verificatiebibliotheken

De volgende MSALs (Microsoft Authentication Libraries) ondersteunen bureaubladtoepassingen.

  Microsoft Authentication Library | Beschrijving
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Ondersteunt het bouwen van een desktoptoepassing op meerdere platforms, zoals Linux, Windows en macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Ondersteunt het bouwen van een desktoptoepassing op meerdere platforms.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Ondersteunt het bouwen van een desktoptoepassing op meerdere platforms.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Ondersteunt alleen desktoptoepassingen die op macOS worden uitgevoerd.

## <a name="public-client-application"></a>Openbare clienttoepassing

Vanuit code-oogpunt zijn desktoptoepassingen openbare clienttoepassingen. De configuratie zal een beetje anders zijn op basis van of u interactieve authenticatie gebruikt of niet.

# <a name="net"></a>[.NET](#tab/dotnet)

Je moet MSAL.NET `IPublicClientApplication`bouwen en manipuleren.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Uitsluitend per code

Met de volgende code wordt een openbare clienttoepassing geanimeerd en worden gebruikers in de openbare Microsoft Azure-cloud inlogt met een werk- of schoolaccount of een persoonlijk Microsoft-account.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Als u van plan bent om interactieve verificatie of apparaatcodestroom te gebruiken, zoals eerder is gebleken, gebruikt u de `.WithRedirectUri` modifier.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Configuratiebestanden gebruiken

Met de volgende code wordt een openbare clienttoepassing geinstantt van een configuratieobject, dat programmatisch kan worden ingevuld of uit een configuratiebestand kan worden gelezen.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Meer uitgewerkte configuratie

U het toepassingsgebouw uitwerken door een aantal modifiers toe te voegen. Als u bijvoorbeeld wilt dat uw toepassing een multitenant-toepassing is in een nationale cloud, zoals de Amerikaanse overheid die hier wordt weergegeven, u schrijven:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET bevat ook een modifier voor Active Directory Federation Services 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Als u ten slotte tokens wilt aanschaffen voor een Azure Active Directory (Azure AD) B2C-tenant, geeft u uw tenant op zoals weergegeven in het volgende codefragment:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Meer informatie

Ga als u meer informatie over het configureren van een MSAL.NET bureaubladtoepassing:

- Zie de referentiedocumentatie `PublicClientApplicationBuilder` [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)voor een lijst met alle modifiers die beschikbaar zijn op .
- Zie [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) in `PublicClientApplicationOptions`de referentiedocumentatie voor een beschrijving van alle opties die in de referentiedocumentatie worden weergegeven.

### <a name="complete-example-with-configuration-options"></a>Voorbeeld compleet met configuratieopties

Stel je een .NET Core-consoletoepassing voor die het volgende `appsettings.json` configuratiebestand heeft:

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

Je hebt weinig code te lezen in dit bestand met behulp van de . NET-gebaseerd configuratiekader:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
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

  // Read the auth and graph endpoint configuration
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

Schrijf nu de volgende code om uw toepassing te maken:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Vóór `.Build()` de aanroep naar de methode `.WithXXX` u uw configuratie overschrijven met aanroepen naar methoden, zoals eerder is gebleken.

# <a name="java"></a>[Java](#tab/java)

Hier is de klasse die wordt gebruikt in MSAL Java ontwikkeling monsters om de monsters te configureren: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[Macos](#tab/macOS)

Met de volgende code wordt een openbare clienttoepassing geanimeerd en worden gebruikers in de openbare Microsoft Azure-cloud inlogt met een werk- of schoolaccount of een persoonlijk Microsoft-account.

### <a name="quick-configuration"></a>Snelle configuratie

Doelstelling-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Meer uitgewerkte configuratie

U het toepassingsgebouw uitwerken door een aantal modifiers toe te voegen. Als u bijvoorbeeld wilt dat uw toepassing een multitenant-toepassing is in een nationale cloud, zoals de Amerikaanse overheid die hier wordt weergegeven, u schrijven:

Doelstelling-C:

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

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een token voor een bureaublad-app aanschaffen](scenario-desktop-acquire-token.md)
