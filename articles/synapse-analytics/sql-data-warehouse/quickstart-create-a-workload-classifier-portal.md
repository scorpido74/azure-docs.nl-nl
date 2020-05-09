---
title: 'Snelstartgids: een classificatie voor de werk belasting maken-Portal'
description: Gebruik Azure Portal om een classificatie van werk belastingen met hoge urgentie te maken.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9b67d3205e95fe7cca6cacaab7e82a1a7e71f3f3
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82794103"
---
# <a name="quickstart-create-a-synapse-sql-pool-workload-classifier-using-the-azure-portal"></a>Quick Start: een Synapse van de SQL-pool maken met behulp van de Azure Portal

In deze Quick Start maakt u een classificatie van de [werk belasting](sql-data-warehouse-workload-classification.md) voor het toewijzen van query's aan een werkbelasting groep.  De classificatie wijst aanvragen van de `ELTLogin` SQL-gebruiker toe aan `DataLoads` de werkbelasting groep.   Volg de Quick Start: zelf studie voor [isolatie van werk belasting configureren](quickstart-configure-workload-isolation-portal.md) om de `DataLoads` werkbelasting groep te maken.  In deze zelf studie wordt de classificatie van de werk belasting gemaakt met de WLM_LABEL optie om aanvragen goed te classificeren.  De classificatie wijst `HIGH` ook de urgentie van het [werk belasting](sql-data-warehouse-workload-importance.md) toe aan deze aanvragen.


Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.


## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Het maken van een exemplaar van een SQL-groep in azure Synapse Analytics kan resulteren in een nieuwe factureer bare service.  Zie [prijzen voor Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

In deze Quick Start wordt ervan uitgegaan dat u al een exemplaar van de SQL-groep hebt in Synapse SQL en dat u de machtigingen voor de Data Base hebt beheerd. Gebruik [Maken en verbinden - portal](create-data-warehouse-portal.md) om een datawarehouse met de naam **mySampleDataWarehouse** te maken.
<br><br>
Er bestaat een `DataLoads` werkbelasting groep.  Zie de [Snelstartgids: zelf studie werk belasting isolatie configureren](quickstart-configure-workload-isolation-portal.md) om de werkbelasting groep te maken.
<br><br>
>[!IMPORTANT] 
>Uw SQL-groep moet online zijn voor het configureren van het beheer van de werk belasting. 


## <a name="create-a-login-for-eltlogin"></a>Een aanmelding maken voor ELTLogin

Maak een SQL Server-verificatie aanmelding in `master` de-data base met `ELTLogin`behulp van [aanmeldings gegevens maken](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) voor.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Gebruiker maken en machtigingen verlenen

Nadat de aanmelding is gemaakt, moet een gebruiker in de Data Base worden gemaakt.  Gebruik [gebruiker maken](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om de SQL-gebruiker `ELTRole` te maken in de **mySampleDataWarehouse**.  Omdat we de classificatie testen tijdens deze zelf studie, moet `ELTLogin` u machtigingen verlenen aan **mySampleDataWarehouse**. 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>Werk belasting classificatie configureren
Met classificatie kunt u aanvragen op basis van een set regels routeren naar een werkbelasting groep.  In de [Snelstartgids: zelf studie werk belasting isolatie configureren](quickstart-configure-workload-isolation-portal.md) we `DataLoads` hebben de werkbelasting groep gemaakt.  U gaat nu een classificatie van de werk belasting maken om query's `DataLoads` naar de werkbelasting groep te routeren.


1.  Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linker pagina van de Azure Portal.
2.  Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** . De SQL-groep wordt geopend.
3.  Klik op **workload Management**.

    ![Klik op menu](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Klik op **instellingen & classificaties** aan de rechter kant van de `DataLoads` werkbelasting groep.

    ![Klik op Maken](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Klik op **classificaties**.
6. Klik op **classificatie toevoegen**.

    ![Klik op Add.](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  Voer `ELTLoginDataLoads` in bij **naam**.
8.  Voer `ELTLogin` voor **lid**in.
9.  Kies `High` voor de urgentie van de **aanvraag**.  *Optioneel*, normale urgentie is standaard.
10. Voer `fact_loads` in voor **Label**.
11. Klik op **Toevoegen**.
12. Klik op **Opslaan**.

    ![Klik op configuratie](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Classificatie controleren en testen
Controleer de weer gave [sys. workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest) -catalogus om te controleren `ELTLoginDataLoads` of de classificatie bestaat.

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Controleer de catalogus weergave [sys. workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest) om classificatie details te controleren.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

Voer de volgende instructies uit om de classificatie te testen.  Zorg ervoor dat u bent ``ELTLogin`` verbonden ``Label`` als en wordt gebruikt in de query.
```sql
CREATE TABLE factstaging (ColA int)
INSERT INTO factstaging VALUES(0)
INSERT INTO factstaging VALUES(1)
INSERT INTO factstaging VALUES(2)
GO

CREATE TABLE testclassifierfact WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT * FROM factstaging
OPTION (LABEL='fact_loads')
```

Controleer de `CREATE TABLE` instructie die is geclassificeerd met `DataLoads` de `ELTLoginDataLoads` werkbelasting groep met de classificatie van de werk belasting.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```



## <a name="clean-up-resources"></a>Resources opschonen

De classificatie van `ELTLoginDataLoads` de werk belasting die in deze zelf studie is gemaakt, verwijderen:

1. Klik aan de rechter kant van de `DataLoads` werkbelasting groep op **1 classificatie** .

    ![Klik op verwijderen](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Klik op **classificaties**.
3. Klik **`...`** aan de rechter kant van de classificatie `ELTLoginDataLoads` van de werk belasting.
4. Klik op **verwijderen**.
5. Klik op **Opslaan**.

    ![Op Opslaan klikken](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Er worden kosten in rekening gebracht voor Data Warehouse-eenheden en gegevens die zijn opgeslagen in uw data warehouse. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag wilt houden, kunt u het berekenen onderbreken wanneer u het datawarehouse niet gebruikt. Door Compute te onderbreken, worden er alleen kosten in rekening gebracht voor gegevens opslag. Wanneer u klaar bent om met de gegevens te werken, hervat u de compute.
- Als u in de toekomst geen kosten meer wilt hebben, kunt u de datawarehouse verwijderen.

Volg deze stappen om resources op te schonen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en selecteer uw data warehouse.

    ![Resources opschonen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Selecteer de knop **pause** om de berekening te onderbreken. Als het datawarehouse is onderbroken, ziet u een knop **Start**.  Selecteer **Start**om de compute te hervatten.

3. Als u het Data Warehouse wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, selecteert u **verwijderen**.

4. Als u de door u gemaakte SQL-Server wilt verwijderen, selecteert u **sqlpoolservername.database.Windows.net** in de vorige installatie kopie en selecteert u vervolgens **verwijderen**.  Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resource groep wilt verwijderen, selecteert u **myResourceGroup**en selecteert u **resource groep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Bewaak uw werk belasting met behulp van de metrische gegevens voor Azure Portal bewaking.  Zie [workload management beheren en bewaken](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) voor meer informatie.
