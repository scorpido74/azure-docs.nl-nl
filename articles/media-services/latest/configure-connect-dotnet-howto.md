---
title: Verbinding maken met Azure Media Services v3 API - .NET
description: In dit artikel wordt uitgelegd hoe u verbinding maken met Media Services v3 API met .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: b8f4de1a5b9d8216ae2442631f5f9135c3c72d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269807"
---
# <a name="connect-to-media-services-v3-api---net"></a>Verbinding maken met Media Services v3 API - .NET

In dit artikel ziet u hoe u verbinding maken met de Azure Media Services v3 .NET SDK met behulp van de serviceprincipal-inlogmethode.

## <a name="prerequisites"></a>Vereisten

- [Een Azure Media Services-account maken](create-account-cli-how-to.md). Zorg ervoor dat u de naam van de brongroep en de naam van het Media Services-account onthoudt
- Installeer een tool die u wilt gebruiken voor .NET-ontwikkeling. De stappen in dit artikel laten zien hoe je [Visual Studio 2019 Community Edition kunt](https://www.visualstudio.com/downloads/)gebruiken. U Visual Studio Code gebruiken, zie [Werken met C#](https://code.visualstudio.com/docs/languages/csharp). U ook een andere codeeditor gebruiken.

> [!IMPORTANT]
> [Naamgevingsconventies bekijken](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Een consoletoepassing maken

1. Start Visual Studio. 
1. Klik **in** het menu Bestand op **Nieuw** > **project**. 
1. Maak een **.NET Core-consoletoepassing.**

De voorbeeld-app in `netcoreapp2.0`dit onderwerp, doelen . De code maakt gebruik van 'async main', die beschikbaar is vanaf C# 7.1. Zie deze [blog](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) voor meer details.

## <a name="add-required-nuget-packages"></a>Benodigde NuGet-pakketten toevoegen

1. Selecteer in Visual Studio de optie **Tools** > **NuGet Package Manager** > **NuGet Manager Console**.
2. Gebruik **Package Manager Console** de opdracht om `Install-Package` in het venster Package Manager Console de volgende NuGet-pakketten toe te voegen. Bijvoorbeeld `Install-Package Microsoft.Azure.Management.Media`.

|Pakket|Beschrijving|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Ga naar [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)om ervoor te zorgen dat u het nieuwste Azure Media Services-pakket gebruikt.|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|ADAL-verificatiebibliotheek voor Azure SDK voor NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Configuratiewaarden lezen uit omgevingsvariabelen en lokale JSON-bestanden|
|`Microsoft.Extensions.Configuration.Json`|Configuratiewaarden lezen uit omgevingsvariabelen en lokale JSON-bestanden
|`WindowsAzure.Storage`|Opslag SDK|

## <a name="create-and-configure-the-app-settings-file"></a>Het app-instellingenbestand maken en configureren

### <a name="create-appsettingsjson"></a>Appsettings.json maken

1. Ga naar **algemeen** > **tekstbestand**.
1. Noem het "appsettings.json".
1. Stel de eigenschap 'Kopiëren naar uitvoermap' van het .json-bestand in op 'Kopiëren als nieuwer' (zodat de toepassing er toegang toe heeft wanneer deze wordt gepubliceerd).

### <a name="set-values-in-appsettingsjson"></a>Waarden instellen in appsettings.json

Voer `az ams account sp create` de opdracht uit zoals beschreven in [toegangs-API's](access-api-cli-how-to.md). De opdracht retourneert json die u moet kopiëren naar uw "appsettings.json".
 
## <a name="add-configuration-file"></a>Een configuratiebestand toevoegen

Voeg voor het gemak een configuratiebestand toe dat verantwoordelijk is voor het lezen van waarden van "appsettings.json".

1. Voeg een nieuwe .cs-klasse toe aan uw project. Noem deze `ConfigWrapper`. 
1. Plak de volgende code in dit bestand (in dit `ConsoleApp1`voorbeeld wordt ervan uitgegaan dat u de naamruimte hebt).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Verbinding maken met de .NET-client

Als u wilt starten met Media Services API's met .NET, moet u een **AzureMediaServicesClient**-object maken. Als u het object wilt maken, moet u referenties opgeven die de client nodig heeft om verbinding te maken met Azure met behulp van Microsoft Azure Active Directory. In de onderstaande code maakt de functie GetCredentialsAsync het object ServiceClientCredentials op basis van de referenties die zijn opgegeven in het lokale configuratiebestand.

1. Open `Program.cs`.
1. Plak de volgende code:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Video's uploaden, coderen en streamen - .NET](stream-files-tutorial-with-api.md) 
- [Zelfstudie: Live streamen met Media Services v3 - .NET](stream-live-tutorial-with-api.md)
- [Zelfstudie: Video's analyseren met Media Services v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Een taakinvoer maken vanuit een lokaal bestand - .NET](job-input-from-local-file-how-to.md)
- [Een taakinvoer maken vanuit een HTTPS-URL - .NET](job-input-from-http-how-to.md)
- [Coderen met een aangepaste transformatie - .NET](customize-encoder-presets-how-to.md)
- [Dynamische AES-128-versleuteling en de sleutelleveringsservice gebruiken - .NET](protect-with-aes128.md)
- [Dynamische DRM-versleuteling en licentielevering gebruiken - .NET](protect-with-drm.md)
- [Een ondertekeningssleutel ophalen uit het bestaand beleid - .NET](get-content-key-policy-dotnet-howto.md)
- [Filters maken met Media Services - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Voorbeelden van geavanceerde video on demand van Azure Functions-v2 met Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Zie ook

* [Naslaginformatie over .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* Zie de repo [.NET SDK-voorbeelden](https://github.com/Azure-Samples/media-services-v3-dotnet) voor meer codevoorbeelden.
