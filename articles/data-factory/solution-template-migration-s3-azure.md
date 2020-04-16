---
title: Gegevens van Amazon S3 migreren naar Azure Data Lake Storage Gen2
description: Meer informatie over het gebruik van een oplossingssjabloon om gegevens van Amazon S3 te migreren met behulp van een externe controletabel om een partitielijst op AWS S3 op te slaan met Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: 23d799f84cb3ac3ca911a5669041b0a25394a7ff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414762"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Gegevens van Amazon S3 migreren naar Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Gebruik de sjablonen om petabytes aan gegevens te migreren die bestaan uit honderden miljoenen bestanden van Amazon S3 naar Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Als u een klein gegevensvolume van AWS S3 naar Azure wilt kopiëren (bijvoorbeeld minder dan 10 TB), is het efficiënter en eenvoudiger om het [hulpprogramma voor kopiëren met Azure Data Factory-kopiegegevens](copy-data-tool.md)te gebruiken. De sjabloon die in dit artikel wordt beschreven, is meer dan wat u nodig hebt.

## <a name="about-the-solution-templates"></a>Informatie over de oplossingssjablonen

Gegevenspartitie wordt aanbevolen, vooral bij het migreren van meer dan 10 TB aan gegevens. Als u de gegevens wilt verdelen, maakt u gebruik van de instelling 'voorvoegsel' om de mappen en bestanden op Amazon S3 op naam te filteren en kan elke ADF-kopieertaak één partitie tegelijk kopiëren. U meerdere ADF-kopieertaken tegelijk uitvoeren voor een betere doorvoer.

Voor gegevensmigratie is normaal gesproken een eenmalige historische gegevensmigratie vereist plus het periodiek synchroniseren van de wijzigingen van AWS S3 naar Azure. Er zijn hieronder twee sjablonen, waarbij één sjabloon eeneenmalige historische gegevensmigratie omvat en een andere sjabloon betrekking heeft op het synchroniseren van de wijzigingen van AWS S3 naar Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Voor de sjabloon om historische gegevens van Amazon S3 te migreren naar Azure Data Lake Storage Gen2

Deze sjabloon *(sjabloonnaam: migreer historische gegevens van AWS S3 naar Azure Data Lake Storage Gen2)* en gaat ervan uit dat u een partitielijst hebt geschreven in een externe controletabel in Azure SQL Database. Dus het zal gebruik maken van een *Lookup* activiteit om de partitie lijst op te halen uit de externe controle tabel, herhalen over elke partitie, en maak elke ADF kopiëren taak kopie een partitie tegelijk. Zodra een kopieertaak is voltooid, wordt de activiteit *Opgeslagen procedure* gebruikt om de status van het kopiëren van elke partitie in de controletabel bij te werken.

De sjabloon bevat vijf activiteiten:
- **Lookup** haalt de partities die niet zijn gekopieerd naar Azure Data Lake Storage Gen2 op uit een externe controletabel. De tabelnaam is *s3_partition_control_table* en de query om gegevens uit de tabel te laden is *"SELECT PartitionPrefix FROM s3_partition_control_table WHERE SuccessOrFailure = 0".*
- **ForEach** krijgt de partitielijst van de *opzoekactiviteit* en verbeeldt elke partitie naar de *TriggerCopy-activiteit.* U instellen *dat batchCount* meerdere ADF-kopieertaken tegelijk uitvoert. We hebben er 2 in deze sjabloon gezet.
- **ExecutePipeline** voert *CopyFolderPartitionFromS3-pijplijn* uit. De reden dat we een andere pijplijn maken om elke kopie van een partitie te maken, is omdat u met deze bewerking eenvoudig de mislukte kopieertaak uitvoeren om die specifieke partitie opnieuw te laden vanaf AWS S3. Alle andere kopieertaken die andere partities laden, worden niet beïnvloed.
- **Kopieer** kopieën van elke partitie van AWS S3 naar Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** werkt de status bij van het kopiëren van elke partitie in de controletabel.

De sjabloon bevat twee parameters:
- **AWS_S3_bucketName** is uw bucketnaam op AWS S3 waar u gegevens van wilt migreren. Als u gegevens uit meerdere buckets op AWS S3 wilt migreren, u nog een kolom toevoegen aan uw externe controletabel om de bucketnaam voor elke partitie op te slaan en ook uw pijplijn bijwerken om gegevens uit die kolom dienovereenkomstig op te halen.
- **Azure_Storage_fileSystem** is uw bestandsSysteemnaam op Azure Data Lake Storage Gen2 waar naar u gegevens wilt migreren.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Voor de sjabloon om alleen gewijzigde bestanden te kopiëren van Amazon S3 naar Azure Data Lake Storage Gen2

