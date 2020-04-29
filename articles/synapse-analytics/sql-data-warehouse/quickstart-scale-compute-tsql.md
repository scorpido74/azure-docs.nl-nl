---
title: Schaal berekenen in azure Synapse Analytics-T-SQL
description: Schaal Compute in azure Synapse Analytics met T-SQL en SQL Server Management Studio (SSMS). Vergroot de schaal van Compute voor betere prestaties, of verklein de schaal juist om kosten te besparen.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 780137c8e081917b317656de3caba60dfaea4810
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80633731"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-using-t-sql"></a>Snelstartgids: Compute scale in azure Synapse Analytics met T-SQL

Schaal Compute in azure Synapse Analytics (voorheen SQL DW) met behulp van T-SQL en SQL Server Management Studio (SSMS). [Vergroot de schaal van Compute](sql-data-warehouse-manage-compute-overview.md) voor betere prestaties of verklein de schaal juist om kosten te besparen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Download en installeer de nieuwste versie van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SQL Server Management Studio).

## <a name="create-a-data-warehouse"></a>Een datawarehouse maken

Gebruik [Quickstart: Create and Connect - portal](create-data-warehouse-portal.md) (Quickstart: maken en verbinden - portal) om een datawarehouse te maken met de naam **mySampleDataWarehouse**. Voltooi de Quick Start om ervoor te zorgen dat u over een firewall regel beschikt en verbinding kunt maken met uw data warehouse vanuit SQL Server Management Studio.

## <a name="connect-to-the-server-as-server-admin"></a>Als serverbeheerder verbinding maken met de server

In deze sectie wordt gebruikgemaakt van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SQL Server Management Studio) om een verbinding tot stand te brengen met de Azure SQL-server.

1. Open SQL Server Management Studio.

2. Voer in het dialoogvenster **Verbinding maken met server** de volgende informatie in:

   | Instelling       | Voorgestelde waarde | Beschrijving |
   | ------------ | ------------------ | ------------------------------------------------- |
   | Servertype | Database-engine | Deze waarde is verplicht |
   | Servernaam | De volledig gekwalificeerde servernaam | Hier volgt een voor beeld: **mySampleDataWarehouseservername.database.Windows.net**. |
   | Verificatie | SQL Server-verificatie | SQL-verificatie is het enige verificatietype dat in deze zelfstudie is geconfigureerd. |
   | Aanmelden | Het beheerdersaccount voor de server | Het account dat u hebt opgegeven tijdens het maken van de server. |
   | Wachtwoord | Het wachtwoord voor het beheerdersaccount voor de server | Het wacht woord dat u hebt opgegeven tijdens het maken van de server. |

    ![Verbinding maken met server](./media/quickstart-scale-compute-tsql/connect-to-server.png)

3. Klik op **Verbinden**. Het venster Objectverkenner wordt geopend in SQL Server Management Studio.

4. Vouw **Databases** uit in Objectverkenner. Vouw vervolgens **mySampleDataWarehouse** uit om de objecten in uw nieuwe Data Base weer te geven.

    ![Database objecten](./media/quickstart-scale-compute-tsql/connected.png)

## <a name="view-service-objective"></a>Servicedoelstelling weergeven

De instelling voor de servicedoelstelling bevat het aantal DWU’s voor de datawarehouse.

De huidige DWU’s voor uw datawarehouse bekijken:

1. Onder de verbinding met **mySampleDataWarehouseservername.database.Windows.net**, vouwt u **systeem databases**uit.
2. Klik met de rechtermuisknop op **master** en selecteer **Nieuwe query**. Een nieuwe queryvenster wordt geopend.
3. Voer de volgende query uit om een selectie te maken in de dynamische beheerweergave sys.database_service_objectives.

    ```sql
    SELECT
        db.name [Database]
    ,    ds.edition [Edition]
    ,    ds.service_objective [Service Objective]
    FROM
         sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE
        db.name = 'mySampleDataWarehouse'
    ```

4. In de volgende resultaten ziet u dat **mySampleDataWarehouse** een servicedoelstelling van DW400 heeft.

    ![Details-Current-dwu](./media/quickstart-scale-compute-tsql/view-current-dwu.png)

## <a name="scale-compute"></a>De schaal van Compute aanpassen

In azure Synapse kunt u de reken resources verg Roten of verkleinen door Data Warehouse-eenheden aan te passen. Met behulp van [Maken en verbinden - portal](create-data-warehouse-portal.md) is **mySampleDataWarehouse** gemaakt en vervolgens gestart met 400 DWU's. In de volgende stappen wordt het aantal DWU's voor **mySampleDataWarehouse** aangepast.

DWU’s wijzigen:

1. Klik met de rechtermuisknop op **master** en selecteer **Nieuwe query**.
2. Gebruik de T-SQL-instructie [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om de servicedoelstelling te wijzigen. Voer de volgende query uit om de servicedoelstelling te wijzigen in DW300.

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c');
    ```

## <a name="monitor-scale-change-request"></a>Schaalaanpassingsverzoek controleren

Als u de voortgang van de eerdere aanpassingsaanvraag wilt bekijken, kunt u de `WAITFORDELAY` T-SQL syntax gebruiken om de dynamische beheerweergave (DMV) sys.dm_operation_status te peilen.

Op de volgende manier peilt u de aanpassingsstatus van het serviceobject:

1. Klik met de rechtermuisknop op **master** en selecteer **Nieuwe query**.
2. Voer de volgende query uit om de DMV sys.dm_operation_status te peilen.

    ```sql
    WHILE
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'mySampleDataWarehouse'
            AND operation = 'ALTER DATABASE'
        ORDER BY
            start_time DESC
    ) = 'IN_PROGRESS'
    BEGIN
        RAISERROR('Scale operation in progress',0,0) WITH NOWAIT;
        WAITFOR DELAY '00:00:05';
    END
    PRINT 'Complete';
    ```

3. In de resulterende uitvoer ziet u een logboek van de statuspeiling.

    ![Bewerkingsstatus](./media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-data-warehouse-state"></a>Status van datawarehouse controleren

Als een datawarehouse wordt onderbroken, kunt u deze niet verbinden met T-SQL. Als u de huidige status van de datawarehouse wilt zien, kunt u een PowerShell-cmdlet gebruiken. Zie [Check data warehouse state - Powershell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) (Status van datawarehouse controleren - PowerShell) voor een voorbeeld.

## <a name="check-operation-status"></a>Bewerkingsstatus controleren

Voer de volgende query uit op de [sys. dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dmv om informatie over verschillende beheer bewerkingen op uw Azure Synapse te retour neren. Zo wordt bijvoorbeeld de bewerking en de status van de bewerking (IN_PROGRESS of COMPLETED) geretourneerd.

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND
    major_resource_id = 'mySampleDataWarehouse'
```

## <a name="next-steps"></a>Volgende stappen

U hebt nu geleerd hoe u de rekenkracht voor uw datawarehouse wijzigt. Voor meer informatie over Azure Synapse, gaat u verder met de zelf studie voor het laden van gegevens.

> [!div class="nextstepaction"]
>[Gegevens laden in een Azure Synapse-analyse](load-data-from-azure-blob-storage-using-polybase.md)
