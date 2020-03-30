---
title: Instantiate een openbare client app (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het momentmaken van een openbare clienttoepassing met configuratieopties met behulp van de Microsoft-verificatiebibliotheek voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1dd06e139f931bbf8554f05f05c5d9b9ccf200e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083590"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Een openbare clienttoepassing instantiëren met configuratieopties met behulp van MSAL.NET

In dit artikel wordt beschreven hoe u een [openbare clienttoepassing](msal-client-applications.md) instantiëren met Microsoft Authentication Library voor .NET (MSAL.NET).  De toepassing wordt geinstantieerd met configuratieopties die zijn gedefinieerd in een instellingenbestand.

Voordat u een toepassing initialiseert, moet u deze eerst [registreren,](quickstart-register-app.md) zodat uw app kan worden geïntegreerd met het Microsoft-identiteitsplatform. Na registratie hebt u mogelijk de volgende informatie nodig (die te vinden is in de Azure-portal):

- De client-id (een tekenreeks die een GUID vertegenwoordigt)
- De URL van de identiteitsprovider (de naam van de instantie) en de aanmeldingsdoelgroep voor uw toepassing. Deze twee parameters staan gezamenlijk bekend als de autoriteit.
- De tenant-id als u een regel van zakelijke toepassing uitsluitend voor uw organisatie (ook wel single-tenant applicatie) schrijft.
- Voor web-apps, en soms voor openbare client-apps (met name wanneer uw app een broker moet gebruiken), hebt u ook de redirectUri ingesteld waar de identiteitsprovider contact opneemt met uw toepassing met de beveiligingstokens.


Een .NET Core-consoletoepassing kan het volgende *configuratiebestand appsettings.json* hebben:

```json
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

De volgende code leest dit bestand met behulp van het .NET-configuratiekader:

```csharp
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
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Met de volgende code wordt uw toepassing gemaakt met behulp van de configuratie vanuit het instellingenbestand:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

