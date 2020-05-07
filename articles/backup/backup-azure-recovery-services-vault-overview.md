---
title: Overzicht van Recovery Services-kluizen
description: Een overzicht en vergelijking tussen Recovery Services kluizen en Azure Backup kluizen.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: a0dacd82b7cf4258c0147bbaf9dc39ee6fc0fa25
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597944"
---
# <a name="recovery-services-vaults-overview"></a>Overzicht van Recovery Services-kluizen

In dit artikel worden de functies van een Recovery Services kluis beschreven. Een Recovery Services kluis is een opslag entiteit in azure die gegevens bevat. De gegevens zijn doorgaans kopieën van gegevens, of configuratie-informatie voor virtuele machines (Vm's), werk belastingen, servers of werk stations. U kunt Recovery Services kluizen gebruiken voor het bewaren van back-upgegevens voor verschillende Azure-Services, zoals IaaS Vm's (Linux of Windows) en Azure SQL-data bases. Recovery Services-kluizen ondersteunen System Center DPM, Windows Server, Azure Backup Server en meer. Recovery Services-kluizen maken het eenvoudig om uw back-upgegevens te ordenen, terwijl de beheertaken minimaal zijn.

Binnen een Azure-abonnement kunt u Maxi maal 500 Recovery Services kluizen per abonnement per regio maken.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Recovery Services kluizen en back-upkluizen vergelijken

Als u nog steeds back-upkluizen hebt, worden deze automatisch bijgewerkt naar Recovery Services-kluizen. Vanaf november 2017 zijn alle back-upkluizen bijgewerkt naar Recovery Services kluizen.

Recovery Services kluizen zijn gebaseerd op het Azure Resource Manager model van Azure, terwijl back-upkluizen zijn gebaseerd op het Azure Service Manager model. Wanneer u een back-upkluis bijwerkt naar een Recovery Services kluis, blijven de back-upgegevens intact tijdens en na het upgrade proces. Recovery Services kluizen bieden functies die niet beschikbaar zijn voor back-upkluizen, zoals:

- **Verbeterde mogelijkheden voor het beveiligen van back-upgegevens**: met Recovery Services kluizen biedt Azure backup beveiligings mogelijkheden voor het beveiligen van Cloud back-ups. Met de beveiligings functies kunt u uw back-ups beveiligen en veilig gegevens herstellen, zelfs als er inbreuk is gemaakt op de productie-en back-upservers. [Meer informatie](backup-azure-security-feature.md)

- **Centrale bewaking voor uw hybride IT-omgeving**: met Recovery Services kluizen kunt u niet alleen uw [Azure IaaS-vm's](backup-azure-manage-vms.md) , maar ook uw [on-premises assets](backup-azure-manage-windows-server.md#manage-backup-items) van een centrale Portal bewaken. [Meer informatie](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Op rollen gebaseerd Access Control (RBAC)**: RBAC biedt nauw keurig beheer van toegangs beheer in Azure. [Azure biedt verschillende ingebouwde rollen](../role-based-access-control/built-in-roles.md)en Azure backup heeft drie [ingebouwde rollen voor het beheren van herstel punten](backup-rbac-rs-vault.md). Recovery Services kluizen zijn compatibel met RBAC, waarmee back-up-en herstel toegang wordt beperkt tot de gedefinieerde set gebruikers rollen. [Meer informatie](backup-rbac-rs-vault.md)

- **Alle configuraties van Azure virtual machines beveiligen**: Recovery Services kluizen beveiligen vm's op basis van Resource Manager, zoals Premium-schijven, Managed disks en versleutelde vm's. Als u een back-upkluis bijwerkt naar een Recovery Services kluis, hebt u de mogelijkheid om uw op Service Manager gebaseerde Vm's te upgraden naar op Resource Manager gebaseerde Vm's. Tijdens de upgrade van de kluis kunt u uw op Service Manager gebaseerde VM-herstel punten behouden en de beveiliging configureren voor de bijgewerkte virtuele machines (Resource Manager-ingeschakeld). [Meer informatie](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Direct herstellen voor IaaS-vm's**: met Recovery Services kluizen kunt u bestanden en mappen van een IaaS-VM herstellen zonder de volledige VM te herstellen, waardoor de herstel tijden sneller zijn. Direct terugzetten voor IaaS Vm's is beschikbaar voor virtuele Windows-en Linux-machines. [Meer informatie](backup-instant-restore-capability.md)

## <a name="storage-settings-in-the-recovery-services-vault"></a>Opslag instellingen in de Recovery Services kluis

Een Recovery Services kluis is een entiteit waarin de back-ups en herstel punten worden opgeslagen die in de loop van de tijd zijn gemaakt. De Recovery Services kluis bevat ook het back-upbeleid dat is gekoppeld aan de beveiligde virtuele machines.

Azure Backup beheert automatisch de opslag voor de kluis. Bekijk hoe [opslag instellingen kunnen worden gewijzigd](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy).

Zie deze artikelen over [geo](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) en [lokale](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) redundantie voor meer informatie over opslag redundantie.

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Uw Recovery Services-kluizen beheren in de portal

Het maken en beheren van Recovery Services kluizen in de Azure Portal is eenvoudig, omdat de back-upservice in andere Azure-Services kan worden geïntegreerd. Deze integratie betekent dat u een Recovery Services kluis kunt maken of beheren *in de context van de doel service*. Als u bijvoorbeeld de herstel punten voor een virtuele machine wilt weer geven, selecteert u uw virtuele machine en klikt u op **back-up** in het menu bewerkingen.

![Recovery Services kluis Details VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Als er geen back-up is geconfigureerd op de VM, wordt u gevraagd om een back-up te configureren. Als er een back-up is geconfigureerd, ziet u back-upgegevens over de virtuele machine, met inbegrip van een lijst met herstel punten.  

![Recovery Services-kluis Details VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

In het vorige voor beeld is **ContosoVM** de naam van de virtuele machine. **ContosoVM-demovault** is de naam van de Recovery Services kluis. U hoeft niet de naam te onthouden van de Recovery Services kluis waarin de herstel punten worden opgeslagen. u hebt dan toegang tot deze gegevens vanaf de virtuele machine.  

Als een Recovery Services kluis meerdere servers beveiligt, kan het meer logisch zijn om te kijken naar de Recovery Services kluis. U kunt zoeken naar alle Recovery Services kluizen in het abonnement en er een kiezen in de lijst.

De volgende secties bevatten koppelingen naar artikelen met uitleg over het gebruik van een Recovery Services kluis in elk type activiteit.

> [!NOTE]
> Recovery Services kluis kan niet worden gemaakt met dezelfde naam als deze binnen 24 uur is verwijderd. Gebruik een andere resource naam of kies een andere resource groep of probeer het na 24 uur opnieuw.

### <a name="back-up-data"></a>Een back-up maken van gegevens

- [Een back-up maken van een Azure VM](backup-azure-vms-first-look-arm.md)
- [Back-up maken van een Windows-Server of Windows-werk station](backup-try-azure-backup-in-10-mins.md)
- [Back-ups maken van DPM-workloads naar Azure](backup-azure-dpm-introduction.md)
- [Maak een back-up van werk belastingen met behulp van Azure Backup Server](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Herstel punten beheren

- [Back-ups van Azure-VM's beheren](backup-azure-manage-vms.md)
- [Bestanden en mappen beheren](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Gegevens uit de kluis herstellen

- [Afzonderlijke bestanden van een Azure-VM herstellen](backup-azure-restore-files-from-vm.md)
- [Een Azure-VM herstellen](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>De kluis beveiligen

- [Back-upgegevens voor de cloud in Recovery Services kluizen beveiligen](backup-azure-security-feature.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](https://docs.microsoft.com/azure/advisor/) is een gepersonaliseerde Cloud consultant waarmee u het gebruik van Azure kunt optimaliseren. Het analyseert uw Azure-gebruik en biedt tijdig aanbevelingen om uw implementaties te optimaliseren en te beveiligen. Het biedt aanbevelingen in vier categorieën: hoge Beschik baarheid, beveiliging, prestaties en kosten.

Azure Advisor voorziet in elk uur [aanbevelingen](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations#protect-your-virtual-machine-data-from-accidental-deletion) voor vm's waarvan geen back-up is gemaakt, zodat u nooit een back-up maakt van belang rijke vm's. U kunt de aanbevelingen ook beheren door ze uit te stellen.  U kunt op de aanbeveling klikken en back-up inschakelen op Vm's in de regel door de kluis op te geven (waarbij back-ups worden opgeslagen) en het back-upbeleid (schema van back-ups en bewaren van back-ups).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende artikelen om:</br>
[Back-up maken van een IaaS-VM](backup-azure-arm-vms-prepare.md)</br>
[Een back-up maken van een Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Back-up maken van een Windows-Server](backup-windows-with-mars-agent.md)
