---
title: Updates en patches voor uw Azure-VM's beheren in Azure Automation
description: Dit artikel beschrijft hoe u Updatebeheer kunt gebruiken om updates en patches voor uw Azure-VM's te beheren.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: a701a5a9fd77bd801bb535fe1f26bfa17c97757b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185786"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Updates en patches voor uw Azure-VM's beheren

Dit artikel beschrijft hoe u de [Updatebeheer](automation-update-management.md)-functie van Azure Automation kunt gebruiken om updates en patches te beheren voor uw Azure-VM's. Zie [Automation-prijzen voor updatebeheer](https://azure.microsoft.com/pricing/details/automation/) voor prijsinformatie.

> [!NOTE]
> Updatebeheer ondersteunt de implementatie van updates voor de eerste partij en het vooraf downloaden van patches. Deze ondersteuning vereist wijzigingen op de systemen die worden bijgewerkt. Zie [Windows Update instellingen configureren voor Azure Automation updatebeheer](automation-configure-windows-update.md) voor meer informatie over het configureren van deze instellingen op uw systemen.

Voordat u de procedures in dit artikel gebruikt, moet u ervoor zorgen dat u Updatebeheer op uw Vm's hebt ingeschakeld met een van de volgende technieken:

* [Updatebeheer inschakelen vanaf een Automation-account](automation-onboard-solutions-from-automation-account.md)
* [Updatebeheer in te scha kelen door te bladeren door de Azure Portal](automation-onboard-solutions-from-browse.md)
* [Updatebeheer inschakelen vanuit een runbook](automation-onboard-solutions.md)
* [Updatebeheer inschakelen vanaf een virtuele Azure-machine](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Het bereik voor de implementatie beperken

Updatebeheer gebruikt een scope configuratie in de werk ruimte om de computers te richten op het ontvangen van updates. Zie [limiet updatebeheer-implementatie bereik](automation-scope-configurations-update-management.md)voor meer informatie.

## <a name="view-update-assessment"></a>Update-evaluatie bekijken

Een update-evaluatie weer geven:

1. Selecteer in uw Automation-account **Update beheer** onder **Update beheer**. 

2. De updates voor uw omgeving worden vermeld op de pagina update beheer. Als er ontbrekende updates worden geïdentificeerd, wordt een lijst met ontbrekende updates weergegeven op het tabblad **Ontbrekende updates**.

3. Selecteer onder **informatie koppeling**de koppeling voor een update om het ondersteunings artikel te openen waarmee u belang rijke informatie over de update krijgt.

    ![Updatestatus bekijken](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. Klik ergens anders in de update om het deel venster zoeken in Logboeken te openen. De query voor zoeken in logboeken is vooraf gedefinieerd voor deze specifieke update. U kunt deze query wijzigen of uw eigen query maken om gedetailleerde informatie weer te geven.

    ![Updatestatus bekijken](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Waarschuwingen configureren

Volg de onderstaande stappen om waarschuwingen in te stellen om de status van een update-implementatie te kennen:

1. Ga in uw Automation-account onder **Bewaking** naar **Waarschuwingen** en klik op **Nieuwe waarschuwingsregel**.

2. Op de pagina waarschuwings regel maken is uw Automation-account al geselecteerd als de resource. Als u deze wilt wijzigen, klikt u op **Resource bewerken**. 

3. Kies op de pagina Een resource selecteren **Automation-accounts** in het vervolgkeuzemenu **Filteren op resourcetype**. 

4. Selecteer het Automation-account dat u wilt gebruiken en klik vervolgens op **gereed**.

5. Klik op **voor waarde toevoegen** om het signaal te selecteren dat geschikt is voor uw update-implementatie. In de volgende tabel vindt u de details van de twee beschikbare signalen.

    |Signaalnaam|Dimensies|Beschrijving
    |---|---|---|
    |`Total Update Deployment Runs`|- Naam van update-implementatie<br>-Status    |Waarschuwingen over de algehele status van een update-implementatie.|
    |`Total Update Deployment Machine Runs`|- Naam van update-implementatie</br>- Status</br>- Doelcomputer</br>- Uitvoerings-id van update-implementatie    |Waarschuwingen over de status van een update-implementatie gericht op specifieke computers.|

6. Selecteer een geldige waarde in de lijst voor een dimensie. Als de gewenste waarde niet voor komt in de lijst, klikt u op **\+** naast de dimensie en typt u de aangepaste naam. Selecteer vervolgens de waarde waarnaar u wilt zoeken. Als u alle waarden van een dimensie wilt selecteren, klikt u op de knop **Selecteren\*** . Als u geen waarde voor een dimensie kiest, wordt die dimensie door Updatebeheer genegeerd.

    ![Signaallogica configureren](./media/automation-tutorial-update-management/signal-logic.png)

7. Voer onder **waarschuwings logica**waarden in de velden **tijd aggregatie** en **drempel waarde** in en klik vervolgens op **gereed**.

8. Voer in het volgende deel venster een naam en een beschrijving in voor de waarschuwing.

9. Stel het veld **Ernst** in op **Informational (Ernst 2)** voor een geslaagde uitvoering of **informatie (Ernst 1)** voor een mislukte uitvoering.

    ![Signaallogica configureren](./media/automation-tutorial-update-management/define-alert-details.png)

10. Klik op **Ja** of **Nee**, afhankelijk van hoe u de waarschuwings regel wilt inschakelen.

11. Als u geen waarschuwingen voor deze regel wilt hebben, selecteert u **waarschuwingen onderdrukken**.

## <a name="configure-action-groups-for-your-alerts"></a>Actie groepen voor uw waarschuwingen configureren

Zodra u uw waarschuwingen hebt geconfigureerd, kunt u een actie groep instellen. Dit is een groep acties die in meerdere waarschuwingen moet worden gebruikt. Deze acties kunnen e-mailmeldingen, runbooks, webhooks en nog veel meer zijn. Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/platform/action-groups.md) voor meer informatie over actiegroepen.

1. Selecteer een waarschuwing en selecteer vervolgens **nieuwe maken** onder **actie groepen**. 

2. Voer een volledige naam en een korte naam in voor de actie groep. Updatebeheer maakt gebruik van de korte naam wanneer u meldingen verzendt met de opgegeven groep.

3. Voer onder **acties**een naam in waarmee de actie wordt opgegeven, bijvoorbeeld **e-mail melding**. 

4. Voor **actie type**selecteert u het juiste type, bijvoorbeeld **e-mail/SMS/push/Voice**. 

5. Klik op **Details bewerken**.

6. Vul het deel venster in voor uw actie type. Als u bijvoorbeeld **e-mail/SMS/push/Voice**gebruikt, voert u een actie naam in, selecteert u het selectie vakje **e-mail** , voert u een geldig e-mail adres in en klikt u vervolgens op **OK**.

    ![Een e-mailactiegroep configureren](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. Klik op **OK** in het deelvenster Actiegroep toevoegen.

8. Voor een waarschuwings-e-mail kunt u het onderwerp van de e-mail aanpassen. Selecteer **acties aanpassen** onder **regel maken**en selecteer vervolgens **e-mail onderwerp**. 

9. Wanneer u klaar bent, klikt u op **waarschuwings regel maken**. 

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Bij het plannen van een update-implementatie wordt een [schema](shared-resources/schedules.md)resource gemaakt die is gekoppeld aan het**patch-MicrosoftOMSComputers**-runbook dat de update-implementatie op de doelcomputers afhandelt. U moet een implementatie plannen die volgt op de release planning en het service venster om updates te installeren. U kunt de typen updates kiezen die moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

>[!NOTE]
>Als u na het maken van de implementatie de planningsresource verwijdert via de Azure-portal of met behulp van PowerShell, wordt de geplande update-implementatie door de verwijdering verbroken en wordt er een foutmelding weergegeven wanneer u probeert de planningsresource opnieuw te configureren vanuit de portal. U kunt de planningsresource alleen verwijderen door het bijbehorende implementatieschema te verwijderen.  

Een nieuwe update-implementatie plannen:

1. Ga in uw Automation-account naar **Update beheer** onder **Update beheer**en selecteer vervolgens **Update-implementatie plannen**.

2. Gebruik onder **nieuwe update-implementatie**het veld **naam** om een unieke naam voor uw implementatie op te geven.

3. Selecteer het besturings systeem dat u wilt instellen als doel voor de update-implementatie.

4. In de regio **groepen die moeten worden bijgewerkt (preview-versie)** definieert u een query die het abonnement, de resource groepen, locaties en Tags combineert om een dynamische groep van virtuele Azure-machines samen te stellen die in uw implementatie moeten worden meegenomen. Zie voor meer informatie [dynamische groepen gebruiken met updatebeheer](automation-update-management-groups.md).

5. Selecteer in de regio **computers die u wilt bijwerken** een opgeslagen zoek opdracht, een geïmporteerde groep of kies **machines** in het vervolg keuzemenu en selecteer afzonderlijke machines. Met deze optie kunt u de gereedheid van de Log Analytics-agent voor elke machine bekijken. Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken.

6. Gebruik de regio **Update classificaties** om [Update classificaties](automation-view-update-assessments.md#work-with-update-classifications) voor producten op te geven. Voor elk product deselecteert u alle ondersteunde update classificaties, maar ook voor de implementatie van de update.

7. Gebruik de regio **updates opnemen/uitsluiten** om specifieke updates voor implementatie te selecteren. Op de pagina opnemen/uitsluiten worden de updates weer gegeven op KB-artikel-ID-nummers die moeten worden opgenomen of uitgesloten. 
    
   > [!IMPORTANT]
   > Houd er rekening mee dat uitsluitingen insluitingen opheffen. Als u bijvoorbeeld een uitsluitings regel van opgeeft `*` , updatebeheer sluit alle patches of pakketten van de installatie uit. Uitgesloten patches worden nog steeds weer gegeven als ontbrekend op de computers. Als u op Linux-computers een pakket opneemt dat een afhankelijk pakket bevat dat is uitgesloten, installeert Updatebeheer het hoofdpakket niet.

   > [!NOTE]
   > U kunt geen updates opgeven die in de update-implementatie zijn vervangen.

8. Selecteer **schema-instellingen**. De standaard begintijd is 30 minuten na de huidige tijd. U kunt de starttijd op elke gewenste tijd instellen, maar minstens 10 minuten na de huidige tijd.

9. Gebruik het veld **recurrence** om op te geven of de implementatie één keer wordt uitgevoerd of een terugkerend schema gebruikt en klik vervolgens op **OK**.

10. Selecteer in de regio **pre-scripts + post-scripts (preview)** de scripts die moeten worden uitgevoerd vóór en na de implementatie. Zie [pre-scripts en post scripts beheren](pre-post-scripts.md)voor meer informatie.
    
11. Gebruik het veld **onderhouds venster (minuten)** om de hoeveelheid tijd op te geven die is toegestaan voor het installeren van updates. Houd rekening met de volgende details wanneer u een onderhoudsvenster opgeeft:

    * Onderhoudsvensters bepalen hoeveel updates worden geïnstalleerd.
    * Updatebeheer stopt niet met het installeren van nieuwe updates als het einde van een onderhoudsvenster nadert.
    * Updatebeheer wordt niet beëindigd als er updates worden uitgevoerd wanneer het onderhoudsvenster wordt overschreden.
    * Als het onderhoudsvenster op Windows wordt overschreden, komt dat vaak doordat het installeren van een servicepack-update lang duurt.

    > [!NOTE]
    > Om te voor komen dat updates buiten een onderhouds venster op Ubuntu worden toegepast, `Unattended-Upgrade` moet u het pakket opnieuw configureren om automatische updates uit te scha kelen. Zie het [onderwerp automatische updates in de Ubuntu-Server handleiding](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)voor informatie over het configureren van het pakket.

12. Gebruik het veld **Opties voor opnieuw opstarten** om op te geven hoe opnieuw opstarten tijdens de implementatie moet worden afgehandeld. De volgende opties zijn beschikbaar: 
    * Opnieuw opstarten indien nodig (standaard)
    * Altijd opnieuw opstarten
    * Nooit opnieuw opstarten
    * Alleen opnieuw opstarten; met deze optie worden geen updates geïnstalleerd

    > [!NOTE]
    > De registersleutels onder [Registry keys used to manage restart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) (registersleutels voor het beheren van opnieuw opstarten) kunnen een gebeurtenis voor opnieuw opstarten veroorzaken als **Opties voor opnieuw opstarten** is ingesteld op **Nooit opnieuw opstarten**.

13. Wanneer u klaar bent met het configureren van de implementatie planning, klikt u op **maken**.

    ![Deelvenster Planningsinstellingen bijwerken](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. U keert nu terug naar het statusdashboard. Selecteer **geplande update-implementaties** om het implementatie schema weer te geven dat u hebt gemaakt.

## <a name="schedule-an-update-deployment-programmatically"></a>Een update-implementatie via een programma plannen

Zie [Software Update Configurations - Create](/rest/api/automation/softwareupdateconfigurations/create) (software-updateconfiguraties: maken) als u wilt leren hoe u een update-implementatie kunt maken met de REST-API. 

U kunt een voor beeld-runbook gebruiken om een wekelijkse update-implementatie te maken. Zie voor meer informatie over dit runbook [Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) (een wekelijkse update-implementatie maken voor een of meer VM's in een resourcegroep).

## <a name="check-deployment-status"></a>Implementatie status controleren

Nadat de geplande implementatie is gestart, kunt u de status ervan weer geven op het tabblad **Update-implementaties** onder **Update beheer**. Als de implementatie momenteel wordt uitgevoerd, is de status **Wordt uitgevoerd**. Wanneer de implementatie is voltooid, verandert de status in **geslaagd**. Als er fouten optreden met een of meer updates in de implementatie, verandert de status in **Gedeeltelijk mislukt**.

## <a name="view-results-of-a-completed-update-deployment"></a>Resultaten van een voltooide update-implementatie weer geven

Wanneer de implementatie is voltooid, kunt u deze selecteren om het dash board ervan weer te geven.

![Statusdashboard voor update-implementatie voor een specifieke implementatie](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Onder **Update resultaten**bevat een samen vatting het totale aantal updates en de implementatie resultaten op de doel-vm's. In de tabel aan de rechter kant ziet u een gedetailleerde uitsplitsing van de updates en de installatie resultaten voor elke.

De beschikbare waarden zijn:

* **Niet geprobeerd** : de update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was op basis van de gedefinieerde duur van het onderhouds venster.
* **Niet geselecteerd** : de update is niet geselecteerd voor implementatie.
* **Geslaagd** : de update is voltooid.
* **Mislukt** : de update is mislukt.

Selecteer **Alle logboeken** als u alle logboekvermeldingen wilt zien die tijdens de implementatie zijn gemaakt.

Selecteer **uitvoer** om de taak stroom te bekijken van het runbook dat verantwoordelijk is voor het beheren van de update-implementatie op de doel-vm's.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

## <a name="view-the-deployment-alert"></a>De implementatie waarschuwing weer geven

Wanneer de update-implementatie is voltooid, ontvangt u de waarschuwing die u tijdens de installatie hebt opgegeven voor de implementatie. Dit is bijvoorbeeld een e-mail bericht waarin een patch wordt bevestigd.

![E-mailactiegroep configureren](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [limiet updatebeheer-implementatie bereik](automation-scope-configurations-update-management.md)voor meer informatie over Scope configuraties.
* Als u wilt zoeken naar logboeken die zijn opgeslagen in uw Log Analytics-werk ruimte, raadpleegt u [Zoek opdrachten in Logboeken in azure monitor logboeken](../azure-monitor/log-query/log-query-overview.md).
* Als u klaar bent met implementaties, raadpleegt u de [werk ruimte ontkoppelen van het Automation-account voor updatebeheer](automation-unlink-workspace-update-management.md).
* Zie [Vm's verwijderen uit updatebeheer](automation-remove-vms-from-update-management.md)om uw vm's uit updatebeheer te verwijderen.
* Zie [Problemen met Updatebeheer oplossen](troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.
* Zie [Problemen met Windows-updateagent oplossen](troubleshoot/update-agent-issues.md)voor informatie over het oplossen van problemen met de Windows Update-agent.
* Zie problemen met de [Linux-Update agent oplossen](troubleshoot/update-agent-issues-linux.md)voor informatie over het oplossen van problemen met de Linux-Update Agent.
