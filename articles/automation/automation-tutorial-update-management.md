---
title: Updates en patches voor uw Azure VM's beheren
description: In dit artikel vindt u een overzicht van het gebruik van Azure Automation Update Management om updates en patches voor uw Azure- en niet-Azure VM's te beheren.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: mvc
ms.openlocfilehash: f7130f3289ce42ca1481ec14be893c846c9ed55b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80335790"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Updates en patches voor uw Azure VM's beheren

Met de oplossing voor updatebeheer kunt u updates en patches beheren voor virtuele machines. In deze zelfstudie leert u hoe u snel de status van beschikbare updates beoordeelt, de installatie van vereiste updates plant, de implementatieresultaten bekijkt en een waarschuwing maakt om te controleren of de updates juist zijn toegepast.

Zie [Automatiseringsprijzen voor Updatebeheer voor](https://azure.microsoft.com/pricing/details/automation/)prijsinformatie .

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een update-evaluatie bekijken
> * Waarschuwingen configureren
> * Een update-implementatie plannen
> * De resultaten van een implementatie bekijken

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* De [updatebeheeroplossing](automation-update-management.md) is ingeschakeld voor een of meer van uw VM's.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md) voor de onboarding.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="view-update-assessment"></a>Update-evaluatie bekijken

Nadat Updatebeheer is ingeschakeld, wordt het deelvenster **Updatebeheer** geopend. Als updates als ontbrekend worden aangemerkt, wordt een lijst met ontbrekende updates weergegeven op het tabblad **Ontbrekende updates.**

Selecteer **onder Koppeling Informatie**de koppeling Bijwerken om het ondersteuningsartikel voor de update te openen. U belangrijke informatie over de update leren.

![Updatestatus bekijken](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Klik ergens anders in de update om het **zoekvenster Logboek** voor de geselecteerde update te openen. De query voor zoeken in logboeken is vooraf gedefinieerd voor deze specifieke update. U kunt deze query wijzigen of uw eigen query maken om gedetailleerde informatie weer te geven over de updates die zijn geïmplementeerd of die ontbreken in uw omgeving.

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
|**Totaal aantal machine-uitvoeringen van update-implementaties**|- Naam van update-implementatie</br>- Status</br>- Doelcomputer</br>- Implementatie-run-id bijwerken|Dit signaal wordt gebruikt om een waarschuwing af te geven over de status van een update-implementatie die voor bepaalde computers is bedoeld|

Selecteer voor de dimensiewaarden een geldige waarde in de lijst. Als de waarde die u zoekt niet in **\+** de lijst staat, klikt u op het bord naast de dimensie en typt u de aangepaste naam. Vervolgens kunt u de waarde selecteren die u zoekt. Als u alle waarden van een dimensie wilt selecteren, klikt u op de knop **Selecteren\***. Als u geen waarde voor een dimensie kiest, wordt de dimensie tijdens de evaluatie genegeerd.

![Signaallogica configureren](./media/automation-tutorial-update-management/signal-logic.png)

Voer onder **Waarschuwingslogica** voor **Drempelwaarde** in: **1**. Wanneer u klaar bent, selecteert u **Gereed**.

### <a name="alert-details"></a>Meldingsdetails

Onder **2. Definieer waarschuwingsgegevens,** voer een naam en beschrijving in voor de waarschuwing. Stel **Ernst** in op **Ter informatie (ernst 2)** voor een geslaagde uitvoering of **Ter informatie (ernst 1)** voor een mislukte uitvoering.

![Signaallogica configureren](./media/automation-tutorial-update-management/define-alert-details.png)

Selecteer **Onder Actiegroepen**de optie **Nieuw maken**. Een actiegroep is een groep acties die u op meerdere waarschuwingen kunt toepassen. Deze acties kunnen bestaan uit, maar zijn niet beperkt tot, e-mailmeldingen, runbooks, webhooks en nog veel meer. Zie Actiegroepen maken en beheren voor meer informatie over [actiegroepen.](../azure-monitor/platform/action-groups.md)

Voer in het vak **Naam van actiegroep** een naam in voor de waarschuwing en een korte naam. De korte naam wordt gebruikt in plaats van een volledige naam voor de actiegroep als er meldingen worden verzonden door deze groep te gebruiken.

Voer **onder Acties**een naam voor de actie in, zoals **E-mailmeldingen**. Selecteer **onder Actietype** **E-mail/SMS/Push/Voice**. Selecteer **Onder Details**de optie Details **bewerken**.

Voer in het deelvenster **E-mail/sms/push/spraak** een naam in. Selecteer het selectievakje **E-mail** en voer vervolgens een geldig e-mailadres in.

![Een e-mailactiegroep configureren](./media/automation-tutorial-update-management/configure-email-action-group.png)

Selecteer **Ok**in het deelvenster **E-mail/sms/push/spraak.** Selecteer **Ok**in het deelvenster **Actiegroep toevoegen.**

Als u het onderwerp van de waarschuwingse-mail wilt aanpassen, selecteert u onder **Actie**maken onder **Acties aanpassen**de optie **E-mailonderwerp**. Wanneer u klaar bent, selecteert u **Waarschuwingsregel maken**. De waarschuwing laat u weten wanneer een update-implementatie is voltooid en op welke machines deze update-implementatie is uitgevoerd.

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Vervolgens plant u een implementatie volgens de releaseplanning en het servicevenster om updates te installeren. U kunt kiezen welke typen updates moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

>[!NOTE]
>Wanneer u een update-implementatie plant, wordt een [planningsbron](shared-resources/schedules.md) gemaakt die is gekoppeld aan de **runbook Patch-MicrosoftOMSComputers** die de update-implementatie op de doelmachines verwerkt. Als u de planningsbron verwijdert uit de Azure-portal of PowerShell gebruikt nadat u de implementatie hebt gemaakt, wordt de geplande update-implementatie afgebroken en wordt er een fout weergegeven wanneer u deze vanuit de portal opnieuw probeert te configureren. U de planningsbron alleen verwijderen door het bijbehorende implementatieschema te verwijderen.  
>

Als u een nieuwe update-implementatie voor de VM wilt plannen, gaat u naar **Updatebeheer** en selecteert u vervolgens **Update-implementatie plannen**.

Geef onder **Nieuwe update-implementatie** de volgende informatie op:

* **Naam**: voer een unieke naam in voor de update-implementatie.

* **Besturingssysteem**: selecteer het besturingssysteem dat het doel is voor de update-implementatie.

* **Groepen om bij te werken (preview)**: definieer een query op basis van een combinatie van abonnement, resourcegroepen, locaties en tags om een dynamische groep virtuele Azure-machines te bouwen voor opname in uw implementatie. Zie [Dynamische groepen](automation-update-management-groups.md) voor meer informatie

* **Bij te werken computers**: selecteer een opgeslagen zoekopdracht, geïmporteerde groep of kies een computer in de vervolgkeuzelijst en selecteer de afzonderlijke computers. Als u **Machines**kiest, wordt de gereedheid van de machine weergegeven in de kolom **Gereedheid bijwerken.** Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken

* **Updateclassificatie**: Selecteer de ondersteunde updateclassificaties die beschikbaar zijn voor elk product dat kan worden opgenomen in de update-implementatie. Voor deze zelfstudie laat u alle typen geselecteerd.

  De classificatietypen zijn:

   |OS  |Type  |
   |---------|---------|
   |Windows     | Essentiële updates</br>Beveiligingsupdates</br>Updatepakketten</br>Functiepakketten</br>Servicepacks</br>Definitie-updates</br>Hulpprogramma's</br>Updates        |
   |Linux     | Essentiële en beveiligingsupdates</br>Andere Updates       |

   Raadpleeg [Updateclassificaties](automation-view-update-assessments.md#update-classifications) voor een beschrijving van de classificatietypen.

* **Updates om op te nemen/uit te sluiten**: hiermee opent u de pagina **Opnemen/uitsluiten**. Updates die moeten worden opgenomen of uitgesloten, worden op afzonderlijke tabbladen weergegeven.

> [!NOTE]
> Het is belangrijk om te weten dat uitsluitingen inclusies overschrijven. Als u bijvoorbeeld een uitsluitingsregel definieert van `*`, worden er geen patches of pakketten geïnstalleerd omdat ze allemaal zijn uitgesloten. Uitgesloten patches nog steeds als ontbrekende van de machine. Voor Linux-machines als een pakket is inbegrepen, maar een afhankelijk pakket heeft dat is uitgesloten, is het pakket niet geïnstalleerd.

> [!NOTE]
> U geen updates opgeven die zijn vervangen voor opname met de update-implementatie.
>

* **Planningsinstellingen**: het deelvenster **Planningsinstellingen** wordt geopend. De standaard begintijd is 30 minuten na de huidige tijd. U kunt de starttijd op elke gewenste tijd instellen, maar minstens 10 minuten na de huidige tijd.

   U kunt ook opgeven dat de implementatie eenmaal moet worden uitgevoerd, of u kunt een planning met meerdere implementaties instellen. Selecteer onder **Terugkeerpatroon** de optie **Eenmaal**. Laat de standaardinstelling als 1 dag staan en selecteer **Ok**. Hiermee stelt u een terugkerend schema in.

* **Voorafgaande scripts en navolgende scripts**: selecteer de scripts die moeten worden uitgevoerd vóór en na de implementatie. Zie[Manage Pre and Post scripts](pre-post-scripts.md) (Voorafgaande en navolgende scripts beheren) voor meer informatie.

* **Onderhoudsvenster (minuten)**: laat hier de standaardwaarde staan. Onderhoudsvensters bepalen de hoeveelheid tijd die is toegestaan voor het installeren van updates. Houd rekening met de volgende details bij het opgeven van een onderhoudsvenster.

  * Onderhoudsvensters bepalen hoeveel updates worden geprobeerd te worden geïnstalleerd.
  * Updatebeheer stopt niet met het installeren van nieuwe updates als het einde van een onderhoudsvenster nadert.
  * Updatebeheer beëindigt lopende updates niet als het onderhoudsvenster wordt overschreden.
  * Als het onderhoudsvenster wordt overschreden op Windows, is dit vaak vanwege een servicepack-update die lang duurt om te installeren.

  > [!NOTE]
  > Om te voorkomen dat updates buiten een onderhoudsvenster op Ubuntu worden toegepast, configureert u het pakket Onbeheerde updates opnieuw om automatische updates uit te schakelen. Zie Onderwerp automatische updates in [de Ubuntu-servergids voor](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)informatie over het configureren van het pakket.

* **Opties voor opnieuw opstarten**: met deze instelling wordt bepaald hoe het opnieuw opstarten moet worden verwerkt. De volgende opties zijn beschikbaar:
  * Opnieuw opstarten indien nodig (standaard)
  * Altijd opnieuw opstarten
  * Nooit opnieuw opstarten
  * Alleen opnieuw opstarten - updates worden niet geïnstalleerd

> [!NOTE]
> De registersleutels die worden weergegeven onder [Registersleutels die worden gebruikt om opnieuw opstarten te beheren,](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kunnen een gebeurtenis opnieuw opstarten veroorzaken als **opnieuw opstarten** is ingesteld op **Nooit opnieuw opstarten.**

Als u klaar bent met het configureren van de planning, selecteert u **Maken**.

![Deelvenster Planningsinstellingen bijwerken](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

U keert nu terug naar het statusdashboard. Selecteer **Geplande update-implementaties** om de gemaakte implementatieplanning weer te geven.

> [!NOTE]
> Updatebeheer biedt ondersteuning voor het implementeren van eerste partij-updates en het vooraf downloaden van patches. Hiervoor is vereist dat wijzigingen in de systemen worden hersteld. Zie [ondersteuning voor eerste partijen en vooraf downloaden](automation-configure-windows-update.md) voor informatie over het configureren van deze instellingen in uw systemen.

**Implementaties bijwerken** kan ook programmatisch worden gemaakt. Zie [Software-updateconfiguraties maken voor](/rest/api/automation/softwareupdateconfigurations/create)meer informatie over het maken van een **update-implementatie** met de REST-API. Er is ook een voorbeeldrunbook dat kan worden gebruikt om een wekelijkse **update-implementatie**te maken. Zie [Een wekelijkse update-implementatie maken voor een of meer VM's in een resourcegroep](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)voor meer informatie over dit runbook.

## <a name="view-results-of-an-update-deployment"></a>Resultaten van een update-implementatie weergeven

Nadat de geplande implementatie is gestart, ziet u de status van deze implementatie op het tabblad **Update-implementaties** onder **Updatebeheer**. Als de implementatie momenteel wordt uitgevoerd, is de status **Wordt uitgevoerd**. Als de implementatie correct is voltooid, wordt de status gewijzigd in **Geslaagd**. Als er fouten optreden met één of meer updates in de implementatie, verandert de status in **Gedeeltelijk mislukt**.

Selecteer de voltooide update-implementatie om het dashboard voor de betreffende update-implementatie te bekijken.

![Statusdashboard voor update-implementatie voor een specifieke implementatie](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Onder **Updateresultaten** ziet u een overzicht van het totale aantal updates en van de implementatieresultaten op de VM. In de tabel aan de rechterkant vindt u gedetailleerde informatie over elke update en het resultaat van de installatie.

De volgende lijst toont de beschikbare waarden:

* **Niet geprobeerd**: de update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was op basis van de opgegeven onderhoudsperiode.
* **Geslaagd**: De update is geslaagd.
* **Mislukt:** de update is mislukt.

Selecteer **Alle logboeken** als u alle logboekvermeldingen wilt zien die tijdens de implementatie zijn gemaakt.

Selecteer **Uitvoer** om de taakstroom te zien van het runbook dat verantwoordelijk is voor het beheer van de update-implementatie op de doel-VM.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

Zodra de update-implementatie is voltooid, wordt er een e-mail verzonden die vergelijkbaar is met het volgende voorbeeld, om aan te geven dat de implementatie is geslaagd:

![E-mailactiegroep configureren](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Onboarding voor Updatebeheer uitvoeren voor een VM 
> * Een update-evaluatie bekijken
> * Waarschuwingen configureren
> * Een update-implementatie plannen
> * De resultaten van een implementatie bekijken

Ga door naar het overzicht van de oplossing voor updatebeheer.

> [!div class="nextstepaction"]
> [Oplossing voor updatebeheer](automation-update-management.md)

