---
title: Updates voor meerdere virtuele machines in Azure beheren
description: In dit artikel wordt beschreven hoe u updates voor virtuele Azure- en niet-Azure-virtuele machines beheert.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: 5376562d9df35539a33f6746b387a1ff7083b8f1
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676450"
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Updates voor meerdere virtuele machines in Azure beheren

U Azure Automation Update Management gebruiken om updates en patches voor uw virtuele Windows- en Linux-machines te beheren. Vanuit uw [Azure Automation](automation-offering-get-started.md)-account, kunt u het volgende doen:

- Virtuele machines activeren.
- De status van beschikbare updates evalueren.
- De installatie van vereiste updates plannen.
- Controleer de implementatieresultaten om te controleren of updates zijn toegepast op alle virtuele machines waarvoor UpdateManagement is ingeschakeld.

Zie Clientvereisten voor [updatebeheer](automation-update-management.md#clients)voor meer informatie over de systeemvereisten voor Updatebeheer.

## <a name="prerequisites"></a>Vereisten

* Een virtuele machine of computer met een van de ondersteunde besturingssystemen.
* Toegang tot een updaterepository voor Linux VM's die zijn aangesloten bij Update Management.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Updatebeheer inschakelen voor virtuele Azure-machines

Open in de Azure-portal uw Automatiseringsaccount en selecteer **Vervolgens Beheer bijwerken**.

Selecteer **Azure VM's toevoegen**.

![Tabblad Azure-VM toevoegen](./media/manage-update-multi/update-onboard-vm.png)

Selecteer een virtuele machine om te onboarden.

Selecteer **onder Updatebeheer inschakelen**de optie **Inschakelen** om aan boord van de virtuele machine te gaan.

![Dialoogvenster Updatebeheer inschakelen](./media/manage-update-multi/update-enable.png)

Wanneer de onboarding is voltooid, is Update Management ingeschakeld voor uw virtuele machine.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Updatebeheer inschakelen voor niet-Azure virtuele machines en computers

De Log Analytics-agent voor Windows en Linux moet worden geïnstalleerd op de VM's die op uw bedrijfsnetwerk of andere cloudomgeving worden uitgevoerd om deze met UpdateManagement in te schakelen. Zie [Overzicht van de agent Log Analytics](../azure-monitor/platform/log-analytics-agent.md)voor meer informatie over de systeemvereisten en ondersteunde methoden om de agent te implementeren op machines die buiten Azure worden gehost.

## <a name="view-computers-attached-to-your-automation-account"></a>Computers weergeven die zijn gekoppeld aan uw Automatiseringsaccount

Nadat u Updatebeheer voor uw machines hebt ingeschakeld, u machinegegevens bekijken door **Computers te**selecteren. U informatie zien over de naam van de machine, de nalevingsstatus, de omgeving, het type besturingssysteem, de geïnstalleerde kritieke en beveiligingsupdates, andere geïnstalleerde updates en de gereedheid van de agent voor uw computers bijwerken.

  ![Tabblad Computers weergeven](./media/manage-update-multi/update-computers-tab.png)

Computers die onlangs zijn ingeschakeld voor Updatebeheer zijn mogelijk nog niet beoordeeld. De nalevingsstatus voor `Not assessed`deze computers is . Hier is een lijst met mogelijke waarden voor nalevingsstatus:

- `Compliant`: Computers die geen kritieke of beveiligingsupdates missen.
- `Non-compliant`: Computers die ten minste één kritieke of beveiligingsupdate missen.
- `Not assessed`: De updatebeoordelingsgegevens zijn niet binnen de verwachte termijn van de computer ontvangen. Voor Linux-computers is de verwachte termijn het laatste uur. Voor Windows-computers is de verwachte termijn de laatste 12 uur.

Als u de status van de agent wilt weergeven, selecteert u de koppeling in de kolom **Gereedheid bijwerken.** Als u deze optie selecteert, wordt het deelvenster Hybride werker geopend en wordt de status van de hybride werknemer weergegeven. In de volgende afbeelding ziet u een voorbeeld van een agent die gedurende een langere periode niet is verbonden met Updatebeheer:

![Tabblad Computers weergeven](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Een update-evaluatie bekijken

Nadat Updatebeheer is ingeschakeld, wordt het deelvenster Updatebeheer geopend. U ziet een lijst met ontbrekende updates op het tabblad **Ontbrekende updates**.

## <a name="collect-data"></a>Gegevens verzamelen

Agents die zijn geïnstalleerd op virtuele machines en computers verzamelen gegevens over updates. De agents verzenden de gegevens naar Azure Update Management.

### <a name="supported-agents"></a>Ondersteunde agents

De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing:

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| Windows-agents |Ja |Updatemanagement verzamelt informatie over systeemupdates van Windows-agents en initieert vervolgens de installatie van vereiste updates. |
| Linux-agents |Ja |Update Management verzamelt informatie over systeemupdates van Linux-agents en initieert vervolgens de installatie van vereiste updates voor ondersteunde distributies. |
| Beheergroep Operations Manager |Ja |Update Management verzamelt informatie over systeemupdates van agents in een verbonden beheergroep. |
| Azure Storage-account |Nee |Azure Storage bevat geen informatie over systeemupdates. |

### <a name="collection-frequency"></a>Verzamelingsfrequentie

Nadat een computer een scan voor update-naleving heeft voltooid, stuurt de agent de informatie in bulk door naar Azure Monitor-logboeken. Op een Windows-computer wordt de compliance-scan standaard elke 12 uur uitgevoerd.

Naast het scanschema wordt de scan voor update-naleving gestart binnen 15 minuten nadat de MMA opnieuw is opgestart, voordat de update wordt geïnstalleerd en na de installatie van de update.

Voor een Linux-computer wordt de compliance-scan standaard elk uur uitgevoerd. Als de MMA-agent opnieuw wordt gestart, wordt binnen 15 minuten een compliance-scan gestart.

Het kan dertig minuten tot zes uur duren voordat er in het dashboard bijgewerkte gegevens van beheerde computers worden weergegeven.

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Als u updates wilt installeren, plant u een implementatie die aansluit bij uw releaseplanning en servicevenster. U kunt kiezen welke typen updates moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

>[!NOTE]
>Wanneer u een update-implementatie plant, wordt een [planningsbron](shared-resources/schedules.md) gemaakt die is gekoppeld aan de **runbook Patch-MicrosoftOMSComputers** die de update-implementatie op de doelmachines verwerkt. Als u de planningsbron verwijdert uit de Azure-portal of PowerShell gebruikt nadat u de implementatie hebt gemaakt, wordt de geplande update-implementatie afgebroken en wordt er een fout weergegeven wanneer u deze vanuit de portal opnieuw probeert te configureren. U de planningsbron alleen verwijderen door het bijbehorende implementatieschema te verwijderen.
>

Als u een nieuwe update-implementatie voor een of meer virtuele machines wilt plannen, selecteert u Onder **Updatebeheer**de **optie Update-implementatie plannen**.

Geef in het deelvenster **Nieuwe updateimplementatie** de volgende gegevens op:

- **Naam:** Voer een unieke naam in om de update-implementatie te identificeren.
- **Besturingssysteem**: Selecteer **Windows** of **Linux**.
- **Groepen bij te werken:** definieer een query op basis van een combinatie van abonnement, brongroepen, locaties en tags om een dynamische groep Azure VM's te bouwen die u in uw implementatie wilt opnemen. Voor niet-Azure VM's worden opgeslagen zoekopdrachten gebruikt om een dynamische groep te maken die u in uw implementatie wilt opnemen. Voor meer informatie zie, [Dynamische groepen](automation-update-management-groups.md).
- **Machines om bij te werken:** selecteer een opgeslagen zoekopdracht, geïmporteerde groep of selecteer Machines om de machines te kiezen die u wilt bijwerken.

   >[!NOTE]
   >Als u de optie Opgeslagen zoeken selecteert, worden de identiteiten van de machine niet teruggegeven, alleen hun namen. Als u meerdere VM's met dezelfde naam hebt in meerdere resourcegroepen, worden deze geretourneerd in de resultaten. Als u de optie **Groepen wilt bijwerken,** wordt aanbevolen om ervoor te zorgen dat u unieke VM's opneemt die aan uw criteria voldoen.

   Als u **Machines**kiest, wordt de gereedheid van de machine weergegeven in de kolom **Gereedheid bijwerken.** U de status van de machine zien voordat u de implementatie van de update plant. Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken

  ![Nieuw update-implementatievenster](./media/manage-update-multi/update-select-computers.png)

- **Updateclassificatie**: Selecteer de typen software die u wilt opnemen in de update-implementatie. Zie Classificaties bijwerken voor een beschrijving van de [classificatietypen.](automation-view-update-assessments.md#update-classifications) De classificatietypen zijn:
  - Essentiële updates
  - Beveiligingsupdates
  - Updatepakketten
  - Functiepakketten
  - Servicepacks
  - Definitie-updates
  - Hulpprogramma's
  - Updates

- **Updates om op te nemen/uit te sluiten**: hiermee opent u de pagina Opnemen/uitsluiten. Updates die moeten worden opgenomen of uitgesloten, worden op afzonderlijke tabbladen weergegeven. Zie [Een update-implementatie plannen voor](automation-tutorial-update-management.md#schedule-an-update-deployment)meer informatie over hoe met opname wordt omgegaan.

> [!NOTE]
> Het is belangrijk om te weten dat uitsluitingen inclusies overschrijven. Als u bijvoorbeeld een uitsluitingsregel definieert van `*`, worden er geen patches of pakketten geïnstalleerd omdat ze allemaal zijn uitgesloten. Uitgesloten patches nog steeds als ontbrekende van de machine. Voor Linux-machines als een pakket is inbegrepen, maar een afhankelijk pakket heeft dat is uitgesloten, is het pakket niet geïnstalleerd.

> [!NOTE]
> U geen updates opgeven die zijn vervangen voor opname met de update-implementatie.
>

- **Schema-instellingen**: U kunt de standaarddatum en -tijd accepteren (30 minuten na de huidige tijd). U ook een andere tijd opgeven.

   U kunt bovendien opgeven of de implementatie eenmaal moet worden uitgevoerd of volgens een terugkerend schema. Als u een terugkerend schema wilt instellen, selecteert u **Onder Herhaling**de optie **Terugkerend**.

   ![Dialoogvenster Schema-instellingen](./media/manage-update-multi/update-set-schedule.png)

- **Voorafgaande scripts en navolgende scripts**: selecteer de scripts die moeten worden uitgevoerd vóór en na de implementatie. Zie[Manage Pre and Post scripts](pre-post-scripts.md) (Voorafgaande en navolgende scripts beheren) voor meer informatie.
- **Onderhoudsvenster (minuten):** geef de periode op die u wilt dat de update-implementatie uitvoert. Met deze instelling zorgt u ervoor dat wijzigingen worden uitgevoerd binnen de gedefinieerde onderhoudsvensters.

- **Beheer opnieuw opstarten** : deze instelling bepaalt hoe opnieuw opstarten wordt afgehandeld voor de implementatie van de update.

   |Optie|Beschrijving|
   |---|---|
   |Reboot indien nodig| **(Standaard)** Indien nodig wordt een herstart gestart als het onderhoudsvenster dit toelaat.|
   |Altijd opnieuw opstarten|Een reboot wordt gestart, ongeacht of een nodig is. |
   |Nooit opnieuw opstarten|Ongeacht of een reboot vereist is, worden reboots onderdrukt.|
   |Alleen opnieuw opstarten - updates worden niet geïnstalleerd|Met deze optie wordt het installeren van updates genegeerd en wordt alleen een reboot gestart.|

Wanneer u klaar bent met het configureren van de planning, selecteert u de knop **Maken** om terug te keren naar het statusdashboard. In **de tabel Gepland** wordt het implementatieschema weergegeven dat u hebt gemaakt.

> [!NOTE]
> Updatebeheer biedt ondersteuning voor het implementeren van eerste partij-updates en het vooraf downloaden van patches. Dit vereist wijzigingen op de systemen die worden gepatcht, zie [first party en pre download ondersteuning](automation-configure-windows-update.md#pre-download-updates) om te leren hoe u deze instellingen op uw systemen configureren.

## <a name="view-results-of-an-update-deployment"></a>Resultaten van een update-implementatie weergeven

Nadat de geplande implementatie is gestart, ziet u de status van deze implementatie op het tabblad **Update-implementaties** onder **Updatebeheer**.

Als de implementatie wordt uitgevoerd, is de status ervan **Actief**. Nadat de implementatie is voltooid, wordt de status **gewijzigd in Geslaagd**.

Als een of meer updates in de implementatie zijn mislukt, verandert de status in **Gedeeltelijk mislukt**.

![Status van update-implementatie](./media/manage-update-multi/update-view-results.png)

Klik op de voltooide implementatie om het dashboard voor de betreffende update-implementatie te bekijken.

In het deelvenster Resultaten bijwerken wordt het totale aantal updates en de implementatieresultaten voor de virtuele machine weergegeven. De tabel aan de rechterkant geeft een gedetailleerde uitsplitsing van elke update en de installatieresultaten. Een van de volgende installatieresultaatwaarden wordt weergegeven:

- `Not attempted`: De update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was op basis van het gedefinieerde onderhoudsvenster.
- `Succeeded`: De update is geslaagd.
- `Failed`: De update is mislukt.

Selecteer **Alle logboeken** voor een overzicht van alle logboekvermeldingen die tijdens de implementatie zijn gemaakt.

Als u de taakstroom van de runbook wilt zien die de update-implementatie op de doelvirtuele machine beheert, selecteert u de uitvoertegel.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Queryupdaterecords voor Updatebeheer voor](automation-update-management-query-logs.md)meer informatie over logboeken, uitvoer en fouten van updatebeheer .
