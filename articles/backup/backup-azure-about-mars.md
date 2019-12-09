---
title: Over de MARS-agent
description: Meer informatie over hoe de MARS-agent de back-upscenario's ondersteunt
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897323"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informatie over de Microsoft Azure Recovery Services-agent (MARS)

In dit artikel wordt beschreven hoe de Azure Backup-service de Microsoft Azure Recovery Services-agent (MARS) gebruikt voor het maken van back-ups en het herstellen van bestanden/mappen, volume of systeem status van on-premises computer naar Azure.

De MARS-agent ondersteunt de volgende back-upscenario's:

![Recovery Services-kluis dashboard](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Bestanden en mappen**: selectief Windows-bestanden en-mappen beveiligen.
- **Volume niveau**: Beveilig een volledig Windows-volume van uw computer.
- **Systeem niveau**: de volledige Windows-systeem status beveiligen.

De MARS-agent ondersteunt de volgende herstel scenario's:

![Recovery Services-kluis dashboard](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Dezelfde server**: dezelfde server waarop de back-up oorspronkelijk is gemaakt.
    -    **Bestanden en mappen**: u kunt bladeren en afzonderlijke bestanden en mappen kiezen die u wilt herstellen.
    -    **Volume niveau**: u kunt het volume en het herstel punt kiezen dat u wilt herstellen en herstellen naar dezelfde locatie of alternatieve locatie op dezelfde computer.  U kunt een kopie van bestaande bestanden maken, bestaande bestanden overschrijven of het herstellen van bestaande bestanden overs Laan.
    -    **Systeem niveau**: u kunt de systeem status en het herstel punt kiezen om terug te zetten op dezelfde computer op een opgegeven locatie.


-   **Alternatieve server**: een andere server, d.w.z. niet dezelfde server waar de back-up is gemaakt.
    -    **Bestanden en mappen**: u kunt bladeren en afzonderlijke bestanden en mappen kiezen die u wilt terugzetten naar een doel machine.
    -    **Volume niveau**: u kunt het volume en het herstel punt dat u wilt herstellen naar een alternatieve locatie kiezen door een kopie van de bestaande bestanden te maken, bestaande bestanden te overschrijven of bestaande bestanden te overs Laan.
    -    **Systeem niveau**: u kunt de systeem status en het herstel punt kiezen om te herstellen als systeem status bestand naar een andere computer.

## <a name="backup-process"></a>Back-upproces

1.  Maak vanuit de Azure Portal een [Recovery service-kluis](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault) en kies bestanden en mappen en/of systeem status in Back-updoelen.
2.  [Down load](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) de kluis referenties van de Recovery service en het installatie programma van de agent naar een on-premises machine. Als u een on-premises machine wilt beveiligen, kiest u de optie back-up bestanden en mappen en systeem status kiezen en de MARS-agent downloaden.
3.  De infra structuur voorbereiden:

    a.    Voer het installatie programma uit om de agent te [installeren](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) .

    b.  Gedownloade kluis referenties gebruiken om de machine te registreren bij Recovery Services kluis.
4.  Gebruik in de agent console op de client [back-up plannen](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy) om de back-up te configureren. Geef het Bewaar beleid van uw back-upgegevens op en start de beveiliging.

![Recovery Services-kluis dashboard](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>Overige scenario's
-   **Back-ups van specifieke bestanden en mappen in azure VM** : de primaire methode voor het maken van back-ups van virtuele Azure-machines (vm's) is door gebruik te maken van een Azure backup-extensie op de VM. Hiermee maakt u een back-up van de volledige VM. Als u back-ups wilt maken van specifieke bestanden en mappen in een VM, kunt u de MARS-agent installeren in azure Vm's. [Meer informatie](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

-   **Offline seeding** : eerste volledige back-ups van gegevens naar Azure, normaal gesp roken grote hoeveel heden gegevens overdragen en vereist meer netwerk bandbreedte in vergelijking met de volgende back-ups die alleen de Delta/incrementeel overdragen. Azure Backup comprimeert de eerste back-ups. Dankzij het proces van offline seeding kan Azure Backup schijven gebruiken om de gecomprimeerde initiële back-upgegevens offline te uploaden naar Azure. [Meer informatie](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).


## <a name="next-steps"></a>Volgende stappen
[De ondersteunings matrix voor de MARS-agent](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Veelgestelde vragen-de MARS-agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
