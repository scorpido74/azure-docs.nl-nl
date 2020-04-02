---
title: Begin-/Stop VM's-oplossing configureren
description: In dit artikel wordt beschreven hoe u de VM's start/stop configureert tijdens off-hours-oplossing om verschillende use cases of scenario's te ondersteunen.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d3ca8d17d6637f0ab2b5a5d3d7a99ac0beaafd2e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550381"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Vm's voor begin-/stop's configureren tijdens de oplossing voor buitenkantoor

Met de Start/Stop VM's tijdens off hours-oplossing u:

- [Vm's plannen om te starten en te stoppen.](#schedule)
- Vm's plannen om te starten en te stoppen in oplopende volgorde [met Azure Tags](#tags) (niet ondersteund voor klassieke VM's).
- Automatisch stoppen VM's op basis [van een laag CPU-gebruik.](#cpuutil)

In dit artikel wordt beschreven hoe u de oplossing met succes configureren om deze scenario's te ondersteunen. U ook leren hoe u andere veelvoorkomende configuratie-instellingen voor de oplossing uitvoert, zoals:

* [E-mailmeldingen configureren](#configure-email-notifications)

* [Een VM toevoegen](#add-a-vm)

* [Een VM uitsluiten](#exclude-a-vm)

* [De opstart- en afsluitschema's wijzigen](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scenario 1: VM's starten/stoppen volgens schema

Dit scenario is de standaardconfiguratie wanneer u de oplossing voor het eerst implementeert. U het bijvoorbeeld zo configureren dat alle VM's in een abonnement worden gestopt wanneer u 's avonds het werk verlaat en 's ochtends starten wanneer u weer op kantoor bent. Wanneer u de planningen **Scheduled-StartVM** en **Scheduled-StopVM** configureert tijdens de implementatie, starten en stoppen ze gerichte VM's. Als u deze oplossing configureert om vm's te stoppen, wordt ondersteuning ondersteund, zie [De opstart- en afsluitschema's wijzigen](#modify-the-startup-and-shutdown-schedules) om te leren hoe u een aangepast schema configureren.

> [!NOTE]
> De tijdzone is uw huidige tijdzone wanneer u de parameter planningstijd configureert. Het wordt echter opgeslagen in UTC-indeling in Azure Automation. U hoeft geen tijdzoneconversie te doen, omdat dit tijdens de implementatie wordt afgehandeld.

U bepaalt welke VM's binnen het bereik zijn door de volgende variabelen te configureren: **External_Start_ResourceGroupNames,** **External_Stop_ResourceGroupNames**en **External_ExcludeVMNames**.

U de actie richten op een abonnements- en resourcegroep of een specifieke lijst met VM's targeten, maar niet beide.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>De start- en stopacties richten op een abonnements- en resourcegroep

1. Configureer de **External_Stop_ResourceGroupNames-** en **External_ExcludeVMNames** variabelen om de doel-VM's op te geven.

2. Schakel de **planningen Scheduled-StartVM** en **Scheduled-StopVM** in en bij.

3. Voer de **ScheduledStartStop_Parent** runbook uit met de parameter ACTION die is ingesteld om te **starten** en de parameter WHATIF is ingesteld op **True** om een voorbeeld van uw wijzigingen te bekijken.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>De start- en stopactie targeten op VM-lijst

1. Voer de **ScheduledStartStop_Parent** runbook uit met de parameter ACTION die is ingesteld om te **starten,** voeg een door komma's gescheiden lijst met VM's toe in de parameter *VMList* en stel de parameter WHATIF in op **True**. Bekijk een voorbeeld van uw wijzigingen.

2. Configureer de **parameter External_ExcludeVMNames** met een door komma's gescheiden lijst met VM's (VM1, VM2, VM3).

3. Dit scenario is niet eer de **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupnames** variabelen. Voor dit scenario moet u uw eigen automatiseringsschema maken. Zie Een [runbook plannen in Azure Automation](../automation/automation-schedules.md)voor meer informatie.

    > [!NOTE]
    > De waarde voor **doelbrongroepnamen** wordt opgeslagen als de waarde voor zowel **External_Start_ResourceGroupNames** als **External_Stop_ResourceGroupNames**. Voor verdere granulariteit u elk van deze variabelen wijzigen om verschillende resourcegroepen te targeten. Gebruik **External_Stop_ResourceGroupNames**voor het starten van actie **External_Start_ResourceGroupNames**en voor stopactie . VM's worden automatisch toegevoegd aan de start- en stopschema's.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenario 2: VMS in volgorde starten/stoppen met behulp van tags

In een omgeving die twee of meer componenten op meerdere VM's bevat die een gedistribueerde werkbelasting ondersteunen, is het belangrijk om de volgorde te ondersteunen waarin componenten worden gestart en gestopt. U dit scenario uitvoeren door de volgende stappen uit te voeren:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>De start- en stopacties richten op een abonnements- en resourcegroep

1. Voeg een **sequencestart** en een **sequencestoptag** met een positieve gehele waarde toe aan VM's die zijn gericht op **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupNames** variabelen. De begin- en stopacties worden uitgevoerd in oplopende volgorde. Zie [Een Virtuele Machine van Windows in Azure taggen](../virtual-machines/windows/tag.md) en een virtuele machine van Linux [taggen in Azure](../virtual-machines/linux/tag.md)voor meer informatie over het taggen van een virtuele machine.

2. Wijzig de schema's **Sequenced-StartVM** en **Sequenced-StopVM** tot de datum en tijd die aan uw vereisten voldoen en schakel de planning in.

3. Voer de **SequencedStartStop_Parent** runbook uit met de parameter ACTION die is ingesteld om te **starten** en de parameter WHATIF is ingesteld op **True** om een voorbeeld van uw wijzigingen te bekijken.

4. Bekijk een voorbeeld van de actie en breng de nodige wijzigingen aan voordat u deze implementeert tegen productie-VM's. Wanneer u klaar bent, voert u het runbook handmatig uit met de parameter ingesteld op **False**of laat u de automatiseringsplanning **Sequenced-StartVM** en **Sequenced-StopVM** automatisch uitvoeren volgens het voorgeschreven schema.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>De start- en stopactie targeten op VM-lijst

1. Voeg een **sequencestart** en een **sequencestoptag** met een positieve gehele waarde toe aan VM's die u wilt toevoegen aan de parameter **VMList.**

2. Voer de **SequencedStartStop_Parent** runbook uit met de parameter ACTION die is ingesteld om te **starten,** voeg een door komma's gescheiden lijst met VM's toe in de parameter *VMList* en stel de parameter WHATIF in op **True**. Bekijk een voorbeeld van uw wijzigingen.

3. Configureer de **parameter External_ExcludeVMNames** met een door komma's gescheiden lijst met VM's (VM1, VM2, VM3).

4. Dit scenario is niet eer de **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupnames** variabelen. Voor dit scenario moet u uw eigen automatiseringsschema maken. Zie Een [runbook plannen in Azure Automation](../automation/automation-schedules.md)voor meer informatie.

5. Bekijk een voorbeeld van de actie en breng de nodige wijzigingen aan voordat u deze implementeert tegen productie-VM's. Wanneer u klaar bent, voert u het monitoring-and-diagnostics/monitoring-action-groupsrunbook handmatig uit met de parameter ingesteld op **False,** of laat u de automatiseringsplanning **Sequenced-StartVM** en **Sequenced-StopVM** automatisch uitvoeren volgens uw voorgeschreven schema.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenario 3: Start/Stop automatisch op basis van CPU-gebruik

Deze oplossing kan helpen bij het beheren van de kosten van het uitvoeren van azure resource manager- en klassieke virtuele machines in uw abonnement door VM's te evalueren die niet worden gebruikt tijdens niet-piekperioden, zoals na uren, en deze automatisch af te sluiten als het processorgebruik minder is dan een opgegeven percentage.

Standaard is de oplossing vooraf geconfigureerd om het percentage CPU-statistiek te evalueren om te zien of het gemiddelde gebruik 5 procent of minder is. Dit scenario wordt beheerd door de volgende variabelen en kan worden gewijzigd als de standaardwaarden niet aan uw vereisten voldoen:

* **External_AutoStop_MetricName**

* **External_AutoStop_Threshold**

* **External_AutoStop_TimeAggregationOperator**

* **External_AutoStop_TimeWindow**

* **External_AutoStop_Frequency**

* **External_AutoStop_Severity**

U de actie inschakelen en targeten op een abonnements- en resourcegroep, of een specifieke lijst met VM's targeten.

Wanneer u de **AutoStop_CreateAlert_Parent** runbook uitvoert, wordt gecontroleerd of het beoogde abonnement, de resourcegroep(s) en de VM's bestaan. Als de VM's bestaan, wordt vervolgens het **AutoStop_CreateAlert_Child** runbook voor elke geverifieerde VM door het bovenliggende runbook aanroept. In deze onderliggende runbook wordt het volgende uitgevoerd:

* Hiermee maakt u een metrische waarschuwingsregel voor elke geverifieerde VM.

* Hiermee wordt de **AutoStop_VM_Child** runbook voor een bepaalde vm geactiveerd als de CPU onder de geconfigureerde drempelwaarde voor het opgegeven tijdsinterval moet zakken. Dit runbook probeert vervolgens de VM te stoppen.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>De actie voor automatisch stoppen richten op alle VM's in een abonnement

1. Zorg ervoor dat de **External_Stop_ResourceGroupNames** variabele leeg is of ingesteld op * (wildcard).

2. [Optionele stap] Als u sommige VM's wilt uitsluiten van de automatische afsluiting, u een door komma's gescheiden lijst met VM-namen toevoegen aan de **variabele External_ExcludeVMNames.**

3. Schakel de **Schedule_AutoStop_CreateAlert_Parent-planning** in om de vereiste stopVM-metrische waarschuwingsregels te maken voor alle VM's in uw abonnement. Als u dit volgens een planning uitvoert, u nieuwe metrische waarschuwingsregels maken wanneer er nieuwe VM's aan het abonnement worden toegevoegd.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-groupmultiple-resource-groups"></a>De actie voor automatisch stoppen targeten op alle VM's in een resourcegroep/meerdere resourcegroepen

1. Voeg een algemene gescheiden lijst met namen van resourcegroepen toe aan de **variabele External_Stop_ResourceGroupNames.**

2. Als u een aantal VM's wilt uitsluiten van de automatische afsluiting, u een door komma's gescheiden lijst met VM-namen toevoegen aan de **variabele External_ExcludeVMNames.**

3. Schakel de **Schedule_AutoStop_CreateAlert_Parent** planning in om de vereiste **stopVM-metrische** waarschuwingsregels te maken voor alle VM's in uw resourcegroepen. Als u dit volgens een planning uitvoert, u nieuwe metrische waarschuwingsregels maken wanneer er nieuwe VM's worden toegevoegd aan de brongroep(en).

### <a name="to-target-the-auto-stop-action-to-a-list-of-vms"></a>De actie automatisch stoppen targeten op een lijst met VM's

1. Maak een nieuwe [planning](shared-resources/schedules.md#creating-a-schedule) en koppel deze aan het **AutoStop_CreateAlert_Parent** runbook, door een door komma's gescheiden lijst met VM-namen toe te voegen aan de parameter **VMList.**

2. Als u sommige VM's wilt uitsluiten van de automatische afsluiting, u een door komma's gescheiden lijst met VM-namen toevoegen aan de **variabele External_ExcludeVMNames.**

## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

Als u e-mailmeldingen wilt wijzigen nadat de oplossing is geïmplementeerd, wijzigt u de actiegroep die tijdens de implementatie is gemaakt.  

> [!NOTE]
> Abonnementen in de Azure Government Cloud bieden geen ondersteuning voor de e-mailfunctionaliteit van deze oplossing.

1. Navigeer in de Azure-portal naar > actiegroepen controleren. Selecteer de actiegroep met de titel **StartStop_VM_Notication**.

    ![Pagina automatiseringsupdatebeheeroplossing](media/automation-solution-vm-management/azure-monitor.png)

2. Klik op de **pagina StartStop_VM_Notification** op **Details bewerken** onder **Details**. Hiermee wordt de **pagina E-mail/SMS/Push/Voice** geopend. Werk het e-mailadres bij en klik op **OK** om de wijzigingen op te slaan.

    ![Pagina automatiseringsupdatebeheeroplossing](media/automation-solution-vm-management/change-email.png)

    U ook aanvullende acties toevoegen aan de actiegroep, voor meer informatie over actiegroepen, [zie actiegroepen](../azure-monitor/platform/action-groups.md)

Het volgende is een voorbeeld e-mail die wordt verzonden wanneer de oplossing sluit virtuele machines.

![Pagina automatiseringsupdatebeheeroplossing](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>VM's toevoegen/uitsluiten

De oplossing biedt de mogelijkheid om VM's toe te voegen om te worden gericht door de oplossing of specifiek machines uit te sluiten van de oplossing.

### <a name="add-a-vm"></a>Een VM toevoegen

Er zijn twee opties die u gebruiken om ervoor te zorgen dat een VM wordt opgenomen in de Start/Stop-oplossing wanneer deze wordt uitgevoerd.

* Elk van de bovenliggende [runbooks](automation-solution-vm-management.md#runbooks) van de oplossing heeft een **VMList-parameter.** U een door komma's gescheiden lijst met VM-namen doorgeven aan deze parameter bij het plannen van het juiste bovenliggende runbook voor uw situatie en deze VM's worden opgenomen wanneer de oplossing wordt uitgevoerd.

* Als u meerdere VM's wilt selecteren, stelt u de **External_Start_ResourceGroupNames** in en **External_Stop_ResourceGroupNames** met de namen van de resourcegroep die de VM's bevatten die u wilt starten of stoppen. U deze waarde `*`ook instellen op, om de oplossing te laten uitvoeren ten opzichte van alle resourcegroepen in het abonnement.

### <a name="exclude-a-vm"></a>Een VM uitsluiten

Als u een virtuele machine van de oplossing wilt uitsluiten, u deze toevoegen aan de **variabele External_ExcludeVMNames.** Deze variabele is een door komma's gescheiden lijst met specifieke VM's die u wilt uitsluiten van de Start/Stop-oplossing. Deze lijst is beperkt tot 140 VM's. Als u meer dan 140 VM's toevoegt aan deze door komma's gescheiden lijst, kunnen VM's die zijn ingesteld om te worden uitgesloten, per ongeluk worden gestart of gestopt.

## <a name="modify-the-startup-and-shutdown-schedules"></a>De opstart- en afsluitschema's wijzigen

Het beheren van de opstart- en afsluitschema's in deze oplossing volgt dezelfde stappen als beschreven in [het plannen van een runbook in Azure Automation.](automation-schedules.md) Er moet een apart schema zijn om VM's te starten en te stoppen.

Het configureren van de oplossing om vm's op een bepaald moment te stoppen, wordt ondersteund. In dit scenario maakt u gewoon een **stopschema** en geen bijbehorende **Start** gepland. Hiervoor doet u het volgende:

1. Zorg ervoor dat u de resourcegroepen hebt toegevoegd die de VM's moeten afsluiten in de **variabele External_Stop_ResourceGroupNames.**

2. Maak uw eigen schema voor de tijd die u wilt afsluiten van de VM's.

3. Navigeer naar het **ScheduledStartStop_Parent-runbook** en klik op **Planning**. Hiermee u het schema selecteren dat u in de vorige stap hebt gemaakt.

4. Selecteer **Parameters en voer instellingen uit** en stel de parameter **ACTION** in op **Stoppen**.

5. Selecteer **OK** om uw wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

* Zie Vm's voor het oplossen van problemen met start-/stop-vm's tijdens kantooruren voor meer informatie over het oplossen van VM's voor [starten/stoppen](troubleshoot/start-stop-vm.md)tijdens het buitenkantoor.

* [Bekijk](automation-solution-vm-management-logs.md) de automatiseringsrecords die zijn geschreven naar Azure Monitor Logs en de voorbeeldlogboekquery's om de status van automatiseringsrunbooktaken van Vm's starten/stoppen te analyseren.
