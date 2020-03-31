---
title: Moderne back-upopslag gebruiken met Azure Backup Server
description: Meer informatie over de nieuwe functies in Azure Backup Server. In dit artikel wordt beschreven hoe u de installatie van uw back-upserver upgraden.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: c6346d7b0275a00271c1787b378a63b8365edf2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172377"
---
# <a name="add-storage-to-azure-backup-server"></a>Opslag toevoegen aan Azure Backup Server

Azure Backup Server V2 en hoger ondersteunt moderne back-upopslag die opslagbesparingen van 50 procent biedt, back-ups die drie keer sneller zijn en efficiëntere opslag. Het biedt ook workload-aware opslag.

> [!NOTE]
> Als u moderne back-upopslag wilt gebruiken, moet u Backup Server V2 of V3 uitvoeren op Windows Server 2016 of V3 op Windows Server 2019.
> Als u Backup Server V2 uitvoert op een eerdere versie van Windows Server, kan Azure Backup Server geen gebruik maken van moderne back-upopslag. In plaats daarvan beschermt het workloads zoals het doet met Backup Server V1. Zie de [beveiligingsmatrix](backup-mabs-protection-matrix.md)voor back-upserverversie .
>
> Om verbeterde back-upprestaties te bereiken raden we aan om MABS v3 met gelaagde opslag te implementeren op Windows Server 2019. Raadpleeg het DPM-artikel "[MbS instellen met gelaagde opslag](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-2019#set-up-mbs-with-tiered-storage)" voor stappen om gelaagde opslag te configureren.

## <a name="volumes-in-backup-server"></a>Volumes in back-upserver

Backup Server V2 of hoger accepteert opslagvolumes. Wanneer u een volume toevoegt, maakt Backup Server het volume op in Resilient File System (ReFS), waarvoor moderne back-upopslag vereist is. Als u een volume wilt toevoegen en het later wilt uitbreiden als dat nodig is, raden we u aan deze werkstroom te gebruiken:

1. Back-upserver instellen op een virtuele machine.
2. Een volume maken op een virtuele schijf in een opslaggroep:
    1. Voeg een schijf toe aan een opslaggroep en maak een virtuele schijf met eenvoudige lay-out.
    2. Voeg eventuele extra schijven toe en breid de virtuele schijf uit.
    3. Maak volumes op de virtuele schijf.
3. Voeg de volumes toe aan back-upserver.
4. Opslag voor workloads configureren.

## <a name="create-a-volume-for-modern-backup-storage"></a>Een volume maken voor moderne back-upopslag

Als u Backup Server V2 of hoger gebruikt met volumes als schijfopslag, u de controle over de opslag behouden. Een volume kan één schijf zijn. Als u de opslag in de toekomst echter wilt uitbreiden, maakt u een volume van een schijf die is gemaakt met behulp van opslagruimten. Dit kan helpen als u het volume voor back-upopslag wilt uitbreiden. Deze sectie biedt aanbevolen procedures voor het maken van een volume met deze instelling.

