---
title: Selecteer een VMware-migratieoptie met Migratie van Azure-migratieserver | Microsoft Documenten
description: Biedt een overzicht van opties voor het migreren van VMware VM's naar Azure met Azure Migrate Server Migration
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028711"
---
# <a name="select-a-vmware-migration-option"></a>Een VMware-migratieoptie selecteren

U VMware VM's migreren naar Azure met het hulpprogramma Azure Migrate Server Migration. Deze tool biedt een aantal opties voor VMware VM-migratie:

- Migratie met behulp van agentless replicatie. Migreer VM's zonder er iets op te hoeven installeren.
- Migratie met een agent voor replicatie. Installeer een agent op de VM voor replicatie.




## <a name="compare-migration-methods"></a>Migratiemethoden vergelijken

Gebruik deze geselecteerde vergelijkingen om u te helpen beslissen welke methode u wilt gebruiken. U ook de volledige ondersteuningsvereisten voor [agentloze](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) en [op agentgebaseerde](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) migratie bekijken.

**Instelling** | **Zonder agent** | **Agent-based**
--- | --- | ---
**Azure-machtigingen** | U hebt machtigingen nodig om een Azure Migrate-project te maken en Azure AD-apps te registreren die zijn gemaakt wanneer u het Azure Migrate-toestel implementeert. | U hebt inzendermachtigingen nodig voor het Azure-abonnement. 
**Gelijktijdige replicatie** | Een maximum van 100 VM's kan tegelijkertijd worden gerepliceerd vanaf een vCenter-server.<br/> Als u meer dan 50 VM's voor migratie hebt, maakt u meerdere batches vm's.<br/> Het repliceren van meer in één keer heeft invloed op de prestaties. | N.v.t.
**Toestelimplementatie** | Het [Azure Migrate-toestel](migrate-appliance.md) wordt on-premises geïmplementeerd. | Het [Azure Migrate Replication-toestel](migrate-replication-appliance.md) wordt on-premises geïmplementeerd.
**Compatibiliteit met siteherstel** | Compatibel. | U niet repliceren met Azure Migrate Server Migration als u replicatie voor een machine hebt ingesteld met Site recovery.
**Doelschijf** | Managed Disks | Managed Disks
**Schijflimieten** | OS-schijf: 2 TB<br/><br/> Gegevensschijf: 4 TB<br/><br/> Maximale schijven: 60 | OS-schijf: 2 TB<br/><br/> Gegevensschijf: 8 TB<br/><br/> Maximale schijven: 63
**Doorgeefschijven** | Niet ondersteund | Ondersteund
**UEFI-opstart** | Niet ondersteund | De gemigreerde VM in Azure wordt automatisch geconverteerd naar een BIOS-opstartVM.<br/><br/> De OS-schijf moet maximaal vier partities hebben en volumes moeten worden opgemaakt met NTFS.


## <a name="deployment-steps-comparison"></a>Vergelijking van implementatiestappen

Nadat u de beperkingen hebt bekeken, u na het bekijken van de stappen die nodig zijn bij het implementeren van elke oplossing, bepalen welke optie u wilt kiezen.

**Taak** | **Details** |**Zonder agent** | **Agent-based**
--- | --- | --- | ---
**Beoordeling** | Beoordeel servers vóór migratie.  Beoordeling is optioneel. We raden u aan machines te beoordelen voordat u ze migreert, maar dat hoeft niet. <br/><br/> Voor de beoordeling stelt Azure Migrate een lichtgewicht toestel in om VM's te ontdekken en te beoordelen. | Als u na beoordeling een agentloze migratie uitvoert, wordt hetzelfde Azure Migrate-toestel dat is ingesteld voor beoordeling gebruikt voor agentloze migratie.  |  Als u na beoordeling een op agent gebaseerde migratie uitvoert, wordt het apparaat dat is ingesteld voor beoordeling niet gebruikt tijdens agentloze migratie. U het apparaat op zijn plaats laten staan of verwijderen als u geen verdere ontdekking en beoordeling wilt doen.
**VMware-servers en VM's voorbereiden op migratie** | Configureer een aantal instellingen op VMware-servers en VM's. | Vereist | Vereist
**Het gereedschap Servermigratie toevoegen** | Voeg het hulpprogramma azure migrateservermigratie toe aan het Azure Migrate-project. | Vereist | Vereist
**Het Azure Migrate-toestel implementeren** | Stel een lichtgewicht toestel in op een VMware VM voor VM-detectie en -beoordeling. | Vereist | Niet vereist.
**De Mobiliteitsservice installeren op VM's** | Installeer de Mobiliteitsservice op elke vm die u wilt repliceren | Niet vereist | Vereist
**Het replicatietoestel azure migrateserver-migratie implementeren implementeren** | Een toestel instellen op een VMware VM om VM's te ontdekken en een brug te slaan tussen de Mobiliteitsservice die wordt uitgevoerd op VM's en Azure Migrate Server Migration | Niet vereist | Vereist
**VM's repliceren**. VM-replicatie inschakelen. | Replicatie-instellingen configureren en VM's selecteren om te repliceren | Vereist | Vereist
**Een testmigratie uitvoeren** | Voer een testmigratie uit om te zien of alles werkt zoals verwacht. | Vereist | Vereist
**Een volledige migratie uitvoeren** | Migreer de VM's. | Vereist | Vereist




## <a name="next-steps"></a>Volgende stappen

[VMware VM's migreren](tutorial-migrate-vmware.md) met agentloze migratie.