Deze sjabloon *(sjabloonnaam: kopieer deltagegevens van AWS S3 naar Azure Data Lake Storage Gen2)* gebruikt LastModifiedTime van elk bestand om de nieuwe of bijgewerkte bestanden alleen van AWS S3 naar Azure te kopiëren. Houd er rekening mee dat uw bestanden of mappen al tijd zijn verdeeld met tijdslice-informatie als onderdeel van de bestands- of mapnaam op AWS S3 (bijvoorbeeld /yyyy/mm/dd/file.csv), u naar deze [zelfstudie](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) gaan om de meer performante benadering voor incrementele het laden van nieuwe bestanden te krijgen. Deze sjabloon gaat ervan uit dat u een partitielijst hebt geschreven in een externe controletabel in Azure SQL Database. Dus het zal gebruik maken van een *Lookup* activiteit om de partitie lijst op te halen uit de externe controle tabel, herhalen over elke partitie, en maak elke ADF kopiëren taak kopie een partitie tegelijk. Wanneer elke kopieertaak de bestanden van AWS S3 begint te kopiëren, is deze gebaseerd op de eigenschap LastModifiedTime om alleen de nieuwe of bijgewerkte bestanden te identificeren en te kopiëren. Zodra een kopieertaak is voltooid, wordt de activiteit *Opgeslagen procedure* gebruikt om de status van het kopiëren van elke partitie in de controletabel bij te werken.

De sjabloon bevat zeven activiteiten:
- **Lookup** haalt de partities op uit een externe controletabel. De tabelnaam wordt *s3_partition_delta_control_table* en de query om gegevens uit de tabel te laden is *"selecteer afzonderlijke partitievoorvoegsel uit s3_partition_delta_control_table".*
- **ForEach** krijgt de partitielijst van de *opzoekactiviteit* en vereert elke partitie naar de *TriggerDeltaCopy-activiteit.* U instellen *dat batchCount* meerdere ADF-kopieertaken tegelijk uitvoert. We hebben er 2 in deze sjabloon gezet.
- **ExecutePipeline** voert *DeltaCopyFolderPartitionFromS3-pijplijn* uit. De reden dat we een andere pijplijn maken om elke kopie van een partitie te maken, is omdat u met deze bewerking eenvoudig de mislukte kopieertaak uitvoeren om die specifieke partitie opnieuw te laden vanaf AWS S3. Alle andere kopieertaken die andere partities laden, worden niet beïnvloed.
- **Lookup** haalt de laatste taakruntijd voor het kopiëren op uit de externe controletabel, zodat de nieuwe of bijgewerkte bestanden kunnen worden geïdentificeerd via LastModifiedTime. De tabelnaam wordt *s3_partition_delta_control_table* en de query om gegevens uit de tabel te laden is *"selecteer max(JobRunTime) als LastModifiedTime van s3_partition_delta_control_table waar PartitionPrefix = '@{pipeline().parameters.prefixStr}' en SuccessOrFailure = 1"*.
- **Kopieer** kopieën van nieuwe of gewijzigde bestanden alleen voor elke partitie van AWS S3 naar Azure Data Lake Storage Gen2. De eigenschap *modifiedDatetimeStart* is ingesteld op de laatste runtijd van de kopieertaak. De eigenschap *van modifiedDatetimeEnd* is ingesteld op de huidige runtijd van kopieertaak. Houd er rekening mee dat de tijd wordt toegepast op utc-tijdzone.
- **SqlServerStoredProcedure** werkt de status bij van het kopiëren van elke partitie en kopieerde de uitvoeringstijd in de controletabel wanneer deze slaagt. De kolom SuccessOrFailure is ingesteld op 1.
- **SqlServerStoredProcedure** werkt de status bij van het kopiëren van elke partitie en kopieerde de uitvoeringstijd in de controletabel wanneer deze mislukt. De kolom SuccessOrFailure is ingesteld op 0.

De sjabloon bevat twee parameters:
- **AWS_S3_bucketName** is uw bucketnaam op AWS S3 waar u gegevens van wilt migreren. Als u gegevens uit meerdere buckets op AWS S3 wilt migreren, u nog een kolom toevoegen aan uw externe controletabel om de bucketnaam voor elke partitie op te slaan en ook uw pijplijn bijwerken om gegevens uit die kolom dienovereenkomstig op te halen.
- **Azure_Storage_fileSystem** is uw bestandsSysteemnaam op Azure Data Lake Storage Gen2 waar naar u gegevens wilt migreren.

## <a name="how-to-use-these-two-solution-templates"></a>Deze twee oplossingssjablonen gebruiken

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Voor de sjabloon om historische gegevens van Amazon S3 te migreren naar Azure Data Lake Storage Gen2 

