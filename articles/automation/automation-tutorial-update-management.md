---
title: Updates en patches voor uw virtuele Azure-machines beheren
description: Dit artikel bevat een overzicht van het gebruik van Azure Automation Updatebeheer voor het beheren van updates en patches voor uw Azure-en niet-Azure-Vm's.
services: automation
author: mgoedtel
ms.service: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 65ce4234da3f44de11522a626d2c0d10524e4673
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278782"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Updates en patches voor uw virtuele Azure-machines beheren

Met de oplossing voor updatebeheer kunt u updates en patches beheren voor virtuele machines. In deze zelfstudie leert u hoe u snel de status van beschikbare updates beoordeelt, de installatie van vereiste updates plant, de implementatieresultaten bekijkt en een waarschuwing maakt om te controleren of de updates juist zijn toegepast.

Zie [Automation-prijzen voor updatebeheer](https://azure.microsoft.com/pricing/details/automation/) voor prijsinformatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Onboarding voor Updatebeheer uitvoeren voor een VM
> * Een update-evaluatie bekijken
> * Waarschuwingen configureren
> * Een update-implementatie plannen
> * De resultaten van een implementatie bekijken

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw Azure-tegoed voor Visual Studio-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u registreren voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een [Azure Automation-account](automation-offering-get-started.md) voor het opslaan van de watcher- en actie-runbooks en de Watcher-taak.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md) voor de onboarding.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="enable-update-management"></a>Updatebeheer inschakelen

Schakel voor deze zelfstudie eerst updatebeheer in op de VM:

