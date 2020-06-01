---
title: Ondersteuningsmatrix voor SAP HANA Backup
description: In dit artikel vindt u informatie over de ondersteunde scenario's en beperkingen wanneer u Azure Backup gebruikt om back-ups te maken van SAP HANA-data bases op Azure-Vm's.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: ed7b3abacd5da3fd06e621d9f35a8dcdddcb9000
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234781"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Ondersteuningsmatrix voor back-up van SAP HANA-databases in virtuele Azure-machines

Azure Backup ondersteunt de back-up van SAP HANA-data bases naar Azure. In dit artikel vindt u een overzicht van de scenario's die worden ondersteund en beperkingen die aanwezig zijn wanneer u Azure Backup gebruikt om back-ups te maken van SAP HANA-data bases op virtuele

> [!NOTE]
> De frequentie van de back-uplogboeken kan nu worden ingesteld op mini maal 15 minuten. Logboek back-ups gaan alleen naar de stroom nadat een volledige back-up voor de data base is voltooid.

## <a name="scenario-support"></a>Scenario-ondersteuning

| **Scenario**               | **Ondersteunde configuraties**                                | **Niet-ondersteunde configuraties**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologie**               | Alleen SAP HANA die worden uitgevoerd in virtuele machines van Azure Linux                    | HANA grote instanties (HLI)                                   |
| **Regio's**                   | **Ga**<br> **Amerikaans-Amerika** : VS-midden, VS-Oost 2, VS-Oost, Noord-Centraal VS, Zuid-Centraal VS, VS-West 2, West-Centraal VS, VS-west, Canada-centraal, Canada-oost, Brazilië-Zuid <br> **Azië en Stille Oceaan** – Australië-centraal, Australië-centraal 2, Australië-oost, Australië-Zuidoost, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, Azië-Oost, Zuidoost-Azië, centraal-india, India-Zuid, West-india, China-oost, China-Noord, China oost2, China-Noord 2 <br> **Europa** – Europa-west, Europa-noord, Frankrijk-centraal, UK-zuid, UK-west, Duitsland-noord, Duitsland-west-centraal, Zwitserland-noord, Zwitserland-West, Centraal Zwitserland-Noord <br> **Afrika/me** -Zuid-Afrika-noord, Zuid-Afrika-west, UAE-noord, UAE-centraal  <BR>  **Azure Government-regio's** | Frankrijk-zuid, Duitsland-centraal, Duitsland-noordoost, US Gov IOWA |
| **Versies van besturings systemen**            | SLES 12 met SP2, SP3 of SP4; SLES 15 met SP1                              | RHEL                                                |
| **HANA-versies**          | Dit SDC op HANA 1. x, MDC op HANA 2. x <= SPS04 Rev 46       | -                                                            |
| **HANA-implementaties**       | SAP HANA op één Azure VM: alleen omhoog schalen. <br><br> Voor implementaties met een hoge Beschik baarheid worden de knoop punten op de twee verschillende machines beschouwd als afzonderlijke knoop punten met afzonderlijke gegevens ketens.               | Uitschalen <br><br> Bij implementaties met een hoge Beschik baarheid wordt er niet automatisch een failover naar het secundaire knoop punt gemaakt. Het configureren van de back-up moet afzonderlijk worden uitgevoerd voor elk knoop punt.                                           |
| **HANA-instanties**         | Eén SAP HANA-exemplaar op één Azure VM: alleen omhoog schalen | Meerdere exemplaren van SAP HANA op één virtuele machine                  |
| **HANA-database typen**    | Individuele database container (dit SDC) op 1. x, meerdere database container (MDC) op 2. x | MDC in HANA 1. x                                              |
| **HANA-database grootte**     | 2-TB volledige back-upgrootte zoals gerapporteerd door HANA)                   |                                                              |
| **Back-uptypen**           | Volledige, differentiële en logboek back-ups                          | Incrementeel, moment opnamen                                       |
| **Typen herstellen**          | Raadpleeg de SAP HANA opmerking [1642148](https://launchpad.support.sap.com/#/notes/1642148) voor meer informatie over de ondersteunde typen herstel bewerkingen |                                                              |
| **Back-uplimieten**          | Maxi maal 2 TB volledige back-upgrootte per SAP HANA-exemplaar         |                                                              |
| **Speciale configuraties** |                                                              | SAP HANA en dynamische lagen <br>  Klonen via LaMa        |

------

>[!NOTE]
>Azure Backup wordt niet automatisch aangepast aan de zomer-en winter tijd bij het maken van een back-up van een SAP HANA-data base die wordt uitgevoerd in een Azure VM.
>
>Pas het beleid zo nodig hand matig aan.


> [!NOTE]
> U kunt nu [de back-up-en herstel](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#monitor-manual-backup-jobs-in-the-portal) taken (op dezelfde machine) die zijn geactiveerd vanuit Hana-systeem eigen clients (SAP Hana Studio/cockpit/DBA-cockpit) bewaken in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van back-ups SAP Hana data bases die worden uitgevoerd op virtuele machines](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Meer informatie over het [herstellen van SAP Hana-data bases die worden uitgevoerd op virtuele Azure-machines](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Meer informatie over het [beheren van SAP Hana databases waarvan een back-up is gemaakt met behulp van Azure backup](sap-hana-db-manage.md)
* Informatie over het [oplossen van veelvoorkomende problemen bij het maken van back-ups van SAP Hana-data bases](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
