---
title: Selecteer een optie voor VMware-migratie met Azure Migrate server migratie | Microsoft Docs
description: Biedt een overzicht van de opties voor het migreren van virtuele VMware-machines naar Azure met Azure Migrate server migratie
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 196b80f600d9895e9fcb6471bfb5df5fb5bbe8d1
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324501"
---
# <a name="select-a-vmware-migration-option"></a>Selecteer een VMware-migratie optie

U kunt virtuele VMware-machines migreren naar Azure met behulp van het hulp programma voor migratie van Azure Migrate-server. Dit hulp programma biedt een aantal opties voor de migratie van virtuele VMware-machines:

- Migratie met replicatie zonder agent. Migreer Vm's zonder dat u iets hoeft te installeren.
- Migratie met een agent voor replicatie. Installeer een agent op de virtuele machine voor replicatie.




## <a name="compare-migration-methods"></a>Migratie methoden vergelijken

Gebruik deze geselecteerde vergelijkingen om u te helpen beslissen welke methode u wilt gebruiken. U kunt ook volledige ondersteunings vereisten voor niet- [gemachtigde](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) en [op agents gebaseerde](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) migraties bekijken.

**Instelling** | **Zonder agent** | **Op basis van een agent**
--- | --- | ---
**Azure-machtigingen** | U hebt machtigingen nodig voor het maken van een Azure Migrate project en voor het registreren van Azure AD-apps die zijn gemaakt bij het implementeren van het Azure Migrate apparaat. | U hebt Inzender machtigingen nodig voor het Azure-abonnement. 
**Gelijktijdige replicatie** | Er kunnen Maxi maal 100 Vm's gelijktijdig worden gerepliceerd vanuit een vCenter Server.<br/> Als u meer dan 50 Vm's voor migratie hebt, maakt u meerdere batches met virtuele machines.<br/> Het repliceren van meer dan één keer is van invloed op de prestaties. | NA
**Implementatie van het apparaat** | Het [Azure migrate apparaat](migrate-appliance.md) wordt on-premises geïmplementeerd. | Het [Azure migrate replicatie apparaat](migrate-replication-appliance.md) wordt on-premises geïmplementeerd.
**Site Recovery compatibiliteit** | Browsercompatibele. | U kunt niet repliceren met Azure Migrate server migratie als u replicatie voor een machine hebt ingesteld met behulp van Site Recovery.
**Doel schijf** | Managed Disks | Managed Disks
**Schijf limieten** | BESTURINGSSYSTEEM schijf: 2 TB<br/><br/> Gegevens schijf: 4 TB<br/><br/> Maximum aantal schijven: 60 | BESTURINGSSYSTEEM schijf: 2 TB<br/><br/> Gegevens schijf: 8 TB<br/><br/> Maximum aantal schijven: 63
**Passthrough-schijven** | Niet ondersteund | Ondersteund
**UEFI-opstart** | Niet ondersteund | De gemigreerde VM in azure wordt automatisch geconverteerd naar een BIOS-opstart-VM.<br/><br/> De besturingssysteem schijf moet Maxi maal vier partities hebben en volumes moeten worden geformatteerd met NTFS.


## <a name="deployment-steps-comparison"></a>Vergelijking van implementatie stappen

Nadat u de beperkingen hebt bekeken, kunt u aan de hand van de stappen in de implementatie van elke oplossing bepalen welke optie u moet kiezen.

**Taak** | **Details** |**Zonder agent** | **Op basis van een agent**
--- | --- | --- | ---
**Onderzoek** | Evalueer servers vóór de migratie.  De evaluatie is optioneel. U wordt aangeraden machines te beoordelen voordat u ze migreert, maar u hoeft dit niet te doen. <br/><br/> Voor beoordeling wordt Azure Migrate een licht gewicht in te stellen voor het detecteren en beoordelen van Vm's. | Als u na de evaluatie een agentloze migratie uitvoert, wordt hetzelfde Azure Migrate apparaat dat is ingesteld voor evaluatie, gebruikt voor migratie zonder agent.  |  Als u na de evaluatie een migratie op basis van een agent uitvoert, wordt het apparaat dat is ingesteld voor evaluatie niet gebruikt tijdens migratie op basis van een agent. U kunt het apparaat laten staan of het verwijderen als u geen verdere detectie en evaluatie wilt uitvoeren.
**VMware-servers en virtuele machines voorbereiden voor migratie** | Configureer een aantal instellingen op VMware-servers en virtuele machines. | Vereist | Vereist
**Het hulp programma voor server migratie toevoegen** | Voeg het hulp programma voor migratie van Azure Migrate-server toe in het Azure Migrate-project. | Vereist | Vereist
**Het Azure Migrate apparaat implementeren** | Stel een licht gewicht in op een virtuele VMware-machine voor detectie en evaluatie van VM'S. | Vereist | Niet vereist.
**De Mobility-service installeren op Vm's** | De Mobility-service installeren op elke virtuele machine die u wilt repliceren | Niet vereist | Vereist
**Het replicatie apparaat voor migratie van Azure Migrate server implementeren** | Stel een apparaat in op een virtuele VMware-machine voor het detecteren van Vm's en de brug tussen de Mobility-service die wordt uitgevoerd op Vm's en Azure Migrate server migratie | Niet vereist | Vereist
**Virtuele machines repliceren**. Schakel VM-replicatie in. | Configureer de replicatie-instellingen en selecteer de Vm's die u wilt repliceren | Vereist | Vereist
**Een testmigratie uitvoeren** | Voer een test migratie uit om te controleren of alles werkt zoals verwacht. | Vereist | Vereist
**Een volledige migratie uitvoeren** | Migreer de Vm's. | Vereist | Vereist




## <a name="next-steps"></a>Volgende stappen

[Migreer VMware-vm's](tutorial-migrate-vmware.md) met agentloze migratie.



