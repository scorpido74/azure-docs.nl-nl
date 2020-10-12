---
title: Ontwikkelen voor Azure Files met Java | Microsoft Docs
description: Meer informatie over het ontwikkelen van Java-toepassingen en-services die gebruikmaken van Azure Files voor het opslaan van bestands gegevens.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2c80d741ca1cf0b4d66bc18488af8ef1da44fd14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462595"
---
# <a name="develop-for-azure-files-with-java"></a>Ontwikkelen voor Azure Files met Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Over deze zelfstudie
In deze zelf studie wordt gedemonstreerd hoe u Java gebruikt voor het ontwikkelen van toepassingen of services die gebruikmaken van Azure Files om bestands gegevens op te slaan. In deze zelf studie maken we een console toepassing en laten we zien hoe u basis bewerkingen uitvoert met Java en Azure Files:

* Azure-bestands shares maken en verwijderen
* Directory's maken en verwijderen
* Bestanden en mappen in een Azure-bestands share opsommen
* Een bestand uploaden, downloaden en verwijderen

> [!Note]  
> Omdat Azure Files mogelijk toegankelijk is via SMB, is het mogelijk om toepassingen te schrijven die toegang hebben tot de Azure-bestands share met behulp van de standaard Java-I/O-klassen. In dit artikel wordt beschreven hoe u toepassingen schrijft die gebruikmaken van de Azure Storage Java SDK, die gebruikmaakt van de [Azure Files rest API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) om te praten met Azure files.

## <a name="create-a-java-application"></a>Een Java-toepassing maken
U hebt de Java Development Kit (JDK) en de [Azure Storage SDK voor Java](https://github.com/Azure/azure-storage-java)nodig om de voor beelden te maken. U moet ook een Azure-opslag account hebben gemaakt.

## <a name="set-up-your-application-to-use-azure-files"></a>Uw toepassing instellen voor gebruik Azure Files
Als u de Azure Storage Api's wilt gebruiken, voegt u de volgende instructie toe aan de bovenkant van het Java-bestand waar u van plan bent om toegang te krijgen tot de opslag service.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Een Azure Storage-connection string instellen
Als u Azure Files wilt gebruiken, moet u verbinding maken met uw Azure-opslag account. De eerste stap is het configureren van een connection string, dat we gebruiken om verbinding te maken met uw opslag account. We gaan nu een statische variabele definiëren.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Vervang your_storage_account_name en your_storage_account_key door de werkelijke waarden voor uw opslag account.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Verbinding maken met een Azure Storage-account
Als u verbinding wilt maken met uw opslag account, moet u het **Cloud Storage account** -object gebruiken om een Connection String door te geven aan de methode **parse** .

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**Cloud Storage account. parse** genereert een InvalidKeyException, zodat u deze in een try/catch-blok moet plaatsen.

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken
Alle bestanden en mappen in Azure Files bevinden zich in een container met de naam **share**. Uw opslag account kan zoveel shares bevatten als uw account capaciteit toestaat. Als u toegang wilt krijgen tot een share en de inhoud ervan, moet u een Azure Files-client gebruiken.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Met behulp van de Azure Files-client kunt u vervolgens een verwijzing naar een share verkrijgen.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Als u de share daad werkelijk wilt maken, gebruikt u de methode **createIfNotExists** van het object CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Op dit moment bevat **share** een verwijzing naar een share met de naam **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Een Azure-bestands share verwijderen
Het verwijderen van een share wordt uitgevoerd door de methode **deleteIfExists** aan te roepen voor een CloudFileShare-object. Hier volgt een voor beeld van een code die dat doet.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Een map maken
U kunt opslag ook indelen door bestanden in submappen te plaatsen in plaats van deze allemaal in de hoofdmap. Met Azure Files kunt u zoveel mappen maken als uw account toestaat. Met de onderstaande code wordt een submap gemaakt met de naam **sampledir** onder de hoofdmap.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Een map verwijderen
Het verwijderen van een directory is een eenvoudige taak, hoewel u er zeker van moet zijn dat u geen map kunt verwijderen die nog bestanden of andere mappen bevat.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Bestanden en mappen in een Azure-bestands share opsommen
U kunt eenvoudig een lijst met bestanden en mappen in een share verkrijgen door **listFilesAndDirectories** aan te roepen op een CloudFileDirectory-verwijzing. De-methode retourneert een lijst met ListFileItem-objecten die u kunt herhalen. Met de volgende code wordt bijvoorbeeld een lijst met bestanden en mappen in de hoofdmap weer geven.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Een bestand uploaden
In deze sectie leert u hoe u een bestand van de lokale opslag uploadt naar de hoofdmap van een share.

De eerste stap bij het uploaden van een bestand is het verkrijgen van een verwijzing naar de map waar deze zich moet bevinden. U doet dit door de methode **getRootDirectoryReference** van het object share aan te roepen.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Nu u een verwijzing naar de hoofdmap van de share hebt, kunt u een bestand uploaden naar de map met de volgende code.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Een bestand downloaden
Een van de frequentere bewerkingen die u kunt uitvoeren op Azure Files is het downloaden van bestanden. In het volgende voor beeld wordt SampleFile.txt door de code gedownload en wordt de inhoud ervan weer gegeven.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Een bestand verwijderen
Een andere algemene Azure Files bewerking is het verwijderen van een bestand. Met de volgende code wordt een bestand verwijderd met de naam SampleFile.txt opgeslagen in een map met de naam **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>Volgende stappen
Als u meer wilt weten over andere Azure Storage-Api's, volgt u deze koppelingen.

* [Azure voor Java-Ontwikkel aars](/java/azure)/)
* [Azure Storage SDK voor Java](https://github.com/azure/azure-storage-java)
* [Azure Storage SDK voor Android](https://github.com/azure/azure-storage-android)
* [Azure Storage Client SDK-referentie](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [REST-API voor Azure Storage-services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog van het Azure Storage-team](https://docs.microsoft.com/archive/blogs/windowsazurestorage/)
* [Gegevens overdragen met het AzCopy-hulp programma van Command-Line](../common/storage-use-azcopy.md)
* [Problemen met betrekking tot Azure Files oplossen - Windows](storage-troubleshoot-windows-file-connection-problems.md)
