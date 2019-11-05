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
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501321"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>Zelf studie: gegevens van SQL Database Edge synchroniseren met Azure Blob-opslag met behulp van Azure Data Factory

In deze zelf studie gebruikt u Azure Data Factory om gegevens uit een tabel in een exemplaar van Azure SQL Database Edge incrementeel te synchroniseren met Azure Blob Storage.

## <a name="before-you-begin"></a>Voordat u begint

Als u nog geen data base of tabel hebt gemaakt in de implementatie van de Azure SQL Database Edge, gebruikt u een van de volgende methoden om er een te maken:

* Gebruik [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) of [Azure Data Studio](/sql/azure-data-studio/download/) om verbinding te maken met SQL database Edge en voer een SQL-script uit om de data base en tabel te maken.
* Maak een SQL database en een tabel met behulp van [Sqlcmd](/sql/tools/sqlcmd-utility/) door rechtstreeks verbinding te maken met de SQL database Edge-module. Zie [verbinding maken met de data base-engine met behulp van Sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/)voor meer informatie.
* Gebruik SQLPackage. exe om een dacpac-bestand te implementeren in de SQL Database Edge-container. Dit kan worden geautomatiseerd door de URI van het SQLPackage-bestand op te geven als onderdeel van de eigenschappen configuratie van de gewenste modules of door rechtstreeks gebruik te maken van het SqlPackage. exe-client hulpprogramma om een dacpac te implementeren naar SQL Database Edge.

    Zie [Sqlpackage downloaden en installeren](/sql/tools/sqlpackage-download/)om sqlpackage te downloaden. De volgende voorbeeld opdrachten voor SqlPackage. exe zijn opgenomen, maar Raadpleeg de SqlPackage-documentatie voor meer informatie.

    **Dacpac maken**:

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **Dacpac Toep assen**:

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Een SQL-tabel en-procedure maken om het watermerk niveau op te slaan en bij te werken

De watermerk tabel wordt gebruikt voor het opslaan van de laatste time stamp waarmee de gegevens al zijn gesynchroniseerd met de Azure-opslag. De opgeslagen procedure Transact-SQL (T-SQL) wordt gebruikt om de watermerk tabel na elke synchronisatie bij te werken. 

Voer de volgende opdrachten uit op het SQL Database rand exemplaar:

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

## <a name="create-a-data-factory-workflow"></a>Een Data Factory werk stroom maken

In deze sectie maakt u een Azure Data Factory pijp lijn om gegevens te synchroniseren vanuit een tabel binnen Azure SQL Database Edge naar Azure Blob-opslag.

### <a name="create-data-factory-using-the-data-factory-ui"></a>Data Factory maken met behulp van de Data Factory-gebruikers interface

