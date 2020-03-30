---
title: Nieuw in Microsoft Azure Backup Server
description: Microsoft Azure Backup Server biedt u verbeterde back-upmogelijkheden voor het beveiligen van VM's, bestanden en mappen, workloads en meer. Meer informatie over het installeren of upgraden naar Azure Backup Server V3.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 61430ce06d3e441fcfe0443eaaf5de3755b04624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582803"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Nieuw in Microsoft Azure Backup Server

Microsoft Azure Backup Server versie 3 (MABS V3) is de nieuwste upgrade en bevat kritieke bugfixes, Windows Server 2019-ondersteuning, SQL 2017-ondersteuning en andere functies en verbeteringen. Zie KB-artikel [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)om de vaste lijst met bugs en de installatie-instructies voor MABS V3 te bekijken.

De volgende functies zijn opgenomen in MABS V3:

## <a name="volume-to-volume-migration"></a>Volume-migratie naar volume

Met Modern Backup Storage (MBS) in MABS V2 hebben we Workload aware storage aangekondigd, waarbij u bepaalde workloads configureert om een back-up te maken naar specifieke opslag, op basis van opslageigenschappen. Na de configuratie u echter back-ups van bepaalde gegevensbronnen naar andere opslag verplaatsen voor een optimaal gebruik van resources. MABS V3 biedt u de mogelijkheid om uw back-ups te migreren en te configureren om in [drie stappen](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842)op een ander volume te worden opgeslagen.

## <a name="prevent-unexpected-data-loss"></a>Onverwacht gegevensverlies voorkomen

In ondernemingen wordt MABS beheerd door een team van beheerders. Hoewel er richtlijnen zijn voor opslag die moeten worden gebruikt voor back-ups, kan een onjuist volume dat aan MABS wordt gegeven als back-upopslag leiden tot verlies van kritieke gegevens. Met MABS V3 u dergelijke scenario's voorkomen door deze volumes te configureren als de volumes die niet beschikbaar zijn voor opslag met behulp van [deze PowerShell-cmdlets.](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage)

## <a name="custom-size-allocation"></a>Toewijzing van aangepaste grootte

Moderne back-upopslag (MBS) verbruikt opslag dun, wanneer en wanneer dat nodig is. Om dit te doen, berekent MABS de grootte van de gegevens waarvan een back-up wordt gemaakt wanneer deze zijn geconfigureerd voor bescherming. Als er echter een back-up van veel bestanden en mappen wordt gemaakt, zoals in het geval van een bestandsserver, kan de grootteberekening lang duren. Met MABS V3 u MABS configureren om de volumegrootte standaard te accepteren, in plaats van de grootte van elk bestand te berekenen, waardoor tijd wordt bespaard.

## <a name="optimized-cc-for-rct-vms"></a>Geoptimaliseerde CC voor RCT VM's

MABS maakt gebruik van RCT (de native change tracking in Hyper-V), waardoor de behoefte aan tijdrovende consistentiecontroles in scenario's als VM-crashes afneemt. RCT biedt betere tolerantie dan het bijhouden van wijzigingen door de VSS-back-ups op basis van momentopnamen. MABS V3 optimaliseert het netwerk- en opslagverbruik verder door alleen de gewijzigde gegevens over te dragen tijdens consistentiecontroles.

## <a name="support-to-tls-12"></a>Ondersteuning voor TLS 1.2

TLS 1.2 is de veilige manier van communiceren voorgesteld door Microsoft met best-in class encryptie. MABS ondersteunt nu TLS 1.2-communicatie tussen MABS en de beveiligde servers, voor verificatie op basis van certificaten en voor cloudback-ups.

## <a name="vmware-vm-protection-support"></a>Ondersteuning voor VMware VM-beveiliging

VMware VM-back-up wordt nu ondersteund voor productie-implementaties. MABS V3 biedt het volgende voor VMware VM-beveiliging:

- Ondersteuning voor vCenter en ESXi 6.5, samen met ondersteuning voor 5.5 en 6.0.
- Automatische beveiliging van VMware VM's in de cloud. Als er nieuwe VMware VM's worden toegevoegd aan een beveiligde map, worden ze automatisch beveiligd voor schijf en cloud.
- Verbeteringen in de herstelefficiëntie voor vmware-alternatieve locatieherstel.

## <a name="sql-2017-support"></a>Ondersteuning voor SQL 2017

MABS V3 kan worden geïnstalleerd met SQL 2017 als De MABS-database. U de SQL-server upgraden van SQL 2016 naar SQL 2017 of vers installeren. U ook een back-up maken van SQL 2017-workload, zowel in geclusterde als niet-geclusterde omgeving met MABS V3.

## <a name="windows-server-2019-support"></a>Ondersteuning voor Windows Server 2019

MABS V3 kan worden geïnstalleerd op Windows Server 2019. Als u MABS V3 wilt gebruiken met WS2019, u uw besturingssysteem upgraden naar WS2019 voordat u MABS V3 installeert/upgradet of u uw OS-post upgraden bij het installeren/upgraden van V3 op WS2016.

MABS V3 is een volledige release en kan rechtstreeks worden geïnstalleerd op Windows Server 2016, Windows Server 2019 of kan worden geüpgraded vanaf MABS V2. Voordat u back-upserver V3 upgradet of installeert, leest u over de vereisten voor de installatie.
Meer informatie over de installatie/upgradestappen voor MABS vindt [u hier.](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package)

> [!NOTE]
>
> MABS heeft dezelfde codebasis als System Center Data Protection Manager. MABS v3 is gelijk aan Data Protection Manager 1807.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het voorbereiden van uw server of het beveiligen van een werkbelasting:

- [Bekende problemen in MABS V3](backup-mabs-release-notes-v3.md)
- [Back-upserverworkloads voorbereiden](backup-azure-microsoft-azure-backup.md)
- [Back-upserver gebruiken om een back-up van een VMware-server te maken](backup-azure-backup-server-vmware.md)
- [Back-upserver gebruiken om een back-up te maken van SQL Server](backup-azure-sql-mabs.md)
- [Moderne back-upopslag gebruiken met back-upserver](backup-mabs-add-storage.md)