1. Maak een besturingselementtabel in Azure SQL Database om de partitielijst met AWS S3 op te slaan. 

    > [!NOTE]
    > De tabelnaam is s3_partition_control_table.
    > Het schema van de controletabel is PartitionPrefix en SuccessOrFailure, waarbij PartitionPrefix de voorvoegselinstelling in S3 is om de mappen en bestanden in Amazon S3 op naam te filteren, en SuccessOrFailure de status van het kopiëren van elke partitie: 0 betekent dat deze partitie niet is gekopieerd naar Azure en 1 betekent dat deze partitie is gekopieerd naar Azure.
    > Er zijn 5 partities gedefinieerd in de controletabel en de standaardstatus van het kopiëren van elke partitie is 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Maak een opgeslagen procedure in dezelfde Azure SQL-database voor beheertabel. 

    > [!NOTE]
    > De naam van de opgeslagen procedure is sp_update_partition_success. Het wordt aangeroepen door SqlServerStoredProcedure-activiteit in uw ADF-pijplijn.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Ga naar de sjabloon **Historische gegevens migreren van AWS S3 naar Azure Data Lake Storage Gen2.** Voer de verbindingen in met uw externe controletabel, AWS S3 als gegevensbronarchief en Azure Data Lake Storage Gen2 als bestemmingsarchief. Houd er rekening mee dat de externe controletabel en de opgeslagen procedure verwijzen naar dezelfde verbinding.

    ![Een nieuwe verbinding maken](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Selecteer **Deze sjabloon gebruiken**.

    ![Deze sjabloon gebruiken](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. U ziet de 2 pijplijnen en 3 gegevenssets zijn gemaakt, zoals weergegeven in het volgende voorbeeld:

    ![De pijplijn bekijken](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Selecteer **Foutopsporing,** voer de **parameters**in en selecteer **Voltooien**.

    ![Klik op **Foutopsporing**](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. U ziet resultaten die vergelijkbaar zijn met het volgende voorbeeld:

    ![Bekijk het resultaat](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Voor de sjabloon om alleen gewijzigde bestanden te kopiëren van Amazon S3 naar Azure Data Lake Storage Gen2

1. Maak een besturingselementtabel in Azure SQL Database om de partitielijst met AWS S3 op te slaan. 

    > [!NOTE]
    > De tabelnaam is s3_partition_delta_control_table.
    > Het schema van de beheertabel is PartitionPrefix, JobRunTime en SuccessOrFailure, waarbij PartitionPrefix de voorvoegselinstelling is in S3 om de mappen en bestanden in Amazon S3 op naam te filteren, JobRunTime de datumtijdwaarde bij het uitvoeren van kopieertaken en SuccessOrFailure de status is van het kopiëren van elke partitie: 0 betekent dat deze partitie niet is gekopieerd naar Azure en 1 betekent dat deze partitie is gekopieerd naar Azure.
    > Er zijn 5 partities gedefinieerd in de controletabel. De standaardwaarde voor JobRunTime kan het moment zijn waarop eenmalige historische gegevensmigratie wordt gestart. ADF-kopieeractiviteit kopieert de bestanden op AWS S3 die voor het laatst zijn gewijzigd na die tijd. De standaardstatus van het kopiëren van elke partitie is 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Maak een opgeslagen procedure in dezelfde Azure SQL-database voor beheertabel. 

    > [!NOTE]
    > De naam van de opgeslagen procedure is sp_insert_partition_JobRunTime_success. Het wordt aangeroepen door SqlServerStoredProcedure-activiteit in uw ADF-pijplijn.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Ga naar de sjabloon **Deltagegevens kopiëren van AWS S3 naar Azure Data Lake Storage Gen2.** Voer de verbindingen in met uw externe controletabel, AWS S3 als gegevensbronarchief en Azure Data Lake Storage Gen2 als bestemmingsarchief. Houd er rekening mee dat de externe controletabel en de opgeslagen procedure verwijzen naar dezelfde verbinding.

    ![Een nieuwe verbinding maken](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Selecteer **Deze sjabloon gebruiken**.

    ![Deze sjabloon gebruiken](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. U ziet de 2 pijplijnen en 3 gegevenssets zijn gemaakt, zoals weergegeven in het volgende voorbeeld:

    ![De pijplijn bekijken](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Selecteer **Foutopsporing,** voer de **parameters**in en selecteer **Voltooien**.

    ![Klik op **Foutopsporing**](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. U ziet resultaten die vergelijkbaar zijn met het volgende voorbeeld:

    ![Bekijk het resultaat](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. U de resultaten uit de besturingselementtabel ook controleren met een query *'selecteer * uit s3_partition_delta_control_table',* u ziet de uitvoer vergelijkbaar met het volgende voorbeeld:

    ![Bekijk het resultaat](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Volgende stappen

- [Bestanden kopiëren vanuit meerdere containers](solution-template-copy-files-multiple-containers.md)
- [Bestanden verplaatsen](solution-template-move-files.md)