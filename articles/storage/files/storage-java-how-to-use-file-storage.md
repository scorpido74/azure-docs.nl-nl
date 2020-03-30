---
title: Ontwikkelen voor Azure-bestanden met Java | Microsoft Documenten
description: Meer informatie over het ontwikkelen van Java-toepassingen en -services die Azure Files gebruiken om bestandsgegevens op te slaan.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 28a280ea7c3bf9ef84a1fff05da5090ed526fb12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837460"
---
# <a name="develop-for-azure-files-with-java"></a>Ontwikkelen voor Azure Files met Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Over deze zelfstudie
In deze zelfstudie worden de basisprincipes van het gebruik van Java getoond om toepassingen of services te ontwikkelen die Azure-bestanden gebruiken om bestandsgegevens op te slaan. In deze zelfstudie maken we een consoletoepassing en laten we zien hoe u basisacties uitvoert met Java- en Azure-bestanden:

* Azure-bestandsshares maken en verwijderen
* Mappen maken en verwijderen
* Bestanden en mappen opsommen in een Azure-bestandsshare
* Een bestand uploaden, downloaden en verwijderen

> [!Note]  
> Omdat Azure Files mogelijk via SMB worden geopend, is het mogelijk om toepassingen te schrijven die toegang hebben tot de Azure-bestandsshare met behulp van de standaard Java I/O-klassen. In dit artikel wordt beschreven hoe u toepassingen schrijft die gebruikmaken van de Azure Storage Java SDK, die de [AZURE Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) gebruikt om met Azure Files te praten.

## <a name="create-a-java-application"></a>Een Java-toepassing maken
Om de voorbeelden te bouwen, hebt u de Java Development Kit (JDK) en de [Azure Storage SDK voor Java](https://github.com/Azure/azure-storage-java)nodig. U had ook een Azure-opslagaccount moeten maken.

## <a name="set-up-your-application-to-use-azure-files"></a>Uw toepassing instellen voor azure-bestanden
Als u de Azure-opslag-API's wilt gebruiken, voegt u de volgende instructie toe aan de bovenkant van het Java-bestand waar u toegang wilt krijgen tot de opslagservice.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Een azure-opslagverbindingstekenreeks instellen
Als u Azure Files wilt gebruiken, moet u verbinding maken met uw Azure-opslagaccount. De eerste stap zou zijn om een verbindingstekenreeks te configureren, die we gebruiken om verbinding te maken met uw opslagaccount. Laten we een statische variabele definiëren om dat te doen.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Vervang your_storage_account_name en your_storage_account_key door de werkelijke waarden voor uw opslagaccount.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Verbinding maken met een Azure-opslagaccount
Als u verbinding wilt maken met uw opslagaccount, moet u het **cloudopslagobject** gebruiken en een verbindingstekenreeks doorgeven aan de **parse-methode.**

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** gooit een InvalidKeyException, dus je moet het in een try/catch-blok plaatsen.

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken
Alle bestanden en mappen in Azure-bestanden bevinden zich in een container die **delen**wordt genoemd. Uw opslagaccount kan zoveel aandelen hebben als uw accountcapaciteit toestaat. Als u toegang wilt krijgen tot een aandeel en de inhoud ervan, moet u een Azure Files-client gebruiken.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Met de Azure Files-client u vervolgens een verwijzing naar een aandeel verkrijgen.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Als u de share daadwerkelijk wilt maken, gebruikt u de **methode createIfNotExists** van het object CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Op dit punt bevat **het aandeel** een verwijzing naar een aandeel met de naam **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Een Azure-bestandsshare verwijderen
Het verwijderen van een aandeel gebeurt door de **methode deleteIfExists** aan te roepen op een CloudFileShare-object. Hier is voorbeeldcode die dat doet.

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
U ook opslag ordenen door bestanden in submappen te plaatsen in plaats van ze allemaal in de hoofdmap te hebben. Met Azure Files u zoveel mappen maken als uw account toestaat. De onderstaande code maakt een submap met de naam **sampledir** onder de hoofdmap.

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
Het verwijderen van een map is een eenvoudige taak, hoewel moet worden opgemerkt dat u een map die nog steeds bestanden of andere mappen bevat, niet verwijderen.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Bestanden en mappen opsommen in een Azure-bestandsshare
Het verkrijgen van een lijst met bestanden en mappen binnen een aandeel is eenvoudig te doen door **listFilesAndDirectories** aan te roepen op een CloudFileDirectory-referentie. De methode retourneert een lijst met ListFileItem-objecten waarop u herhalen. Als voorbeeld wordt in de volgende code bestanden en mappen in de hoofdmap weergegeven.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Bestand uploaden
In deze sectie leert u hoe u een bestand vanuit lokale opslag uploadt naar de hoofdmap van een share.

De eerste stap bij het uploaden van een bestand is het verkrijgen van een verwijzing naar de directory waar het zich zou moeten bevinden. U doet dit door de **getRootDirectoryReference-methode** van het share-object aan te roepen.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Nu u een verwijzing hebt naar de hoofdmap van het aandeel, u er een bestand op uploaden met behulp van de volgende code.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Bestand downloaden
Een van de meest voorkomende bewerkingen die u uitvoert tegen Azure Files, is het downloaden van bestanden. In het volgende voorbeeld downloadt de code SampleFile.txt en geeft de inhoud ervan weer.

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
Een andere veelvoorkomende Azure-bewerking is het verwijderen van bestanden. Met de volgende code wordt een bestand met de naam SampleFile.txt verwijderd dat is opgeslagen in een map met de naam **sampledir.**

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
Als u meer wilt weten over andere Azure-opslag-API's, volgt u deze koppelingen.

* [Azure voor Java-ontwikkelaars](/java/azure)/)
* [Azure Storage SDK voor Java](https://github.com/azure/azure-storage-java)
* [Azure Storage SDK voor Android](https://github.com/azure/azure-storage-android)
* [Azure Storage Client SDK-referentie](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [REST-API voor Azure Storage-services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog van azure-opslagteam](https://blogs.msdn.com/b/windowsazurestorage/)
* [Gegevens overbrengen met het AzCopy-opdrachtregelhulpprogramma](../common/storage-use-azcopy.md)
* [Problemen met betrekking tot Azure Files oplossen - Windows](storage-troubleshoot-windows-file-connection-problems.md)
