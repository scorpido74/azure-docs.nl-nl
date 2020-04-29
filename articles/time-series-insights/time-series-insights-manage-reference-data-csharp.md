---
title: Referentie gegevens beheren in GA omgevingen met C#-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het beheren van referentie gegevens voor uw GA-omgeving door het maken van een aangepaste toepassing die is geschreven in C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: f0ce0f7d90540274d24a7e0248e6f197b74033a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416971"
---
# <a name="manage-ga-reference-data-for-an-azure-time-series-insights-environment-using-c"></a>GA referentie gegevens voor een Azure Time Series Insights omgeving beheren met C #

In dit artikel wordt beschreven hoe u C#-, [MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)-en Azure Active Directory kunt combi neren om PROGRAMMATISCHe API-aanvragen naar de api van Azure Time Series Insights ga [Gegevensbeheer](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)te maken.

> [!TIP]
> GA C#-code voorbeelden weer [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)geven op.

## <a name="summary"></a>Samenvatting

De voorbeeld code hieronder bevat de volgende functies:

* Een toegangs Token ophalen met behulp van [MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) **PublicClientApplication**.
* Sequentiële CREATE-, READ-, UPDATE-en DELETE-bewerkingen voor de GA [Reference gegevensbeheer-API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api).
* Algemene antwoord codes, inclusief [veelvoorkomende fout codes](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api#validation-and-error-handling).
    
    De referentie Gegevensbeheer-API verwerkt elk item afzonderlijk en een fout met één item verhindert niet dat de anderen kunnen volt ooien. Als uw aanvraag bijvoorbeeld 100 items heeft en er een fout optreedt in een item, worden er 99 items geschreven en wordt er een geweigerd.

## <a name="prerequisites-and-setup"></a>Vereisten en installatie

Voer de volgende stappen uit voordat u de voorbeeld code compileert en uitvoert:

1. [Richt een GA Azure time series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started
) -omgeving in.

1. Maak in uw omgeving [een set met referentie gegevens](time-series-insights-add-reference-data-set.md) . Gebruik het volgende referentie gegevens schema:

   | Sleutelnaam | Type |
   | --- | --- |
   | uuid | Tekenreeks | 

1. Configureer uw Azure Time Series Insights-omgeving voor Azure Active Directory zoals beschreven in [verificatie en autorisatie](time-series-insights-authentication-and-authorization.md). Gebruiken `http://localhost:8080/` als de **omleidings-URI**.

1. Installeer de vereiste project afhankelijkheden.

1. Bewerk de voorbeeld code hieronder door elk **#PLACEHOLDER #** te vervangen door de juiste omgevings-id.

1. Voer `dotnet run` uit in de hoofdmap van uw project. Wanneer u hierom wordt gevraagd, gebruikt u uw gebruikers profiel om u aan te melden bij Azure. 

## <a name="project-dependencies"></a>Projectafhankelijkheden

Het is raadzaam om de nieuwste versie van Visual Studio en **NetCore. app**te gebruiken:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versie 16.4.2 +
* [NetCore. app](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) -versie 2.2.8

De voorbeeld code bevat twee vereiste afhankelijkheden:

* MSAL.NET [micro soft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client/) -4.7.1-pakket.
* [Newton soft. json](https://www.nuget.org/packages/Newtonsoft.Json) -12.0.3-pakket.

De pakketten toevoegen met behulp van [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 12.0.3`
* `dotnet add package Microsoft.Identity.Client --version 4.7.1`

Of

1. Een `csharp-tsi-msal-ga-sample.csproj` bestand declareren:

    ```XML
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <LangVersion>latest</LangVersion>
        <RootNamespace>csharp-tsi-msal-ga-sample</RootNamespace>
        <RunWorkingDirectory>$(MSBuildThisFileDirectory)</RunWorkingDirectory>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Identity.Client" Version="4.7.1.0" Culture="neutral" PublicKeyToken="0a613f4dd989e8ae" />
        <PackageReference Include="Newtonsoft.Json" Version="12.0.3" />
      </ItemGroup>
    </Project>
    ```
1. Voer vervolgens `dotnet restore` uit.

## <a name="c-sample-code"></a>C#-voorbeeld code

```csharp
// Copyright (c) Microsoft Corporation.  All rights reserved.

namespace CsharpTsiMsalGaSample
{
    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    public static class Program
    {
        /**
         * Review the product documentation for detailed configuration steps or skip ahead and configure your environment settings.
         * 
         * https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization
         */

        // Azure Time Series Insights environment configuration
        internal static string EnvironmentFqdn = "#PLACEHOLDER#.env.timeseries.azure.com";
        internal static string EnvironmentReferenceDataSetName = "#PLACEHOLDER#";

        // Azure Active Directory application configuration
        internal static string AadClientApplicationId = "#PLACEHOLDER#";
        internal static string[] AadScopes = new string[] { "https://api.timeseries.azure.com//user_impersonation" };
        internal static string AadRedirectUri = "http://localhost:8080/";
        internal static string AadTenantName = "#PLACEHOLDER#";
        internal static string AadAuthenticationAuthority = "https://login.microsoftonline.com/" + AadTenantName + ".onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/";

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (AadClientApplicationId == "#PLACEHOLDER#" || AadScopes.Length == 0 || AadRedirectUri == "#PLACEHOLDER#" || AadTenantName.StartsWith("#PLACEHOLDER#"))
            {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started"} to update the values of 'AadClientApplicationId', 'AadScopes', 'AadRedirectUri', and 'AadAuthenticationAuthority'.");
            }

            /**
             * MSAL.NET configuration. Review the product documentation for more information about MSAL.NET authentication options.
             * 
             * https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/
             */

            IPublicClientApplication app = PublicClientApplicationBuilder
                .Create(AadClientApplicationId)
                .WithRedirectUri(AadRedirectUri)
                .WithAuthority(AadAuthenticationAuthority)
                .Build();

            AuthenticationResult result = await app
                .AcquireTokenInteractive(AadScopes)
                .ExecuteAsync();

            Console.WriteLine("MSAL Authentication Token Acquired: {0}", result.AccessToken);
            Console.WriteLine("");
            return result.AccessToken;
        }

        // System.Net.HttpClient helper to wrap HTTP POST made to the GA Reference Data API
        private static async Task<HttpResponseMessage> AsyncHttpPostRequestHelper(HttpClient httpClient, string input)
        {
             if (EnvironmentFqdn.StartsWith("#PLACEHOLDER#") || EnvironmentReferenceDataSetName == "#PLACEHOLDER#")
             {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization"} to update the values of 'EnvironmentFqdn' and 'EnvironmentReferenceDataSetName'.");
             }

             Console.WriteLine("HTTP JSON Request Body: {0}", input);
             Console.WriteLine("");
             HttpContent requestBody = new StringContent(input, Encoding.UTF8, "application/json");

             Uri uri = new UriBuilder("https", EnvironmentFqdn)
             {
                Path = $"referencedatasets/{EnvironmentReferenceDataSetName}/$batch",
                Query = "api-version=2016-12-12"
             }.Uri;
                
             Console.WriteLine("Making HTTP POST to URI: {0}", uri);
             Console.WriteLine("");

             HttpResponseMessage response = await httpClient.PostAsync(uri, requestBody);
             if (response.IsSuccessStatusCode)
             {
                var jsonString = await response.Content.ReadAsStringAsync();
                var jsonStringTransferObject = JsonConvert.DeserializeObject<object>(jsonString);
                Console.WriteLine("HTTP JSON Response Body: {0}", jsonStringTransferObject);
                Console.WriteLine("");
                return response;
             }

             return null;
        }

        private static async Task TsiMsalGaSample()
        {
            Console.WriteLine("Beginning demo...");
            Console.WriteLine("");
            Console.WriteLine("The following samples assume a single Key Name (uuid) with String type...");
            Console.WriteLine("");

            string accessToken = await AcquireAccessTokenAsync();
            var httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + accessToken);

            {   
                // CREATE reference data
                Console.WriteLine("CREATE reference data example...");
                Console.WriteLine("");
                string createInput = @"
                    {
                        ""put"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""floor"": 2
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";


                var createResponse = await AsyncHttpPostRequestHelper(httpClient, createInput);

                // READ reference data
                Console.WriteLine("READ reference data example...");
                Console.WriteLine("");
                string readInput = @"
                    {
                        ""get"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""desc"": ""example""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";

                var readResponse = await AsyncHttpPostRequestHelper(httpClient, readInput);

                // UPDATE reference data
                Console.WriteLine("UPDATE reference data example...");
                Console.WriteLine("");
                string updateInput = @"
                    {
                        ""patch"": [
                            {
                                ""uuid"": ""Fan1"",
                                ""color"": ""Red""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""desc"": ""Example""
                            }
                        ]
                    }";

                var inputResponse = await AsyncHttpPostRequestHelper(httpClient, updateInput);

                // DELETE reference data
                Console.WriteLine("DELETE reference data example...");
                Console.WriteLine("");
                string deleteInput = @"
                    {
                        ""delete"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""desc"": ""Blue""
                            }
                        ]
                    }";

                var deleteResponse = await AsyncHttpPostRequestHelper(httpClient, deleteInput);
            }
        }

        internal static void Main(string[] args)
        {
            Task.Run(async () => await TsiMsalGaSample()).Wait();
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Lees de naslag documentatie over [referentie gegevensbeheer-API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) voor Ga naar.
