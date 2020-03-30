---
title: Ondersteuningsmatrix voor de MARS-agent
description: In dit artikel wordt een overzicht van azure backup-ondersteuning samengevat wanneer u een back-up maakt van machines waarop de MARS-agent (Microsoft Azure Recovery Services) wordt uitgevoerd.
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 6085bc647c06b5907282460a2d8706b8549e1bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247863"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Ondersteuningsmatrix voor back-up met de MARS-agent (Microsoft Azure Recovery Services)

U de [Azure Backup-service](backup-overview.md) gebruiken om een back-up te maken van on-premises machines en apps en om een back-up te maken van virtuele Azure-machines (VM's). In dit artikel worden ondersteuningsinstellingen en -beperkingen samengevat wanneer u de MARS-agent (Microsoft Azure Recovery Services) gebruikt om een back-up van machines te maken.

## <a name="the-mars-agent"></a>De MARS-agent

Azure Backup maakt gebruik van de MARS-agent om back-ups te maken van gegevens van on-premises machines en Azure VM's naar een vault voor back-upherstelservices in Azure. De MARS-agent kan:

- Voer on-premises Windows-machines uit, zodat ze rechtstreeks een back-up kunnen maken naar een vault voor back-upherstelservices in Azure.
- Voer op Windows VM's, zodat ze direct een back-up naar een kluis kunnen maken.
- Uitvoeren op Microsoft Azure Backup Server (MABS) of een DPM-server (System Center Data Protection Manager). In dit scenario maken machines en workloads een back-up naar MABS of naar de DPM-server. De MARS-agent maakt vervolgens een back-up van deze server naar een kluis in Azure.

> [!NOTE]
>Azure Backup biedt geen ondersteuning voor automatische aanpassing van de klok voor zomertijd (Zomertijd). Wijzig het beleid om ervoor te zorgen dat rekening wordt gehouden met zomerbesparingen om discrepantie tussen de werkelijke tijd en de geplande back-uptijd te voorkomen.

Uw back-upopties zijn afhankelijk van waar de agent is geïnstalleerd. Zie [Azure Backup-architectuur met de MARS-agent](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)voor meer informatie. Zie [Back-ups maken van DPM of MABS voor](backup-architecture.md#architecture-back-up-to-dpmmabs)informatie over MABS- en DPM-back-uparchitectuur. Zie ook [vereisten](backup-support-matrix-mabs-dpm.md) voor de back-uparchitectuur.

**Installeren** | **Details**
--- | ---
Download de nieuwste MARS-agent | U de nieuwste versie van de agent downloaden uit de kluis, of [direct downloaden.](https://aka.ms/azurebackup_agent)
Direct op een machine installeren | U de MARS-agent rechtstreeks installeren op een on-premises Windows-server of op een Windows-vm waarop een van de [ondersteunde besturingssystemen](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)wordt uitgevoerd.
Installeren op een back-upserver | Wanneer u DPM of MABS instelt om een back-up te maken van Azure, downloadt en installeert u de MARS-agent op de server. U de agent installeren op [ondersteunde besturingssystemen](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) in de ondersteuningsmatrix voor back-upservers.

> [!NOTE]
> Azure VM's die zijn ingeschakeld voor back-up hebben standaard een Azure Backup-extensieinstallatie. Deze extensie maakt een back-up van de hele VM. U de MARS-agent naast de extensie installeren en uitvoeren op een Azure VM als u een back-up wilt maken van specifieke mappen en bestanden, in plaats van de volledige VM.
> Wanneer u de MARS-agent uitvoert op een Azure VM, maakt deze een back-up van bestanden of mappen die zich in tijdelijke opslag op de VM bevinden. Back-ups mislukken als de bestanden of mappen uit de tijdelijke opslag worden verwijderd of als de tijdelijke opslag wordt verwijderd.

## <a name="cache-folder-support"></a>Ondersteuning voor cachemappen

Wanneer u de MARS-agent gebruikt om een back-up van gegevens te maken, maakt de agent een momentopname van de gegevens en slaat deze op in een map met lokale cache voordat deze de gegevens naar Azure verzendt. De cachemap (scratch) heeft verschillende vereisten:

**Cache** | **Details**
--- | ---
Grootte |  Vrije ruimte in de cachemap moet ten minste 5 tot 10 procent van de totale grootte van uw back-upgegevens zijn.
Locatie | De cachemap moet lokaal worden opgeslagen op de machine waarvan een back-up wordt gemaakt en moet online zijn. De cachemap mag niet op een netwerkshare, op verwisselbare media of op een offline volume staan.
Map | De cachemap mag niet worden versleuteld op een gededupliceerd volume of in een map die is gecomprimeerd, dat schaars is of een reparse punt heeft.
Locatiewijzigingen | U de cachelocatie wijzigen door`net stop bengine`de back-upengine ( ) te stoppen en de cachemap naar een nieuw station te kopiëren. (Zorg ervoor dat de nieuwe schijf voldoende ruimte heeft.) Werk vervolgens twee registervermeldingen onder **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** **(Config/ScratchLocation** en **Config/CloudBackupProvider/ScratchLocation)** bij naar de nieuwe locatie en start de engine opnieuw op.

## <a name="networking-and-access-support"></a>Ondersteuning voor netwerken en toegang

### <a name="url-and-ip-access"></a>URL- en IP-toegang

De MARS-agent heeft toegang nodig tot deze URL's:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net

En naar deze IP-adressen:

- 20.190.128.0/18
- 40.126.0.0/18

Toegang tot alle hierboven genoemde URL's en IP-adressen maakt gebruik van het HTTPS-protocol op poort 443.

### <a name="azure-expressroute-support"></a>Azure ExpressRoute-ondersteuning

U een back-up maken van uw gegevens via Azure ExpressRoute met openbare peering (beschikbaar voor oude circuits) en Microsoft-peering. Back-up via private peering wordt niet ondersteund.

Met openbare peering: zorg voor toegang tot de volgende domeinen/adressen:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

Selecteer met Microsoft-peering de volgende services/regio's en relevante communitywaarden:

- Azure Active Directory (12076:5060)
- Microsoft Azure-regio (afhankelijk van de locatie van uw vault voor Herstelservices)
- Azure Storage (afhankelijk van de locatie van uw vault van Herstelservices)

Zie voor meer informatie de [routeringsvereisten expressroute.](https://docs.microsoft.com/azure/expressroute/expressroute-routing)

>[!NOTE]
>Public Peering is afgeschaft voor nieuwe circuits.

### <a name="throttling-support"></a>Ondersteuning voor Beperking

**Functie** | **Details**
--- | ---
Bandbreedtebeheer | Ondersteund. Gebruik in de MARS-agent **Wijzigingseigenschappen** om de bandbreedte aan te passen.
Netwerkbeperking | Niet beschikbaar voor back-upmachines waarop Windows Server 2008 R2, Windows Server 2008 SP2 of Windows 7 worden uitgevoerd.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

>[!NOTE]
> De MARS-agent ondersteunt geen Windows Server Core SKU's.

U de MARS-agent gebruiken om rechtstreeks een back-up te maken naar Azure op de onderstaande besturingssystemen waarop wordt uitgevoerd:

1. On-premises Windows-servers
2. Azure VM's met Windows

De besturingssystemen moeten 64-bits zijn en moeten de nieuwste servicespacks en -updates uitvoeren. In de volgende tabel worden de volgende besturingssystemen samengevat:

**Besturingssysteem** | **Bestanden/mappen** | **Systeemstatus** | **Vereisten voor software/module**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Ja | Nee |  De bijbehorende serverversie controleren op software-/modulevereisten
Windows 8.1 (Enterprise, Pro)| Ja |Nee | De bijbehorende serverversie controleren op software-/modulevereisten
Windows 8 (Enterprise, Pro) | Ja | Nee | De bijbehorende serverversie controleren op software-/modulevereisten
Windows Server 2016 (Standaard, Datacenter, Essentials) | Ja | Ja | - .NET 4.5 <br> - Windows PowerShell <br> - Laatste compatibele Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Standaard, Datacenter, Foundation, Essentials) | Ja | Ja | - .NET 4.5 <br> - Windows PowerShell <br> - Laatste compatibele Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standaard, Datacenter, Foundation) | Ja | Ja |- .NET 4.5 <br> -Windows PowerShell <br> - Laatste compatibele Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0 <br> - Implementatie Image Servicing and Management (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standaard, werkgroep) | Ja | Nee | - .NET 4.5 <br> - Windows PowerShell <br> - Laatste compatibele Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Standaard, Datacenter, Essentials) | Ja | Ja | - .NET 4.5 <br> - Windows PowerShell <br> - Laatste compatibele Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0

Zie [Ondersteunde MABS- en DPM-besturingssystemen](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)voor meer informatie .

### <a name="operating-systems-at-end-of-support"></a>Besturingssystemen aan het einde van de ondersteuning

De volgende besturingssystemen zijn aan het einde van de ondersteuning en het wordt sterk aanbevolen om het besturingssysteem te upgraden om beschermd te blijven.

Als bestaande toezeggingen het upgraden van het besturingssysteem verhinderen, u overwegen de Windows-servers naar Azure VM's te migreren en Azure VM-back-ups te gebruiken om beschermd te blijven. Ga hier naar de [migratiepagina](https://azure.microsoft.com/migration/windows-server/) voor meer informatie over het migreren van uw Windows-server.

Voor on-premises of gehoste omgevingen, waar u het besturingssysteem niet upgraden of migreren naar Azure, activeert u Extended Security Updates voor de machines om beschermd en ondersteund te blijven. Merk op dat alleen specifieke edities in aanmerking komen voor uitgebreide beveiligingsupdates. Ga naar de [faq-pagina](https://www.microsoft.com/cloud-platform/extended-security-updates) voor meer informatie.

| **Besturingssysteem**                                       | **Bestanden/mappen** | **Systeemstatus** | **Vereisten voor software/module**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Ja               | Nee                 | De bijbehorende serverversie controleren op software-/modulevereisten |
| Windows Server 2008 R2 (standaard, onderneming, datacenter, foundation) | Ja               | Ja                | - .NET 3,5, .NET 4,5 <br>  - Windows PowerShell <br>  - Compatibel Microsoft VC++ Redistributable <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Implementatie Image Servicing and Management (DISM.exe) |
| Windows Server 2008 SP2 (Standaard, Datacenter, Foundation)  | Ja               | Nee                 | - .NET 3,5, .NET 4,5 <br>  - Windows PowerShell <br>  - Compatibel Microsoft VC++ Redistributable <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Implementatie Image Servicing and Management (DISM.exe) <br>  - Basis virtuele server 2005 + KB948515 |

## <a name="backup-limits"></a>Back-uplimieten

### <a name="size-limits"></a>Groottelimieten

Azure Backup beperkt de grootte van een bestands- of mapgegevensbron waarvan een back-up kan worden gemaakt. De items waarvan u een back-up maakt van één volume, mogen de groottes die in deze tabel zijn samengevat, niet overschrijden:

**Besturingssysteem** | **Maximale grootte**
--- | ---
Windows Server 2012 of hoger |54.400 GB
Windows Server 2008 R2 SP1 |1.700 GB
Windows Server 2008 SP2| 1.700 GB
Windows 8 of hoger| 54.400 GB
Windows 7| 1.700 GB

### <a name="other-limitations"></a>Andere beperkingen

- MARS ondersteunt geen bescherming van meerdere machines met dezelfde naam in één kluis.

## <a name="supported-file-types-for-backup"></a>Ondersteunde bestandstypen voor back-up

**Type** | **Ondersteuning**
--- | ---
Gecodeerde<sup>*</sup>| Ondersteund.
Gecomprimeerd | Ondersteund.
Sparse | Ondersteund.
Gecomprimeerd en schaars |Ondersteund.
Vaste koppelingen| Wordt niet ondersteund. Overgeslagen.
Reparsepunt| Wordt niet ondersteund. Overgeslagen.
Versleuteld en schaars |Wordt niet ondersteund. Overgeslagen.
Gecomprimeerde stroom| Wordt niet ondersteund. Overgeslagen.
Schaarse stroom| Wordt niet ondersteund. Overgeslagen.
OneDrive (gesynchroniseerde bestanden zijn schaarse streams)| Wordt niet ondersteund.
Mappen met DFS-replicatie ingeschakeld | Wordt niet ondersteund.

\*Zorg ervoor dat de MARS-agent toegang heeft tot de vereiste certificaten om toegang te krijgen tot de versleutelde bestanden. Ontoegankelijke bestanden worden overgeslagen.

## <a name="supported-drives-or-volumes-for-backup"></a>Ondersteunde schijven of volumes voor back-up

**Station/volume** | **Ondersteuning** | **Details**
--- | --- | ---
Alleen-lezen volumes| Niet ondersteund | De Shadow Service (Volume Copy Shadow Service) werkt alleen als het volume beschrijfbaar is.
Offline volumes| Niet ondersteund |VSS werkt alleen als het volume online is.
Netwerkaandeel| Niet ondersteund |Het volume moet lokaal op de server zijn.
BitLocker-vergrendelde volumes| Niet ondersteund |Het volume moet worden ontgrendeld voordat de back-up wordt gestart.
Identificatie van bestandssystemen| Niet ondersteund |Alleen NTFS wordt ondersteund.
Verwisselbare media| Niet ondersteund |Alle bronnen van back-upitems moeten een *vaste* status hebben.
Gededuplicated stations | Ondersteund | Azure Backup converteert gededupliceerd gegevens naar normale gegevens. Het optimaliseert, versleutelt, slaat op en stuurt de gegevens naar de kluis.

## <a name="support-for-initial-offline-backup"></a>Ondersteuning voor de eerste offline back-up

Azure Backup ondersteunt *offline zaaien* om de eerste back-upgegevens over te zetten naar Azure met behulp van schijven. Deze ondersteuning is handig als uw eerste back-up waarschijnlijk in het bereik van terabytes (TBs) is. Offline back-up wordt ondersteund voor:

- Directe back-up van bestanden en mappen op on-premises machines waarop de MARS-agent wordt uitgevoerd.
- Back-up van workloads en bestanden van een DPM-server of MABS.

Offline back-up kan niet worden gebruikt voor systeemstatusbestanden.

## <a name="support-for-data-restoration"></a>Ondersteuning voor gegevensherstel

Met de functie [Direct herstellen](backup-instant-restore-capability.md) van Azure Backup u gegevens herstellen voordat deze naar de kluis worden gekopieerd. De machine die u een back-up maakt, moet .NET Framework 4.5.2 of hoger draaien.

Back-ups kunnen niet worden hersteld naar een doelmachine waarop een eerdere versie van het besturingssysteem wordt uitgevoerd. Een back-up die is gemaakt van een computer waarop Windows 7 wordt uitgevoerd, kan bijvoorbeeld worden hersteld op Windows 8 of hoger. Maar een back-up die is genomen van een computer waarop Windows 8 wordt uitgevoerd, kan niet worden hersteld op een computer waarop Windows 7 wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [back-uparchitectuur die de MARS-agent gebruikt.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
- Meer informatie over wat wordt ondersteund wanneer u [de MARS-agent uitvoert op MABS of een DPM-server.](backup-support-matrix-mabs-dpm.md)
