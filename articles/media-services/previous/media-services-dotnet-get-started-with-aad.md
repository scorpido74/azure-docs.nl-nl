---
title: Azure AD-verificatie gebruiken om toegang te krijgen tot azure mediaservices-API met .NET | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u Azure Active Directory-verificatie (Azure AD) gebruiken om toegang te krijgen tot de AMS-API (Azure Media Services) met .NET.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b53fca292630ef988ee1357ea50adc4d7b7e9be5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162876"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Azure AD-verificatie gebruiken om toegang te krijgen tot azure media services-api met .NET

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Vanaf windowsazure.mediaservices 4.0.0.4 ondersteunt Azure Media Services verificatie op basis van Azure Active Directory (Azure AD). In dit onderwerp ziet u hoe u Azure AD-verificatie gebruikt om toegang te krijgen tot azure media services-api met Microsoft .NET.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Zie gratis [proefversie van Azure voor](https://azure.microsoft.com/pricing/free-trial/)meer informatie. 
- Een Media Services-account. Zie [Een Azure Media Services-account maken met de Azure-portal](media-services-portal-create-account.md)voor meer informatie.
- Het nieuwste [NuGet-pakket.](https://www.nuget.org/packages/windowsazure.mediaservices)
- Vertrouwdheid met het onderwerp [Toegang tot Azure Media Services API met Azure AD-verificatieoverzicht](media-services-use-aad-auth-to-access-ams-api.md). 

Wanneer u Azure AD-verificatie gebruikt met Azure Media Services, u op twee manieren verifiëren:

- **Gebruikersverificatie** verifieert een persoon die de app gebruikt om te communiceren met Azure Media Services-bronnen. De interactieve toepassing moet eerst vragen aan de gebruiker om referenties. Een voorbeeld is een beheerconsole-app die wordt gebruikt door geautoriseerde gebruikers om coderingstaken of live streaming te controleren. 
- **Serviceprincipal-verificatie** verifieert een service. Toepassingen die deze verificatiemethode vaak gebruiken, zijn apps die daemonservices, services op het middensegment of geplande taken uitvoeren, zoals web-apps, functie-apps, logische apps, API's of microservices.

>[!IMPORTANT]
>Azure Media Service ondersteunt momenteel een Azure Access Control Service-verificatiemodel. De toegangscontroleautorisatie wordt echter afgeschaft op 22 juni 2018. We raden u aan zo snel mogelijk te migreren naar een Azure Active Directory-verificatiemodel.

## <a name="get-an-azure-ad-access-token"></a>Een Azure AD-toegangstoken ophalen

Als u verbinding wilt maken met de Azure Media Services API met Azure AD-verificatie, moet de client-app een Azure AD-toegangstoken aanvragen. Wanneer u de SDK van de Media Services .NET-client gebruikt, worden veel van de details over het aanschaffen van een Azure AD-toegangstoken voor u verpakt en vereenvoudigd in de klassen [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) en [AzureAdTokenCredentials.](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) 

U hoeft bijvoorbeeld geen azure-ad-autoriteit, Media Services-bron URI of native Azure AD-toepassingsgegevens op te geven. Dit zijn bekende waarden die al zijn geconfigureerd door de klasse Azure AD-toegangstokenprovider. 

Als u azure media service .NET SDK niet gebruikt, raden we u aan de [Azure AD-verificatiebibliotheek](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)te gebruiken. Zie De Azure-portal gebruiken om toegang te krijgen [tot Azure AD-verificatieinstellingen](media-services-portal-get-started-with-aad.md)als u waarden wilt krijgen voor de parameters die u moet gebruiken met Azure AD-verificatiebibliotheek.

U hebt ook de mogelijkheid om de standaardimplementatie van de **AzureAdTokenProvider** te vervangen door uw eigen implementatie.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Azure Media Services .NET SDK installeren en configureren

>[!NOTE] 
>Als u Azure AD-verificatie wilt gebruiken met de Media Services .NET SDK, moet u over het nieuwste [NuGet-pakket](https://www.nuget.org/packages/windowsazure.mediaservices) beschikken. Voeg ook een verwijzing toe naar de **microsoft.identitymodel.clients.ActiveDirectory-assemblage.** Als u een bestaande app gebruikt, neemt u de assemblage **van Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** toe. 

1. Maak in Visual Studio een nieuwe C#-consoletoepassing.
2. Gebruik het [NuGet-pakket windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) om **Azure Media Services .NET SDK**te installeren. 

    Als u verwijzingen wilt toevoegen met NuGet, neemt u de volgende stappen: klik in **Solution Explorer**met de rechtermuisknop op de projectnaam en selecteer **NuGet-pakketten beheren.** Zoek vervolgens naar **windowsazure.mediaservices** en selecteer **Installeren**.
    
    -of-

    Voer de volgende opdracht uit in **Package Manager Console** in Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Voeg **het gebruik** toe aan uw broncode.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Gebruikersverificatie gebruiken

Als u verbinding wilt maken met de Azure Media Service API met de optie voor gebruikersverificatie, moet de client-app een Azure AD-token aanvragen met behulp van de volgende parameters:  

- Eindpunt azure AD-tenant. De tenantgegevens kunnen worden opgehaald uit de Azure-portal. Plaats de plaats boven de aangemelde gebruiker in de rechterbovenhoek.
- Media Services resource URI.
- Media Services (native) application client ID. 
- Media Services (native) applicatie redirect URI. 

De waarden voor deze parameters zijn te vinden in **AzureEnvironments.AzureCloudEnvironment**. De **azureenvironments.AzureCloudEnvironment** constant is een helper in de .NET SDK om de juiste omgevingsvariabele instellingen te krijgen voor een openbaar Azure Data Center. 

Het bevat vooraf gedefinieerde omgevingsinstellingen voor alleen toegang tot Media Services in openbare datacenters. Voor soevereine of overheidscloudregio's u respectievelijk **AzureChinaCloudEnvironment,** **AzureUsGovernmentEnvironment**of **AzureGermanCloudEnvironment** gebruiken.

In het volgende codevoorbeeld wordt een token getekend:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Als u wilt beginnen met programmeren tegen Media Services, moet u een **CloudMediaContext-exemplaar** maken dat de servercontext vertegenwoordigt. De **CloudMediaContext** bevat verwijzingen naar belangrijke verzamelingen, waaronder taken, assets, bestanden, toegangsbeleid en locators. 

U moet ook de **resource URI voor Media REST Services** doorgeven aan de **CloudMediaContext** constructor. Als u de bron URI voor Media REST Services wilt krijgen, meldt u zich aan bij de Azure-portal, selecteert u uw Azure Media Services-account, selecteert u **API-toegang**en selecteert u **Verbinding maken met Azure Media Services met gebruikersverificatie**. 

In het volgende codevoorbeeld wordt een **CloudMediaContext-exemplaar** gemaakt:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

In het volgende voorbeeld ziet u hoe u het Azure AD-token en de context maakt:

    namespace AzureADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }
    
        }
    }

