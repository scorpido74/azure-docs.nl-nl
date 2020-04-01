---
title: Gegevens van Azure SQL Database Edge synchroniseren met Azure Data Factory | Microsoft Documenten
description: Meer informatie over het synchroniseren van gegevens tussen Azure SQL Database Edge en Azure Blob-opslag
keywords: sql-databaseedge, synchronisatiegegevens van sql-databaseedge, sql-databaseedge-gegevensfabriek
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: e6fd9e6431137708ba93328a8ed1359b93b4ee1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74851686"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Zelfstudie: Gegevens synchroniseren van SQL Database Edge naar Azure Blob-opslag met Azure Data Factory

In deze zelfstudie gebruikt u Azure Data Factory om gegevens stapsgewijs te synchroniseren met Azure Blob-opslag vanuit een tabel in een instantie van Azure SQL Database Edge.

## <a name="before-you-begin"></a>Voordat u begint

Als u nog geen database of tabel hebt gemaakt in uw Azure SQL Database Edge-implementatie, gebruikt u een van deze methoden om er een te maken:

* Gebruik [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) of Azure Data [Studio](/sql/azure-data-studio/download/) om verbinding te maken met SQL Database Edge. Voer een SQL-script uit om de database en tabel te maken.
* Maak een SQL-database en -tabel met [SQLCMD](/sql/tools/sqlcmd-utility/) door rechtstreeks verbinding te maken met de SQL Database Edge-module. Zie [Verbinding maken met de databaseengine met sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/)voor meer informatie.
* Gebruik SQLPackage.exe om een DAC-pakketbestand te implementeren in de SQL Database Edge-container. U dit proces automatiseren door het SqlPackage-bestand URI op te geven als onderdeel van de gewenste eigenschappenconfiguratie van de module. U ook direct de SqlPackage.exe-clienttool gebruiken om een DAC-pakket te implementeren in SQL Database Edge.

    Zie [Sqlpackage](/sql/tools/sqlpackage-download/)downloaden en installeren voor informatie over het downloaden van SqlPackage.exe. Hieronder volgen enkele voorbeeldopdrachten voor SqlPackage.exe. Zie voor meer informatie de sqlpackage.exe-documentatie.

    **Een DAC-pakket maken**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Een DAC-pakket toepassen**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Een SQL-tabel en -procedure maken om de watermerkniveaus op te slaan en bij te werken

Een watermerktabel wordt gebruikt om de laatste tijdstempel op te slaan tot welke gegevens al zijn gesynchroniseerd met Azure Storage. Een door Transact-SQL (T-SQL) opgeslagen procedure wordt gebruikt om de watermerktabel na elke synchronisatie bij te werken.

Voer deze opdrachten uit op de sql-databaserand-instantie:

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

## <a name="create-a-data-factory-pipeline"></a>Een datafabriekpijplijn maken

In deze sectie maakt u een Azure Data Factory-pijplijn om gegevens te synchroniseren met Azure Blob-opslag vanuit een tabel in Azure SQL Database Edge.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Een gegevensfabriek maken met de gebruikersinterface van datafabriek

Maak een gegevensfabriek door de instructies in [deze zelfstudie te volgen.](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)

### <a name="create-a-data-factory-pipeline"></a>Een datafabriekpijplijn maken