1. In het menu [Azure Portal](https://portal.azure.com) selecteert u **virtuele machines** of zoekt en selecteert u **virtuele machines** op de **Start** pagina.
1. Selecteer de virtuele machine waarvoor u Updatebeheer wilt inschakelen.
1. Selecteer op de VM-pagina, onder **BEWERKINGEN** de optie **Updatebeheer**. Het deelvenster **Updatebeheer inschakelen** wordt geopend.

Er wordt een validatie uitgevoerd om te bepalen of updatebeheer is ingeschakeld voor deze VM. Deze validatie omvat controles voor een Log Analytics werkruimte en een gekoppeld Automation-account, en of de Updatebeheer oplossing is ingeschakeld in de werk ruimte.

Een [Log Analytics](../azure-monitor/platform/data-platform-logs.md)-werkruimte wordt gebruikt om gegevens te verzamelen die worden gegenereerd met functies en services zoals Updatebeheer. De werkruimte biedt één locatie om gegevens uit meerdere bronnen te bekijken en te analyseren.

Tijdens het validatie proces wordt ook gecontroleerd of de virtuele machine is ingericht met de Log Analytics-agent en de automatiserings Hybrid Runbook Worker. Deze agent wordt gebruikt om te communiceren met Azure Automation en om informatie op te vragen over de status van de update. De agent vereist dat poort 443 is geopend, zodat met de Azure Automation-service kan worden gecommuniceerd en updates kunnen worden gedownload.

Als een van de volgende vereiste onderdelen ontbreekt na de onboarding, wordt dit automatisch toegevoegd:

* [Log Analytics](../azure-monitor/platform/data-platform-logs.md)-werkruimte
* Een [Automation-account](./automation-offering-get-started.md)
* Een [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) (ingeschakeld op de VM)

Stel onder **Updatebeheer** het volgende in: de locatie, de Log Analytics-werkruimte en het Automation-account dat moet worden gebruikt. Selecteer vervolgens **Inschakelen**. Als deze opties niet beschikbaar zijn, betekent dit dat er een andere automatiseringsoplossing is ingeschakeld voor de VM. In dit geval moeten dezelfde werkruimte en hetzelfde Automation-account worden gebruikt.

![Schakel het venster voor de oplossing voor updatebeheer in](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

Het inschakelen van de oplossing kan enkele minuten duren. Sluit gedurende deze tijd het browservenster niet. Nadat de oplossing is ingeschakeld, wordt informatie over ontbrekende updates op de VM naar Azure Monitor-logboeken verzonden. Het duurt tussen 30 minuten en 6 uur voordat de gegevens beschikbaar zijn voor analyse.

## <a name="view-update-assessment"></a>Update-evaluatie bekijken

Nadat Updatebeheer is ingeschakeld, wordt het deelvenster **Updatebeheer** geopend. Als er updates worden geïdentificeerd als ontbrekend, wordt een lijst met ontbrekende updates weer gegeven op het tabblad **ontbrekende updates** .

Selecteer onder **informatie koppeling**de koppeling bijwerken om het ondersteunings artikel voor de update te openen. U kunt belang rijke informatie over de update ontdekken.

![Updatestatus bekijken](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Klik ergens anders in de update om het deelvenster **Zoeken in logboeken** te openen voor de geselecteerde update. De query voor zoeken in logboeken is vooraf gedefinieerd voor deze specifieke update. U kunt deze query wijzigen of uw eigen query maken om gedetailleerde informatie weer te geven over de updates die zijn geïmplementeerd of die ontbreken in uw omgeving.

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

Voer onder **Acties** een naam in voor de actie, bijvoorbeeld **E-mailmeldingen**. Selecteer onder **ACTIETYPE** de optie **E-mail/sms/push/spraak**. Selecteer onder **DETAILS** de optie **Details bewerken**.

Voer in het deelvenster **E-mail/sms/push/spraak** een naam in. Selecteer het selectievakje **E-mail** en voer vervolgens een geldig e-mailadres in.

![Een e-mailactiegroep configureren](./media/automation-tutorial-update-management/configure-email-action-group.png)

Selecteer **OK** in het deelvenster **E-mail/sms/push/spraak**. Selecteer **OK** in het deelvenster **Actiegroep toevoegen**.

Selecteer onder **Regel maken** bij **Acties aanpassen** de optie **E-mailonderwerp** als u het onderwerp van het waarschuwingsbericht wilt aanpassen. Wanneer u klaar bent, selecteert u **Waarschuwingsregel maken**. De waarschuwing laat u weten wanneer een update-implementatie is voltooid en op welke machines deze update-implementatie is uitgevoerd.

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Vervolgens plant u een implementatie volgens de releaseplanning en het servicevenster om updates te installeren. U kunt kiezen welke typen updates moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

Als u een nieuwe update-implementatie voor de VM wilt plannen, gaat u naar **Updatebeheer** en selecteert u vervolgens **Update-implementatie plannen**.

Geef onder **Nieuwe update-implementatie** de volgende informatie op:

* **Naam**: voer een unieke naam in voor de update-implementatie.

* **Besturingssysteem**: selecteer het besturingssysteem dat het doel is voor de update-implementatie.

* **Groepen om bij te werken (preview)** : definieer een query op basis van een combinatie van abonnement, resourcegroepen, locaties en tags om een dynamische groep virtuele Azure-machines te bouwen voor opname in uw implementatie. Zie [Dynamische groepen](automation-update-management-groups.md) voor meer informatie

* **Bij te werken computers**: selecteer een opgeslagen zoekopdracht, geïmporteerde groep of kies een computer in de vervolgkeuzelijst en selecteer de afzonderlijke computers. Als u **Computers** selecteert, wordt de gereedheid van de computer weergegeven in de kolom **GEREEDHEID VOOR UPDATE-AGENT**. Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken

* **Updateclassificatie**: selecteer de typen software die de update-implementatie moet opnemen in de implementatie. Voor deze zelfstudie laat u alle typen geselecteerd.

  De classificatietypen zijn:

   |Besturingssysteem  |Type  |
   |---------|---------|
   |Windows     | Essentiële updates</br>Beveiligingsupdates</br>Updatepakketten</br>Functiepakketten</br>Servicepacks</br>Definitie-updates</br>Hulpprogramma's</br>Updates        |
   |Linux     | Essentiële en beveiligingsupdates</br>Andere Updates       |

   Raadpleeg [Updateclassificaties](automation-view-update-assessments.md#update-classifications) voor een beschrijving van de classificatietypen.

* **Updates om op te nemen/uit te sluiten**: hiermee opent u de pagina **Opnemen/uitsluiten**. Updates die moeten worden opgenomen of uitgesloten, worden op afzonderlijke tabbladen weergegeven.

> [!NOTE]
> Het is belang rijk te weten dat uitsluitingen insluitingen opheffen. Als u bijvoorbeeld een uitsluitings regel van `*`definieert, worden er geen patches of pakketten geïnstalleerd, aangezien deze allemaal uitgesloten zijn. Uitgesloten patches worden nog steeds weer gegeven als ontbrekend van de machine. Voor Linux-machines als een pakket is opgenomen, maar een afhankelijk pakket heeft dat is uitgesloten, is het pakket niet geïnstalleerd.

* **Planningsinstellingen**: het deelvenster **Planningsinstellingen** wordt geopend. De standaard begintijd is 30 minuten na de huidige tijd. U kunt de starttijd op elke gewenste tijd instellen, maar minstens 10 minuten na de huidige tijd.

   U kunt ook opgeven dat de implementatie eenmaal moet worden uitgevoerd, of u kunt een planning met meerdere implementaties instellen. Selecteer onder **Terugkeerpatroon** de optie **Eenmaal**. Laat de standaardwaarde staan op 1 dag en selecteer **OK**. Hiermee stelt u een terugkerend schema in.

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

