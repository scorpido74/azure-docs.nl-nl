---
title: Ondersteuningsmatrix voor SAP HANA Backup
description: Lees in dit artikel meer over de ondersteunde scenario's en beperkingen wanneer u Azure-back-up gebruikt om een back-up te maken van SAP HANA-databases in Azure VM's.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252439"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Ondersteuningsmatrix voor back-up van SAP HANA-databases in virtuele Azure-machines

Azure Backup ondersteunt de back-up van SAP HANA-databases naar Azure. In dit artikel worden de ondersteunde scenario's en beperkingen samengevat wanneer u Azure Backup gebruikt om een back-up te maken van SAP HANA-databases in Azure VM's.

> [!NOTE]
> De frequentie van de logboekback-up kan nu worden ingesteld op een minimum van 15 minuten. Logboekbackups beginnen pas te stromen nadat een succesvolle volledige back-up voor de database is voltooid.

## <a name="scenario-support"></a>Scenario-ondersteuning

| **Scenario**               | **Ondersteunde configuraties**                                | **Niet-ondersteunde configuraties**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologie**               | SAP HANA draait alleen in Azure Linux VM's                    | HANA Large Instances (HLI)                                   |
| **Geos**                   | **Ga:**<br> **Amerika** – Centraal VS, Oost-VS 2, Oost-VS, Noord-Centraal VS, South Central US, West US 2, West Central US, West US, Canada Central, Canada East, Brazilië South <br> **Azië-Pacific** – Australië Centraal, Australië Centraal 2, Australië Oost, Australië Zuidoost, Japan Oost, Japan West, Korea Centraal, Korea Zuid-, Oost-Azië, Zuidoost-Azië, Centraal-India, Zuid-India, West-India, China-oosten, China Noord, China East2, China Noord 2 <br> **Europa** – West-Europa, Noord-Europa, Frankrijk Centraal, Verenigd Koninkrijk Zuid, Uk West, Duitsland Noord, Duitsland West Centraal, Zwitserland Noord, Zwitserland West, Centraal Zwitserland Noord <br> **Afrika / ME** - Zuid-Afrika Noord, Zuid-Afrika West, VAE Noord, VAE Centraal  <BR>  **Azure Government-regio's** | Frankrijk Zuid, Duitsland Centraal, Duitsland Noordoost, US Gov IOWA |
| **OS-versies**            | SLES 12 met SP2, SP3 of SP4; SLES 15 met SP1                              | RHEL                                                |
| **HANA-versies**          | SDC op HANA 1.x, MDC op HANA 2.x <= SPS04 Rev 46       | -                                                            |
| **HANA-implementaties**       | SAP HANA op één Azure VM - Alleen opschalen. <br><br> Voor implementaties met hoge beschikbaarheid worden beide knooppunten op de twee verschillende machines behandeld als afzonderlijke knooppunten met afzonderlijke gegevensketens.               | Uitschalen <br><br> Bij implementaties met hoge beschikbaarheid mislukt back-up niet automatisch naar het secundaire knooppunt. Het configureren van back-up moet afzonderlijk worden gedaan voor elk knooppunt.                                           |
| **HANA-exemplaren**         | Eén SAP HANA-exemplaar op één Azure VM – alleen opschalen | Meerdere SAP HANA-exemplaren op één VM                  |
| **HANA-databasetypen**    | Single Database Container (SDC) ON 1.x, Multi-Database Container (MDC) op 2.x | MDC in HANA 1.x                                              |
| **HANA-databasegrootte**     | 2-TB volledige back-up grootte zoals gerapporteerd door HANA)                   |                                                              |
| **Back-uptypen**           | Volledige, differentiële en logboekback-ups                          | Incrementele momentopnamen                                       |
| **Hersteltypen**          | Raadpleeg de SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) voor meer informatie over de ondersteunde hersteltypen |                                                              |
| **Back-uplimieten**          | Tot 2 TB volledige back-upgrootte per SAP HANA-exemplaar         |                                                              |
| **Speciale configuraties** |                                                              | SAP HANA + Dynamische tiering <br>  Klonen door LaMa        |

------

> [!NOTE]
> Back-up- en herstelactiviteiten van SAP HANA native clients (SAP HANA Studio/ Cockpit/ DBA Cockpit) worden momenteel niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Informatie over het [maken van back-ups van SAP HANA-databases die worden uitgevoerd op Azure VM's](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Meer informatie over het [herstellen van SAP HANA-databases die worden uitgevoerd op Azure VM's](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Meer informatie over het [beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met Azure Backup](sap-hana-db-manage.md)
* Meer informatie over het [oplossen van veelvoorkomende problemen bij het maken van back-ups van SAP HANA-databases](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
