---
title: Bulkkopie uit een database met de controletabel
description: Meer informatie over het gebruik van een oplossingssjabloon om bulkgegevens uit een database te kopiëren met behulp van een externe controletabel om een partitielijst met brontabellen op te slaan met Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: 3a42d7da21cfb2e3066fbdd81b27c82155d8456f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439913"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Bulkkopie uit een database met een controletabel

Als u gegevens uit een gegevensmagazijn in Oracle Server, Netezza, Teradata of SQL Server wilt kopiëren naar Azure SQL Data Warehouse, moet u enorme hoeveelheden gegevens uit meerdere tabellen laden. Meestal moeten de gegevens in elke tabel worden verdeeld, zodat u rijen met meerdere threads parallel vanuit één tabel laden. In dit artikel wordt een sjabloon beschreven die in deze scenario's moet worden gebruikt.

 >! OPMERKING Als u gegevens uit een klein aantal tabellen met een relatief klein gegevensvolume naar SQL Data Warehouse wilt kopiëren, is het efficiënter om het [hulpprogramma voor kopiëren met Azure Data Factory Copy Data](copy-data-tool.md)te gebruiken. De sjabloon die in dit artikel wordt beschreven, is meer dan u nodig hebt voor dat scenario.

## <a name="about-this-solution-template"></a>Over deze oplossingssjabloon

Met deze sjabloon wordt een lijst met brondatabasepartities opgehaald die u wilt kopiëren uit een externe controletabel. Vervolgens wordt elke partitie in de brondatabase over deze partitie heen getint en worden de gegevens naar de bestemming kopieën gemaakt.

De sjabloon bevat drie activiteiten:
- **Lookup** haalt de lijst met bepaalde databasepartities op uit een externe controletabel.
- **ForEach** krijgt de partitielijst van de opzoekactiviteit en vereert elke partitie naar de activiteit Kopiëren.
- **Kopieer** kopieën van elke partitie van het brondatabasearchief naar het doelarchief.

De sjabloon definieert de volgende parameters:
- *Control_Table_Name* is uw externe controletabel, die de partitielijst voor de brondatabase opslaat.
- *Control_Table_Schema_PartitionID* is de naam van de kolomnaam in de externe besturingselementtabel die elke partitie-id opslaat. Zorg ervoor dat de partitie-id uniek is voor elke partitie in de brondatabase.
- *Control_Table_Schema_SourceTableName* is uw externe controletabel die elke tabelnaam opslaat in de brondatabase.
- *Control_Table_Schema_FilterQuery* is de naam van de kolom in de externe besturingselementtabel die de filterquery opslaat om de gegevens uit elke partitie in de brondatabase te krijgen. Als u de gegevens bijvoorbeeld per jaar hebt verdeeld, kan de query die in elke rij is opgeslagen, vergelijkbaar zijn met 'selecteren * uit gegevensbron waar LastModifytime >= ''2015-01-01 00:00:00'' en LastModifytime <= ''2015-12-31 23:59:59.999'''.
- *Data_Destination_Folder_Path* is het pad waarop de gegevens worden gekopieerd naar uw bestemmingsarchief (van toepassing wanneer de bestemming die u kiest 'Bestandssysteem' of 'Azure Data Lake Storage Gen1' is). 
- *Data_Destination_Container* is het pad van de hoofdmap waarnaar de gegevens worden gekopieerd in uw bestemmingsarchief. 
- *Data_Destination_Directory* is het mappad onder de hoofdmap waar de gegevens naar uw bestemmingsarchief worden gekopieerd. 

De laatste drie parameters, die het pad in uw bestemmingsarchief definiëren, zijn alleen zichtbaar als de bestemming die u kiest opslag op basis van bestanden is. Als u 'Azure Synapse Analytics (voorheen SQL DW)' als bestemmingsarchief kiest, zijn deze parameters niet vereist. Maar de tabelnamen en het schema in SQL Data Warehouse moeten dezelfde zijn als die in de brondatabase.

## <a name="how-to-use-this-solution-template"></a>Deze oplossingssjabloon gebruiken

1. Maak een besturingselementtabel in SQL Server of Azure SQL Database om de lijst met brondatabasepartities op te slaan voor bulkkopiëren. In het volgende voorbeeld zijn er vijf partities in de brondatabase. Drie partities zijn voor de *datasource_table*, en twee zijn voor de *project_table*. De kolom *LastModifytime* wordt gebruikt om de gegevens in tabel *datasource_table* uit de brondatabase te verdelen. De query die wordt gebruikt om de eerste partitie te lezen is 'select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' en LastModifytime <= ''2015-12-31 23:59:59.999''. U een vergelijkbare query gebruiken om gegevens uit andere partities te lezen.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Ga naar de sjabloon **Bulkkopiëren uit database.** Maak een **nieuwe** verbinding met de externe controletabel die u in stap 1 hebt gemaakt.

    ![Een nieuwe verbinding maken met de controletabel](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Maak een **nieuwe** verbinding met de brondatabase waarvan u gegevens kopieert.

    ![Een nieuwe verbinding met de brondatabase maken](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Maak een **nieuwe** verbinding met het doelgegevensarchief waarnaar u de gegevens kopieert.

    ![Een nieuwe verbinding maken met het doelarchief](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selecteer **Deze sjabloon gebruiken**.

6. U ziet de pijplijn, zoals in het volgende voorbeeld wordt weergegeven:

    ![De pijplijn bekijken](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selecteer **Foutopsporing,** voer de **parameters**in en selecteer **Voltooien**.

    ![Klik op **Foutopsporing**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. U ziet resultaten die vergelijkbaar zijn met het volgende voorbeeld:

    ![Bekijk het resultaat](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Optioneel) Als u 'Azure Synapse Analytics (voorheen SQL DW)' als gegevensbestemming hebt gekozen, moet u een verbinding met Azure Blob-opslag invoeren voor fasering, zoals vereist door SQL Data Warehouse Polybase. De sjabloon genereert automatisch een containerpad voor uw Blob-opslag. Controleer of de container is gemaakt nadat de pijplijn is uitgevoerd.
    
    ![Polybase-instelling](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Data Factory](introduction.md)
