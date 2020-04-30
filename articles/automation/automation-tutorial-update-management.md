---
title: Updates en patches voor uw virtuele Azure-machines beheren
description: Dit artikel bevat een overzicht van het gebruik van Azure Automation Updatebeheer voor het beheren van updates en patches voor uw Azure-en niet-Azure-Vm's.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 62c661f75aef77117a61be7e802562e6dde17ba5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604677"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Updates en patches voor uw virtuele Azure-machines beheren

Met de oplossing voor updatebeheer kunt u updates en patches beheren voor virtuele machines. In deze zelfstudie leert u hoe u snel de status van beschikbare updates beoordeelt, de installatie van vereiste updates plant, de implementatieresultaten bekijkt en een waarschuwing maakt om te controleren of de updates juist zijn toegepast.

Zie [Automation-prijzen voor updatebeheer](https://azure.microsoft.com/pricing/details/automation/)voor informatie over prijzen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een update-evaluatie bekijken
> * Waarschuwingen configureren
> * Een update-implementatie plannen
> * De resultaten van een implementatie bekijken

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* De [updatebeheer](automation-update-management.md) oplossing is ingeschakeld voor een of meer van uw virtuele machines.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md) voor de onboarding.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="view-update-assessment"></a>Update-evaluatie bekijken

Nadat u Updatebeheer hebt ingeschakeld, wordt de pagina update beheer geopend. Als er updates worden geïdentificeerd als ontbrekend, wordt een lijst met ontbrekende updates weer gegeven op het tabblad **ontbrekende updates** .

Selecteer onder **informatie koppeling**de koppeling bijwerken om het ondersteunings artikel voor de update te openen. U kunt belang rijke informatie over de update ontdekken.

