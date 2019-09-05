---
title: Failover tijdens nood herstel met Azure Site Recovery | Microsoft Docs
description: Meer informatie over het mislukken van Vm's en fysieke servers tijdens herstel na nood gevallen met de Azure Site Recovery-service.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/30/2019
ms.author: raynew
ms.openlocfilehash: da55d83665792f6ea2f4c78aa2a6c3ca26c39233
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383192"
---
# <a name="fail-over-vms-and-physical-servers"></a>Failover van Vm's en fysieke servers 

In dit artikel wordt beschreven hoe u virtuele machines en fysieke servers die worden beveiligd door Site Recovery failover.

## <a name="prerequisites"></a>Vereisten
1. Alvorens een failover uit te voeren, voert u [een testfailover uit om te](site-recovery-test-failover-to-azure.md) controleren of alles werkt zoals verwacht.
1. [Bereid het netwerk](site-recovery-network-design.md) voor op de doel locatie voordat u een failover doet.  

Gebruik de volgende tabel om te weten te zijn over de failover-opties van Azure Site Recovery. Deze opties worden ook weer gegeven voor verschillende failover-scenario's.

| Scenario | Toepassings herstel vereiste | Werk stroom voor Hyper-V | Werk stroom voor VMware
|---|--|--|--|
|Geplande failover vanwege een aanstaande downtime van het Data Center| Geen gegevens verlies voor de toepassing wanneer een geplande activiteit wordt uitgevoerd| Voor Hyper-V repliceert ASR gegevens met een Kopieer frequentie die is opgegeven door de gebruiker. Geplande failover wordt gebruikt om de frequentie te overschrijven en de laatste wijzigingen te repliceren voordat een failover wordt gestart. <br/> <br/> 1. Plan een onderhouds venster conform het wijzigings beheer proces van uw bedrijf. <br/><br/> 2. Gebruikers op de hoogte stellen van aanstaande downtime. <br/><br/> 3. De gebruiker gerichte toepassing offline halen.<br/><br/>4. Geplande failover initiëren met behulp van de ASR-Portal. De on-premises virtuele machine wordt automatisch afgesloten.<br/><br/>Effectief verlies van toepassings gegevens = 0 <br/><br/>Een logboek met herstel punten is ook opgenomen in een Bewaar periode voor een gebruiker die een ouder herstel punt wil gebruiken. (24 uur bewaren voor Hyper-V). Als replicatie voorbij het tijds bestek van het retentie venster is gestopt, kunnen klanten nog steeds failover gebruiken met de meest recente beschik bare herstel punten. | Voor VMware repliceert ASR gegevens voortdurend via CDP. Failover geeft de gebruiker de mogelijkheid om failover uit te scha kelen naar de meest recente gegevens (waaronder het afsluiten van post toepassingen)<br/><br/> 1. Een onderhouds venster plannen volgens het wijzigings beheerproces <br/><br/>2. gebruikers op de hoogte stellen van aanstaande downtime <br/><br/>3. De gebruiker gerichte toepassing offline halen.<br/><br/>4. Start een geplande failover via de ASR-Portal naar het laatste punt nadat de toepassing offline is. Gebruik de optie ' geplande failover ' op de portal en selecteer het laatste punt voor failover. De on-premises virtuele machine wordt automatisch afgesloten.<br/><br/>Effectief verlies van toepassings gegevens = 0 <br/><br/>Een logboek met herstel punten in een Bewaar venster is voorzien van een klant die een ouder herstel punt wil gebruiken. (72 uur van bewaren voor VMware). Als replicatie voorbij het tijds bestek van het retentie venster is gestopt, kunnen klanten nog steeds failover gebruiken met de meest recente beschik bare herstel punten.
|Failover vanwege een ongeplande downtime van het Data Center (natuurlijke of nood ramp) | Mini maal verlies van gegevens voor de toepassing | 1. Het BCP-abonnement van de organisatie initiëren <br/><br/>2. Start de niet-geplande failover via de ASR-Portal naar het meest recente of een punt vanuit het retentie venster (logboek).| 1. Start het BCP-abonnement van de organisatie. <br/><br/>2. Start de niet-geplande failover via de ASR-Portal naar het meest recente of een punt vanuit het retentie venster (logboek).


## <a name="run-a-failover"></a>Een failover uitvoeren
In deze procedure wordt beschreven hoe u een failover uitvoert voor een [herstel plan](site-recovery-create-recovery-plans.md). U kunt ook de failover voor één virtuele machine of fysieke server uitvoeren op de pagina **gerepliceerde items** , zoals [hier](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure)wordt beschreven.


![Failover](./media/site-recovery-failover/Failover.png)

