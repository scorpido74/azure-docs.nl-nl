---
title: Bulksgewijs kopiëren van een Data Base naar Azure Data Explorer met behulp van de Azure Data Factory sjabloon
description: In dit artikel leert u hoe u een Azure Data Factory sjabloon kunt gebruiken om bulksgewijs te kopiëren van een Data Base naar Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293552"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Bulksgewijs kopiëren van een Data Base naar Azure Data Explorer met behulp van de Azure Data Factory sjabloon 

Azure Data Explorer is een snelle, volledig beheerde service voor gegevens analyse. Het biedt real-time analyse van grote hoeveel heden gegevens die streamen van veel bronnen, zoals toepassingen, websites en IoT-apparaten. 

Als u gegevens wilt kopiëren van een data base in Oracle Server, Netezza, Teradata of SQL Server naar Azure Data Explorer, moet u grote hoeveel heden gegevens uit meerdere tabellen laden. Normaal gesp roken moeten de gegevens in elke tabel worden gepartitioneerd, zodat u rijen met meerdere threads parallel vanuit één tabel kunt laden. In dit artikel wordt een sjabloon beschreven voor gebruik in deze scenario's.

[Azure Data Factory sjablonen](/azure/data-factory/solution-templates-introduction) zijn vooraf gedefinieerde Data Factory pijp lijnen. Deze sjablonen kunnen u helpen om snel aan de slag te gaan met Data Factory en de ontwikkelings tijd voor gegevens integratie projecten te reduceren. 

U maakt de *bulk kopie van de Data Base naar Azure Data Explorer* sjabloon met behulp van *lookup* -en *foreach* -activiteiten. Voor het sneller kopiëren van gegevens kunt u de sjabloon gebruiken om een groot aantal pijp lijnen per data base of per tabel te maken. 

