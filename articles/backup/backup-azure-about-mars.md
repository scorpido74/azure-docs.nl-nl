---
title: Over de MARS-agent
description: Meer informatie over hoe de MARS-agent de back-upscenario's ondersteunt
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d558a19b3025ab1400e873f97b0ce5e5a860c75a
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902867"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informatie over de Microsoft Azure Recovery Services-agent (MARS)

In dit artikel wordt beschreven hoe de Azure Backup-service de Microsoft Azure Recovery Services-agent (MARS) gebruikt voor het maken van back-ups en het herstellen van bestanden, mappen en het volume of de systeem status van een on-premises computer naar Azure.

De MARS-agent ondersteunt de volgende back-upscenario's:

![Scenario's voor MARS-back-ups](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Bestanden en mappen**: selectief Windows-bestanden en-mappen beveiligen.
- **Volume niveau**: Beveilig een volledig Windows-volume van uw computer.
- **Systeem niveau**: de volledige Windows-systeem status beveiligen.

De MARS-agent ondersteunt de volgende herstel scenario's:

![MARS-herstel scenario's](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Dezelfde server**: de server waarop de back-up oorspronkelijk is gemaakt.
    -    **Bestanden en mappen**: Kies de afzonderlijke bestanden en mappen die u wilt herstellen.
    -    **Volume niveau**: Kies het volume en herstel punt dat u wilt herstellen en herstel het vervolgens op dezelfde locatie of op een andere locatie op dezelfde computer.  Een kopie van bestaande bestanden maken, bestaande bestanden overschrijven of bestaande bestanden overs Laan.
    -    **Systeem niveau**: Kies de systeem status en het herstel punt die u wilt herstellen op dezelfde computer op een opgegeven locatie.


-   **Alternatieve server**: een andere server dan de server waar de back-up is gemaakt.
    -    **Bestanden en mappen**: Kies de afzonderlijke bestanden en mappen waarvan u het herstel punt wilt herstellen naar een doel machine.
    -    **Volume niveau**: Kies het volume en herstel punt dat u wilt herstellen op een andere locatie. Een kopie van bestaande bestanden maken, bestaande bestanden overschrijven of bestaande bestanden overs Laan.
    -    **Systeem niveau**: Kies de systeem status en het herstel punt dat u wilt herstellen als een systeem status bestand naar een andere computer.

## <a name="backup-process"></a>Back-upproces

1. Maak vanuit het Azure Portal een [Recovery Services kluis](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault)en kies bestanden, mappen en de systeem status van de Back-updoelen.
2. [Down load de Recovery Services kluis referenties en het installatie programma van de agent](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) naar een on-premises machine. 

    Als u de on-premises machine wilt beveiligen door de back-upoptie te selecteren, kiest u bestanden, mappen en de systeem status en downloadt u de MARS-agent.

3. De infra structuur voorbereiden:

    a. Voer het installatie programma uit om [de agent te installeren](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent).

    b. Gebruik de gedownloade kluis referenties om de machine te registreren bij de Recovery Services kluis.
4. [Configureer de back-up](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy)vanuit de agent console op de client. Geef het Bewaar beleid van uw back-upgegevens op om het te beveiligen.

![Diagram van Azure Backup Agent](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>Overige scenario's
-   **Back-ups maken van specifieke bestanden en mappen in azure virtual machines**: de primaire methode voor het maken van back-ups van virtuele Azure-machines (vm's) is het gebruik van een Azure backup-extensie op de VM. De uitbrei ding maakt een back-up van de volledige VM. Als u een back-up wilt maken van specifieke bestanden en mappen in een VM, kunt u de MARS-agent installeren in de virtuele machines van Azure. Zie [architectuur: ingebouwde Azure VM-back-up](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)voor meer informatie.

-   **Offline seeding**: eerste volledige back-ups van gegevens naar Azure worden doorgaans grote hoeveel heden gegevens overgebracht en vereisen meer netwerk bandbreedte. Bij de volgende back-ups wordt alleen de Delta, of incrementele hoeveelheid gegevens overgedragen. Azure Backup comprimeert de eerste back-ups. Dankzij het proces van *offline seeding*kan Azure backup schijven gebruiken om de gecomprimeerde initiële back-upgegevens offline te uploaden naar Azure. Zie [werk stroom voor offline back-ups voor dpm en Azure backup server](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen
[Ondersteunings matrix voor MARS-agent](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Veelgestelde vragen over MARS-agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