1. Selecteer in Serverbeheer de**opslaggroepen** **bestands- en opslagservicesvolumes** > **Volumes** > . Selecteer **onder FYSIEKE SCHIJVEN**de optie Nieuwe **opslaggroep**.

    ![Een nieuwe opslaggroep maken](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Selecteer in de vervolgkeuzelijst **TAKEN** de optie **Nieuwe virtuele schijf**.

    ![Een virtuele schijf toevoegen](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Selecteer de opslaggroep en selecteer **Fysieke schijf toevoegen**.

    ![Een fysieke schijf toevoegen](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Selecteer de fysieke schijf en selecteer **Virtuele schijf uitbreiden**.

    ![De virtuele schijf uitbreiden](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Selecteer de virtuele schijf en selecteer **Nieuw volume**.

    ![Een nieuw volume maken](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. Selecteer **in het** dialoogvenster Server en schijf selecteren de server en de nieuwe schijf. Selecteer vervolgens **Volgende**.

    ![De server en schijf selecteren](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Volumes toevoegen aan schijfopslag back-upserver

> [!NOTE]
>
> - Voeg slechts één schijf toe aan de groep om het aantal kolomen op 1 te houden. U vervolgens schijven toevoegen als dat nodig is.
> - Als u onderweg meerdere schijven aan de opslaggroep toevoegt, wordt het aantal schijven opgeslagen als het aantal kolommen. Wanneer er meer schijven worden toegevoegd, kunnen ze slechts een veelvoud zijn van het aantal kolommen.

Als u een volume wilt toevoegen aan back-upserver, u in het deelvenster **Beheer** de opslag opnieuw scannen en selecteert u **Toevoegen**. Er wordt een lijst weergegeven met alle beschikbare volumes voor back-upserveropslag. Nadat beschikbare volumes zijn toegevoegd aan de lijst met geselecteerde volumes, u ze een vriendelijke naam geven om u te helpen ze te beheren. Als u deze volumes wilt opmaken op ReFS, zodat back-upserver de voordelen van moderne back-upopslag kan gebruiken, selecteert u **OK**.

![Beschikbare volumes toevoegen](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Opslag met workloads instellen

Met workload-aware opslag u de volumes selecteren die bij voorkeur bepaalde soorten workloads opslaan. U bijvoorbeeld dure volumes instellen die een groot aantal invoer-/uitvoerbewerkingen per seconde (IOPS) ondersteunen om alleen de workloads op te slaan waarvoor frequente back-ups met een hoog volume nodig zijn. Een voorbeeld is SQL Server met transactielogboeken. Andere workloads waarvan minder vaak een back-up wordt genomen, zoals VM's, kunnen worden geback-upt van lage kostenvolumes.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

U opslag met workloads instellen met behulp van de PowerShell-cmdlet Update-DPMDiskStorage, die de eigenschappen van een volume in de opslaggroep op een Azure Backup Server bijwerkt.

Syntaxis:

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

De volgende schermafbeelding toont de cmdlet Update-DPMDiskStorage in het PowerShell-venster.

![De opdracht Update-DPMDiskStorage in het PowerShell-venster](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

De wijzigingen die u aanbrengt met PowerShell, worden weergegeven in de administratorconsole van de back-upserver.

![Schijven en volumes in de administratorconsole](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Verouderde opslag migreren naar moderne back-upopslag

Nadat u back-upserver V2 hebt geüpuimeerd of geïnstalleerd en het besturingssysteem hebt geüpuimeerd naar Windows Server 2016, werkt u uw beveiligingsgroepen bij om moderne back-upopslag te gebruiken. Standaard worden beveiligingsgroepen niet gewijzigd. Ze blijven functioneren zoals ze in eerste instantie werden opgezet.

Het bijwerken van beveiligingsgroepen voor het gebruik van Modern Backup Storage is optioneel. Als u de beveiligingsgroep wilt bijwerken, stopt u de beveiliging van alle gegevensbronnen met behulp van de optie Gegevens behouden. Voeg vervolgens de gegevensbronnen toe aan een nieuwe beveiligingsgroep.

1. Selecteer in de administratorconsole de functie **Beveiliging.** Klik in de lijst **met leden van de beveiligingsgroep** met de rechtermuisknop op het lid en selecteer Vervolgens De beveiliging van lid **stoppen**.

   ![Stop bescherming van lid](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Controleer in het dialoogvenster **Verwijderen uit groep** de gebruikte schijfruimte en de beschikbare vrije ruimte voor de opslaggroep. Standaard worden de herstelpunten op de schijf gelaten, zodat ze verlopen volgens het bijbehorende bewaarbeleid. Klik op **OK**.

   Als u de gebruikte schijfruimte onmiddellijk wilt retourneren naar de gratis opslaggroep, schakelt u het selectievakje **Replica op schijf verwijderen in** om de back-upgegevens (en herstelpunten) die aan dat lid zijn gekoppeld, te verwijderen.

   ![Verwijderen uit dialoogvenster Groeperen](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Maak een beveiligingsgroep die moderne back-upopslag gebruikt. Neem de onbeschermde gegevensbronnen op.

## <a name="add-disks-to-increase-legacy-storage"></a>Schijven toevoegen om oudere opslag te vergroten

Als u verouderde opslag met back-upserver wilt gebruiken, moet u mogelijk schijven toevoegen om de oudere opslag te vergroten.

Schijfopslag toevoegen:

1. Selecteer in de administratorconsole de optie**Opslag toevoegen van de schijfopslag** >  **beheren** > .**Add**

    ![Dialoogvenster Schijfopslag toevoegen](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. Selecteer schijven **toevoegen**in het dialoogvenster **Schijfopslag toevoegen** .

3. Selecteer in de lijst met beschikbare schijven de schijven die u wilt toevoegen, selecteer **Toevoegen**en selecteer **OK**.

## <a name="next-steps"></a>Volgende stappen

Nadat u back-upserver hebt geïnstalleerd, leest u hoe u uw server voorbereidt of begint u met het beveiligen van een werkbelasting.

- [Back-upserverworkloads voorbereiden](backup-azure-microsoft-azure-backup.md)
- [Back-upserver gebruiken om een back-up van een VMware-server te maken](backup-azure-backup-server-vmware.md)
- [Back-upserver gebruiken om een back-up te maken van SQL Server](backup-azure-sql-mabs.md)
