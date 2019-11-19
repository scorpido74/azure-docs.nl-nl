---
title: Release opmerkingen voor Microsoft Azure Backup Server v3
description: In dit artikel vindt u informatie over bekende problemen en tijdelijke oplossingen voor Microsoft Azure Backup Server v3 (MABS).
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: b47d83e0e3714f3f035397acaadeac9cda39d12a
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172283"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Release opmerkingen voor Microsoft Azure Backup Server

In dit artikel vindt u de bekende problemen en tijdelijke oplossingen voor Microsoft Azure Backup Server v3 (MABS).

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Back-up en herstel mislukt voor geclusterde werk belastingen

**Beschrijving:** Het maken van een back-up/herstel mislukt voor geclusterde gegevens bronnen, zoals Hyper-V-cluster of SQL-cluster (SQL always on) of Exchange in database beschikbaarheids groep (DAG) na de upgrade van MABS v2 naar MABS v3.

**Tijdelijke oplossing:** Als u dit wilt voor komen, opent u SQL Server Management Studio (SSMS)) en voert u het volgende SQL-script uit op de DPM-Data Base:

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

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Upgrade uitvoeren naar MABS v3 mislukt in Russische land instelling

**Beschrijving:** Upgrade van MABS v2 naar MABS v3 in Russische land instelling mislukt met fout code **4387**.

**Tijdelijke oplossing:** Voer de volgende stappen uit om een upgrade uit te voeren naar MABS V3 met het Russische installatie pakket:

1. Maak een [back-up](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) van uw SQL database en verwijder MABS v2 (de beveiligde gegevens behouden tijdens het verwijderen).
2. Voer een upgrade uit naar SQL 2017 (Enter prise) en verwijder rapportage als onderdeel van de upgrade.
3. [Installeren](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Installeren](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Rapportage configureren met de para meters zoals beschreven in de [SSRS-configuratie met SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Installeren](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Herstellen](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL met behulp van SSMS en voer het hulp programma DPM-Sync uit, zoals [hier](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10))wordt beschreven.
8. Werk de eigenschap DataBaseVersion in de tabel dbo. tbl_DLS_GlobalSetting met behulp van de volgende opdracht:

```sql
        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'
```

9. Start de MSDPM-service.

## <a name="next-steps"></a>Volgende stappen

[Wat is er nieuw in MABS v3](backup-mabs-whats-new-mabs.md)
