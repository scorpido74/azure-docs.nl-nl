---
title: Overzicht van Recovery Services-kluizen
description: Een overzicht en vergelijking tussen Vaults van Recovery Services en Azure Backup-kluizen.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: e6a359287533c9ffdd688b5285b24b9c70fa7b7f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436967"
---
# <a name="recovery-services-vaults-overview"></a>Overzicht van Recovery Services-kluizen

In dit artikel worden de functies van een kluis van Recovery Services beschreven. Een vault van Recovery Services is een opslagentiteit in Azure die gegevens bevat. De gegevens zijn meestal kopieën van gegevens of configuratiegegevens voor virtuele machines (VM's), workloads, servers of werkstations. U Vaults van Recovery Services gebruiken om back-upgegevens vast te houden voor verschillende Azure-services, zoals IaaS VM's (Linux of Windows) en Azure SQL-databases. Vaults van Recovery Services ondersteunen System Center DPM, Windows Server, Azure Backup Server en meer. Recovery Services-kluizen maken het eenvoudig om uw back-upgegevens te ordenen, terwijl de beheertaken minimaal zijn.

Binnen een Azure-abonnement u maximaal 500 Vaults voor Herstelservices per abonnement per regio maken.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Vaults en Back-upkluizen van Recovery Services vergelijken

Als u nog steeds back-upkluizen hebt, worden ze automatisch geüpgraded naar vaults van Recovery Services. In november 2017 zijn alle Vaults voor Back-up geüpgraded naar Vaults van Recovery Services.

Vaults van Recovery Services zijn gebaseerd op het Azure Resource Manager-model van Azure, terwijl back-upkluizen zijn gebaseerd op het Azure Service Manager-model. Wanneer u een vault voor back-upupgradenaar een vault van Recovery Services upgradet, blijven de back-upgegevens intact tijdens en na het upgradeproces. Vaults van Recovery Services bieden functies die niet beschikbaar zijn voor back-upkluizen, zoals:

- **Verbeterde mogelijkheden om back-upgegevens te beveiligen:** met Vaults van Recovery Services biedt Azure Backup beveiligingsmogelijkheden om cloudback-ups te beveiligen. De beveiligingsfuncties zorgen ervoor dat u uw back-ups beveiligen en gegevens veilig herstellen, zelfs als productie- en back-upservers worden aangetast. [Meer informatie](backup-azure-security-feature.md)

- **Centrale bewaking voor uw hybride IT-omgeving:** met Vaults van Recovery Services u niet alleen uw [Azure IaaS VM's](backup-azure-manage-vms.md) controleren, maar ook uw [on-premises assets](backup-azure-manage-windows-server.md#manage-backup-items) vanuit een centrale portal. [Meer informatie](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **RBAC (Role-Based Access Control):** RBAC biedt fijnmazige toegangscontrole in Azure. [Azure biedt verschillende ingebouwde rollen](../role-based-access-control/built-in-roles.md)en Azure Backup heeft drie [ingebouwde rollen om herstelpunten te beheren.](backup-rbac-rs-vault.md) Vaults van Recovery Services zijn compatibel met RBAC, dat de back-up beperkt en de toegang tot de gedefinieerde set gebruikersrollen herstelt. [Meer informatie](backup-rbac-rs-vault.md)

- **Bescherm alle configuraties van Azure Virtual Machines:** Vaults van Recovery Services beschermen Vm's op basis van Resource Manager, waaronder Premium-schijven, beheerde schijven en versleutelde VM's. Als u een vault voor back-ups upgradet naar een vault voor Herstelservices, u uw VM's op basis van Service Manager upgraden naar vm's op basis van Resource Manager. Tijdens het upgraden van de kluis u uw VM-herstelpunten op basis van Service Manager behouden en de beveiliging configureren voor de bijgewerkte VM's (Resource Manager). [Meer informatie](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Direct herstellen voor IaaS VM's:** met Vaults van Recovery Services u bestanden en mappen van een IaaS-vm herstellen zonder de hele VM te herstellen, waardoor snellere hersteltijden mogelijk zijn. Instant restore voor IaaS VM's is beschikbaar voor zowel Windows- als Linux-VM's. [Meer informatie](backup-instant-restore-capability.md)

## <a name="storage-settings-in-the-recovery-services-vault"></a>Opslaginstellingen in de kluis Recovery Services

Een vault van Recovery Services is een entiteit die de back-ups en herstelpunten opslaat die in de loop van de tijd zijn gemaakt. De kluis Recovery Services bevat ook het back-upbeleid dat is gekoppeld aan de beveiligde virtuele machines.

Azure Backup verwerkt automatisch de opslag voor de kluis. Bekijk hoe [opslaginstellingen kunnen worden gewijzigd](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy).

Zie deze artikelen over [geo-](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) en [lokale](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) redundantie voor meer informatie over redundantie voor opslag.

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Uw Vaults van Recovery Services beheren in de portal

Het maken en beheren van Vaults van Recovery Services in de Azure-portal is eenvoudig omdat de Back-upservice is geïntegreerd in andere Azure-services. Deze integratie betekent dat u een Vault voor Recovery Services maken of beheren *in het kader van de doelservice.* Als u bijvoorbeeld de herstelpunten voor een virtuele machine wilt weergeven, selecteert u uw VM en klikt u op **Back-up** in het menu Bewerkingen.

![Recovery Services vault details VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Als de VM geen back-up heeft geconfigureerd, wordt u gevraagd een back-up te configureren. Als de back-up is geconfigureerd, ziet u back-upgegevens over de VM, inclusief een lijst met herstelpunten.  

![Vault details VM voor herstelservices](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

In het vorige voorbeeld is **ContosoVM** de naam van de virtuele machine. **ContosoVM-demovault** is de naam van de Vault Recovery Services. U hoeft de naam van de kluis Recovery Services die de herstelpunten opslaat niet te onthouden, u hebt toegang tot deze informatie vanaf de virtuele machine.  

Als één vault van Recovery Services meerdere servers beschermt, is het mogelijk logischer om naar de kluis Van Recovery Services te kijken. U zoeken naar alle Vaults van Recovery Services in het abonnement en er een kiezen uit de lijst.

In de volgende secties vindt u koppelingen naar artikelen waarin wordt uitgelegd hoe u een kluis van Recovery Services gebruikt in elk type activiteit.

> [!NOTE]
> De kluis Recovery Services kan niet met dezelfde naam worden gemaakt als deze binnen 24 uur is verwijderd. Gebruik een andere resourcenaam of kies een andere resourcegroep of probeer het na 24 uur opnieuw.

### <a name="back-up-data"></a>Back-ups maken van gegevens

- [Een back-up maken van een Azure VM](backup-azure-vms-first-look-arm.md)
- [Een back-up maken van Windows Server of Windows-workstation](backup-try-azure-backup-in-10-mins.md)
- [Back-ups maken van DPM-workloads naar Azure](backup-azure-dpm-introduction.md)
- [Voorbereiden op een back-up van workloads met Azure Backup Server](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Herstelpunten beheren

- [Back-ups van Azure-VM's beheren](backup-azure-manage-vms.md)
- [Bestanden en mappen beheren](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Gegevens uit de kluis herstellen

- [Afzonderlijke bestanden herstellen vanaf een Azure-vm](backup-azure-restore-files-from-vm.md)
- [Een Azure-vm herstellen](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Beveilig de kluis

- [Cloudback-upgegevens beveiligen in kluizen van Recovery Services](backup-azure-security-feature.md)

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende artikelen om:</br>
[Een back-up maken van een IaaS-vm](backup-azure-arm-vms-prepare.md)</br>
[Een back-up maken van een Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Een back-up maken van een Windows-server](backup-windows-with-mars-agent.md)
