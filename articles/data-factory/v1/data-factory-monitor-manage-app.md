---
title: Gegevenspijplijnen bewaken en beheren - Azure
description: Meer informatie over het gebruik van de app Monitoring and Management om Azure-gegevensfabrieken en -pijplijnen te bewaken en te beheren.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: de56ba1281d0f20c8be838fa1bc9ebc24905b26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260369"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Azure Data Factory-pijplijnen bewaken en beheren met de app Monitoring and Management
> [!div class="op_single_selector"]
> * [Azure-portal/Azure PowerShell gebruiken](data-factory-monitor-manage-pipelines.md)
> * [De app Monitoring en Beheer gebruiken](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de datafabriekservice gebruikt, raadpleegt u [De pijplijnen van Gegevensfabriek controleren en beheren in](../monitor-visually.md).

In dit artikel wordt beschreven hoe u de app Monitoring en Beheer gebruiken om uw Data Factory-pijplijnen te bewaken, te beheren en te debuggen. U aan de slag met het gebruik van de toepassing door de volgende video te bekijken:

> [!NOTE]
> De gebruikersinterface in de video komt mogelijk niet precies overeen met wat u in de portal ziet. Het is iets ouder, maar concepten blijven hetzelfde. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>De app Monitoring en Beheer starten
Als u de app Monitor en Beheer wilt starten, klikt u op de tegel **Monitor & Beheren** op het blade van **gegevensfabriek** voor uw gegevensfabriek.

![De tegel Controle op de startpagina van Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

U ziet de app Monitoring en Beheer openen in een apart venster.  

![App voor controle en beheer](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Als u ziet dat de webbrowser vastzit bij 'Autoriseren...,, schakelt u het selectievakje **Cookies van derden en sitegegevens blokkeren** uit of houdt u deze geselecteerd, maakt u een uitzondering voor **login.microsoftonline.com**en probeert u de app opnieuw te openen.


In de lijst Activiteitwindows in het middelste deelvenster ziet u een activiteitenvenster voor elke run van een activiteit. Als u bijvoorbeeld de activiteit gepland hebt die vijf uur per uur wordt uitgevoerd, ziet u vijf activiteitsvensters die zijn gekoppeld aan vijf gegevenssegmenten. Als u geen activiteitsvensters ziet in de lijst onderaan, gaat u als volgt te werk:
 
- Werk de **begin-** en **eindtijdfilters** bovenaan bij om de begin- en eindtijden van uw pijplijn aan te passen en klik op de knop **Toepassen.**  
- De lijst Activiteit Windows wordt niet automatisch vernieuwd. Klik **op** de knop Vernieuwen op de werkbalk in de lijst **Activiteit Windows.**  

Als u geen Data Factory-toepassing hebt om deze stappen mee te testen, doet u de zelfstudie: [kopieer gegevens van Blob Storage naar SQL Database met Gegevensfactory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>De app Monitoring en Beheer begrijpen
Er zijn drie tabbladen aan de linkerkant: **Resource Explorer,** **Monitoring Views**, en **Waarschuwingen**. Het eerste tabblad **(Resource Explorer)** is standaard geselecteerd.

### <a name="resource-explorer"></a>Bronverkenner
U ziet het volgende:

* De **structuurweergave** Resource Explorer in het linkerdeelvenster.
* De **diagramweergave** bovenaan in het middelste deelvenster.
* De lijst **ActiviteitWindows** onder in het middelste deelvenster.
* De tabbladen **Eigenschappen,** **Activiteitsvensterverkenner**en **Script** in het rechterdeelvenster.

In Resource Explorer ziet u alle bronnen (pijplijnen, gegevenssets, gekoppelde services) in de gegevensfabriek in een structuurweergave. Wanneer u een object selecteert in Resource Explorer:

* De entiteit Associated Data Factory wordt gemarkeerd in de diagramweergave.
* [Gekoppelde activiteitsvensters](data-factory-scheduling-and-execution.md) worden gemarkeerd in de lijst ActiviteitWindows onderaan.  
* De eigenschappen van het geselecteerde object worden weergegeven in het venster Eigenschappen in het rechterdeelvenster.
* De JSON-definitie van het geselecteerde object wordt weergegeven, indien van toepassing. Bijvoorbeeld: een gekoppelde service, een gegevensset of een pijplijn.

![Bronverkenner](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Zie [het](data-factory-scheduling-and-execution.md) artikel Planning en uitvoering voor gedetailleerde conceptuele informatie over activiteitsvensters.

### <a name="diagram-view"></a>Diagramweergave
De diagramweergave van een gegevensfabriek biedt één ruit om een gegevensfabriek en bijbehorende activa te controleren en te beheren. Wanneer u een entiteit Gegevensfabriek (gegevensset/pijplijn) selecteert in de diagramweergave:

* De entiteit gegevensfabriek is geselecteerd in de structuurweergave.
* De bijbehorende activiteitsvensters worden gemarkeerd in de lijst Activiteitwindows.
* De eigenschappen van het geselecteerde object worden weergegeven in het venster Eigenschappen.

Wanneer de pijplijn is ingeschakeld (niet in een onderbroken status), wordt deze weergegeven met een groene regel:

![Pijplijn loopt](./media/data-factory-monitor-manage-app/PipelineRunning.png)

U een pijplijn onderbreken, hervatten of beëindigen door deze te selecteren in de diagramweergave en de knoppen op de opdrachtbalk te gebruiken.

![Onderbreken/hervatten op de opdrachtbalk](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Er zijn drie knoppen voor de opdrachtbalk voor de pijplijn in de diagramweergave. U de tweede knop gebruiken om de pijplijn te pauzeren. Het onderbreken beëindigt de momenteel lopende activiteiten niet en laat ze doorgaan tot voltooiing. De derde knop pauzeert de pijplijn en beëindigt de bestaande uitvoeringsactiviteiten. De eerste knop hervat de pijplijn. Wanneer de pijplijn is onderbroken, verandert de kleur van de pijplijn. Een onderbroken pijplijn ziet er bijvoorbeeld uit in de volgende afbeelding: 

![Pijplijn onderbroken](./media/data-factory-monitor-manage-app/PipelinePaused.png)

U twee of meer pijplijnen selecteren met de Ctrl-toets. U de knoppen van de opdrachtbalk gebruiken om meerdere pijplijnen tegelijk te pauzeren/hervatten.

U ook met de rechtermuisknop op een pijplijn klikken en opties selecteren om een pijplijn op te schorten, te hervatten of te beëindigen. 

![Contextmenu voor pijplijn](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Klik **op** de optie Pijplijn openen om alle activiteiten in de pijplijn te bekijken. 

![Menu Pijplijn openen](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

In de geopende pijplijnweergave ziet u alle activiteiten in de pijplijn. In dit voorbeeld is er slechts één activiteit: Activiteit kopiëren. 

![Geopende pijplijn](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Als u terug wilt gaan naar de vorige weergave, klikt u op de naam van de gegevensfabriek in het broodkruimelmenu bovenaan.

In de pijplijnweergave ziet u het pop-upvenster Activiteit Windows voor die gegevensset wanneer u een uitvoergegevensset selecteert of wanneer u uw muis over de uitvoergegevensset beweegt.

![Pop-upvenster Activiteit Windows](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

U op een activiteitenvenster klikken om details hiervoor te zien in het venster **Eigenschappen** in het rechterdeelvenster.

![Eigenschappen van activiteitsvenster](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

Ga in het rechterdeelvenster naar het tabblad **Activiteitsvensterverkenner** voor meer details.

![Activiteitsvensterverkenner](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

U ziet ook **opgeloste variabelen** voor elke runpoging voor een activiteit in de sectie **Pogingen.**

![Opgeloste variabelen](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Ga naar het tabblad **Script** om de JSON-scriptdefinitie voor het geselecteerde object te bekijken.   

![Tabblad Script](./media/data-factory-monitor-manage-app/ScriptTab.png)

U activiteitsvensters op drie plaatsen zien:

* De pop-up activiteit Windows in de diagramweergave (middelste deelvenster).
* De activiteitsvensterverkenner in het rechterdeelvenster.
* De lijst Activiteit Windows in het onderste deelvenster.

In de pop-up activiteit windows en Activiteit Venster Verkenner, u scrollen naar de vorige week en de volgende week met behulp van de links en rechts pijlen.

![Pijl-links/rechts in activiteitsvensterverkenner](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Onder aan de diagramweergave ziet u de volgende knoppen: Inzoomen, Uitzoomen, Zoomen op pasvorm, Zoom 100%, Vergrendelingsindeling. Met de knop **Indeling vergrendelen** voorkomt u dat u per ongeluk tabellen en pijplijnen in de diagramweergave verplaatst. Het is standaard ingeschakeld. U het uitschakelen en entiteiten verplaatsen in het diagram. Wanneer u deze uitschakelt, u de laatste knop gebruiken om tabellen en pijplijnen automatisch te plaatsen. U ook in- of uitzoomen met het muiswiel.

![Zoomopdrachten voor diagramweergave](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lijst Activiteitsvensters
In de lijst Activiteitwindows onder aan het middelste deelvenster worden alle activiteitsvensters weergegeven voor de gegevensset die u hebt geselecteerd in de Resourceverkenner of de diagramweergave. Standaard is de lijst in aflopende volgorde, wat betekent dat u bovenaan het laatste activiteitsvenster ziet.

![Lijst Activiteitsvensters](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Deze lijst wordt niet automatisch vernieuwd, dus gebruik de knop Vernieuwen op de werkbalk om deze handmatig te vernieuwen.  

Activiteitsvensters kunnen zich in een van de volgende statussen begeven:

<table>
<tr>
    <th align="left">Status</th><th align="left">Substatus</th><th align="left">Beschrijving</th>
</tr>
<tr>
    <td rowspan="8">Wachten</td><td>PlanningTijd</td><td>De tijd is niet gekomen voor de activiteit venster uit te voeren.</td>
</tr>
<tr>
<td>Afhankelijkvan gegevenssetafhankelijkheden</td><td>De upstream afhankelijkheden zijn nog niet klaar.</td>
</tr>
<tr>
<td>ComputeResources</td><td>De rekenbronnen zijn niet beschikbaar.</td>
</tr>
<tr>
<td>GelijktijdigheidLimiet</td> <td>Alle activiteitsinstanties zijn bezig met het uitvoeren van andere activiteitvensters.</td>
</tr>
<tr>
<td>ActiviteitHervat</td><td>De activiteit wordt onderbroken en kan de activiteitsvensters niet uitvoeren totdat deze is hervat.</td>
</tr>
<tr>
<td>Opnieuw proberen</td><td>De uitvoering van de activiteit wordt opnieuw geprobeerd.</td>
</tr>
<tr>
<td>Validatie</td><td>Validatie is nog niet gestart.</td>
</tr>
<tr>
<td>ValidatieRetry</td><td>Validatie wacht om opnieuw te worden geprobeerd.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Valideren</td><td>De validatie is in volle gang.</td>
</tr>
<td>-</td>
<td>Het activiteitenvenster wordt verwerkt.</td>
</tr>
<tr>
<td rowspan="4">Mislukt</td><td>Time-out</td><td>De uitvoering van de activiteit duurde langer dan wat is toegestaan door de activiteit.</td>
</tr>
<tr>
<td>Geannuleerd</td><td>Het activiteitenvenster is geannuleerd door gebruikersactie.</td>
</tr>
<tr>
<td>Validatie</td><td>Validatie is mislukt.</td>
</tr>
<tr>
<td>-</td><td>Het activiteitsvenster kan niet worden gegenereerd of gevalideerd.</td>
</tr>
<td>Gereed</td><td>-</td><td>Het activiteitenvenster is klaar voor consumptie.</td>
</tr>
<tr>
<td>Overgeslagen</td><td>-</td><td>Het activiteitenvenster is niet verwerkt.</td>
</tr>
<tr>
<td>Geen</td><td>-</td><td>Een activiteitsvenster bestond vroeger met een andere status, maar is opnieuw ingesteld.</td>
</tr>
</table>


Wanneer u op een activiteitenvenster in de lijst klikt, ziet u details hierover in de **activiteit Windows Verkenner** of het venster **Eigenschappen** aan de rechterkant.

![Activiteitsvensterverkenner](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Activiteitsvensters vernieuwen
De details worden niet automatisch vernieuwd, dus gebruik de vernieuwingsknop (de tweede knop) op de opdrachtbalk om de lijst met activiteitsvensters handmatig te vernieuwen.  

### <a name="properties-window"></a>Venster Eigenschappen
Het venster Eigenschappen bevindt zich in het bovenste deelvenster van de app Monitoring en Beheer.

![Venster Eigenschappen](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Er worden eigenschappen weergegeven voor het item dat u hebt geselecteerd in de lijst Resourceverkenner (structuurweergave), diagramweergave of lijst Activiteitwindows.

### <a name="activity-window-explorer"></a>Activiteitsvensterverkenner
Het **venster Activiteitvensterverkenner** bevindt zich in het bovenste deelvenster van de app Monitoring en Beheer. Hierin worden details weergegeven over het activiteitsvenster dat u hebt geselecteerd in het pop-upvenster Activiteit Windows of de lijst Activiteit Windows.

![Activiteitsvensterverkenner](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

U overschakelen naar een ander activiteitenvenster door er bovenin op te klikken in de agendaweergave. U ook de pijl-links/pijl-rechts bovenaan gebruiken om activiteitsvensters van de vorige week of de volgende week te bekijken.

U de werkbalkknoppen in het onderste deelvenster gebruiken om het activiteitenvenster opnieuw uit te voeren of de details in het deelvenster te vernieuwen.

### <a name="script"></a>Script
U het tabblad **Script** gebruiken om de JSON-definitie van de geselecteerde entiteit Data Factory (gekoppelde service, gegevensset of pijplijn) weer te geven.

![Tabblad Script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Systeemweergaven gebruiken
De app Monitoring en Beheer bevat vooraf gebouwde systeemweergaven **(Recente activiteitsvensters**, **Mislukte activiteitsvensters**, **In-Progress-activiteitvensters)** waarmee u recente/mislukte/voortgangsactiviteitvensters voor uw gegevensfabriek weergeven.

Ga naar het tabblad **Controleweergaven** aan de linkerkant door erop te klikken.

![Tabblad Controleweergaven](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Momenteel zijn er drie systeemweergaven die worden ondersteund. Selecteer een optie om recente activiteitvensters, mislukte activiteitvensters of activiteitsvensters in de lijst ActiviteitWindows (onder aan het middelste deelvenster) te bekijken.

Wanneer u de optie **Recente activiteitsvensters** selecteert, ziet u alle recente activiteitsvensters in aflopende volgorde van de **laatste pogingstijd**.

U de weergave **Mislukte activiteitsvensters** gebruiken om alle mislukte activiteitsvensters in de lijst te zien. Selecteer een mislukt activiteitenvenster in de lijst om details hierover te zien in het venster **Eigenschappen** of in het **venster Activiteitsvensterverkenner**. U ook logboeken downloaden voor een mislukt activiteitenvenster.

## <a name="sort-and-filter-activity-windows"></a>Activiteitsvensters sorteren en filteren
Wijzig de **begin-** en **eindtijdinstellingen** in de opdrachtbalk om activiteitsvensters te filteren. Nadat u de begin- en eindtijd hebt gewijzigd, klikt u op de knop naast de eindtijd om de lijst Activiteit Windows te vernieuwen.

![Begin- en eindtijden](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Momenteel zijn alle tijden in UTC-indeling in de app Monitoring and Management.
>
>

Klik in de **lijst Activiteitwindows**op de naam van een kolom (bijvoorbeeld: Status).

![Kolommenu activiteit Windows-lijst](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

U kunt het volgende doen:

* Sorteren in oplopende volgorde.
* Sorteer in aflopende volgorde.
* Filteren op een of meer waarden (Gereed, wachten, enzovoort).

Wanneer u een filter opgeeft in een kolom, ziet u de filterknop die is ingeschakeld voor die kolom, wat aangeeft dat de waarden in de kolom gefilterde waarden zijn.

![Filteren op een kolom van de lijst Activiteit Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

U hetzelfde pop-upvenster gebruiken om filters te wissen. Als u alle filters voor de lijst Activiteit Windows wilt wissen, klikt u op de knop Filter wissen op de opdrachtbalk.

![Alle filters voor de lijst Activiteit Windows wissen](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Batchacties uitvoeren
### <a name="rerun-selected-activity-windows"></a>Geselecteerde activiteitvensters opnieuw uitvoeren
Selecteer een activiteitenvenster, klik op de pijl-omlaag voor de eerste opdrachtbalkknop en selecteer **Opnieuw uitvoeren** / **met upstream in de pijplijn**. Wanneer u de optie **Opnieuw uitvoeren met upstream in pipeline** selecteert, worden ook alle upstream-activiteitvensters opnieuw uitgevoerd.
    ![Een activiteitenvenster opnieuw uitvoeren](./media/data-factory-monitor-manage-app/ReRunSlice.png)

U ook meerdere activiteitsvensters in de lijst selecteren en deze tegelijkertijd opnieuw uitvoeren. U activiteitsvensters filteren op basis van de status **(bijvoorbeeld:** Mislukt)--en vervolgens de mislukte activiteitsvensters opnieuw uitvoeren nadat het probleem is verholpen waardoor de activiteitsvensters mislukken. Zie de volgende sectie voor meer informatie over filteractiviteitvensters in de lijst.  

### <a name="pauseresume-multiple-pipelines"></a>Meerdere pijplijnen onderbreken/hervatten
U twee of meer pijplijnen selecteren met ctrl. U de knoppen van de opdrachtbalk (die zijn gemarkeerd in de rode rechthoek in de volgende afbeelding) gebruiken om ze te pauzeren/hervatten.

![Onderbreken/hervatten op de opdrachtbalk](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
