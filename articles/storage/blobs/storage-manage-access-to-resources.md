---
title: Openbare leestoegang voor containers en blobs beheren
titleSuffix: Azure Storage
description: Meer informatie over het beschikbaar maken van containers en blobs voor anonieme toegang en hoe u deze programmatisch openen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4d9a54c220861b19d67b07998e609ee72897446a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255481"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Anonieme leestoegang tot containers en blobs beheren

U kunt anonieme, openbare leestoegang inschakelen voor een container en de bijbehorende blobs in Azure Blob-opslag. Door dit te doen, kunt u alleen-lezentoegang verlenen aan deze resources zonder dat u uw accountsleutel hoeft te delen en zonder dat een handtekening voor gedeelde toegang (SAS) nodig is.

Openbare leestoegang is het beste voor scenario's waarin u wilt dat bepaalde blobs altijd beschikbaar zijn voor anonieme leestoegang. Voor meer fijnmazige besturingselementen u een handtekening voor gedeelde toegang maken. Met handtekeningen voor gedeelde toegang u beperkte toegang bieden met behulp van verschillende machtigingen voor een bepaalde periode. Zie [SAS (Shared Access Signatures) gebruiken in Azure Storage](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie over het maken van handtekeningen voor gedeelde toegang.

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Anonieme gebruikersmachtigingen verlenen aan containers en blobs

De standaardinstelling is dat een container en alle bijbehorende blobs alleen toegankelijk zijn voor een gebruiker die over de juiste machtigingen beschikt. Als u anonieme gebruikers leestoegang wilt geven voor een container en de bijbehorende blobs, kunt u het niveau voor openbare toegang van de container instellen. Wanneer u openbare toegang instelt voor een container, kunnen anonieme gebruikers blobs lezen binnen een openbaar toegankelijke container zonder dat ze hun aanvraag hoeven te autoriseren.

U een container configureren met de volgende machtigingen:

- **Geen openbare leestoegang:** De container en de blobs zijn alleen toegankelijk voor de eigenaar van het opslagaccount. Dit is de standaardinstelling voor alle nieuwe containers.
- **Alleen openbare leestoegang voor blobs:** Blobs in de container kunnen op anoniem verzoek worden gelezen, maar containergegevens zijn niet beschikbaar. Anonieme clients kunnen de blobs in de container niet opsommen.
- **Openbare leestoegang voor container en blobs:** Alle container- en blobgegevens kunnen op anoniem verzoek worden gelezen. Clients kunnen blobs in de container op vragen op anoniem verzoek opsommen, maar kunnen geen containers opsommen binnen het opslagaccount.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Toegangsniveau voor containers instellen in de Azure-portal

Vanuit de [Azure-portal](https://portal.azure.com)u het openbare toegangsniveau voor een of meer containers bijwerken:

1. Navigeer naar het overzicht van uw opslagaccount in de Azure-portal.
1. Selecteer **Blobs**onder **Blob-service** op het menublad .
1. Selecteer de containers waarvoor u het openbare toegangsniveau wilt instellen.
1. Gebruik de knop **Toegangsniveau wijzigen** om de instellingen voor openbare toegang weer te geven.
1. Selecteer het gewenste openbare toegangsniveau in de vervolgkeuzelijst **Openbaar toegangsniveau** en klik op de knop OK om de wijziging toe te passen op de geselecteerde containers.

In de volgende schermafbeelding ziet u hoe u het openbare toegangsniveau voor de geselecteerde containers wijzigen.

![Schermafbeelding van het instellen van openbaar toegangsniveau in de portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> U het openbare toegangsniveau voor een afzonderlijke blob niet wijzigen. Het niveau van de openbare toegang wordt alleen ingesteld op containerniveau.

### <a name="set-container-public-access-level-with-net"></a>Toegangsniveau voor containers instellen met .NET

Als u machtigingen voor een container wilt instellen met de Azure Storage-clientbibliotheek voor .NET, haalt u eerst de bestaande machtigingen van de container op door een van de volgende methoden aan te roepen:

- [Getpermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Stel vervolgens de eigenschap **PublicAccess** in op het object [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) dat wordt geretourneerd met de methode **Machtigingen getpermissions.**

Tot slot u een van de volgende methoden aanroepen om de machtigingen van de container bij te werken:

- [Setpermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

In het volgende voorbeeld worden de machtigingen van de container ingesteld op volledige openbare leestoegang. Als u machtigingen wilt instellen voor alleen openbare leestoegang voor blobs, stelt u de eigenschap **PublicAccess** in op **BlobContainerPublicAccessType.Blob**. Als u alle machtigingen voor anonieme gebruikers wilt verwijderen, stelt u de eigenschap in op **BlobContainerPublicAccessType.Off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Anoniem toegang tot containers en blobs

Een client die anoniem toegang heeft tot containers en blobs, kan constructors gebruiken waarvoor geen referenties nodig zijn. In de volgende voorbeelden worden een aantal verschillende manieren weergegeven om anoniem naar containers en blobs te verwijzen.

### <a name="create-an-anonymous-client-object"></a>Een anoniem clientobject maken

U een nieuw serviceclientobject maken voor anonieme toegang door het blob-opslageindpunt voor het account op te geven. U moet echter ook de naam weten van een container in dat account die beschikbaar is voor anonieme toegang.

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

### <a name="reference-a-container-anonymously"></a>Anoniem naar een container verwijzen

Als u de URL naar een container hebt die anoniem beschikbaar is, u deze gebruiken om rechtstreeks naar de container te verwijzen.

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

### <a name="reference-a-blob-anonymously"></a>Anoniem een blob verwijzen

Als u de URL naar een blob hebt die beschikbaar is voor anonieme toegang, u rechtstreeks naar de blob verwijzen met die URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure Storage toestaan](../common/storage-auth.md)
- [Beperkte toegang verlenen tot Azure Storage-bronnen met behulp van gedeelde toegangshandtekeningen (SAS)](../common/storage-sas-overview.md)
- [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api)
