---
title: 'Snelstartgids: Azure Blob-opslag bibliotheek V12-java'
description: In deze Quick Start leert u hoe u de Azure Blob Storage-client bibliotheek versie 12 voor Java kunt gebruiken om een container en een BLOB in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 5196dbbfb52ce75031a53764b371d6d34b43fba7
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091421"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-java"></a>Snelstartgids: Azure Blob Storage-client bibliotheek V12 voor Java

Ga aan de slag met de Azure Blob Storage-client bibliotheek V12 voor Java. Azure Blob Storage is Microsoft's oplossing voor opslag van objecten in de cloud. Volg de stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens.

> [!NOTE]
> Zie [Snelstartgids: Azure Blob Storage-client bibliotheek voor Java](storage-quickstart-blobs-java-legacy.md)om aan de slag te gaan met de vorige SDK-versie.

Gebruik de Azure Blob Storage-client bibliotheek V12 voor Java naar:

* Een container maken
* Een BLOB uploaden naar Azure Storage
* Alle blobs in een container weer geven
* Een BLOB downloaden naar uw lokale computer
* Een container verwijderen

[API-referentie documentatie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/index.html) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob) | [pakket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob?repo=jcenter) | -voor [beelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

* JDK-versie 8 of hoger van [Java Development Kit](/java/azure/jdk/?view=azure-java-stable)
* [Apache Maven](https://maven.apache.org/download.cgi)
* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Azure Storage-account: [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-client bibliotheek V12 voor Java.

### <a name="create-the-project"></a>Het project maken

Maak een Java-kern toepassing *met de naam BLOB-Quick Start-V12*.

1. In een console venster (zoals cmd, Power shell of bash), gebruikt u maven om een nieuwe console-app te maken met de naam *BLOB-Quick Start-V12*. Typ de volgende **MVN** -opdracht op één regel om een eenvoudig ' Hello World! ' te maken. Java-project. De opdracht wordt hier op meerdere regels weer gegeven voor de Lees baarheid.

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart
                          -DartifactId=blob-quickstart-v12
                          -DarchetypeArtifactId=maven-archetype-quickstart
                          -DarchetypeVersion=1.4
                          -DinteractiveMode=false
   ```

1. De uitvoer van het project genereren ziet er ongeveer als volgt uit:

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

1. Maak in de Directory *BLOB-Quick Start-V12* een andere map met de naam *Data*. Hier worden de BLOB-gegevens bestanden gemaakt en opgeslagen.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Het pakket installeren

Open het bestand *pom. XML* in de tekst editor. Voeg het volgende afhankelijkheids element toe aan de groep met afhankelijkheden.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Het app-Framework instellen

Vanuit de projectmap:

1. Ga naar de map */src/main/Java/com/blobs/QuickStart*
1. Open het *app. java* -bestand in uw editor
1. De `System.out.println("Hello world!");`-instructie verwijderen
1. `import`-instructies toevoegen

Hier volgt de code:

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

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieer uw referenties van de Azure Portal

Wanneer de voorbeeld toepassing een aanvraag indient om Azure Storage, moet deze worden geautoriseerd. Als u een aanvraag wilt autoriseren, voegt u de referenties van uw opslag account toe aan de toepassing als een connection string. U kunt de referenties van het opslagaccount weergeven door de volgende stappen te volgen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. Hier worden de toegangssleutels van uw account weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en selecteer de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

Nadat u de verbindingsreeks hebt gekopieerd, schrijft u deze naar een nieuwe omgevingsvariabele op de lokale computer waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en volgt u de aanwijzingen voor uw besturingssysteem. Vervang `<yourconnectionstring>` door de daad werkelijke connection string.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Nadat u de omgevings variabele in Windows hebt toegevoegd, moet u een nieuw exemplaar van het opdracht venster starten.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Program ma's opnieuw starten

Nadat u de omgevings variabele hebt toegevoegd, start u alle actieve Program ma's die moeten worden gelezen van de omgevings variabele opnieuw. Start uw ontwikkel omgeving of editor bijvoorbeeld opnieuw op voordat u doorgaat.

## <a name="object-model"></a>Object model

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveel heden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslag account
* Een container in het opslag account
* Een BLOB in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blob-introduction/blob1.png)

Gebruik de volgende Java-klassen om te communiceren met deze resources:

* [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClient.html): met de klasse `BlobServiceClient` kunt u Azure storage resources en BLOB-containers bewerken. Het opslag account biedt de naam ruimte op het hoogste niveau voor de Blob service.
* [BlobServiceClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClientBuilder.html): de klasse `BlobServiceClientBuilder` biedt een interface van Fluent Builder om de configuratie en instantiëring van `BlobServiceClient` objecten te helpen ondersteunen.
* [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html): met de klasse `BlobContainerClient` kunt u Azure Storage containers en de bijbehorende blobs bewerken.
* [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html): met de klasse `BlobClient` kunt u Azure Storage blobs bewerken.
* [BlobItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/models/BlobItem.html): de klasse `BlobItem` vertegenwoordigt afzonderlijke blobs die zijn geretourneerd door een aanroep van `listBlobsFlat`.

## <a name="code-examples"></a>Code voorbeelden

In deze voorbeeld code fragmenten ziet u hoe u het volgende kunt uitvoeren met de Azure Blob Storage-client bibliotheek voor Java:

* [De connection string ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container weer geven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de connection string voor het opslag account op uit de omgevings variabele die in de sectie [uw opslag Connection String configureren](#configure-your-storage-connection-string) is gemaakt.

Voeg deze code toe in de `Main` methode:

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("CONNECT_STR");
```

### <a name="create-a-container"></a>Een container maken

Kies een naam voor de nieuwe container. De onderstaande code voegt een UUID-waarde toe aan de container naam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak vervolgens een instantie van de klasse [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) en roep vervolgens de [Create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#create--) -methode aan om de container in uw opslag account te maken.

Voeg deze code toe aan het einde van de `Main` methode:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende code fragment:

1. Hiermee maakt u een tekst bestand in de lokale *gegevens* Directory.
1. Hiermee wordt een verwijzing naar een [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) -object opgehaald door de methode [getBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#getBlobClient-java.lang.String-) in de container aan te roepen vanuit de sectie [een container maken](#create-a-container) .
1. Hiermee wordt het lokale tekst bestand geüpload naar de BLOB door de methode [uploadFromFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html#uploadFromFile-java.lang.String-) aan te roepen. Met deze methode wordt de BLOB gemaakt als deze nog niet bestaat, maar wordt deze niet overschreven als dat wel het geval is.

Voeg deze code toe aan het einde van de `Main` methode:

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

Vermeld de blobs in de container door de methode [listBlobs](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#listBlobs--) aan te roepen. In dit geval is er slechts één BLOB aan de container toegevoegd, zodat de vermelding in de lijst alleen die ene BLOB retourneert.

Voeg deze code toe aan het einde van de `Main` methode:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Blobs downloaden

Down load de eerder gemaakte BLOB door de methode [downloadToFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/specialized/BlobClientBase.html#downloadToFile-java.lang.String-) aan te roepen. De voorbeeld code voegt het achtervoegsel ' downloaden ' toe aan de naam van het bestand, zodat u beide bestanden in het lokale bestands systeem kunt zien.

Voeg deze code toe aan het einde van de `Main` methode:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code wordt de resources opgeschoond die de app heeft gemaakt door de volledige container te verwijderen met de methode [Delete](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#delete--) . Ook worden de lokale bestanden verwijderd die zijn gemaakt door de app.

De app wordt gepauzeerd voor gebruikers invoer door `System.console().readLine()` aan te roepen voordat de blob, container en lokale bestanden worden verwijderd. Dit is een goede kans om te controleren of de resources daad werkelijk zijn gemaakt, voordat ze worden verwijderd.

Voeg deze code toe aan het einde van de `Main` methode:

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

Met deze app wordt een test bestand gemaakt in uw lokale map en geüpload naar Blob Storage. In het voor beeld worden de blobs in de container weer gegeven en wordt het bestand met een nieuwe naam gedownload, zodat u de oude en nieuwe bestanden kunt vergelijken.

Navigeer naar de map met het bestand *pom. XML* en compileer het project met behulp van de volgende `mvn` opdracht.

```console
mvn compile
```

Bouw vervolgens het pakket.

```console
mvn package
```

Voer de volgende `mvn` opdracht uit om de app uit te voeren.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

De uitvoer van de app is vergelijkbaar met het volgende voor beeld:

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

Voordat u het opschoon proces start, controleert u de map met uw *MyDocuments* voor de twee bestanden. Als u ze opent, ziet u dat ze identiek zijn.

Nadat u de bestanden hebt gecontroleerd, drukt u op **Enter** om de test bestanden te verwijderen en de demo te volt ooien.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u blobs kunt uploaden, downloaden en vermelden met behulp van Java.

Als u voor beeld-apps voor Blob-opslag wilt zien, gaat u door naar:

> [!div class="nextstepaction"]
> [V12 Java-voor beelden voor Azure Blob Storage SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Zie de [Azure SDK voor Java](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md)voor meer informatie.
* Voor zelf studies, voor beelden, snel starten en andere documentatie gaat u naar [Azure voor Java Cloud-ontwikkel aars](/azure/java/).
