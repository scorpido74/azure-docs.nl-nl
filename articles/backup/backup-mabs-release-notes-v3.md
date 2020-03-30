---
title: Releasenotes voor Microsoft Azure Backup Server v3
description: In dit artikel vindt u informatie over de bekende problemen en tijdelijke oplossingen voor Microsoft Azure Backup Server (MABS) v3.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: b47d83e0e3714f3f035397acaadeac9cda39d12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172283"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Releasenotes voor Microsoft Azure Backup Server

In dit artikel worden de bekende problemen en tijdelijke oplossingen voor Microsoft Azure Backup Server (MABS) V3 weergegeven.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Back-up- en herstelmislukt voor geclusterde workloads

**Beschrijving:** Back-up/herstel mislukt voor geclusterde gegevensbronnen zoals Hyper-V-cluster of SQL-cluster (SQL Always On) of Exchange in database availability group (DAG) na het upgraden van MABS V2 naar MABS V3.

**Werk rond:** Open SQL Server Management Studio (SSMS)) en voer het volgende SQL-script uit op de DPM DB:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Upgrade naar MABS V3 mislukt in Russische locale

**Beschrijving:** Upgrade van MABS V2 naar MABS V3 in Russische landstaat mislukt met een foutcode **4387**.

**Werk rond:** Volg de volgende stappen om te upgraden naar MABS V3 met russisch installatiepakket:

1. [Maak een back-up van](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) uw SQL-database en verwijder MABS V2 (kies ervoor om de beveiligde gegevens te bewaren tijdens het verwijderen).
2. Upgrade naar SQL 2017 (Enterprise) en verwijder rapportage als onderdeel van de upgrade.
3. [Installeren](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Installeren](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Configureer rapportage met behulp van de parameters zoals gedocumenteerd in [SSRS-configuratie met SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Installeren](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Herstellen](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL met Behulp van SSMS en voer DPM-Sync tool zoals [hier](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10))beschreven .
8. Werk de eigenschap 'DataBaseVersion' in de tabel dbo.tbl_DLS_GlobalSetting bij met de volgende opdracht:

```sql
        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'
```

9. MsDPM-service starten.

## <a name="next-steps"></a>Volgende stappen

[Nieuw in MABS V3](backup-mabs-whats-new-mabs.md)
