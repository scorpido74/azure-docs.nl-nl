---
title: De oplossing voor het starten/stoppen van Vm's Azure Automation configureren
description: In dit artikel wordt beschreven hoe u de VM's buiten bedrijfsuren starten/stoppen-oplossing kunt configureren voor de ondersteuning van verschillende use cases of scenario's.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9842a736cf922e0490f2b0c8acb1d2e5833f3d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604761"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Het configureren van Vm's voor starten/stoppen tijdens een buiten-kantoor oplossing

Met de oplossing **Vm's starten/stoppen buiten kantoor uren** kunt u het volgende doen:

- [Plan vm's om te starten en te stoppen](#schedule).
- Plan Vm's om in oplopende volg orde te starten en te stoppen met [behulp van Azure Tags](#tags) (niet ondersteund voor klassieke vm's).
- Vm's automatisch stoppen op basis van [laag CPU-gebruik](#cpuutil).

In dit artikel wordt beschreven hoe u de oplossing kunt configureren voor de ondersteuning van deze scenario's. U kunt ook meer informatie over het uitvoeren van andere algemene configuratie-instellingen voor de oplossing, zoals:

* [E-mailmeldingen configureren](#configure-email-notifications)

* [Een VM toevoegen](#add-a-vm)

* [Een VM uitsluiten](#exclude-a-vm)

* [De planningen voor opstarten en afsluiten wijzigen](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scenario 1: Vm's starten/stoppen volgens een planning

Dit scenario is de standaard configuratie wanneer u de oplossing voor het eerst implementeert. U kunt deze bijvoorbeeld zo configureren dat alle virtuele machines in een abonnement worden gestopt wanneer u het werk in de avond verlaat en ze 's morgens start wanneer u weer terug bent in het kantoor. Wanneer u de planning **gepland-StartVM** en **gepland-StopVM** configureert tijdens de implementatie, worden de beoogde vm's gestart en gestopt. Het configureren van deze oplossing voor het stoppen van Vm's wordt ondersteund, Zie [de opstart-en afsluit Schema's wijzigen](#modify-the-startup-and-shutdown-schedules) voor meer informatie over het configureren van een aangepaste planning.

> [!NOTE]
> De tijd zone is uw huidige tijd zone bij het configureren van de plannings tijd parameter. Het wordt echter opgeslagen in UTC-indeling in Azure Automation. U hoeft geen tijd zone conversie uit te voeren, aangezien deze wordt verwerkt tijdens de implementatie.

U bepaalt welke Vm's binnen het bereik vallen door de volgende variabelen te configureren: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**en **External_ExcludeVMNames**.

U kunt de actie richten op een abonnement en resource groep, of een specifieke lijst met Vm's maken, maar niet beide.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>De start-en stop acties voor een abonnement en resource groep richten

1. Configureer de `External_Stop_ResourceGroupNames` variabelen `External_ExcludeVMNames` en om de doel-vm's op te geven.

2. De **geplande-StartVM** en **geplande StopVM-** schema's inschakelen en bijwerken.

3. Voer het **ScheduledStartStop_Parent** runbook uit met het parameter veld voor de **actie** die is ingesteld op **Start** en het veld **WHATIF** para meter is ingesteld op True om een voor beeld van de wijzigingen weer te geven.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>De start-en stop actie richten op de VM-lijst

1. Voer het **ScheduledStartStop_Parent** runbook uit met de **actie** die is ingesteld om te **starten**, voeg een door komma's gescheiden lijst met Vm's toe in het parameter veld **VMList** en stel het veld **WHATIF** parameter in op True. Bekijk een voor beeld van uw wijzigingen.

2. Configureer de `External_ExcludeVMNames` variabele met een door komma's gescheiden lijst met virtuele machines (VM1, VM2, VM3).

3. In dit scenario worden de `External_Start_ResourceGroupNames` variabelen en `External_Stop_ResourceGroupnames` niet nageleefd. Voor dit scenario moet u uw eigen Automation-schema maken. Zie [een Runbook plannen in azure Automation](../automation/automation-schedules.md)voor meer informatie.

    > [!NOTE]
    > De waarde voor **doel-ResourceGroup namen** wordt opgeslagen als de waarde voor `External_Start_ResourceGroupNames` zowel `External_Stop_ResourceGroupNames`en. Voor nadere granulariteit kunt u elk van deze variabelen wijzigen in doel verschillende resource groepen. Voor het starten van actie `External_Start_ResourceGroupNames`, gebruiken en `External_Stop_ResourceGroupNames` gebruiken voor stop actie. Vm's worden automatisch toegevoegd aan de planningen starten en stoppen.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenario 2: VM'S in volg orde starten/stoppen met behulp van Tags

In een omgeving met twee of meer onderdelen op meerdere Vm's die een gedistribueerde werk belasting ondersteunen, wordt de volg orde waarin de onderdelen worden gestart en gestopt, op de juiste wijze ondersteund. U kunt dit scenario uitvoeren door de volgende stappen uit te voeren:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>De start-en stop acties voor een abonnement en resource groep richten

1. Voeg een `sequencestart` en een `sequencestop` tag met een positieve integerwaarde toe aan vm's die zijn gericht `External_Start_ResourceGroupNames` op `External_Stop_ResourceGroupNames` en variabelen. De start-en stop acties worden in oplopende volg orde uitgevoerd. Zie een [virtuele Windows-machine coderen in azure](../virtual-machines/windows/tag.md) en [een virtuele Linux-machine in azure labelen](../virtual-machines/linux/tag.md)voor meer informatie over het coderen van een VM.

2. Wijzig de planningen **Sequenced-StartVM** en **Sequence-StopVM** in de datum en tijd die aan uw vereisten voldoen en schakel de planning in.

3. Voer het **SequencedStartStop_Parent** runbook uit met de **actie** ingesteld op **Start** en **WHATIF** ingesteld op True om een voor beeld van de wijzigingen weer te geven.

4. Bekijk een voor beeld van de actie en breng de benodigde wijzigingen aan voordat u implementeert op productie-Vm's. Als u klaar bent, voert u het runbook hand matig uit met de para meter ingesteld op **Onwaar**, of laat u het Automation-schema `Sequenced-StartVM` automatisch `Sequenced-StopVM` uitvoeren volgens uw voorgeschreven planning.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>De start-en stop actie richten op de VM-lijst

1. Voeg een `sequencestart` en een `sequencestop` tag toe met een positieve integerwaarde voor vm's die u wilt toevoegen aan `VMList` de para meter.

2. Voer het **SequencedStartStop_Parent** runbook uit met de **actie** die is ingesteld op **starten**, voeg een door komma's gescheiden lijst met Vm's toe in het parameter veld **VMList** en stel **WHATIF** in op True. Bekijk een voor beeld van uw wijzigingen.

3. Configureer de `External_ExcludeVMNames` variabele met een door komma's gescheiden lijst met virtuele machines (VM1, VM2, VM3).

4. In dit scenario worden de `External_Start_ResourceGroupNames` variabelen en `External_Stop_ResourceGroupnames` niet nageleefd. Voor dit scenario moet u uw eigen Automation-schema maken. Zie [een Runbook plannen in azure Automation](../automation/automation-schedules.md)voor meer informatie.

5. Bekijk een voor beeld van de actie en breng de benodigde wijzigingen aan voordat u implementeert op productie-Vm's. Als u klaar bent, voert u hand matig de **bewakings-en-diagnose/bewaking-Action-groupsrunbook** uit met de para meter ingesteld op **Onwaar**. U kunt het Automation-schema `Sequenced-StartVM` ook `Sequenced-StopVM` automatisch laten uitvoeren volgens uw voorgeschreven schema.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenario 3: automatisch starten/stoppen op basis van CPU-gebruik

Met deze oplossing kunt u de kosten van het uitvoeren van Azure Resource Manager en klassieke virtuele machines in uw abonnement helpen beheren door Vm's te evalueren die niet worden gebruikt tijdens niet-piek perioden, zoals na uur, en ze automatisch af te sluiten als het processor gebruik minder is dan een opgegeven percentage.

De oplossing is standaard vooraf geconfigureerd om het percentage CPU-metriek te evalueren om te controleren of het gemiddelde gebruik 5% of minder is. Dit scenario wordt bepaald door de volgende variabelen en kan worden gewijzigd als de standaard waarden niet voldoen aan uw vereisten:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

U kunt de actie op basis van een abonnement en resource groep inschakelen en deze richten, of een specifieke lijst met Vm's richten.

Wanneer u het **AutoStop_CreateAlert_Parent** runbook uitvoert, wordt gecontroleerd of het doel abonnement, de resource groep (en) en de vm's bestaan. Als de Vm's bestaan, roept het runbook het **AutoStop_CreateAlert_Child** runbook op voor elke GEVERIFIEERDE virtuele machine door het bovenliggende runbook. Dit onderliggende runbook voert het volgende uit:

* Hiermee maakt u een waarschuwings regel voor metrische gegevens voor elke geverifieerde VM.

* Hiermee wordt het **AutoStop_VM_Child** runbook voor een bepaalde virtuele machine geactiveerd als de CPU onder de geconfigureerde drempel waarde voor het opgegeven tijds interval daalt. Dit runbook probeert vervolgens de virtuele machine te stoppen.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>De actie automatisch stoppen voor alle virtuele machines in een abonnement instellen

1. Zorg ervoor dat `External_Stop_ResourceGroupNames` de variabele leeg is of is ingesteld op * (joker tekens).

2. [Optionele stap] Als u sommige Vm's wilt uitsluiten van het automatisch afsluiten, kunt u een door komma's gescheiden lijst met VM-namen toevoegen aan de `External_ExcludeVMNames` variabele.

3. Stel in `Schedule_AutoStop_CreateAlert_Parent` dat het schema moet worden uitgevoerd om de vereiste waarschuwings regels voor het stoppen van de VM-metrische gegevens voor alle virtuele machines in uw abonnement te maken. Door dit type planning uit te voeren, kunt u nieuwe metrische waarschuwings regels maken wanneer er nieuwe virtuele machines aan het abonnement worden toegevoegd.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>De actie automatisch stoppen voor alle Vm's in een resource groep of meerdere resource groepen instellen

1. Voeg een door komma's gescheiden lijst met namen van resource groepen toe `External_Stop_ResourceGroupNames` aan de variabele.

2. Als u sommige Vm's wilt uitsluiten van het automatisch afsluiten, kunt u een door komma's gescheiden lijst met VM-namen toevoegen aan de `External_ExcludeVMNames` variabele.

3. Schakel het **Schedule_AutoStop_CreateAlert_Parent** schema uit dat moet worden uitgevoerd om de vereiste waarschuwings regels voor het stoppen van de VM-metrische gegevens voor alle virtuele machines in de resource groepen te maken. Als u deze bewerking uitvoert volgens een planning, kunt u nieuwe metrische waarschuwings regels maken wanneer er nieuwe virtuele machines worden toegevoegd aan de resource groep (en).

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>De actie voor het uitschakelen van een lijst met Vm's

1. Maak een nieuwe [planning](shared-resources/schedules.md#creating-a-schedule) en koppel deze aan het **AutoStop_CreateAlert_Parent** runbook en voeg een door KOMMA'S gescheiden lijst met VM-namen toe `VMList` aan de para meter.

2. Als u sommige Vm's wilt uitsluiten van automatisch afsluiten, kunt u desgewenst een door komma's gescheiden lijst met VM-namen toevoegen aan de `External_ExcludeVMNames` variabele.

## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

Als u e-mail meldingen wilt wijzigen nadat de oplossing is geïmplementeerd, wijzigt u de actie groep die tijdens de implementatie is gemaakt.  

> [!NOTE]
> Abonnementen in de Azure Government Cloud bieden geen ondersteuning voor de e-mail functionaliteit van deze oplossing.

1. Ga in het Azure Portal naar **controle**en vervolgens op **actie groepen**. Selecteer de actie groep met de naam **StartStop_VM_Notication**.

    ![Pagina Automation Updatebeheer-oplossing](media/automation-solution-vm-management/azure-monitor.png)

2. Klik op de pagina **StartStop_VM_Notification** op **Details bewerken** onder **Details**. Hiermee opent u de pagina **e-mail/SMS/push/Voice** . Werk het e-mail adres bij en klik op **OK** om uw wijzigingen op te slaan.

    ![Pagina Automation Updatebeheer-oplossing](media/automation-solution-vm-management/change-email.png)

    U kunt ook aanvullende acties toevoegen aan de actie groep, Zie [actie groepen](../azure-monitor/platform/action-groups.md) voor meer informatie over actie groepen

Hier volgt een voor beeld van een e-mail bericht dat wordt verzonden wanneer virtuele machines worden afgesloten met de oplossing.

![Pagina Automation Updatebeheer-oplossing](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Vm's toevoegen/uitsluiten

De oplossing biedt de mogelijkheid om Vm's toe te voegen aan de hand van de oplossing of om specifieke machines uit te sluiten van de oplossing.

### <a name="add-a-vm"></a>Een VM toevoegen

Er zijn twee opties die u kunt gebruiken om ervoor te zorgen dat een virtuele machine wordt opgenomen in de oplossing starten/stoppen wanneer deze wordt uitgevoerd.

* Elk van de bovenliggende [runbooks](automation-solution-vm-management.md#runbooks) van de oplossing heeft een `VMList` para meter. U kunt een door komma's gescheiden lijst met VM-namen door geven aan deze para meter bij het plannen van het juiste bovenliggende runbook voor uw situatie en deze Vm's worden opgenomen wanneer de oplossing wordt uitgevoerd.

* Als u meerdere Vm's wilt selecteren `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` stelt u en met de namen van de resource groepen die de vm's bevatten die u wilt starten of stoppen. U kunt ook de variabelen instellen op een waarde van `*` om de oplossing uit te voeren op alle resource groepen in het abonnement.

### <a name="exclude-a-vm"></a>Een VM uitsluiten

Als u een virtuele machine wilt uitsluiten van de oplossing, kunt u deze `External_ExcludeVMNames` toevoegen aan de variabele. Deze variabele is een door komma's gescheiden lijst met specifieke Vm's die moeten worden uitgesloten van de oplossing voor starten en stoppen. Deze lijst is beperkt tot 140 Vm's. Als u meer dan 140 Vm's toevoegt aan deze door komma's gescheiden lijst, kunnen Vm's die zijn ingesteld om te worden uitgesloten, onbedoeld worden gestart of gestopt.

## <a name="modify-the-startup-and-shutdown-schedules"></a>De planningen voor opstarten en afsluiten wijzigen

Het beheren van de opstart-en afsluit schema's in deze oplossing volgt dezelfde stappen als beschreven in [een Runbook plannen in azure Automation](automation-schedules.md). Er moet een apart schema zijn om Vm's te starten en te stoppen.

Het configureren van de oplossing om alleen Vm's op een bepaald moment te stoppen, wordt ondersteund. In dit scenario maakt u alleen een **Stop** schema en geen bijbehorende **begin** planning. Hiervoor doet u het volgende:

1. Zorg ervoor dat u de resource groepen hebt toegevoegd voor de virtuele machines die u `External_Stop_ResourceGroupNames` wilt afsluiten in de variabele.

2. Maak uw eigen planning voor het tijdstip waarop u de virtuele machines wilt afsluiten.

3. Ga naar het **ScheduledStartStop_Parent** runbook en klik op **schema**. Hierdoor kunt u het schema selecteren dat u in de vorige stap hebt gemaakt.

4. Selecteer **para meters en voer instellingen uit** en stel het **actie** veld in op **stoppen**.

5. Selecteer **OK** om uw wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

* Zie [problemen oplossen met Vm's starten/stoppen](troubleshoot/start-stop-vm.md)voor meer informatie over het oplossen van VM's buiten bedrijfsuren starten/stoppen.

* [Controleer](automation-solution-vm-management-logs.md) de Automation-records die zijn geschreven naar Azure monitor logboeken en het voor beeld van zoek query's in Logboeken om de status van Automation runbook-taken te analyseren van Vm's voor starten/stoppen.