1. Selecteer **herstel plannen** > *recoveryplan_name*. Klik op **failover**
2. Selecteer op het scherm **failover** een **herstel punt** voor failover naar. U kunt een van de volgende opties gebruiken:
   1. **Laatste**: Met deze optie wordt de taak gestart door eerst alle gegevens te verwerken die naar Site Recovery service zijn verzonden. Het verwerken van de gegevens maakt een herstel punt voor elke virtuele machine. Dit herstel punt wordt gebruikt door de virtuele machine tijdens een failover. Deze optie biedt de laagste RPO (beoogd herstel punt) als de virtuele machine die is gemaakt na een failover, alle gegevens die zijn gerepliceerd naar Site Recovery service wanneer de failover werd geactiveerd.
   1. **Laatst verwerkt**: Met deze optie wordt een failover uitgevoerd van alle virtuele machines van het herstel plan naar het laatste herstel punt dat al door Site Recovery service is verwerkt. Wanneer u een testfailover uitvoert voor een virtuele machine, wordt er ook een tijds tempel van het meest recente verwerkte herstel punt weer gegeven. Als u een failover uitvoert van een herstel plan, gaat u naar de afzonderlijke virtuele machine en bekijkt u de **meest recente tegel herstel punten** om deze informatie op te halen. Als er geen tijd wordt besteed aan het verwerken van de niet-verwerkte gegevens, biedt deze optie een lage RTO (Recovery Time objectief)-failover.
   1. **Laatste toepassingsconsistente punt**: Met deze optie wordt een failover uitgevoerd van alle virtuele machines van het herstel plan naar het laatste toepassings consistente herstel punt dat al door Site Recovery service is verwerkt. Wanneer u een testfailover uitvoert voor een virtuele machine, wordt er ook een tijds tempel van het meest recente toepassings consistente herstel punt weer gegeven. Als u een failover uitvoert van een herstel plan, gaat u naar de afzonderlijke virtuele machine en bekijkt u de **meest recente tegel herstel punten** om deze informatie op te halen.
   1. **Laatste verwerkte multi-VM**: Deze optie is alleen beschikbaar voor herstel plannen waarvoor ten minste één virtuele machine met consistentie van meerdere VM'S is ingeschakeld. Virtuele machines die deel uitmaken van een failover van een replicatie groep naar het meest recente, veelvoorkomende herstel punt met meerdere VM'S. Andere failover van virtuele machines naar het meest recente verwerkte herstel punt.  
   1. **Nieuwste multi-VM-app-consistent**: Deze optie is alleen beschikbaar voor herstel plannen waarvoor ten minste één virtuele machine met consistentie van meerdere VM'S is ingeschakeld. Virtuele machines die deel uitmaken van een failover van een replicatie groep naar het meest recente, algemene multi-VM-toepassings consistente herstel punt. Andere failover van virtuele machines naar het meest recente toepassings consistente herstel punt.
   1. **Aangepast**: Als u een testfailover uitvoert voor een virtuele machine, kunt u deze optie gebruiken om een failover uit te voeren naar een bepaald herstel punt.

      > [!NOTE]
      > De optie voor het kiezen van een herstel punt is alleen beschikbaar wanneer u een failover naar Azure voordoet.
      >
      >


1. Als sommige virtuele machines in het herstel plan een failover hebben uitgevoerd in een vorige uitvoering en nu de virtuele machines actief zijn op de bron-en doel locatie, kunt u de optie voor het wijzigen van de **richting** gebruiken om te bepalen in welke richting de failover moet plaatsvinden.
1. Als er een failover wordt uitgevoerd naar Azure en gegevens versleuteling is ingeschakeld voor de Cloud (alleen van toepassing als u virtuele Hyper-v-machines van een VMM-server hebt beveiligd), selecteert u in **versleutelings sleutel** het certificaat dat is uitgegeven tijdens het inschakelen van gegevens versleuteling tijdens Setup op de VMM-server.
1. Selecteer **afsluit machine voordat u de failover start** als u wilt dat site Recovery probeert om de virtuele bron machines af te sluiten voordat de failover wordt geactiveerd. Failover wordt voortgezet, zelfs als het afsluiten mislukt.  

    > [!NOTE]
    > Als virtuele Hyper-v-machines zijn beveiligd, wordt de optie voor afsluiten ook geprobeerd om de on-premises gegevens te synchroniseren die nog niet naar de service zijn verzonden voordat de failover wordt geactiveerd.
    >
    >

1. U kunt de voortgang van de failover volgen op de pagina **Taken**. Zelfs als er fouten optreden tijdens een niet-geplande failover, wordt het herstel plan uitgevoerd totdat het is voltooid.
1. Controleer na de failover de virtuele machine door u aan te melden. Als u wilt overschakelen naar een ander herstel punt van de virtuele machine, kunt u de optie **herstel punt wijzigen** gebruiken.
1. Wanneer u tevreden bent met de virtuele machine waarvoor u de failover hebt uitgevoerd, kunt u de failover **Doorvoeren**. **Door voeren verwijdert alle herstel punten die beschikbaar zijn bij de service en de** optie **herstel punt wijzigen** is niet meer beschikbaar.

