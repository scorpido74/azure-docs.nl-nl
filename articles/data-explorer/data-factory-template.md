---
title: In bulk kopiëren van een database naar Azure Data Explorer met behulp van de sjabloon Azure Data Factory
description: In dit artikel leert u een Azure Data Factory-sjabloon te gebruiken om in bulk uit een database naar Azure Data Explorer te kopiëren
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293552"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>In bulk kopiëren van een database naar Azure Data Explorer met behulp van de sjabloon Azure Data Factory 

Azure Data Explorer is een snelle, volledig beheerde, data-analytics service. Het biedt real-time analyse van grote hoeveelheden gegevens die uit vele bronnen worden gestreamd, zoals toepassingen, websites en IoT-apparaten. 

Als u gegevens uit een database in Oracle Server, Netezza, Teradata of SQL Server wilt kopiëren naar Azure Data Explorer, moet u enorme hoeveelheden gegevens uit meerdere tabellen laden. Meestal moeten de gegevens in elke tabel worden verdeeld, zodat u rijen met meerdere threads parallel vanuit één tabel laden. In dit artikel wordt een sjabloon beschreven die in deze scenario's moet worden gebruikt.

[Azure Data Factory-sjablonen](/azure/data-factory/solution-templates-introduction) zijn vooraf gedefinieerde Data Factory-pijplijnen. Met deze sjablonen u snel aan de slag met Data Factory en de ontwikkeltijd voor data-integratieprojecten verkorten. 

U maakt de *sjabloon Bulk copy van Database naar Azure Data Explorer* met behulp van *Opzoek-* en *ForEach-activiteiten.* Voor sneller kopiëren van gegevens u de sjabloon gebruiken om veel pijplijnen per database of per tabel te maken. 

> [!IMPORTANT]
> Zorg ervoor dat u het hulpprogramma gebruikt dat geschikt is voor de hoeveelheid gegevens die u wilt kopiëren.
> * Gebruik de *sjabloon Bulk copy van Database naar Azure Data Explorer* om grote hoeveelheden gegevens uit databases zoals SQL Server en Google BigQuery naar Azure Data Explorer te kopiëren. 
> * Gebruik het [*hulpprogramma Gegevenskopiëren gegevens in*](data-factory-load-data.md) de gegevensfabriek om een paar tabellen met kleine of matige hoeveelheden gegevens te kopiëren naar Azure Data Explorer. 

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* [Een Azure Data Explorer-cluster en -database](create-cluster-database-portal.md).
* [Maak een gegevensfabriek](data-factory-load-data.md#create-a-data-factory).
* Een bron van gegevens in een database.

## <a name="create-controltabledataset"></a>ControlTableDataset maken

*ControlTableDataset* geeft aan welke gegevens worden gekopieerd van de bron naar de bestemming in de pijplijn. Het aantal rijen geeft het totale aantal pijplijnen aan dat nodig is om de gegevens te kopiëren. U moet ControlTableDataset definiëren als onderdeel van de brondatabase.

Een voorbeeld van de SQL Server-brontabelindeling wordt weergegeven in de volgende code:
    
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
|PartitionId   |  De kopievolgorde | 1  |  
|SourceQuery   |  De query die aangeeft welke gegevens worden gekopieerd tijdens de runtime van de pijplijn | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  De naam van de doeltabel | MyAdxTable       |  

Als uw ControlTableDataset een andere indeling heeft, maakt u een vergelijkbare ControlTableDataset voor uw indeling.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>De sjabloon Bulkcopy van Database naar Azure Data Explorer gebruiken

1. Selecteer in het deelvenster **Laten we aan de slag** de optie Pijplijn maken van **sjabloon** om het deelvenster **Galerie sjabloon** te openen.

    ![Het deelvenster Azure Data Factory "Laten we aan de slag gaan"](media/data-factory-template/adf-get-started.png)

1. Selecteer de sjabloon **Bulkcopy van Database naar Azure Data Explorer.**
 
    ![De sjabloon 'Bulkkopiëren van database naar Azure Data Explorer'](media/data-factory-template/pipeline-from-template.png)

1.  Geef in het deelvenster **Bulkkopie van Database naar Azure Data Explorer** onder **Gebruikersinvoer**uw gegevenssets op door het volgende te doen: 

    a. Selecteer in de vervolgkeuzelijst **ControlTableDataset** de gekoppelde service aan de controletabel die aangeeft welke gegevens van de bron naar de bestemming worden gekopieerd en waar deze op de bestemming worden geplaatst. 

    b. Selecteer in de vervolgkeuzelijst **SourceDataset** de gekoppelde service aan de brondatabase. 

    c. Selecteer de tabel Azure Data Explorer in de vervolgkeuzelijst **AzureDataExplorerTable.** Als de gegevensset niet bestaat, [maakt u de gekoppelde Azure Data Explorer-service](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) om de gegevensset toe te voegen.

    d. Selecteer **Deze sjabloon gebruiken**.

    ![Het deelvenster 'Bulkkopiëren van database naar Azure Data Explorer'](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Selecteer een gebied in het canvas, buiten de activiteiten, om toegang te krijgen tot de sjabloonpijplijn. Selecteer het tabblad **Parameters** om de parameters voor de tabel in te voeren, inclusief **Naam** (regeltabelnaam) en **Standaardwaarde** (kolomnamen).

    ![Pijplijnparameters](media/data-factory-template/pipeline-parameters.png)

1.  Selecteer **Onder Opzoeken**de optie **GetPartitionList** om de standaardinstellingen weer te geven. De query wordt automatisch gemaakt.
1.  Selecteer de opdrachtactiviteit **ForEachPartition,** selecteer het tabblad **Instellingen** en ga als volgt te werk:

    a. Voer in het vak **Batchtelling** een getal in van 1 tot 50. Deze selectie bepaalt het aantal pijplijnen dat parallel loopt totdat het aantal *controltabledataset-rijen* is bereikt. 

    b. Schakel het selectievakje **Sequentieel** *niet* in om ervoor te zorgen dat de pijplijnbatches parallel lopen.

    ![ForEachPartition-instellingen](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Het beste is om veel pijplijnen parallel uit te voeren, zodat uw gegevens sneller kunnen worden gekopieerd. Als u de efficiëntie wilt verhogen, partitieert u de gegevens in de brontabel en wijst u één partitie per pijplijn toe, afhankelijk van datum en tabel.

1. Selecteer **Alles valideren** om de azure data factory-pijplijn te valideren en bekijk het resultaat in het deelvenster **Pijplijnvalidatieuitvoer.**

    ![Sjabloonpijplijnen valideren](media/data-factory-template/validate-template-pipelines.png)

1. Selecteer indien nodig **Foutopsporing**en selecteer **Trigger toevoegen** om de pijplijn uit te voeren.

    ![De knoppen 'Foutopsporing' en 'Pijplijn uitvoeren'](media/data-factory-template/trigger-run-of-pipeline.png)    

U de sjabloon nu gebruiken om grote hoeveelheden gegevens uit uw databases en tabellen efficiënt te kopiëren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [kopiëren van gegevens naar Azure Data Explorer met Azure Data Factory](data-factory-load-data.md).
* Meer informatie over de [Azure Data Explorer-connector](/azure/data-factory/connector-azure-data-explorer) in Azure Data Factory.
* Meer informatie over [Azure Data Explorer-query's](/azure/data-explorer/web-query-data) voor gegevensquery's.






