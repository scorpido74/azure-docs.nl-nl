---
title: Verificatie van de gegevens consistentie in de Kopieer activiteit
description: Meer informatie over het inschakelen van de verificatie van gegevens consistentie in de Kopieer activiteit in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: a386c7d44cf5ba7eda895006cda7ce1fa9b798ac
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664975"
---
#  <a name="data-consistency-verification-in-copy-activity-preview"></a>Verificatie van de gegevens consistentie in de Kopieer activiteit (preview-versie)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Wanneer u gegevens van de bron naar het doel archief verplaatst, biedt Azure Data Factory Kopieer activiteit een optie waarmee u aanvullende gegevens consistentie verificatie kunt uitvoeren om ervoor te zorgen dat de gegevens niet alleen worden gekopieerd van de bron naar het doel archief, maar ook zijn geverifieerd om consistent te zijn tussen de bron-en doel opslag. Zodra er inconsistente gegevens zijn gevonden tijdens het verplaatsen van gegevens, kunt u de Kopieer activiteit afbreken of door gaan met het kopiëren van de rest door de instelling fout tolerantie in te scha kelen om inconsistente gegevens over te slaan. U kunt de overgeslagen object namen ophalen door de instelling voor sessie logboek in de Kopieer activiteit in te scha kelen. 

> [!IMPORTANT]
> Deze functie is momenteel in preview, met de volgende beperkingen waarmee we actief werken:
>- Verificatie van gegevens consistentie is alleen beschikbaar op binaire bestanden die worden gekopieerd tussen archieven op basis van bestanden met het gedrag ' PreserveHierarchy ' in de Kopieer activiteit. Voor het kopiëren van tabellaire gegevens is de gegevens consistentie verificatie nog niet beschikbaar in de Kopieer activiteit.
>- Wanneer u de instelling voor het sessie logboek inschakelt in de Kopieer activiteit om de inconsistente bestanden die worden overgeslagen, te registreren, kan de volledigheid van het logboek bestand niet 100% worden gegarandeerd als de Kopieer activiteit is mislukt.
>- Het sessie logboek bevat alleen inconsistente bestanden, waarbij de gekopieerde bestanden tot nu toe niet worden geregistreerd.

## <a name="supported-data-stores"></a>Ondersteunde gegevensarchieven

### <a name="source-data-stores"></a>Brongegevens archieven

-   [Azure Blob-opslag](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Bestandssysteem](connector-file-system.md)
-   [HDFS](connector-hdfs.md)

### <a name="destination-data-stores"></a>Doel gegevens archieven

-   [Azure Blob-opslag](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Bestandssysteem](connector-file-system.md)


## <a name="configuration"></a>Configuratie
In het volgende voor beeld wordt een JSON-definitie geboden om de verificatie van gegevens consistentie in de Kopieer activiteit in te scha kelen: 

```json
"typeProperties": { 
"source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
        } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
}, 
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2_storage", 
            "type": "LinkedServiceReference" 
        }, 
        "path": "/sessionlog/" 
} 
} 
```

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | -------- 
validateDataConsistency | Als u waar voor deze eigenschap instelt, wordt door de Kopieer activiteit de bestands grootte, de lastModifiedDate en de MD5-controlesom gecontroleerd voor elk object dat is gekopieerd van bron naar doel archief om ervoor te zorgen dat de gegevens consistentie tussen het bron-en doel archief. Houd er rekening mee dat de Kopieer prestaties worden beïnvloed door deze optie in te scha kelen.  | True<br/>False (standaard) | Nee
dataInconsistency | Een van de sleutel-waardeparen in de skipErrorFile-eigenschappen verzameling om te bepalen of u de inconsistente gegevens wilt overs Laan.<br/> -True: u wilt de rest kopiëren door inconsistente gegevens over te slaan.<br/> -False: u wilt de Kopieer activiteit afbreken zodra inconsistente gegevens zijn gevonden.<br/>Houd er rekening mee dat deze eigenschap alleen geldig is wanneer u validateDataConsistency instelt als waar.  | True<br/>False (standaard) | Nee
logStorageSettings | Een groep eigenschappen die kan worden opgegeven om het sessie logboek in te scha kelen voor het vastleggen van overgeslagen objecten. | | Nee
linkedServiceName | De gekoppelde service van [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) of [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) voor het opslaan van de sessie logboek bestanden. | De namen van een `AzureBlobStorage` of meer `AzureBlobFS` gekoppelde service, die verwijst naar het exemplaar dat u gebruikt om de logboek bestanden op te slaan. | Nee
path | Het pad van de logboek bestanden. | Geef het pad op waarin u de logboek bestanden wilt opslaan. Als u geen pad opgeeft, maakt de service een container voor u. | Nee

