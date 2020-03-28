---
title: Zelfstudie - Configuratie van de virtuele machine van Windows beheren in Azure
description: In deze zelfstudie leert u hoe u wijzigingen identificeren en pakketupdates op een virtuele Windows-machine beheren
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d97323f1916ee46e6b1f8d4ca8723b950baca39c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238578"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Zelfstudie: Wijzigingen controleren en een virtuele Windows-machine bijwerken in Azure

Met Azure [Change Tracking and](../../automation/change-tracking.md) Update [Management](../../automation/automation-update-management.md)u eenvoudig wijzigingen in uw Virtuele Windows-machines in Azure identificeren en updates van het besturingssysteem voor die VM's beheren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Windows-updates beheren.
> * Controleer wijzigingen en voorraad.

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell openen

Azure Cloud Shell is een gratis interactieve shell die u gebruiken om de stappen in dit artikel uit te voeren. Er zijn algemene Azure-hulpprogramma's die vooraf zijn geïnstalleerd en geconfigureerd om te gebruiken met uw Azure-account.

Als u een codeblok in Cloud Shell wilt openen, selecteert u **Probeer het** uit de rechterbovenhoek van dat codeblok.

U Cloud Shell ook openen op [https://shell.azure.com/powershell](https://shell.azure.com/powershell)een apart browsertabblad door naar. Selecteer **Kopiëren** om codeblokken te kopiëren, plak ze op het tabblad Cloud Shell en selecteer de toets Enter om de code uit te voeren.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Voor het configureren van Azure-bewaking en updatebeheer in deze zelfstudie hebt u een Windows-VM in Azure nodig.

Stel eerst een beheerdersnaam en -wachtwoord in voor de virtuele machine met [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Maak vervolgens de VM met [Nieuw-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). In het volgende voorbeeld `myVM` wordt `East US` een VM met de naam op de locatie genaamerd. Als deze nog niet bestaan, `myResourceGroupMonitor` worden de brongroep en de ondersteunende netwerkbronnen gemaakt:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Het duurt enkele minuten voordat de bronnen en virtuele machine zijn gemaakt.

## <a name="manage-windows-updates"></a>Windows-updates beheren

Updatebeheer helpt u bij het beheren van updates en patches voor uw Azure Windows VM's. Rechtstreeks vanuit uw VM u snel:

- De status van beschikbare updates evalueren.
- De installatie van vereiste updates plannen.
- Controleer de implementatieresultaten om te controleren of updates zijn toegepast op de vm.

Zie [Automatiseringsprijzen voor Updatebeheer voor](https://azure.microsoft.com/pricing/details/automation/)prijsinformatie .

### <a name="enable-update-management"></a>Updatebeheer inschakelen

Updatebeheer inschakelen voor uw vm:

1. Selecteer **virtuele machines**aan de linkerkant van het venster .
1. Kies een VM in de lijst.
1. Selecteer **Beheer bijwerken**in het deelvenster **Bewerkingen** van het VM-venster .
1. Het venster **Updatebeheer inschakelen** wordt geopend.

Validatie wordt uitgevoerd om te bepalen of Updatebeheer is ingeschakeld voor deze vm. Validatie omvat controles voor een Log Analytics-werkruimte, voor een gekoppeld automatiseringsaccount en voor de vraag of de oplossing zich in de werkruimte bevindt.

U gebruikt een [Log Analytics-werkruimte](../../log-analytics/log-analytics-overview.md) om gegevens te verzamelen die worden gegenereerd door functies en services zoals Updatebeheer. De werkruimte biedt één locatie om gegevens uit meerdere bronnen te bekijken en te analyseren.

Als u extra acties wilt uitvoeren op VM's waarvoor updates nodig zijn, u Azure Automation gebruiken om boeken tegen VM's uit te voeren. Dergelijke acties omvatten het downloaden of toepassen van updates.

Het validatieproces controleert ook of de VM is ingericht met de Microsoft Monitoring Agent (MMA) en Automation Hybrid Runbook Worker. U gebruikt de agent om met de VM te communiceren en informatie te verkrijgen over de updatestatus.

Kies in het venster **Updatebeheer inschakelen** de werkruimte en automatiseringsaccount Log Analytics en **selecteer**inschakelen . De oplossing duurt maximaal 15 minuten om ingeschakeld te worden.

Een van de volgende vereisten die ontbreken tijdens het instappen worden automatisch toegevoegd:

* [Werkruimte Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automatisering](../../automation/automation-offering-get-started.md)
* Een [hybride runbook-werknemer](../../automation/automation-hybrid-runbook-worker.md), die is ingeschakeld op de VM

Nadat de oplossing is ingeschakeld, wordt het **beheervenster Bijwerken** geopend. Configureer de locatie, de werkruimte Log Analytics en het automatiseringsaccount om te gebruiken en selecteer **inschakelen**. Als deze opties gedimd lijken, is een andere automatiseringsoplossing ingeschakeld voor de VM en moet het werkruimte- en automatiseringsaccount van die oplossing worden gebruikt.

![Updatebeheeroplossing inschakelen](./media/tutorial-monitoring/manageupdates-update-enable.png)

Het kan tot 15 minuten duren voordat de oplossing Voor updatebeheer is ingeschakeld. Sluit gedurende deze tijd het browservenster niet. Nadat de oplossing is ingeschakeld, wordt informatie over ontbrekende updates op de VM naar Azure Monitor-logboeken verzonden. Het kan 30 minuten tot 6 uur duren voordat de gegevens beschikbaar zijn voor analyse.

### <a name="view-an-update-assessment"></a>Een update-evaluatie bekijken

Nadat Updatebeheer is ingeschakeld, wordt het **beheervenster Bijwerken** weergegeven. Nadat de evaluatie van updates is voltooid, ziet u een lijst met ontbrekende updates op het tabblad **Ontbrekende updates.**

 ![Updatestatus bekijken](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Als u updates wilt installeren, plant u een implementatie na uw release-planning en servicevenster. U kiest welke updatetypen u wilt opnemen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

Als u een nieuwe update-implementatie voor de VM wilt plannen, selecteert u **Update-implementatie plannen** boven aan het **updatebeheervenster.** Geef in het venster **Nieuwe update-implementatie** de volgende gegevens op:

| Optie | Beschrijving |
| --- | --- |
| **Naam** |Voer een unieke naam in om de implementatie van de update te identificeren. |
|**Besturingssysteem**| Selecteer **Linux** of **Windows**.|
| **Groepen die moeten worden bijgewerkt** |Voor VM's die op Azure worden gehost, definieert u een query op basis van een combinatie van abonnementen, brongroepen, locaties en tags. Met deze query wordt een dynamische groep vm's met Azure-gehoste groep gebouwd die in uw implementatie worden opgenomen. </br></br>Selecteer een bestaande opgeslagen zoekopdracht voor VM's die niet op Azure worden gehost. Met deze zoekopdracht u een groep van deze VM's selecteren die u in de implementatie wilt opnemen. </br></br> Zie [Dynamische groepen voor](../../automation/automation-update-management-groups.md)meer informatie.|
| **Machines om bij te werken** |Selecteer **Opgeslagen zoeken**, **Geïmporteerde groep**of **Machines**.<br/><br/>Als u **Machines**selecteert, u afzonderlijke machines kiezen in de vervolgkeuzelijst. De gereedheid van elke machine wordt weergegeven in de kolom **UPDATE AGENT READINESS van** de tabel.</br></br> Zie [Computergroepen in Azure Monitorlogboeken](../../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken |
|**Classificaties bijwerken**|Kies alle benodigde updateclassificaties.|
|**Updates opnemen/uitsluiten**|Selecteer deze optie om het deelvenster **Opnemen/uitsluiten te** openen. Updates die moeten worden opgenomen en die moeten worden uitgesloten, staan op afzonderlijke tabbladen. Zie [Een update-implementatie plannen voor](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment)meer informatie over hoe met opname wordt omgegaan. |
|**Instellingen plannen**|Kies het tijdstip om te starten en selecteer **Eenmaal** of **Terugkerend**.|
| **Pre-scripts + Postscripts**|Kies de scripts die voor en na uw implementatie moeten worden uitgevoerd.|
| **Onderhoudsvenster** | Voer het aantal minuten in dat is ingesteld voor updates. Geldige waarden variëren van 30 tot 360 minuten. |
| **Besturingselement opnieuw opstarten**| Selecteer hoe reboots worden verwerkt. Beschikbare selecties zijn:<ul><li>**Reboot indien nodig**</li><li>**Altijd opnieuw opstarten**</li><li>**Nooit opnieuw opstarten**</li><li>**Alleen opnieuw opstarten**</li></ul>**Indien nodig opnieuw opstarten** is de standaardselectie. Als u **Alleen opnieuw opstarten**selecteert, worden updates niet geïnstalleerd.|

Nadat u klaar bent met het configureren van de planning, klikt u op **Maken** om terug te keren naar het statusdashboard. In **de tabel Gepland** wordt het implementatieschema weergegeven dat u hebt gemaakt.

U ook programmatisch update-implementaties maken. Zie [Software-updateconfiguraties maken voor](/rest/api/automation/softwareupdateconfigurations/create)meer informatie over het maken van een update-implementatie met de REST API. Er is ook een voorbeeld van runbook die u gebruiken om een wekelijkse update-implementatie te maken. Zie [Een wekelijkse update-implementatie maken voor een of meer VM's in een resourcegroep](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)voor meer informatie over dit runbook.

### <a name="view-results-of-an-update-deployment"></a>Resultaten van een update-implementatie weergeven

Nadat de geplande implementatie is gestart, u de implementatiestatus zien op het tabblad **Implementaties bijwerken** van het **venster Beheer bijwerken.**

Als de implementatie momenteel wordt uitgevoerd, wordt de status 'In uitvoering' weergegeven. Na een succesvolle voltooiing verandert de status in 'Geslaagd'. Maar als updates in de implementatie mislukken, is de status 'Gedeeltelijk mislukt'.

Selecteer de voltooide update-implementatie om het dashboard voor die implementatie te bekijken.

![Statusdashboard voor update-implementatie voor specifieke implementatie](./media/tutorial-monitoring/manageupdates-view-results.png)

De tegel **Resultaten bijwerken** toont een overzicht van het totale aantal updates en implementatieresultaten op de VM. De tabel aan de rechterkant toont een gedetailleerde uitsplitsing van elke update en de installatieresultaten. Elk resultaat heeft een van de volgende waarden:

* **Niet geprobeerd**: de update is niet geïnstalleerd. Er was niet genoeg tijd beschikbaar op basis van de gedefinieerde onderhoudsvensterduur.
* **Geslaagd**: De update is geslaagd.
* **Mislukt:** de update is mislukt.

Selecteer **Alle logboeken** als u alle logboekvermeldingen wilt zien die tijdens de implementatie zijn gemaakt.

Selecteer de tegel **Uitvoer** om de taakstroom van de runbook te zien die verantwoordelijk is voor het beheren van de update-implementatie op de doel-VM.

Selecteer **Fouten** om gedetailleerde informatie over eventuele implementatiefouten te bekijken.

## <a name="monitor-changes-and-inventory"></a>Wijzigingen en inventaris bewaken

U een inventaris van de software, bestanden, Linux-daemons, Windows-services en Windows-registersleutels op uw computers verzamelen en bekijken. Het bijhouden van de configuraties van uw machines helpt u operationele problemen in uw omgeving te lokaliseren en de toestand van uw machines beter te begrijpen.

### <a name="enable-change-and-inventory-management"></a>Wijzigings- en voorraadbeheer inschakelen

Ga als het gaat om wijzigings- en voorraadbeheer voor uw vm:

1. Selecteer **virtuele machines**aan de linkerkant van het venster .
1. Kies een VM in de lijst.
1. Selecteer **onder Bewerkingen** in het VM-venster **voorraad** of **Bijhouden wijzigen**.
1. Het deelvenster **Wijzigingstracking en -voorraad inschakelen** wordt geopend.

Configureer de locatie, de werkruimte Log Analytics en het automatiseringsaccount om te gebruiken en selecteer **Inschakelen.** Als de opties gedimd lijken, is er al een automatiseringsoplossing ingeschakeld voor de VM. In dat geval moet het reeds ingeschakelde werkruimte- en automatiseringsaccount worden gebruikt.

Hoewel de oplossingen afzonderlijk in het menu worden weergegeven, zijn ze dezelfde oplossing. Als u er één inschakelt, worden beide ingeschakeld voor de VM.

![Bijhouden van wijzigingen en inventaris inschakelen](./media/tutorial-monitoring/manage-inventory-enable.png)

Nadat de oplossing is ingeschakeld, kan het enige tijd duren voordat de voorraad op de vm is verzameld voordat gegevens worden weergegeven.

### <a name="track-changes"></a>Wijzigingen bijhouden

Selecteer Bij uw VM onder **OPERATIONS**De optie **Bijhouden wijzigen** en selecteer **Vervolgens Instellingen bewerken**. Het deelvenster **Bijhouden wijzigen** wordt geopend. Selecteer het type instelling dat u wilt bijhouden en selecteer **+ Toevoegen** om de instellingen te configureren.

De beschikbare instellingen voor Windows zijn:

* Windows-register
* Windows-bestanden

Zie Wijzigingen op een vm oplossen voor gedetailleerde informatie over het bijhouden van [wijzigingen.](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Inventaris weergeven

Selecteer op uw VM **Voorraad** onder **BEWERKINGEN**. Op het tabblad **Software** staat een tabel met de gevonden software. De details op hoog niveau voor elke softwarerecord worden in de tabel weergegeven. Deze details omvatten de naam van de software, versie, uitgever en de laatste vernieuwde tijd.

![Inventaris weergeven](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Activiteitslogboeken en wijzigingen controleren

Selecteer **activiteitslogboekverbinding beheren** om het **logboekvenster voor Azure-activiteit** te openen in het **venster Venster Bijhouden** van wijzigingen op uw virtuele machinegroep. Selecteer **Verbinding maken** om Wijzigingstracking te verbinden met het Azure-activiteitenlogboek voor uw vm.

Nadat Wijzigingstracking is ingeschakeld, gaat u naar het **deelvenster Overzicht** voor uw vm en selecteert u **Stoppen** om uw vm te stoppen. Wanneer u daarom wordt gevraagd, selecteert u **Ja** om de virtuele machine te stoppen. Nadat de vm is toegewezen, selecteert u **Start** om uw VM opnieuw te starten.

Als u een VM stopt en opnieuw start, wordt een gebeurtenis in het activiteitenlogboek gestopt en opnieuw gestart. Ga terug naar het **venster Bijhouden wijzigen** en selecteer het tabblad **Gebeurtenissen** onder aan het deelvenster. Na een tijdje worden de gebeurtenissen weergegeven in de grafiek en de tabel. U elke gebeurtenis selecteren om gedetailleerde informatie voor die gebeurtenis weer te geven.

![Wijzigingen in het activiteitenlogboek weergeven](./media/tutorial-monitoring/manage-activitylog-view-results.png)

De vorige grafiek toont wijzigingen die zich in de loop van de tijd hebben voorgedaan. Nadat u een Azure Activity Log-verbinding hebt toegevoegd, worden in de lijngrafiek bovenaan Azure Activity Log-gebeurtenissen weergegeven.

Elke rij staafgrafieken vertegenwoordigt een ander traceerbaar wijzigingstype. Deze typen zijn Linux-daemons, bestanden, Windows-registersleutels, software en Windows-services. Op het tabblad **Wijzigen** worden de wijzigingsgegevens weergegeven. Wijzigingen worden weergegeven in de volgorde van wanneer elke plaats heeft gevonden, waarbij de meest recente wijziging als eerste wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Wijzigingstracking en Updatebeheer voor uw vm geconfigureerd en beoordeeld. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Maak een resourcegroep en VM.
> * Windows-updates beheren.
> * Controleer wijzigingen en voorraad.

Ga naar de volgende zelfstudie voor meer informatie over het bewaken van uw VM.

> [!div class="nextstepaction"]
> [Virtuele machines bewaken](tutorial-monitor.md)
