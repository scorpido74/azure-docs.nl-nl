---
title: Updates voor meerdere virtuele machines in Azure beheren
description: In dit artikel wordt beschreven hoe u updates voor virtuele Azure-machines beheert.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 367a4409c004c98cc4b5ec844aab5b05ec74abcb
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374502"
---
# <a name="manage-updates-for-multiple-machines"></a>Updates voor meerdere machines beheren

U kunt de Updatebeheer oplossing gebruiken voor het beheren van updates en patches voor uw virtuele Windows-en Linux-machines. Vanuit uw [Azure Automation](automation-offering-get-started.md)-account, kunt u het volgende doen:

- Virtuele machines onboarden
- De status van beschik bare updates evalueren
- Installatie van vereiste updates plannen
- Bekijk de implementatie resultaten om te controleren of de updates zijn toegepast op alle virtuele machines waarvoor Updatebeheer is ingeschakeld

## <a name="prerequisites"></a>Vereisten

Als u Updatebeheer wilt gebruiken, hebt u het volgende nodig:

- Een virtuele machine of computer met een van de ondersteunde besturingssystemen.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Updatebeheer wordt ondersteund op de volgende besturings systemen:

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Ondersteunt alleen update-evaluaties.         |
|Windows Server 2008 R2 SP1 en hoger     |Windows Power Shell 4,0 of hoger is vereist. ([WMF 4,0 downloaden](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows Power shell 5,1 wordt aanbevolen voor een betere betrouw baarheid. ([WMF 5,1 downloaden](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) en 7 (x64)      | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|Red Hat Enterprise 6 (x86/x64) en 7 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|SUSE Linux Enterprise Server 11 (x86/x64) en 12 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|Ubuntu 14,04 LTS, 16,04 LTS en 18,04 LTS (x86/x64)      |Linux-agents moeten toegang hebben tot een opslagplaats voor updates.         |

> [!NOTE]
> Om te voorkomen dat updates buiten een onderhoudsperiode in Ubuntu worden toegepast, moet u het pakket Unattended-Upgrade opnieuw configureren en automatische updates uitschakelen. Zie het onderwerp [Automatic Updates](https://help.ubuntu.com/lts/serverguide/automatic-updates.html) (Automatische updates) in de Ubuntu Server Guide voor meer informatie.

Linux-agents moeten toegang hebben tot een opslagplaats voor updates.

Deze oplossing biedt geen ondersteuning voor een Log Analytics-agent voor Linux die is geconfigureerd om te rapporteren aan meerdere Azure Log Analytics-werk ruimten.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Updatebeheer voor virtuele Azure-machines inschakelen

Open uw Automation-account in het Azure Portal en selecteer vervolgens **Update beheer**.

Selecteer **virtuele machines van Azure toevoegen**.

![Het tabblad Azure VM toevoegen](./media/manage-update-multi/update-onboard-vm.png)

Selecteer een virtuele machine om te onboarden.

Selecteer onder **updatebeheer inschakelen**de optie inschakelen om de virtuele machine vrij te **maken** .

![Dialoogvenster Updatebeheer inschakelen](./media/manage-update-multi/update-enable.png)

Wanneer de onboarding is voltooid, wordt Updatebeheer voor uw virtuele machine ingeschakeld.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Updatebeheer inschakelen voor virtuele machines en computers die niet van Azure zijn

Zie [Windows-computers verbinden met de Azure Monitor-service in azure](../log-analytics/log-analytics-windows-agent.md)voor meer informatie over het inschakelen van updatebeheer voor virtuele Windows-machines en-computers zonder Azure.

Zie [uw Linux-computers verbinden met Azure monitor-logboeken](../log-analytics/log-analytics-agent-linux.md)voor meer informatie over het inschakelen van updatebeheer voor virtuele machines en computers die niet van Azure Linux zijn.

## <a name="view-computers-attached-to-your-automation-account"></a>Computers weer geven die zijn gekoppeld aan uw Automation-account

Nadat u Updatebeheer voor uw machines hebt ingeschakeld, kunt u computer gegevens weer geven door **computers**te selecteren. U kunt informatie weer geven over de naam van de *computer*, de *nalevings status*, de *omgeving*, het *type besturings systeem*, de *essentiële updates en de geïnstalleerde beveiliging*, *andere geïnstalleerde updates*en de *gereedheid* voor het bijwerken van de agent voor uw lidcomputers.

  ![Tabblad Computers weergeven](./media/manage-update-multi/update-computers-tab.png)

Computers die onlangs zijn ingeschakeld voor Updatebeheer, zijn mogelijk nog niet beoordeeld. De status van de compatibiliteits status voor deze computers wordt **niet beoordeeld**. Hier volgt een lijst met mogelijke waarden voor de nalevings status:

- **Compatibel**: computers waarop essentiële of beveiligings updates ontbreken.

- **Niet-compatibel**: computers waarop ten minste één essentiële of beveiligings update ontbreekt.

- **Niet beoordeeld**: de gegevens van de update beoordeling zijn niet ontvangen van de computer binnen de verwachte periode. Voor Linux-computers is de verwachte periode in het afgelopen uur. Voor Windows-computers bevindt de verwachte periode zich in de afgelopen 12 uur.

Als u de status van de agent wilt weer geven, selecteert u de koppeling in de GEREEDHEIDS kolom van de **Update Agent** . Als u deze optie selecteert, wordt het deel venster **Hybrid worker** geopend en wordt de status van de Hybrid worker weer gegeven. In de volgende afbeelding ziet u een voor beeld van een agent die gedurende lange tijd niet is verbonden met Updatebeheer:

![Tabblad Computers weergeven](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Een update-evaluatie bekijken

Nadat Updatebeheer is ingeschakeld, wordt het deelvenster **Updatebeheer** geopend. U ziet een lijst met ontbrekende updates op het tabblad **Ontbrekende updates**.

## <a name="collect-data"></a>Gegevens verzamelen

Agents die zijn geïnstalleerd op virtuele machines en computers, verzamelen gegevens over updates. De agents verzenden de gegevens naar Azure Updatebeheer.

### <a name="supported-agents"></a>Ondersteunde agents

De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing:

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| Windows-agents |Ja |Updatebeheer verzamelt informatie over systeem updates van Windows-agents en start vervolgens de installatie van de vereiste updates. |
| Linux-agents |Ja |Updatebeheer verzamelt informatie over systeem updates van Linux-agents en start vervolgens de installatie van de vereiste updates op ondersteunde distributies. |
| Beheergroep Operations Manager |Ja |Updatebeheer verzamelt informatie over systeem updates van agents in een verbonden beheer groep. |
| Azure-opslagaccount |Nee |Azure Storage bevat geen informatie over systeem updates. |

### <a name="collection-frequency"></a>Verzamelingsfrequentie

Nadat een computer een scan voor de compatibiliteit van updates heeft voltooid, stuurt de agent de gegevens bulksgewijs door naar Azure Monitor Logboeken. Op een Windows-computer wordt de compatibiliteits scan standaard elke 12 uur uitgevoerd.

Naast het scan schema wordt de controle op update vereisten binnen 15 minuten gestart nadat de MMA opnieuw is opgestart, vóór de installatie van de update en na de installatie van de update.

Voor een Linux-computer wordt standaard elk uur de compatibiliteits scan uitgevoerd. Als de MMA-agent opnieuw wordt gestart, wordt een nalevings scan binnen 15 minuten gestart.

Het kan tussen 30 minuten en 6 uur duren voordat het dash board bijgewerkte gegevens van beheerde computers weergeeft.

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Als u updates wilt installeren, plant u een implementatie die wordt uitgelijnd met uw release planning en service venster. U kunt kiezen welke typen updates moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

Als u een nieuwe update-implementatie wilt plannen voor een of meer virtuele machines, klikt u onder **Update beheer**op **Update-implementatie plannen**.

Geef in het deel venster **nieuwe update-implementatie** de volgende informatie op:

- **Naam**: Voer een unieke naam in voor het identificeren van de update-implementatie.
- **Besturings systeem**: Selecteer **Windows** of **Linux**.
- **Groepen om bij te werken (preview)** : definieer een query op basis van een combinatie van abonnement, resourcegroepen, locaties en tags om een dynamische groep virtuele Azure-machines te bouwen voor opname in uw implementatie. Zie [Dynamische groepen](automation-update-management-groups.md) voor meer informatie
- **Machines die moeten worden bijgewerkt**: Selecteer een opgeslagen zoek opdracht, geïmporteerde groep of selecteer machines om de machines te kiezen die u wilt bijwerken. Als u **Computers** selecteert, wordt de gereedheid van de computer weergegeven in de kolom **GEREEDHEID VOOR UPDATE-AGENT**. U kunt de status van de machine bekijken voordat u de update-implementatie plant. Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken

  ![Deel venster nieuwe update-implementatie](./media/manage-update-multi/update-select-computers.png)

- **Update classificatie**: Selecteer de typen software die moeten worden meegenomen in de update-implementatie. Zie [Update classificaties](automation-view-update-assessments.md#update-classifications)voor een beschrijving van de classificatie typen. De classificatietypen zijn:
  - Essentiële updates
  - Beveiligingsupdates
  - Updatepakketten
  - Functiepakketten
  - Servicepacks
  - Definitie-updates
  - Tools
  - Updates

- **Updates om op te nemen/uit te sluiten**: hiermee opent u de pagina **Opnemen/uitsluiten**. Updates die moeten worden opgenomen of uitgesloten, worden op afzonderlijke tabbladen weergegeven. Zie [een update-implementatie plannen](automation-tutorial-update-management.md#schedule-an-update-deployment)voor meer informatie over het verwerken van de opname.

- **Schema-instellingen**: U kunt de standaarddatum en -tijd accepteren (30 minuten na de huidige tijd). U kunt ook een andere tijd opgeven.

   U kunt bovendien opgeven of de implementatie eenmaal moet worden uitgevoerd of volgens een terugkerend schema. Als u een terugkerend schema wilt instellen, selecteert u **terugkerend**onder **terugkeer patroon**.

   ![Dialoogvenster Schema-instellingen](./media/manage-update-multi/update-set-schedule.png)

- **Voorafgaande scripts en navolgende scripts**: selecteer de scripts die moeten worden uitgevoerd vóór en na de implementatie. Zie[Manage Pre and Post scripts](pre-post-scripts.md) (Voorafgaande en navolgende scripts beheren) voor meer informatie.
- **Onderhouds venster (minuten)** : Geef op hoe lang de update-implementatie moet plaatsvinden. Met deze instelling zorgt u ervoor dat wijzigingen worden uitgevoerd binnen de gedefinieerde onderhoudsvensters.

- **Besturings element voor opnieuw opstarten** : deze instelling bepaalt hoe opnieuw opstarten voor de update-implementatie wordt afgehandeld.

   |Optie|Beschrijving|
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

In het deel venster met **Update resultaten** ziet u het totale aantal updates en de implementatie resultaten voor de virtuele machine. De tabel aan de rechter kant geeft een gedetailleerde uitsplitsing van elke update en de installatie resultaten. Een van de volgende installatieresultaatwaarden wordt weergegeven:

- **Niet geprobeerd**: de update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was op basis van het gedefinieerde onderhouds venster.
- **Geslaagd**: de update is voltooid.
- **Mislukt**: de update is mislukt.

Selecteer **Alle logboeken** voor een overzicht van alle logboekvermeldingen die tijdens de implementatie zijn gemaakt.

Selecteer de tegel uitvoer om de taak stroom te bekijken van het runbook dat de update-implementatie op de virtuele doel machine beheert.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [updatebeheer-oplossing in azure](../operations-management-suite/oms-solution-update-management.md)voor meer informatie over updatebeheer, zoals Logboeken, uitvoer en fouten.

