---
title: Toegang tot toepassings gegevens beveiligen
titleSuffix: Azure Storage
description: Gebruik SAS-tokens, versleuteling en HTTPS om de gegevens van een toepassing in de cloud te beveiligen.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: b027ed6b936761e35e835401f9ce8398fac33073
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129639"
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

In dit deel van de serie zelfstudies worden SAS-tokens gebruikt voor toegang tot de miniaturen. In deze stap gaat u de openbare toegang tot de container *thumbnails* instellen op `off`.

```azurecli-interactive 
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
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

De vertakking `sasTokens` van de opslagplaats werkt het bestand `StorageHelper.cs` bij. Dit vervangt de taak `GetThumbNailUrls` met het onderstaande codevoorbeeld. De bijgewerkte taak haalt de Url's van de miniaturen op met behulp van een [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) om de start tijd, verloop tijd en machtigingen voor het SAS-token op te geven. Zodra de web-app is geïmplementeerd, haalt deze met behulp van een SAS-token de miniaturen met een URL op. De bijgewerkte taak wordt in het volgende voorbeeld weergegeven:

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

In de vorige taak zijn de volgende klassen, eigenschappen en methoden gebruikt:

| Klasse | Eigenschappen | Methoden |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [URI](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Reeds](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [Recht](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [Naam](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [Query](/dotnet/api/system.uribuilder.query) |  |
|[Orderverzamellijst](/dotnet/api/system.collections.generic.list-1) | | [Toe](/dotnet/api/system.collections.generic.list-1.add) |

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
