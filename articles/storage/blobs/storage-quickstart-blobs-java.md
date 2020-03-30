---
title: 'Snelstart: Azure Blob-opslagbibliotheek v12 - Java'
description: In deze quickstart leert u hoe u de Azure Blob-opslagclientbibliotheekversie 12 voor Java gebruiken om een container en een blob in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/27/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: c883653754e1f69d3b2d79b256d57a036c70b58e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330133"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Snelstart: blobs beheren met Java v12 SDK

In deze quickstart leer je blobs te beheren met Java. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, waaronder afbeeldingen, documenten, streamingmedia en archiefgegevens. Je uploadt, downloadt en lijstblobs en je maakt en verwijdert containers.

[API-naslagdocumentatie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/index.html) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob) | [(Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob?repo=jcenter) | [Voorbeelden](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) versie 8 of hoger.
- [Apache Maven.](https://maven.apache.org/download.cgi)

> [!NOTE]
> Zie [Snelstart: Blobs beheren met Java v8 SDK](storage-quickstart-blobs-java-legacy.md)om aan de slag te gaan met de vorige SDK-versie.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Instellen

In deze sectie u een project voorbereiden om te werken met de Azure Blob-opslagclientbibliotheek v12 voor Java.

### <a name="create-the-project"></a>Het project maken

Maak een Java-applicatie met de naam *blob-quickstart-v12*.

1. Gebruik Maven in een consolevenster (zoals cmd, PowerShell of Bash) om een nieuwe console-app te maken met de naam *blob-quickstart-v12*. Typ de volgende **mvn-opdracht** om een 'Hallo wereld! Java-project.

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart \
                          -DartifactId=blob-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. De output van het genereren van het project moet er ongeveer als volgt uitzien:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. Maak aan de zijkant van de *blob-quickstart-v12-map* een andere map met de naam *gegevens*. Dit is waar de blob-gegevensbestanden worden gemaakt en opgeslagen.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Het pakket installeren

Open het *pom.xml-bestand* in uw teksteditor. Voeg het volgende afhankelijkheidselement toe aan de groep afhankelijkheden.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In de projectmap:

1. Navigeer naar de map */src/main/java/com/blobs/quickstart*
1. Het *app.java-bestand* openen in uw editor
1. De `System.out.println("Hello world!");` instructie verwijderen
1. Richtlijnen `import` toevoegen

Hier is de code:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveelheden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslagaccount
* Een container in het opslagaccount
* Een blob in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

Gebruik de volgende Java-klassen om met deze bronnen te communiceren:

* [BlobServiceClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClient.html) `BlobServiceClient` met de klasse u Azure Storage-bronnen en blob-containers manipuleren. Het opslagaccount biedt de naamruimte op het hoogste niveau voor de Blob-service.
* [BlobServiceClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClientBuilder.html): `BlobServiceClientBuilder` De klasse biedt een vloeiende builder-API om `BlobServiceClient` de configuratie en instantiatie van objecten te helpen.
* [BlobContainerClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) `BlobContainerClient` met de klasse u Azure Storage-containers en hun blobs manipuleren.
* [BlobClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) `BlobClient` met de klasse u Azure Storage-blobs manipuleren.
* [BlobItem:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/models/BlobItem.html) `BlobItem` de klasse vertegenwoordigt afzonderlijke blobs `listBlobsFlat`die zijn geretourneerd van een aanroep naar .

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeldcodefragmenten ziet u hoe u het volgende uitvoeren met de Azure Blob-opslagclientbibliotheek voor Java:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container in een lijst weergeven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Een container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op uit de omgevingsvariabele die is gemaakt in de sectie [Uw opslagverbindingstekenreeks configureren.](#configure-your-storage-connection-string)

Voeg deze code `Main` toe in de methode:

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Een container maken

Bepaal een naam voor de nieuwe container. De onderstaande code voegt een UUID-waarde toe aan de containernaam om er zeker van te zijn dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak vervolgens een instantie van de klasse [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) en roep vervolgens de [methode voor het maken](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#create--) aan om de container daadwerkelijk in uw opslagaccount te maken.

Voeg deze code toe `Main` aan het einde van de methode:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende codefragment:

1. Hiermee maakt u een tekstbestand in de lokale *gegevensmap.*
1. Hier wordt een verwijzing naar een [BlobClient-object](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) weergegeven door de [getBlobClient-methode](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#getBlobClient-java.lang.String-) op de container aan te roepen vanuit de sectie [Een container](#create-a-container) maken.
1. Uploadt het lokale tekstbestand naar de blob door de [methode uploadFromfile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html#uploadFromFile-java.lang.String-) aan te roepen. Met deze methode wordt de blob gemaakt als deze nog niet bestaat, maar deze niet overschrijft als dit wel het wel het wel is.

Voeg deze code toe `Main` aan het einde van de methode:

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Lijst van de blobs in de container door de [methode listBlobs](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#listBlobs--) aan te roepen. In dit geval is er slechts één blob aan de container toegevoegd, zodat de lijstbewerking alleen die ene blob retourneert.

Voeg deze code toe `Main` aan het einde van de methode:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Blobs downloaden

Download de eerder gemaakte blob door de [downloadToFile-methode aan te](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/specialized/BlobClientBase.html#downloadToFile-java.lang.String-) roepen. De voorbeeldcode voegt een achtervoegsel van DOWNLOADEN toe aan de bestandsnaam, zodat u beide bestanden in het lokale bestandssysteem zien.

Voeg deze code toe `Main` aan het einde van de methode:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code worden de resources die de app heeft gemaakt, opgeschoond door de hele container te verwijderen met behulp van de [verwijdermethode.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#delete--) Het verwijdert ook de lokale bestanden die door de app zijn gemaakt.

De app pauzeert voor `System.console().readLine()` gebruikersinvoer door aan te roepen voordat de blob-, container- en lokale bestanden worden verwijderd. Dit is een goede kans om te controleren of de resources correct zijn gemaakt, voordat ze worden verwijderd.

Voeg deze code toe `Main` aan het einde van de methode:

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>De code uitvoeren

Deze app maakt een testbestand in uw lokale map en uploadt het naar Blob-opslag. In het voorbeeld worden vervolgens de blobs in de container weergegeven en wordt het bestand gedownload met een nieuwe naam, zodat u de oude en nieuwe bestanden vergelijken.

Navigeer naar de map met het *pom.xml-bestand* en `mvn` compileer het project met de volgende opdracht.

```console
mvn compile
```

Bouw vervolgens het pakket.

```console
mvn package
```

Voer de `mvn` volgende opdracht uit om de app uit te voeren.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

De uitvoer van de app is vergelijkbaar met het volgende voorbeeld:

```output
Azure Blob storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Controleer voordat u met het opschoneproces begint, de *gegevensmap* voor de twee bestanden. Als u ze opent, ziet u dat ze identiek zijn.

Nadat u de bestanden hebt geverifieerd, drukt u op **enter** om de testbestanden te verwijderen en de demo te voltooien.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je blobs uploaden, downloaden en aanbieden met Java.

Ga verder met:

> [!div class="nextstepaction"]
> [Azure Blob-opslag SDK v12 Java-voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Zie de Azure [SDK voor Java voor](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md)meer informatie.
* Ga voor zelfstudies, voorbeelden, quickstarts en andere documentatie naar [Azure voor Java-cloudontwikkelaars.](/azure/java/)
