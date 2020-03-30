---
title: Onderhoudsmeldingen
description: Overzicht van onderhoudsmeldingen voor virtuele machines die in Azure worden uitgevoerd.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 68159577cb31145be5063bb19af6db71ca1727bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115682"
---
# <a name="handling-planned-maintenance-notifications"></a>Geplande onderhoudsmeldingen afhandelen

Van tijd tot tijd voert Azure updates uit om de betrouwbaarheid, prestaties en veiligheid te verbeteren van de host-infrastructuur voor virtuele machines. Updates zijn wijzigingen zoals het patchen van de hostingomgeving of het upgraden en ontmantelen van hardware. Een meerderheid van deze updates worden voltooid zonder enige impact op de gehoste virtuele machines. Er zijn echter gevallen waarin updates wel een impact hebben:

- Als het onderhoud geen herstart vereist, gebruikt Azure in-place migratie om de VM te pauzeren terwijl de host wordt bijgewerkt. Deze typen onderhoudsbewerkingen worden toegepast fout domein door fout domein. De voortgang wordt gestopt als er waarschuwingsgezondheidssignalen worden ontvangen.

- Als onderhoud een herstart vereist, krijgt u een melding van wanneer het onderhoud is gepland. U krijgt een tijdsvenster van ongeveer 35 dagen waar u zelf het onderhoud starten, wanneer het voor u werkt.