## <a name="planned-failover"></a>Geplande failover
Virtuele machines/fysieke servers die zijn beveiligd met Site Recovery ondersteunen ook **geplande failover**. Geplande failover is een failover-optie van een gegevens verlies van nul. Wanneer een geplande failover wordt geactiveerd, worden de virtuele bron machines afgesloten. de meest recente gegevens worden gesynchroniseerd en vervolgens wordt een failover geactiveerd.

> [!NOTE]
> Tijdens de failover van virtuele Hyper-v-machines van een on-premises site naar een andere on-premises site, moet u eerst de virtuele machine opnieuw **repliceren** naar een primaire site en vervolgens een failover activeren om terug te gaan naar de primaire on-premises site. Als de primaire virtuele machine niet beschikbaar is, moet u voordat u begint te **repliceren** , de virtuele machine terugzetten vanuit een back-up.   
 
 
## <a name="failover-job"></a>Failover-taak

![Failover](./media/site-recovery-failover/FailoverJob.png)

Wanneer een failover wordt geactiveerd, moet u de volgende stappen uitvoeren:

1. Controle op vereisten: Deze stap zorgt ervoor dat aan alle voor waarden die zijn vereist voor de failover wordt voldaan
1. Cluster Met deze stap worden de gegevens verwerkt en zo gereed dat een virtuele machine van Azure kan worden gemaakt. Als u het **meest recente** herstel punt hebt gekozen, wordt met deze stap een herstel punt gemaakt op basis van de gegevens die naar de service zijn verzonden.
1. Starten: Met deze stap maakt u een virtuele Azure-machine met behulp van de gegevens die in de vorige stap zijn verwerkt.

> [!WARNING]
> **Failover van een in voortgang niet annuleren**: Voordat de failover wordt gestart, wordt de replicatie voor de virtuele machine gestopt. Als u een taak die wordt uitgevoerd **annuleert** , wordt de failover gestopt, maar de virtuele machine begint niet te repliceren. De replicatie kan niet opnieuw worden gestart.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Benodigde tijd voor failover naar Azure

In bepaalde gevallen vereist een failover van virtuele machines een extra tussenliggende stap die doorgaans ongeveer 8 tot 10 minuten duurt. In de volgende gevallen is de tijd die nodig is voor failover hoger dan gebruikelijk:

* Virtuele VMware-machines die gebruikmaken van Mobility service van versie ouder dan 9,8
* Fysieke servers
* Virtuele VMware Linux-machines
* Virtuele Hyper-V-machines die worden beveiligd als fysieke servers
* Virtuele VMware-machines waarbij de volgende Stuur Programma's niet aanwezig zijn als opstart Stuur Programma's
    * storvsc
    * vmbus
    * storflt
    * Intelide
    * ATAPI
* Virtuele VMware-machines waarvoor geen DHCP-service is ingeschakeld, ongeacht of ze DHCP of statische IP-adressen gebruiken

In alle andere gevallen is deze tussenliggende stap niet vereist en is de tijd die nodig is voor de failover lager.

## <a name="using-scripts-in-failover"></a>Scripts gebruiken in failover
Mogelijk wilt u bepaalde acties automatiseren tijdens het uitvoeren van een failover. U kunt met behulp van scripts of [Azure Automation-runbooks](site-recovery-runbook-automation.md) in [herstel plannen](site-recovery-create-recovery-plans.md) .

## <a name="post-failover-considerations"></a>Overwegingen na failover
Na een failover kunt u de volgende aanbevelingen overwegen:
### <a name="retaining-drive-letter-after-failover"></a>Stationsletter behouden na failover
Azure Site Recovery wordt het bewaren van stationsletters afhandelen. [Meer](vmware-azure-exclude-disk.md#example-1-exclude-the-sql-server-tempdb-disk) informatie over hoe dit gebeurt wanneer u ervoor kiest om bepaalde schijven uit te sluiten.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Voorbereiden op het verbinden met virtuele Azure-machines na een failover

Als u na een failover verbinding wilt maken met virtuele Azure-machines met behulp van RDP/SSH, volgt u de procedure die [hier](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) wordt beschreven.

Volg de stappen die [hier](site-recovery-failover-to-azure-troubleshoot.md) worden beschreven om eventuele verbindingsproblemen na een failover op te lossen.


## <a name="next-steps"></a>Volgende stappen

> [!WARNING]
> Als er een failover van de virtuele machines is uitgevoerd en het on-premises Data Center beschikbaar is, moet u de virtuele VMware-machines opnieuw [**beveiligen**](vmware-azure-reprotect.md) in het on-premises Data Center.

Gebruik de optie [**geplande failover**](hyper-v-azure-failback.md) om virtuele Hyper-v-machines **terug te sturen** naar on-premises van Azure.

Als u failover hebt uitgevoerd voor een virtuele Hyper-v-machine naar een ander on-premises Data Center dat wordt beheerd door een VMM-server en het primaire Data Center beschikbaar is, gebruikt u de optie **omgekeerde replicatie** om de replicatie terug te starten naar het primaire Data Center.
