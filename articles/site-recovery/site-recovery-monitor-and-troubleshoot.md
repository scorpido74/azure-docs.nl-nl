---
title: Azure-siteherstel controleren | Microsoft Documenten
description: Problemen en bewerkingen met Azure Site Recovery-replicatie met behulp van de portal bewaken en oplossen
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: aa9d776df50306ab1705426c923413b5a5d545a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68717354"
---
# <a name="monitor-site-recovery"></a>Site Recovery bewaken

In dit artikel leest u hoe u Azure [Site Recovery](site-recovery-overview.md)controleren met behulp van ingebouwde monitoring siteherstel.  U controleren:

- De status en status van machines die zijn gerepliceerd door Site Recovery
- Test failover status van machines.
- Problemen en fouten die van invloed zijn op de configuratie en replicatie.
- Infrastructuurcomponenten zoals on-premises servers.


## <a name="before-you-start"></a>Voordat u begint

Misschien wilt u [veelvoorkomende bewakingsvragen](monitoring-common-questions.md) bekijken voordat u begint.

## <a name="monitor-in-the-dashboard"></a>Monitor in het dashboard

1. Klik in de kluis op **Overzicht**. Het dashboard Herstelservices consolideert alle bewakingsgegevens voor de kluis op één locatie. Er zijn pagina's voor zowel Site Recovery als de Azure Backup-service en u schakelen tussen deze pagina's.

    ![Dashboard Siteherstel](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Vanaf het dashboard u inzoomen op verschillende gebieden. 

    ![Dashboard Siteherstel](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. Klik **in Gerepliceerde items**op Alles **weergeven** om alle servers in de kluis te bekijken.
4. Klik op de statusdetails in elke sectie om in te zoomen.
5. Sorteer in **de infrastructuurweergave**de controlegegevens op het type machines dat u nabouwt.

## <a name="monitor-replicated-items"></a>Gerepliceerde items controleren

Controleer **in gerepliceerde items**de status van alle machines in de kluis die replicatie hebben ingeschakeld.

**Staat** | **Details**
--- | ---
Goed | Replicatie vordert normaal. Er worden geen fout- of waarschuwingssymptomen gedetecteerd.
Waarschuwing | Er worden een of meer waarschuwingssymptomen gedetecteerd die van invloed kunnen zijn op replicatie.
Kritiek | Er zijn een of meer kritieke replicatiefoutsymptomen gedetecteerd.<br/><br/> Deze foutsymptomen zijn meestal indicatoren die replicatie vastgelopen, of niet zo snel vordert als de snelheid van de gegevenswijziging.
Niet van toepassing | Servers waarvan momenteel niet wordt verwacht dat ze worden gerepliceerd. Dit kunnen machines zijn die zijn mislukt.

## <a name="monitor-test-failovers"></a>Monitor test failovers

Controleer in **failovertestsucces**de failoverstatus voor machines in de kluis.

- We raden u aan om ten minste eenmaal per zes maanden een testfailover uit te voeren op gerepliceerde machines. Het is een manier om te controleren of failover werkt zoals verwacht, zonder uw productieomgeving te verstoren. 
- Een testfailover wordt pas als geslaagd beschouwd nadat de failover- en failover-opruiming is voltooid.

**Staat** | **Details**
--- | ---
Aanbevolen test | Machines die geen testfailover hebben gehad sinds de beveiliging is ingeschakeld.
Uitgevoerd met succes | Machines met of meer succesvolle testfailovers.
Niet van toepassing | Machines die momenteel niet in aanmerking komen voor een testfailover. Machines die niet zijn mislukt, hebben bijvoorbeeld een eerste replicatie/testfailover/failover in uitvoering.

## <a name="monitor-configuration-issues"></a>Configuratieproblemen controleren

Houd bij **Configuratieproblemen**eventuele problemen bij die van invloed kunnen zijn op uw vermogen om niet meer te mislukken.

- Configuratieproblemen (met uitzondering van de beschikbaarheid van software-updates) worden gedetecteerd door een periodieke validatorbewerking die standaard elke 12 uur wordt uitgevoerd. U de validatorbewerking onmiddellijk laten uitvoeren door op het vernieuwingspictogram naast de **sectiekop Configuratieproblemen** te klikken.
- Klik op de links voor meer informatie. Voor problemen die van invloed zijn op specifieke machines, moet u **op de aandacht van** de doelconfiguraties klikken in de kolom **Doelconfiguraties.** Details zijn onder meer herstelaanbevelingen.

**Staat** | **Details**
--- | ---
Ontbrekende configuraties | Er ontbreekt een noodzakelijke instelling, zoals een herstelnetwerk of een resourcegroep.
Ontbrekende bronnen | Een opgegeven bron kan niet worden gevonden of is niet beschikbaar in het abonnement. De bron is bijvoorbeeld verwijderd of gemigreerd. Gecontroleerde resources omvatten de doelgroep, doel VNet / subnet, log / target storage account, target beschikbaarheid set, target IP-adres.
Abonnementsquotum |  Het beschikbare quotumsaldo voor abonnementsresources wordt vergeleken met het saldo dat nodig is om te mislukken over alle machines in de kluis.<br/><br/> Als er niet genoeg resources zijn, wordt een onvoldoende quotumsaldo gerapporteerd.<br/><br/> Quota zijn monitoring voor VM core count, VM family core count, network interface card (NIC) count.
Software-updates | De beschikbaarheid van nieuwe software-updates en informatie over aflopende softwareversies.


## <a name="monitor-errors"></a>Fouten controleren

Monitor **in het overzicht**van de fout momenteel actieve foutsymptomen die van invloed kunnen zijn op replicatie van servers in de kluis en controleer het aantal getroffen machines.

- Fouten die van invloed zijn op on-premises infrastructuurcomponenten worden weergegeven zijn het begin van de sectie. Bijvoorbeeld het niet ontvangen van een heartbeat van de Azure Site Recovery Provider op de on-premises configuratieserver of Hyper-V-host.
- Vervolgens worden replicatiefoutsymptomen weergegeven die van invloed zijn op gerepliceerde servers.
- De tabelvermeldingen worden gesorteerd op afnemende volgorde van de fouternst en vervolgens door de telvolgorde van de getroffen machines te verlagen.
- Het aantal beïnvloede servers is een handige manier om te begrijpen of één onderliggend probleem van invloed kan zijn op meerdere machines. Een netwerkfout kan bijvoorbeeld gevolgen hebben voor alle machines die naar Azure worden gerepliceerd. 
- Er kunnen meerdere replicatiefouten optreden op één server. In dit geval telt elk foutsymptoom die server in de lijst met de getroffen servers. Nadat het probleem is opgelost, worden replicatieparameters verbeterd en wordt de fout uit de machine verwijderd.

## <a name="monitor-the-infrastructure"></a>Controleer de infrastructuur.

Controleer in **de infrastructuurweergave**de infrastructuuronderdelen die betrokken zijn bij replicatie en de verbindingsstatus tussen servers en de Azure-services.

- Een groene lijn geeft aan dat de verbinding in orde is.
- Een rode lijn met het pictogram met bedekte fouten geeft het bestaan aan van een of meer foutsymptomen die van invloed zijn op de connectiviteit.
-  Plaats de muisaanwijzer op het foutpictogram om de fout en het aantal getroffen entiteiten weer te geven. Klik op het pictogram voor een gefilterde lijst met getroffen entiteiten.

    ![Infrastructuurweergave siteherstel (kluis)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Tips voor het bewaken van de infrastructuur

- Zorg ervoor dat de on-premises infrastructuurcomponenten (configuratieserver, processervers, VMM-servers, Hyper-V-hosts, VMware-machines) de nieuwste versies van de Site Recovery Provider en/of agents uitvoeren.
- Als u alle functies in de infrastructuurweergave wilt gebruiken, moet u [Rollup 22](https://support.microsoft.com/help/4072852) bijwerken voor deze componenten uitvoeren.
- Als u de infrastructuurweergave wilt gebruiken, selecteert u het juiste replicatiescenario in uw omgeving. U inzoomen in de weergave voor meer details. In de volgende tabel ziet u welke scenario's worden weergegeven.

    **Scenario** | **Staat**  | **Bekijk je de foto?**
    --- |--- | ---
    **Replicatie tussen on-premises sites** | Alle staten | Nee 
    **Azure VM-replicatie tussen Azure-gebieden**  | Replicatie ingeschakeld/eerste replicatie in uitvoering | Ja
    **Azure VM-replicatie tussen Azure-gebieden** | Mislukte over/fail back | Nee   
    **Replicatie van VMware naar Azure** | Replicatie ingeschakeld/eerste replicatie in uitvoering | Ja     
    **Replicatie van VMware naar Azure** | Mislukte over/mislukt terug | Nee      
    **Hyper-V-replicatie naar Azure** | Mislukte over/mislukt terug | Nee

- Als u de infrastructuurweergave voor één replicerende machine wilt bekijken, klikt u in het kluismenu op **Gerepliceerde items**en selecteert u een server.  




## <a name="monitor-recovery-plans"></a>Herstelplannen controleren

In **herstelplannen**controleert u het aantal plannen, maakt u nieuwe plannen en wijzigt u bestaande plannen.  

## <a name="monitor-jobs"></a>Taken controleren

Controleer in **Taken**de status van siteherstelbewerkingen.

- De meeste bewerkingen in Azure Site Recovery worden asynchroon uitgevoerd, waarbij een trackingtaak wordt gemaakt en gebruikt om de voortgang van de bewerking bij te houden. 
- Het taakobject heeft alle informatie die u nodig hebt om de status en de voortgang van de bewerking bij te houden. 

Taken als volgt controleren:

1. In de sectie dashboard > **Vacatures** ziet u een overzicht van taken die in de afgelopen 24 uur zijn voltooid, aan de gang zijn of wachten op invoer. U op elke staat klikken om meer informatie te krijgen over de relevante vacatures.
2. Klik **op Alles weergeven** om alle taken van de afgelopen 24 uur te bekijken.

    > [!NOTE]
    > U ook toegang krijgen tot taakgegevens in het kluismenu > **Site Recovery Jobs.** 

2. In de lijst **Met taken voor siteherstel** wordt een lijst met taken weergegeven. In het bovenste menu u foutgegevens voor een specifieke taak krijgen, de takenlijst filteren op basis van specifieke criteria en geselecteerde taakgegevens exporteren naar Excel.
3. U inzoomen op een taak door erop te klikken. 

## <a name="monitor-virtual-machines"></a>Virtuele machines bewaken

Ontvang **in gerepliceerde items**een lijst met gerepliceerde machines. 
    ![Lijstweergave van gerepliceerde items siteherstel](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. U informatie bekijken en filteren. In het actiemenu bovenaan u acties uitvoeren voor een bepaalde machine, waaronder het uitvoeren van een testfailover of het weergeven van specifieke fouten.
3. Klik **op Kolommen** om extra kolommen weer te geven, bijvoorbeeld om RPO, doelconfiguratieproblemen en replicatiefouten weer te geven.
4. Klik **op Filteren** om informatie weer te geven op basis van specifieke parameters, zoals replicatiestatus of een bepaald replicatiebeleid.
5. Klik met de rechtermuisknop op een machine om bewerkingen zoals testfailover ervoor te starten of om specifieke foutgegevens weer te geven die eraan zijn gekoppeld.
6. Klik op een machine om in te zoomen op meer details. Details zijn onder meer:
   - **Replicatiegegevens:** huidige status en status van de machine.
   - **RPO** (doel herstelpunt): Huidige RPO voor de virtuele machine en het tijdstip waarop de RPO voor het laatst is berekend.
   - **Herstelpunten**: Laatst beschikbare herstelpunten voor de machine.
   - **Failovergereed:** geeft aan of er een testfailover is uitgevoerd voor de machine, de agentversie die op de machine wordt uitgevoerd (voor machines die de Mobiliteitsservice uitvoeren) en eventuele configuratieproblemen.
   - **Fouten:** Lijst van replicatiefoutsymptomen die momenteel op de machine worden waargenomen en mogelijke oorzaken/acties.
   - **Gebeurtenissen**: Een chronologische lijst van recente gebeurtenissen die van invloed zijn op de machine. Foutgegevens tonen de momenteel waarneembare foutsymptomen, terwijl gebeurtenissen een historisch record is van problemen die van invloed zijn geweest op de machine.
   - **Infrastructuurweergave:** geeft de status van de infrastructuur voor het scenario weer wanneer machines worden gerepliceerd naar Azure.

     ![Gerepliceerde itemdetails/overzicht van siteherstel](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Abonneren op e-mailmeldingen

U zich abonneren om e-mailmeldingen te ontvangen voor deze kritieke gebeurtenissen:
 
- Kritieke toestand voor gerepliceerde machine.
- Geen connectiviteit tussen de on-premises infrastructuurcomponenten en de Site Recovery-service. Connectiviteit tussen Site Recovery en on-premises servers die in een kluis zijn geregistreerd, wordt gedetecteerd met behulp van een heartbeatmechanisme.
- Failoverfouten.

Abonneer je als volgt:

Klik in de sectie Vault > **Monitoring** op **Siteherstelgebeurtenissen**.
1. Klik op **E-mailmeldingen**.
1. Schakel in **e-mailmelding**meldingen in en geef aan naar wie u moet verzenden. U sturen naar alle abonnement beheerders worden verzonden meldingen, en optioneel specifieke e-mailadressen.

    ![E-mailmeldingen](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](monitor-log-analytics.md) het bewaken van siteherstel met Azure Monitor.
