---
title: Antwoorden op veelgestelde vragen
description: 'Antwoorden op veelgestelde vragen over de functies van Azure Backup, waaronder de Recovery Services-kluis, waarvan een back-up kan worden gemaakt, hoe het werkt, versleuteling en limieten. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: f95a4e7e9084c304838b01bdfaceee261d8151cf
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173232"
---
# <a name="azure-backup---frequently-asked-questions"></a>Veelgestelde vragen over Azure Backup

In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Backup-service.

## <a name="recovery-services-vault"></a>Recovery Services-kluis

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Is er een limiet voor het aantal kluizen dat voor elk Azure-abonnement kan worden gemaakt?

Ja. U kunt Maxi maal 500 Recovery Services kluizen maken, per ondersteund gebied van Azure Backup, per abonnement. Als u extra kluizen nodig hebt, maakt u een extra abonnement.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Zijn er beperkingen met betrekking tot het aantal servers/machines dat kan worden geregistreerd voor elke kluis?

U kunt Maxi maal 1000 Azure virtuele machines per kluis registreren. Als u de Microsoft Azure Backup-agent gebruikt, kunt u Maxi maal 50 MAB agents per kluis registreren. En u kunt 50 MAB-servers/DPM-servers registreren bij een kluis.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Hoeveel gegevens bronnen/items kunnen worden beveiligd in een kluis?

U kunt Maxi maal 2000 gegevens bronnen of-items beveiligen voor alle werk belastingen (IaaS VM, SQL, AFS, enz.) in een kluis.
Als u bijvoorbeeld al 500 Vm's en 400 Azure Files shares in de kluis hebt beveiligd, kunt u Maxi maal 1100 SQL-data bases in het bestand beveiligen.

### <a name="how-many-policies-can-i-create-per-vault"></a>Hoeveel beleids regels kan ik per kluis maken?

U kunt Maxi maal 200 beleids regels per kluis hebben.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Als mijn organisatie één kluis heeft, hoe kan ik dan gegevens van verschillende servers in de kluis isoleren bij het herstellen van gegevens?

Server gegevens die u samen wilt herstellen, moeten dezelfde wachtwoordzin gebruiken bij het instellen van de back-up. Als u herstel op een specifieke server of servers wilt isoleren, gebruikt u alleen een wachtwoordzin voor die server of servers. U kunt bijvoorbeeld verschillende wachtwoordzinnen voor de human resource-server, de accountingserver en de opslagserver gebruiken.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Kan ik mijn kluis verplaatsen tussen abonnementen?