> [!IMPORTANT]
> Zorg ervoor dat u het hulp programma gebruikt dat geschikt is voor de hoeveelheid gegevens die u wilt kopiëren.
> * Gebruik de sjabloon *bulksgewijs kopiëren van Data Base naar azure Data Explorer* om grote hoeveel heden gegevens uit data bases zoals SQL Server en Google BigQuery naar Azure Data Explorer te kopiëren. 
> * Gebruik het [*hulp programma Data Factory gegevens kopiëren*](data-factory-load-data.md) om enkele tabellen met kleine of gemiddelde hoeveel heden gegevens naar Azure Data Explorer te kopiëren. 

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Een Azure Data Explorer-cluster en -database](create-cluster-database-portal.md).
* [Maak een Data Factory](data-factory-load-data.md#create-a-data-factory).
* Een bron van gegevens in een Data Base.

## <a name="create-controltabledataset"></a>ControlTableDataset maken

*ControlTableDataset* geeft aan welke gegevens van de bron naar het doel in de pijp lijn worden gekopieerd. Het aantal rijen geeft het totale aantal pijp lijnen aan dat nodig is om de gegevens te kopiëren. U moet ControlTableDataset definiëren als onderdeel van de bron database.

Een voor beeld van de indeling van de SQL Server bron tabel wordt weer gegeven in de volgende code:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

De code-elementen worden beschreven in de volgende tabel:

|Eigenschap  |Beschrijving  | Voorbeeld
|---------|---------| ---------|
|PartitionId   |  De Kopieer volgorde | 1  |  
|SourceQuery   |  De query die aangeeft welke gegevens tijdens de pijplijn runtime worden gekopieerd | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  De naam van de doel tabel | MyAdxTable       |  

Als uw ControlTableDataset zich in een andere indeling bevindt, maakt u een vergelijk bare ControlTableDataset voor uw indeling.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>De sjabloon bulksgewijs kopiëren van Data Base naar Azure Data Explorer gebruiken

1. Selecteer in het deel venster **aan de slag** de optie **pijp lijn maken van sjabloon** om het deel venster **sjabloon galerie** te openen.

    ![Het Azure Data Factory deel venster aan de slag](media/data-factory-template/adf-get-started.png)

1. Selecteer de sjabloon **bulksgewijs kopiëren van Data Base naar Azure Data Explorer** .
 
    ![De sjabloon "bulksgewijs kopiëren van Data Base naar Azure Data Explorer"](media/data-factory-template/pipeline-from-template.png)

1.  Geef in het deel venster **bulksgewijs kopiëren van Data Base naar Azure Data Explorer** onder **gebruikers invoer**de gegevens sets op door het volgende te doen: 

    a. Selecteer in de vervolg keuzelijst **ControlTableDataset** de gekoppelde service voor de controle tabel die aangeeft welke gegevens van de bron naar het doel worden gekopieerd en waar deze in het doel moeten worden geplaatst. 

    b. Selecteer in de vervolg keuzelijst **Source dataset** de gekoppelde service voor de bron database. 

    c. Selecteer in de vervolg keuzelijst **AzureDataExplorerTable** de tabel Azure Data Explorer. Als de gegevensset niet bestaat, [maakt u de gekoppelde Azure Data Explorer-Service](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) om de gegevensset toe te voegen.

    d. Selecteer **Deze sjabloon gebruiken**.

    ![Het deel venster ' bulksgewijs kopiëren van de Data Base naar Azure Data Explorer '](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Selecteer een gebied in het canvas, buiten de activiteiten, om toegang te krijgen tot de sjabloon pijplijn. Selecteer het tabblad **para meters** om de para meters voor de tabel op te geven, met inbegrip van de **naam** (naam van de beheer tabel) en de **standaard waarde** (kolom namen).

    ![Pijplijnparameters](media/data-factory-template/pipeline-parameters.png)

1.  Onder **Opzoeken**selecteert u **GetPartitionList** om de standaard instellingen weer te geven. De query wordt automatisch gemaakt.
1.  Selecteer de opdracht activiteit **ForEachPartition**, selecteer het tabblad **instellingen** en voer de volgende handelingen uit:

    a. Voer in het vak **aantal batches** een getal in van 1 tot en met 50. Deze selectie bepaalt het aantal pijp lijnen dat parallel wordt uitgevoerd totdat het aantal *ControlTableDataset* rijen is bereikt. 

    b. Om ervoor te zorgen dat de pijplijn batches parallel worden uitgevoerd, schakelt u het selectie vakje **opeenvolgend** *niet* in.

    ![ForEachPartition-instellingen](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Het best practice is om een groot aantal pijp lijnen parallel uit te voeren zodat uw gegevens sneller kunnen worden gekopieerd. Als u de efficiëntie wilt verhogen, partitioneert u de gegevens in de bron tabel en wijst u één partitie per pijp lijn toe, volgens de datum en tabel.

1. Selecteer **Alles valideren** om de Azure Data Factory pijp lijn te valideren en bekijk vervolgens het resultaat in het deel venster voor de validatie van de **pijp lijn** .

    ![Sjabloon pijplijnen valideren](media/data-factory-template/validate-template-pipelines.png)

1. Selecteer, indien nodig, **fout opsporing**en selecteer vervolgens **trigger toevoegen** om de pijp lijn uit te voeren.

    ![De knoppen fout opsporing en pijp lijn uitvoeren](media/data-factory-template/trigger-run-of-pipeline.png)    

U kunt nu de sjabloon gebruiken om op efficiënte wijze grote hoeveel heden gegevens uit uw data bases en tabellen te kopiëren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [kopiëren van gegevens naar Azure Data Explorer met behulp van Azure Data Factory](data-factory-load-data.md).
* Meer informatie over de [Azure Data Explorer-connector](/azure/data-factory/connector-azure-data-explorer) in azure Data Factory.
* Meer informatie over [Azure Data Explorer query's](/azure/data-explorer/web-query-data) voor het uitvoeren van gegevens query's.






