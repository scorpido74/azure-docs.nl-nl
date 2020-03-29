---
title: Objectopslag (Blob) gebruiken vanuit iOS - Azure | Microsoft Documenten
description: Sla niet-gestructureerde gegevens op in de cloud met Azure Blob Storage (objectopslag).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 54085d602246d38adb970ed02f451241ca7ba19d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726409"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Blob-opslag van iOS gebruiken

In dit artikel ziet u hoe u veelvoorkomende scenario's uitvoert met Microsoft Azure Blob-opslag. De voorbeelden zijn geschreven in Doelstelling-C en gebruiken de [Azure Storage Client Library voor iOS.](https://github.com/Azure/azure-storage-ios) De scenario's die worden behandeld zijn het uploaden, aanbieden, downloaden en verwijderen van blobs. Zie de sectie [Volgende stappen](#next-steps) voor meer informatie over blobs. U de [voorbeeld-app](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) ook downloaden om snel het gebruik van Azure Storage in een iOS-toepassing te zien.

Zie Inleiding tot Azure [Blob-opslag voor](storage-blobs-introduction.md)meer informatie over Blob-opslag.

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>De Azure Storage iOS-bibliotheek importeren in uw toepassing

U de Azure Storage iOS-bibliotheek importeren in uw toepassing door de [Azure Storage CocoaPod te](https://cocoapods.org/pods/AZSClient) gebruiken of door het **Framework-bestand** te importeren. CocoaPod is de aanbevolen manier omdat het de integratie van de bibliotheek gemakkelijker maakt, maar het importeren uit het framework-bestand is minder opdringerig voor uw bestaande project.

Als u deze bibliotheek wilt gebruiken, hebt u het volgende nodig:

- iOS 8+
- Xcode 7+

## <a name="cocoapod"></a>CacaoPod

1. Als u dit nog niet hebt gedaan, [installeert u CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) op uw computer door een terminalvenster te openen en de volgende opdracht uit te voeren

    ```shell
    sudo gem install cocoapods
    ```

2. Maak vervolgens in de projectmap (de map met het .xcodeproj-bestand) een nieuw bestand met de naam _Podfile_(geen bestandsextensie). Voeg het volgende toe aan _Podfile_ en sla op.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. Navigeer in het terminalvenster naar de projectmap en voer de volgende opdracht uit

    ```shell
    pod install
    ```

4. Als uw .xcodeproj is geopend in Xcode, sluit u deze. Open in uw projectmap het nieuw gemaakte projectbestand met de extensie .xcworkspace. Dit is het bestand waar je vanaf nu aan werkt.

## <a name="framework"></a>Framework

De andere manier om de bibliotheek te gebruiken is om het framework handmatig te bouwen:

1. Download of kloon eerst de [repo azure-storage-ios.](https://github.com/azure/azure-storage-ios)
2. Ga naar de*Lib* -> Azure *Storage Client* -> *Library*en open `AZSClient.xcodeproj` in Xcode.
3. Wijzig linksboven in Xcode het actieve schema van 'Azure Storage Client Library' in 'Framework'.
4. Bouw het project (∙+B). Hiermee wordt `AZSClient.framework` een bestand op uw bureaublad gemaakt.

U het frameworkbestand vervolgens als volgt importeren in uw toepassing:

1. Maak een nieuw project of open uw bestaande project in Xcode.
2. Sleep en `AZSClient.framework` zet de in uw Xcode-projectnavigator.
3. Selecteer *Items zo nodig kopiëren*en klik op *Voltooien*.
4. Klik op uw project in de navigatie met de linkerkant en klik op het tabblad *Algemeen* boven aan de projecteditor.
5. Klik onder de sectie *Gekoppelde frameworks en bibliotheken* op de knop Toevoegen (+).
6. Zoek `libxml2.2.tbd` en voeg deze toe aan uw project in de lijst met bibliotheken die al zijn verstrekt.

## <a name="import-the-library"></a>De bibliotheek importeren

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Als u Swift gebruikt, moet u een overbruggingsheader maken en \<AZSClient/AZSClient.h> importeren:

1. Maak een `Bridging-Header.h`kopbestand en voeg de bovenstaande importinstructie toe.
2. Ga naar het tabblad *Instellingen voor bouwen* en zoek naar *doelstelling-c-overbruggingskop*.
3. Dubbelklik op het veld van *Objective-C Bridging Header* en voeg het pad toe aan uw koptekstbestand:`ProjectName/Bridging-Header.h`
4. Bouw het project (++B) om te controleren of de overbruggingsheader is opgepikt door Xcode.
5. Begin de bibliotheek rechtstreeks in een Swift-bestand te gebruiken, er is geen importverklaringen nodig.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynchrone bewerkingen

> [!NOTE]
> Alle methoden die een aanvraag uitvoeren tegen de service zijn asynchrone bewerkingen. In de codevoorbeelden vindt u dat deze methoden een voltooiingshandler hebben. De code in de voltooiingshandler wordt uitgevoerd **nadat** de aanvraag is voltooid. Code nadat de voltooiingshandler wordt uitgevoerd **terwijl** de aanvraag wordt gedaan.

## <a name="create-a-container"></a>Een container maken

Elke blob in Azure Storage moet zich in een container bevinden. In het volgende voorbeeld ziet u hoe u een container maakt, *newcontainer*genaamd, in uw opslagaccount als deze nog niet bestaat. Bij het kiezen van een naam voor uw container, rekening houden met de hierboven genoemde naamgevingsregels.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

U bevestigen dat dit werkt door te kijken naar de [Microsoft Azure Storage Explorer](https://storageexplorer.com) en te controleren of *newcontainer* in de lijst met containers voor uw opslagaccount staat.

## <a name="set-container-permissions"></a>Containermachtigingen instellen

De machtigingen van een container zijn standaard geconfigureerd voor **privétoegang.** Containers bieden echter een aantal verschillende opties voor containertoegang:

- **Privé:** Container- en blobgegevens kunnen alleen door de eigenaar van het account worden gelezen.
- **Blob:** Blob-gegevens in deze container kunnen worden gelezen via een anoniem verzoek, maar containergegevens zijn niet beschikbaar. Clients kunnen blobs in de container niet opsommen via een anoniem verzoek.
- **Container**: Container- en blobgegevens kunnen worden gelezen via een anoniem verzoek. Clients kunnen blobs in de container opsommen via een anoniem verzoek, maar kunnen geen containers opsommen binnen het opslagaccount.

In het volgende voorbeeld ziet u hoe u een container **maakt** met containertoegangsmachtigingen, waarmee openbare, alleen-lezen toegang voor alle gebruikers op internet mogelijk is:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Een blob uploaden naar een container

Zoals vermeld in de sectie Klodderserviceconcepten biedt Blob Storage drie verschillende typen blobs: blobs blokkeren, blobs toevoegen en paginablobs. De Azure Storage iOS-bibliotheek ondersteunt alle drie de typen blobs. In de meeste gevallen is een blok-blob het aangewezen type om te gebruiken.

In het volgende voorbeeld ziet u hoe u een blokblob uploadt vanaf een NSString. Als er in deze container al een blob met dezelfde naam bestaat, wordt de inhoud van deze blob overschreven.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

U bevestigen dat dit werkt door te kijken naar de [Microsoft Azure Storage Explorer](https://storageexplorer.com) en te controleren of de container, *containerpublic*, bevat de blob, *sampleblob*. In dit voorbeeld hebben we een openbare container gebruikt, zodat u ook controleren of deze toepassing heeft gewerkt door naar de blobs URI te gaan:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Naast het uploaden van een blokblob vanuit een NSString, bestaan er vergelijkbare methoden voor NSData, NSInputStream of een lokaal bestand.

## <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

In het volgende voorbeeld ziet u hoe u alle blobs in een container weergeven. Houd bij het uitvoeren van deze bewerking rekening met de volgende parameters:

- **continuationToken** - Het vervolgtoken geeft aan waar de aanbiedingsbewerking moet beginnen. Als er geen token wordt verstrekt, worden blobs vanaf het begin weergegeven. Een willekeurig aantal blobs kan worden vermeld, van nul tot een ingesteld maximum. Zelfs als deze methode nul `results.continuationToken` resultaten retourneert, als deze niet nihil is, kunnen er meer blobs op de service zijn die niet zijn vermeld.
- **voorvoegsel** : u het voorvoegsel opgeven dat u moet gebruiken voor blob-aanbieding. Alleen blobs die beginnen met dit voorvoegsel worden weergegeven.
- **useFlatBlobListing** - Zoals vermeld in de sectie [Containers en blobs benoemen en verwijzen,](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) de Blob-service een vlak opslagschema zijn, maar u een virtuele hiërarchie maken door blobs te benoemen met padinformatie. Niet-platte aanbieding wordt momenteel echter niet ondersteund. Deze functie komt binnenkort. Voor nu moet deze waarde **JA**zijn.
- **blobListingDetails** - U opgeven welke objecten u wilt opnemen bij het aanbieden van blobs
  - _AZSBlobListingDetailsNone_: Lijst alleen gepleegdblobs, en niet blob metadata terug.
  - _AZSBlobListingDetailsSnapshots:_ Lijst gepleegdblobs en blob snapshots.
  - _AZSBlobListingDetailsMetadata:_ Blob-metagegevens ophalen voor elke blob die in de aanbieding is geretourneerd.
  - _AZSBlobListingDetailsUncommittedBlobs_: Lijst gepleegde en niet-vastgelegde blobs.
  - _AZSBlobListingDetailsCopy_: Kopieereigenschappen opnemen in de aanbieding.
  - _AZSBlobListingDetailsAll:_ Vermeld alle beschikbare toegewezen blobs, niet-vastgelegde blobs en momentopnamen en retourneer alle metagegevens en kopieerstatus voor die blobs.
- **maxResults** - Het maximum aantal resultaten dat moet worden retourneren voor deze bewerking. Gebruik -1 om geen limiet in te stellen.
- **completionHandler** - Het codeblok dat moet worden uitgevoerd met de resultaten van de aanbiedingsbewerking.

In dit voorbeeld wordt een helpermethode gebruikt om de methode lijstblobs opnieuw aan te roepen telkens wanneer een vervolgtoken wordt geretourneerd.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Een blob downloaden

In het volgende voorbeeld ziet u hoe u een blob downloadt naar een NSString-object.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Een blob verwijderen

In het volgende voorbeeld ziet u hoe u een blob verwijdert.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Een blobcontainer verwijderen

In het volgende voorbeeld ziet u hoe u een container verwijdert.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Volgende stappen

Nu je hebt geleerd hoe je Blob-opslag van iOS gebruiken, volg je deze koppelingen voor meer informatie over de iOS-bibliotheek en de opslagservice.

- [Azure-opslagclientbibliotheek voor iOS](https://github.com/azure/azure-storage-ios)
- [Azure Storage iOS-referentiedocumentatie](https://azure.github.io/azure-storage-ios/)
- [REST-API voor Azure Storage-services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog van azure-opslagteam](https://blogs.msdn.com/b/windowsazurestorage)

Als je vragen hebt over deze bibliotheek, kun je een bericht plaatsen op ons [MSDN Azure-forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) of [Stack Overflow.](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files)
Als u functiesuggesties hebt voor Azure Storage, plaatst u een bericht naar [feedback voor Azure Storage.](https://feedback.azure.com/forums/217298-storage/)
