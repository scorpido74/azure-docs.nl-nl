---
title: 'Snelstart: bulkbelastingsgegevens met één T-SQL-instructie'
description: Bulkbelastingsgegevens met behulp van de instructie KOPIËREN
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d03c7d72a0adf02959badac758f94e47fd81de5c
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992226"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Snelstart: bulkbelastingsgegevens met de instructie KOPIËREN

In deze quickstart laadt u gegevens in bulk in uw SQL-groep met behulp van de eenvoudige en flexibele [copy-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) voor het innemen van gegevens met een hoge doorvoer. De COPY-instructie is het aanbevolen laadprogramma, omdat u gegevens naadloos en flexibel laden door functionaliteit te bieden aan:

- Gebruikers met een lager bevoorrecht bestand kunnen laden zonder dat er strikte CONTROLE-machtigingen voor het gegevensmagazijn nodig zijn
- Gebruik slechts één T-SQL-instructie zonder extra databaseobjecten te hoeven maken
- Gebruik maken van een fijner machtigingsmodel zonder opslagaccountsleutels bloot te leggen met Share Access Signatures (SAS)
- Een ander opslagaccount opgeven voor de FOUTFILE-locatie (REJECTED_ROW_LOCATION)
- Standaardwaarden voor elke doelkolom aanpassen en brongegevensvelden opgeven die u in specifieke doelkolommen wilt laden
- Een aangepaste rijterminator opgeven voor CSV-bestanden
- Escape string, veld en rij delimiters voor CSV-bestanden
- Sql Server-datumnotaties gebruiken voor CSV-bestanden
- Jokertekens en meerdere bestanden opgeven in het opslaglocatiepad

## <a name="prerequisites"></a>Vereisten

Deze quickstart gaat ervan uit dat u al een SQL-pool hebt. Als er geen SQL-pool is gemaakt, gebruikt u de snelstart [van de portal Maken en verbinden.](create-data-warehouse-portal.md)

## <a name="create-the-target-table"></a>De doeltabel maken

In dit voorbeeld laden we gegevens uit de new yorktaxidataset. we laden een tafel genaamd Trip die taxireizen binnen een jaar vertegenwoordigt. Voer het volgende uit om de tabel te maken:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>De instructie KOPIËREN uitvoeren

Voer de volgende copy-instructie uit waarmee gegevens uit het Azure Blob-opslagaccount worden geladen in de tabel Trip.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>De belasting controleren

Controleer of uw belasting vooruitgang boekt door periodiek de volgende query uit te voeren:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Volgende stappen

- Zie [Aanbevolen procedures voor het laden van gegevens voor](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data)aanbevolen procedures voor het laden van gegevens .
- Zie [Workload Isolation](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql)voor informatie over het beheren van de resources voor uw gegevensbelasting. 
