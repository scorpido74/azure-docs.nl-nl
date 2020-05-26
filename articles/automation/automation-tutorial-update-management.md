---
title: Updates en patches voor uw Azure-VM's beheren in Azure Automation
description: Dit artikel beschrijft hoe u Updatebeheer kunt gebruiken om updates en patches voor uw Azure-VM's te beheren.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 5b5172df6ed6993742a08d5ac08cf700681dfc6a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829151"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Updates en patches voor uw Azure-VM's beheren

Dit artikel beschrijft hoe u de [Updatebeheer](automation-update-management.md)-functie van Azure Automation kunt gebruiken om updates en patches te beheren voor uw Azure-VM's. 

Zie [Automation-prijzen voor updatebeheer](https://azure.microsoft.com/pricing/details/automation/) voor prijsinformatie.

## <a name="prerequisites"></a>Vereisten

* De functie [Updatebeheer](automation-update-management.md) ingeschakeld op een of meer van uw VM's. 
* Updatebeheer ingeschakeld op een [virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="view-update-assessment"></a>Update-evaluatie bekijken

Nadat u Updatebeheer hebt ingeschakeld, wordt de pagina Updatebeheer geopend. Als er ontbrekende updates worden geïdentificeerd, wordt een lijst met ontbrekende updates weergegeven op het tabblad **Ontbrekende updates**.

Selecteer onder **Koppeling naar informatie** de koppeling naar de update om het ondersteuningsartikel voor de update te openen. Hier kunt u belangrijke informatie lezen over de update.

![Updatestatus bekijken](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Klik ergens anders in de update om het deelvenster Zoeken in logboeken te openen voor de geselecteerde update. De query voor zoeken in logboeken is vooraf gedefinieerd voor deze specifieke update. U kunt deze query wijzigen of uw eigen query maken om gedetailleerde informatie over geïmplementeerde of ontbrekende updates in uw omgeving weer te geven.

![Updatestatus bekijken](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Waarschuwingen configureren

In deze stap leert u een waarschuwing instellen zodat u de status van een update-implementatie kent.

### <a name="alert-conditions"></a>Meldingsvoorwaarden

Ga in uw Automation-account onder **Bewaking** naar **Waarschuwingen** en klik op **Nieuwe waarschuwingsregel**.

Uw Automation-account is al als resource geselecteerd. Als u dit wilt wijzigen, klikt u op **Selecteren**. Kies op de pagina Een resource selecteren **Automation-accounts** in het vervolgkeuzemenu **Filteren op resourcetype**. Selecteer uw Automation-account en klik op **Gereed**.

Klik op **Voorwaarde toevoegen** om het signaal te selecteren dat geschikt is voor de update-implementatie. In de volgende tabel vindt u de details van de twee beschikbare signalen.

|Signaalnaam|Dimensies|Beschrijving|
|---|---|---|
|`Total Update Deployment Runs`|- Naam van update-implementatie<br>- Status|Dit signaal geeft een waarschuwing met de algemene status van een update-implementatie.|
|`Total Update Deployment Machine Runs`|- Naam van update-implementatie</br>- Status</br>- Doelcomputer</br>- Uitvoerings-id van update-implementatie|Dit signaal geeft een waarschuwing met de status van een update-implementatie voor bepaalde computers.|

Selecteer een geldige waarde in de lijst voor een dimensie. Als de gewenste waarde niet in de lijst voorkomt, klikt u op het **\+** -teken naast de dimensie en typt u de aangepaste naam. Selecteer vervolgens de waarde waarnaar u wilt zoeken. Als u alle waarden van een dimensie wilt selecteren, klikt u op de knop **Selecteren\*** . Als u geen waarde voor een dimensie kiest, negeert Updatebeheer die dimensie.

![Signaallogica configureren](./media/automation-tutorial-update-management/signal-logic.png)

Voer onder **Waarschuwingslogica** voor **Drempelwaarde** in: **1**. Wanneer u klaar bent, selecteert u **Gereed**.

### <a name="alert-details"></a>Meldingsdetails

Selecteer bij **2. Waarschuwingsdetails definiëren** een naam en beschrijving voor de waarschuwing. Stel **Ernst** in op **Ter informatie (ernst 2)** voor een geslaagde uitvoering of **Ter informatie (ernst 1)** voor een mislukte uitvoering.

![Signaallogica configureren](./media/automation-tutorial-update-management/define-alert-details.png)

Selecteer onder **Actiegroepen** de optie **Nieuwe maken**. Een actiegroep is een groep acties die u op meerdere waarschuwingen kunt toepassen. Deze acties kunnen e-mailmeldingen, runbooks, webhooks en nog veel meer zijn. Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/platform/action-groups.md) voor meer informatie over actiegroepen.

Voer in het veld **Naam van actiegroep** een naam in voor de waarschuwing en een korte naam. Updatebeheer gebruikt de korte naam in plaats van de volledige naam van een actiegroep bij het verzenden van meldingen met de opgegeven groep.

Voer onder **Acties** een naam in voor de actie, bijvoorbeeld **E-mailmelding**. Selecteer onder **Actietype** de optie **E-mail/sms/push/spraak**. Selecteer onder **Details** de optie **Details bewerken**.

Voer in het deelvenster E-mail/sms/push/spraak een naam in. Selecteer het selectievakje **E-mail** en voer een geldig e-mailadres in.

![Een e-mailactiegroep configureren](./media/automation-tutorial-update-management/configure-email-action-group.png)

Klik op **OK** in het deelvenster E-mail/sms/push/spraak. Klik op **OK** in het deelvenster Actiegroep toevoegen.

Selecteer onder **Regel maken** bij **Acties aanpassen** de optie **E-mailonderwerp** als u het onderwerp van het waarschuwingsbericht wilt aanpassen. Wanneer u klaar bent, selecteert u **Waarschuwingsregel maken**. De waarschuwing laat u weten wanneer een update-implementatie is voltooid en op welke machines de update-implementatie is uitgevoerd.

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Plan een implementatie volgens uw releaseplanning en servicevenster om updates te installeren. U kunt de typen updates kiezen die moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

>[!NOTE]
>Bij het plannen van een update-implementatie wordt een [schema](shared-resources/schedules.md)resource gemaakt die is gekoppeld aan het**patch-MicrosoftOMSComputers**-runbook dat de update-implementatie op de doelcomputers afhandelt. Als u na het maken van de implementatie de planningsresource verwijdert via de Azure-portal of met behulp van PowerShell, wordt de geplande update-implementatie door de verwijdering verbroken en wordt er een foutmelding weergegeven wanneer u probeert de planningsresource opnieuw te configureren vanuit de portal. U kunt de planningsresource alleen verwijderen door het bijbehorende implementatieschema te verwijderen.  

Als u een nieuwe update-implementatie voor de VM wilt plannen, gaat u naar **Updatebeheer** en selecteert u vervolgens **Update-implementatie plannen**.

Geef onder **Nieuwe update-implementatie** de volgende informatie op:

* **Naam**: voer een unieke naam in voor de update-implementatie.

* **Besturingssysteem**: selecteer het besturingssysteem dat het doel is voor de update-implementatie.

* **Groepen om bij te werken (preview)** : definieer een query met een combinatie van abonnement, resourcegroepen, locaties en tags om een dynamische groep virtuele Azure-machines te bouwen voor opname in uw implementatie. Zie [Dynamische groepen](automation-update-management-groups.md) voor meer informatie.

* **Bij te werken computers**: selecteer een opgeslagen zoekopdracht of geïmporteerde groep, of kies **Computers** in de vervolgkeuzelijst en selecteer de afzonderlijke computers. Als u **Computers** selecteert, wordt de gereedheid van elke computer weergegeven in de kolom **Gereedheid voor update-agent**. Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken.

* **Updateclassificatie**: Deselecteer voor elk product alle ondersteunde updateclassificaties behalve degene die u wilt opnemen in uw update-implementatie. Zie [Updateclassificaties](automation-view-update-assessments.md#work-with-update-classifications) voor een beschrijving van de classificatietypen.

* **Updates om op te nemen/uit te sluiten**: hiermee opent u de pagina Opnemen/uitsluiten. Updates die moeten worden opgenomen of uitgesloten, bevinden zich op afzonderlijke tabbladen door de KB-artikel-id-nummers op te geven. Wanneer u een of meer id-nummers opgeeft, moet u alle classificaties verwijderen of uitschakelen met de update-implementatie. Dit zorgt ervoor dat er geen andere updates worden opgenomen in uw updatepakket wanneer u update-id's opgeeft.

> [!NOTE]
> Het is belangrijk te weten dat uitsluitingen insluitingen overschrijven. Als u bijvoorbeeld de uitsluitingsregel `*`definieert, installeert Updatebeheer geen patches of pakketten, omdat ze allemaal worden uitgesloten. Uitgesloten patches worden nog wel weergegeven als ontbrekend op de computer. Als u op Linux-computers een pakket opneemt dat een afhankelijk pakket bevat dat is uitgesloten, installeert Updatebeheer het hoofdpakket niet.

> [!NOTE]
> U kunt updates die zijn vervangen, niet opgeven om te worden opgenomen in de update-implementatie.
>
* **Planningsinstellingen**: Het deelvenster Planningsinstellingen wordt geopend. De standaard begintijd is 30 minuten na de huidige tijd. U kunt de starttijd op elke gewenste tijd instellen, maar minstens 10 minuten na de huidige tijd.

   U kunt ook opgeven dat de implementatie eenmaal moet worden uitgevoerd, of u kunt een planning met meerdere implementaties instellen. Selecteer onder **Terugkeerpatroon** de optie **Eenmaal**. Laat de standaardwaarde op 1 dag staan en klik op **OK**. Deze vermeldingen stellen een terugkerende planning in.

* **Voorafgaande scripts en navolgende scripts**: selecteer de scripts die moeten worden uitgevoerd vóór en na de implementatie. Zie[Manage Pre and Post scripts](pre-post-scripts.md) (Voorafgaande en navolgende scripts beheren) voor meer informatie.

* **Onderhoudsvenster (minuten)** : Laat de standaardwaarde staan. Onderhoudsvensters bepalen de hoeveelheid tijd die is toegestaan voor het installeren van updates. Houd rekening met de volgende details wanneer u een onderhoudsvenster opgeeft:

  * Onderhoudsvensters bepalen hoeveel updates worden geïnstalleerd.
  * Updatebeheer stopt niet met het installeren van nieuwe updates als het einde van een onderhoudsvenster nadert.
  * Updatebeheer wordt niet beëindigd als er updates worden uitgevoerd wanneer het onderhoudsvenster wordt overschreden.
  * Als het onderhoudsvenster op Windows wordt overschreden, komt dat vaak doordat het installeren van een servicepack-update lang duurt.

  > [!NOTE]
  > Als u wilt voorkomen dat updates buiten een onderhoudsvenster in Ubuntu worden toegepast, moet u het pakket Unattended-Upgrade opnieuw configureren en automatische updates uitschakelen. Zie [het onderwerp Automatic Updates in de Engelstalige Ubuntu Server Guide](https://help.ubuntu.com/lts/serverguide/automatic-updates.html) voor informatie over het configureren van het pakket.

* **Opties voor opnieuw opstarten**: Gebruiken om opties op te geven voor het afhandelen van opnieuw opstarten. De volgende opties zijn beschikbaar:
  * Opnieuw opstarten indien nodig (standaard)
  * Altijd opnieuw opstarten
  * Nooit opnieuw opstarten
  * Alleen opnieuw opstarten: er worden geen updates geïnstalleerd

> [!NOTE]
> De registersleutels onder [Registry keys used to manage restart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) (registersleutels voor het beheren van opnieuw opstarten) kunnen een gebeurtenis voor opnieuw opstarten veroorzaken als **Opties voor opnieuw opstarten** is ingesteld op **Nooit opnieuw opstarten**.

Als u klaar bent met het configureren van de planning, klikt u op **Maken**.

![Deelvenster Planningsinstellingen bijwerken](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

U keert nu terug naar het statusdashboard. Selecteer **Geplande update-implementaties** om de zojuist gemaakte implementatieplanning weer te geven.

> [!NOTE]
> Updatebeheer biedt ondersteuning voor het implementeren van de eerste partij-updates en het vooraf downloaden van patches. Deze ondersteuning vereist wijzigingen op de systemen die worden bijgewerkt. Zie [Ondersteuning voor eerste partij en vooraf downloaden](automation-configure-windows-update.md) voor informatie over het configureren van deze instellingen op uw systemen.

U kunt ook programmatisch update-implementaties maken. Zie [Software Update Configurations - Create](/rest/api/automation/softwareupdateconfigurations/create) (software-updateconfiguraties: maken) als u wilt leren hoe u een update-implementatie kunt maken met de REST-API. Er is ook een voorbeeldrunbook dat u kunt gebruiken om een wekelijkse update-implementatie te maken. Zie voor meer informatie over dit runbook [Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) (een wekelijkse update-implementatie maken voor een of meer VM's in een resourcegroep).

## <a name="view-results-of-an-update-deployment"></a>Resultaten van een update-implementatie weergeven

Nadat de geplande implementatie is gestart, ziet u de status van deze implementatie op het tabblad **Update-implementaties** onder **Updatebeheer**. Als de implementatie momenteel wordt uitgevoerd, is de status **Wordt uitgevoerd**. Als de implementatie correct is voltooid, verandert de status in **Geslaagd**. Als er fouten optreden met een of meer updates in de implementatie, verandert de status in **Gedeeltelijk mislukt**.

Selecteer de voltooide update-implementatie om het dashboard ervan weer te geven.

![Statusdashboard voor update-implementatie voor een specifieke implementatie](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Onder **Updateresultaten** ziet u een overzicht van het totale aantal updates en van de implementatieresultaten op de VM. In de tabel aan de rechterkant vindt u gedetailleerde informatie over elke update en het resultaat van de installatie.

De beschikbare waarden zijn:

* **Niet geprobeerd**: De update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was op basis van het ingestelde onderhoudsvenster.
* **Geslaagd**: De update is bijgewerkt.
* **Mislukt**: De update is mislukt.

Selecteer **Alle logboeken** als u alle logboekvermeldingen wilt zien die tijdens de implementatie zijn gemaakt.

Selecteer **Uitvoer** om de taakstroom te zien van het runbook dat verantwoordelijk is voor het beheer van de update-implementatie op de doel-VM.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

Wanneer de update-implementatie is geslaagd, ontvangt u een e-mailbericht dat lijkt op het volgende:

![E-mailactiegroep configureren](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [Overzicht van Updatebeheer](automation-update-management.md) voor informatie over Updatebeheer.