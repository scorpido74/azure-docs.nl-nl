---
title: Zelf studie-configuratie van virtuele Windows-machines beheren in azure
description: In deze zelf studie leert u hoe u wijzigingen kunt identificeren en pakket updates kunt beheren op een virtuele Windows-machine
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ed36dc669c8b89ba4a2b7831c6eb6f8742e73730
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100410"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Zelf studie: wijzigingen bewaken en een virtuele Windows-machine bijwerken in azure

Met Azure [Wijzigingen bijhouden](../../automation/change-tracking.md) en [updatebeheer](../../automation/automation-update-management.md)kunt u eenvoudig wijzigingen in uw virtuele Windows-machines in azure identificeren en updates van besturings systemen voor deze vm's beheren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Windows-updates beheren.
> * Wijzigingen en inventaris bewaken.

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell openen

Azure Cloud Shell is een gratis interactieve shell die u kunt gebruiken om de stappen in dit artikel uit te voeren. Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd en geconfigureerd voor gebruik met uw Azure-account.

Als u een code blok in Cloud Shell wilt openen, selecteert u **Dit** in de rechter bovenhoek van het code blok.

U kunt Cloud Shell ook openen in een afzonderlijk browser tabblad door naar te [https://shell.azure.com/powershell](https://shell.azure.com/powershell)gaan. Selecteer **kopiëren** om code blokken te kopiëren, plak ze op het tabblad Cloud shell en selecteer de toets ENTER om de code uit te voeren.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Voor het configureren van Azure-bewaking en updatebeheer in deze zelfstudie hebt u een Windows-VM in Azure nodig.

Stel eerst een beheerdersnaam en -wachtwoord in voor de virtuele machine met [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Maak vervolgens de virtuele machine met [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). In het volgende voor beeld wordt een `myVM` VM gemaakt `East US` met de naam op de locatie. Als ze nog niet bestaan, worden de resource `myResourceGroupMonitor` groep en de ondersteunende netwerk bronnen gemaakt:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Het duurt enkele minuten voordat de bronnen en virtuele machine zijn gemaakt.

## <a name="manage-windows-updates"></a>Windows-updates beheren

Updatebeheer helpt u bij het beheren van updates en patches voor uw Azure Windows-Vm's. Rechtstreeks vanuit uw virtuele machine kunt u snel het volgende doen:

- De status van beschikbare updates evalueren.
- De installatie van vereiste updates plannen.
- Bekijk de implementatie resultaten om te controleren of de updates zijn toegepast op de VM.

Zie [Automation-prijzen voor update beheer](https://azure.microsoft.com/pricing/details/automation/)voor informatie over prijzen.

### <a name="enable-update-management"></a>Updatebeheer inschakelen

Updatebeheer voor uw virtuele machine inschakelen:

1. Selecteer **virtuele machines**aan de linkerkant van het venster.
1. Kies een virtuele machine in de lijst.
1. Selecteer in het deel venster **bewerkingen** van het VM-venster **Update beheer**.
1. Het venster **updatebeheer inschakelen** wordt geopend.

Er wordt een validatie uitgevoerd om te bepalen of Updatebeheer is ingeschakeld voor deze virtuele machine. Validatie omvat controles voor een Log Analytics-werk ruimte, voor een gekoppeld Automation-account, en of de oplossing zich in de werk ruimte bevindt.

U gebruikt een [log Analytics](../../log-analytics/log-analytics-overview.md) werkruimte om gegevens te verzamelen die worden gegenereerd door functies en services zoals updatebeheer. De werkruimte biedt één locatie om gegevens uit meerdere bronnen te bekijken en te analyseren.

Als u aanvullende acties wilt uitvoeren op virtuele machines die updates vereisen, kunt u Azure Automation gebruiken om runbooks uit te voeren op Vm's. Dergelijke acties omvatten het downloaden of Toep assen van updates.

Tijdens het validatie proces wordt ook gecontroleerd of de virtuele machine is ingericht met micro soft Monitoring Agent (MMA) en Automation Hybrid Runbook Worker. U gebruikt de agent om te communiceren met de virtuele machine en om informatie te verkrijgen over de status van de update.

Kies in het venster **updatebeheer inschakelen** de log Analytics werk ruimte en het Automation-account en selecteer vervolgens **inschakelen**. Het duurt Maxi maal 15 minuten om de oplossing in te scha kelen.

Een van de volgende vereiste onderdelen die ontbreken tijdens het voorbereiden worden automatisch toegevoegd:

* [Log Analytics](../../log-analytics/log-analytics-overview.md) werk ruimte
* [Automation](../../automation/automation-offering-get-started.md)
* Een [hybride runbook worker](../../automation/automation-hybrid-runbook-worker.md), dat is ingeschakeld op de VM

Nadat de oplossing is ingeschakeld, wordt het venster **Update beheer** geopend. Configureer de locatie, Log Analytics werk ruimte en het Automation-account dat moet worden gebruikt en selecteer vervolgens **inschakelen**. Als deze opties grijs worden weer gegeven, is er een andere automatiserings oplossing voor de virtuele machine ingeschakeld en moet de werk ruimte en het Automation-account van de oplossing worden gebruikt.

![Updatebeheer oplossing inschakelen](./media/tutorial-monitoring/manageupdates-update-enable.png)

Het kan tot vijf tien minuten duren voordat de Updatebeheer-oplossing is ingeschakeld. Sluit gedurende deze tijd het browservenster niet. Nadat de oplossing is ingeschakeld, wordt informatie over ontbrekende updates op de VM naar Azure Monitor-logboeken verzonden. Het kan 30 minuten tot 6 uur duren voordat de gegevens beschikbaar worden voor analyse.

### <a name="view-an-update-assessment"></a>Een update-evaluatie bekijken

Nadat Updatebeheer is ingeschakeld, wordt het venster **Update beheer** weer gegeven. Nadat de evaluatie van de updates is voltooid, ziet u een lijst met ontbrekende updates op het tabblad **ontbrekende updates** .

 ![Updatestatus bekijken](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Als u updates wilt installeren, plant u een implementatie na uw release-planning en servicevenster. U kiest welke update typen u wilt toevoegen aan de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

Als u een nieuwe update-implementatie voor de virtuele machine wilt plannen, selecteert u **Update-implementatie plannen** boven aan het venster **Update beheer** . Geef in het venster **nieuwe update-implementatie** de volgende informatie op:

| Optie | Beschrijving |
| --- | --- |
| **Naam** |Voer een unieke naam in om de update-implementatie te identificeren. |
|**Besturingssysteem**| Selecteer **Linux** of **Windows**.|
| **Bij te werken groepen** |Voor virtuele machines die worden gehost op Azure, definieert u een query op basis van een combi natie van abonnement, resource groepen, locaties en tags. Deze query bouwt een dynamische groep met door Azure gehoste Vm's op die in uw implementatie moeten worden opgenomen. </br></br>Voor Vm's die niet worden gehost in azure, selecteert u een bestaande opgeslagen zoek opdracht. Met deze zoek opdracht kunt u een groep van deze Vm's selecteren die in de implementatie moet worden meegenomen. </br></br> Zie [dynamische groepen](../../automation/automation-update-management-groups.md)voor meer informatie.|
| **Machines die moeten worden bijgewerkt** |Selecteer **opgeslagen zoek opdracht**, **geïmporteerde groep**of **machines**.<br/><br/>Als u **machines**selecteert, kunt u afzonderlijke machines kiezen in de vervolg keuzelijst. De gereedheid van elke machine wordt weer gegeven in de kolom **gereedheids Update Agent** van de tabel.</br></br> Zie [Computergroepen in Azure Monitorlogboeken](../../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken |
|**Update classificaties**|Kies alle benodigde update classificaties.|
|**Updates opnemen/uitsluiten**|Selecteer deze optie om het deel venster **opnemen/uitsluiting** te openen. Updates die moeten worden opgenomen en die moeten worden uitgesloten, bevinden zich op afzonderlijke tabbladen. Zie [een update-implementatie plannen](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment)voor meer informatie over het verwerken van de opname. |
|**Schema-instellingen**|Kies het tijdstip waarop u wilt beginnen en selecteer **één** of meer **keren.**|
| **Pre-scripts en post scripts**|Kies de scripts die moeten worden uitgevoerd vóór en na de implementatie.|
| **Onderhouds venster** | Voer het aantal minuten in dat moet worden ingesteld voor updates. Geldige waarden variëren van 30 tot 360 minuten. |
| **Besturings element opnieuw opstarten**| Selecteer hoe opnieuw opstarten wordt verwerkt. Beschik bare selecties zijn:<ul><li>**Opnieuw opstarten indien nodig**</li><li>**Altijd opnieuw opstarten**</li><li>**Nooit opnieuw opstarten**</li><li>**Alleen opnieuw opstarten**</li></ul>**Opnieuw opstarten als dit vereist** is, is de standaard selectie. Als u **alleen opnieuw opstarten**selecteert, worden er geen updates geïnstalleerd.|

Nadat u klaar bent met het configureren van de planning, klikt u op **maken** om terug te gaan naar het status dashboard. De **geplande** tabel toont het implementatie schema dat u hebt gemaakt.

U kunt ook programmatisch update-implementaties maken. Zie [Software-update configuraties-maken](/rest/api/automation/softwareupdateconfigurations/create)voor meer informatie over het maken van een update-implementatie met behulp van de rest API. Er is ook een voor beeld van een runbook dat u kunt gebruiken om een wekelijkse update-implementatie te maken. Zie [een wekelijkse update-implementatie maken voor een of meer virtuele machines in een resource groep](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)voor meer informatie over dit runbook.

### <a name="view-results-of-an-update-deployment"></a>Resultaten van een update-implementatie weergeven

Nadat de geplande implementatie is gestart, ziet u de implementatie status op het tabblad **Update-implementaties** van het venster **Update beheer** .

Als de implementatie momenteel wordt uitgevoerd, wordt de status weer gegeven als ' wordt uitgevoerd '. Wanneer de bewerking is voltooid, wordt de status gewijzigd in geslaagd. Maar als er updates in de implementatie mislukken, is de status gedeeltelijk mislukt.

Selecteer de voltooide update-implementatie om het dash board voor die implementatie weer te geven.

![Statusdashboard voor update-implementatie voor specifieke implementatie](./media/tutorial-monitoring/manageupdates-view-results.png)

De tegel **Update resultaten** toont een samen vatting van het totale aantal updates en de implementatie resultaten op de virtuele machine. In de tabel aan de rechter kant ziet u een gedetailleerde uitsplitsing van elke update en de installatie resultaten. Elk resultaat heeft een van de volgende waarden:

* **Niet geprobeerd**: de update is niet geïnstalleerd. Er is onvoldoende tijd beschikbaar op basis van de gedefinieerde duur van het onderhouds venster.
* **Geslaagd**: de update is voltooid.
* **Mislukt**: de update is mislukt.

Selecteer **Alle logboeken** als u alle logboekvermeldingen wilt zien die tijdens de implementatie zijn gemaakt.

Selecteer de tegel **uitvoer** om de taak stroom te bekijken van het runbook dat verantwoordelijk is voor het beheer van de update-implementatie op de doel-VM.

Selecteer **fouten** om gedetailleerde informatie over eventuele implementatie fouten weer te geven.

## <a name="monitor-changes-and-inventory"></a>Wijzigingen en inventaris bewaken

U kunt een inventaris van de software, bestanden, Linux-daemons, Windows-Services en Windows-register sleutels op uw computers verzamelen en weer geven. Het bijhouden van de configuraties van uw machines helpt u bij het identificeren van operationele problemen in uw omgeving en de status van uw computers beter te begrijpen.

### <a name="enable-change-and-inventory-management"></a>Wijzigings-en inventaris beheer inschakelen

Wijzigings-en inventaris beheer inschakelen voor uw virtuele machine:

1. Selecteer **virtuele machines**aan de linkerkant van het venster.
1. Kies een virtuele machine in de lijst.
1. Selecteer onder **bewerkingen** in het VM-venster de optie **inventaris** of het **bijhouden van wijzigingen**.
1. Het deel venster **Wijzigingen bijhouden en inventaris inschakelen** wordt geopend.

Configureer de locatie, Log Analytics-werk ruimte en het Automation-account dat moet worden gebruikt, en selecteer vervolgens **inschakelen**. Als de opties grijs worden weer gegeven, is er al een Automation-oplossing ingeschakeld voor de virtuele machine. In dat geval moet u de al ingeschakelde werk ruimte en het Automation-account gebruiken.

Hoewel de oplossingen afzonderlijk in het menu worden weer gegeven, zijn ze dezelfde oplossing. Als u er één inschakelt, worden beide ingeschakeld voor de VM.

![Bijhouden van wijzigingen en inventaris inschakelen](./media/tutorial-monitoring/manage-inventory-enable.png)

Nadat de oplossing is ingeschakeld, kan het enige tijd duren voordat de inventarisatie op de VM wordt verzameld voordat de gegevens worden weer gegeven.

### <a name="track-changes"></a>Wijzigingen bijhouden

Selecteer **Wijzigingen bijhouden** op uw VM onder **bewerkingen**en selecteer vervolgens **Instellingen bewerken**. Het deel venster **Wijzigingen bijhouden** wordt geopend. Selecteer het type instelling dat u wilt bijhouden en selecteer **+ Toevoegen** om de instellingen te configureren.

De beschik bare instellingen voor Windows zijn:

* Windows-register
* Windows-bestanden

Zie [problemen met wijzigingen in een virtuele machine oplossen](../../automation/automation-tutorial-troubleshoot-changes.md)voor meer informatie over wijzigingen bijhouden.

### <a name="view-inventory"></a>Inventaris weergeven

Selecteer op de VM de optie **inventaris** onder **bewerkingen**. Op het tabblad **Software** ziet u een tabel met de software die is gevonden. De details op hoog niveau voor elke software record worden weer gegeven in de tabel. Deze details zijn onder andere de software naam, versie, uitgever en laatst vernieuwde tijd.

![Inventaris weergeven](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Activiteiten logboeken en wijzigingen bewaken

Selecteer in het venster **Wijzigingen bijhouden** op uw VM de optie **activiteiten logboek beheren** om het deel venster **activiteiten logboek van Azure** te openen. Selecteer **verbinding maken** om wijzigingen bijhouden te verbinden met het Azure-activiteiten logboek voor uw VM.

Nadat Wijzigingen bijhouden is ingeschakeld, gaat u naar het deel venster **overzicht** voor uw VM en selecteert u **stoppen** om de virtuele machine te stoppen. Wanneer u daarom wordt gevraagd, selecteert u **Ja** om de virtuele machine te stoppen. Nadat de toewijzing van de virtuele machine ongedaan is gemaakt, selecteert u **starten** om de VM opnieuw op te starten.

Wanneer een virtuele machine wordt gestopt en opnieuw wordt gestart, wordt een gebeurtenis geregistreerd in het activiteiten logboek. Ga terug naar het deel venster voor het **bijhouden van wijzigingen** en selecteer het tabblad **gebeurtenissen** onder aan het deel venster. Na een tijdje worden de gebeurtenissen weer gegeven in de grafiek en in de tabel. U kunt elke gebeurtenis selecteren om gedetailleerde informatie voor die gebeurtenis weer te geven.

![Wijzigingen in het activiteitenlogboek weergeven](./media/tutorial-monitoring/manage-activitylog-view-results.png)

In het vorige diagram ziet u wijzigingen die in de loop van de tijd hebben plaatsgevonden. Nadat u een Azure-activiteit logboek verbinding hebt toegevoegd, worden in het lijn diagram bovenaan de gebeurtenissen van het Azure-activiteiten logboek weer gegeven.

Elke rij staaf grafieken vertegenwoordigt een ander type wijziging dat kan worden bijgehouden. Dit type is Linux-daemons, bestanden, Windows-register sleutels, software en Windows-Services. Op het tabblad **wijzigen** worden de wijzigings gegevens weer gegeven. Wijzigingen worden weer gegeven in de volg orde waarin ze zich voordoen, waarbij de meest recente wijziging het eerst wordt weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u Wijzigingen bijhouden en Updatebeheer voor uw virtuele machine geconfigureerd en gecontroleerd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Maak een resource groep en een virtuele machine.
> * Windows-updates beheren.
> * Wijzigingen en inventaris bewaken.

Ga naar de volgende zelf studie voor meer informatie over het bewaken van uw VM.

> [!div class="nextstepaction"]
> [Virtuele machines bewaken](tutorial-monitor.md)
