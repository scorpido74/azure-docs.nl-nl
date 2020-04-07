---
title: Updates en patches voor uw Azure VM's beheren
description: In dit artikel vindt u een overzicht van het gebruik van Azure Automation Update Management om updates en patches voor uw Azure- en niet-Azure VM's te beheren.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 888dc99162551482afc715f1a793614d2c866384
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677037"
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

Nadat u Updatebeheer hebt ingeschakeld, wordt de pagina **Beheer bijwerken** geopend. Als updates als ontbrekend worden aangemerkt, wordt een lijst met ontbrekende updates weergegeven op het tabblad **Ontbrekende updates.**

Selecteer **onder Koppeling Informatie**de koppeling Bijwerken om het ondersteuningsartikel voor de update te openen. U belangrijke informatie over de update leren.

![Updatestatus bekijken](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Klik ergens anders in de update om het **zoekvenster Logboek** voor de geselecteerde update te openen. De query voor zoeken in logboeken is vooraf gedefinieerd voor deze specifieke update. U deze query wijzigen of uw eigen query maken om gedetailleerde informatie over updates die in uw omgeving zijn geïmplementeerd of ontbreken weer te geven.

![Updatestatus bekijken](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Waarschuwingen configureren

In deze stap leert u een waarschuwing instellen zodat u de status van een update-implementatie kent.

### <a name="alert-conditions"></a>Meldingsvoorwaarden

Ga in uw automatiseringsaccount naar **Waarschuwingen** onder **Controleren**en klik vervolgens op **Nieuwe waarschuwingsregel**.

Uw Automatiseringsaccount is al geselecteerd als resource. Als u deze wilt wijzigen, klikt u op **Selecteren**. Kies **op de** pagina Een resource selecteren de optie **Accounts automatiseren** in het vervolgkeuzemenu **Filter op resourcetype.** Selecteer uw automatiseringsaccount en klik op **Gereed**.

Klik op **Voorwaarde toevoegen** om het signaal te selecteren dat geschikt is voor de update-implementatie. De volgende tabel toont de details van de twee beschikbare signalen.

|Signaalnaam|Dimensies|Beschrijving|
|---|---|---|
|`Total Update Deployment Runs`|- Naam van update-implementatie<br>- Status|Dit signaal waarschuwt voor de algehele status van een update-implementatie.|
|`Total Update Deployment Machine Runs`|- Naam van update-implementatie</br>- Status</br>- Doelcomputer</br>- Implementatie-run-id bijwerken|Dit signaal waarschuwt over de status van een update-implementatie gericht op specifieke machines.|

Selecteer voor een dimensie een geldige waarde in de lijst. Als de gewenste waarde niet in de **\+** lijst staat, klikt u op het teken naast de dimensie en typt u de aangepaste naam. Selecteer vervolgens de waarde die u wilt zoeken. Als u alle waarden voor een dimensie wilt selecteren, klikt u op de knop **Selecteren. \* ** Als u geen waarde voor een dimensie kiest, negeert Updatebeheer die dimensie.

![Signaallogica configureren](./media/automation-tutorial-update-management/signal-logic.png)

Voer onder **Waarschuwingslogica** voor **Drempelwaarde** in: **1**. Wanneer u klaar bent, selecteert u **Gereed**.

### <a name="alert-details"></a>Meldingsdetails

Onder **2. Definieer waarschuwingsgegevens,** voer een naam en beschrijving in voor de waarschuwing. Stel **Ernst** in op **Ter informatie (ernst 2)** voor een geslaagde uitvoering of **Ter informatie (ernst 1)** voor een mislukte uitvoering.

![Signaallogica configureren](./media/automation-tutorial-update-management/define-alert-details.png)

Selecteer **Onder Actiegroepen**de optie **Nieuw maken**. Een actiegroep is een groep acties die u op meerdere waarschuwingen kunt toepassen. De acties kunnen e-mailmeldingen, runbooks, webhooks en nog veel meer bevatten. Zie Actiegroepen maken en beheren voor meer informatie over [actiegroepen.](../azure-monitor/platform/action-groups.md)

Voer in het veld **Groepsnaam** een naam in voor de waarschuwing en een korte naam. Updatebeheer gebruikt de korte naam in plaats van een volledige naam van de actiegroep bij het verzenden van meldingen met de opgegeven groep.

Voer **onder Acties**een naam voor de actie in, zoals **e-mailmelding**. Selecteer **E-mail/sms/push/voice**voor **actietype**. Selecteer Details **bewerken**voor **details**.

Voer in het deelvenster **E-mail/sms/push/spraak** een naam in. Selecteer het selectievakje **E-mail** en voer vervolgens een geldig e-mailadres in.

![Een e-mailactiegroep configureren](./media/automation-tutorial-update-management/configure-email-action-group.png)

Klik in het deelvenster **E-mail/SMS/Push/Voice** op **OK**. Klik **in** het deelvenster Actiegroep toevoegen op **OK**.

Als u het onderwerp van de waarschuwingse-mail wilt aanpassen, selecteert u onder **Actie**maken onder **Acties aanpassen**de optie **E-mailonderwerp**. Wanneer u klaar bent, selecteert u **Waarschuwingsregel maken**. De waarschuwing geeft aan wanneer een update-implementatie is geslaagd en welke machines deel uitmaakten van de update-implementatie.

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Vervolgens plant u een implementatie volgens de releaseplanning en het servicevenster om updates te installeren. U de updatetypen kiezen die u in de implementatie wilt opnemen. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

>[!NOTE]
>Als u een update-implementatie plant, wordt een [planningsbron](shared-resources/schedules.md) gemaakt die is gekoppeld aan de **runbook Patch-MicrosoftOMSComputers** die de update-implementatie op de doelmachines verwerkt. Als u de planningsbron verwijdert uit de Azure-portal of PowerShell gebruikt nadat u de implementatie hebt gemaakt, wordt de geplande update-implementatie afgebroken en wordt een fout weergegeven wanneer u de planningsbron uit de portal probeert opnieuw te configureren. U de planningsbron alleen verwijderen door het bijbehorende implementatieschema te verwijderen.  

Als u een nieuwe update-implementatie voor de VM wilt plannen, gaat u naar **Updatebeheer** en selecteert u vervolgens **Update-implementatie plannen**.

Geef onder **Nieuwe update-implementatie** de volgende informatie op:

* **Naam**: voer een unieke naam in voor de update-implementatie.

* **Besturingssysteem**: selecteer het besturingssysteem dat het doel is voor de update-implementatie.

* **Groepen bij te werken (voorbeeld)**: Definieer een query die abonnement, brongroepen, locaties en tags combineert om een dynamische groep Azure VM's te bouwen om op te nemen in uw implementatie. Zie [Dynamische groepen voor](automation-update-management-groups.md)meer informatie.

* **Machines bijwerken:** Selecteer een opgeslagen zoekopdracht, geïmporteerde groep of kies **Machines** in het vervolgkeuzemenu en selecteer afzonderlijke machines. Als u **Machines**kiest, wordt de gereedheid van elke machine weergegeven in de kolom **Gereedheid bijwerken.** Zie [Computergroepen in Azure Monitor-logboeken](../azure-monitor/platform/computer-groups.md)voor meer informatie over de verschillende methoden voor het maken van computergroepen in Azure Monitor-logboeken.

* **Updateclassificatie:** Schakel voor elk product alle ondersteunde updateclassificaties uit, maar de classificaties die u in uw update-implementatie wilt opnemen. Laat voor deze zelfstudie alle typen geselecteerd voor alle producten.

  De classificatietypen zijn:

   |OS  |Type  |
   |---------|---------|
   |Windows     | Essentiële updates</br>Beveiligingsupdates</br>Updatepakketten</br>Functiepakketten</br>Servicepacks</br>Definitie-updates</br>Hulpprogramma's</br>Updates<br>Stuurprogramma        |
   |Linux     | Essentiële en beveiligingsupdates</br>Andere Updates       |

   Zie Classificaties bijwerken voor beschrijvingen van de [classificatietypen.](automation-view-update-assessments.md#update-classifications)

* **Updates die moeten worden opgenomen/uitgesloten** : hiermee opent u de pagina Opnemen/uitsluiten. Updates die moeten worden opgenomen of uitgesloten, worden op afzonderlijke tabbladen weergegeven door de KB-artikel-ID-nummers op te geven. Wanneer u een of meer ID-nummers opgeeft, moet u alle classificaties met de update-implementatie verwijderen of ongedaan maken. Dit zorgt ervoor dat er geen andere updates in uw updatepakket worden opgenomen bij het opgeven van update-id's.

> [!NOTE]
> Het is belangrijk om te weten dat uitsluitingen inclusies overschrijven. Als u bijvoorbeeld een uitsluitingsregel definieert van `*`Update Management, worden er geen patches of pakketten geïnstalleerd, omdat ze allemaal zijn uitgesloten. Uitgesloten patches nog steeds als ontbrekende van de machine. Voor Linux-machines installeert Update Management het hoofdpakket niet als u een pakket bevat dat een afhankelijk pakket heeft dat is uitgesloten.

> [!NOTE]
> U geen updates opgeven die zijn vervangen voor opname met de update-implementatie.
>

* **Planningsinstellingen**: het deelvenster **Planningsinstellingen** wordt geopend. De standaard begintijd is 30 minuten na de huidige tijd. U kunt de starttijd op elke gewenste tijd instellen, maar minstens 10 minuten na de huidige tijd.

   U kunt ook opgeven dat de implementatie eenmaal moet worden uitgevoerd, of u kunt een planning met meerdere implementaties instellen. Selecteer onder **Terugkeerpatroon** de optie **Eenmaal**. Laat de standaardinstelling als 1 dag staan en klik op **OK**. Deze vermeldingen stellen een terugkerend schema op.

* **Voorafgaande scripts en navolgende scripts**: selecteer de scripts die moeten worden uitgevoerd vóór en na de implementatie. Zie[Manage Pre and Post scripts](pre-post-scripts.md) (Voorafgaande en navolgende scripts beheren) voor meer informatie.

* **Onderhoudsvenster (minuten)**: laat hier de standaardwaarde staan. Onderhoudsvensters bepalen de hoeveelheid tijd die is toegestaan voor het installeren van updates. Houd rekening met de volgende details bij het opgeven van een onderhoudsvenster:

  * Onderhoudsvensters bepalen hoeveel updates er zijn geïnstalleerd.
  * Updatebeheer stopt niet met het installeren van nieuwe updates als het einde van een onderhoudsvenster nadert.
  * Updatebeheer beëindigt lopende updates niet als het onderhoudsvenster wordt overschreden.
  * Als het onderhoudsvenster wordt overschreden op Windows, is dit vaak omdat het installeren van een servicepack-update veel tijd in beslag neemt.

  > [!NOTE]
  > Om te voorkomen dat updates buiten een onderhoudsvenster op Ubuntu worden toegepast, configureert u het pakket Onbeheerde updates opnieuw om automatische updates uit te schakelen. Zie Onderwerp automatische updates in [de Ubuntu-servergids voor](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)informatie over het configureren van het pakket.

* **Opties voor opnieuw opstarten:** hiermee u opties opgeven voor het afhandelen van opnieuw opstarten. De volgende opties zijn beschikbaar:
  * Indien nodig opnieuw opstarten (standaard)
  * Altijd opnieuw opstarten
  * Nooit opnieuw opstarten
  * Alleen opnieuw opstarten - installeert geen updates

> [!NOTE]
> De registersleutels die worden weergegeven onder [Registersleutels die worden gebruikt om opnieuw op te starten,](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kunnen een gebeurtenis opnieuw opstarten veroorzaken als **de opties voor opnieuw opstarten** is ingesteld op Nooit opnieuw **opstarten.**

Wanneer u klaar bent met het configureren van de planning, klikt u op **Maken**.

![Deelvenster Planningsinstellingen bijwerken](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

U keert nu terug naar het statusdashboard. Selecteer **Geplande update-implementaties** om het zojuist gemaakte implementatieschema weer te geven.

> [!NOTE]
> Update Management ondersteunt het implementeren van first-party updates en pre-downloading patches. Deze ondersteuning vereist wijzigingen op de systemen die worden gepatcht. Zie [Ondersteuning voor eerste partij en vooraf downloaden](automation-configure-windows-update.md) om te leren hoe u deze instellingen op uw systemen configureert.

U ook programmatisch update-implementaties maken. Zie [Software-updateconfiguraties maken voor](/rest/api/automation/softwareupdateconfigurations/create)meer informatie over het maken van een update-implementatie met de REST API. Er is ook een voorbeeld runbook die u gebruiken om een wekelijkse update-implementatie te maken. Zie [Een wekelijkse update-implementatie maken voor een of meer VM's in een resourcegroep](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)voor meer informatie over dit runbook.

## <a name="view-results-of-an-update-deployment"></a>Resultaten van een update-implementatie weergeven

Nadat de geplande implementatie is gestart, ziet u de status van deze implementatie op het tabblad **Update-implementaties** onder **Updatebeheer**. Als de implementatie momenteel wordt uitgevoerd, is de status **Wordt uitgevoerd**. Wanneer de implementatie is voltooid, wordt de status **gewijzigd in Geslaagd**. Als er fouten zijn met een of meer updates in de implementatie, is de status **gedeeltelijk mislukt**.

Selecteer de voltooide update-implementatie om het dashboard te bekijken.

![Statusdashboard voor update-implementatie voor een specifieke implementatie](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Onder **Updateresultaten** ziet u een overzicht van het totale aantal updates en van de implementatieresultaten op de VM. In de tabel aan de rechterkant vindt u gedetailleerde informatie over elke update en het resultaat van de installatie.

De beschikbare waarden zijn:

* **Niet geprobeerd**: de update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was, op basis van de gedefinieerde duur van het onderhoudsvenster.
* **Geslaagd**: De update is geslaagd.
* **Mislukt:** de update is mislukt.

Selecteer **Alle logboeken** selecteren om alle logboekvermeldingen te zien die de implementatie heeft gemaakt.

Selecteer **Uitvoer** om de taakstroom te zien van het runbook dat verantwoordelijk is voor het beheer van de update-implementatie op de doel-VM.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

Wanneer uw update-implementatie slaagt, ontvangt u een bevestigende e-mail die vergelijkbaar is met het volgende:

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