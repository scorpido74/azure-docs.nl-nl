---
title: Open bare Lees toegang voor containers en blobs beheren
titleSuffix: Azure Storage
description: Meer informatie over het maken van containers en blobs beschikbaar voor anonieme toegang en hoe u deze via een programma kunt openen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 7c524cb30b73c95329650924123b2ebc26a5d8a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856005"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Anonieme leestoegang tot containers en blobs beheren

U kunt anonieme, openbare leestoegang inschakelen voor een container en de bijbehorende blobs in Azure Blob-opslag. Door dit te doen, kunt u alleen-lezentoegang verlenen aan deze resources zonder dat u uw accountsleutel hoeft te delen en zonder dat een handtekening voor gedeelde toegang (SAS) nodig is.

Open bare Lees toegang is het beste voor scenario's waarin u wilt dat bepaalde blobs altijd beschikbaar zijn voor anonieme lees toegang. Voor meer nauw keurige controle kunt u een gedeelde toegangs handtekening maken. Met hand tekeningen voor gedeelde toegang kunt u beperkte toegang bieden met behulp van verschillende machtigingen, voor een specifieke periode. Zie [using Shared Access signatures (SAS) (Engelstalig) in azure Storage](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie over het maken van hand tekeningen voor gedeelde toegang.

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Anonieme gebruikers machtigingen verlenen aan containers en blobs

De standaardinstelling is dat een container en alle bijbehorende blobs alleen toegankelijk zijn voor een gebruiker die over de juiste machtigingen beschikt. Als u anonieme gebruikers leestoegang wilt geven voor een container en de bijbehorende blobs, kunt u het niveau voor openbare toegang van de container instellen. Wanneer u openbare toegang instelt voor een container, kunnen anonieme gebruikers blobs lezen binnen een openbaar toegankelijke container zonder dat ze hun aanvraag hoeven te autoriseren.

U kunt een container met de volgende machtigingen configureren:

- **Geen open bare Lees toegang:** De container en de blobs kunnen alleen worden geopend door de eigenaar van het opslag account. Dit is de standaard waarde voor alle nieuwe containers.
- **Open bare Lees toegang alleen voor blobs:** Blobs in de container kunnen worden gelezen door anonieme aanvragen, maar er zijn geen container gegevens beschikbaar. Anonieme clients kunnen de blobs in de container niet inventariseren.
- **Open bare Lees toegang voor container en de bijbehorende blobs:** Alle container-en BLOB-gegevens kunnen door anonieme aanvragen worden gelezen. Clients kunnen blobs in de container inventariseren door anonieme aanvragen, maar kunnen containers niet inventariseren binnen het opslag account.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Stel een openbaar toegangs niveau in voor de container in de Azure Portal

Vanuit het [Azure Portal](https://portal.azure.com)kunt u het niveau van open bare toegang voor een of meer containers bijwerken:

1. Navigeer naar het overzicht van het opslag account in de Azure Portal.
1. Selecteer onder **BLOB service** op de menu Blade de optie **blobs**.
1. Selecteer de containers waarvoor u het niveau van open bare toegang wilt instellen.
1. Gebruik de knop **toegangs niveau wijzigen** om de instellingen voor open bare toegang weer te geven.
1. Selecteer het gewenste open bare toegangs niveau van de vervolg keuzelijst **openbaar toegangs niveau** en klik op de knop OK om de wijziging toe te passen op de geselecteerde containers.

De volgende scherm afbeelding laat zien hoe u het niveau van open bare toegang voor de geselecteerde containers kunt wijzigen.

![Scherm afbeelding die laat zien hoe u het niveau van open bare toegang instelt in de portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> U kunt het niveau van de open bare toegang voor een afzonderlijke BLOB niet wijzigen. Niveau van open bare toegang wordt alleen op container niveau ingesteld.

### <a name="set-container-public-access-level-with-net"></a>Het niveau van open bare toegang tot de container instellen met .NET

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

Als u de machtigingen voor een container wilt instellen, roept u de methode [BlobContainerClient. SetAccessPolicy](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) aan. 

In het volgende voor beeld worden de machtigingen van de container ingesteld op volledige open bare Lees toegang. Als u machtigingen wilt instellen op open bare Lees toegang voor blobs, geeft u het veld **PublicAccessType. blob** door aan de methode [BlobContainerClient. SetAccessPolicy](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) . Als u alle machtigingen voor anonieme gebruikers wilt verwijderen, gebruikt u het veld **BlobContainerPublicAccessType. none** .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_SetPublicContainerPermissions":::

# <a name="net-v11-sdk"></a>[\.NET V11 SDK](#tab/dotnet11)

Als u de machtigingen voor een container wilt instellen met behulp van de Azure Storage-client bibliotheek voor .NET, moet u eerst de bestaande machtigingen van de container ophalen door een van de volgende methoden aan te roepen:

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Stel vervolgens de eigenschap **PublicAccess** in op het [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) -object dat wordt geretourneerd door de methode **GetPermissions** .

Als laatste moet u een van de volgende methoden aanroepen om de machtigingen van de container bij te werken:

- [Recht](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

In het volgende voor beeld worden de machtigingen van de container ingesteld op volledige open bare Lees toegang. Als u machtigingen wilt instellen op open bare Lees toegang voor blobs, stelt u de eigenschap **PublicAccess** in op **BlobContainerPublicAccessType. blob**. Als u alle machtigingen voor anonieme gebruikers wilt verwijderen, stelt u de eigenschap in op **BlobContainerPublicAccessType. off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

---

## <a name="access-containers-and-blobs-anonymously"></a>Anoniem toegang krijgen tot containers en blobs

Een client die toegang heeft tot containers en blobs anoniem kan gebruikmaken van constructors waarvoor geen referenties zijn vereist. In de volgende voor beelden ziet u een aantal verschillende manieren om te verwijzen naar containers en blobs anoniem.

### <a name="create-an-anonymous-client-object"></a>Een anoniem client object maken

U kunt een nieuw service-client object voor anonieme toegang maken door het eind punt van de Blob-opslag voor het account op te geven. U moet echter ook de naam weten van een container in dat account die beschikbaar is voor anonieme toegang.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET V11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
``` 

---

### <a name="reference-a-container-anonymously"></a>Anoniem verwijzen naar een container

Als u de URL naar een container hebt die anoniem beschikbaar is, kunt u deze gebruiken om rechtstreeks naar de container te verwijzen.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET V11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
``` 

---

### <a name="reference-a-blob-anonymously"></a>Anoniem verwijzen naar een BLOB

Als u de URL naar een BLOB hebt die beschikbaar is voor anonieme toegang, kunt u rechtstreeks naar de BLOB verwijzen met behulp van die URL:

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET V11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
``` 

---

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure Storage autoriseren](../common/storage-auth.md)
- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md)
- [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api)
