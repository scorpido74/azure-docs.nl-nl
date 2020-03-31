---
title: Ondersteuningsmatrix voor back-up van Azure-VM
description: Biedt een overzicht van ondersteuningsinstellingen en -beperkingen bij het maken van back-ups van Azure VM's met de Azure Backup-service.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: d86ce94c62ec9f25b364e9fdc963e3043b274722
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389287"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Ondersteuningsmatrix voor back-up van Azure-VM

U de [Azure Backup-service](backup-overview.md) gebruiken om een back-up te maken van on-premises machines en workloads en Virtuele Azure-machines (VM's). In dit artikel worden ondersteuningsinstellingen en -beperkingen samengevat wanneer u een back-up maakt van Azure VM's met Azure Backup.

Andere ondersteuning matrices:

- [Matrix voor algemene ondersteuning](backup-support-matrix.md) voor Azure Backup
- [Ondersteuningsmatrix](backup-support-matrix-mabs-dpm.md) voor DPM-back-up (Azure Backup Server/System Center Data Protection Manager)
- [Ondersteuningsmatrix](backup-support-matrix-mars-agent.md) voor back-up met de MARS-agent (Microsoft Azure Recovery Services)

## <a name="supported-scenarios"></a>Ondersteunde scenario's

U als volgt een back-up maken en Azure VM's herstellen met de Azure Backup-service.

**Scenario** | **Backup** | **Agent** |**Herstellen**
--- | --- | --- | ---
Directe back-up van Azure VM's  | Maak een back-up van de hele VM.  | Er is geen extra agent nodig op de Azure VM. Azure Backup installeert en gebruikt een extensie voor de [Azure VM-agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) die op de VM wordt uitgevoerd. | Als volgt herstellen:<br/><br/> - **Een basisvm maken.** Dit is handig als de VM geen speciale configuratie heeft, zoals meerdere IP-adressen.<br/><br/> - **De VM-schijf herstellen**. Herstel de schijf. Bevestig deze vervolgens aan een bestaande virtuele machine of maak een nieuwe virtuele machine van de schijf met PowerShell.<br/><br/> - **VM-schijf vervangen**. Als er een virtuele machine bestaat en deze beheerde schijven gebruikt (onversleuteld), u een schijf herstellen en deze gebruiken om een bestaande schijf op de virtuele machine te vervangen.<br/><br/> - **Specifieke bestanden/mappen herstellen**. U bestanden/mappen herstellen vanuit een vm in plaats van vanuit de hele virtuele machine.
Directe back-up van Azure VM's (alleen Windows)  | Een back-up maken van specifieke bestanden/mappen/volume. | Installeer de [azure recovery services-agent](backup-azure-file-folder-backup-faq.md).<br/><br/> U de MARS-agent naast de back-upextensie voor de Azure VM-agent uitvoeren om een back-up te maken van de VM op bestands-/mapniveauniveau. | Specifieke mappen/bestanden herstellen.
Back-ups maken van Azure VM naar back-upserver  | Back-ups maken van bestanden/mappen/volumes; systeemstatus/bare metal-bestanden; app-gegevens naar System Center DPM of naar Microsoft Azure Backup Server (MABS).<br/><br/> DPM/MABS maakt dan een back-up naar de back-upkluis. | Installeer de DPM/MABS-beveiligingsagent op de VM. De MARS-agent is geïnstalleerd op DPM/MABS.| Bestanden/mappen/volumes herstellen; systeemstatus/bare metal-bestanden; app-gegevens.

Meer informatie over back-ups [via een back-upserver](backup-architecture.md#architecture-back-up-to-dpmmabs) en [over ondersteuningsvereisten.](backup-support-matrix-mabs-dpm.md)

>[!NOTE]
> **Azure Backup ondersteunt nu selectieve schijfback-up en herstel met behulp van de Azure Virtual Machine-back-upoplossing.**
>
>Tegenwoordig ondersteunt Azure Backup een back-up van alle schijven (besturingssysteem en gegevens) in een VM samen met behulp van de back-upoplossing voor virtuele machines. Met de functionaliteit van de uitgesloten schijf krijgt u een optie om een of enkele back-ups te maken van de vele gegevensschijven in een vm. Dit biedt een efficiënte en kosteneffectieve oplossing voor uw back-up- en herstelbehoeften. Elk herstelpunt bevat gegevens van de schijven die zijn opgenomen in de back-upbewerking, waarmee u verder een subset van schijven laten herstellen vanaf het gegeven herstelpunt tijdens de herstelbewerking. Dit geldt voor het herstellen van zowel de momentopname als de kluis.
>
>**Als u zich wilt aanmelden voor de preview, schrijft u ons opAskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Ondersteunde back-upacties

**Actie** | **Ondersteuning**
--- | ---
Een back-up maken van een VM die de vm afsluiten/offline afsluit | Ondersteund.<br/><br/> Momentopname is alleen crashconsistent, niet app-consistent.
Back-ups maken van schijven na migratie naar beheerde schijven | Ondersteund.<br/><br/> Back-up blijft werken. Geen actie vereist.
Back-ups maken van beheerde schijven na het inschakelen van resourcegroepvergrendeling | Wordt niet ondersteund.<br/><br/> Azure Backup kan de oudere herstelpunten niet verwijderen en back-ups mislukken wanneer de maximale limiet aan herstelpunten is bereikt.
Back-upbeleid voor een virtuele machine wijzigen | Ondersteund.<br/><br/> Er wordt een back-up van de VM gemaakt door gebruik te maken van de plannings- en bewaarinstellingen in nieuw beleid. Als de bewaarinstellingen worden verlengd, worden bestaande herstelpunten gemarkeerd en bewaard. Als ze worden verminderd, worden bestaande herstelpunten gesnoeid in de volgende opruimtaak en uiteindelijk verwijderd.
Een back-uptaak annuleren| Ondersteund tijdens het momentopnameproces.<br/><br/> Niet ondersteund wanneer de momentopname wordt overgebracht naar de kluis.
Een back-up maken van de VM naar een andere regio of abonnement |Wordt niet ondersteund.
Back-ups per dag (via de Azure VM-extensie) | Eén geplande back-up per dag.<br/><br/>De Azure Backup-service ondersteunt maximaal negen on-demand back-ups per dag, maar Microsoft raadt niet meer dan vier dagelijkse on-demand back-ups aan om de beste prestaties te garanderen.
Back-ups per dag (via de MARS-agent) | Drie geplande back-ups per dag.
Back-ups per dag (via DPM/MABS) | Twee geplande back-ups per dag.
Maandelijkse/jaarlijkse back-up| Niet ondersteund bij het maken van back-ups met Azure VM-extensie. Alleen dagelijks en wekelijks wordt ondersteund.<br/><br/> U het beleid instellen om dagelijkse/wekelijkse back-ups te behouden voor een maandelijkse/jaarlijkse bewaarperiode.
Automatische klokaanpassing | Wordt niet ondersteund.<br/><br/> Azure Backup past zich niet automatisch aan voor wijzigingen in de zomertijd wanneer u een back-up maakt van een virtuele machine.<br/><br/>  Wijzig het beleid indien nodig handmatig.
[Beveiligingsfuncties voor hybride back-up](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Het uitschakelen van beveiligingsfuncties wordt niet ondersteund.
Maak een back-up van de VM waarvan de machinetijd is gewijzigd | Wordt niet ondersteund.<br/><br/> Als de machinetijd wordt gewijzigd in een toekomstige datumtijd nadat de back-up voor die vm is ingemaakt; Maar zelfs als de tijdswijziging wordt teruggedraaid, is een succesvolle back-up niet gegarandeerd.  

## <a name="operating-system-support-windows"></a>Ondersteuning voor het besturingssysteem (Windows)

In de volgende tabel worden de ondersteunde besturingssystemen samengevat bij het maken van back-ups van Windows Azure VM's.

**Scenario** | **Ondersteuning voor besturingssysteem**
--- | ---
Back-ups maken met Azure VM-agentextensie | - Windows 10-client (alleen 64 bits) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM en Sp1 Standard)  <br/><br/> - Windows Server 2008 (alleen 64 bits)
Back-up met MARS-agent | [Ondersteunde besturingssystemen.](backup-support-matrix-mars-agent.md#supported-operating-systems)
Back-up smaken met DPM/MABS | Ondersteunde besturingssystemen voor back-up met [MABS](backup-mabs-protection-matrix.md) en [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

Azure Backup biedt geen ondersteuning voor 32-bits besturingssystemen.

## <a name="support-for-linux-backup"></a>Ondersteuning voor Linux-back-up

Dit wordt ondersteund als u een back-up wilt maken van Linux-machines.

**Actie** | **Ondersteuning**
--- | ---
Back-ups maken van Linux Azure VM's met de Linux Azure VM-agent | Bestand consistente back-up.<br/><br/> App-consistente back-up met behulp van [aangepaste scripts](backup-azure-linux-app-consistent.md).<br/><br/> Tijdens het herstellen u een nieuwe virtuele machine maken, een schijf herstellen en deze gebruiken om een vm te maken, of een schijf herstellen en deze gebruiken om een schijf op een bestaande virtuele machine te vervangen. U ook afzonderlijke bestanden en mappen herstellen.
Back-ups maken van Linux Azure VM's met MARS-agent | Wordt niet ondersteund.<br/><br/> De MARS-agent kan alleen worden geïnstalleerd op Windows-machines.
Back-ups maken van Linux Azure VM's met DPM/MABS | Wordt niet ondersteund.

## <a name="operating-system-support-linux"></a>Ondersteuning voor het besturingssysteem (Linux)

Voor Azure VM Linux-back-ups ondersteunt Azure Backup de lijst met [Linux-distributies die zijn goedgekeurd door Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Houd rekening met het volgende:

- Azure Backup biedt geen ondersteuning voor Core OS Linux.
- Azure Backup biedt geen ondersteuning voor 32-bits besturingssystemen.
- Andere bring-your-own Linux-distributies kunnen werken zolang de [Azure VM-agent voor Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) beschikbaar is op de VM en zolang Python wordt ondersteund.
- Azure Backup biedt geen ondersteuning voor een linux-vm die is geconfigureerd met proxy als python-versie 2.7 niet is geïnstalleerd.

## <a name="backup-frequency-and-retention"></a>Back-upfrequentie en -retentie

**Instelling** | **Grenzen**
--- | ---
Maximale herstelpunten per beveiligde instantie (machine/werkbelasting) | 9999.
Maximale vervaltijd voor een herstelpunt | Geen limiet.
Maximale back-upfrequentie naar kluis (Azure VM-extensie) | Eén keer per dag.
Maximale back-upfrequentie naar kluis (MARS-agent) | Drie back-ups per dag.
Maximale back-upfrequentie naar DPM/MABS | Elke 15 minuten voor SQL Server.<br/><br/> Een keer per uur voor andere workloads.
Bewaarperiode van herstelpunt | Dagelijks, wekelijks, maandelijks en jaarlijks.
Maximale bewaarperiode | Afhankelijk van back-upfrequentie.
Herstelpunten op DPM-/MABS-schijf | 64 voor bestandsservers en 448 voor app-servers.<br/><br/> Tapeherstelpunten zijn onbeperkt voor on-premises DPM.

## <a name="supported-restore-methods"></a>Ondersteunde herstelmethoden

**Herstel, optie** | **Details**
--- | ---
**Een nieuwe VM maakt** | Maakt snel een basis-VM up and running vanaf een herstelpunt.<br/><br/> U een naam voor de VM opgeven, de brongroep en het virtuele netwerk (VNet) selecteren waarin deze wordt geplaatst en een opslagaccount opgeven voor de herstelde vm. De nieuwe vm moet worden gemaakt in dezelfde regio als de bron-VM.
**Schijf herstellen** | Hiermee herstelt u een VM-schijf, die vervolgens kan worden gebruikt om een nieuwe virtuele machine te maken.<br/><br/> Azure Backup biedt een sjabloon waarmee u een vm aanpassen en maken. <br/><br> Met de hersteltaak genereert u een sjabloon die u downloaden en gebruiken om aangepaste VM-instellingen op te geven en een vm te maken.<br/><br/> De schijven worden gekopieerd naar de resourcegroep die u opgeeft.<br/><br/> U de schijf ook koppelen aan een bestaande virtuele machine of een nieuwe virtuele machine maken met PowerShell.<br/><br/> Deze optie is handig als u de vm wilt aanpassen, configuratie-instellingen wilt toevoegen die er niet waren op het moment van back-up, of instellingen wilt toevoegen die moeten worden geconfigureerd met behulp van de sjabloon of PowerShell.
**Bestaande schijf vervangen** | U een schijf herstellen en deze gebruiken om een schijf op de bestaande vm te vervangen.<br/><br/> De huidige VM moet bestaan. Als deze optie is verwijderd, kan deze optie niet worden gebruikt.<br/><br/> Azure Backup maakt een momentopname van de bestaande VM voordat u de schijf vervangt en slaat deze op in de opgeeflocatie die u opgeeft. Bestaande schijven die met de VM zijn verbonden, worden vervangen door het geselecteerde herstelpunt.<br/><br/> De momentopname wordt gekopieerd naar de kluis en bewaard in overeenstemming met het bewaarbeleid. <br/><br/> Na de bewerking van de schijf vervangen, blijft de oorspronkelijke schijf behouden in de brongroep. U ervoor kiezen om de originele schijven handmatig te verwijderen als ze niet nodig zijn. <br/><br/>Bestaande bestaande vervangen wordt ondersteund voor niet-versleutelde beheerde VM's. Het wordt niet ondersteund voor onbeheerde schijven, [gegeneraliseerde VM's](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)of voor VM's [die zijn gemaakt met behulp van aangepaste afbeeldingen.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)<br/><br/> Als het herstelpunt meer of minder schijven heeft dan de huidige VM, wordt het aantal schijven in het herstelpunt alleen de VM-configuratie weergegeven.<br><br> Bestaande bestaande gegevens vervangen wordt niet ondersteund door gekoppelde resources (zoals [door de gebruiker toegewezen beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) of Key [Vault),](https://docs.microsoft.com/azure/key-vault/key-vault-overview)omdat de back-upclient-app geen machtigingen heeft voor deze bronnen tijdens het uitvoeren van het herstel.
**Regio kruisen (secundaire regio)** | Herstel van verschillende regio's kan worden gebruikt om Azure VM's te herstellen in het secundaire gebied, een [azure-gekoppelde regio.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> U alle Azure VM's voor het geselecteerde herstelpunt herstellen als de back-up in de secundaire regio wordt uitgevoerd.<br><br> Deze functie is beschikbaar voor de onderstaande opties:<br> * [Een VM maken](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Schijven herstellen](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> We ondersteunen momenteel de optie [Bestaande schijven vervangen](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) niet.<br><br> Machtigingen<br> De herstelbewerking op secundair gebied kan worden uitgevoerd door back-upbeheerders en app-beheerders.

## <a name="support-for-file-level-restore"></a>Ondersteuning voor herstel op bestandsniveau

**Herstellen** | **Ondersteund**
--- | ---
Bestanden herstellen op verschillende besturingssystemen | U bestanden herstellen op elke machine met hetzelfde (of compatibele) besturingssysteem als de back-up VM. Zie de [tabel Compatibel besturingssysteem](backup-azure-restore-files-from-vm.md#system-requirements).
Bestanden herstellen van versleutelde VM's | Wordt niet ondersteund.
Bestanden herstellen van opslagaccounts met beperkte netwerkopslag | Wordt niet ondersteund.
Bestanden herstellen op VM's met Windows-opslagruimten | Herstellen wordt niet ondersteund op dezelfde vm.<br/><br/> Herstel in plaats daarvan de bestanden op een compatibele virtuele machine.
Bestanden herstellen op Linux VM met behulp van LVM/raid-arrays | Herstellen wordt niet ondersteund op dezelfde vm.<br/><br/> Herstellen op een compatibele virtuele machine.
Bestanden herstellen met speciale netwerkinstellingen | Herstellen wordt niet ondersteund op dezelfde vm. <br/><br/> Herstellen op een compatibele virtuele machine.

## <a name="support-for-vm-management"></a>Ondersteuning voor VM-beheer

In de volgende tabel wordt een overzicht van de ondersteuning voor back-up tijdens VM-beheertaken, zoals het toevoegen of vervangen van VM-schijven.

**Herstellen** | **Ondersteund**
--- | ---
Herstellen tussen abonnement/regio/zone. | Wordt niet ondersteund.
Herstellen naar een bestaande vm | Gebruik de optie Schijf vervangen.
Schijf herstellen met opslagaccount ingeschakeld voor Azure Storage Service Encryption (SSE) | Wordt niet ondersteund.<br/><br/> Herstellen naar een account dat geen SSE heeft ingeschakeld.
Herstellen naar accounts voor gemengde opslag |Wordt niet ondersteund.<br/><br/> Op basis van het type opslagaccount zijn alle herstelde schijven premium of standaard en niet gemengd.
VM rechtstreeks herstellen naar een beschikbaarheidsset | Voor beheerde schijven u de schijf herstellen en de optie beschikbaarheidsset in de sjabloon gebruiken.<br/><br/> Niet ondersteund voor onbeheerde schijven. Voor niet-beheerde schijven herstelt u de schijf en maakt u een vm in de beschikbaarheidsset.
Back-up van onbeheerde VM's herstellen na een upgrade naar beheerde VM| Ondersteund.<br/><br/> U schijven herstellen en vervolgens een beheerde vm maken.
VM herstellen om punt te herstellen voordat de VM naar beheerde schijven is gemigreerd | Ondersteund.<br/><br/> U herstelt naar niet-beheerde schijven (standaard), converteert de herstelde schijven naar beheerde schijf en maakt een VM met de beheerde schijven.
Een VM herstellen die is verwijderd. | Ondersteund.<br/><br/> U de VM herstellen vanaf een herstelpunt.
Een DC-vm (Domain Controller) herstellen die deel uitmaakt van een multi-DC-configuratie via portal | Ondersteund als u de schijf herstelt en een VM maakt met PowerShell.
VM herstellen in ander virtueel netwerk |Ondersteund.<br/><br/> Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio bevinden.

## <a name="vm-compute-support"></a>Ondersteuning voor VM-gegevensbedien

**Compute** | **Ondersteuning**
--- | ---
VM-grootte |Elke Azure VM-grootte met ten minste 2 CPU-cores en 1 GB RAM.<br/><br/> [Meer informatie.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Back-ups maken van VM's in [beschikbaarheidssets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Ondersteund.<br/><br/> U een vm niet herstellen in een beschikbare set met de optie om snel een vm te maken. In plaats daarvan, wanneer u de VM herstelt, herstelt u de schijf en gebruikt u deze om een virtuele machine te implementeren, of herstelt u een schijf en gebruikt u deze om een bestaande schijf te vervangen.
Back-ups maken van VM's die zijn geïmplementeerd met [Hub (Hybrid Use Benefit)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Ondersteund.
Back-ups maken van VM's die zijn geïmplementeerd in een [schaalset](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Wordt niet ondersteund.
Back-ups maken van VM's die zijn geïmplementeerd vanuit de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Gepubliceerd door Microsoft, derde partij) |Ondersteund.<br/><br/> De VM moet een ondersteund besturingssysteem hebben.<br/><br/> Wanneer u bestanden op de vm herstelt, u alleen herstellen naar een compatibel besturingssysteem (niet een eerder of later besturingssysteem). We herstellen de Azure Marketplace VM's die worden ondersteund als VM's niet, omdat deze aankoopgegevens nodig hebben, maar alleen als schijven.
Back-ups maken van VM's die zijn geïmplementeerd vanuit een aangepaste afbeelding (derden) |Ondersteund.<br/><br/> De VM moet een ondersteund besturingssysteem hebben.<br/><br/> Wanneer u bestanden op de vm herstelt, u alleen herstellen naar een compatibel besturingssysteem (niet een eerder of later besturingssysteem).
Back-ups maken van VM's die zijn gemigreerd naar Azure| Ondersteund.<br/><br/> Als u een back-up van de VM wilt maken, moet de VM-agent op de gemigreerde machine zijn geïnstalleerd.
Back-ups maken van multi-VM-consistentie | Azure Backup biedt geen consistentie in gegevens en toepassingen voor meerdere VM's.
Back-up met [diagnostische instellingen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Unsupported. <br/><br/> Als het herstel van de Azure VM met diagnostische instellingen wordt geactiveerd met de optie [Nieuw maken,](backup-azure-arm-restore-vms.md#create-a-vm) mislukt het herstel.
Vm's met zonevastgemaakt | Ondersteund (voor VM die na januari 2019 een back-up maakt en waar [beschikbaarheidszone](https://azure.microsoft.com/global-infrastructure/availability-zones/) beschikbaar is).<br/><br/>We ondersteunen momenteel het herstellen naar dezelfde zone die is vastgemaakt in VM's. Als de zone echter niet beschikbaar is, mislukt het herstellen.
Gen2 VM's | Ondersteund <br> Azure Backup ondersteunt back-up en herstel van [Gen2 VM's.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) Wanneer deze VM's zijn hersteld vanaf herstelpunt, worden ze hersteld als [Gen2 VM's.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)

## <a name="vm-storage-support"></a>Ondersteuning voor VM-opslag

**Component** | **Ondersteuning**
--- | ---
Azure VM-gegevensschijven | Maak een back-up van een vm met 16 of minder gegevensschijven.<BR> Als u zich wilt aanmelden voor de persoonlijke preview-versie van VM's met meer dan 16 schijven (maximaal 32 schijven), stuurt u een bericht naar AskAzureBackupTeam@microsoft.com
Gegevensschijfgrootte | De individuele schijfgrootte kan maximaal 32 TB en maximaal 256 TB zijn gecombineerd voor alle schijven in een VM.
Opslagtype | Standaard HDD, Standaard SSD, Premium SSD.
Managed Disks | Ondersteund.
Versleutelde schijven | Ondersteund.<br/><br/> Er kan een back-up worden gemaakt van Azure VM's met Azure Disk Encryption (met of zonder de Azure AD-app).<br/><br/> Versleutelde VM's kunnen niet worden hersteld op bestands-/mapniveau. U moet de hele VM herstellen.<br/><br/> U versleuteling inschakelen op VM's die al zijn beveiligd met Azure Backup.
Schijven met Schrijfversneller ingeschakeld | Wordt niet ondersteund.<br/><br/> Azure-back-up sluit automatisch de schijven uit met Write Accelerator ingeschakeld tijdens back-up. Aangezien er geen back-up van deze schijven zijn, u deze schijven niet herstellen vanuit herstelpunten van de VM.
Back-ups maken & gededuplicated VM's/schijven herstellen | Azure Backup biedt geen ondersteuning voor ontdubbeling. Zie dit [artikel](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) voor meer informatie <br/> <br/>  - Azure Backup dedliceert niet over VM's in de kluis Recovery Services <br/> <br/>  - Als er VM's in deduplicatiestatus zijn tijdens het herstellen, kunnen de bestanden niet worden hersteld omdat de indeling niet wordt weergegeven. U echter wel het volledige VM-herstel uitvoeren.
Schijf toevoegen aan beveiligde VM | Ondersteund.
Formaat schijf wijzigen op beveiligde vm | Ondersteund.
Gedeelde opslag| Back-ups maken van VM's met CSV (Cluster Shared Volume) of Scale-Out File Server wordt niet ondersteund. CSV-schrijvers zullen waarschijnlijk mislukken tijdens de back-up. Bij herstel komen schijven met CSV-volumes mogelijk niet omhoog.
[Gedeelde schijven](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | Wordt niet ondersteund.

## <a name="vm-network-support"></a>Ondersteuning voor VM-netwerken

**Component** | **Ondersteuning**
--- | ---
Aantal netwerkinterfaces (NIC's) | Maximaal het maximum aantal NIC's dat wordt ondersteund voor een specifieke Azure VM-grootte.<br/><br/> NIC's worden gemaakt wanneer de VM wordt gemaakt tijdens het herstelproces.<br/><br/> Het aantal NIC's op de herstelde VM weerspiegelt het aantal NIC's op de VM wanneer u beveiliging hebt ingeschakeld. Het verwijderen van NIC's nadat u beveiliging hebt ingeschakeld, heeft geen invloed op het aantal.
Externe/interne load balancer |Ondersteund. <br/><br/> [Meer informatie](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) over het herstellen van VM's met speciale netwerkinstellingen.
Meerdere gereserveerde IP-adressen |Ondersteund. <br/><br/> [Meer informatie](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) over het herstellen van VM's met speciale netwerkinstellingen.
VM's met meerdere netwerkadapters| Ondersteund. <br/><br/> [Meer informatie](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) over het herstellen van VM's met speciale netwerkinstellingen.
VM's met openbare IP-adressen| Ondersteund.<br/><br/> Koppel een bestaand openbaar IP-adres aan de NIC of maak een adres aan en koppel het aan de NIC nadat restore is uitgevoerd.
Netwerkbeveiligingsgroep (NSG) op NIC/subnet. |Ondersteund.
Statisch IP-adres | Wordt niet ondersteund.<br/><br/> Aan een nieuwe vm die is gemaakt vanaf een herstelpunt, wordt een dynamisch IP-adres toegewezen.<br/><br/> Voor klassieke VM's u geen back-ups maken van een vm met een gereserveerd IP-adres en geen gedefinieerd eindpunt.
Dynamisch IP-adres |Ondersteund.<br/><br/> Als de NIC op de bron-VM dynamische IP-adressering gebruikt, wordt de NIC op de herstelde VM standaard ook gebruikt.
Azure Traffic Manager| Ondersteund.<br/><br/>Als de back-upvm zich in Traffic Manager bevindt, voegt u de herstelde VM handmatig toe aan dezelfde instantie Traffic Manager.
Azure DNS |Ondersteund.
Aangepaste DNS |Ondersteund.
Uitgaande connectiviteit via HTTP-proxy | Ondersteund.<br/><br/> Een geverifieerde proxy wordt niet ondersteund.
Service-eindpunten voor virtueel netwerk| Ondersteund.<br/><br/> Firewall- en instellingen voor virtuele netwerkopslagaccount moeten toegang bieden vanaf alle netwerken.

## <a name="vm-security-and-encryption-support"></a>Ondersteuning voor VM-beveiliging en versleuteling

Azure Backup ondersteunt versleuteling voor in-transit- en inactieve gegevens:

Netwerkverkeer naar Azure:

- Back-upverkeer van servers naar de kluis Van Recovery Services wordt versleuteld met behulp van Advanced Encryption Standard 256.
- Back-upgegevens worden verzonden via een beveiligde HTTPS-koppeling.
- De back-upgegevens worden versleuteld opgeslagen in de Recovery Services-kluis.
- Alleen u beschikt over de wachtwoordzin waarmee deze gegevens kunnen worden ontgrendeld. De back-upgegevens kunnen niet door Microsoft ontsleuteld.

  > [!WARNING]
  > Nadat u de kluis hebt ingesteld, hebt alleen u toegang tot de versleutelingssleutel. Microsoft bewaart nooit een kopie en heeft geen toegang tot de sleutel. Als de sleutel verkeerd wordt geplaatst, kan Microsoft de back-upgegevens niet herstellen.

Gegevensbeveiliging:

- Wanneer u een back-up maakt van Azure VM's, moet u versleuteling instellen *binnen* de virtuele machine.
- Azure Backup ondersteunt Azure Disk Encryption, dat BitLocker gebruikt op virtuele Windows-machines en ons **dm-crypt** op Linux virtuele machines.
- Op de backend maakt Azure Backup gebruik van [Azure Storage Service-versleuteling](../storage/common/storage-service-encryption.md), waarmee data-at-rest worden beschermd.

**Machine** | **In-transit** | **Inactief**
--- | --- | ---
On-premises Windows-machines zonder DPM/MABS | ![Ja][green] | ![Ja][green]
Azure-VM's | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met DPM | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met MABS | ![Ja][green] | ![Ja][green]

## <a name="vm-compression-support"></a>Ondersteuning voor VM-compressie

Back-up ondersteunt de compressie van back-upverkeer, zoals samengevat in de volgende tabel. Houd rekening met het volgende:

- Voor Azure VM's leest de VM-extensie de gegevens rechtstreeks uit het Azure-opslagaccount via het opslagnetwerk. Het is niet nodig om dit verkeer te comprimeren.
- Als u DPM of MABS gebruikt, u bandbreedte besparen door de gegevens te comprimeren voordat er een back-up van wordt gemaakt naar DPM/MABS.

**Machine** | **Comprimeren naar MABS/DPM (TCP)** | **Comprimeren naar kluis (HTTPS)**
--- | --- | ---
On-premises Windows-machines zonder DPM/MABS | N.v.t. | ![Ja][green]
Azure-VM's | N.v.t. | N.v.t.
On-premises/Azure VM's met DPM | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met MABS | ![Ja][green] | ![Ja][green]

## <a name="next-steps"></a>Volgende stappen

- [Back-ups maken van Azure VM's](backup-azure-arm-vms-prepare.md).
- [Rechtstreeks een back-up maken van Windows-machines](tutorial-backup-windows-server-to-azure.md), zonder een back-upserver.
- [MABS instellen](backup-azure-microsoft-azure-backup.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar MABS maken.
- [DPM instellen](backup-azure-dpm-introduction.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar DPM maken.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
