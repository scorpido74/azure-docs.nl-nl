---
title: Een open bare client-app instantiëren met opties (micro soft-verificatie bibliotheek voor .NET)
titleSuffix: Microsoft identity platform
description: Meer informatie over het instantiëren van een open bare client toepassing met configuratie opties met behulp van de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdc6d65d811c09ffae94fe6394f8ae7e61cf0a6f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802865"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Een open bare client toepassing instantiëren met configuratie opties met behulp van MSAL.NET

In dit artikel wordt beschreven hoe u een [open bare client toepassing](msal-client-applications.md) kunt instantiëren met micro soft Authentication Library voor .net (MSAL.net).  De toepassing wordt geïnstantieerd met configuratie opties die zijn gedefinieerd in een instellingen bestand.

Voordat u een toepassing initialiseert, moet u deze eerst [registreren](quickstart-register-app.md) , zodat uw app kan worden geïntegreerd met het micro soft Identity-platform. Na de registratie hebt u mogelijk de volgende informatie nodig (die kan worden gevonden in de Azure Portal):

- De client-ID (een teken reeks die een GUID vertegenwoordigt)
- De URL van de identiteits provider (de naam van het exemplaar) en de aanmeldings doel groep voor uw toepassing. Deze twee para meters worden gezamenlijk bekend als de-instantie.
- De Tenant-ID als u alleen een line-of-Business-toepassing schrijft voor uw organisatie (ook wel een toepassing met één Tenant genoemd).
- Voor web-apps en soms voor open bare client-apps (met name wanneer uw app een Broker moet gebruiken), moet u ook de redirectUri instellen waar de ID-provider verbinding maakt met uw toepassing met de beveiligings tokens.


Een .NET core-console toepassing kan het volgende bestand *appSettings. json* configureren:

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

Met de volgende code wordt dit bestand gelezen met behulp van het .NET-configuratie raamwerk:

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

Met de volgende code wordt uw toepassing gemaakt met behulp van de configuratie uit het instellingen bestand:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