Maak een Data Factory met behulp van de instructies in deze [zelf studie](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Een Data Factory pijp lijn maken

1. Selecteer op de pagina **aan de slag** van de Data Factory gebruikers interface de tegel **pijp lijn maken** .

    ![Data Factory-pijp lijn maken](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Voer op de pagina **Algemeen** van het venster **Eigenschappen** voor de pijp lijn de naam **PeriodicSync** in.

3. Voeg de **opzoek** activiteit toe om de oude watermerk waarde op te halen. Vouw in de **werkset activiteiten**het gedeelte **Algemeen**uit en sleep & de **opzoek** activiteit naar het ontwerp oppervlak voor pijp lijnen neer te zetten. Wijzig de naam van de activiteit in *OldWatermark*.

    ![oud water merk zoeken](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Ga naar het tabblad **instellingen** en selecteer **+ Nieuw** voor **bron gegevensset**. In deze stap maakt u een gegevensset om gegevens in de watermarktable weer te geven. Deze tabel bevat de oude grenswaarde die is gebruikt in de vorige kopieerbewerking.

5. Selecteer in het venster **nieuwe gegevensset** de optie **Azure SQL Server**en selecteer **door gaan**.  

6. Voer in het venster **set-eigenschappen** voor de gegevensset *water Mark dataset* in als naam.

7. Voor **gekoppelde service**selecteert u **Nieuw**en voert u de volgende stappen uit:

    1. Voer *SQLDBEdgeLinkedService* in als **naam**.

    2. Voer uw SQL Database Edge-Server Details in voor de **Server naam**.

    3. Voer de **naam van uw data base** in vanuit de vervolg keuzelijst.

    4. Voer uw **gebruikers naam** en **wacht woord**in.

    5. Selecteer **verbinding testen**om de verbinding met het SQL database rand exemplaar te testen.

    6. Selecteer **Maken**.

    ![een gekoppelde service maken](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Selecteer **OK**

8. Selecteer **bewerken**op het tabblad **instellingen** .

9. Selecteer [dbo] op het tabblad **verbinding** *. [ watermarktable]* voor de **tabel**. Als u de gegevens in de tabel wilt bekijken, selecteert u **voorbeeld gegevens**.

10. Schakel over naar de pijplijn editor door het tabblad pijp lijn bovenaan te selecteren of door de naam van de pijp lijn in de structuur weergave aan de linkerkant te selecteren. Bevestig in het venster Eigenschappen voor de **opzoek activiteit**dat **water Mark dataset** is geselecteerd voor het veld **bron gegevensset** .

11. Vouw in de werkset **activiteiten** het item **Algemeen**uit, Sleep een andere **opzoek** activiteit naar het ontwerp oppervlak voor pijp lijnen en stel de naam in op **NewWatermark** op het tabblad **Algemeen** van het venster Eigenschappen. Met deze opzoekactiviteit wordt de nieuwe grenswaarde opgehaald uit de tabel met de brongegevens die moeten worden gekopieerd naar de bestemming.

12. Ga in het venster Eigenschappen voor de tweede **opzoek** activiteit naar het tabblad **instellingen** en selecteer **Nieuw** om een gegevensset te maken die verwijst naar de bron tabel die de nieuwe watermerk waarde bevat.

13. Selecteer in het venster **nieuwe gegevensset** de optie SQL database rand instantie en selecteer **door gaan**.

    1. Voer in het venster **set Properties** **Source dataset** in als **name**. Selecteer *SQLDBEdgeLinkedService* voor gekoppelde service.

    2. Selecteer ***de tabel die u wilt synchroniseren voor de*** tabel. U kunt ook een query voor deze gegevensset opgeven, zoals verderop in de zelf studie wordt vermeld. De query heeft voorrang op de tabel die u in deze stap opgeeft.

    3. Selecteer **OK**.

14. Schakel over naar de pijplijn editor door het tabblad pijp lijn bovenaan te selecteren of door de naam van de pijp lijn in de structuur weergave aan de linkerkant te selecteren. Bevestig in het venster Eigenschappen voor de **opzoekactiviteit** dat **SourceDataset** is geselecteerd in het veld **Brongegevensset**.

15. Selecteer **query** voor het veld **query gebruiken** en voer de volgende query na het bijwerken van de tabel naam in de query in: u selecteert alleen de maximum waarde van tijds tempel in de tabel. Zorg ervoor dat u ook alleen de **eerste rij**hebt ingeschakeld.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![query selecteren](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. Vouw in de werkset **activiteiten** de optie **& trans formatie verplaatsen**uit, Sleep de **Kopieer** activiteit uit de werkset activiteiten en stel de naam in op **IncrementalCopy**.

17. Verbind beide **opzoek** activiteiten met de **Kopieer** activiteit door de **groene knop** die is gekoppeld aan de **opzoek** activiteiten te slepen naar de **Kopieer** activiteit. Laat de muis knop los als u ziet dat de rand kleur van de Kopieer activiteit is gewijzigd in blauw.

18. Selecteer de **Kopieer** activiteit en bevestig dat de eigenschappen voor de activiteit worden weer geven in het venster **Eigenschappen** .

19. Ga naar **Source** in het venster **Properties** en voer de volgende stappen uit:

    1. Selecteer **SourceDataset** in het veld **Brongegevensset**.

    2. Selecteer **Query** in het veld **Query gebruiken**.

    3. Voer de SQL-query voor het **query** veld in. Onderstaande voorbeeld query

    4. SQL-query:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Ga naar het tabblad **sink** en selecteer **+ Nieuw** voor het veld **sink-gegevensset** .

21. In deze zelf studie is Sink Data Store van het type **Azure Blob Storage**. Selecteer **Azure Blob-opslag**en selecteer **door gaan** in het venster **nieuwe gegevensset** .

22. Selecteer in het venster **indeling selecteren** het notatie type van uw gegevens en selecteer **door gaan**.

23. Voer in het venster **set Properties** **SinkDataset** in als name. Voor gekoppelde service selecteert u **+ Nieuw**. In deze stap maakt u een verbinding (gekoppelde service) voor uw **Azure Blob-opslag**.

24. Voer de volgende stappen uit in het venster **nieuwe gekoppelde service (Azure Blob-opslag)** :

    1. Voer *AzureStorageLinkedService* in als naam.

    2. Selecteer uw Azure Storage-account voor de naam van het **opslag account** met uw Azure-abonnement.

    3. Test de **verbinding** en selecteer vervolgens **volt ooien**.

25. Controleer in het venster **set Properties** of *AzureStorageLinkedService* is geselecteerd voor **gekoppelde service**. Selecteer vervolgens **maken** en **OK**.

26. Selecteer **bewerken**op het tabblad **sink** .

27. Ga naar het tabblad **verbinding** van *SinkDataset* en voer de volgende stappen uit:

    1. Voer voor het veld **bestandspad** *asdedatasync/incrementalcopy*in, waarbij **adftutorial** de naam van de BLOB-container is en **incrementalcopy** de mapnaam is. Maak de container als deze bestaat niet of stel deze in op de naam van een bestaande container. Als de uitvoermap *incrementalcopy* niet bestaat, wordt deze automatisch gemaakt in Azure Data Factory. U kunt ook de knop **Bladeren** voor het **bestandspad** gebruiken om naar een map in een blob-container te navigeren.

    2. Voor het **bestand** deel van het **veld bestandspad** selecteert u **dynamische inhoud toevoegen [Alt + P]** en voert u vervolgens *@CONCATin (' incremental-', pipeline (). RunId, '. txt ')* in het geopende venster. Selecteer vervolgens **Voltooien**. De bestandsnaam wordt dynamisch gegenereerd met behulp van de expressie. Elke pijplijnuitvoering heeft een unieke id. De kopieeractiviteit gebruikt de run-id om de bestandsnaam te genereren.

28. Schakel over naar de **pijplijn** editor door het tabblad pijp lijn bovenaan te selecteren of door de naam van de pijp lijn in de structuur weergave aan de linkerkant te selecteren.

29. Vouw in de **Activiteiten**-werkset de optie **Algemeen** uit. Gebruik vervolgens slepen-en-neerzetten om de **opgeslagen-procedureactiviteit** uit de **Activiteiten**-werkset te verplaatsen naar het ontwerpoppervlak voor pijplijnen. **Verbind** de groene uitvoer (geslaagd) van de **kopieeractiviteit** met de **opgeslagen-procedureactiviteit**.

30. Selecteer **opgeslagen procedure-activiteit** in de pijp lijn Designer, wijzig de naam ervan in *SPtoUpdateWatermarkActivity*.

31. Ga naar het tabblad **SQL-account** en selecteer *SQLDBEdgeLinkedService* voor **gekoppelde service**.

32. Open het tabblad **Stored Procedure** en voer de volgende stappen uit:

    1. Selecteer [dbo] voor de naam van de **opgeslagen procedure** *. [ usp_write_watermark]* .

    2. Als u waarden wilt opgeven voor de opgeslagen procedure parameters, selecteert u para meter importeren en voert u de volgende waarden in voor de para meters:

    |Naam|Type|Waarde|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {activity (' NewWaterMark '). output. firstRow. NewWatermarkvalue}|
    |TableName|Tekenreeks|@ {activity (' OldWaterMark '). output. firstRow. TableName}|

33. Selecteer **valideren** op de werk balk om de instellingen voor de pijp lijn te valideren. Controleer of er geen validatiefouten zijn. Selecteer **>>** om **het venster pijplijn validatie rapport** te sluiten.

34. Publiceer entiteiten (gekoppelde services, gegevenssets en pijplijnen) naar de Azure Data Factory-service door op de knop **Alles publiceren** te klikken. Wacht tot u een bericht ziet waarin staat dat het publiceren is voltooid.

## <a name="trigger-a-pipeline-on-schedule"></a>Een pijp lijn op schema activeren

1. Selecteer **trigger toevoegen**op de werk balk van de pijp lijn en selecteer vervolgens **Nieuw/bewerken**, selecteer **+ Nieuw**.

2. Noem uw trigger met *HourlySync*, kies **type** als schema en stel **terugkeer patroon** in op elke 1 uur.

3. Selecteer **OK**.

4. Selecteer **Alles publiceren**.

5. Selecteer **nu activeren**.

6. Ga naar het tabblad **Controleren** aan de linkerkant. U kunt de status van de pijplijnuitvoering zien die is geactiveerd met de handmatige trigger. Selecteer de knop **vernieuwen** om de lijst te vernieuwen.

## <a name="next-steps"></a>Volgende stappen

Met de Azure Data Factory-pijp lijn in deze zelf studie worden gegevens gekopieerd van een tabel op het SQL Database Edge-exemplaar naar een locatie in Azure Blob Storage met een frequentie van een uur. Ga door deze [zelf studies](../data-factory/tutorial-copy-data-portal.md)voor meer informatie over het gebruik van Data Factory in meer scenario's.
