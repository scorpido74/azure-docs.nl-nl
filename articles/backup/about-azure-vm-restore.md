---
title: Over het herstel proces van de virtuele Azure-machine
description: Meer informatie over hoe de Azure Backup-service virtuele Azure-machines herstelt
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: b9a2636a2144ea40457bdc3d88786785cb012e0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84325822"
---
# <a name="about-azure-vm-restore"></a>Over Azure-VM herstellen

In dit artikel wordt beschreven hoe de [Azure backup-service](https://docs.microsoft.com/azure/backup/backup-overview) virtuele machines (vm's) van Azure herstelt. Er zijn een aantal opties voor terugzetten. We bespreken de verschillende scenario's die ze ondersteunen.

## <a name="concepts"></a>Concepten

- **Herstel punt** (ook bekend als **herstel punt**): een herstel punt is een kopie van de oorspronkelijke gegevens waarvan een back-up wordt gemaakt.

- **Laag (moment opname versus kluis)**: Azure VM-back-up vindt plaats in twee fasen:

  - In fase 1 wordt de moment opname die is gemaakt samen met de schijf opgeslagen. Dit wordt een **moment opname-laag**genoemd. Het terugzetten van de moment opname-laag is sneller (dan het herstellen van de kluis), omdat deze de wacht tijd voor moment opnamen voor het kopiëren naar de kluis elimineren voordat de herstel bewerking wordt geactiveerd. Het terugzetten vanuit de momentopname laag wordt dus ook wel [direct terugzetten](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability)genoemd.
  - In fase 2 wordt de moment opname overgebracht en opgeslagen in de kluis die wordt beheerd door de Azure Backup-service. Dit wordt ook wel de **kluis-laag**genoemd.

- **Herstel van de oorspronkelijke locatie (herstellen)**: een herstel bewerking die vanaf het herstel punt is uitgevoerd naar de bron-Azure-VM vanaf waar de back-ups zijn gemaakt, vervangen door de status die is opgeslagen in het herstel punt. Hiermee vervangt u de besturingssysteem schijf en de gegevens schijf (s) van de bron-VM.

- **Alternatief-locatie herstel (ALR)**: een herstel bewerking die vanaf het herstel punt is uitgevoerd naar een andere server dan de oorspronkelijke server waarop de back-ups zijn gemaakt.

- **Herstel op item niveau (ILR):** Herstellen van afzonderlijke bestanden of mappen in de VM vanaf het herstel punt

- **Beschik baarheid (replicatie typen)**: Azure Backup biedt twee typen replicatie om uw opslag/gegevens Maxi maal beschikbaar te stellen:
  - Met [lokaal redundante opslag LRS](../storage/common/storage-redundancy-lrs.md) worden uw gegevens drie keer gerepliceerd (er worden drie kopieën gemaakt van uw gegevens) in een opslagschaaleenheid in een datacenter. Alle kopieën van de gegevens komen binnen dezelfde regio voor. LRS is een goedkope optie voor het beschermen van uw gegevens tegen lokale hardwarefouten.
  - [Geografisch redundante opslag (GRS)](../storage/common/storage-redundancy-grs.md) is de standaardinstelling en is de replicatieoptie die wordt aanbevolen. Met GRS worden uw gegevens gerepliceerd naar een secundaire regio (honderden kilometers verwijderd van de primaire locatie van de brongegevens). GRS is duurder dan LRS, maar biedt een hoger duurzaamheidsniveau voor uw gegevens, zelfs in geval van een regionale onderbreking.

- **Cross-Region Restore (CRR)**: als u een van de [Opties](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)voor het terugzetten van meerdere regio's (CRR) hebt, kunt u virtuele Azure-machines herstellen in een secundaire regio, een [Azure-gekoppelde regio](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).

## <a name="restore-scenarios"></a>Scenario's herstellen

![Scenario's herstellen ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Scenario**                                                 | **Wat is er gebeurd**                                             | **Wanneer gebruikt u dit?**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Herstellen om een nieuwe virtuele machine te maken](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms) | Hiermee wordt de volledige VM teruggezet naar herstellen (als de bron-VM nog bestaat) of ALR | <li> Als de bron-VM verloren is gegaan of is beschadigd, kunt u de volledige VM herstellen  <li> U kunt een kopie van de virtuele machine maken  <li> U kunt een herstel analyse uitvoeren voor controle of naleving  <li> Deze optie werkt niet voor Azure-Vm's die zijn gemaakt op basis van Marketplace-installatie kopieën (dat wil zeggen, als deze niet beschikbaar zijn omdat de licentie is verlopen). |
| [Schijven van de virtuele machine terugzetten](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) | Schijven die zijn gekoppeld aan de VM herstellen                             |  Alle schijven: met deze optie wordt de sjabloon gemaakt en wordt de schijf hersteld. U kunt deze sjabloon bewerken met speciale configuraties (bijvoorbeeld beschikbaarheids sets) om te voldoen aan uw vereisten en vervolgens de sjabloon gebruiken en de schijf herstellen om de virtuele machine opnieuw te maken. |
| [Specifieke bestanden binnen de VM herstellen](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) | Kies herstel punt, blader, Selecteer bestanden en zet ze terug in hetzelfde (of compatibel) besturings systeem als de back-up van de virtuele machine. |  Als u weet welke specifieke bestanden u moet herstellen, gebruikt u deze optie in plaats van de volledige virtuele machine te herstellen. |
| [Een versleutelde VM herstellen](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) | Herstel de schijven vanuit de portal en gebruik Power shell om de virtuele machine te maken | <li> [Versleutelde VM met Azure Active Directory (AAD)](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows-aad)  <li> [Versleutelde VM zonder AAD](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows) <li> [Versleutelde virtuele machine *met Aad* gemigreerd naar *zonder Aad*](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-faq#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [Meerdere regio's herstellen](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) | Een nieuwe virtuele machine maken of schijven terugzetten naar een secundaire regio (Azure gekoppelde regio) | <li> **Volledige onderbreking**: met de functie voor het terugzetten van meerdere regio's is er geen wacht tijd meer om gegevens in de secundaire regio te herstellen. U kunt herstel bewerkingen in de secundaire regio initiëren, zelfs voordat Azure een onderbreking declareert. <li> **Gedeeltelijke storing**: uitval tijd kan optreden in specifieke opslag clusters waar Azure Backup uw back-upgegevens opslaat of zelfs in het netwerk, Azure backup en opslag clusters verbindt die zijn gekoppeld aan uw back-upgegevens. Met het herstel van meerdere regio's kunt u een herstel bewerking uitvoeren in de secundaire regio met behulp van een replica van back-ups van gegevens in de secundaire regio. <li> **Geen storing**: u kunt bedrijfs continuïteit en herstel na nood gevallen (BCDR) uitvoeren voor controle-of nalevings doeleinden met de gegevens van de secundaire regio. Hierdoor kunt u een back-up van gegevens in de secundaire regio herstellen, zelfs als er geen volledige of gedeeltelijke onderbreking in de primaire regio is voor bedrijfs continuïteit en herstel na nood gevallen.  |

------





## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen over het terugzetten van de VM](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#restore)
- [Ondersteunde herstel methoden](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#supported-restore-methods)
- [Problemen met herstellen oplossen](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#restore)
