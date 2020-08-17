---
title: Overzicht van Recovery Services-kluizen
description: Een overzicht en vergelijking tussen Recovery Services kluizen en Azure Backup kluizen.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 2b292a39e38ef5e298f45c2babbee9fbd20c39ea
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261868"
---
# <a name="recovery-services-vaults-overview"></a>Overzicht van Recovery Services-kluizen

In dit artikel worden de functies van een Recovery Services kluis beschreven. Een Recovery Services kluis is een opslag entiteit in azure die gegevens bevat. De gegevens zijn doorgaans kopieën van gegevens, of configuratie-informatie voor virtuele machines (Vm's), werk belastingen, servers of werk stations. U kunt Recovery Services kluizen gebruiken voor het bewaren van back-upgegevens voor verschillende Azure-Services, zoals IaaS Vm's (Linux of Windows) en Azure SQL-data bases. Recovery Services-kluizen ondersteunen System Center DPM, Windows Server, Azure Backup Server en meer. Recovery Services-kluizen maken het eenvoudig om uw back-upgegevens te ordenen, terwijl de beheertaken minimaal zijn. Recovery Services kluizen zijn gebaseerd op het Azure Resource Manager model van Azure, dat functies bevat zoals:

- **Verbeterde mogelijkheden voor het beveiligen van back-upgegevens**: met Recovery Services kluizen biedt Azure backup beveiligings mogelijkheden voor het beveiligen van Cloud back-ups. Met de beveiligings functies kunt u uw back-ups beveiligen en veilig gegevens herstellen, zelfs als er inbreuk is gemaakt op de productie-en back-upservers. [Meer informatie](backup-azure-security-feature.md)

- **Centrale bewaking voor uw hybride IT-omgeving**: met Recovery Services kluizen kunt u niet alleen uw [Azure IaaS-vm's](backup-azure-manage-vms.md) , maar ook uw [on-premises assets](backup-azure-manage-windows-server.md#manage-backup-items) van een centrale Portal bewaken. [Meer informatie](backup-azure-monitoring-built-in-monitor.md)

- **Op rollen gebaseerd Access Control (RBAC)**: RBAC biedt nauw keurig beheer van toegangs beheer in Azure. [Azure biedt verschillende ingebouwde rollen](../role-based-access-control/built-in-roles.md)en Azure backup heeft drie [ingebouwde rollen voor het beheren van herstel punten](backup-rbac-rs-vault.md). Recovery Services kluizen zijn compatibel met RBAC, waarmee back-up-en herstel toegang wordt beperkt tot de gedefinieerde set gebruikers rollen. [Meer informatie](backup-rbac-rs-vault.md)

- **Voorlopig verwijderen**: met zacht verwijderen, zelfs als een schadelijke actor een back-up verwijdert (of de back-upgegevens per ongeluk zijn verwijderd), worden de back-upgegevens 14 extra dagen bewaard, zodat het back-upitem zonder gegevens verlies kan worden hersteld. De resterende 14 dagen voor het bewaren van back-upgegevens in de status ' voorlopig verwijderen ' zijn niet van toepassing op de klant. [Meer informatie](backup-azure-security-feature-cloud.md).

- **Restore voor meerdere regio's**: met cross Region Restore (CRR) kunt u virtuele Azure-machines herstellen in een secundaire regio, een Azure-gekoppelde regio. Als Azure een nood geval in de primaire regio declareert, kunnen de gegevens die in de secundaire regio worden gerepliceerd, in de secundaire regio worden teruggezet om de werkelijke downtime in de primaire regio voor hun omgeving te verminderen. [Meer informatie](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Opslag instellingen in de Recovery Services kluis

Een Recovery Service-kluis is een entiteit waarin de back-ups en herstelpunten worden opgeslagen die in de loop van de tijd zijn gemaakt. De Recovery Service-kluis bevat ook de beleidsregels voor back-up die aan de beveiligde virtuele machines zijn gekoppeld.

- Azure Backup beheert automatisch de opslag voor de kluis. Bekijk hoe [opslag instellingen kunnen worden gewijzigd](./backup-create-rs-vault.md#set-storage-redundancy).

- Zie deze artikelen over [geo](../storage/common/storage-redundancy.md) en [lokale](../storage/common/storage-redundancy.md) redundantie voor meer informatie over opslag redundantie.

### <a name="additional-resources"></a>Aanvullende bronnen

- [Door de kluis ondersteunde en niet-ondersteunde scenario's](backup-support-matrix.md#vault-support)
- [Veelgestelde vragen over de kluis](backup-azure-backup-faq.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) is een gepersonaliseerde Cloud consultant waarmee u het gebruik van Azure kunt optimaliseren. Het analyseert uw Azure-gebruik en biedt tijdig aanbevelingen om uw implementaties te optimaliseren en te beveiligen. Het biedt aanbevelingen in vier categorieën: hoge Beschik baarheid, beveiliging, prestaties en kosten.

Azure Advisor voorziet in elk uur [aanbevelingen](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) voor vm's waarvan geen back-up is gemaakt, zodat u nooit een back-up maakt van belang rijke vm's. U kunt de aanbevelingen ook beheren door ze uit te stellen.  U kunt de aanbeveling selecteren en back-up inschakelen op Vm's in de regel door de kluis op te geven (waarbij back-ups worden opgeslagen) en het back-upbeleid (schema van back-ups en bewaren van back-ups).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende artikelen om:</br>
[Back-up maken van een IaaS-VM](backup-azure-arm-vms-prepare.md)</br>
[Een back-up maken van een Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Back-up maken van een Windows-Server](backup-windows-with-mars-agent.md)
