---
title: Computer instellen voor Media Services ontwikkeling met .NET
description: Meer informatie over de vereisten voor Media Services met behulp van de Media Services SDK voor .NET. Leer ook hoe u een Visual Studio-app maakt.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "64724113"
---
# <a name="media-services-development-with-net"></a>Media Services ontwikkeling met .NET 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

In dit artikel wordt beschreven hoe u Media Services toepassingen gaat ontwikkelen met .NET.

Met de **Azure Media Services .NET SDK** -bibliotheek kunt u Media Services met behulp van .net. Om het nog gemakkelijker te maken om te ontwikkelen met .NET, wordt de **Azure Media Services .NET SDK-extensie** bibliotheek gegeven. Deze bibliotheek bevat een set uitbreidings methoden en hulp functies die uw .NET-code vereenvoudigen. Beide bibliotheken zijn beschikbaar via **NuGet** en **github**.

## <a name="prerequisites"></a>Vereisten
* Een Media Services-account in een nieuw of bestaand Azure-abonnement. Zie het artikel [een Media Services-account maken voor meer informatie](media-services-portal-create-account.md).
* Besturings systemen: Windows 10, Windows 7, Windows 2008 R2 of Windows 8.
* .NET Framework 4,5 of hoger.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.
In deze sectie wordt beschreven hoe u een project maakt in Visual Studio en hoe u dit instelt voor Media Services ontwikkeling.  In dit geval is het project een C# Windows-console toepassing, maar dezelfde installatie stappen die hier worden weer gegeven, zijn van toepassing op andere soorten projecten die u kunt maken voor Media Services toepassingen (bijvoorbeeld een Windows Forms toepassing of een ASP.NET-webtoepassing).

In deze sectie wordt uitgelegd hoe u **NuGet** kunt gebruiken om Media Services .NET SDK-extensies en andere afhankelijke bibliotheken toe te voegen.

U kunt ook de nieuwste Media Services .NET SDK-bits verkrijgen via GitHub ([github.com/azure/Azure-SDK-for-Media-Services](https://github.com/Azure/azure-sdk-for-media-services) of [github.com/azure/Azure-SDK-for-Media-Services-Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), de oplossing bouwen en de verwijzingen toevoegen aan het client project. Alle benodigde afhankelijkheden worden automatisch gedownload en opgehaald.

1. Maak in Visual Studio een nieuwe C#-consoletoepassing. Voer de **naam**, **locatie**en naam van de **oplossing**in en klik vervolgens op OK.
2. Bouw de oplossing.
3. Gebruik **NuGet** om **Azure Media Services .NET SDK-extensies** (**windowsazure. Media Services. Extensions**) te installeren en toe te voegen. Als u dit pakket installeert, wordt ook de **Media Services .NET SDK** geïnstalleerd en worden alle andere vereiste afhankelijkheden toegevoegd.
   
    Zorg ervoor dat de nieuwste versie van NuGet is geïnstalleerd. Zie [NuGet](https://nuget.codeplex.com/)voor meer informatie en installatie-instructies.

    1. Klik in Solution Explorer met de rechter muisknop op de naam van het project en kies **NuGet-pakketten beheren**.

    2. Het dialoogvenster NuGet-pakketten beheren wordt weergegeven.

    3. Zoek in de online galerie naar Azure Media Services-extensies, kies **Azure Media Services .NET SDK-extensies** (**windowsazure. Media Services. Extensions**) en klik vervolgens op de knop **installeren** .
   
    4. Het project wordt gewijzigd en er wordt verwezen naar de Media Services .NET SDK-extensies, Media Services .NET SDK en andere afhankelijke assembly's worden toegevoegd.
4. Als u een schone ontwikkel omgeving wilt promoten, kunt u het NuGet-pakket herstellen inschakelen. Zie [NuGet package Restore](https://docs.nuget.org/consume/package-restore)voor meer informatie.
5. Voeg een verwijzing naar de **System. Configuration** -assembly toe. Deze assembly bevat de System. Configuration. Klasse **ConfigurationManager** die wordt gebruikt voor toegang tot configuratie bestanden (bijvoorbeeld app. config).
   
    1. Als u referenties wilt toevoegen met behulp van het dialoog venster verwijzingen beheren, klikt u met de rechter muisknop op de naam van het project in de Solution Explorer. Klik vervolgens op **toevoegen**en vervolgens op **verwijzing...**.
   
    2. Het dialoog venster referenties beheren wordt weer gegeven.
    3. Zoek en selecteer onder .NET Framework-assembly's de System. Configuration-assembly en druk op **OK**.
6. Open het bestand app. config en voeg de sectie **appSettings** aan het bestand toe. Stel de waarden in die nodig zijn om verbinding te maken met de Media Services-API. Zie [toegang tot de Azure Media Services-API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie. 

    Stel de waarden in die nodig zijn om verbinding te maken met behulp van de **Service-Principal** -verificatie methode.

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

7. Voeg de verwijzing **System. Configuration** toe aan uw project.
8. Overschrijf de bestaande **using** -instructies aan het begin van het Program.CS-bestand met de volgende code:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    U bent nu klaar om te beginnen met het ontwikkelen van een Media Services-toepassing.    

## <a name="example"></a>Voorbeeld

Hier volgt een klein voor beeld dat verbinding maakt met de AMS-API en alle beschik bare media Processors vermeldt.

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

[U kunt nu verbinding maken met de AMS-API en de](media-services-use-aad-auth-to-access-ams-api.md) [ontwikkeling](media-services-dotnet-get-started.md)starten.


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

