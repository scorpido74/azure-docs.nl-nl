---
title: Gegevens van Azure SQL Database rand synchroniseren met behulp van Azure Data Factory | Microsoft Docs
description: Meer informatie over het synchroniseren van gegevens tussen Azure SQL Database Edge en Azure Blob-opslag
keywords: SQL data base Edge, gegevens synchroniseren vanuit SQL data base Edge, SQL data base Edge data factory
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: e6fd9e6431137708ba93328a8ed1359b93b4ee1f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74851686"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Zelf studie: gegevens van SQL Database Edge synchroniseren met Azure Blob-opslag met behulp van Azure Data Factory

In deze zelf studie gebruikt u Azure Data Factory om gegevens incrementeel te synchroniseren met Azure Blob-opslag vanuit een tabel in een exemplaar van Azure SQL Database Edge.

## <a name="before-you-begin"></a>Voordat u begint

Als u nog geen data base of tabel hebt gemaakt in de implementatie van de Azure SQL Database Edge, gebruikt u een van de volgende methoden om er een te maken:

* Gebruik [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) of [Azure Data Studio](/sql/azure-data-studio/download/) om verbinding te maken met SQL database Edge. Voer een SQL-script uit om de data base en tabel te maken.
* Maak een SQL database en tabel met behulp van [Sqlcmd](/sql/tools/sqlcmd-utility/) door rechtstreeks verbinding te maken met de module SQL database Edge. Zie [verbinding maken met de data base-engine met behulp van Sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/)voor meer informatie.
* Gebruik SQLPackage. exe om een DAC-pakket bestand te implementeren naar de SQL Database Edge-container. U kunt dit proces automatiseren door de URI van het SqlPackage-bestand op te geven als onderdeel van de configuratie van de gewenste eigenschappen van de module. U kunt ook rechtstreeks het client hulpprogramma SqlPackage. exe gebruiken om een DAC-pakket te implementeren naar SQL Database Edge.

    Zie [SqlPackage downloaden en installeren](/sql/tools/sqlpackage-download/)voor meer informatie over het downloaden van SqlPackage. exe. Hier volgen enkele voor beelden van opdrachten voor SqlPackage. exe. Zie de documentatie van SqlPackage. exe voor meer informatie.

    **Een DAC-pakket maken**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Een DAC-pakket Toep assen**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Een SQL-tabel en-procedure maken om het watermerk niveau op te slaan en bij te werken

Er wordt een watermerk tabel gebruikt om de laatste tijds tempel op te slaan waarmee de gegevens al zijn gesynchroniseerd met Azure Storage. Een opgeslagen Transact-SQL (T-SQL)-procedure wordt gebruikt om de watermerk tabel na elke synchronisatie bij te werken.

Voer deze opdrachten uit op het SQL Database rand exemplaar:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Een Data Factory pijp lijn maken

In deze sectie maakt u een Azure Data Factory pijp lijn om gegevens te synchroniseren met Azure Blob-opslag vanuit een tabel in Azure SQL Database Edge.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Een data factory maken met behulp van de Data Factory gebruikers interface

Maak een data factory door de instructies in [deze zelf studie](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)te volgen.

### <a name="create-a-data-factory-pipeline"></a>Een Data Factory pijp lijn maken