>[!NOTE]
>Als u een uitzondering krijgt met de tekst 'De externe server heeft een fout geretourneerd: (401) Onbevoegd", raadpleegt u het gedeelte [Toegangsbeheer](media-services-use-aad-auth-to-access-ams-api.md#access-control) van de Accessing Azure Media Services API met Azure AD-verificatieoverzicht.

## <a name="use-service-principal-authentication"></a>Serviceprincipal-verificatie gebruiken
    
Als u verbinding wilt maken met de Azure Media Services API met de serviceprincipaloptie, moet uw middelste-tier-app (web-API of webtoepassing) een Azure AD-token met de volgende parameters aanvragen:  

- Eindpunt azure AD-tenant. De tenantgegevens kunnen worden opgehaald uit de Azure-portal. Plaats de plaats boven de aangemelde gebruiker in de rechterbovenhoek.
- Media Services resource URI.
- Azure AD-toepassingswaarden: de **client-id** en **het clientgeheim**.

De waarden voor de geheime **parameters client-id** en **client** zijn te vinden in de Azure-portal. Zie [Aan de slag met Azure AD-verificatie met de Azure-portal](media-services-portal-get-started-with-aad.md)voor meer informatie.

In het volgende codevoorbeeld wordt een token gemaakt met behulp van de **AzureAdTokenCredentials** constructor die **AzureAdClientSymmetricKey** als parameter neemt: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

U ook de **AzureAdTokenCredentials-constructor** opgeven die **AzureAdClientCertificate** als parameter neemt. 

Zie [Authenticeren naar Azure AD in daemon-apps met certificaten - handmatige configuratiestappen](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md)voor instructies over het maken en configureren van een certificaat in een formulier dat door Azure AD kan worden gebruikt.

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Als u wilt beginnen met programmeren tegen Media Services, moet u een **CloudMediaContext-exemplaar** maken dat de servercontext vertegenwoordigt. U moet ook de **resource URI voor Media REST Services** doorgeven aan de **CloudMediaContext** constructor. U de **waarde van de resource URI voor Media REST Services** ook van de Azure-portal halen.

In het volgende codevoorbeeld wordt een **CloudMediaContext-exemplaar** gemaakt:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
In het volgende voorbeeld ziet u hoe u het Azure AD-token en de context maakt:

    namespace AzureADAuthSample
    {
    
        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureCloudEnvironment);
            
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
    
                Console.ReadLine();
            }
    
        }
    }

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [het uploaden van bestanden naar je account.](media-services-dotnet-upload-files.md)
