---
title: Azure Data Factory sjabloon gebruiken voor bulksgewijs kopiëren van de Data Base naar Azure Data Explorer
description: In dit onderwerp leert u hoe u een Azure Data Factory sjabloon gebruikt voor het bulksgewijs kopiëren van een Data Base naar Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: acfcee6e8308bc508bd709c40bd1794beab3130f
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804728"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>Azure Data Factory sjabloon gebruiken voor bulksgewijs kopiëren van de Data Base naar Azure Data Explorer

Azure Data Explorer is een snelle, volledig beheerde gegevens analyse service voor realtime-analyse van grote hoeveel heden gegevensstreaming van veel bronnen, zoals toepassingen, websites en IoT-apparaten. Azure Data Factory is een volledig beheerde service voor gegevens integratie in de Cloud. U kunt de Azure Data Factory-service gebruiken om uw Azure Data Explorer-data base te vullen met gegevens van uw bestaande systeem en tijd te besparen bij het bouwen van uw analyse oplossingen. 

[Azure Data Factory sjablonen](/azure/data-factory/solution-templates-introduction) zijn vooraf gedefinieerde Azure Data Factory pijp lijnen waarmee u snel aan de slag kunt met Data Factory en de ontwikkelings tijd voor het maken van gegevens integratie projecten kunt verminderen. De **bulk kopie van de Data Base naar Azure Data Explorer** sjabloon is gemaakt met behulp van **lookup** -en **foreach** -activiteiten. U kunt de sjabloon gebruiken om een groot aantal pijp lijnen per data base of tabel te maken voor het sneller kopiëren van gegevens. 

> [!IMPORTANT]
> * Gebruik de sjabloon **bulksgewijs kopiëren van Data Base naar azure Data Explorer** om grote hoeveel heden gegevens uit data bases zoals SQL Server en Google BigQuery naar Azure Data Explorer te kopiëren. 
> * Gebruik het [hulp programma kopiëren](data-factory-load-data.md) om enkele tabellen te kopiëren met kleine of gemiddelde hoeveel heden gegevens naar Azure Data Explorer. 

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Een Azure Data Explorer-cluster en-data base](create-cluster-database-portal.md)
* [Een data factory maken](data-factory-load-data.md#create-a-data-factory)
* Bron van de gegevens in de data base

## <a name="create-controltabledataset"></a>ControlTableDataset maken

De **ControlTableDataset** geeft aan welke gegevens worden gekopieerd van de bron naar het doel in de pijp lijn. Het aantal rijen geeft het totale aantal pijp lijnen aan dat nodig is om de gegevens te kopiëren. De **ControlTableDataset** moet worden gedefinieerd als onderdeel van de bron database.

Voor beeld van SQL Server indeling van de bron tabel:
    
    ```sql   
    CREATE TABLE control_table (
        PartitionId int,
        SourceQuery varchar(255),
        ADXTableName varchar(255)
    );
    ```
    
        |Eigenschap  |Description  | Voorbeeld
        |---------|---------| ---------|
        |PartitionId   |   bestelling Kopiëren | 1  |  
        |SourceQuery   |   query die aangeeft welke gegevens tijdens de pijplijn runtime worden gekopieerd | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
        |ADXTableName  |  doel tabel naam | MyAdxTable       |  

Als uw **ControlTableDataset** zich in een andere indeling bevindt, maakt u een vergelijk bare **ControlTableDataset** voor uw indeling.

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>Bulk kopie van Data Base naar Azure Data Explorer-sjabloon gebruiken

1. Op de **pagina aan de slag** selecteert u de tegel **pijp lijn maken op basis van sjabloon** of selecteert u het potlood pictogram (tabblad**Auteur** ) aan de rechter **+** >  >  **pijp lijn van sjabloon** om de sjabloon galerie te openen.

    ![Azure Data Factory laten we aan de slag](media/data-factory-template/adf-get-started.png)

1. Selecteer sjabloon **bulksgewijs kopiëren van Data Base naar Azure Data Explorer**.
 
    ![Pijp lijn selecteren op basis van sjabloon](media/data-factory-template/pipeline-from-template.png)

1.  Selecteer in het venster **bulksgewijs kopiëren van Data Base naar Azure Data Explorer** bestaande gegevens sets in de vervolg keuzelijst. 

    * Een **ControlTableDataset** -gekoppelde service voor het beheren van de tabel die aangeeft welke gegevens van de bron naar de bestemming worden gekopieerd en waar deze in het doel moeten worden geplaatst. 
    * **Source dataset** : gekoppelde service naar de bron database. 
    * **AzureDataExplorerTable** -Azure Data Explorer-tabel. Als de gegevensset niet bestaat, [maakt u de gekoppelde Azure Data Explorer-Service](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) om de gegevensset toe te voegen.
    * Selecteer **deze sjabloon gebruiken**.

    ![Azure Data Explorer-sjabloon voor bulksgewijs kopiëren configureren](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Selecteer een gebied in het canvas, buiten de activiteiten, om toegang te krijgen tot de sjabloon pijplijn. Selecteer **para meters** om de para meters voor de tabel op te geven, inclusief de **naam** (naam van de beheer tabel) en de **standaard waarde** (kolom namen).

    ![Pijplijnparameters](media/data-factory-template/pipeline-parameters.png)

1.  Selecteer lookup-activiteit, **GetPartitionList**om de standaard instellingen weer te geven. De query wordt automatisch gemaakt.
1.  Selecteer de opdracht activiteit **ForEachPartition**, selecteer **instellingen**
    * **Batch-aantal**: Selecteer getal van 1-50. Deze selectie bepaalt het aantal pijp lijnen dat parallel wordt uitgevoerd totdat het **ControlTableDataset** aantal rijen is bereikt. 
    * Schakel het selectie vakje **OPEENVOLGEND** niet in zodat de pijplijn batches parallel worden uitgevoerd.

    ![ForEachPartition-instellingen](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > De aanbevolen procedure is om een groot aantal pijp lijnen parallel uit te voeren, zodat gegevens sneller kunnen worden gekopieerd. Partitioneer de gegevens in de bron tabel en wijs een partitie per pijp lijn toe, volgens de datum en tabel, om de efficiëntie te verbeteren.

1. Selecteer **Alles valideren** om de ADF-pijp lijn te valideren. Zie de validatie-uitvoer van de **pijp lijn**.

    ![Sjabloon pijplijnen valideren](media/data-factory-template/validate-template-pipelines.png)

1. Selecteer **fouten opsporen**, indien nodig, en voeg vervolgens een **trigger** toe om de pijp lijn uit te voeren.

    ![Pijp lijn uitvoeren](media/data-factory-template/trigger-run-of-pipeline.png)    


U kunt nu de sjabloon **bulksgewijs kopiëren van Data Base naar Azure Data Explorer** gebruiken om op efficiënte wijze grote hoeveel heden gegevens uit uw data bases en tabellen te kopiëren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de procedure voor het [kopiëren van gegevens naar Azure Data Explorer met behulp van Azure Data Factory](data-factory-load-data.md).

* Meer informatie over de [Azure Data Explorer-connector](/azure/data-factory/connector-azure-data-explorer) in azure Data Factory.

* Meer informatie over [Azure Data Explorer query's](/azure/data-explorer/web-query-data) voor het uitvoeren van gegevens query's.






