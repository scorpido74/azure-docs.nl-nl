---
title: Gebruik .NET SDK voor Microsoft Azure StorSimple Data Manager-taken
description: Meer informatie over het gebruik van .NET SDK om StorSimple Data Manager-taken te starten
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b7cf1d3b9d4a9d751348c4792f904062b00ac104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76270730"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>De .NET SDK gebruiken om gegevenstransformatie te starten

## <a name="overview"></a>Overzicht

In dit artikel wordt uitgelegd hoe u de functie gegevenstransformatie binnen de StorSimple Data Manager-service gebruiken om StorSimple-apparaatgegevens te transformeren. De getransformeerde gegevens worden vervolgens verbruikt door andere Azure-services in de cloud.

U een taak voor gegevenstransformatie op twee manieren starten:

- De .NET SDK gebruiken
- Azure Automation-runbook gebruiken
 
  In dit artikel wordt beschreven hoe u een voorbeeldtoepassing .NET-console maakt om een taak voor gegevenstransformatie te starten en deze vervolgens bij te houden voor voltooiing. Ga voor meer informatie over het initiëren van gegevenstransformatie via Automatisering naar [Azure Automation runbook gebruiken om taken voor gegevenstransformatie te activeren.](storsimple-data-manager-job-using-automation.md)

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u:
*   Een computer met draaien:

    - Visual Studio 2012, 2013, 2015 of 2017.

    - Azure Powershell. [Azure Powershell downloaden](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Een correct geconfigureerde taakdefinitie in StorSimple Data Manager binnen een resourcegroep.
*   Alle benodigde dlls. Download deze dlls uit de [GitHub repository.](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)script uit de GitHub repository.

## <a name="step-by-step-procedure"></a>Stapsgewijze procedure

Voer de volgende stappen uit om .NET te gebruiken om een taak voor gegevenstransformatie te starten.

1. Ga als volgt te werk om de configuratieparameters op te halen:
    1. Download `Get-ConfigurationParams.ps1` het script van de GitHub-repository op `C:\DataTransformation` locatie.
    1. Voer `Get-ConfigurationParams.ps1` het script uit vanuit de GitHub-repository. Typ de volgende opdracht:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        U alle waarden doorgeven voor de ActiveDirectoryKey en AppName.

2. Met dit script worden de volgende waarden uitgevoerd:
    * Client-id
    * Tenant-id
    * Active Directory-sleutel (hetzelfde als de bovenstaande)
    * Abonnements-id

        ![Scriptuitvoer van configuratieparameters](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Maak met Visual Studio 2012, 2013 of 2015 een C# .NET-consoletoepassing.

    1. **Start Visual Studio 2012/2013/2015**.
    1. Selecteer **Bestand > Nieuw > Project**.

        ![Een project maken 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Selecteer **Geïnstalleerde >-sjablonen > Visual C# > Console-toepassing**.
    3. Voer **DataTransformationApp** voor de **naam in.**
    4. Selecteer **C:\DataTransformation** voor de **locatie**.
    6. Klik op **OK** om het project te maken.

        ![Een project 2 maken](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Voeg nu alle dlls toe die aanwezig zijn in de [map dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) als **Verwijzingen** in het project dat u hebt gemaakt. Voer het volgende uit om de dll-bestanden toe te voegen:

   1. Ga in Visual Studio naar **View > Solution Explorer**.
   2. Klik op de pijl links van het project Data Transformation App. Klik **op Verwijzingen** en klik vervolgens met de rechtermuisknop op **Naslagwaarde**.
    
       ![Voeg dlls 1 toe](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Blader naar de locatie van de map pakketten, selecteer alle dlls en klik op **Toevoegen**en klik op **OK**.

       ![Voeg dlls 2 toe](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Voeg de volgende **using**-instructies toe aan het bronbestand (Program.cs) in het project.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Met de volgende code wordt de taakinstantie voor gegevenstransformatie geïnitialiseren. Voeg dit toe aan de **hoofdmethode**. Vervang de waarden van configuratieparameters zoals eerder verkregen. Sluit de waarden van **naam van resourcegroep** en **resourcenaam**aan . De **ResourceGroupName** is de gekoppeld aan de Gegevensbeheer StorSimple waarop de taakdefinitie is geconfigureerd. De **ResourceName** is de naam van uw StorSimple Data Manager-service.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. De parameters opgeven waarmee de taakdefinitie moet worden uitgevoerd

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (OF)

    Als u de parameters voor taakdefinitie tijdens de looptijd wilt wijzigen, voegt u de volgende code toe:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Voeg na de initialisatie de volgende code toe om een taak voor gegevenstransformatie op de taakdefinitie te activeren. Sluit de juiste **naam voor taakdefinitie**aan .

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Zodra de code is geplakt, bouwt u de oplossing. Hier is een screenshot van het codefragment om de taakinstantie voor gegevenstransformatie te initialiseren.

   ![Codefragment voor het initialiseren van de taak voor gegevenstransformatie](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Deze taak transformeert de gegevens die overeenkomen met de hoofdmap- en bestandsfilters binnen het StorSimple-volume en plaatst deze in de opgegeven container/bestandsshare. Wanneer een bestand wordt getransformeerd, wordt een bericht toegevoegd aan een opslagwachtrij (in hetzelfde opslagaccount als de container/bestandsshare) met dezelfde naam als de taakdefinitie. Dit bericht kan worden gebruikt als een trigger om verdere verwerking van het bestand te starten.

10. Zodra de taak is geactiveerd, u de volgende code gebruiken om de taak bij te houden voor voltooiing. Het is niet verplicht om deze code toe te voegen voor de taakdie wordt uitgevoerd.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    Hier is een screenshot van het volledige codevoorbeeld dat wordt gebruikt om de taak te activeren met behulp van .NET.

    ![Volledig fragment van code om een .NET-taak te activeren](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Volgende stappen

[Gebruik de gebruikersinterface van StorSimple Data Manager om uw gegevens te transformeren.](storsimple-data-manager-ui.md)
