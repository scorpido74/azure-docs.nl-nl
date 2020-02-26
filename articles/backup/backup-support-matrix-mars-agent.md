---
title: Ondersteunings matrix voor de MARS-agent
description: Dit artikel bevat een overzicht van Azure Backup ondersteuning bij het maken van een back-up van computers waarop de Microsoft Azure Recovery Services-agent (MARS) wordt uitgevoerd.
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: ef57688dd7b5ccee4e71ac0a54138ac567320aa2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582633"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Ondersteunings matrix voor back-up met de Microsoft Azure Recovery Services-agent (MARS)

U kunt de [Azure backup-service](backup-overview.md) gebruiken om een back-up te maken van on-premises machines en apps en om back-ups te maken van Azure virtual machines (vm's). In dit artikel vindt u een overzicht van de ondersteunings instellingen en beperkingen wanneer u de Microsoft Azure Recovery Services-agent (MARS) gebruikt voor het maken van back-ups van computers

## <a name="the-mars-agent"></a>De MARS-agent

Azure Backup maakt gebruik van de MARS-agent om back-ups te maken van gegevens van on-premises machines en Azure-Vm's naar een back-upRecovery Services kluis in Azure. De MARS-agent kan:

- Voer uit op on-premises Windows-computers, zodat ze rechtstreeks een back-up kunnen maken naar een back-upRecovery Services kluis in Azure.
- Voer uit op Windows-Vm's zodat ze rechtstreeks een back-up kunnen maken naar een kluis.
- Voer uit op Microsoft Azure Backup Server (MABS) of een System Center Data Protection Manager (DPM)-server. In dit scenario maken machines en workloads een back-up naar MABS of naar de DPM-server. De MARS-agent maakt vervolgens een back-up van deze server naar een kluis in Azure.

> [!NOTE]
>Azure Backup biedt geen ondersteuning voor automatische aanpassing van de klok voor zomer tijd (DST). Wijzig het beleid om er zeker van te zijn dat rekening wordt gehouden met zomer tijd om te voor komen dat er verschil is tussen de werkelijke tijd en de geplande back-up.

Uw back-upopties zijn afhankelijk van waar de agent is geïnstalleerd. Zie voor meer informatie [Azure backup architectuur met behulp van de Mars-agent](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Zie [back-up naar DPM of MABS](backup-architecture.md#architecture-back-up-to-dpmmabs)voor meer informatie over de MABS-en DPM-back-uparchitectuur. Zie ook de [vereisten](backup-support-matrix-mabs-dpm.md) voor de back-uparchitectuur.

**Installatie** | **Details**
--- | ---
De nieuwste MARS-agent downloaden | U kunt de nieuwste versie van de agent downloaden van de kluis of [deze rechtstreeks downloaden](https://aka.ms/azurebackup_agent).
Rechtstreeks op een computer installeren | U kunt de MARS-agent rechtstreeks installeren op een on-premises Windows-Server of op een Windows-VM waarop een van de [ondersteunde besturings systemen](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)wordt uitgevoerd.
Installeren op een back-upserver | Wanneer u DPM of MABS instelt om een back-up te maken naar Azure, downloadt en installeert u de MARS-agent op de-server. U kunt de agent installeren op [ondersteunde besturings systemen](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) in de ondersteunings matrix van de back-upserver.

> [!NOTE]
> Virtuele Azure-machines die zijn ingeschakeld voor back-up, hebben standaard een Azure Backup extensie-installatie. Deze uitbrei ding maakt een back-up van de volledige VM. U kunt de MARS-agent op een virtuele machine van Azure installeren en uitvoeren naast de extensie als u een back-up wilt maken van specifieke mappen en bestanden, in plaats van de volledige VM.
> Wanneer u de MARS-agent op een virtuele machine van Azure uitvoert, worden er back-ups gemaakt van bestanden of mappen die zich in de tijdelijke opslag op de virtuele machine bevinden. Back-ups mislukken als de bestanden of mappen worden verwijderd uit de tijdelijke opslag of als de tijdelijke opslag wordt verwijderd.

## <a name="cache-folder-support"></a>Ondersteuning voor cachemap

Wanneer u de MARS-agent gebruikt voor het maken van een back-up van gegevens, neemt de agent een moment opname van de gegevens en slaat deze op in een lokale cachemap voordat de gegevens naar Azure worden verzonden. De cache (scratch)-map heeft verschillende vereisten:

**Cache** | **Details**
--- | ---
Grootte |  Beschik bare ruimte in de cachemap moet ten minste 5 tot 10 procent van de totale grootte van uw back-upgegevens zijn.
Locatie | De cachemap moet lokaal zijn opgeslagen op de computer waarvan een back-up wordt gemaakt en moet online zijn. De cachemap mag zich niet op een netwerk share, op Verwissel bare media of op een offline volume bevinden.
Map | De cachemap mag niet worden versleuteld op een ontdubbeld volume of in een gecomprimeerde map, een sparse of een reparsepunt.
Locatie wijzigingen | U kunt de locatie van de cache wijzigen door de back-upengine (`net stop bengine`) te stoppen en de cachemap naar een nieuw station te kopiëren. (Zorg ervoor dat er voldoende ruimte beschikbaar is op het nieuwe station.) Werk vervolgens twee Register vermeldingen onder **HKLM\SOFTWARE\Microsoft\Windows Azure backup** (**config/ScratchLocation** en **config/CloudBackupProvider/ScratchLocation**) bij naar de nieuwe locatie en start de engine opnieuw.

## <a name="networking-and-access-support"></a>Ondersteuning voor netwerken en toegang

### <a name="url-and-ip-access"></a>URL-en IP-toegang

De MARS-agent moet toegang hebben tot deze Url's:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

En naar deze IP-adressen:

- 20.190.128.0/18
- 40.126.0.0/18

De toegang tot alle hierboven vermelde Url's en IP-adressen maakt gebruik van het HTTPS-protocol op poort 443.

### <a name="azure-expressroute-support"></a>Ondersteuning voor Azure ExpressRoute

U kunt een back-up maken van uw gegevens via Azure ExpressRoute met open bare peering (beschikbaar voor oude circuits) en micro soft-peering. Back-up via privé-peering wordt niet ondersteund.

Met open bare peering: Zorg ervoor dat u toegang hebt tot de volgende domeinen/adressen:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

Bij micro soft-peering moet u de volgende services/regio's en relevante Community-waarden selecteren:

- Azure Active Directory (12076:5060)
- Microsoft Azure regio (op basis van de locatie van uw Recovery Services kluis)
- Azure Storage (op basis van de locatie van uw Recovery Services kluis)

Zie de [ExpressRoute-routerings vereisten](https://docs.microsoft.com/azure/expressroute/expressroute-routing)voor meer informatie.

>[!NOTE]
>Open bare peering is afgeschaft voor nieuwe circuits.

### <a name="throttling-support"></a>Ondersteuning voor beperking

**Functie** | **Details**
--- | ---
Bandbreedte regeling | Ondersteund. In de MARS-agent gebruikt u **Eigenschappen wijzigen** om de band breedte aan te passen.
Netwerk beperking | Niet beschikbaar voor back-ups van computers waarop Windows Server 2008 R2, Windows Server 2008 SP2 of Windows 7 wordt uitgevoerd.

## <a name="support-for-direct-backups"></a>Ondersteuning voor directe back-ups

>[!NOTE]
> De MARS-agent biedt geen ondersteuning voor Windows Server Core-Sku's.

U kunt de MARS-agent gebruiken om rechtstreeks een back-up naar Azure te maken op de volgende besturings systemen die worden uitgevoerd op:

1. On-premises Windows-servers
2. Virtuele Azure-machines waarop Windows wordt uitgevoerd

De besturings systemen moeten 64 bits zijn en moeten de nieuwste service packs en updates uitvoeren. De volgende tabel bevat een overzicht van deze besturings systemen:

**Besturingssysteem** | **Bestanden/mappen** | **Systeem status** | **Vereisten voor software/modules**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Ja | Nee |  Controleer de bijbehorende server versie voor vereisten voor software/modules
Windows 8,1 (Enter prise, Pro)| Ja |Nee | Controleer de bijbehorende server versie voor vereisten voor software/modules
Windows 8 (Enterprise, Pro) | Ja | Nee | Controleer de bijbehorende server versie voor vereisten voor software/modules
Windows 7 (Ultimate, Enter prise, Pro, Home Premium/Basic, starter) | Ja | Nee | Controleer de bijbehorende server versie voor vereisten voor software/modules
Windows Server 2016 (Standard, Data Center, Essentials) | Ja | Ja | -.NET 4,5 <br> -Windows Power shell <br> -Nieuwste compatibele micro soft VC + + te distribueren pakket <br> -Micro soft Management Console (MMC) 3,0
Windows Server 2012 R2 (Standard, Data Center, Foundation, Essentials) | Ja | Ja | -.NET 4,5 <br> -Windows Power shell <br> -Nieuwste compatibele micro soft VC + + te distribueren pakket <br> -Micro soft Management Console (MMC) 3,0
Windows Server 2012 (Standard, Data Center, Foundation) | Ja | Ja |-.NET 4,5 <br> -Windows Power shell <br> -Nieuwste compatibele micro soft VC + + te distribueren pakket <br> -Micro soft Management Console (MMC) 3,0 <br> -Deployment Image Servicing and Management (DISM. exe)
Windows Storage Server 2016/2012 R2/2012 (standaard, werk groep) | Ja | Nee | -.NET 4,5 <br> -Windows Power shell <br> -Nieuwste compatibele micro soft VC + + te distribueren pakket <br> -Micro soft Management Console (MMC) 3,0
Windows Server 2019 (Standard, Data Center, Essentials) | Ja | Ja | -.NET 4,5 <br> -Windows Power shell <br> -Nieuwste compatibele micro soft VC + + te distribueren pakket <br> -Micro soft Management Console (MMC) 3,0

Zie [supported MABS and DPM Operating Systems](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)(Engelstalig) voor meer informatie.

## <a name="backup-limits"></a>Back-uplimieten

### <a name="size-limits"></a>Maximale grootte

Azure Backup beperkt de grootte van een gegevens bron van een bestand of map waarvan een back-up kan worden gemaakt. De items waarvan u een back-up hebt gemaakt vanaf één volume mogen niet groter zijn dan de grootte die in deze tabel wordt weer gegeven:

**Besturingssysteem** | **Maximale grootte**
--- | ---
Windows Server 2012 of hoger |54.400 GB
Windows Server 2008 R2 SP1 |1\.700 GB
Windows Server 2008 SP2| 1\.700 GB
Windows 8 of hoger| 54.400 GB
Windows 7| 1\.700 GB

### <a name="other-limitations"></a>Andere beperkingen

- MARS biedt geen ondersteuning voor de beveiliging van meerdere machines met dezelfde naam naar één kluis.

## <a name="supported-file-types-for-backup"></a>Ondersteunde bestands typen voor back-up

**Type** | **Ondersteuning**
--- | ---
Versleuteld| Ondersteund.
Gecomprimeerd | Ondersteund.
Sparse | Ondersteund.
Gecomprimeerd en verspreid |Ondersteund.
Vaste koppelingen| Wordt niet ondersteund. Genegeerd.
Reparsepunt| Wordt niet ondersteund. Genegeerd.
Versleuteld en verspreid |Wordt niet ondersteund. Genegeerd.
Gecomprimeerde stream| Wordt niet ondersteund. Genegeerd.
Sparse stream| Wordt niet ondersteund. Genegeerd.
OneDrive (gesynchroniseerde bestanden zijn sparse-streams)| Wordt niet ondersteund.
Mappen met DSF-replicatie ingeschakeld | Wordt niet ondersteund.

## <a name="supported-drives-or-volumes-for-backup"></a>Ondersteunde stations of volumes voor back-up

**Station/volume** | **Ondersteuning** | **Details**
--- | --- | ---
Alleen-lezen volumes| Niet ondersteund | Volume Copy Shadow Service (VSS) werkt alleen als het volume schrijfbaar is.
Offline volumes| Niet ondersteund |VSS werkt alleen als het volume online is.
Netwerk share| Niet ondersteund |Het volume moet lokaal op de server zijn.
Met BitLocker vergrendelde volumes| Niet ondersteund |Het volume moet worden ontgrendeld voordat de back-up wordt gestart.
Bestandssysteem identificatie| Niet ondersteund |Alleen NTFS wordt ondersteund.
Verwissel bare media| Niet ondersteund |Alle bronnen van back-upitems moeten een *vaste* status hebben.
Ontdubbelde stations | Ondersteund | Azure Backup converteert ontdubbelde gegevens naar normale gegevens. De gegevens worden geoptimaliseerd, versleuteld, opgeslagen en verzonden naar de kluis.

## <a name="support-for-initial-offline-backup"></a>Ondersteuning voor eerste offline back-up

Azure Backup ondersteunt *offline seeding* om de eerste back-upgegevens over te dragen naar Azure met behulp van schijven. Deze ondersteuning is handig als de eerste back-up waarschijnlijk in het omvang bereik van terabytes (TBs) ligt. Offline back-up wordt ondersteund voor:

- Directe back-ups van bestanden en mappen op on-premises machines waarop de MARS-agent wordt uitgevoerd.
- Back-ups van werk belastingen en bestanden van een DPM-server of MABS.

Offline back-up kan niet worden gebruikt voor systeem status bestanden.

## <a name="support-for-data-restoration"></a>Ondersteuning voor het herstellen van gegevens

Met de functie voor [direct terugzetten](backup-instant-restore-capability.md) van Azure Backup kunt u gegevens herstellen voordat deze naar de kluis worden gekopieerd. Op de machine waarvan u een back-up maakt, moet .NET Framework 4.5.2 of hoger worden uitgevoerd.

Back-ups kunnen niet worden hersteld naar een doel computer waarop een eerdere versie van het besturings systeem wordt uitgevoerd. U kunt bijvoorbeeld een back-up die is gemaakt van een computer waarop Windows 7 wordt uitgevoerd, herstellen in Windows 8 of hoger. Een back-up die is gemaakt vanaf een computer waarop Windows 8 wordt uitgevoerd, kan echter niet worden hersteld op een computer waarop Windows 7 wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [back-uparchitectuur die gebruikmaakt van de Mars-agent](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Meer informatie over wat er wordt ondersteund wanneer u [de Mars-agent op MABS of een DPM-server uitvoert](backup-support-matrix-mabs-dpm.md).
