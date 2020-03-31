---
title: Een Azure Migrate-project verwijderen
description: Beschrijft hoe u een Azure Migrate-project maakt en een beoordelings-/migratietool toevoegt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512728"
---
# <a name="delete-an-azure-migrate-project"></a>Een Azure Migrate-project verwijderen

In dit artikel wordt beschreven hoe u een [Azure Migrate-project verwijdert.](migrate-overview.md)


## <a name="before-you-start"></a>Voordat u begint

Voordat u een project verwijdert:

- Wanneer u een project verwijdert, worden het project en de gedetecteerde machinemetagegevens verwijderd.
- Als u een loganalytics-werkruimte hebt gekoppeld aan het hulpprogramma Serverbeoordeling voor afhankelijkheidsanalyse, bepaalt u of u de werkruimte wilt verwijderen. 
    - De werkruimte wordt niet automatisch verwijderd. Verwijder het handmatig.
    - Controleer waarvoor een werkruimte wordt gebruikt voordat u deze verwijdert. Dezelfde Log Analytics-werkruimte kan voor meerdere scenario's worden gebruikt.
    - Voordat u het project verwijdert, u een koppeling naar de werkruimte in **Azure Migreren vinden - Servers** > **Azure Migreren - Serverbeoordeling**onder **OMS Workspace**.
    - Als u een werkruimte wilt verwijderen nadat u een project hebt verwijderd, zoekt u de werkruimte in de desbetreffende resourcegroep en volgt [u deze instructies](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Een project verwijderen


1. Open in de Azure-portal de brongroep waarin het project is gemaakt.
2. Selecteer **verborgen typen weergeven**op de pagina resourcegroep .
3. Selecteer het project en de bijbehorende resources die u wilt verwijderen.
    - Het brontype voor Azure Migrate-projecten is **Microsoft.Migrate/migrateprojects**.
    - Bekijk in het volgende gedeelte de bronnen die zijn gemaakt voor detectie, beoordeling en migratie in een Azure Migrate-project.
    - Als de resourcegroep alleen het Azure Migrate-project bevat, u de volledige brongroep verwijderen.
    - Als u een project uit de vorige versie van Azure Migrate wilt verwijderen, zijn de stappen hetzelfde. Het resourcetype voor deze projecten is **migratieproject**.


## <a name="created-resources"></a>Gemaakt resources

In deze tabellen worden de bronnen samengevat die zijn gemaakt voor detectie, beoordeling en migratie in een Azure Migrate-project.

> [!NOTE]
> Verwijder de sleutelkluis met de nodige voorzichtigheid, omdat deze mogelijk beveiligingssleutels bevat.

### <a name="vmwarephysical-server"></a>VMware/fysieke server

**Resource** | **Type**
--- | ---
"Toestelnaam"kv | Key Vault
"Toestelnaam"-site | Microsoft.OffAzure/VMwareSites
"ProjectName" | Microsoft.Migrate/migrateenprojecten
ProjectName-project | Microsoft.Migrate/assessmentProjecten
"ProjectName"rsvault | Recovery Services-kluis
"ProjectName"-MigrateVault-* | Recovery Services-kluis
migrerenappligwsa* | Storage-account
migrerenapplilsa* | Storage-account
migreerapplicsa* | Storage-account
migrerenapplikv* | Key Vault
migrerenapplisbns16041 | Service Bus-naamruimte

### <a name="hyper-v-vm"></a>Hyper-V VM 

**Resource** | **Type**
--- | ---
"ProjectName" | Microsoft.Migrate/migrateenprojecten
ProjectName-project | Microsoft.Migrate/assessmentProjecten
HyperV*kv | Key Vault
HyperV*-site | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Recovery Services-kluis


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van aanvullende [beoordelings-](how-to-assess.md) en [migratietools.](how-to-migrate.md) 
