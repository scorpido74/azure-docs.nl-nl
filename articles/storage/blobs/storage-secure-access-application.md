---
title: Toegang tot toepassings gegevens beveiligen
titleSuffix: Azure Storage
description: Gebruik SAS-tokens, versleuteling en HTTPS om de gegevens van een toepassing in de cloud te beveiligen.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: 1075c03820efba44ceb8dea28aff6302d2667cf2
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892427"
---
# <a name="secure-access-to-application-data"></a>Toegang tot toepassings gegevens beveiligen

Deze zelfstudie is deel drie van een serie. U leert hoe u informatie over toegang tot het opslagaccount kunt beveiligen. 

In deel drie van de serie leert u het volgende:

> [!div class="checklist"]
> * SAS-tokens gebruiken voor toegang tot miniatuurafbeeldingen
> * Versleuteling aan de serverzijde inschakelen
> * Alleen-HTTPS-transport inschakelen

[Azure Blob Storage](../common/storage-introduction.md#blob-storage) biedt een robuuste service voor het opslaan van bestanden voor toepassingen. In deze zelf studie wordt [het vorige onderwerp][previous-tutorial] uitgebreid om te laten zien hoe u de toegang tot uw opslag account kunt beveiligen vanuit een webtoepassing. Wanneer u klaar bent, zijn de afbeeldingen versleuteld en gebruikt de web-app beveiligde SAS-tokens voor toegang tot de miniatuurafbeeldingen.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie moet u de vorige hand leiding voor opslag hebben voltooid: [automatisch formaat van geüploade afbeeldingen wijzigen met behulp van Event grid][previous-tutorial].

## <a name="set-container-public-access"></a>Openbare toegang tot de container instellen

In dit deel van de serie zelfstudies worden SAS-tokens gebruikt voor toegang tot de miniaturen. In deze stap gaat u de openbare toegang tot de container _thumbnails_ instellen op `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>SAS-tokens voor miniaturen configureren

In deel één van deze serie zelfstudies, werden door de webtoepassing afbeeldingen van een openbare container weergegeven. In dit deel van de reeks gebruikt u SAS-tokens (Shared Access signatures) om de miniatuur afbeeldingen op te halen. Met SAS-tokens kunt u beperkte toegang verlenen tot een container of blob op basis van IP, protocol, tijdsinterval of toegestane rechten. Zie voor meer informatie over SA'S [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md).

In dit voorbeeld maakt de opslagplaats van de broncode gebruik van de vertakking `sasTokens` die een bijgewerkt codevoorbeeld bevat. Verwijder de bestaande GitHub-implementatie met [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Configureer vervolgens GitHub-implementatie naar de webtoepassing met de opdracht [az webapp deployment source config](/cli/azure/webapp/deployment/source).  

In de volgende opdracht is `<web-app>` de naam van uw web-app.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

De vertakking `sasTokens` van de opslagplaats werkt het bestand `StorageHelper.cs` bij. Dit vervangt de taak `GetThumbNailUrls` met het onderstaande codevoorbeeld. Met de bijgewerkte taak worden de miniatuur-URL's opgehaald door een [SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy) in te stellen om de begintijd, verlooptijd en machtigingen voor het SAS-token op te geven. Zodra de web-app is geïmplementeerd, haalt deze met behulp van een SAS-token de miniaturen met een URL op. De bijgewerkte taak wordt in het volgende voorbeeld weergegeven:
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

In de vorige taak zijn de volgende klassen, eigenschappen en methoden gebruikt:

|Klasse  |Eigenschappen| Methoden  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)        |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     | |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)     | |[SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)       |
|[BlobContinuationToken](/dotnet/api/microsoft.azure.storage.blob.blobcontinuationtoken)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment)    | [Results](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment.results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy)     | [SharedAccessStartTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime)<br>[Machtigingen](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.permissions) |        |

## <a name="server-side-encryption"></a>Versleuteling aan de serverzijde

[Azure Storage Service Encryption (SSE)](../common/storage-service-encryption.md) helpt u bij het beschermen en beveiligen van uw gegevens. SSE-versleutelt data-at-rest, handelt de versleuteling, ontsleuteling en het sleutelbeheer af. Alle gegevens worden versleuteld met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krachtigste blokversleutelingsmethoden die er bestaan.

SSE versleutelt automatisch gegevens in alle prestatielagen (Standaard en Premium), alle implementatiemodellen (Azure Resource Manager en het klassieke model) en alle services van Azure Storage (Blob, Queue, Table en File). 

## <a name="enable-https-only"></a>Alleen-HTTPS inschakelen

Om ervoor te zorgen dat aanvragen voor gegevens naar en van een opslagaccount beveiligd zijn, kunt u aanvragen beperken tot alleen-HTTPS. Werk het vereiste protocol voor het opslagaccount bij met de opdracht [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Test de verbinding met `curl` met behulp van het `HTTP`-protocol.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Nu veilige overdracht is vereist, wordt het volgende bericht weergegeven:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Volgende stappen

In deel drie van de serie, hebt u geleerd hoe u de toegang tot het opslagaccount beveiligt, om het volgende te doen:

> [!div class="checklist"]
> * SAS-tokens gebruiken voor toegang tot miniatuurafbeeldingen
> * Versleuteling aan de serverzijde inschakelen
> * Alleen-HTTPS-transport inschakelen

Ga verder met deel vier van de serie als u wilt weten hoe u een toepassing voor cloudopslag kunt bewaken en problemen ermee kunt oplossen.

> [!div class="nextstepaction"]
> [Toepassing voor cloudopslag bewaken en problemen ermee oplossen](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
