---
title: Activa en gerelateerde entiteiten beheren met Media Services .NET SDK
description: Meer informatie over het beheren van assets en gerelateerde entiteiten met de Media Services SDK voor .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: a686465b0006c2e9aac6e06cb4ab12d30921e8c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251139"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Activa en gerelateerde entiteiten beheren met Media Services .NET SDK
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [Rest](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

In dit onderwerp wordt uitgelegd hoe u azure mediaservices-entiteiten beheert met .NET.

Vanaf 1 april 2017 wordt elke taakrecord in uw account die ouder is dan 90 dagen, automatisch verwijderd, samen met de bijbehorende onderliggende taakrecords, zelfs als het totale aantal records lager is dan het maximale quotum. Op 1 april 2017 wordt bijvoorbeeld elke vacature in uw account ouder dan 31 december 2016 automatisch verwijderd. Als u de taak-/taakgegevens wilt archiveren, u de in dit onderwerp beschreven code gebruiken.

## <a name="prerequisites"></a>Vereisten

Stel uw ontwikkelomgeving in en vul het app.config-bestand in met verbindingsgegevens, zoals beschreven in [de ontwikkeling van Media Services met .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Een assetreferentie
Een veel voorkomende taak is om een verwijzing naar een bestaand item in Media Services te krijgen. In het volgende codevoorbeeld ziet u hoe u een assetreferentie krijgen van de assetverzameling op het servercontextobject, op basis van een asset-id. In het volgende codevoorbeeld wordt een Linq-query gebruikt om een verwijzing naar een bestaand IAsset-object te krijgen.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>Alle activa weergeven
Aangezien het aantal activa dat u in opslag hebt groeit, is het nuttig om uw activa op te sommen. In het volgende codevoorbeeld ziet u hoe u de verzameling Assets op het object servercontext herhalen. Bij elk element schrijft het codevoorbeeld ook een aantal van de eigendomswaarden aan de console. Elk item kan bijvoorbeeld veel mediabestanden bevatten. In het codevoorbeeld worden alle bestanden geschreven die aan elk item zijn gekoppeld.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>Een taakreferentie ophalen

Wanneer u werkt met verwerkingstaken in Media Services-code, moet u vaak een verwijzing naar een bestaande taak op basis van een id krijgen. In het volgende codevoorbeeld ziet u hoe u een verwijzing naar een IJob-object uit de verzameling Vacatures ophalen.

Mogelijk moet u een taakreferentie krijgen bij het starten van een langlopende coderingstaak en moet u de taakstatus op een thread controleren. In dergelijke gevallen moet u een vernieuwde verwijzing naar een taak ophalen wanneer de methode terugkeert van een thread.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>Vacatures en activa weergeven
Een belangrijke gerelateerde taak is het vermelden van assets met hun bijbehorende taak in Media Services. In het volgende codevoorbeeld ziet u hoe u elk IJob-object weergeven en vervolgens wordt voor elke taak eigenschappen weergegeven over de taak, alle gerelateerde taken, alle invoerelementen en alle uitvoerelementen. De code in dit voorbeeld kan nuttig zijn voor tal van andere taken. Als u bijvoorbeeld de uitvoerelementen wilt weergeven van een of meer coderingstaken die u eerder hebt uitgevoerd, geeft deze code aan hoe u toegang krijgt tot de uitvoerelementen. Wanneer u een verwijzing hebt naar een uitvoeritem, u de inhoud vervolgens aan andere gebruikers of toepassingen leveren door deze te downloaden of URL's aan te bieden. 

Zie [Assets leveren bij de Media Services SDK voor .NET voor](media-services-deliver-streaming-content.md)meer informatie over opties voor het leveren van assets.

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>Alle toegangsbeleidsregels weergeven
In Media Services u een toegangsbeleid definiëren voor een asset of zijn bestanden. Een toegangsbeleid definieert de machtigingen voor een bestand of een actief (welk type toegang en de duur). In uw Media Services-code definieert u doorgaans een toegangsbeleid door een IAccessPolicy-object te maken en vervolgens te koppelen aan een bestaand item. Vervolgens maakt u een ILocator-object, waarmee u directe toegang bieden tot assets in Media Services. Het Visual Studio-project dat bij deze documentatiereeks hoort, bevat verschillende codevoorbeelden die laten zien hoe u toegangsbeleid en locators maken en toewijzen aan assets.

In het volgende codevoorbeeld ziet u hoe u alle toegangsbeleidsregels op de server weergeven en het type machtigingen dat aan elk van de machtigingen is gekoppeld. Een andere handige manier om toegangsbeleid weer te geven, is door alle ILocator-objecten op de server weer te geven en vervolgens voor elke locator het bijbehorende toegangsbeleid weer te geven met behulp van de eigenschap AccessPolicy.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>Toegangsbeleid beperken 

>[!NOTE]
> Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). U moet dezelfde beleids-id gebruiken als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om gedurende een lange periode gehandhaafd te blijven (niet-upload-beleidsregels). 

U bijvoorbeeld een algemene set beleidsregels maken met de volgende code die slechts één keer in uw toepassing wordt uitgevoerd. U i-d's aanmelden bij een logboekbestand voor later gebruik:

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

Vervolgens u de bestaande iD's in uw code als volgt gebruiken:

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>Alle locators weergeven
Een locator is een URL die een direct pad biedt om toegang te krijgen tot een actief, samen met machtigingen voor het item zoals gedefinieerd door het bijbehorende toegangsbeleid van de locator. Elk item kan een verzameling ILocator-objecten bevatten die eraan zijn gekoppeld op de eigenschap Locators. De servercontext heeft ook een Locators-verzameling die alle locators bevat.

In het volgende codevoorbeeld worden alle locators op de server weergegeven. Voor elke locator wordt de id voor het bijbehorende asset- en toegangsbeleid weergegeven. Het toont ook het type machtigingen, de vervaldatum en het volledige pad naar het item.

Houd er rekening mee dat een locatorpad naar een asset slechts een basis-URL is voor het item. Als u een direct pad wilt maken naar afzonderlijke bestanden waarnaar een gebruiker of toepassing kan bladeren, moet uw code het specifieke bestandspad toevoegen aan het zoekpad. Zie het onderwerp Assets leveren met [de Media Services SDK voor .NET voor](media-services-deliver-streaming-content.md)meer informatie over hoe u dit doen.

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>Opsommen door middel van grote collecties van entiteiten
Bij het opvragen van entiteiten is er een limiet van 1000 entiteiten die tegelijk worden geretourneerd omdat openbare REST v2 queryresultaten beperkt tot 1000 resultaten. U moet Overslaan en nemen gebruiken wanneer u grote verzamelingen entiteiten opsomt. 

De volgende functie lussen door alle taken in de meegeleverde Media Services Account. Media Services levert 1000 banen op in Jobs Collection. De functie maakt gebruik van Skip en Take om ervoor te zorgen dat alle taken worden opgesomd (voor het geval u meer dan 1000 vacatures in uw account hebt).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>Een actief verwijderen
In het volgende voorbeeld wordt een actief verwijderd.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>Een taak verwijderen
Als u een taak wilt verwijderen, moet u de status van de taak controleren zoals aangegeven in de eigenschap State. Taken die zijn voltooid of geannuleerd, kunnen worden verwijderd, terwijl taken die zich in bepaalde andere toestanden bevinden, zoals wachtrijen, geplande of verwerking, eerst moeten worden geannuleerd en vervolgens kunnen worden verwijderd.

In het volgende codevoorbeeld wordt een methode weergegeven voor het verwijderen van een taak door taakstatussen te controleren en vervolgens te verwijderen wanneer de status is voltooid of geannuleerd. Deze code is afhankelijk van de vorige sectie in dit onderwerp voor het verkrijgen van een verwijzing naar een taak: Een taakreferentie aanvragen.

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>Een toegangsbeleid verwijderen
In het volgende codevoorbeeld ziet u hoe u een verwijzing naar een toegangsbeleid op basis van een beleids-id krijgen en vervolgens het beleid verwijderen.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

