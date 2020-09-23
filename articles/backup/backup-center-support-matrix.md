---
title: Ondersteunings matrix voor Back-upcentrum
description: Dit artikel bevat een overzicht van de scenario's die Back-upcentrum ondersteunt voor elk type werk belasting
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8effc2514abf1cac55abc28b625b869810536baf
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995436"
---
# <a name="support-matrix-for-backup-center"></a>Ondersteunings matrix voor Back-upcentrum

Back-upcentrum biedt ondernemingen één enkel deel venster voor het [bepalen, bewaken, bedienen en analyseren van back-ups op schaal](backup-center-overview.md). Dit artikel bevat een overzicht van de scenario's die worden ondersteund door het Back-upcentrum voor elk type werk belasting.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

| **Categorie** | **Scenario**  | **Ondersteunde workloads**  | **Limieten** |
| -------------| ------------- | ----------------------- |------------|
| Bewaking   | Alle taken weer geven | <li> Virtuele Azure-machine <br><br> <li> Azure Database for PostgreSQL server | <li> 7 dagen aan taken die beschikbaar zijn in het kader. <br> <li> Elk filter/vervolg keuzelijst ondersteunt een maximum van 1000 items. Back-upcentrum kan dus worden gebruikt voor het bewaken van Maxi maal 1000 abonnementen en 1000-kluizen tussen tenants. |
| Bewaking | Alle back-upinstanties weer geven | <li> Virtuele Azure-machine <br><br> <li> Azure Database for PostgreSQL server | Hetzelfde als hierboven |
| Bewaking | Alle back-upbeleid weer geven | <li> Virtuele Azure-machine <br><br> <li> Azure Database for PostgreSQL server | Hetzelfde als hierboven |
| Bewaking | Alle kluizen weer geven | <li> Virtuele Azure-machine <br><br> <li> Azure Database for PostgreSQL server | Hetzelfde als hierboven |
| Acties | Back-up configureren | <li> Virtuele Azure-machine <br><br> <li> Azure Database for PostgreSQL server | Raadpleeg de ondersteunings matrices voor [Azure VM backup](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) en [Azure database for PostgreSQL Server Backup](backup-azure-database-postgresql.md#support-matrix) |
| Acties | Back-upexemplaar herstellen | <li> Virtuele Azure-machine <br><br> <li> Azure Database for PostgreSQL server | Raadpleeg de ondersteunings matrices voor [Azure VM backup](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) en [Azure database for PostgreSQL Server Backup](backup-azure-database-postgresql.md#support-matrix) |
| Acties | Kluis maken | <li> Virtuele Azure-machine <br><br> <li> Azure Database for PostgreSQL server | Raadpleeg de ondersteunings matrices voor [Recovery Services kluis](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support) |
| Acties | Back-upbeleid maken | <li> Virtuele Azure-machine <br><br> <li> Azure Database for PostgreSQL server | Raadpleeg de ondersteunings matrices voor [Recovery Services kluis](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support) |
| Acties | Back-up op aanvraag uitvoeren voor een back-upexemplaar | <li> Virtuele Azure-machine <br><br> <li> Azure Database for PostgreSQL server | Raadpleeg de ondersteunings matrices voor [Azure VM backup](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) en [Azure database for PostgreSQL Server Backup](backup-azure-database-postgresql.md#support-matrix) |
| Acties | Back-up stoppen voor een back-upexemplaar | <li> Virtuele Azure-machine <br><br> <li> Azure Database for PostgreSQL server | Raadpleeg de ondersteunings matrices voor [Azure VM backup](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) en [Azure database for PostgreSQL Server Backup](backup-azure-database-postgresql.md#support-matrix) |
| Inzichten | Back-uprapporten weer geven | <li> Virtuele Azure-machine <br><br> <li> SQL in virtuele machine van Azure <br><br> <li> SAP HANA op de virtuele machine van Azure <br><br> <li> Azure Files <br><br> <li> System Center Data Protection Manager <br><br> <li> Azure Backup-Agent (MARS) <br><br> <li> Azure Backup-server (MABS) | Raadpleeg [ondersteunde scenario's voor back-uprapporten](https://docs.microsoft.com/azure/backup/configure-reports#supported-scenarios) |
| Beheer | Ingebouwd en aangepaste Azure-beleid weer geven en toewijzen onder categorie back-up | N.v.t. | N.v.t. |
| Beheer | Gegevens bronnen weer geven die niet zijn geconfigureerd voor back-up | <li> Virtuele Azure-machine <br><br> <li> Azure Database for PostgreSQL server | N.v.t. |

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

| **Categorie** | **Scenario**  |
|--------------|---------------|
| Bewaking | Waarschuwingen op schaal weer geven |
| Acties | Kluis instellingen op schaal configureren |
| Acties | Taak voor het terugzetten van meerdere regio's uit het Back-upcentrum uitvoeren |

## <a name="next-steps"></a>Volgende stappen

* [Raadpleeg de ondersteunings matrix voor Azure Backup](https://docs.microsoft.com/azure/backup/backup-support-matrix)
* [De ondersteunings matrix voor Azure VM backup bekijken](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)
* [De ondersteunings matrix voor Azure Database for PostgreSQL Server back-up bekijken](backup-azure-database-postgresql.md#support-matrix)
