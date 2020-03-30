---
title: Wat is Azure Backup?
description: Biedt een overzicht van de Azure Backup-service en hoe deze bijdraagt aan uw BCDR-strategie (Business Continuity and Disaster Recovery).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: cf48090b2c32f0c3a1c8170873cb8d6a771fe21f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240294"
---
# <a name="what-is-the-azure-backup-service"></a>Wat is de Azure Backup-service?

De Azure Backup-service biedt eenvoudige, beveiligde en kosteneffectieve oplossingen om een back-up te maken van uw gegevens en die te herstellen vanuit Microsoft Azure-cloud.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>Waarvan kan ik een back-up maken?

- **On-premises** - Back-ups maken van bestanden, mappen en systeemstatus met de [MARS-agent (Microsoft Azure Recovery Services).](backup-support-matrix-mars-agent.md) Of gebruik de MABS-agent (DPM- of Azure Backup Server)-agent om on-premises VM's[(Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) en [VMWare)](backup-azure-backup-server-vmware.md)en andere [on-premises workloads](backup-mabs-protection-matrix.md) te beschermen
- **Azure VM's** - [Maak een back-up van volledige Windows/Linux VM's](backup-azure-vms-introduction.md) (met behulp van back-ups) of maakt een back-up van bestanden, mappen en systeemstatus met behulp van de [MARS-agent.](backup-azure-manage-mars.md)
- **Azure Files deelt** - [Back-ups van Azure File-shares aan een opslagaccount](backup-afs.md)
- **SQL Server in Azure VM's** -  [Back-ups maken van SQL Server-databases die worden uitgevoerd op Azure VM's](backup-azure-sql-database.md)
- **SAP HANA-databases in Azure Vm's** - [Back-up sbackup SAP HANA-databases die draaien op Azure VM's](backup-azure-sap-hana-database.md)

![Overzicht van Azure Backup](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Waarom Azure Backup gebruiken?

Azure Backup biedt deze belangrijke voordelen:

- **On-load on-premises back-up:** Azure Backup biedt een eenvoudige oplossing voor het maken van back-ups van uw on-premises resources naar de cloud. Back-ups op korte en lange termijn zonder de noodzaak om complexe on-premises back-upoplossingen te implementeren.
- **Maak een back-up van Azure IaaS VM's:** Azure Backup biedt onafhankelijke en geïsoleerde back-ups om te voorkomen dat originele gegevens per ongeluk worden vernietigd. Back-ups worden opgeslagen in een vault van Recovery Services met ingebouwd beheer van herstelpunten. Configuratie en schaalbaarheid zijn eenvoudig, back-ups zijn geoptimaliseerd en u eenvoudig herstellen als dat nodig is.
- **Gemakkelijk schalen**: Azure Backup gebruikt de onderliggende kracht en onbeperkte schaal van de Azure-cloud voor hoge beschikbaarheid, zonder overhead voor onderhoud of bewaking.
- **Onbeperkte gegevensoverdracht:** Azure Backup beperkt niet de hoeveelheid binnenkomende of uitgaande gegevens die u overdraagt of brengt kosten in rekening voor de gegevens die worden overgedragen.
  - Uitgaande gegevens zijn gegevens die tijdens een herstelbewerking worden overgebracht uit een Recovery Services-kluis.
  - Als u een offline initiële back-up uitvoert met de Azure Import/Export-service om grote hoeveelheden gegevens te importeren, zijn er kosten verbonden aan binnenkomende gegevens.  [Meer informatie](backup-azure-backup-import-export.md).
- **Houd gegevens veilig:** Azure Backup biedt oplossingen voor het beveiligen van gegevens [tijdens het transport](backup-azure-security-feature.md) en in [rust.](backup-azure-security-feature-cloud.md)
- **Gecentraliseerde bewaking en beheer:** Azure Backup biedt [ingebouwde bewakings- en waarschuwingsmogelijkheden](backup-azure-monitoring-built-in-monitor.md) in een vault van Recovery Services. Deze mogelijkheden zijn beschikbaar zonder extra beheerinfrastructuur. U ook de schaal van uw monitoring en rapportage verhogen [met Azure Monitor.](backup-azure-monitoring-use-azuremonitor.md)
- **App-consistente back-ups**downloaden: een toepassingsconsistente back-up betekent dat een herstelpunt alle benodigde gegevens heeft om de back-upkopie te herstellen. Azure Backup biedt toepassingsconsistente back-ups, die ervoor zorgen dat er geen aanvullende oplossingen nodig zijn om de gegevens te herstellen. Herstellen van toepassingsconsistente gegevens verkort de hersteltijd, zodat u snel weer normaal aan het werk kunt.
- **Gegevens op korte en lange termijn bewaren:** U [vaults](backup-azure-recovery-services-vault-overview.md) van Recovery Services gebruiken voor het bewaren van gegevens op korte en lange termijn.
- **Automatisch opslagbeheer**: voor hybride omgevingen is vaak heterogene opslag vereist, soms on-premises en soms in de cloud. Met Azure Backup zijn er geen kosten verbonden aan het gebruik van on-premises opslagapparaten. De back-upopslag wordt automatisch door Azure Backup toegewezen en beheerd en u betaalt naar gebruik. U betaalt dus alleen voor de opslagruimte die u verbruikt. [Lees meer](https://azure.microsoft.com/pricing/details/backup) over prijzen.
- **Meerdere opslagopties**: Azure Backup biedt twee typen replicatie om uw opslag/gegevens maximaal beschikbaar te houden.
  - Met [lokaal redundante opslag LRS](../storage/common/storage-redundancy-lrs.md) worden uw gegevens drie keer gerepliceerd (er worden drie kopieën gemaakt van uw gegevens) in een opslagschaaleenheid in een datacenter. Alle kopieën van de gegevens komen binnen dezelfde regio voor. LRS is een goedkope optie voor het beschermen van uw gegevens tegen lokale hardwarefouten.
  - [Geografisch redundante opslag (GRS)](../storage/common/storage-redundancy-grs.md) is de standaardinstelling en is de replicatieoptie die wordt aanbevolen. Met GRS worden uw gegevens gerepliceerd naar een secundaire regio (honderden kilometers verwijderd van de primaire locatie van de brongegevens). GRS kost meer dan LRS, maar GRS biedt een hoger niveau van duurzaamheid voor uw gegevens, zelfs als er een regionale storing is.

## <a name="next-steps"></a>Volgende stappen

- [Bekijk](backup-architecture.md) de architectuur en componenten voor verschillende back-upscenario's.
- [Controleer](backup-support-matrix.md) de ondersteuningsvereisten en -beperkingen voor back-ups en voor [Azure VM-back-up.](backup-support-matrix-iaas.md)