1. Selecteer op de pagina **Laten we aan de slag gaan** van de gebruikersinterface van de gegevensfabriek de optie Pijplijn **maken**.

    ![Een datafabriekpijplijn maken](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Voer op de pagina **Algemeen** van het venster **Eigenschappen** voor de pijplijn **PeriodicSync** in voor de naam.

3. Voeg de opzoekactiviteit toe om de oude watermerkwaarde te krijgen. Vouw **in** het deelvenster Activiteiten **Algemeen** uit en sleep de **opzoekactiviteit** naar het oppervlak van de pijplijnontwerper. Wijzig de naam van de activiteit in **OldWatermark**.

    ![Voeg de oude watermerkopzoeking toe](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Ga naar het tabblad **Instellingen** en selecteer **Nieuw** voor **brongegevensset**. U maakt nu een gegevensset om gegevens in de tabel met watermerken weer te geven. Deze tabel bevat de oude grenswaarde die is gebruikt in de vorige kopieerbewerking.

5. Selecteer **Azure SQL Server**in het venster Nieuwe **gegevensset** en selecteer **Doorgaan**.  

6. Voer in het venster **Eigenschappen instellen** voor de gegevensset onder **Naam** **watermerkgegevensset**in .

7. Selecteer **Voor Gekoppelde service** **Nieuw**en voer de volgende stappen uit:

    1. Voer onder **Naam** **SQLDBEdgeLinkedService in**.

    2. Voer **onder Servernaam**de gegevens van de SQL Database Edge-server in.

    3. Selecteer de **naam van uw database** in de lijst.

    4. Voer uw **gebruikersnaam** en **wachtwoord in.**

    5. Als u de verbinding met de SQL Database Edge-instantie wilt testen, selecteert u **Verbinding testen**.

    6. Selecteer **Maken**.

    ![Een gekoppelde service maken](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Selecteer **OK**.

8. Selecteer op het tabblad **Instellingen** de optie **Bewerken**.

9. Selecteer op het tabblad **Verbinding** **[dbo].[ watermarktbaar]** voor **tafel**. Als u een voorbeeld wilt bekijken van gegevens in de tabel, selecteert u **Voorbeeldgegevens**.

10. Ga naar de pijplijneditor door het tabblad pijplijn bovenaan te selecteren of door de naam van de pijplijn te selecteren in de structuurweergave aan de linkerkant. Controleer in het venster Eigenschappen voor de opzoekactiviteit of **WatermarkDataset** is geselecteerd in de lijst **Brongegevensset.**

11. Vouw **in** het deelvenster Activiteiten **Algemeen** uit en sleep een andere **opzoekactiviteit** naar het oppervlak van de pijplijnontwerper. Stel de naam in **op NewWatermark op** het tabblad **Algemeen** van het eigenschappenvenster. Met deze opzoekactiviteit wordt de nieuwe watermerkwaarde opgehaald uit de tabel die de brongegevens bevat, zodat deze naar de bestemming kan worden gekopieerd.

12. Ga in het eigenschappenvenster voor de tweede opzoekactiviteit over naar het tabblad **Instellingen** en selecteer **Nieuw** om een gegevensset te maken die naar de brontabel met de nieuwe watermerkwaarde wijst.

13. Selecteer **sql-databaserandinstantie**in het venster **Nieuwe gegevensset** en selecteer **Doorgaan**.

    1. Voer in het venster **Eigenschappen instellen** onder **Naam** **bronsetgegevens in**. Selecteer **SQLDBEdgeLinkedService**onder **Gekoppelde service**.

    2. Selecteer **onder Tabel**de tabel die u wilt synchroniseren. U ook een query voor deze gegevensset opgeven, zoals later in deze zelfstudie wordt beschreven. De query heeft voorrang op de tabel die u in deze stap opgeeft.

    3. Selecteer **OK**.

14. Ga naar de pijplijneditor door het tabblad pijplijn bovenaan te selecteren of door de naam van de pijplijn te selecteren in de structuurweergave aan de linkerkant. Controleer in het venster Eigenschappen voor de opzoekactiviteit of **SourceDataset** is geselecteerd in de lijst **Brongegevensset.**

15. Selecteer **Query** onder **Query gebruiken**. Werk de tabelnaam in de volgende query bij en voer de query in. U selecteert alleen de maximale waarde van `timestamp` de tabel. Zorg ervoor dat u **alleen de eerste rij**selecteert.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![query selecteren](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. Vouw in het deelvenster **Activiteiten** **Verplaatsen & transformeren** uit en sleep de activiteit **Kopiëren** van het deelvenster **Activiteiten** naar het ontwerpoppervlak. Stel de naam van de activiteit in **op Incrementcopy**.

17. Verbind beide opzoekactiviteiten met de kopieeractiviteit door de groene knop die is gekoppeld aan de opzoekactiviteiten, naar de kopieeractiviteit te slepen. Laat de muisknop los wanneer u de randkleur van de activiteit Kopiëren in blauw ziet wijzigen.

18. Selecteer de kopieeractiviteit en controleer of de eigenschappen voor de activiteit worden weergegeven in het venster **Eigenschappen**.

19. Ga naar het tabblad **Bron** in het venster **Eigenschappen** en voer de volgende stappen uit:

    1. Selecteer **SourceDataset**in het vak **Brongegevensset** .

    2. Selecteer **onder Query gebruiken**de optie **Query**.

    3. Voer de SQL-query in het vak **Query in.** Hier is een voorbeeldquery:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Selecteer op het tabblad **Sink** de optie **Nieuw** onder **Sink Dataset**.

21. In deze zelfstudie is het sink-gegevensarchief een Azure Blob-opslaggegevensarchief. Selecteer **Azure Blob-opslag**en selecteer **Doorgaan** in het venster **Nieuwe gegevensset.**

22. Selecteer in het venster **Opmaak** selecteren de indeling van uw gegevens en selecteer **Doorgaan**.

23. Voer **sinkdataset**in het venster **Eigenschappen instellen** onder **Naam**in . Selecteer Onder **Gekoppelde service**de optie **Nieuw**. U maakt nu een verbinding (een gekoppelde service) met uw Azure Blob-opslag.

24. Voer de volgende stappen uit in het venster **Nieuwe Gekoppelde Service (Azure Blob-opslag):**

    1. Voer **in** het vak Naam **AzureStorageLinkedService**in .

    2. Selecteer **onder Accountnaam Opslag**het Azure-opslagaccount voor uw Azure-abonnement.

    3. Test de verbinding en selecteer **Voltooien**.

25. Controleer in het venster **Eigenschappen instellen** of **AzureStorageLinkedService** is geselecteerd onder **Gekoppelde service**. Selecteer **Maken** en **OK**.

26. Selecteer op het tabblad **Sink** de optie **Bewerken**.

27. Ga naar het tabblad **Verbinding** van SinkDataset en voer de volgende stappen uit:

    1. Voer **onder Bestandspad** *asdedatasync/incrementalcopy*in, waarbij *asdedatasync* de naam blobcontainer is en *incrementele kopie* de mapnaam is. Maak de container als deze niet bestaat of gebruik de naam van een bestaande container. Azure Data Factory maakt automatisch de *incrementele kopie* van de uitvoermap als deze niet bestaat. U kunt ook de knop **Bladeren** voor het **bestandspad** gebruiken om naar een map in een blob-container te navigeren.

    2. Selecteer **Dynamische inhoud toevoegen [Alt+P]** en voer vervolgens ('Incrementeel-', **File** **File path** ** @CONCATpijplijn() in voor het bestandsgedeelte van het bestandspad. RunId, '.txt')** in het venster dat opent. Selecteer **Finish**. De bestandsnaam wordt dynamisch gegenereerd door de expressie. Elke pijplijnuitvoering heeft een unieke id. De kopieeractiviteit gebruikt de run-id om de bestandsnaam te genereren.

28. Ga naar de pijplijneditor door het tabblad pijplijn bovenaan te selecteren of door de naam van de pijplijn te selecteren in de structuurweergave aan de linkerkant.

29. Vouw **in** het deelvenster Activiteiten **Algemeen** uit en sleep de activiteit **Opgeslagen procedure** van het deelvenster **Activiteiten** naar het oppervlak van het pijplijnontwerp. Verbind de groene (succes) uitvoer van de activiteit Kopiëren met de activiteit Opgeslagen procedure.

30. Selecteer **Opgeslagen procedureactiviteit** in de pijplijnontwerper en wijzig de naam in **SPtoUpdateWatermarkActivity**.

31. Schakel over naar het tabblad **SQL-account** en selecteer ***QLDBEdgeLinkedService** onder **Gekoppelde service**.

32. Ga naar het tabblad **Opgeslagen procedure** en voer de volgende stappen uit:

    1. Selecteer onder **De naam van de opgeslagen procedure** **[dbo].[ usp_write_watermark]**.

    2. Als u waarden voor de opgeslagen procedureparameters wilt opgeven, selecteert u **De parameter Importeren** en voert u deze waarden in voor de parameters:

    |Name|Type|Waarde|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|Tekenreeks|@{activiteit('OldWaterMark').output.firstRow.TableName}|

33. Als u de pijplijninstellingen wilt valideren, selecteert **u Valideren** op de werkbalk. Controleer of er geen validatiefouten zijn. Als u het venster **Pijplijnvalidatierapport wilt** sluiten, selecteert u **>>**.

34. Publiceer de entiteiten (gekoppelde services, gegevenssets en pijplijnen) naar de Azure Data Factory-service door de knop **Alles publiceren** te selecteren. Wacht tot u een bericht ziet waarin wordt bevestigd dat de publicatiebewerking is geslaagd.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Een pijplijn activeren op basis van een planning

1. Selecteer op de werkbalk van de pijplijn De optie **Trigger toevoegen,** selecteer **Nieuw/Bewerken**en selecteer Vervolgens **Nieuw**.

2. Geef uw trigger **HourlySync**een naam. Selecteer **Onder Type**de optie **Planning**. Stel de **herhaling** in op elk 1 uur.

3. Selecteer **OK**.

4. Selecteer **Alles publiceren**.

5. Selecteer **Nu activeren**.

6. Ga naar het tabblad **Controleren** aan de linkerkant. U kunt de status van de pijplijnuitvoering zien die is geactiveerd met de handmatige trigger. Selecteer **Vernieuwen** om de lijst te vernieuwen.

## <a name="next-steps"></a>Volgende stappen

De azure data factory-pijplijn in deze zelfstudie kopieert gegevens uit een tabel op een SQL Database Edge-instantie naar een locatie in Azure Blob-opslag eenmaal per uur. Zie deze [zelfstudies voor](../data-factory/tutorial-copy-data-portal.md)meer informatie over het gebruik van Data Factory in andere scenario's.
