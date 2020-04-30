---
title: 'Snelstartgids: gegevens bulksgewijs laden met behulp van één T-SQL-instructie'
description: Gegevens bulksgewijs laden met behulp van de instructie COPY
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
ms.openlocfilehash: d39b3085a802ca0ff745ab1f63f4a8fba966ea48
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81115002"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Snelstartgids: gegevens bulksgewijs laden met behulp van de instructie COPY

In deze Quick Start kunt u gegevens bulksgewijs laden in uw SQL-pool met behulp van de eenvoudige en flexibele [kopie-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) voor gegevens opname met hoge door voer. De instructie COPY is het aanbevolen laad hulpprogramma, zodat u naadloos en flexibel gegevens kunt laden door de volgende functionaliteit te bieden:

- Gebruikers met een beperkte bevoegdheid toestaan om te laden zonder dat dit strikt beheer machtigingen voor het Data Warehouse nodig is
- Gebruik slechts één T-SQL-instructie zonder extra database objecten te hoeven maken
- Maak gebruik van een nauw keurig machtigings model zonder opslag account sleutels weer te geven met behulp van hand tekeningen voor gedeelde toegang (SAS)
- Geef een ander opslag account op voor de locatie van de ERRORFILE (REJECTED_ROW_LOCATION)
- Standaard waarden voor elke doel kolom aanpassen en bron gegevens velden opgeven die moeten worden geladen in specifieke doel kolommen
- Een aangepaste rij-Terminator opgeven voor CSV-bestanden
- Escape teken reeks-, veld-en rij scheidings tekens voor CSV-bestanden
- Gebruik SQL Server datum notaties voor CSV-bestanden
- Joker tekens en meerdere bestanden opgeven in het pad naar de opslag locatie

## <a name="prerequisites"></a>Vereisten

In deze Quick Start wordt ervan uitgegaan dat u al een SQL-groep hebt. Als er geen SQL-groep is gemaakt, gebruikt u de Snelstartgids [Create and Connect-Portal](create-data-warehouse-portal.md) .

## <a name="create-the-target-table"></a>De doel tabel maken

In dit voor beeld worden gegevens uit de nieuwe taxi-gegevensset van Amsterdam geladen. Er wordt een tabel met de naam trip geladen die in één jaar is opgenomen voor de taxi. Voer de volgende handelingen uit om de tabel te maken:

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

## <a name="run-the-copy-statement"></a>De instructie COPY uitvoeren

Voer de volgende instructie COPY uit om gegevens uit het Azure Blob Storage-account te laden in de tabel reis.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>De belasting bewaken

Controleer of het laden van voortgang plaatsvindt door regel matig de volgende query uit te voeren:

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

- Zie [Aanbevolen procedures voor het laden van gegevens](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data)voor aanbevolen procedures voor het laden van gegevens.
- Zie [workload-isolatie](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql)voor informatie over het beheren van de resources voor het laden van uw gegevens. 
