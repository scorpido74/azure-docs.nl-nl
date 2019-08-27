---
title: Onderhouds meldingen voor Linux-Vm's in azure verwerken | Microsoft Docs
description: Bekijk onderhouds meldingen voor virtuele Linux-machines die in Azure worden uitgevoerd en start self-service onderhoud.
services: virtual-machines-linux
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: shants
ms.openlocfilehash: 347df9567817cdaea731e06646196142885757b1
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018826"
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Meldingen voor gepland onderhoud verwerken voor virtuele Linux-machines

Van tijd tot tijd voert Azure updates uit om de betrouwbaarheid, prestaties en veiligheid te verbeteren van de host-infrastructuur voor virtuele machines. Updates zijn wijzigingen zoals het patchen van de hosting omgeving of het upgraden en uit bedrijf nemen van hardware. Een meerderheid van deze updates wordt uitgevoerd zonder dat dit van invloed is op de gehoste virtuele machines. Er zijn echter gevallen waarin updates een impact hebben:

- Als voor het onderhoud geen herstart is vereist, gebruikt Azure in-place migratie om de VM te onderbreken terwijl de host wordt bijgewerkt. Deze niet-opnieuw opgestart onderhouds bewerkingen worden toegepast op een fout domein en de voortgang wordt gestopt als er waarschuwings status signalen worden ontvangen.

- Als de computer opnieuw moet worden opgestart, wordt een melding weer gegeven wanneer het onderhoud is gepland. In deze gevallen krijgt u een tijd venster met meestal 35 dagen waarop u het onderhoud zelf kunt starten, wanneer dit voor u geschikt is.