1. Selecteer op de pagina **aan de slag** van de Data Factory gebruikers interface de optie **pijp lijn maken**.

    ![Een Data Factory pijp lijn maken](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Voer op de pagina **Algemeen** van het venster **Eigenschappen** voor de pijp lijn **PeriodicSync** in als naam.

3. Voeg de opzoek activiteit toe om de oude watermerk waarde op te halen. Vouw in het deel venster **activiteiten** het item **Algemeen** uit en sleep de **lookup** -activiteit naar het ontwerp oppervlak voor pijp lijnen. Wijzig de naam van de activiteit in **OldWatermark**.

    ![De oude watermerk zoekactie toevoegen](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Ga naar het tabblad **instellingen** en selecteer **Nieuw** voor **bron gegevensset**. U maakt nu een gegevensset om gegevens in de watermerk tabel weer te geven. Deze tabel bevat de oude grenswaarde die is gebruikt in de vorige kopieerbewerking.

5. Selecteer in het venster **nieuwe gegevensset** de optie **Azure SQL Server**en selecteer vervolgens **door gaan**.  

6. Voer in het venster **set Properties** voor de gegevensset onder **naam** **water Mark dataset**in.

7. Voor **gekoppelde service**selecteert u **Nieuw**en voert u de volgende stappen uit:

    1. Voer onder **naam** **SQLDBEdgeLinkedService**in.

    2. Voer bij **Server naam**uw SQL database Edge-Server Details in.

    3. Selecteer de **naam van uw data base** in de lijst.

    4. Voer uw **gebruikers naam** en **wacht woord**in.

    5. Selecteer **verbinding testen**om de verbinding met het SQL database rand exemplaar te testen.

    6. Selecteer **Maken**.

    ![Een gekoppelde service maken](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Selecteer **OK**.

8. Selecteer op het tabblad **instellingen** de optie **bewerken**.

9. Selecteer [dbo] op het tabblad **verbinding** **. [ watermarktable]** voor de **tabel**. Als u de gegevens in de tabel wilt bekijken, selecteert u **voorbeeld gegevens**.

10. Schakel over naar de pijplijn editor door het tabblad pijp lijn bovenaan te selecteren of door de naam van de pijp lijn in de structuur weergave aan de linkerkant te selecteren. Bevestig in het venster Eigenschappen voor de opzoek activiteit dat **water Mark dataset** is geselecteerd in de lijst **bron gegevensset** .

11. Vouw in het deel venster **activiteiten** het item **Algemeen** uit en sleep een andere **opzoek** activiteit naar het ontwerp oppervlak voor pijp lijnen. Stel de naam in op **NewWatermark** op het tabblad **Algemeen** van het venster Eigenschappen. Met deze opzoek activiteit wordt de nieuwe watermerk waarde opgehaald uit de tabel die de bron gegevens bevat, zodat deze kan worden gekopieerd naar de bestemming.

12. Ga in het venster Eigenschappen voor de tweede opzoek activiteit naar het tabblad **instellingen** en selecteer **Nieuw** om een gegevensset te maken die verwijst naar de bron tabel die de nieuwe watermerk waarde bevat.

13. Selecteer in het venster **nieuwe gegevensset** de optie **SQL database rand instantie**en selecteer vervolgens **door gaan**.

    1. Voer in het venster **set Properties** onder **naam** **Source dataset**in. Onder **gekoppelde service**selecteert u **SQLDBEdgeLinkedService**.

    2. Selecteer onder **tabel**de tabel die u wilt synchroniseren. U kunt ook een query opgeven voor deze gegevensset, zoals verderop in deze zelf studie wordt beschreven. De query heeft voor rang op de tabel die u in deze stap opgeeft.

    3. Selecteer **OK**.

14. Schakel over naar de pijplijn editor door het tabblad pijp lijn bovenaan te selecteren of door de naam van de pijp lijn in de structuur weergave aan de linkerkant te selecteren. Bevestig in het venster Eigenschappen voor de opzoek activiteit dat **Source dataset** is geselecteerd in de lijst **bron gegevensset** .

15. Selecteer **query** onder **query gebruiken**. Werk de tabel naam bij in de volgende query en voer vervolgens de query in. U selecteert alleen de maximum waarde van `timestamp` in de tabel. Zorg ervoor dat u **alleen de eerste rij**selecteert.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![query selecteren](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. Vouw in het deel venster **activiteiten** het item **verplaatsen & transformeren** uit en sleep de **Kopieer** activiteit vanuit het deel venster **activiteiten** naar het ontwerp oppervlak. Stel de naam van de activiteit in op **IncrementalCopy**.

17. Verbind beide opzoekactiviteiten met de kopieeractiviteit door de groene knop die is gekoppeld aan de opzoekactiviteiten, naar de kopieeractiviteit te slepen. Laat de muis knop los als u ziet dat de rand kleur van de Kopieer activiteit is gewijzigd in blauw.

18. Selecteer de kopieeractiviteit en controleer of de eigenschappen voor de activiteit worden weergegeven in het venster **Eigenschappen**.

19. Ga naar het tabblad **bron** in het venster **Eigenschappen** en voer de volgende stappen uit:

    1. Selecteer in het vak **bron gegevensset** de optie **Source dataset**.

    2. Selecteer onder **query gebruiken**de optie **query**.

    3. Voer de SQL-query in het **query** -vak in. Hier volgt een voor beeld van een query:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Op het tabblad **sink** selecteert u **Nieuw** onder **sink-gegevensset**.

21. In deze zelf studie is het sink-gegevens archief een Azure Blob Storage-gegevens archief. Selecteer **Azure Blob-opslag**en selecteer vervolgens **door gaan** in het venster **nieuwe gegevensset** .

22. Selecteer in het venster **indeling selecteren** de indeling van uw gegevens en selecteer vervolgens **door gaan**.

23. Voer in het venster **set Properties** onder **naam** **SinkDataset**in. Onder **gekoppelde service**selecteert u **Nieuw**. U maakt nu een verbinding (een gekoppelde service) aan uw Azure Blob-opslag.

24. Voer de volgende stappen uit in het venster **nieuwe gekoppelde service (Azure Blob-opslag)** :

    1. Typ **AzureStorageLinkedService**in het vak **naam** .

    2. Selecteer bij **naam van opslag account**het Azure-opslag account voor uw Azure-abonnement.

    3. Test de verbinding en selecteer vervolgens **volt ooien**.

25. Controleer in het venster **set Properties** of **AzureStorageLinkedService** is geselecteerd onder **gekoppelde service**. Selecteer **maken** en **OK**.

26. Selecteer **bewerken**op het tabblad **sink** .

27. Ga naar het tabblad **verbinding** van SinkDataset en voer de volgende stappen uit:

    1. Voer bij **bestandspad** *asdedatasync/incrementalcopy*in, waarbij *asdedatasync* de naam van de BLOB-container is en *incrementalcopy* de mapnaam is. Maak de container als deze niet bestaat, of gebruik de naam van een bestaande. Azure Data Factory maakt automatisch de uitvoermap *incrementalcopy* als deze nog niet bestaat. U kunt ook de knop **Bladeren** voor het **bestandspad** gebruiken om naar een map in een blob-container te navigeren.

    2. Voor het **bestand** deel **van het bestandspad**selecteert u **dynamische inhoud toevoegen [Alt + P]** en voert ** @CONCATu vervolgens (' incremental-', pipeline () in. RunId, '. txt ')** in het venster dat wordt geopend. Selecteer **Finish**. De bestands naam wordt dynamisch gegenereerd door de expressie. Elke pijplijnuitvoering heeft een unieke id. De kopieeractiviteit gebruikt de run-id om de bestandsnaam te genereren.

28. Schakel over naar de pijplijn editor door het tabblad pijp lijn bovenaan te selecteren of door de naam van de pijp lijn in de structuur weergave aan de linkerkant te selecteren.

29. Vouw in het deel venster **activiteiten** het item **Algemeen** uit en sleep de activiteit **opgeslagen procedure** van het deel venster **activiteiten** naar het ontwerp oppervlak voor pijp lijnen. De groene (geslaagde) uitvoer van de Kopieer activiteit verbinden met de opgeslagen procedure activiteit.

30. Selecteer **opgeslagen procedure-activiteit** in de pijp lijn Designer en wijzig de naam ervan in **SPtoUpdateWatermarkActivity**.

31. Ga naar het tabblad **SQL-account** en selecteer ***QLDBEdgeLinkedService** onder **gekoppelde service**.

32. Ga naar het tabblad **opgeslagen procedure** en voer de volgende stappen uit:

    1. Selecteer onder **naam opgeslagen procedure**de optie **[dbo]. [ usp_write_watermark]**.

    2. Als u waarden wilt opgeven voor de opgeslagen procedure parameters, selecteert u **para meter importeren** en voert u deze waarden in voor de para meters:

    |Naam|Type|Waarde|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {activity (' NewWaterMark '). output. firstRow. NewWatermarkvalue}|
    |TableName|Tekenreeks|@ {activity (' OldWaterMark '). output. firstRow. TableName}|

33. Selecteer **valideren** op de werk balk om de instellingen voor de pijp lijn te valideren. Controleer of er geen validatiefouten zijn. Selecteer **>>** om het venster **pijplijn validatie rapport** te sluiten.

34. Publiceer de entiteiten (gekoppelde services, gegevens sets en pijp lijnen) naar de Azure Data Factory-service door de knop **Alles publiceren** te selecteren. Wacht totdat er een bericht wordt weer gegeven waarin wordt bevestigd dat de publicatie is voltooid.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Een pijp lijn activeren op basis van een planning

1. Selecteer **trigger toevoegen**op de werk balk van de pijp lijn, selecteer **Nieuw/bewerken**en selecteer vervolgens **Nieuw**.

2. Geef de trigger de naam **HourlySync**. Onder **type**, selecteer **planning**. Stel het **terugkeer patroon** in op elke 1 uur.

3. Selecteer **OK**.

4. Selecteer **Alles publiceren**.

5. Selecteer **nu activeren**.

6. Ga naar het tabblad **Controleren** aan de linkerkant. U kunt de status van de pijplijnuitvoering zien die is geactiveerd met de handmatige trigger. Selecteer **Vernieuwen** om de lijst te vernieuwen.

## <a name="next-steps"></a>Volgende stappen

Met de Azure Data Factory-pijp lijn in deze zelf studie worden gegevens van een tabel op een SQL Database Edge-exemplaar één keer per uur gekopieerd naar een locatie in Azure Blob Storage. Zie deze [zelf studies](../data-factory/tutorial-copy-data-portal.md)voor meer informatie over het gebruik van Data Factory in andere scenario's.
