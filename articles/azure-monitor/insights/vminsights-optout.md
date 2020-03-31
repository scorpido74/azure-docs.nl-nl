---
title: Controle uitschakelen in Azure Monitor voor VM's
description: In dit artikel wordt beschreven hoe u stoppen met het bewaken van uw virtuele machines in Azure Monitor voor VM's.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480518"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Controle van uw VM's uitschakelen in Azure-monitor voor VM's

Nadat u de bewaking van uw virtuele machines (VM's) hebt ingeschakeld, u er later voor kiezen om de bewaking in Azure Monitor voor VM's uit te schakelen. In dit artikel ziet u hoe u de controle voor een of meer VM's uitschakelt.  

Azure Monitor voor VM's biedt momenteel geen ondersteuning voor het selectief uitschakelen van VM-monitoring. Uw Log Analytics-werkruimte biedt mogelijk ondersteuning voor Azure Monitor voor VM's en andere oplossingen. Het kan ook andere bewakingsgegevens verzamelen. Als uw Log Analytics-werkruimte deze services biedt, moet u het effect en de methoden begrijpen om de controle uit te schakelen voordat u begint.

Azure Monitor voor VM's is afhankelijk van de volgende componenten om de ervaring te bieden:

* Een log-analysewerkruimte, die bewakingsgegevens van VM's en andere bronnen opslaat.
* Een verzameling prestatiemeteritems die in de werkruimte zijn geconfigureerd. De verzameling werkt de bewakingsconfiguratie bij op alle VM's die met de werkruimte zijn verbonden.
* `VMInsights`, dat is een bewakingsoplossing die is geconfigureerd in de werkruimte. Met deze oplossing wordt de bewakingsconfiguratie bijgewerkt op alle VM's die met de werkruimte zijn verbonden.
* `MicrosoftMonitoringAgent`en `DependencyAgent`, die Azure VM-extensies zijn. Deze extensies verzamelen en verzenden gegevens naar de werkruimte.

Houd bij het uitschakelen van de controle op uw VM's rekening met deze overwegingen:

* Als u met één virtuele machine hebt geëvalueerd en de vooraf geselecteerde standaardwerkruimte Log Analytics hebt gebruikt, u de bewaking uitschakelen door de afhankelijkheidsagent van de VM te verwijderen en de logboekanalyseagent los te koppelen van deze werkruimte. Deze benadering is geschikt als u van plan bent de vm voor andere doeleinden te gebruiken en later besluit deze opnieuw aan een andere werkruimte te verbinden.
* Als u een reeds bestaande Log Analytics-werkruimte hebt geselecteerd die andere bewakingsoplossingen en gegevensverzameling uit andere bronnen ondersteunt, u oplossingsonderdelen uit de werkruimte verwijderen zonder uw werkruimte te onderbreken of te beïnvloeden.  

>[!NOTE]
> Nadat u de oplossingsonderdelen uit uw werkruimte hebt verwijderd, u prestaties en kaartgegevens voor uw Azure-VM's blijven zien. Gegevens worden uiteindelijk niet meer weergegeven in de weergaven **Prestaties** **en kaart.** De optie **Inschakelen** is beschikbaar bij de geselecteerde Azure VM, zodat u in de toekomst de bewaking opnieuw inschakelen.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Azure Monitor voor VM's volledig verwijderen

Als u de werkruimte Log Analytics nog steeds nodig hebt, voert u de volgende stappen uit om Azure Monitor voor VM's volledig te verwijderen. U verwijdert de `VMInsights` oplossing uit de werkruimte.  

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle services**in de Azure-portal . Typ in de lijst met resources **Log Analytics**. Terwijl u begint met typen, filtert de lijst suggesties op basis van uw invoer. Selecteer **Log Analytics**.
3. Selecteer in uw lijst met Log Analytics-werkruimten de werkruimte die u hebt gekozen wanneer u Azure Monitor voor VM's hebt ingeschakeld.
4. **Selecteer**oplossingen aan de linkerkant .  
5. Selecteer **VMInsights(naam van de werkruimte)** in de lijst met oplossingen. Selecteer **Verwijderen**op de pagina **Overzicht** voor de oplossing . Wanneer u wordt gevraagd om te bevestigen, selecteert u **Ja**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Controle uitschakelen en de werkruimte behouden  

Als uw Log Analytics-werkruimte nog steeds controle uit andere bronnen moet ondersteunen, moet u deze stappen volgen om de controle op de VM die u hebt gebruikt om Azure-monitor voor VM's te evalueren, uit te schakelen. Voor Azure VM's verwijdert u de VM-extensie afhankelijkheidsagent en de VM-extensie Log Analytics-agent voor Windows of Linux rechtstreeks uit de VM. 

>[!NOTE]
>Verwijder de agent Log Analytics niet als: 
>
> * Azure Automation beheert de VM om processen te orkestreren of configuratie of updates te beheren. 
> * Azure Security Center beheert de VM voor beveiliging en bedreigingsdetectie. 
>
> Als u de Log Analytics-agent verwijdert, voorkomt u dat deze services en oplossingen uw VM proactief beheren. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Selecteer **virtuele machines**in de Azure-portal . 
3. Selecteer een VM in de lijst. 
4. Selecteer Links **Extensies**. Selecteer **DependencyAgent**op de pagina **Extensies** .
5. **Selecteer**verwijderen op de pagina extensie-eigenschappen .
6. Selecteer **MicrosoftMonitoringAgent**op de pagina **Extensies** . **Selecteer**verwijderen op de pagina extensie-eigenschappen .  
