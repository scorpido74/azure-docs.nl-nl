---
title: Computer instellen voor mediaservicesontwikkeling met .NET
description: Meer informatie over de vereisten voor Media Services die de Media Services SDK voor .NET gebruiken. Lees ook hoe u een Visual Studio-app maakt.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 51fffbd170daecfec6fcea95caa0526e6d881407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64724113"
---
# <a name="media-services-development-with-net"></a>Media Services ontwikkeling met .NET 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

In dit artikel wordt besproken hoe u mediaservices-toepassingen ontwikkelen met behulp van .NET.

Met de **Azure Media Services .NET SDK-bibliotheek** u programmeren tegen Media Services met behulp van .NET. Om het nog eenvoudiger te maken om te ontwikkelen met .NET, wordt de **Azure Media Services .NET SDK Extensions-bibliotheek** aangeboden. Deze bibliotheek bevat een reeks extensiemethoden en helperfuncties die uw .NET-code vereenvoudigen. Beide bibliotheken zijn beschikbaar via **NuGet** en **GitHub.**

## <a name="prerequisites"></a>Vereisten
* Een Media Services-account in een nieuw of bestaand Azure-abonnement. Zie het artikel [Hoe maak je een Media Services-account](media-services-portal-create-account.md)aan .
* Besturingssystemen: Windows 10, Windows 7, Windows 2008 R2 of Windows 8.
* .NET Framework 4.5 of hoger.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.
In deze sectie ziet u hoe u een project maakt in Visual Studio en het instelt voor de ontwikkeling van Media Services.  In dit geval is het project een C#Windows-consoletoepassing, maar dezelfde installatiestappen die hier worden weergegeven, zijn van toepassing op andere typen projecten die u maken voor Media Services-toepassingen (bijvoorbeeld een Windows Forms-toepassing of een ASP.NET-toepassing).

In deze sectie ziet u hoe **u NuGet** gebruikt om Media Services .NET SDK-extensies en andere afhankelijke bibliotheken toe te voegen.

U ook de nieuwste Media Services .NET SDK-bits van GitHub[(github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) of [github.com/Azure/azure-sdk-for-media-services-extensions)](https://github.com/Azure/azure-sdk-for-media-services-extensions)krijgen, de oplossing bouwen en de verwijzingen naar het clientproject toevoegen. Alle benodigde afhankelijkheden worden automatisch gedownload en geëxtraheerd.

1. Maak in Visual Studio een nieuwe C#-consoletoepassing. Voer de **naam Naam**, **Locatie**en **Oplossing**in en klik op OK.
2. Bouw de oplossing.
3. Gebruik **NuGet** om **Azure Media Services .NET SDK Extensions** **(windowsazure.mediaservices.extensions)** te installeren en toe te voegen. Als u dit pakket installeert, wordt ook de **Media Services .NET SDK** geïnstalleerd en worden alle andere vereiste afhankelijkheden toegevoegd.
   
    Zorg ervoor dat je de nieuwste versie van NuGet hebt geïnstalleerd. Zie [NuGet voor](https://nuget.codeplex.com/)meer informatie en installatie-instructies.

    1. Klik in Solution Explorer met de rechtermuisknop op de naam van het project en kies **NuGet-pakketten beheren.**

    2. Het dialoogvenster NuGet-pakketten beheren wordt weergegeven.

    3. Zoek in de galerie Online naar Azure MediaServices-extensies, kies **Azure Media Services .NET SDK-extensies** **(windowsazure.mediaservices.extensions)** en klik vervolgens op de knop **Installeren.**
   
    4. Het project wordt gewijzigd en verwijzingen naar de Media Services .NET SDK Extensions, Media Services .NET SDK en andere afhankelijke samenstellingen worden toegevoegd.
4. Om een schonere ontwikkelingsomgeving te bevorderen, u nuGet Package Restore inschakelen. Zie [NuGet Package Restore voor](https://docs.nuget.org/consume/package-restore)meer informatie" .
5. Voeg een verwijzing toe naar **System.Configuration** assembly. Deze assemblage bevat de System.Configuration. **ConfigurationManager-klasse** die wordt gebruikt om toegang te krijgen tot configuratiebestanden (bijvoorbeeld App.config).
   
    1. Als u verwijzingen wilt toevoegen met het dialoogvenster Verwijzingen beheren, klikt u met de rechtermuisknop op de projectnaam in de Oplossingsverkenner. Klik vervolgens op **Toevoegen**en klik vervolgens op **Referentie...**.
   
    2. Het dialoogvenster Verwijzingen beheren wordt weergegeven.
    3. Zoek en selecteer onder .NET framework-samenstellingen de system.configuration assembly en druk op **OK**.
6. Open het app.config-bestand en voeg een sectie **appInstellingen** toe aan het bestand. Stel de waarden in die nodig zijn om verbinding te maken met de Media Services API. Zie [Toegang tot de Azure Media Services API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie. 

    Stel de waarden in die nodig zijn om verbinding te maken met de **verificatiemethode Service principal.**

        ```csharp
                <configuration>
                ...
                    <appSettings>
                        <add key="AMSAADTenantDomain" value="tenant"/>
                        <add key="AMSRESTAPIEndpoint" value="endpoint"/>
                        <add key="AMSClientId" value="id"/>
                        <add key="AMSClientSecret" value="secret"/>
                    </appSettings>
                </configuration>
        ```

7. Voeg de referentie **System.Configuration** toe aan uw project.
8. Overschrijven van de bestaande **met behulp van** instructies aan het begin van de Program.cs bestand met de volgende code:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    Op dit moment bent u klaar om te beginnen met het ontwikkelen van een Media Services applicatie.    

## <a name="example"></a>Voorbeeld

Hier is een klein voorbeeld dat verbinding maakt met de AMS API en alle beschikbare mediaprocessors weergeeft.

```csharp
        class Program
        {
            // Read values from the App.config file.

            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];
        
            private static CloudMediaContext _context = null;
            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
                // List all available Media Processors
                foreach (var mp in _context.MediaProcessors)
                {
                    Console.WriteLine(mp.Name);
                }
        
            }
 ```

## <a name="next-steps"></a>Volgende stappen

Nu [kunt u verbinding maken met de AMS API](media-services-use-aad-auth-to-access-ams-api.md) en beginnen met het [ontwikkelen.](media-services-dotnet-get-started.md)


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