Ja. Als u een Recovery Services kluis wilt verplaatsen, raadpleegt u dit [artikel](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>Kan ik back-upgegevens verplaatsen naar een andere kluis?

Nee. Back-upgegevens die zijn opgeslagen in een kluis, kunnen niet worden verplaatst naar een andere kluis.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Kan ik na een back-up overschakelen van GRS naar LRS?

Nee. Een Recovery Services kluis kan alleen opslag opties wijzigen voordat back-ups zijn opgeslagen.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Kan ik een herstel op item niveau (ILR) uitvoeren voor Vm's waarvan een back-up is gemaakt naar een Recovery Services kluis?

- ILR wordt ondersteund voor Azure-Vm's waarvoor een back-up van Azure VM is gemaakt. Zie voor meer informatie [artikel](backup-azure-restore-files-from-vm.md)
- ILR wordt niet ondersteund voor online herstel punten van on-premises Vm's waarvan een back-up is gemaakt door Azure backup server of System Center DPM.

## <a name="azure-backup-agent"></a>Azure Backup-agent

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Waar vind ik Veelgestelde vragen over de Azure Backup-Agent voor Azure VM-back-up?

- Lees deze [Veelgestelde vragen](backup-azure-vm-backup-faq.md)voor de agent die wordt uitgevoerd op virtuele machines van Azure.
- Lees deze [Veelgestelde vragen](backup-azure-file-folder-backup-faq.md)voor de agent die wordt gebruikt om een back-up te maken van Azure-bestands mappen.

## <a name="general-backup"></a>Algemene back-up

### <a name="are-there-limits-on-backup-scheduling"></a>Gelden er limieten voor de back-upplanning?

Ja.

- U kunt Maxi maal drie keer per dag een back-up maken van Windows Server-of Windows-machines. U kunt het plannings beleid instellen op dagelijkse of wekelijkse planningen.
- U kunt Maxi maal twee keer per dag een back-up van DPM maken. U kunt het plannings beleid instellen op dagelijks, wekelijks, maandelijks en jaarlijks.
- Eenmaal per dag maakt u een back-up van Azure-Vm's.

### <a name="what-operating-systems-are-supported-for-backup"></a>Welke besturings systemen worden ondersteund voor back-up?

Azure Backup ondersteunt deze besturings systemen voor het maken van back-ups van bestanden en mappen en voor apps die worden beveiligd door Azure Backup Server en DPM.

**Besturingssysteem** | **SKU** | **Details**
--- | --- | ---
Werk station | |
Windows 10 64-bits | Enterprise, Pro, Home | Op machines moeten de nieuwste service packs en updates worden uitgevoerd.
Windows 8,1 64 bits | Enterprise, Pro | Op machines moeten de nieuwste service packs en updates worden uitgevoerd.
Windows 8 64-bits | Enterprise, Pro | Op machines moeten de nieuwste service packs en updates worden uitgevoerd.
Windows 7 64-bits | Ultimate, Enterprise, Professional, Home Premium Home Basic, Starter | Op machines moeten de nieuwste service packs en updates worden uitgevoerd.
Server | |
Windows Server 2019 64-bits | Standard, Datacenter, Essentials | Met de nieuwste service packs/updates.
Windows Server 2016 64-bits | Standard, Datacenter, Essentials | Met de nieuwste service packs/updates.
Windows Server 2012 R2 64 bits | Standard, Datacenter, Foundation | Met de nieuwste service packs/updates.
Windows Server 2012 64-bits | Datacenter, Foundation, Standard | Met de nieuwste service packs/updates.
Windows Storage Server 2016 64 bits | Standard, Workgroup | Met de nieuwste service packs/updates.
Windows Storage Server 2012 R2 64 bits | Standard, Workgroup, Essential | Met de nieuwste service packs/updates.
Windows Storage Server 2012 64 bits | Standard, Workgroup | Met de nieuwste service packs/updates.
Windows Server 2008 R2 SP1 64 bits | Standard, Enterprise, Datacenter, Foundation | Met de nieuwste updates.
Windows Server 2008 64-bits | Standard, Enter prise, Data Center | Met de nieuwste updates.

Azure Backup biedt geen ondersteuning voor 32-bits besturings systemen.

Voor Azure VM Linux-back-ups ondersteunt Azure Backup [de lijst met distributies die zijn goedgekeurd door Azure](../virtual-machines/linux/endorsed-distros.md), met uitzonde ring van het kern besturingssysteem Linux en 32-bits besturings systeem. Andere uw eigen Linux-distributies kunnen werken zolang de VM-agent op de virtuele machine beschikbaar is, en er is ondersteuning voor python.

### <a name="are-there-size-limits-for-data-backup"></a>Zijn er grootte limieten voor gegevens back-up?

De maximale grootte is als volgt:

OS/machine | Maximale grootte van de gegevens bron
--- | ---
Windows 8 of hoger | 54.400 GB
Windows 7 |1700 GB
Windows Server 2012 of hoger | 54.400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Azure VM | 16 gegevens schijven<br/><br/> Een gegevens schijf van Maxi maal 4095 GB

### <a name="how-is-the-data-source-size-determined"></a>Hoe wordt de grootte van de gegevens bron bepaald?

In de volgende tabel wordt uitgelegd hoe de grootte voor elke gegevensbron wordt bepaald.

**Gegevens bron** | **Details**
--- | ---
Volume |De hoeveelheid gegevens waarvan een back-up wordt gemaakt van een virtuele machine met één volume waarvan een back-up wordt gemaakt.
SQL Server-database |Grootte van de grootte van één SQL database waarvan een back-up wordt gemaakt.
SharePoint | Som van de inhoud en configuratie databases binnen een share point-Farm waarvan een back-up wordt gemaakt.
Exchange |De som van alle Exchange-data bases in een Exchange-server waarvan een back-up wordt gemaakt.
BMR/systeem status |Elk afzonderlijk exemplaar van BMR of systeem status van de computer waarvan een back-up wordt gemaakt.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Geldt er een limiet voor de hoeveelheid gegevens waarvan een back-up is gemaakt met behulp van een Recovery Services kluis?

Er is geen limiet voor de hoeveelheid gegevens waarvan u een back-up kunt maken met behulp van een Recovery Services kluis.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Waarom is de grootte van de gegevens die worden overgebracht naar de Recovery Services kluis kleiner dan de gegevens die voor de back-up zijn geselecteerd?

Gegevens waarvan een back-up is gemaakt van Azure Backup Agent, DPM en Azure Backup Server, worden gecomprimeerd en versleuteld voordat ze worden overgedragen. Wanneer compressie en versleuteling wordt toegepast, zijn de gegevens in de kluis 30-40% kleiner.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Kan ik afzonderlijke bestanden van een herstel punt in de kluis verwijderen?

Nee, Azure Backup biedt geen ondersteuning voor het verwijderen of verwijderen van afzonderlijke items uit opgeslagen back-ups.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Als ik een back-uptaak annuleer nadat deze is gestart, worden de overgebrachte back-upgegevens verwijderd?

Nee. Alle gegevens die zijn overgebracht naar de kluis voordat de back-uptaak werd geannuleerd, blijven in de kluis.

- Azure Backup gebruikt een controlepuntmechanisme waarmee tijdens de back-up van tijd tot tijd controlepunten worden toegevoegd aan de back-upgegevens.
- Omdat de back-upgegevens controlepunten bevatten, kan tijdens het volgende back-upproces de integriteit van de bestanden worden gecontroleerd.
- De volgende back-uptaak is incrementeel ten opzichte van de gegevens waarvan eerder een back-up is gemaakt. Incrementele back-ups dragen alleen nieuwe of gewijzigde gegevens over, zodat de bandbreedte beter wordt benut.

Als u een back-uptaak voor een virtuele Azure-machine annuleert, worden eventuele overgedragen gegevens geannuleerd. De volgende back-uptaak draagt incrementele gegevens over van na de vorige succesvolle back-up-taak.

## <a name="retention-and-recovery"></a>Retentie en herstel

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Zijn het Bewaar beleid voor DPM-en Windows-computers zonder DPM hetzelfde?

Ja, ze hebben allebei een dagelijks, wekelijks, maandelijks en jaarlijks Bewaar beleid.

### <a name="can-i-customize-retention-policies"></a>Kan ik het Bewaar beleid aanpassen?

Ja, u hebt beleids regels aanpassen. U kunt bijvoorbeeld wekelijks en dagelijks Bewaar vereisten configureren, maar niet jaarlijks en maandelijks.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Kan ik verschillende tijdstippen gebruiken voor de back-upplanning en het Bewaar beleid?

Nee. Het bewaarbeleid kan alleen worden toegepast op back-uppunten. In deze afbeelding ziet u bijvoorbeeld een Bewaar beleid voor back-ups die zijn gemaakt op 12am en 18:00 uur.

![Back-up en retentie plannen](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Als er lange tijd een back-up wordt bewaard, duurt het meer tijd om een ouder gegevens punt te herstellen?

Nee. De tijd voor het herstellen van het oudste of het nieuwste punt is hetzelfde. Elk herstelpunt gedraagt zich als een volledig punt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Als elk herstelpunt een volledig punt is, heeft dit dan invloed op de totale factureerbare back-upopslag?

Producten met een lange bewaartermijn slaan de back-upgegevens doorgaans op als volledige punten.

- De volledige punten maken *inefficiënt* gebruik van de opslagruimte, maar kunnen wel gemakkelijker en sneller worden hersteld.
- Incrementele kopieën zijn opslag *efficiënt* , maar u moet een keten van gegevens herstellen die van invloed zijn op uw herstel tijd

De opslagarchitectuur van Azure Backup biedt u het beste van beide werelden door de gegevens optimaal op te slaan voor snelle herstelbewerkingen en lage opslagkosten. Dit zorgt ervoor dat uw ingangs-en uitgangs bandbreedte efficiënt wordt gebruikt. De hoeveelheid gegevens opslag en de tijd die nodig is om de gegevens te herstellen, wordt tot een minimum beperkt. Meer informatie over [incrementele back-ups](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Geldt er een limiet voor het aantal herstelpunten dat kan worden gemaakt?

U kunt maximaal 9999 herstelpunten maken per beveiligd exemplaar. Een beveiligd exemplaar is een computer, een server (fysiek of virtueel) of een werk belasting die back-ups maakt van Azure.

- Meer informatie over [back-up en retentie](./backup-overview.md#backup-and-retention).

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Hoe vaak kan ik gegevens herstellen waarvan een back-up is gemaakt naar Azure?

Er is geen limiet voor het aantal herstelbewerkingen vanuit Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Als ik gegevens ga herstellen, betaal ik dan voor het uitgaande verkeer van Azure?

Nee. Herstel is gratis en er worden geen kosten in rekening gebracht voor het uitgaande verkeer.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Wat gebeurt er wanneer ik mijn back-upbeleid Wijzig?

Wanneer een nieuw beleid wordt toegepast, wordt het schema en de retentie van het nieuwe beleid gevolgd.

- Als de retentie is uitgebreid, worden de bestaande herstelpunten gemarkeerd voor bewaring conform het nieuwe beleid.
- Als de retentie is beperkt, worden de bestaande herstelpunten gemarkeerd voor verwijdering in de eerstvolgende opschoontaak, en worden ze vervolgens verwijderd.

## <a name="encryption"></a>Versleuteling

### <a name="is-the-data-sent-to-azure-encrypted"></a>Worden de gegevens die naar Azure worden verzonden, versleuteld?

Ja. Gegevens worden versleuteld op de on-premises machine met behulp van AES256. De gegevens worden verzonden via een beveiligde HTTPS-koppeling. De gegevens die in de cloud worden verzonden, worden alleen beveiligd met een HTTPS-koppeling tussen de opslag-en de herstel service. het iSCSI-protocol beveiligt de gegevens die worden verzonden tussen de Recovery service en de computer van de gebruiker. Beveiligde tunneling wordt gebruikt om het iSCSI-kanaal te beveiligen.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Worden de back-upgegevens op Azure ook versleuteld?

Ja. De gegevens in azure zijn versleuteld-op-rest.

- Voor on-premises back-ups wordt versleuteling op rest geboden met de wachtwoordzin die u opgeeft wanneer u een back-up maakt naar Azure.
- Voor virtuele Azure-machines worden gegevens versleuteld op rest met behulp van Storage Service Encryption (SSE).

De back-upgegevens worden nooit door Microsoft ontsleuteld.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Wat is de minimale lengte van de versleuteling van de sleutel die wordt gebruikt om back-upgegevens te versleutelen?

Als u gebruikmaakt van een Azure Backup-agent, moet de versleutelingssleutel ten minste 16 tekens bevatten. Voor virtuele Azure-machines is er geen limiet voor de lengte van de sleutels die door Azure KeyVault worden gebruikt.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Wat gebeurt er als ik de versleutelingssleutel kwijtraak? Kan ik de gegevens herstellen? Kan micro soft de gegevens herstellen?

De sleutel voor het versleutelen van de back-upgegevens is alleen beschikbaar op uw site. Microsoft bewaart geen kopie in Azure en heeft geen toegang tot de sleutel. Als u de sleutel op een andere locatie plaatst, kan micro soft de back-upgegevens niet herstellen.

## <a name="next-steps"></a>Volgende stappen

Lees de andere veelgestelde vragen:

- [Veelgestelde vragen](backup-azure-vm-backup-faq.md) over back-ups van Azure-vm's.
- [Veelgestelde vragen](backup-azure-file-folder-backup-faq.md) over de Azure backup-agent
