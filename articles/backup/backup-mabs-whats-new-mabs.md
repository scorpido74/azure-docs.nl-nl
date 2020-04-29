---
title: Nieuw in Microsoft Azure Backup Server
description: Microsoft Azure Backup Server biedt uitgebreide back-upmogelijkheden voor het beveiligen van Vm's, bestanden en mappen, werk belastingen en nog veel meer. Meer informatie over het installeren of upgraden van Azure Backup Server v3.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 61430ce06d3e441fcfe0443eaaf5de3755b04624
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77582803"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Nieuw in Microsoft Azure Backup Server

Microsoft Azure Backup Server versie 3 (MABS v3) is de meest recente upgrade en omvat essentiële probleem oplossingen, ondersteuning voor Windows Server 2019, SQL 2017-ondersteuning en andere functies en verbeteringen. Zie KB-artikel [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)voor een overzicht van de fouten die zijn opgelost en de installatie-instructies voor MABS v3.

De volgende functies zijn opgenomen in MABS V3:

## <a name="volume-to-volume-migration"></a>Migratie van volumes naar volume

Met Modern Backup Storage (MBS) in MABS v2 hebben we werkbelasting bewuste opslag aangekondigd, waar u bepaalde werk belastingen zodanig configureert dat een back-up wordt gemaakt van specifieke opslag, op basis van opslag eigenschappen. Na de configuratie is het echter mogelijk dat u back-ups van bepaalde gegevens bronnen wilt verplaatsen naar andere opslag voor optimaal gebruik van bronnen. MABS V3 biedt u de mogelijkheid om uw back-ups te migreren en ze zodanig te configureren dat deze in [drie stappen](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842)worden opgeslagen op een ander volume.

## <a name="prevent-unexpected-data-loss"></a>Onverwacht gegevens verlies voor komen

In ondernemingen wordt MABS beheerd door een team van beheerders. Hoewel er richt lijnen zijn voor opslag die moet worden gebruikt voor back-ups, kan een onjuist volume dat is gegeven aan MABS als back-upopslag leiden tot verlies van kritieke gegevens. Met MABS v3 kunt u dergelijke scenario's voor komen door de volumes te configureren als die die niet beschikbaar zijn voor opslag met behulp van [deze Power shell-cmdlets](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Toewijzing van aangepaste grootte

Modern Backup Storage (MBS) verbruikt opslag dun, indien nodig. Om dit te doen, berekent MABS de grootte van de gegevens waarvan een back-up wordt gemaakt wanneer deze is geconfigureerd voor beveiliging. Als er echter meerdere bestanden en mappen samen een back-up worden gemaakt, zoals in het geval van een bestands server, kan de grootte berekening lang duren. Met MABS v3 kunt u MABS zo configureren dat de grootte van het volume als standaard wordt geaccepteerd, in plaats van de grootte van elk bestand te berekenen, waardoor de tijd wordt bespaard.

## <a name="optimized-cc-for-rct-vms"></a>Geoptimaliseerde CC voor RCT-Vm's

MABS maakt gebruik van RCT (het systeem eigen bijhouden van wijzigingen in Hyper-V), wat de nood zaak van het tijdrovende consistentie controles in scenario's verlaagt als de VM vastloopt. RCT biedt betere tolerantie dan het bijhouden van wijzigingen door de VSS-back-ups op basis van momentopnamen. MABS v3 optimaliseert het netwerk-en opslag verbruik verder door alleen de gewijzigde gegevens tijdens de consistentie controles over te dragen.

## <a name="support-to-tls-12"></a>Ondersteuning voor TLS 1,2

TLS 1,2 is de veilige manier van communicatie die door micro soft wordt voorgesteld met de beste versleuteling van de klasse. MABS ondersteunt nu TLS 1,2-communicatie tussen MABS en de beveiligde servers, voor verificatie op basis van certificaten en voor Cloud back-ups.

## <a name="vmware-vm-protection-support"></a>Ondersteuning voor VMware VM-beveiliging

VM-back-ups van VMware worden nu ondersteund voor productie-implementaties. MABS V3 biedt het volgende voor VMware VM-beveiliging:

- Ondersteuning voor vCenter en ESXi 6,5, samen met ondersteuning voor 5,5 en 6,0.
- Automatische beveiliging van virtuele VMware-machines in de Cloud. Als nieuwe virtuele VMware-machines worden toegevoegd aan een beveiligde map, worden ze automatisch beveiligd op schijf en in de Cloud.
- Herstel efficiëntie verbeteringen voor het herstellen van een alternatieve locatie in VMware.

## <a name="sql-2017-support"></a>Ondersteuning voor SQL 2017

MABS v3 kan worden geïnstalleerd met SQL 2017 als de MABS-data base. U kunt de SQL-Server upgraden van SQL 2016 naar SQL 2017 of het product vernieuwen. U kunt ook een back-up maken van de SQL 2017-werk belasting in geclusterde en niet-geclusterde omgevingen met MABS v3.

## <a name="windows-server-2019-support"></a>Ondersteuning voor Windows Server 2019

MABS v3 kan worden geïnstalleerd op Windows Server 2019. Als u MABS v3 wilt gebruiken met WS2019, kunt u uw besturings systeem upgraden naar WS2019 voordat u installeert/bijwerkt naar MABS v3 of u kunt een upgrade uitvoeren van uw besturings systeem na het installeren/upgraden van v3 op WS2016.

MABS v3 is een volledige versie en kan rechtstreeks worden geïnstalleerd op Windows Server 2016, Windows Server 2019 of worden bijgewerkt vanaf MABS v2. Lees over de installatie vereisten voordat u een upgrade uitvoert naar of een back-upserver v3 installeert.
Meer informatie over de installatie-en upgrade stappen voor MABS vindt u [hier](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).

> [!NOTE]
>
> MABS heeft dezelfde code base als System Center Data Protection Manager. MABS v3 is gelijk aan Data Protection Manager 1807.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het voorbereiden van uw server of het beveiligen van een workload:

- [Bekende problemen in MABS v3](backup-mabs-release-notes-v3.md)
- [Werk belastingen voor de back-upserver voorbereiden](backup-azure-microsoft-azure-backup.md)
- [Back-upserver gebruiken om een back-up te maken van een VMware-Server](backup-azure-backup-server-vmware.md)
- [Back-upserver gebruiken om back-ups te maken van SQL Server](backup-azure-sql-mabs.md)
- [Modern Backup Storage gebruiken met een back-upserver](backup-mabs-add-storage.md)