>[!NOTE]
>- Gegevens consistentie wordt niet ondersteund in het faserings kopie scenario. 
>- Bij het kopiëren van binaire bestanden uit een opslag archief naar Azure Blob Storage of Azure Data Lake Storage Gen2, wordt door de Kopieer activiteit de bestands grootte en de verificatie van de MD5-controlesom voor de consistentie van de gegevens tussen de bron-en doel opslag gegarandeerd. 
>- Bij het kopiëren van binaire bestanden uit een opslag archief naar andere opslag archieven dan Azure Blob Storage of Azure Data Lake Storage Gen2, wordt door de Kopieer activiteit de bestands grootte gecontroleerd om de gegevens consistentie tussen het bron-en doel archief te garanderen.


## <a name="monitoring"></a>Bewaking

### <a name="output-from-copy-activity"></a>Uitvoer van Kopieer activiteit
Nadat de Kopieer activiteit volledig is uitgevoerd, kunt u het resultaat van de verificatie van de gegevens consistentie controleren door de uitvoer van elke Kopieer activiteit uit te voeren:

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```
U kunt de details van de verificatie van gegevens consistentie van de eigenschap dataConsistencyVerification bekijken.

Waarde van **VerificationResult**: 
-   **Gecontroleerd**: de gekopieerde gegevens zijn geverifieerd om consistent te zijn tussen het bron-en doel archief. 
-   **NotVerified**: de gekopieerde gegevens zijn niet geverifieerd om consistent te zijn omdat u de validateDataConsistency niet hebt ingeschakeld in de Kopieer activiteit. 
-   Niet **ondersteund**: de gekopieerde gegevens zijn niet gecontroleerd om consistent te zijn omdat de gegevens consistentie verificatie niet wordt ondersteund voor dit specifieke Kopieer paar. 

Waarde van **InconsistentData**: 
-   **Found**: de activiteit ADF Copy heeft inconsistente gegevens gevonden. 
-   **Overgeslagen**: de activiteit voor het kopiëren van ADF heeft inconsistente gegevens gevonden en overgeslagen. 
-   **Geen**: er zijn geen inconsistente gegevens gevonden met de ADF Copy-activiteit. Dit kan zijn omdat uw gegevens zijn geverifieerd om consistent te zijn tussen de bron-en doel opslag of omdat u validateDataConsistency hebt uitgeschakeld in de Kopieer activiteit. 

### <a name="session-log-from-copy-activity"></a>Sessie logboek van Kopieer activiteit

Als u configureert om het inconsistente bestand te registreren, kunt u het logboek bestand vinden in dit pad: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  De logboek bestanden worden de CSV-bestanden. 

Het schema van een logboek bestand is als volgt:

Kolom | Beschrijving 
-------- | -----------  
Tijdstempel | De tijds tempel wanneer ADF de inconsistente bestanden overs laat.
Niveau | Het logboek niveau van dit item. Het wordt weer gegeven in het waarschuwings niveau voor het item met het overs laan van bestanden.
OperationName | Operationeel gedrag van de ADF-Kopieer activiteit op elk bestand. Het wordt ' FileSkip ' om het bestand op te geven dat moet worden overgeslagen.
OperationItem | De naam van het bestand dat moet worden overgeslagen.
Bericht | Meer informatie om te illustreren waarom bestanden worden overgeslagen.

Het voor beeld van een logboek bestand is als volgt: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
In het bovenstaande logboek bestand kunt u zien dat sample1. csv is overgeslagen omdat het niet is gelukt om te controleren of het bron-en doel archief consistent zijn. U kunt meer informatie krijgen over waarom sample1. CSV inconsistent wordt omdat dit door andere toepassingen werd gewijzigd terwijl de activiteit ADF copy op hetzelfde moment wordt gekopieerd. 



## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Fout tolerantie van Kopieer activiteit](copy-activity-fault-tolerance.md)


