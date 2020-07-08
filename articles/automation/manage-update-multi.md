---
title: Updates voor meerdere virtuele machines in Azure Automation beheren
description: In dit artikel leest u hoe u updates voor meerdere Vm's beheert.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: d08afc6e501fd76167e0939633442213958f0d49
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83834625"
---
# <a name="manage-updates-for-multiple-vms"></a>Updates voor meerdere virtuele machines beheren

U kunt Azure Automation Updatebeheer gebruiken om updates en patches voor uw Windows-en Linux-Vm's te beheren. Vanuit uw [Azure Automation](automation-offering-get-started.md)-account, kunt u het volgende doen:

- Schakel Vm's voor update beheer in.
- De status van beschikbare updates evalueren.
- De installatie van vereiste updates plannen.
- Bekijk de implementatie resultaten om te controleren of de updates zijn toegepast op alle virtuele machines waarvoor Updatebeheer is ingeschakeld.

Zie [updatebeheer-client vereisten](automation-update-management.md#client-requirements)voor meer informatie over de systeem vereisten voor updatebeheer.

## <a name="prerequisites"></a>Vereisten

* Een virtuele machine of computer waarop een van de ondersteunde besturings systemen is geïnstalleerd.
* Toegang tot een update opslagplaats voor Linux-Vm's die zijn ingeschakeld voor Updatebeheer.

## <a name="enable-update-management-for-azure-vms"></a>Updatebeheer voor virtuele Azure-machines inschakelen

1. Open uw Automation-account in het Azure Portal en selecteer vervolgens **Update beheer**.

2. Selecteer **virtuele machines van Azure toevoegen**.

    ![Tabblad Azure-VM toevoegen](./media/manage-update-multi/update-onboard-vm.png)

3. Selecteer een virtuele machine die u wilt inschakelen en selecteer **inschakelen** onder **inschakelen updatebeheer**.

    ![Dialoogvenster Updatebeheer inschakelen](./media/manage-update-multi/update-enable.png)

    Wanneer de bewerking is voltooid, wordt Updatebeheer ingeschakeld op de virtuele machine.

## <a name="enable-update-management-for-non-azure-vms-and-computers"></a>Updatebeheer inschakelen voor Vm's en computers die niet van Azure zijn

De Log Analytics-agent voor Windows en Linux moet worden geïnstalleerd op de virtuele machines die worden uitgevoerd op uw bedrijfs netwerk of in een andere cloud omgeving, zodat deze kunnen worden ingeschakeld met Updatebeheer. Zie [overzicht van de log Analytics-agent](../azure-monitor/platform/log-analytics-agent.md)voor meer informatie over de systeem vereisten en ondersteunde methoden voor het implementeren van de agent op computers die buiten Azure worden gehost.

## <a name="view-computers-attached-to-your-automation-account"></a>Computers weer geven die zijn gekoppeld aan uw Automation-account

Nadat u Updatebeheer voor uw machines hebt ingeschakeld, kunt u computer gegevens weer geven door **computers**te selecteren. U kunt informatie weer geven over de naam van de computer, de nalevings status, de omgeving, het type besturings systeem, de essentiële updates en de geïnstalleerde beveiliging, andere geïnstalleerde updates en de gereedheid voor het bijwerken van de agent voor uw computers.

  ![Tabblad Computers weergeven](./media/manage-update-multi/update-computers-tab.png)

Computers die onlangs zijn ingeschakeld voor Updatebeheer, zijn mogelijk nog niet beoordeeld. De compatibiliteits status voor deze computers is `Not assessed` . Hier volgt een lijst met mogelijke waarden voor de nalevings status:

- `Compliant`: Computers waarop geen essentiële of beveiligings updates ontbreken.
- `Non-compliant`: Computers waarop ten minste één essentiële of beveiligings update ontbreekt.
- `Not assessed`: De gegevens van de update beoordeling zijn niet ontvangen van de computer binnen de verwachte periode. Voor Linux-computers is de verwachte periode het laatste uur. Voor Windows-computers is de verwachte periode de laatste 12 uur.

Als u de status van de agent wilt weer geven, selecteert u de koppeling in de gereedheids kolom van de **Update Agent** . Als u deze optie selecteert, wordt het deel venster Hybrid Worker geopend en wordt de status van de Hybrid Worker weer gegeven. In de volgende afbeelding ziet u een voor beeld van een agent die gedurende lange tijd niet is verbonden met Updatebeheer:

![Tabblad Computers weergeven](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Een update-evaluatie bekijken

Nadat Updatebeheer is ingeschakeld, wordt het deel venster Updatebeheer geopend. U ziet een lijst met ontbrekende updates op het tabblad **Ontbrekende updates**.

## <a name="collect-data"></a>Gegevens verzamelen

Agents die zijn geïnstalleerd op Vm's en computers, verzamelen gegevens over updates. De agents verzenden de gegevens naar Azure Updatebeheer.

### <a name="supported-agents"></a>Ondersteunde agents

De volgende tabel beschrijft de verbonden bronnen die Updatebeheer ondersteunt:

| Verbonden bron | Ondersteund | Description |
| --- | --- | --- |
| Windows-agents |Yes |Updatebeheer verzamelt informatie over systeem updates van Windows-agents en start vervolgens de installatie van de vereiste updates. |
| Linux-agents |Yes |Updatebeheer verzamelt informatie over systeem updates van Linux-agents en start vervolgens de installatie van de vereiste updates op ondersteunde distributies. |
| Beheergroep Operations Manager |Yes |Updatebeheer verzamelt informatie over systeem updates van agents in een verbonden beheer groep. |
| Azure Storage-account |No |Azure Storage bevat geen informatie over systeem updates. |

### <a name="collection-frequency"></a>Verzamelingsfrequentie

Nadat een computer een scan voor de compatibiliteit van updates heeft voltooid, stuurt de agent de gegevens bulksgewijs door naar Azure Monitor Logboeken. Op een Windows-computer wordt de compatibiliteits scan standaard elke 12 uur uitgevoerd.

Naast het scan schema wordt de controle op update vereisten binnen 15 minuten gestart nadat de MMA opnieuw is opgestart, vóór de installatie van de update en na de installatie van de update.

Voor een Linux-computer wordt standaard elk uur de compatibiliteits scan uitgevoerd. Als de MMA-agent opnieuw wordt gestart, wordt een nalevings scan binnen 15 minuten gestart.

Het kan dertig minuten tot zes uur duren voordat er in het dashboard bijgewerkte gegevens van beheerde computers worden weergegeven.

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Als u updates wilt installeren, plant u een implementatie die wordt uitgelijnd met uw release planning en service venster. U kunt kiezen welke typen updates moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

>[!NOTE]
>Wanneer u een update-implementatie plant, wordt er een [plannings](shared-resources/schedules.md) resource gemaakt die is gekoppeld aan het **patch-MicrosoftOMSComputers-** runbook dat de update-implementatie op de doel computers afhandelt. Als u de plannings resource uit de Azure Portal verwijdert of Power shell na het maken van de implementatie gebruikt, wordt de geplande update-implementatie verbroken en wordt er een fout weer gegeven wanneer u deze opnieuw probeert te configureren vanuit de portal. U kunt de planningsresource alleen verwijderen door het bijbehorende implementatieschema te verwijderen.
>

Als u een nieuwe update-implementatie wilt plannen voor een of meer virtuele machines, selecteert u onder **Update beheer**de optie **Update-implementatie plannen**.

Geef in het deel venster **nieuwe update-implementatie** de volgende informatie op:

- **Naam**: Voer een unieke naam in voor het identificeren van de update-implementatie.
- **Besturings systeem**: Selecteer **Windows** of **Linux**.
- **Groepen die moeten worden bijgewerkt**: Definieer een query op basis van een combi natie van abonnement, resource groepen, locaties en tags om een dynamische groep virtuele Azure-machines te bouwen die in uw implementatie moeten worden meegenomen. Voor niet-Azure Vm's worden opgeslagen Zoek opdrachten gebruikt om een dynamische groep te maken die in uw implementatie moet worden meegenomen. Zie [dynamische groepen](automation-update-management-groups.md)voor meer informatie.
- **Machines die moeten worden bijgewerkt**: Selecteer een opgeslagen zoek opdracht, geïmporteerde groep of selecteer machines om de machines te kiezen die u wilt bijwerken.

   >[!NOTE]
   >Als u de optie voor opgeslagen Zoek opdrachten selecteert, worden geen computer identiteiten, alleen hun namen, geretourneerd. Als u meerdere virtuele machines met dezelfde naam in meerdere resource groepen hebt, worden deze geretourneerd in de resultaten. Het gebruik van de optie **voor het bijwerken van groepen** wordt aanbevolen om ervoor te zorgen dat u unieke vm's opneemt die aan uw criteria voldoen.

   Als u **computers**kiest, wordt de gereedheid van de machine weer gegeven in de kolom **gereedheid van de Update Agent** . U kunt de status van de machine bekijken voordat u de update-implementatie plant. Zie [computer groepen in azure monitor-logboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende methoden voor het maken van computer groepen in azure monitor-Logboeken.

  ![Deel venster nieuwe update-implementatie](./media/manage-update-multi/update-select-computers.png)

- **Update classificatie**: Selecteer de typen software die moeten worden meegenomen in de update-implementatie. Zie [Update classificaties](automation-view-update-assessments.md#work-with-update-classifications)voor een beschrijving van de classificatie typen. De classificatietypen zijn:
  - Essentiële updates
  - Beveiligingsupdates
  - Updatepakketten
  - Functiepakketten
  - Servicepacks
  - Definitie-updates
  - Hulpprogramma's
  - Updates

- **Updates om op te nemen/uit te sluiten**: hiermee opent u de pagina Opnemen/uitsluiten. Updates die moeten worden opgenomen of uitgesloten, worden op afzonderlijke tabbladen weergegeven. Zie [een update-implementatie plannen](automation-tutorial-update-management.md#schedule-an-update-deployment)voor meer informatie over het verwerken van de opname.

> [!NOTE]
> Het is belangrijk te weten dat uitsluitingen insluitingen overschrijven. Als u bijvoorbeeld een uitsluitings regel van opgeeft `*` , worden er geen patches of pakketten geïnstalleerd, aangezien deze allemaal uitgesloten zijn. Uitgesloten patches worden nog wel weergegeven als ontbrekend op de computer. Voor Linux-machines als een pakket is opgenomen, maar een afhankelijk pakket heeft dat is uitgesloten, is het pakket niet geïnstalleerd.

> [!NOTE]
> U kunt updates die zijn vervangen, niet opgeven om te worden opgenomen in de update-implementatie.

- **Schema-instellingen**: U kunt de standaarddatum en -tijd accepteren (30 minuten na de huidige tijd). U kunt ook een andere tijd opgeven.

   U kunt bovendien opgeven of de implementatie eenmaal moet worden uitgevoerd of volgens een terugkerend schema. Als u een terugkerend schema wilt instellen, selecteert u **terugkerend**onder **terugkeer patroon**.

   ![Dialoogvenster Schema-instellingen](./media/manage-update-multi/update-set-schedule.png)

- **Voorafgaande scripts en navolgende scripts**: selecteer de scripts die moeten worden uitgevoerd vóór en na de implementatie. Zie[Manage Pre and Post scripts](pre-post-scripts.md) (Voorafgaande en navolgende scripts beheren) voor meer informatie.
- **Onderhouds venster (minuten)**: Geef op hoe lang de update-implementatie moet plaatsvinden. Met deze instelling zorgt u ervoor dat wijzigingen worden uitgevoerd binnen de gedefinieerde onderhoudsvensters.

- **Besturings element voor opnieuw opstarten** : deze instelling bepaalt hoe opnieuw opstarten voor de update-implementatie wordt afgehandeld.

   |Optie|Description|
   |---|---|
   |Opnieuw opstarten indien nodig| **(Standaard)** Als dat het geval is, wordt opnieuw opstarten gestart als het onderhouds venster toestaat.|
   |Altijd opnieuw opstarten|Opnieuw opstarten wordt gestart, ongeacht of er een is vereist. |
   |Nooit opnieuw opstarten|Als opnieuw opstarten is vereist, wordt het opnieuw opstarten onderdrukt.|
   |Alleen opnieuw opstarten - updates worden niet geïnstalleerd|Deze optie negeert de installatie van updates en initieert alleen opnieuw opstarten.|

Wanneer u klaar bent met het configureren van de planning, selecteert u de knop **maken** om terug te keren naar het status dashboard. In de **geplande** tabel wordt het implementatie schema weer gegeven dat u hebt gemaakt.

> [!NOTE]
> Updatebeheer biedt ondersteuning voor het implementeren van eerste partij-updates en het vooraf downloaden van patches. Hiervoor moeten wijzigingen worden aangebracht in de systemen die worden hersteld. Zie [voor ondersteuning voor eerste partij en vooraf downloaden](automation-configure-windows-update.md#pre-download-updates) voor meer informatie over het configureren van deze instellingen op uw systemen.

## <a name="view-results-of-an-update-deployment"></a>Resultaten van een update-implementatie weergeven

Nadat de geplande implementatie is gestart, ziet u de status van deze implementatie op het tabblad **Update-implementaties** onder **Updatebeheer**.

Als de implementatie wordt uitgevoerd, is de status ervan **Actief**. Nadat de implementatie is voltooid, verandert de status in **geslaagd**.

Als een of meer updates in de implementatie zijn mislukt, verandert de status in **Gedeeltelijk mislukt**.

![Status van update-implementatie](./media/manage-update-multi/update-view-results.png)

Klik op de voltooide implementatie om het dashboard voor de betreffende update-implementatie te bekijken.

In het deel venster met update resultaten ziet u het totale aantal updates en de implementatie resultaten voor de virtuele machine. De tabel aan de rechter kant geeft een gedetailleerde uitsplitsing van elke update en de installatie resultaten. Een van de volgende installatieresultaatwaarden wordt weergegeven:

- `Not attempted`: De update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was op basis van het gedefinieerde onderhouds venster.
- `Succeeded`: De update is voltooid.
- `Failed`: De update is mislukt.

Selecteer **Alle logboeken** voor een overzicht van alle logboekvermeldingen die tijdens de implementatie zijn gemaakt.

Selecteer de tegel uitvoer om de taak stroom te bekijken van het runbook dat de update-implementatie op de doel-VM beheert.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Query updatebeheer-logboeken](automation-update-management-query-logs.md)als u wilt zoeken naar update Logboeken.
