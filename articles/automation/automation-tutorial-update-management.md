---
title: Updates en patches voor uw virtuele Azure-machines beheren
description: Dit artikel bevat een overzicht van het gebruik van Azure Automation Updatebeheer voor het beheren van updates en patches voor uw Azure-en niet-Azure-Vm's.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: mvc
ms.openlocfilehash: 347f2fbc0f12aa775c42dbb14a4625dc509a20ed
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239656"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Updates en patches voor uw virtuele Azure-machines beheren

Met de oplossing voor updatebeheer kunt u updates en patches beheren voor virtuele machines. In deze zelfstudie leert u hoe u snel de status van beschikbare updates beoordeelt, de installatie van vereiste updates plant, de implementatieresultaten bekijkt en een waarschuwing maakt om te controleren of de updates juist zijn toegepast.

Zie [Automation-prijzen voor updatebeheer](https://azure.microsoft.com/pricing/details/automation/) voor prijsinformatie.

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

Nadat Updatebeheer is ingeschakeld, wordt het deelvenster **Updatebeheer** geopend. Als er updates worden geïdentificeerd als ontbrekend, wordt een lijst met ontbrekende updates weer gegeven op het tabblad **ontbrekende updates** .

Selecteer onder **informatie koppeling**de koppeling bijwerken om het ondersteunings artikel voor de update te openen. U kunt belang rijke informatie over de update ontdekken.

![Updatestatus bekijken](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Klik ergens anders in de update om het deel venster **Zoeken in Logboeken** te openen voor de geselecteerde update. De query voor zoeken in logboeken is vooraf gedefinieerd voor deze specifieke update. U kunt deze query wijzigen of uw eigen query maken om gedetailleerde informatie weer te geven over de updates die zijn geïmplementeerd of die ontbreken in uw omgeving.

![Updatestatus bekijken](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Waarschuwingen configureren

In deze stap leert u een waarschuwing instellen zodat u de status van een update-implementatie kent.

### <a name="alert-conditions"></a>Meldingsvoorwaarden

Ga in uw Automation-account, onder **Bewaking**, naar **Waarschuwingen** en klik op **+ Nieuwe waarschuwingsregel**.

Het Automation-account is al als resource geselecteerd. Als u het wilt wijzigen, kunt u op **Selecteren** klikken en selecteert u op de pagina **Select a resource** de optie **Automation-accounts** in de vervolgkeuzelijst **Filter by resource type**. Selecteer uw Automation-account en selecteer **Gereed**.

Klik op **Voorwaarde toevoegen** om het signaal te selecteren dat geschikt is voor de update-implementatie. In de volgende tabel vindt u de details van de twee beschikbare signalen voor update-implementaties:

|Signaalnaam|Dimensies|Beschrijving|
|---|---|---|
|**Totaal aantal uitvoeringen van update-implementaties**|- Naam van update-implementatie</br>- Status|Dit signaal wordt gebruikt om een waarschuwing af te geven over de algemene status van een update-implementatie.|
|**Totaal aantal machine-uitvoeringen van update-implementaties**|- Naam van update-implementatie</br>- Status</br>- Doelcomputer</br>-Run-ID van update-implementatie|Dit signaal wordt gebruikt om een waarschuwing af te geven over de status van een update-implementatie die voor bepaalde computers is bedoeld|

Selecteer voor de dimensiewaarden een geldige waarde in de lijst. Als de gezochte waarde niet in de lijst voorkomt, klikt u op het **\+** -teken naast de dimensie en typt u de aangepaste naam. Vervolgens kunt u de waarde selecteren die u zoekt. Als u alle waarden van een dimensie wilt selecteren, klikt u op de knop **Selecteren\*** . Als u geen waarde voor een dimensie kiest, wordt de dimensie tijdens de evaluatie genegeerd.

![Signaallogica configureren](./media/automation-tutorial-update-management/signal-logic.png)

Voer onder **Waarschuwingslogica** voor **Drempelwaarde** in: **1**. Wanneer u klaar bent, selecteert u **Gereed**.

### <a name="alert-details"></a>Meldingsdetails

Onder **2. Definieer waarschuwings Details**, voer een naam en beschrijving in voor de waarschuwing. Stel **Ernst** in op **Ter informatie (ernst 2)** voor een geslaagde uitvoering of **Ter informatie (ernst 1)** voor een mislukte uitvoering.

![Signaallogica configureren](./media/automation-tutorial-update-management/define-alert-details.png)

Selecteer onder **Actiegroepen** de optie **Nieuwe maken**. Een actiegroep is een groep acties die u op meerdere waarschuwingen kunt toepassen. Deze acties kunnen bestaan uit, maar zijn niet beperkt tot, e-mailmeldingen, runbooks, webhooks en nog veel meer. Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/platform/action-groups.md) voor meer informatie over actiegroepen.

Voer in het vak **Naam van actiegroep** een naam in voor de waarschuwing en een korte naam. De korte naam wordt gebruikt in plaats van een volledige naam voor de actiegroep als er meldingen worden verzonden door deze groep te gebruiken.

Voer onder **acties**een naam in voor de actie, zoals **e-mail meldingen**. Onder **actie type**selecteert u **e-mail/SMS/push/Voice**. Selecteer **Details bewerken**onder **Details**.

Voer in het deelvenster **E-mail/sms/push/spraak** een naam in. Selecteer het selectievakje **E-mail** en voer vervolgens een geldig e-mailadres in.

![Een e-mailactiegroep configureren](./media/automation-tutorial-update-management/configure-email-action-group.png)

Selecteer **OK**in het deel venster **e-mail/SMS/push/Voice** . Selecteer **OK**in het deel venster **actie groep toevoegen** .

Als u het onderwerp van de waarschuwings-e-mail wilt aanpassen, selecteert **u onder** **acties aanpassen**de optie **e-mail onderwerp**. Wanneer u klaar bent, selecteert u **Waarschuwingsregel maken**. De waarschuwing laat u weten wanneer een update-implementatie is voltooid en op welke machines deze update-implementatie is uitgevoerd.

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Vervolgens plant u een implementatie volgens de releaseplanning en het servicevenster om updates te installeren. U kunt kiezen welke typen updates moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

>[!NOTE]
>Wanneer u een update-implementatie plant, wordt er een [plannings](shared-resources/schedules.md) resource gemaakt die is gekoppeld aan het **patch-MicrosoftOMSComputers-** runbook dat de update-implementatie op de doel computers afhandelt. Als u de plannings resource uit de Azure Portal verwijdert of Power shell na het maken van de implementatie gebruikt, wordt de geplande update-implementatie verbroken en wordt er een fout weer gegeven wanneer u deze opnieuw probeert te configureren vanuit de portal. U kunt de plannings resource alleen verwijderen door het bijbehorende implementatie schema te verwijderen.  
>

Als u een nieuwe update-implementatie voor de VM wilt plannen, gaat u naar **Updatebeheer** en selecteert u vervolgens **Update-implementatie plannen**.

Geef onder **Nieuwe update-implementatie** de volgende informatie op:

* **Naam**: voer een unieke naam in voor de update-implementatie.

* **Besturingssysteem**: selecteer het besturingssysteem dat het doel is voor de update-implementatie.

* **Groepen om bij te werken (preview)** : definieer een query op basis van een combinatie van abonnement, resourcegroepen, locaties en tags om een dynamische groep virtuele Azure-machines te bouwen voor opname in uw implementatie. Zie [Dynamische groepen](automation-update-management-groups.md) voor meer informatie

* **Bij te werken computers**: selecteer een opgeslagen zoekopdracht, geïmporteerde groep of kies een computer in de vervolgkeuzelijst en selecteer de afzonderlijke computers. Als u **computers**kiest, wordt de gereedheid van de machine weer gegeven in de kolom **gereedheid van de Update Agent** . Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken

* **Update classificatie**: Selecteer de ondersteunde update classificaties die beschikbaar zijn voor elk product dat kan worden opgenomen in de update-implementatie. Voor deze zelfstudie laat u alle typen geselecteerd.

  De classificatietypen zijn:

   |OS  |Type  |
   |---------|---------|
   |Windows     | Essentiële updates</br>Beveiligingsupdates</br>Updatepakketten</br>Functiepakketten</br>Servicepacks</br>Definitie-updates</br>Hulpprogramma's</br>Updates        |
   |Linux     | Essentiële en beveiligingsupdates</br>Andere Updates       |

   Raadpleeg [Updateclassificaties](automation-view-update-assessments.md#update-classifications) voor een beschrijving van de classificatietypen.

* **Updates om op te nemen/uit te sluiten**: hiermee opent u de pagina **Opnemen/uitsluiten**. Updates die moeten worden opgenomen of uitgesloten, worden op afzonderlijke tabbladen weergegeven.

> [!NOTE]
> Het is belang rijk te weten dat uitsluitingen insluitingen opheffen. Als u bijvoorbeeld een uitsluitings regel van `*`definieert, worden er geen patches of pakketten geïnstalleerd, aangezien deze allemaal uitgesloten zijn. Uitgesloten patches worden nog steeds weer gegeven als ontbrekend van de machine. Voor Linux-machines als een pakket is opgenomen, maar een afhankelijk pakket heeft dat is uitgesloten, is het pakket niet geïnstalleerd.

> [!NOTE]
> U kunt geen updates opgeven die zijn vervangen om te worden opgenomen in de update-implementatie.
>

* **Planningsinstellingen**: het deelvenster **Planningsinstellingen** wordt geopend. De standaard begintijd is 30 minuten na de huidige tijd. U kunt de starttijd op elke gewenste tijd instellen, maar minstens 10 minuten na de huidige tijd.

   U kunt ook opgeven dat de implementatie eenmaal moet worden uitgevoerd, of u kunt een planning met meerdere implementaties instellen. Selecteer onder **Terugkeerpatroon** de optie **Eenmaal**. Wijzig de standaard instelling als 1 dag en selecteer **OK**. Hiermee stelt u een terugkerend schema in.

* **Voorafgaande scripts en navolgende scripts**: selecteer de scripts die moeten worden uitgevoerd vóór en na de implementatie. Zie[Manage Pre and Post scripts](pre-post-scripts.md) (Voorafgaande en navolgende scripts beheren) voor meer informatie.

* **Onderhoudsvenster (minuten)** : laat hier de standaardwaarde staan. Onderhouds Vensters bepalen de hoeveelheid tijd die is toegestaan voor het installeren van updates. Houd rekening met de volgende details wanneer u een onderhouds venster opgeeft.

  * Onderhouds Vensters bepalen hoeveel updates worden geïnstalleerd.
  * Updatebeheer stopt met het installeren van nieuwe updates als het einde van een onderhouds venster nadert.
  * Met Updatebeheer worden lopende updates niet beëindigd als het onderhouds venster wordt overschreden.
  * Als het onderhouds venster op Windows is overschreden, is het vaak een update van een service pack dat veel tijd kost om te installeren.

  > [!NOTE]
  > Om te voor komen dat updates buiten een onderhouds venster op Ubuntu worden toegepast, moet u het pakket voor de upgrade zonder toezicht opnieuw configureren om automatische updates uit te scha kelen. Zie [Automatische updates onderwerp in de hand leiding voor de Ubuntu-Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)voor meer informatie over het configureren van het pakket.

* **Opties voor opnieuw opstarten**: met deze instelling wordt bepaald hoe het opnieuw opstarten moet worden verwerkt. De volgende opties zijn beschikbaar:
  * Opnieuw opstarten indien nodig (standaard)
  * Altijd opnieuw opstarten
  * Nooit opnieuw opstarten
  * Alleen opnieuw opstarten - updates worden niet geïnstalleerd

> [!NOTE]
> De register sleutels onder [register sleutels die worden gebruikt voor het beheren van opnieuw opstarten](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kunnen een gebeurtenis voor opnieuw opstarten veroorzaken als **het besturings element voor opnieuw** opstarten is ingesteld op **nooit opnieuw opstarten**.

Als u klaar bent met het configureren van de planning, selecteert u **Maken**.

![Deelvenster Planningsinstellingen bijwerken](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

U keert nu terug naar het statusdashboard. Selecteer **Geplande update-implementaties** om de gemaakte implementatieplanning weer te geven.

> [!NOTE]
> Updatebeheer biedt ondersteuning voor het implementeren van eerste partij-updates en het vooraf downloaden van patches. Hiervoor is vereist dat wijzigingen in de systemen worden hersteld. Zie [ondersteuning voor eerste partijen en vooraf downloaden](automation-configure-windows-update.md) voor informatie over het configureren van deze instellingen in uw systemen.

**Update-implementaties** kunnen ook programmatisch worden gemaakt. Zie [Software-update configuraties-maken](/rest/api/automation/softwareupdateconfigurations/create)voor meer informatie over het maken van een **Update-implementatie** met behulp van de rest API. Er is ook een voor beeld van een runbook dat kan worden gebruikt voor het maken van een wekelijkse **Update-implementatie**. Zie [een wekelijkse update-implementatie maken voor een of meer virtuele machines in een resource groep](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)voor meer informatie over dit runbook.

## <a name="view-results-of-an-update-deployment"></a>Resultaten van een update-implementatie weergeven

Nadat de geplande implementatie is gestart, ziet u de status van deze implementatie op het tabblad **Update-implementaties** onder **Updatebeheer**. Als de implementatie momenteel wordt uitgevoerd, is de status **Wordt uitgevoerd**. Als de implementatie correct is voltooid, wordt de status gewijzigd in **Geslaagd**. Als er fouten optreden met één of meer updates in de implementatie, verandert de status in **Gedeeltelijk mislukt**.

Selecteer de voltooide update-implementatie om het dashboard voor de betreffende update-implementatie te bekijken.

![Statusdashboard voor update-implementatie voor een specifieke implementatie](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Onder **Updateresultaten** ziet u een overzicht van het totale aantal updates en van de implementatieresultaten op de VM. In de tabel aan de rechterkant vindt u gedetailleerde informatie over elke update en het resultaat van de installatie.

De volgende lijst toont de beschikbare waarden:

* **Niet geprobeerd**: de update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was op basis van de opgegeven onderhoudsperiode.
* **Geslaagd**: de update is voltooid.
* **Mislukt**: de update is mislukt.

Selecteer **Alle logboeken** als u alle logboekvermeldingen wilt zien die tijdens de implementatie zijn gemaakt.

Selecteer **Uitvoer** om de taakstroom te zien van het runbook dat verantwoordelijk is voor het beheer van de update-implementatie op de doel-VM.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

Zodra de update-implementatie is voltooid, wordt er een e-mail verzonden die vergelijkbaar is met het volgende voorbeeld, om aan te geven dat de implementatie is geslaagd:

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
> [Oplossing voor updatebeheer](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)