Gepland onderhoud waarvoor opnieuw opstarten is vereist, wordt gepland in golven. Elke golf heeft een ander bereik (regio's).

- Een golf begint met een melding aan klanten. Standaard wordt er een melding verzonden naar de eigenaar van het abonnement en mede-eigen aars. U kunt meer ontvangers en bericht opties zoals e-mail, SMS en webhooks toevoegen aan de meldingen met Azure- [activiteiten logboek waarschuwingen](../../azure-monitor/platform/activity-logs-overview.md).  
- Op het moment van de melding wordt een *self-service venster* beschikbaar gesteld. Tijdens deze periode van meestal 35 dagen kunt u zien welke van uw virtuele machines zijn opgenomen in deze Wave en proactief onderhoud starten volgens uw eigen plannings behoeften.
- Na het self-service venster begint een *gepland onderhouds venster* . Op een bepaald moment tijdens dit venster plant Azure en past het vereiste onderhoud toe op uw virtuele machine. 

Het doel van het hebben van twee vensters is dat u voldoende tijd hebt om onderhoud te starten en uw virtuele machine opnieuw op te opstarten, terwijl u zeker weet wanneer Azure automatisch onderhoud start.


U kunt de Azure Portal, Power shell, REST API en CLI gebruiken om een query uit te zoeken naar de onderhouds Vensters voor uw virtuele machines en het onderhoud van self-service te starten.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Moet u onderhoud starten met tijdens het selfservice venster?  

De volgende richt lijnen kunnen u helpen beslissen of u deze mogelijkheid wilt gebruiken en op uw eigen moment onderhoud kunt starten. 

> [!NOTE] 
> Het onderhoud van self-service is mogelijk niet beschikbaar voor al uw virtuele machines. Als u wilt bepalen of proactieve herimplementatie beschikbaar is voor uw virtuele machine, zoekt u naar de **Start nu** in de onderhouds status. Self-service onderhoud is momenteel niet beschikbaar voor Cloud Services (Web/Worker-rol) en Service Fabric.


Selfservice onderhoud wordt niet aanbevolen voor implementaties met behulp van **beschikbaarheids sets** , omdat dit Maxi maal beschik bare instellingen zijn, waarbij slechts één update domein op een bepaald moment wordt beïnvloed. 
- Laat Azure het onderhoud activeren. Voor onderhoud waarvoor opnieuw opstarten is vereist, moet u er rekening mee houden dat het onderhoud wordt uitgevoerd op het update domein, dat de update domeinen niet noodzakelijkerwijs het onderhoud op de juiste plaats ontvangen en dat er een onderbreking van 30 minuten tussen de update domeinen is. 
- Als een tijdelijk verlies van een deel van uw capaciteit (1/update domein) een probleem is, kan het eenvoudig worden gecompenseerd door toevoeging instanties toe te wijzen tijdens de onderhouds periode. 
- Voor onderhoud waarbij niet opnieuw moet worden opgestart, worden updates toegepast op het niveau van het fout domein. 

Gebruik self-service onderhoud **niet** in de volgende scenario's: 
- Als u uw virtuele machines regel matig afsluit, met behulp van DevTest Labs, met behulp van automatisch afsluiten of volgens een planning, kan dit de onderhouds status herstellen en daardoor extra downtime veroorzaken.
- Op korte en gelaagde Vm's die u weet, worden vóór het einde van de onderhouds Golf verwijderd. 
- Voor werk belastingen met een grote status die is opgeslagen in de lokale (tijdelijke) schijf die bij het bijwerken wordt bewaard. 
- Wanneer u de grootte van uw virtuele machine regel matig wijzigt, kan dit de onderhouds status herstellen. 
- Als u geplande gebeurtenissen hebt aangenomen die proactieve failover of het correct afsluiten van uw werk belasting hebben uitgevoerd, wordt 15 minuten voor het afsluiten van het onderhoud gestart

**Gebruik** self-service onderhoud als u van plan bent om uw virtuele machine tijdens de geplande onderhouds fase zonder onderbreking uit te voeren en geen van de hierboven vermelde prestatie meters van toepassing zijn. 

Het is aan te raden selfservice onderhoud in de volgende gevallen te gebruiken:
- U moet een exact onderhouds venster aan uw beheer of eind klant communiceren. 
- U moet het onderhoud op een bepaalde datum volt ooien. 
- U moet de volg orde van onderhoud, bijvoorbeeld een toepassing met meerdere lagen, beheren om veilig herstel te garanderen.
- U hebt meer dan 30 minuten aan VM-herstel tijd tussen twee update domeinen (UDs) nodig. Als u de tijd tussen update domeinen wilt bepalen, moet u onderhoud activeren op uw virtuele machines met één update domein (UD) tegelijk.



## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Vm's zoeken die zijn gepland voor onderhoud met behulp van CLI

Informatie over gepland onderhoud kan worden weer gegeven met behulp van de [Azure VM Get-instance-View](/cli/azure/vm?view=azure-cli-latest).
 
Onderhouds informatie wordt alleen geretourneerd als er onderhoud wordt gepland. Als er geen onderhoud is gepland die van invloed is op de virtuele machine, retourneert de opdracht geen onderhouds informatie. 

```azure-cli
az vm get-instance-view -g rgName -n vmName
```

De volgende waarden worden geretourneerd onder MaintenanceRedeployStatus: 

| Value | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Hiermee wordt aangegeven of u op dit moment onderhoud kunt starten op de virtuele machine |
| PreMaintenanceWindowStartTime         | Het begin van het onderhouds self-service venster wanneer u onderhoud op uw virtuele machine kunt initiëren |
| PreMaintenanceWindowEndTime           | Het einde van het onderhouds self-service venster wanneer u onderhoud op uw virtuele machine kunt initiëren |
| MaintenanceWindowStartTime            | Het begin van het geplande onderhouds venster waarin Azure onderhoud initieert op uw virtuele machine |
| MaintenanceWindowEndTime              | Het einde van het geplande onderhouds venster waarin Azure onderhoud initieert op uw virtuele machine |
| LastOperationResultCode               | Het resultaat van de laatste poging om onderhoud op de VM te initiëren |




## <a name="start-maintenance-on-your-vm-using-cli"></a>Onderhoud starten op uw virtuele machine met behulp van CLI

Met de volgende aanroep wordt onderhoud gestart op een VM als `IsCustomerInitiatedMaintenanceAllowed` deze is ingesteld op waar.

```azure-cli
az vm perform-maintenance -g rgName -n vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="classic-deployments"></a>Klassieke implementaties

Als u nog steeds oudere Vm's hebt die zijn geïmplementeerd met behulp van het klassieke implementatie model, kunt u de klassieke Azure-CLI gebruiken om te zoeken naar Vm's en het onderhoud te initiëren.

Zorg ervoor dat u zich in de juiste modus bevindt voor gebruik met een klassieke virtuele machine door het volgende te typen:

```
azure config mode asm
```

Als u de onderhouds status van een virtuele machine met de naam *myVM*wilt ophalen, typt u:

```
azure vm show myVM 
``` 

Als u onderhoud wilt starten op uw klassieke virtuele machine met de naam *myVM* in de *myService* -service en *myDeployment* -implementatie, typt u:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```


## <a name="faq"></a>Veelgestelde vragen


**V: Waarom moet u nu mijn virtuele machines opnieuw opstarten?**

**A:** Hoewel het meren deel van updates en upgrades voor het Azure-platform geen invloed hebben op de beschik baarheid van de virtuele machine, zijn er gevallen waarin we niet kunnen voor komen dat virtuele machines die worden gehost in azure niet worden gestart. We hebben verschillende wijzigingen verzameld die ons nodig hebben om de servers opnieuw op te starten. Dit leidt ertoe dat virtuele machines opnieuw worden opgestart.

**V: Als ik uw aanbevelingen voor hoge Beschik baarheid volg met een Beschikbaarheidsset, ben ik dan veilig?**

**A:** Voor virtuele machines die worden geïmplementeerd in een beschikbaarheidsset of een schaalset voor virtuele machines is het begrip Updatedomein (UD) van toepassing. Bij het uitvoeren van onderhoud voldoet Azure aan de UD-beperking en worden virtuele machines van verschillende UD niet opnieuw opgestart (binnen dezelfde beschikbaarheidsset).  Azure wacht nog ten minste 30 minuten voordat de volgende groep virtuele machines wordt verplaatst. 

Zie [Beschik baarheid voor virtuele machines in azure](availability.MD)voor meer informatie over maximale Beschik baarheid.

**V: Hoe kan ik ontvang dan een melding over gepland onderhoud?**

**A:** Een geplande onderhouds Golf wordt gestart door een planning in te stellen voor een of meer Azure-regio's. Binnenkort daarna wordt er een e-mail melding verzonden naar de eigen aars van het abonnement (één e-mail per abonnement). Aanvullende kanalen en ontvangers voor deze melding kunnen worden geconfigureerd met behulp van waarschuwingen voor activiteiten Logboeken. Als u een virtuele machine implementeert in een regio waar gepland onderhoud al is gepland, wordt de melding niet weer gegeven, maar moet u de onderhouds status van de VM controleren.

**V: Ik zie geen indicatie van gepland onderhoud in de portal, Power shell of CLI. Wat is er aan de hand?**

**A:** Informatie met betrekking tot gepland onderhoud is alleen beschikbaar tijdens een geplande onderhouds Golf voor de virtuele machines waarop deze worden beïnvloed. Met andere woorden, als u geen gegevens ziet, kan het zijn dat de onderhouds Golf al is voltooid (of niet is gestart) of dat uw virtuele machine al wordt gehost op een bijgewerkte server.

**V: Is er een manier om precies te weten wanneer mijn virtuele machine wordt beïnvloed?**

**A:** Wanneer u het schema instelt, definiëren we een tijd venster van enkele dagen. De exacte volg orde van servers (en Vm's) in dit venster is echter onbekend. Klanten die de exacte tijd willen weten voor hun virtuele machines, kunnen [geplande gebeurtenissen](scheduled-events.md) en query's vanaf de virtuele machine gebruiken en een melding van 15 minuten ontvangen voordat een VM opnieuw wordt opgestart.

**V: Hoe lang duurt het om de virtuele machine opnieuw op te starten?**

**A:**  Afhankelijk van de grootte van uw virtuele machine kan het enkele minuten duren voordat de computer opnieuw wordt opgestart. Tijdens het opnieuw opstarten van Azure in het venster gepland onderhoud wordt het systeem doorgaans ongeveer 25 minuten uitgevoerd. Houd er rekening mee dat als u Cloud Services (Web/Worker), Virtual Machine Scale Sets of beschikbaarheids sets gebruikt, u 30 minuten krijgt tussen elke groep Vm's (UD) tijdens het geplande onderhouds venster.

**V: Wat is de ervaring in het geval van Virtual Machine Scale Sets?**

**A:** Gepland onderhoud is nu beschikbaar voor Virtual Machine Scale Sets. Zie [gepland onderhoud voor virtuele-machine schaal sets](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) document voor instructies over het initiëren van self-service onderhoud.

**V: Wat is de ervaring in het geval van Cloud Services (Web/Worker) en Service Fabric?**

**A:** Hoewel deze platformen worden beïnvloed door gepland onderhoud, worden klanten die gebruikmaken van deze platformen als veilig beschouwd, omdat alleen virtuele machines binnen een enkel Upgrade Domain (UD) tegelijkertijd worden beïnvloed. Self-service onderhoud is momenteel niet beschikbaar voor Cloud Services (Web/Worker-rol) en Service Fabric.

**V: Ik zie geen onderhouds informatie op mijn Vm's. Wat is er verkeerd gegaan?**

**A:** Er zijn verschillende redenen waarom u geen onderhouds informatie ziet over uw Vm's:
1.  U gebruikt een abonnement dat is gemarkeerd als interne micro soft.
2.  Uw Vm's zijn niet gepland voor onderhoud. Het kan zijn dat de onderhouds Golf is beëindigd, geannuleerd of gewijzigd, zodat uw Vm's hierdoor niet meer worden beïnvloed.
3.  U beschikt niet over de kolom **onderhoud** die is toegevoegd aan de lijst weergave van de virtuele machine. Hoewel we deze kolom aan de standaard weergave hebben toegevoegd, moeten klanten die zijn geconfigureerd om niet-standaard kolommen te zien, de kolom **onderhoud** hand matig toevoegen aan hun VM-lijst weergave.

**V: Mijn VM is voor de tweede keer gepland voor onderhoud. Waarom is dat?**

**A:** In bepaalde gevallen is er onderhoud voor uw VM gepland terwijl dit al is uitgevoerd en uw VM opnieuw is geïmplementeerd:
1.  De onderhouds Golf is geannuleerd en opnieuw gestart met een andere nettolading. Het kan zijn dat er een beschadigde Payload is gedetecteerd en dat er alleen een extra Payload moet worden geïmplementeerd.
2.  De virtuele machine is op een ander knoop punt retoucheerd vanwege een hardwarefout.
3.  U hebt ervoor gekozen om te stoppen (toewijzing ongedaan te maken) en de virtuele machine opnieuw op te starten.
4.  U hebt **automatisch afsluiten** ingeschakeld voor de virtuele machine.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u zich kunt registreren voor onderhouds gebeurtenissen vanuit de VM met behulp van [Scheduled Events](scheduled-events.md).
