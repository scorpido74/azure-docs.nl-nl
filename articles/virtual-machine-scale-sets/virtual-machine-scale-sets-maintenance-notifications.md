---
title: Onderhoudsmeldingen voor virtuele machineschaalsets in Azure
description: Bekijk onderhoudsmeldingen en start selfserviceonderhoud voor virtuele machineschaalsets in Azure.
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: shants
ms.openlocfilehash: 53ebb7c4710c5455ef90701dc7e94f1b846a874a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062703"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Meldingen voor gepland onderhoud voor virtuele-machineschaalsets


Azure voert periodiek updates uit om de betrouwbaarheid, prestaties en beveiliging van de hostinfrastructuur voor virtuele machines (VM's) te verbeteren. Updates kunnen bestaan uit het patchen van de hostingomgeving of het upgraden en ontmantelen van hardware. De meeste updates hebben geen invloed op de gehoste VM's. Updates zijn echter van invloed op VM's in deze scenario's:

- Als het onderhoud geen herstart vereist, gebruikt Azure in-place migratie om de VM te pauzeren terwijl de host wordt bijgewerkt. Onderhoudsbewerkingen waarvoor geen herstart vereist is, worden toegepast door het foutdomein. De voortgang wordt gestopt als er waarschuwingsgezondheidssignalen worden ontvangen.

- Als onderhoud een herstart vereist, krijgt u een melding van wanneer het onderhoud is gepland. In deze gevallen krijgt u een tijdvenster dat meestal 35 dagen is waar u het onderhoud zelf starten, wanneer het voor u werkt.


Gepland onderhoud waarvoor een herstart vereist is, is gepland in golven. Elke golf heeft een ander bereik (regio's):

- Een golf begint met een melding aan klanten. Standaard wordt de melding verzonden naar de eigenaar van het abonnement en mede-eigenaren. U ontvangers en berichtenopties zoals e-mail, sms en webhooks toevoegen aan de meldingen met [azure activity log-waarschuwingen.](../azure-monitor/platform/platform-logs-overview.md)  
- Met een melding wordt een *selfservicevenster* beschikbaar gesteld. Tijdens dit venster dat meestal 35 dagen duurt, u zien welke van uw VM's in de golf zijn opgenomen. U proactief starten met onderhoud volgens uw eigen planningsbehoeften.
- Na het selfservicevenster begint een *gepland onderhoudsvenster.* Op een bepaald moment tijdens dit venster plant en past Azure het vereiste onderhoud toe op uw VM. 

Het doel van het hebben van twee vensters is om u voldoende tijd te geven om het onderhoud te starten en uw VM opnieuw op te starten terwijl u weet wanneer Azure automatisch het onderhoud start.

U de Azure-portal, PowerShell, de REST API en de Azure CLI gebruiken om query's voor onderhoudsvensters voor uw virtuele machineschaalset VM's te gebruiken en om selfserviceonderhoud te starten.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Moet u beginnen met onderhoud tijdens de self-service window?  

Met de volgende richtlijnen u beslissen of u het onderhoud wilt starten op een door u gekozen tijdstip.

> [!NOTE] 
> Zelfserviceonderhoud is mogelijk niet beschikbaar voor al uw VM's. Als u wilt bepalen of proactieve redeploy beschikbaar is voor uw vm, zoekt u nu naar **Start** in de onderhoudsstatus. Op dit moment is selfserviceonderhoud niet beschikbaar voor Azure Cloud Services (Web/Worker Role) en Azure Service Fabric.


Selfserviceonderhoud wordt niet aanbevolen voor implementaties die *beschikbaarheidssets*gebruiken. Beschikbaarheidssets zijn zeer beschikbare opstellingen waarin slechts één updatedomein op elk gewenst moment wordt beïnvloed. Voor beschikbaarheidssets:

- Laat Azure het onderhoud activeren. Voor onderhoud waarvoor een herstart vereist is, wordt het onderhoud uitgevoerd met het domein bijwerken per updatedomein. Updatedomeinen ontvangen het onderhoud niet noodzakelijkerwijs opeenvolgend. Er is een pauze van 30 minuten tussen updatedomeinen.
- Als een tijdelijk verlies van een deel van uw capaciteit (1/update domein telling) is een punt van zorg, u gemakkelijk compenseren voor het verlies door het toewijzen van extra exemplaren tijdens de onderhoudsperiode.
- Voor onderhoud waarvoor geen herstart nodig is, worden updates toegepast op het niveau van het foutdomein. 
    
**Gebruik geen** selfserviceonderhoud in de volgende scenario's: 

- Als u uw VM's vaak afsluit, handmatig, met Behulp van DevTest Labs, met behulp van automatisch afsluiten of door een schema te volgen. Zelfserviceonderhoud in deze scenario's kan de onderhoudsstatus terugdraaien en extra downtime veroorzaken.
- Op kortstondige VM's waarvan u weet dat ze voor het einde van de onderhoudsgolf worden verwijderd. 
- Voor workloads met een grote status die is opgeslagen in de lokale (kortstondige) schijf die u na de update wilt onderhouden. 
- Als u het formaat van uw vm vaak wijzigt. In dit scenario kan de onderhoudsstatus worden teruggedraaid. 
- Als u geplande gebeurtenissen hebt aangenomen die een proactieve failover of gracieuze afsluiting van uw werklast mogelijk maken 15 minuten voordat het afsluiten van onderhoud begint.

**Gebruik** zelfserviceonderhoud als u van plan bent om uw VM ononderbroken uit te voeren tijdens de geplande onderhoudsfase en geen van de voorgaande tegenindicaties van toepassing is. 

Het is het beste om selfservice-onderhoud te gebruiken in de volgende gevallen:

- U moet een exact onderhoudsvenster communiceren naar het management of uw klant. 
- U moet het onderhoud op een bepaalde datum voltooien. 
- U moet de volgorde van het onderhoud controleren, bijvoorbeeld in een multi-tier applicatie, om veilig herstel te garanderen.
- U hebt meer dan 30 minuten VM-hersteltijd nodig tussen twee updatedomeinen. Als u de tijd tussen updatedomeinen wilt beheren, moet u het onderhoud van uw VM's één updatedomein tegelijk activeren.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Virtuele machineschaalsets weergeven die worden beïnvloed door onderhoud in de portal

Wanneer een geplande onderhoudsgolf is gepland, u de lijst met virtuele machineschaalsets bekijken die worden beïnvloed door de komende onderhoudsgolf met behulp van de Azure-portal. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het linkermenu **Alle services**en selecteer Vervolgens **Virtuele machineschaalsets**.
3. Selecteer kolommen **bewerken** onder **Virtuele machineschaalsets**om de lijst met beschikbare kolommen te openen.
4. Selecteer in de sectie **Beschikbare kolommen** de optie **Zelfserviceonderhoud**en verplaats deze naar de lijst **Geselecteerde kolommen.** Selecteer **Toepassen**.  

    Als u het **onderhoudsitem Selfservice** eenvoudiger wilt vinden, u de vervolgkeuzeoptie wijzigen in de sectie **Beschikbare kolommen** van **Alles** naar **Eigenschappen**.

De kolom **Self-service onderhoud** wordt nu weergegeven in de lijst met virtuele machineschaalsets. Elke virtuele machineschaalset kan een van de volgende waarden hebben voor de kolom selfserviceonderhoud:

| Waarde | Beschrijving |
|-------|-------------|
| Ja | Ten minste één VM in uw virtuele machineschaalset bevindt zich in een selfservicevenster. U op elk moment met onderhoud beginnen tijdens dit selfservicevenster. | 
| Nee | Er bevinden zich geen VM's in een selfservicevenster in de getroffen virtuele machineschaalset. | 
| - | Uw virtuele machines schaalsets maken geen deel uit van een geplande onderhoudsgolf.| 

## <a name="notification-and-alerts-in-the-portal"></a>Meldingen en waarschuwingen in de portal

Azure communiceert een planning voor gepland onderhoud door een e-mail te sturen naar de eigenaar van het abonnement en de groep mede-eigenaren. U ontvangers en kanalen aan deze communicatie toevoegen door waarschuwingen voor activiteitenlogboeken te maken. Zie [Abonnementsactiviteit controleren met het Azure-activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Monitor**in het linkermenu . 
3. Selecteer in het deelvenster **Monitor - Waarschuwingen (klassiek)** de optie **+Waarschuwing voor activiteitslogboek toevoegen**.
4. Selecteer of voer op de **waarschuwingspagina Activiteitslogboek toevoegen** de gevraagde gegevens in of voer deze in. Controleer **in Criteria**of u de volgende waarden instelt:
   - **Gebeurteniscategorie**: **Servicestatus selecteren**.
   - **Services**: Selecteer **virtuele machineschaalsets en virtuele machines**.
   - **Type**: Selecteer **Gepland onderhoud**. 
    
Zie Meldingen van [activiteitenlogboeken maken](../azure-monitor/platform/activity-log-alerts.md) voor meer informatie over het configureren van meldingen van activiteitenlogboeken
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Start onderhoud op uw virtuele machine schaalset vanaf de portal

U meer onderhoudsgerelateerde details zien in het overzicht van virtuele machineschaalsets. Als ten minste één vm in de virtuele machineschaalset is opgenomen in de geplande onderhoudsgolf, wordt boven aan de pagina een nieuw meldingslint toegevoegd. Selecteer het meldingslint om naar de pagina **Onderhoud** te gaan. 

Op de pagina **Onderhoud** u zien welke VM-instantie wordt beïnvloed door het geplande onderhoud. Als u het onderhoud wilt starten, schakelt u het selectievakje in dat overeenkomt met de betreffende vm. Selecteer vervolgens **Onderhoud starten**.

Nadat u het onderhoud hebt gestart, ondergaan de getroffen VM's in uw virtuele machineschaalset onderhoud en zijn ze tijdelijk niet beschikbaar. Als u het selfservicevenster hebt gemist, u nog steeds het tijdvenster zien wanneer de grootte van uw virtuele machine wordt onderhouden door Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Controle onderhoudsstatus met PowerShell

U Azure PowerShell gebruiken om te zien wanneer VM's in uw virtuele machineschaalsets zijn gepland voor onderhoud. Geplande onderhoudsinformatie is beschikbaar met de [cmdlet Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) wanneer u de `-InstanceView` parameter gebruikt.
 
Onderhoudsinformatie wordt alleen geretourneerd als onderhoud is gepland. Als er geen onderhoud is gepland dat van invloed is op de VM-instantie, geeft de cmdlet geen onderhoudsgegevens terug. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

De volgende eigenschappen worden geretourneerd onder **MaintenanceRedeployStatus:** 

| Waarde | Beschrijving   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceA toegestaan | Geeft aan of u op dit moment met onderhoud aan de VM beginnen. |
| Begintijd voor onderhoud         | Het begin van het zelfbedieningsvenster voor onderhoud wanneer u onderhoud aan uw VM starten. |
| PreMaintenanceWindowEndTime           | Het einde van het zelfbedieningsvenster voor onderhoud wanneer u onderhoud aan uw VM starten. |
| Begintijd van onderhoudsvenster            | Het begin van het geplande onderhoud waarin Azure onderhoud op uw VM initieert. |
| MaintenanceWindowEndTime              | Het einde van het geplande onderhoudsvenster waarin Azure onderhoud aan uw VM initieert. |
| LastOperationResultCode               | Het resultaat van de laatste poging om onderhoud aan de VM te starten. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Onderhoud aan uw VM-exemplaar starten met PowerShell

U het onderhoud aan een VM starten als **isCustomerInitiatedMaintenanceA toegestaan** is ingesteld op **true.** Gebruik de cmdlet [Set-AzVmss](/powershell/module/az.compute/set-azvmss) met `-PerformMaintenance` parameter.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Controleer de onderhoudsstatus met behulp van de CLI

U geplande onderhoudsgegevens bekijken met behulp van [az vmss-lijst-exemplaren](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Onderhoudsinformatie wordt alleen geretourneerd als onderhoud is gepland. Als er geen onderhoud is dat van invloed is op de VM-instantie, wordt de opdracht geen onderhoudsgegevens teruggegeven. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

De volgende eigenschappen worden geretourneerd onder **MaintenanceRedeployStatus** voor elke VM-instantie: 

| Waarde | Beschrijving   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceA toegestaan | Geeft aan of u op dit moment met onderhoud aan de VM beginnen. |
| Begintijd voor onderhoud         | Het begin van het zelfbedieningsvenster voor onderhoud wanneer u onderhoud aan uw VM starten. |
| PreMaintenanceWindowEndTime           | Het einde van het zelfbedieningsvenster voor onderhoud wanneer u onderhoud aan uw VM starten. |
| Begintijd van onderhoudsvenster            | Het begin van het geplande onderhoud waarin Azure onderhoud op uw VM initieert. |
| MaintenanceWindowEndTime              | Het einde van het geplande onderhoudsvenster waarin Azure onderhoud aan uw VM initieert. |
| LastOperationResultCode               | Het resultaat van de laatste poging om onderhoud aan de VM te starten. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Onderhoud aan uw VM-exemplaar starten met de CLI

De volgende oproep initieert `IsCustomerInitiatedMaintenanceAllowed` onderhoud aan een VM-exemplaar als deze is ingesteld op **true:**

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Veelgestelde vragen

**V: Waarom moet je mijn VM's nu opnieuw opstarten?**

**A:** Hoewel de meeste updates en upgrades naar het Azure-platform geen invloed hebben op de beschikbaarheid van VM's, kunnen we in sommige gevallen niet voorkomen dat vm's die in Azure worden gehost opnieuw worden opgestart. We hebben verschillende wijzigingen verzameld die ons verplichten om onze servers opnieuw op te starten, wat zal resulteren in het opnieuw opstarten van VM.We have accumulate several changes that require us to restart our servers that will result in VM reboot.

**V: Als ik uw aanbevelingen voor hoge beschikbaarheid volg met behulp van een beschikbaarheidsset, ben ik dan veilig?**

**A:** Virtuele machines die zijn geïmplementeerd in een beschikbaarheidsset of in virtuele machineschaalsets, maken gebruik van updatedomeinen. Bij het uitvoeren van onderhoud voldoet Azure aan de beperking van het updatedomein en start het vm's niet opnieuw op vanuit een ander updatedomein (binnen dezelfde beschikbaarheidsset). Azure wacht ook minstens 30 minuten voordat u naar de volgende groep VM's gaat. 

Zie [Regio's en beschikbaarheid voor virtuele machines in Azure voor](../virtual-machines/windows/availability.md)meer informatie over hoge beschikbaarheid.

**V: Hoe kan ik op de hoogte worden gesteld van gepland onderhoud?**

**A:** Een geplande onderhoudsgolf begint met het instellen van een planning voor een of meer Azure-regio's. Kort daarna wordt een e-mailmelding verzonden naar de eigenaren van het abonnement (één e-mail per abonnement). U kanalen en ontvangers voor deze melding toevoegen met waarschuwingen voor activiteitenlogboeken. Als u een vm implementeert naar een gebied waarin gepland onderhoud al is gepland, ontvangt u de melding niet. Controleer in plaats daarvan de onderhoudsstatus van de VM.

**V: Ik zie geen indicatie van gepland onderhoud in het portaal, PowerShell of de CLI. Wat is er aan de hand?**

**A:** Informatie met betrekking tot gepland onderhoud is tijdens een geplande onderhoudsgolf alleen beschikbaar voor de VM's die worden beïnvloed door het geplande onderhoud. Als u geen gegevens ziet, is de onderhoudsgolf mogelijk al voltooid (of niet gestart) of wordt uw vm mogelijk al gehost op een bijgewerkte server.

**V: Is er een manier om precies te weten wanneer mijn VM zal worden beïnvloed?**

**A:** Wanneer we het schema instellen, definiëren we een tijdvenster van meerdere dagen. De exacte volgorde van servers (en virtuele machines) binnen dit venster is onbekend. Als u wilt weten wanneer uw VM's precies worden bijgewerkt, u [geplande gebeurtenissen](../virtual-machines/windows/scheduled-events.md)gebruiken. Wanneer u geplande gebeurtenissen gebruikt, u vanuit de VM een melding van 15 minuten ontvangen voordat een vm opnieuw wordt opgestart.

**V: Hoe lang duurt het voordat u mijn VM opnieuw opstart?**

**A:**  Afhankelijk van de grootte van uw vm kan het opstarten enkele minuten duren tijdens het selfserviceonderhoudsvenster. Tijdens de door Azure gestarte reboots in het geplande onderhoudsvenster duurt het opnieuw opstarten doorgaans ongeveer 25 minuten. Als u Cloud Services (Web/Worker Role), virtuele machineschaalsets of beschikbaarheidssets gebruikt, krijgt u 30 minuten tussen elke groep VM's (updatedomein) tijdens het geplande onderhoudsvenster. 

**V: Ik zie geen onderhoudsinformatie over mijn VM's. Wat ging er mis?**

**A:** Er zijn verschillende redenen waarom u mogelijk geen onderhoudsinformatie op uw VM's ziet:
   - U gebruikt een abonnement dat is gemarkeerd als *Microsoft Internal*.
   - Uw VM's zijn niet gepland voor onderhoud. Het kan zijn dat de onderhoudsgolf is beëindigd, is geannuleerd of is gewijzigd, zodat uw VM's er niet langer door worden beïnvloed.
   - De kolom **Onderhoud** wordt niet toegevoegd aan de weergave VM-lijst. Hoewel we deze kolom aan de standaardweergave hebben toegevoegd, moet u, als u uw weergave configureert om niet-standaardkolommen weer te geven, de kolom **Onderhoud** handmatig toevoegen aan de lijstweergave vm.

**V: Mijn VM is voor de tweede keer gepland voor onderhoud. Waarom?**

**A:** In verschillende gebruiksgevallen wordt uw VM gepland voor onderhoud nadat u uw onderhoud al hebt voltooid en opnieuw hebt geïmplementeerd:
   - We hebben de onderhoudsgolf geannuleerd en opnieuw opgestart met een ander laadvermogen. Het kan zijn dat we een defecte lading hebben ontdekt en we moeten gewoon een extra lading inzetten.
   - Uw VM is door een hardwarefout naar een ander knooppunt *genas.*
   - U hebt ervoor gekozen om de VM te stoppen (detoewijzen) en de VM opnieuw te starten.
   - U hebt **automatische afsluiting** ingeschakeld voor de VM.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het registreren voor onderhoudsgebeurtenissen vanuit de VM met [geplande gebeurtenissen](../virtual-machines/windows/scheduled-events.md).
