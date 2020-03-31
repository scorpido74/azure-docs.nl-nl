---
title: Blobpad wijzigen vanaf de standaardinstelling
description: Meer informatie over het instellen van een Azure-functie om de naam van een blobbestandspad te wijzigen
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 5ba1709ae195631371e4ea72667ba9b2a4bf279e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76270630"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Een blobpad wijzigen vanaf het standaardpad

Wanneer de StorSimple Data Manager-service de gegevens transformeert, worden de getransformeerde blobs standaard in een opslagcontainer geplaatst zoals opgegeven tijdens het maken van de doelopslagplaats. Als de blobs op deze locatie aankomen, u deze blobs verplaatsen naar een alternatieve locatie. In dit artikel wordt beschreven hoe u een Azure-functie instelt om de naam van een standaardblobbestandspad te wijzigen en de blobs dus naar een andere locatie te verplaatsen.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u een correct geconfigureerde taakdefinitie hebt in uw StorSimple Data Manager-service.

## <a name="create-an-azure-function"></a>Een Azure-functie maken

Voer de volgende stappen uit om een Azure-functie te maken:

1. Ga naar de [Azure-portal.](https://portal.azure.com/)

2. Klik **op + Een resource maken**. Typ **Functie-app** in het vak **Zoeken** en druk op **Enter**. Selecteer en klik op **de functie-app** in de lijst met weergegeven apps.

    ![Typ 'Functie-app' in het vak Zoeken](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Klik **op Maken**.

    ![De knop Functie-app](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Voer in het configuratieblad van de **functie-app** de volgende stappen uit:

    1. Geef een unieke **app-naam op.**
    2. Selecteer in de vervolgkeuzelijst de **optie Abonnement**. Dit abonnement moet hetzelfde zijn als het abonnement dat is gekoppeld aan uw StorSimple Data Manager-service.
    3. Selecteer Nieuwe resourcegroep **maken.**
    4. Selecteer **Verbruiksplan**voor de vervolgkeuzelijst **Hostingplan** .
    5. Geef een locatie op waar uw functie wordt uitgevoerd. U wilt dezelfde regio waar de StorSimple Data Manager-service en het opslagaccount bevinden dat is gekoppeld aan de taakdefinitie.
    6. Selecteer een bestaand opslagaccount of maak een nieuw opslagaccount. Een opslagaccount wordt intern gebruikt voor de functie.

        ![Nieuwe configuratiegegevens van functie-app invoeren](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Klik **op Maken**. De functie-app wordt gemaakt.
     
        ![Functie-app gemaakt](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Selecteer **Functies**en klik op **+ Nieuwe functie**.

    ![Klik op + Nieuwe functie](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Selecteer **C#** voor de taal. Selecteer **C#** in de array met sjabloontegels in de tegel **QueueTrigger-CSharp.**

7. In de **wachtrijtrigger:**

    1. Voer een **naam** voor uw functie in.
    2. Typ in het vak **Wachtrijnaam** de naam van de taakdefinitie van de gegevenstransformatie.
    3. Klik **onder Opslagaccountverbinding**op **nieuw**. Selecteer in de lijst met opslagaccounts het account dat is gekoppeld aan uw taakdefinitie. Noteer de naam van de verbinding (gemarkeerd). De naam is later vereist in de Azure-functie.

        ![Een nieuwe C#-functie maken](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Klik **op Maken**. De **functie** wordt gemaakt.

     
10. Voer in het venster Functie _het CSX-bestand_ uit.

    ![Een nieuwe C#-functie maken](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Voer de volgende stappen uit.

    1. Plak de volgende code:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. Vervang **STORAGE_CONNECTIONNAME** op regel 11 door uw opslagaccountverbinding (zie stap 7c).

        ![Naam van de opslagverbinding kopiëren](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. Sla de functie **op.**

        ![Opslaan, functie](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Als u de functie wilt voltooien, voegt u nog een bestand toe door de volgende stappen uit te voeren:

    1. Klik **op Bestanden weergeven**.

       ![De koppeling Bestanden weergeven](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Klik op **+ Toevoegen**.
        
        ![De koppeling Bestanden weergeven](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Typ **project.json**en druk op **Enter**. Plak in het **bestand project.json** de volgende code:

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. Klik op **Opslaan**.

        ![De koppeling Bestanden weergeven](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

U hebt een Azure-functie gemaakt. Deze functie wordt geactiveerd telkens wanneer een nieuwe blob wordt gegenereerd door de taak voor gegevenstransformatie.

## <a name="next-steps"></a>Volgende stappen

[De gebruikersinterface van StorSimple Data Manager gebruiken om uw gegevens te transformeren](storsimple-data-manager-ui.md)