![Updatestatus bekijken](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Klik ergens anders in de update om het deel venster zoeken in Logboeken te openen voor de geselecteerde update. De query voor zoeken in logboeken is vooraf gedefinieerd voor deze specifieke update. U kunt deze query wijzigen of uw eigen query maken om gedetailleerde informatie weer te geven over de geïmplementeerde of ontbrekende updates in uw omgeving.

![Updatestatus bekijken](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Waarschuwingen configureren

In deze stap leert u een waarschuwing instellen zodat u de status van een update-implementatie kent.

### <a name="alert-conditions"></a>Meldingsvoorwaarden

Ga in uw Automation-account naar **waarschuwingen** onder **bewaking**en klik vervolgens op **nieuwe waarschuwings regel**.

Uw Automation-account is al geselecteerd als de resource. Als u deze wilt wijzigen, klikt u op **selecteren**. Kies op de pagina een resource selecteren de optie **Automation-accounts** in het vervolg keuzemenu **filteren op resource type** . Selecteer uw Automation-account en klik vervolgens op **gereed**.

Klik op **Voorwaarde toevoegen** om het signaal te selecteren dat geschikt is voor de update-implementatie. In de volgende tabel ziet u de details van de twee beschik bare signalen.

|Signaalnaam|Dimensies|Beschrijving|
|---|---|---|
|`Total Update Deployment Runs`|- Naam van update-implementatie<br>- Status|Dit signaal waarschuwt over de algehele status van een update-implementatie.|
|`Total Update Deployment Machine Runs`|- Naam van update-implementatie</br>- Status</br>- Doelcomputer</br>-Run-ID van update-implementatie|Dit signaal waarschuwt over de status van een update-implementatie gericht op specifieke computers.|

Selecteer een geldige waarde voor een dimensie in de lijst. Als de gewenste waarde niet voor komt in de lijst, klikt **\+** u op het teken naast de dimensie en typt u de aangepaste naam. Selecteer vervolgens de waarde waarnaar u wilt zoeken. Als u alle waarden voor een dimensie wilt selecteren, klikt u op de knop **selecteren \* ** . Als u geen waarde voor een dimensie kiest, wordt die dimensie door Updatebeheer genegeerd.

![Signaallogica configureren](./media/automation-tutorial-update-management/signal-logic.png)

Voer onder **Waarschuwingslogica** voor **Drempelwaarde** in: **1**. Wanneer u klaar bent, selecteert u **Gereed**.

### <a name="alert-details"></a>Meldingsdetails

Onder **2. Definieer waarschuwings Details**, voer een naam en beschrijving in voor de waarschuwing. Stel **Ernst** in op **Ter informatie (ernst 2)** voor een geslaagde uitvoering of **Ter informatie (ernst 1)** voor een mislukte uitvoering.

![Signaallogica configureren](./media/automation-tutorial-update-management/define-alert-details.png)

Selecteer onder **actie groepen**de optie **nieuwe maken**. Een actiegroep is een groep acties die u op meerdere waarschuwingen kunt toepassen. De acties kunnen e-mail meldingen, runbooks, webhooks en nog veel meer zijn. Zie [actie groepen maken en beheren](../azure-monitor/platform/action-groups.md)voor meer informatie over actie groepen.

Voer in het veld **naam van actie groep** een naam in voor de waarschuwing en een korte naam. Updatebeheer gebruikt de korte naam in plaats van de naam van een volledige actie groep bij het verzenden van meldingen met de opgegeven groep.

Onder **acties**voert u een naam in voor de actie, zoals **e-mail meldingen**. Selecteer voor **actie type** **e-mail/SMS/push/Voice**. Selecteer **Details bewerken**voor **meer informatie**.

Voer in het deelvenster E-mail/sms/push/spraak een naam in. Selecteer het selectie vakje **e-mail** en voer een geldig e-mail adres in.

![Een e-mailactiegroep configureren](./media/automation-tutorial-update-management/configure-email-action-group.png)

Klik in het deel venster E-mail/SMS/push/Voice op **OK**. Klik op **OK**in het deel venster actie groep toevoegen.

Als u het onderwerp van de waarschuwings-e-mail wilt aanpassen, selecteert **u onder** **acties aanpassen**de optie **e-mail onderwerp**. Wanneer u klaar bent, selecteert u **Waarschuwingsregel maken**. De waarschuwing laat u weten wanneer een update-implementatie slaagt en welke computers deel uitmaken van de update-implementatie.

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Vervolgens plant u een implementatie volgens de releaseplanning en het servicevenster om updates te installeren. U kunt kiezen welke update typen u in de implementatie wilt gebruiken. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

>[!NOTE]
>Bij het plannen van een update-implementatie wordt een [plannings](shared-resources/schedules.md) resource gemaakt die is gekoppeld aan het **patch-MicrosoftOMSComputers-** runbook dat de update-implementatie op de doel computers verwerkt. Als u de plannings resource uit de Azure Portal verwijdert of Power shell na het maken van de implementatie gebruikt, wordt de geplande update-implementatie door de verwijdering verbroken en wordt er een fout melding weer gegeven wanneer u probeert de plannings resource opnieuw te configureren vanuit de portal. U kunt de plannings resource alleen verwijderen door het bijbehorende implementatie schema te verwijderen.  

Als u een nieuwe update-implementatie voor de VM wilt plannen, gaat u naar **Updatebeheer** en selecteert u vervolgens **Update-implementatie plannen**.

Geef onder **Nieuwe update-implementatie** de volgende informatie op:

* **Naam**: voer een unieke naam in voor de update-implementatie.

* **Besturingssysteem**: selecteer het besturingssysteem dat het doel is voor de update-implementatie.

* **Groepen die moeten worden bijgewerkt (preview)**: Definieer een query die het abonnement, de resource groepen, locaties en Tags combineert om een dynamische groep virtuele Azure-machines samen te stellen die in uw implementatie moeten worden meegenomen. Zie [dynamische groepen](automation-update-management-groups.md)voor meer informatie.

* **Machines die moeten worden bijgewerkt**: Selecteer een opgeslagen zoek opdracht, geïmporteerde groep of kies **machines** in de vervolg keuzelijst en selecteer afzonderlijke machines. Als u **computers**kiest, wordt de gereedheid van elke machine weer gegeven in de kolom **gereedheids Update Agent** . Zie [computer groepen in azure monitor-logboeken](../azure-monitor/platform/computer-groups.md)voor meer informatie over de verschillende methoden voor het maken van computer groepen in azure monitor-Logboeken.

* **Update classificatie**: voor elk product deselecteert u alle ondersteunde update classificaties, maar u kunt ze ook in uw update-implementatie toevoegen. Voor deze zelf studie moet u alle typen selecteren die voor alle producten zijn geselecteerd.

  De classificatietypen zijn:

   |OS  |Type  |
   |---------|---------|
   |Windows     | Essentiële updates</br>Beveiligingsupdates</br>Updatepakketten</br>Functiepakketten</br>Servicepacks</br>Definitie-updates</br>Hulpprogramma's</br>Updates<br>Stuurprogramma        |
   |Linux     | Essentiële en beveiligingsupdates</br>Andere Updates       |

   Zie [Update classificaties](automation-view-update-assessments.md#update-classifications)voor beschrijvingen van de classificatie typen.

* **Updates die moeten worden opgenomen/uitgesloten** : Hiermee opent u de pagina include/exclude. Updates die moeten worden opgenomen of uitgesloten, bevinden zich op afzonderlijke tabbladen door de KB-artikel-ID-nummers op te geven. Wanneer u een of meer ID-nummers opgeeft, moet u alle classificaties verwijderen of uitschakelen met de update-implementatie. Dit zorgt ervoor dat er geen andere updates zijn opgenomen in uw update pakket wanneer u update-Id's opgeeft.

> [!NOTE]
> Het is belang rijk te weten dat uitsluitingen insluitingen opheffen. Als u bijvoorbeeld een uitsluitings regel van `*`opgeeft, worden er door updatebeheer geen patches of pakketten geïnstalleerd, aangezien deze allemaal zijn uitgesloten. Uitgesloten patches worden nog steeds weer gegeven als ontbrekend van de machine. Als u voor Linux-machines een pakket opneemt dat een afhankelijk pakket bevat dat is uitgesloten, Updatebeheer het hoofd pakket niet installeren.

> [!NOTE]
> U kunt geen updates opgeven die zijn vervangen om te worden opgenomen in de update-implementatie.
>

* **Planningsinstellingen**: het deelvenster Planningsinstellingen wordt geopend. De standaard begintijd is 30 minuten na de huidige tijd. U kunt de starttijd op elke gewenste tijd instellen, maar minstens 10 minuten na de huidige tijd.

   U kunt ook opgeven dat de implementatie eenmaal moet worden uitgevoerd, of u kunt een planning met meerdere implementaties instellen. Selecteer onder **Terugkeerpatroon** de optie **Eenmaal**. Wijzig de standaard instelling als 1 dag en klik op **OK**. Deze vermeldingen stellen een terugkerende planning in.

* **Voorafgaande scripts en navolgende scripts**: selecteer de scripts die moeten worden uitgevoerd vóór en na de implementatie. Zie[Manage Pre and Post scripts](pre-post-scripts.md) (Voorafgaande en navolgende scripts beheren) voor meer informatie.

* **Onderhoudsvenster (minuten)**: laat hier de standaardwaarde staan. Onderhouds Vensters bepalen de hoeveelheid tijd die is toegestaan voor het installeren van updates. Houd rekening met de volgende details wanneer u een onderhouds venster opgeeft:

  * Onderhouds Vensters bepalen hoeveel updates worden geïnstalleerd.
  * Updatebeheer niet stopt met het installeren van nieuwe updates als het einde van een onderhouds venster nadert.
  * Updatebeheer wordt niet beëindigd als er updates worden uitgevoerd als het onderhouds venster wordt overschreden.
  * Als het onderhouds venster op Windows is overschreden, is het vaak een update van een service pack lang te maken.

  > [!NOTE]
  > Om te voor komen dat updates buiten een onderhouds venster op Ubuntu worden toegepast, moet u het pakket voor de upgrade zonder toezicht opnieuw configureren om automatische updates uit te scha kelen. Zie [Automatische updates onderwerp in de hand leiding voor de Ubuntu-Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)voor meer informatie over het configureren van het pakket.

* **Opties voor opnieuw opstarten**: gebruik om opties op te geven voor het afhandelen van opnieuw opstarten. De volgende opties zijn beschikbaar:
  * Opnieuw opstarten indien nodig (standaard)
  * Altijd opnieuw opstarten
  * Nooit opnieuw opstarten
  * Alleen opnieuw opstarten: er worden geen updates geïnstalleerd

> [!NOTE]
> De register sleutels onder [register sleutels die worden gebruikt voor het beheren van opnieuw opstarten](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kunnen een gebeurtenis voor opnieuw opstarten veroorzaken als de **Opties voor opnieuw** opstarten zijn ingesteld op **nooit opnieuw opstarten**.

Wanneer u klaar bent met het configureren van de planning, klikt u op **maken**.

![Deelvenster Planningsinstellingen bijwerken](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

U keert nu terug naar het statusdashboard. Selecteer **geplande update-implementaties** om het implementatie schema dat zojuist is gemaakt weer te geven.

> [!NOTE]
> Updatebeheer ondersteunt het implementeren van updates voor de eerste partij en het downloaden van patches. Deze ondersteuning vereist wijzigingen op de systemen die worden bijgewerkt. Zie [ondersteuning voor de eerste partij en vooraf downloaden](automation-configure-windows-update.md) voor meer informatie over het configureren van deze instellingen op uw systemen.

U kunt ook programmatisch update-implementaties maken. Zie [Software-update configuraties-maken](/rest/api/automation/softwareupdateconfigurations/create)voor meer informatie over het maken van een update-implementatie met behulp van de rest API. Er is ook een voor beeld van een runbook dat u kunt gebruiken om een wekelijkse update-implementatie te maken. Zie [een wekelijkse update-implementatie maken voor een of meer virtuele machines in een resource groep](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)voor meer informatie over dit runbook.

## <a name="view-results-of-an-update-deployment"></a>Resultaten van een update-implementatie weergeven

Nadat de geplande implementatie is gestart, ziet u de status van deze implementatie op het tabblad **Update-implementaties** onder **Updatebeheer**. Als de implementatie momenteel wordt uitgevoerd, is de status **Wordt uitgevoerd**. Wanneer de implementatie is voltooid, verandert de status in **geslaagd**. Als er fouten zijn met een of meer updates in de implementatie, is de status **gedeeltelijk mislukt**.

Selecteer de voltooide update-implementatie om het dash board ervan weer te geven.

![Statusdashboard voor update-implementatie voor een specifieke implementatie](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Onder **Updateresultaten** ziet u een overzicht van het totale aantal updates en van de implementatieresultaten op de VM. In de tabel aan de rechterkant vindt u gedetailleerde informatie over elke update en het resultaat van de installatie.

De beschik bare waarden zijn:

* **Niet geprobeerd**: de update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was op basis van de opgegeven duur van het onderhouds venster.
* **Geslaagd**: de update is voltooid.
* **Mislukt**: de update is mislukt.

Selecteer **alle logboeken** om alle logboek vermeldingen weer te geven die door de implementatie zijn gemaakt.

Selecteer **Uitvoer** om de taakstroom te zien van het runbook dat verantwoordelijk is voor het beheer van de update-implementatie op de doel-VM.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

Wanneer de update-implementatie is voltooid, ontvangt u een e-mail bericht dat lijkt op het volgende:

![E-mailactiegroep configureren](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Onboarding voor Updatebeheer uitvoeren voor een VM 
> * Een update-evaluatie bekijken
> * Waarschuwingen configureren
> * Een update-implementatie plannen
> * De resultaten van een implementatie bekijken

Ga door naar het overzicht van de oplossing voor updatebeheer.

> [!div class="nextstepaction"]
> [Oplossing voor updatebeheer](automation-update-management.md)