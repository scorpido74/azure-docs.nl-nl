---
title: 'Quickstart: De Java Storage SDK gebruiken om een BLOB te maken'
description: In deze snelstart gaat u een container maken in objectopslag (Azure Blob), een bestand uploaden, objecten vermelden en downloaden met behulp van de Java Storage SDK.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.topic: quickstart
ms.custom: seo-java-july2019
ms.openlocfilehash: 633054d0e06a78124b8fdcefface665b2bf8c25d
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900448"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-the-java-storage-sdk-v10"></a>Quickstart: Blobs uploaden, downloaden en vermelden met behulp van de V10 toevoegen voor Java Storage SDK

In deze snelstart leert u hoe u de nieuwe Java Storage SDK kunt gebruiken om blok-bobs te uploaden, te downloaden en weer te geven in een container in Azure Blob-opslag. De nieuwe Java SDK maakt gebruik van het reactieve programmeermodel met RxJava, dat asynchrone bewerkingen mogelijk maakt. Meer informatie over RxJava [reactieve extensies voor de Java-VM](https://github.com/ReactiveX/RxJava). 

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Zorg dat u de volgende aanvullende vereisten hebt geïnstalleerd:

* [Maven](https://maven.apache.org/download.cgi) om te werken vanaf de opdrachtregel of een Java integrated development environment van uw voorkeur.
* [JDK](https://aka.ms/azure-jdks)

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

De [voorbeeldtoepassing](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart) die in deze quickstart wordt gebruikt, is een eenvoudige consoletoepassing. 

Gebruik [git](https://git-scm.com/) om een kopie van de toepassing naar uw ontwikkelomgeving te downloaden.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

Met deze opdracht wordt de opslagplaats naar uw lokale git-map gekloond.

Nadat het importeren van het project is voltooid, opent u **Snelstart.java** (locatie: **src/main/java/quickstart**).

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren
Voor deze oplossing moet u de naam en sleutel van uw opslagaccount veilig opslaan. Bewaar ze in omgevingsvariabelen die lokaal zijn op de computer waarop het voorbeeld wordt uitgevoerd. Volg het Linux- of het Windows-voorbeeld, afhankelijk van uw besturingssysteem, voor het maken van de omgevingsvariabelen.

### <a name="linux-example"></a>Linux-voorbeeld

```bash
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Windows-voorbeeld

```CMD
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Met dit voorbeeld wordt een testbestand gemaakt in uw standaardmap, **AppData\Local\Temp**, voor Windows-gebruikers. Vervolgens wordt u gevraagd de volgende stappen uit te voeren:

1. Voer opdrachten in om het testbestand te uploaden naar Azure Blob Storage.
2. Maak een lijst van de blobs in de container.
3. Download het geüploade bestand met een nieuwe naam, zodat u het oude en nieuwe bestand kunt vergelijken. 

Als u het voorbeeld met behulp van Maven wilt uitvoeren op de opdrachtregel, opent u een shell en bladert u naar **storage-blobs-java-v10-quickstart** binnen de gekloonde map. Voer vervolgens `mvn compile exec:java` uit.

Dit voorbeeld toont uw uitvoer als u de toepassing op Windows uitvoert.

```Output
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

U beheert het voorbeeld; voer dus opdrachten in om de code te laten uitvoeren. De invoer is hoofdlettergevoelig.

U kunt ook een hulpprogramma zoals [Azure Storage Explorer](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) gebruiken om de bestanden in de Blob-opslag te bekijken. Azure Storage Explorer is een gratis hulpprogramma voor meerdere platforms waarmee u toegang hebt tot de gegevens van uw opslagaccount. 

Controleer de bestanden. Selecteer vervolgens **E** en selecteer **Enter** om de demo te voltooien en de testbestanden te verwijderen. Nu u weet wat het voorbeeld doet, opent u het bestand **Quickstart.java** om de code te bekijken. 

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

In de volgende secties wordt de voorbeeldcode besproken, zodat u begrijpt hoe deze werkt.

### <a name="get-references-to-the-storage-objects"></a>Verwijzingen naar de opslagobjecten ophalen

Eerst maakt u verwijzingen naar objecten die worden gebruikt voor het verkrijgen van toegang tot de Blob-opslag en voor het beheren ervan. Deze objecten bouwen voort op elkaar. Elk object wordt gebruikt door het volgende object in de lijst.

1. Maak een exemplaar van het object **StorageURL** dat naar het opslagaccount wijst.

    * Het object [StorageURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-stable) vertegenwoordigt uw storage-account. U gebruikt het om een nieuwe pijplijn te genereren. 
    * Een pijplijn is een set beleidsregels die wordt gebruikt om aanvragen en antwoorden te bewerken met mechanismen voor autorisatie, logboekregistratie en opnieuw proberen. Zie [HTTP Pipeline](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline) voor meer informatie.  
    * Maak met behulp van de pijplijn een exemplaar van het object [ServiceURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-stable).
    * Gebruik het **ServiceURL**-object om een ​​exemplaar van [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable) te maken.
    * De **ContainerURL** is nodig om bewerkingen uit te voeren op blobcontainers.

2. Maak een exemplaar van het object **ContainerURL**, dat de container voorstelt waartoe u toegang wilt. Containers organiseren uw blobs op dezelfde manier als mappen op uw computer uw bestanden ordenen.

    * De **ContainerURL** biedt een toegangspunt tot de containerservice. 
    * U kunt een exemplaar van het [BlobURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-stable)-object maken met behulp van [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * De **BlobURL** is nodig om blobs te maken.

3. Maak een exemplaar van het object **BlobURL** dat verwijst naar de specifieke blob waarin u geïnteresseerd bent. 

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Containers, blobs en metagegevens een naam geven en hiernaar verwijzen) voor meer informatie over de namen van containers en blobs.

### <a name="create-a-container"></a>Een container maken 

In deze sectie maakt u een exemplaar van de **ContainerURL**. U maakt hierbij ook een nieuwe container. De container in het voorbeeld heet **quickstart**. 

In dit voorbeeld wordt [containerURL.create](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-stable#com_microsoft_azure_storage_blob__container_u_r_l_create_Metadata_PublicAccessType_Context_) gebruikt, zodat u bij elke nieuwe uitvoering van het voorbeeld een nieuwe container kunt maken. U kunt de container ook vooraf maken, zodat u dit niet in de code hoeft te doen.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>Blobs uploaden naar de container

Blob-opslag ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. Blok-blobs worden het meest gebruikt. Ze worden gebruikt in deze snelstart. 

1. Als u een bestand naar een blob wilt uploaden, haalt u een verwijzing naar de blob op in de doelcontainer. 
2. Nadat u de blobverwijzing hebt ontvangen, kunt u een bestand uploaden met een van de volgende API's:

   * API's op laag niveau. Voorbeelden zijn [BlockBlobURL.upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-stable#com_microsoft_azure_storage_blob__block_blob_u_r_l_upload_Flowable_ByteBuffer__long_BlobHTTPHeaders_Metadata_BlobAccessConditions_Context_), ook wel PutBlob genoemd, en [BlockBlobURL.stageBlock](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-stable), ook wel PutBLock genoemd, in het exemplaar van **BlockBlobURL**. 

   * API's op hoog niveau die worden geboden in de klasse [TransferManager](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-stable). Een voorbeeld is de methode [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-stable). 

     Met deze bewerking wordt de blob gemaakt als deze nog niet bestaat. Als de blob al bestaat, wordt deze overschreven.

Met de voorbeeldcode wordt een lokaal bestand gemaakt dat kan worden gebruikt voor uploaden en downloaden, Het bestand dat moet worden geüpload, wordt opgeslagen als **sourceFile** en de URL van de blob wordt opgeslagen in **blob**. In het volgende voorbeeld wordt het bestand geüpload naar de container met de naam **quickstart**.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {

    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());

    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

Blok-bobs kunnen bestaan uit elk type tekstbestand of binair bestand. Pagina-blobs worden hoofdzakelijk gebruikt voor de VHD-bestanden die worden gebruikt als back-up voor IaaS-VM‘s. Toevoeg-blobs worden gebruikt om gegevens toe te voegen aan het eind en worden vaak gebruikt voor logboekregistratie. De meeste objecten die worden opgeslagen in Blob-opslag, zijn blok-blobs.

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Met behulp van [ContainerURL.listBlobsFlatSegment](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-stable) kunt u een lijst met objecten in een container ophalen. Met deze methode worden maximaal 5.000 objecten tegelijk geretourneerd samen met een vervolgmarkering (markering Volgende) als de container meer objecten bevat om weer te geven. Maak een Helper-functie die zichzelf herhaaldelijk aanroept wanneer er een markering Volgende is in het vorige **listBlobsFlatSegment**-antwoord.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs,
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }

    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */

        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */

        return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
            .flatMap(containersListBlobFlatSegmentResponse ->
                listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Blobs downloaden

Download blobs naar de lokale schijf met behulp van [BlobURL.download](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-stable).

Met de volgende code wordt een blob gedownload die in een vorige sectie is geüpload. Het achtervoegsel **_DOWNLOADED** wordt toegevoegd aan de naam van de blob, zodat u beide bestanden op de lokale schijf kunt zien. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>Resources opschonen

Als u de blobs die in deze snelstart zijn geüpload, niet nodig hebt, kunt u de hele container verwijderen met behulp van [ContainerURL.delete](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-stable). Met deze methode worden ook de bestanden in de container verwijderd.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u bestanden overbrengt tussen een lokale schijf en Azure Blob-opslag met behulp van Java. 

> [!div class="nextstepaction"]
> [Storage SDK V10 voor Java-broncode](https://github.com/Azure/azure-storage-java/)
> [API-verwijzing](https://docs.microsoft.com/java/api/overview/azure/storage)
> [Meer informatie over RxJava](https://github.com/ReactiveX/RxJava)