Gepland onderhoud waarvoor een herstart vereist is, is gepland in golven. Elke golf heeft een ander bereik (regio's).

- Een golf begint met een melding aan klanten. Standaard wordt de melding verzonden naar de servicebeheerder en medebeheerders. U meer geadresseerden en berichtenopties toevoegen, zoals e-mail, sms en webhooks, met behulp van [waarschuwingen voor activiteitenlogboeken.](../service-health/alerts-activity-log-service-notifications.md)  
- Zodra een melding wordt uitgevoerd, wordt een *selfservicevenster* beschikbaar gesteld. Tijdens dit venster u nagaan welke van uw virtuele machines worden beïnvloed en het onderhoud starten op basis van uw eigen planningsbehoeften. Het selfservicevenster is meestal ongeveer 35 dagen.
- Na het selfservicevenster begint een *gepland onderhoudsvenster.* Op een bepaald moment tijdens dit venster plant en past Azure het vereiste onderhoud toe op uw virtuele machine. 

Het doel van het hebben van twee vensters is om u voldoende tijd te geven om onderhoud te starten en uw virtuele machine opnieuw op te starten terwijl u weet wanneer Azure automatisch het onderhoud start.


U de Azure-portal, PowerShell, REST API en CLI gebruiken om query's op te vragen voor de onderhoudsvensters voor uw VM's en zelfserviceonderhoud te starten.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Moet u beginnen met het onderhoud tijdens het selfservicevenster?  

De volgende richtlijnen moeten u helpen beslissen of u deze mogelijkheid wilt gebruiken en het onderhoud op uw eigen tijd wilt starten. 

> [!NOTE] 
> Zelfserviceonderhoud is mogelijk niet beschikbaar voor al uw VM's. Als u wilt bepalen of proactieve redeploy beschikbaar is voor uw vm, zoekt u nu naar het **startscherm** in de onderhoudsstatus. Selfservice-onderhoud is momenteel niet beschikbaar voor Cloud Services (Web/Worker Role) en Service Fabric.


Selfserviceonderhoud wordt niet aanbevolen voor implementaties met behulp van **beschikbaarheidssets.** Beschikbaarheidssets worden al slechts één updatedomein tegelijk bijgewerkt. 

- Laat Azure het onderhoud activeren. Voor onderhoud dat opnieuw moet worden opgestart, wordt het onderhoudsdomein per updatedomein uitgevoerd. De updatedomeinen ontvangen het onderhoud niet noodzakelijkerwijs opeenvolgend en dat er een pauze van 30 minuten is tussen updatedomeinen. 
- Als een tijdelijk verlies van een bepaalde capaciteit (1 updatedomein) een probleem is, u instanties toevoegen tijdens de onderhoudsperiode. 
- Voor onderhoud waarvoor geen reboot nodig is, worden updates toegepast op het niveau van het foutdomein. 

**Gebruik geen** selfserviceonderhoud in de volgende scenario's: 
- Als u uw VM's vaak afsluit, handmatig, met Behulp van DevTest Labs, met behulp van automatische afsluiting of volgens een schema, kan dit de onderhoudsstatus terugdraaien en dus extra downtime veroorzaken.
- Op kortstondige VM's waarvan u weet dat ze voor het einde van de onderhoudsgolf worden verwijderd. 
- Voor workloads met een grote status die is opgeslagen in de lokale (kortstondige) schijf die gewenst is om te worden onderhouden bij het bijwerken. 
- Voor gevallen waarin u het formaat van uw vm vaak wijzigt, omdat de onderhoudsstatus kan worden teruggedraaid. 
- Als u geplande gebeurtenissen hebt aangenomen die een proactieve failover of gracieuze afsluiting van uw werklast mogelijk maken, 15 minuten voor het begin van het afsluiten van het onderhoud

**Gebruik** selfserviceonderhoud als u van plan bent om uw VM ononderbroken uit te voeren tijdens de geplande onderhoudsfase en geen van de hierboven genoemde tegenindicaties van toepassing is. 

Het is het beste om selfservice onderhoud te gebruiken in de volgende gevallen:
- U moet een exact onderhoudsvenster communiceren naar uw management of eindklant. 
- U moet het onderhoud op een bepaalde datum voltooien. 
- U moet de volgorde van het onderhoud te controleren, bijvoorbeeld, multi-tier applicatie om veilig herstel te garanderen.
- Er is meer dan 30 minuten VM-hersteltijd nodig tussen twee updatedomeinen (UD's). Als u de tijd tussen updatedomeinen wilt beheren, moet u het onderhoud van uw VM's één updatedomein (UD) tegelijk activeren.


## <a name="faq"></a>Veelgestelde vragen


**V: Waarom moet je mijn virtuele machines nu opnieuw opstarten?**

**A:** Hoewel de meeste updates en upgrades voor het Azure-platform geen invloed hebben op de beschikbaarheid van virtuele machines, zijn er gevallen waarin we niet kunnen voorkomen dat virtuele machines opnieuw opstarten die in Azure worden gehost. We hebben verschillende wijzigingen verzameld die ons verplichten om onze servers opnieuw op te starten, wat zal resulteren in een reboot van virtuele machines.

**V: Als ik uw aanbevelingen voor hoge beschikbaarheid volg met behulp van een beschikbaarheidsset, ben ik dan veilig?**

**A:** Virtuele machines die zijn geïmplementeerd in een beschikbaarheidsset of virtuele machineschaalsets hebben het begrip Update-domeinen (UD). Bij het uitvoeren van onderhoud houdt Azure zich aan de UD-beperking en start het virtuele machines van verschillende UD (binnen dezelfde beschikbaarheidsset) niet opnieuw op.  Azure wacht ook minstens 30 minuten voordat u naar de volgende groep virtuele machines gaat. 

Zie [Beschikbaarheid voor virtuele machines in Azure voor](./linux/availability.md)meer informatie over hoge beschikbaarheid.

**V: Hoe krijg ik een melding over gepland onderhoud?**

**A:** Een geplande onderhoudsgolf begint met het instellen van een planning voor een of meer Azure-regio's. Kort daarna wordt een e-mailmelding verzonden naar de abonnementsbeheerders (één e-mail per abonnement). Extra kanalen en ontvangers voor deze melding kunnen worden geconfigureerd met behulp van waarschuwingen voor activiteitenlogboeken. Als u een virtuele machine implementeert in een regio waar gepland onderhoud al is gepland, ontvangt u de melding niet, maar moet u de onderhoudsstatus van de VM controleren.

**V: Ik zie geen indicatie van gepland onderhoud in het portaal, Powershell of CLI. Wat is er aan de hand?**

**A:** Informatie met betrekking tot gepland onderhoud is tijdens een geplande onderhoudsgolf alleen beschikbaar voor de VM's die hierdoor zullen worden beïnvloed. Met andere woorden, als u geen gegevens ziet, kan het zijn dat de onderhoudsgolf al is voltooid (of niet is gestart) of dat uw virtuele machine al wordt gehost in een bijgewerkte server.

**V: Is er een manier om precies te weten wanneer mijn virtuele machine zal worden beïnvloed?**

**A:** Bij het instellen van de planning definiëren we een tijdvenster van meerdere dagen. Echter, de exacte volgorde van servers (en VM's) binnen dit venster is onbekend. Klanten die de exacte tijd voor hun VM's willen weten, kunnen [geplande gebeurtenissen](./linux/scheduled-events.md) en query's gebruiken vanuit de virtuele machine en een 15 minuten durende melding ontvangen voordat een VM opnieuw opstart.

**V: Hoe lang duurt het voordat u mijn virtuele machine opnieuw opstart?**

**A:**  Afhankelijk van de grootte van uw vm kan het opstarten enkele minuten duren tijdens het selfserviceonderhoudsvenster. Tijdens de azure-gestarte reboots in het geplande onderhoudsvenster duurt het opnieuw opstarten doorgaans ongeveer 25 minuten. Houd er rekening mee dat in het geval u Cloud Services (Web/Worker Role), Virtual Machine Scale Sets of beschikbaarheidssets gebruikt, u 30 minuten krijgt tussen elke groep VM's (UD) tijdens het geplande onderhoudsvenster.

**V: Wat is de ervaring in het geval van Virtuele Machine Schaalsets?**

**A:** Gepland onderhoud is nu beschikbaar voor virtuele machineschaalsets. Voor instructies over het initiëren van selfserviceonderhoud raadpleegt u gepland onderhoud voor het document [van virtuele machineschaalsets.](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md)

**V: Wat is de ervaring in het geval van Cloud Services (Web/Worker Role) en Service Fabric?**

**A:** Hoewel deze platforms worden beïnvloed door gepland onderhoud, worden klanten die deze platforms gebruiken als veilig beschouwd, aangezien alleen VM's in één Upgrade-domein (UD) op een bepaald moment worden beïnvloed. Selfservice-onderhoud is momenteel niet beschikbaar voor Cloud Services (Web/Worker Role) en Service Fabric.

**V: Ik zie geen onderhoudsinformatie over mijn VM's. Wat ging er mis?**

**A:** Er zijn verschillende redenen waarom u geen onderhoudsinformatie op uw VM's ziet:
1.  U gebruikt een abonnement dat is gemarkeerd als intern van Microsoft.
2.  Uw VM's zijn niet gepland voor onderhoud. Het kan zijn dat de onderhoudsgolf is beëindigd, geannuleerd of gewijzigd, zodat uw VM's er geen invloed meer op hebben.
3.  De kolom **Onderhoud** wordt niet toegevoegd aan de weergave VM-lijst. Hoewel we deze kolom aan de standaardweergave hebben toegevoegd, moeten klanten die zijn geconfigureerd om niet-standaardkolommen te zien, de kolom **Onderhoud** handmatig toevoegen aan hun vm-lijstweergave.

**V: Mijn VM is voor de tweede keer gepland voor onderhoud. Waarom?**

**A:** Er zijn verschillende use cases waarbij u uw VM voor onderhoud ziet staan nadat u uw onderhoudsimplementatie al hebt voltooid:
1.  We hebben de onderhoudsgolf geannuleerd en opnieuw opgestart met een ander laadvermogen. Het kan zijn dat we defecte lading hebben ontdekt en we moeten gewoon een extra lading inzetten.
2.  Uw VM is door een hardwarefout naar een ander knooppunt *genas.*
3.  U hebt ervoor gekozen om de VM te stoppen (detoewijzen) en de VM opnieuw te starten.
4.  U hebt **automatische afsluiting** ingeschakeld voor de VM.



## <a name="next-steps"></a>Volgende stappen

U gepland onderhoud afhandelen met de [Azure CLI,](maintenance-notifications-cli.md) [Azure PowerShell](maintenance-notifications-powershell.md) of [portal.](maintenance-notifications-portal.md)

