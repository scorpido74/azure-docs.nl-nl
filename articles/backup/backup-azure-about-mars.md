---
title: Over de MARS Agent
description: Meer informatie over hoe de MARS-agent de back-upscenario's ondersteunt
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673282"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informatie over de MICROSOFT Azure Recovery Services (MARS)-agent

In dit artikel wordt beschreven hoe de Azure Backup-service de MARS-agent (Microsoft Azure Recovery Services) gebruikt om een back-up te maken en bestanden, mappen en de volume- of systeemstatus te herstellen van een on-premises computer naar Azure.

De MARS-agent ondersteunt de volgende back-upscenario's:

![MARS-back-upscenario's](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Bestanden en mappen:** bescherm Windows-bestanden en -mappen selectief.
- **Volumeniveau:** bescherm een volledig Windows-volume van uw machine.
- **Systeemniveau:** bescherm een hele Windows-systeemstatus.

De MARS-agent ondersteunt de volgende herstelscenario's:

![MARS-herstelscenario's](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Dezelfde server:** de server waarop de back-up oorspronkelijk is gemaakt.
  - **Bestanden en mappen:** kies de afzonderlijke bestanden en mappen die u wilt herstellen.
  - **Volumeniveau:** kies het volume- en herstelpunt dat u wilt herstellen en herstel het vervolgens naar dezelfde locatie of een alternatieve locatie op dezelfde machine.  Maak een kopie van bestaande bestanden, overschrijf bestaande bestanden of sla het herstellen van bestaande bestanden over.
  - **Systeemniveau:** kies de systeemstatus en het herstelpunt om op een bepaalde locatie naar dezelfde machine te herstellen.

- **Alternatieve server:** een andere server dan de server waar de back-up is gemaakt.
  - **Bestanden en mappen:** kies de afzonderlijke bestanden en mappen waarvan het herstelpunt u wilt herstellen naar een doelmachine.
  - **Volumeniveau:** kies het volume- en herstelpunt dat u naar een andere locatie wilt herstellen. Maak een kopie van bestaande bestanden, overschrijf bestaande bestanden of sla het herstellen van bestaande bestanden over.
  - **Systeemniveau:** kies het systeemstatus en herstelpunt dat u als systeemstatusbestand wilt herstellen naar een alternatieve machine.

## <a name="backup-process"></a>Back-upproces

1. Maak vanuit de Azure-portal een [kluis van Recovery Services](install-mars-agent.md#create-a-recovery-services-vault)en kies bestanden, mappen en de systeemstatus op basis van de back-updoelen.
2. [Download de vault credentials en agent installer van Recovery Services](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) naar een on-premises machine.

    Als u de on-premises machine wilt beschermen door de optie Back-up te selecteren, kiest u bestanden, mappen en de systeemstatus en downloadt u de MARS-agent.

3. Bereid de infrastructuur voor:

    a. Voer het installatieprogramma uit om de agent te [installeren.](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent)

    b. Gebruik uw gedownloade kluisreferenties om de machine te registreren bij de kluis van Recovery Services.
4. Configureer [de back-up](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy)vanuit de agentconsole op de client. Geef het bewaarbeleid van uw back-upgegevens op om deze te beveiligen.

![Azure Backup-agentdiagram](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>Overige scenario's

- **Maak een back-up van specifieke bestanden en mappen binnen virtuele Azure-machines:** de primaire methode voor het maken van back-ups van virtuele Azure-machines is het gebruik van een Azure Backup-extensie op de VM. De extensie maakt een back-up van de hele VM. Als u een back-up wilt maken van specifieke bestanden en mappen binnen een vm, u de MARS-agent installeren in de Azure VM's. Zie [Architectuur: Ingebouwde Azure VM Backup](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)voor meer informatie.

- **Offline zaaien:** Initiële volledige back-ups van gegevens naar Azure brengen doorgaans grote hoeveelheden gegevens over en vereisen meer netwerkbandbreedte. Latere back-ups dragen alleen de delta of incrementele hoeveelheid gegevens over. Azure Backup comprimeert de eerste back-ups. Door het proces van *offline zaaien*kan Azure Backup schijven gebruiken om de gecomprimeerde initiële back-upgegevens offline naar Azure te uploaden. Zie [Azure Backup offline-backup met Azure Data Box](offline-backup-azure-data-box.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Ondersteuningsmatrix voor MARS-agent](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[VEELgestelde vragen over MARS-agenten](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
