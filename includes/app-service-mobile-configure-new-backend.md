---
title: bestand opnemen
description: bestand opnemen
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 04/15/2020
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: b5b6389491fd91b716a05e7c57c98addb6352bd9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461842"
---
1. Download de SDK-client snelstart voor de volgende platforms:
    
    [iOS (Doelstelling-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Als u het iOS-project gebruikt, moet u\*"azuresdk-iOS- .zip" downloaden van [de nieuwste GitHub-release.](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest) Rits het bestand `MicrosoftAzureMobile.framework` uit en voeg het bestand toe aan de hoofdmap van het project.
    >

2. U moet een databaseverbinding toevoegen of verbinding maken met een bestaande verbinding. Bepaal eerst of u een gegevensarchief maakt of een bestaand archief gebruikt.

    - **Een nieuw gegevensarchief maken:** Als u een gegevensarchief gaat maken, gebruikt u de volgende snelstart:

        [Snelstart: aan de slag met afzonderlijke databases in Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Bestaande gegevensbron**: Volg de onderstaande instructies als u een bestaande databaseverbinding wilt gebruiken

        1. SQL Database Connection String-indeling -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** Naam van de server, dit kan worden gevonden in de overzichtspagina voor uw database en is meestal in de vorm van "server_name.database.windows.net".
            **{port}** gewoonlijk 1433.
            **{your_catalogue}** Naam van de database.
            **{your_username}** Gebruikersnaam om toegang te krijgen tot uw database.
            **{your_password}** Wachtwoord om toegang te krijgen tot uw database.

            [Meer informatie over SQL Connection String-indeling](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Voeg de verbindingstekenreeks toe aan uw **mobiele app** In App Service u verbindingstekenreeksen voor uw toepassing beheren met behulp van de optie **Configuratie** in het menu.

            Een verbindingstekenreeks toevoegen:

            1. Klik op het tabblad **Toepassingsinstellingen.**

            2. Klik op **[+] Nieuwe verbindingstekenreeks**.

            3. U moet **naam,** **waarde** en **type** opgeven voor uw verbindingstekenreeks.

            4. **Type naam** als`MS_TableConnectionString`

            5. Waarde moet de verbindingstekenreeks zijn die u in de stap ervoor hebt gevormd.

            6. Als u een verbindingstekenreeks toevoegt aan een SQL Azure-database, kiest u **SQLAzure** onder **type**.

3. Azure Mobile Apps heeft SDK's voor backends .NET en Node.js.

   - **Node.js backend**
    
     Als je de Quickstart-app van Node.js gaat gebruiken, volg je de onderstaande instructies.
     
        1. Een nieuwe API maken - U wijzigingen aanbrengen in de Azure-portal of de code lokaal wijzigen in uw ontwikkelomgeving en vervolgens publiceren naar Azure. Klik op `App Service Editor (Preview)` `Development Tools` het menu onder dat een bewerkingservaring in de browser biedt voor uw app-code.
        
        2. Klik `Go` op en zodra de App Service Editor wordt geopend, heb je volledige controle over de broncode. Ervan uitgaande dat u al een express- en azure-mobile-apps-pakket hebt geïnstalleerd met de opdracht npm-installatie, klikt u op de api-map onder WWWROOT om aangepaste API's te maken of te bewerken. Breng uw wijzigingen aan in het codebestand en de wijzigingen worden automatisch opgeslagen.
        
        3. U hebt volledige controle over de Azure SQL Database die wordt gebruikt om de toepassingsgegevens op te slaan. U eenvoudig nieuwe tabellen maken in de database.
 
   - **.NET-back-end**
    
        Als je de QuickStart-app van .NET gaat gebruiken, volg je de onderstaande instructies.

        1. Download het Azure Mobile Apps .NET-serverproject vanuit de [azure-mobile-apps-quickstarts repository](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Bouw het .NET-serverproject lokaal in Visual Studio.

        3. Open in Visual Studio Solution Explorer `ZUMOAPPNAMEService` met de rechtermuisknop op project, klik op **Publiceren**, u ziet een `Publish to App Service` venster. Als u aan Mac werkt, bekijkt u [hier](https://docs.microsoft.com/azure/app-service/deploy-local-git)andere manieren om de app te implementeren.
        
           ![Visual studio publishing](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Selecteer **App Service** als publicatiedoel en klik vervolgens op Bestaand **selecteren**en klik vervolgens op de knop **Publiceren** onder aan het venster.

        5. U moet zich eerst aanmelden bij Visual Studio met uw Azure-abonnement. Selecteer `Subscription`de `Resource Group`, en selecteer vervolgens de naam van uw app. Wanneer u klaar bent, klikt u op **OK,** hiermee wordt het .NET-serverproject dat u lokaal hebt geïmplementeerd in de backend van de appservice. Wanneer de implementatie is voltooid, `http://{zumoappname}.azurewebsites.net/` wordt u doorgestuurd naar in de browser.                   
