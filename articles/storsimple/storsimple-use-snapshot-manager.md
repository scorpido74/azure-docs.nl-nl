---
title: StorSimple Snapshot Manager-gebruikersinterface | Microsoft Documenten
description: Beschrijft de gebruikersinterface van StorSimple Snapshot Manager en legt uit hoe u deze gebruiken om back-uptaken en de back-upcatalogus te beheren.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.custom: ''
ms.openlocfilehash: 56771d2e62289485017f34c6a9ab26e1d53610ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933979"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>De gebruikersinterface van StorSimple Snapshot Manager gebruiken om back-uptaken en back-upcatalogus te beheren

## <a name="overview"></a>Overzicht
De StorSimple Snapshot Manager heeft een intuïtieve gebruikersinterface die u gebruiken om back-ups te maken en te beheren. Deze zelfstudie geeft een inleiding tot de gebruikersinterface en legt vervolgens uit hoe u elk van de componenten gebruiken. Zie [Wat is StorSimple Snapshot Manager voor](storsimple-what-is-snapshot-manager.md) een gedetailleerde beschrijving van de StorSimple Snapshot Manager?

### <a name="console-description"></a>Beschrijving van de console
Als u de gebruikersinterface wilt weergeven, klikt u op het pictogram StorSimple Snapshot Manager op uw bureaublad. Het consolevenster wordt weergegeven, zoals in de volgende afbeelding wordt weergegeven.

![Deelvensters van StorSimple-momentopnamebeheer](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Het consolevenster heeft vijf belangrijke elementen. Klik op de juiste link voor een volledige beschrijving van elk element.

* [Menubalk](#menu-bar) 
* [Gereedschapsbalk](#tool-bar) 
* [Deelvenster Bereik](#scope-pane) 
* [Deelvenster Resultaten](#results-pane) 
* [Deelvenster Acties](#actions-pane) 

Daarnaast ondersteunt de StorSimple Snapshot Manager [toetsenbordnavigatie en een aantal sneltoetsen.](#keyboard-navigation-and-shortcuts)

### <a name="console-accessibility"></a>Toegankelijkheid van de console
De gebruikersinterface van StorSimple Snapshot Manager ondersteunt de toegankelijkheidsfuncties van het Windows-besturingssysteem en de Microsoft Management Console (MMC), evenals enkele StorSimple Snapshot Manager-specifieke sneltoetsen. 

* Ga voor een beschrijving van de [toegankelijkheidsfuncties](https://support.microsoft.com/kb/126449)van Windows naar Sneltoetsen voor Windows . 
* Ga voor een beschrijving van de toegankelijkheidsfuncties van MMC naar [Toegankelijkheid voor MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Ga voor een beschrijving van de [toegankelijkheidsfuncties](#keyboard-navigation-and-shortcuts)van StorSimple Snapshot Manager naar Toetsenbordnavigatie en sneltoetsen .

## <a name="menu-bar"></a>Menubalk
De menubalk boven aan het consolevenster bevat [menu's Bestand,](#file-menu) [Actie](#action-menu), [Weergave](#view-menu), [Favorieten,](#favorites-menu) [Venster](#window-menu)en [Help.](#help-menu)

Klik op een item op de menubalk om een lijst met beschikbare opdrachten in dat menu te bekijken. In het volgende voorbeeld wordt het menu **Weergave** weergegeven dat is geselecteerd op de menubalk.

![Menu Weergeven geselecteerd](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Bestand
Het menu **Bestand** bevat standaard MMC-opdrachten (Microsoft Management Console).

#### <a name="menu-access"></a>Menutoegang
Als u het menu **Bestand wilt** weergeven, klikt u op **Bestand** op de menubalk. Het volgende menu wordt weergegeven.

![Menu StorSimple Snapshot Manager-bestand](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menubeschrijving
In de volgende tabel worden items beschreven die worden weergegeven in het menu **Bestand.**

| Menu-item | Beschrijving |
|:--- |:--- |
| Nieuw |Klik **op Nieuw** om een nieuwe console te maken op basis van de StorSimple Snapshot Manager. |
| Geopend |Klik **op Openen** om een bestaande console te openen. |
| Opslaan |Klik **op Opslaan** om de huidige console op te slaan. |
| Opslaan als |Klik **op Opslaan als** om een nieuwe, hernoemde instantie van de huidige console te maken. Gebruik de optie **Opslaan als** om een weergave aan te passen en op te slaan voor later ophalen. U bijvoorbeeld StorSimple Snapshot Manager-modules maken die naar specifieke servers wijzen. |
| Module toevoegen toevoegen/verwijderen |Klik **op Module toevoegen/verwijderen** om modules toe te voegen of te verwijderen en om knooppunten in het **deelvenster Bereik te** ordenen. Ga voor meer informatie naar [Module-ins en extensies toevoegen, verwijderen en organiseren in MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opties |Klik **op Opties** om het consolepictogram te wijzigen, gebruikerstoegangsmodi en -machtigingen op te geven of consolebestanden te verwijderen om de beschikbare schijfruimte te vergroten. |
| Lijst met bestandspaden |Klik op een pad in de genummerde lijst om een bestand dat u onlangs hebt geopend, opnieuw te openen. |
| Afsluiten |Klik **op Afsluiten** om het menu **Bestand** te sluiten. |

### <a name="action-menu"></a>Actiemenu
Gebruik het menu **Actie** om uit beschikbare acties te selecteren. De items die voor u beschikbaar zijn, zijn afhankelijk van de selectie die u maakt in het deelvenster **Bereik** of het deelvenster **Resultaten.**

#### <a name="menu-access"></a>Menutoegang
Ga als volgt te werk om het menu **Actie** weer te geven:

* Klik met de rechtermuisknop op een item in het **deelvenster Bereik** of het deelvenster **Resultaten.**
* Selecteer een item in het **deelvenster Bereik** of het deelvenster **Resultaten** en klik op **Actie** op de menubalk. 

Als u bijvoorbeeld het bovenste knooppunt in het **deelvenster Bereik** selecteert en vervolgens met de rechtermuisknop of klik op **Actie** in de menubalk, wordt het volgende menu weergegeven.

![Menu StorSimple Snapshot Manager Actie](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Het deelvenster **Handelingen** (rechts van de console) bevat dezelfde lijst met acties als het menu **Actie.** Bovendien bevat het deelvenster **Handelingen** de **menuopties weergave,** waarmee u een aangepaste weergave van het deelvenster **Resultaten** maken.

![Deelvenster Handelingen met het menu Weergave geopend](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menubeschrijving
De volgende tabel bevat een alfabetische lijst met StorSimple Snapshot Manager-acties. 

* In de kolom **Actie** worden acties weergegeven die u uitvoeren op knooppunten en resultaten. 
* In de kolom **Navigatie** wordt uitgelegd hoe u het juiste **actiemenu** weergeeft, zodat u de actie selecteren. Sommige acties worden **Action** weergegeven in meerdere actiemenu's. Selecteer voor deze acties één **navigatieoptie** in de lijst met opsommingstekens. 
* In **de** kolom Beschrijving wordt beschreven hoe u elke actie gebruikt in **het** actiemenu of het deelvenster Handelingen en wordt uitgelegd wat deze actie doet.

> [!NOTE]
> Het deelvenster **Acties** en **de actiemenu's** bevatten extra opties, zoals **Weergave**, Nieuw venster **van hier,** **Vernieuwen,** **Exportlijst**en **Help**. Deze opties zijn beschikbaar als onderdeel van het MMC en zijn niet specifiek voor StorSimple Snapshot Manager. De tabel bevat beschrijvingen van deze opties.
> 
> 

| Actie | Navigatie | Beschrijving |
|:--- |:--- |:--- |
| Verifiëren |Klik op het knooppunt **Apparaten** en klik met de rechtermuisknop op een apparaat in het deelvenster **Resultaten.** |Klik **op Verifiëren** om het wachtwoord in te voeren dat u voor het apparaat hebt geconfigureerd. |
| Klonen |**Back-upcatalogus**uitvouwen, **Cloudmomentopnamen**uitbreiden, op een gedateerde back-up klikken en vervolgens een volume selecteren in het deelvenster **Resultaten.** |Klik **op Clone** om een kopie van een cloudmomentopname te maken en deze op te slaan op een locatie die u aanwijst. |
| Een apparaat configureren |Klik met de rechtermuisknop op het knooppunt **Apparaten.** |Klik **op Een apparaat configureren** om één apparaat of meerdere apparaten te configureren om verbinding te maken met de Windows-host. |
| Back-upbeleid maken |Voer een van de volgende handelingen uit:<ul><li>Klik met de rechtermuisknop op **Back-upbeleid**.</li><li>Klik of vouw **Volumegroepen**uit en klik vervolgens met de rechtermuisknop op een volumegroep.</li><li>Klik op een **back-upcatalogus**of vouw deze uit en klik vervolgens met de rechtermuisknop op een volumegroep.</li></ul> |Klik **op Back-upbeleid maken** om een geplande back-up voor een volumegroep te configureren. |
| Volumegroep maken |Voer een van de volgende handelingen uit:<ul><li>Klik op het knooppunt **Volumes** en klik vervolgens met de rechtermuisknop op een volume in het deelvenster **Resultaten.**</li><li>Klik met de rechtermuisknop op het knooppunt **Volumegroepen.**</li></ul> |Klik **op Volumegroep maken** om volumes toe te wijzen aan een volumegroep. |
| Verwijderen |Klik op een knooppunt of resultaat **Action** (Dit item wordt weergegeven in veel actiemenu's en actiedeelvensters.) **Actions** |Klik **op Verwijderen** om het knooppunt of resultaat dat u hebt geselecteerd te verwijderen. Wanneer het bevestigingsdialoogvenster wordt weergegeven, bevestigt of annuleert u de verwijdering. |
| Details |Klik op het knooppunt **Apparaten** en klik vervolgens met de rechtermuisknop op een apparaat in het deelvenster **Resultaten.** |Klik op **Details** om de configuratiegegevens voor een apparaat te bekijken. |
| Bewerken |Klik **op Back-upbeleid**en klik vervolgens met de rechtermuisknop op een beleid in het deelvenster **Resultaten.** |Klik **op Bewerken** om het back-upschema voor een volumegroep te wijzigen. |
| Exportlijst |Klik op een knooppunt of resultaat **Action** (Dit item wordt weergegeven in alle actiemenu's en actiedeelvensters.) **Actions** |Klik **op Lijst exporteren** om een lijst op te slaan in een CSV-bestand (comma-separated value). U dit bestand vervolgens importeren in een spreadsheettoepassing voor analyse. |
| Help |Klik op een knooppunt of resultaat. (Dit item wordt **Action** weergegeven in alle actiemenu's en actiedeelvensters.) **Actions** |Klik op **Help** om online Help te openen in een apart browservenster. |
| Nieuw venster vanaf hier |Klik op een knooppunt of resultaat **Action** (Dit item wordt weergegeven in alle actiemenu's en actiedeelvensters.) **Actions** |Klik **op Nieuw venster van hier** om een nieuw StorSimple Snapshot Manager-venster te openen. |
| Vernieuwen |Klik op een knooppunt of resultaat **Action** (Dit item wordt weergegeven in alle actiemenu's en actiedeelvensters.) **Actions** |Klik **op Vernieuwen** om het momenteel weergegeven venster StorSimple Snapshot Manager bij te werken. |
| Apparaat vernieuwen |Klik op het knooppunt **Apparaten** en klik met de rechtermuisknop op een apparaat in het deelvenster **Resultaten.** |Klik **op Apparaat vernieuwen** om een specifiek verbonden apparaat te synchroniseren met StorSimple Snapshot Manager. |
| Apparaten vernieuwen |Klik met de rechtermuisknop op het knooppunt **Apparaten.** |Klik **op Apparaten vernieuwen** om de lijst met verbonden apparaten te synchroniseren met StorSimple Snapshot Manager. |
| Volumes opnieuw scannen |Klik met de rechtermuisknop op het knooppunt **Volumes.** |Klik **op Volumes opnieuw scannen** om de lijst met volumes bij te werken die in het deelvenster **Resultaten** worden weergegeven. |
| Herstellen |Vouw **back-upcatalogus**uit, vouw een volumegroep uit, vouw **Lokale momentopnamen** of **Cloudsnapshots**uit en klik vervolgens met de rechtermuisknop op een back-up. |Klik **op Herstellen** om de huidige volumegroepgegevens te vervangen door de gegevens van de geselecteerde back-up. |
| Maak een back-up |Voer een van de volgende handelingen uit:<ul><li>**Volumegroepen**uitvouwen en klik vervolgens met de rechtermuisknop op een volumegroep.</li><li>Vouw **back-upcatalogus**uit en klik vervolgens met de rechtermuisknop op een volumegroep.</li></ul> |Klik **op Back-up maken** om onmiddellijk een back-uptaak te starten. |
| Weergave importeren in- of uitschakelen |Klik met de rechtermuisknop op het bovenste knooppunt in het deelvenster **Scope** (het knooppunt **Van Momentopname van StorSimple** in de voorbeelden). |Klik **op Weergave importeren inschakelen** om de volumegroepen en bijbehorende back-ups weer te geven of te verbergen die zijn geïmporteerd uit het storSimple Device Manager-servicedashboard. |

### <a name="view-menu"></a>Menu Beeld
Gebruik het menu **Weergave** om een aangepaste weergave van de inhoud van het deelvenster **Resultaten** te maken. Het menu **Weergave** bevat **Kolommen toevoegen/verwijderen** en opties **aanpassen.**

#### <a name="menu-access"></a>Menutoegang
U hebt toegang tot het menu **Weergave** op de menubalk of in het deelvenster **Handelingen.**

![Menu StorSimple Snapshot Manager-weergave](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menubeschrijving
In de volgende tabel worden items beschreven die worden weergegeven in het menu **Weergave.**

| Menu-item | Beschrijving |
|:--- |:--- |
| Kolommen toevoegen/verwijderen |Klik **op Kolommen toevoegen/verwijderen** om kolommen toe te voegen of te verwijderen in het deelvenster **Resultaten.** |
| Aanpassen |Klik **op Aanpassen** om items weer te geven of te verbergen in het consolevenster Van StorSimple Snapshot Manager. |

### <a name="favorites-menu"></a>Menu Favorieten
Gebruik het menu **Favorieten** om paginaweergaven en taken die u vaak gebruikt, toe te voegen, te verwijderen en te ordenen. 

#### <a name="menu-access"></a>Menutoegang
U het menu **Favorieten** openen op de menubalk.

![Menu StorSimple Snapshot Manager-favorieten](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menubeschrijving
In de volgende tabel worden items beschreven die worden weergegeven in het menu **Favorieten.**

| Menu-item | Beschrijving |
|:--- |:--- |
| Toevoegen aan Favorieten |Klik **op Toevoegen aan favorieten** om de huidige weergave toe te voegen aan uw lijst met favorieten. |
| Favorieten organiseren |Klik **op Favorieten organiseren** om de inhoud van de map Favorieten te ordenen. |

### <a name="window-menu"></a>Venstermenu
Gebruik het menu **Venster** om de consolevensters van StorSimple Snapshot Manager toe te voegen en opnieuw te rangschikken.

#### <a name="menu-access"></a>Menutoegang
U hebt toegang tot het **menu Venster** op de menubalk.

![Venster van StorSimple-momentopnamebeheer](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

De genummerde lijst onder aan het menu toont de vensters die momenteel geopend zijn. Klik op een venster in die lijst om het venster naar de voorgrond te brengen. 

#### <a name="menu-description"></a>Menubeschrijving
In de volgende tabel worden de items beschreven die in het menu Venster worden weergegeven.

| Menu-item | Beschrijving |
|:--- |:--- |
| Nieuw venster |Klik **op Nieuw venster** om een nieuw consolevenster te openen (naast het bestaande venster). |
| Trapsgewijze |Klik **op Trapsgewijs** om de geopende consolevensters in een trapsgewijze stijl weer te geven. |
| Tegel horizontaal |Klik horizontaal op **Tegel** om de geopende consolevensters weer te geven in een tegel- of rasterindeling. |
| Pictogrammen rangschikken |Als u meerdere consolevensters open en verspreid over uw bureaublad hebt, minimaliseert u deze en klikt u vervolgens op **Pictogrammen rangschikken** om ze in een horizontale rij onder aan het scherm te rangschikken. |

### <a name="help-menu"></a>Menu Help
Gebruik het **Help-menu** om de beschikbare online help voor StorSimple Snapshot Manager en het MMC weer te geven. U ook informatie bekijken over de softwareversies MMC en StorSimple Snapshot Manager die momenteel op uw systeem zijn geïnstalleerd. 

U hebt toegang tot het **Menu Help** op de menubalk. U ook de helponderwerpen van StorSimple Snapshot Manager openen in het deelvenster **Handelingen.**

![StorSimple-knopbeheermenu](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menubeschrijving
In de volgende tabel worden items beschreven die in het Menu Help worden weergegeven.

| Menu-item | Beschrijving |
|:--- |:--- |
| Hulp bij StorSimple Snapshot Manager |Klik **op Help op StorSimple Snapshot Manager** om StorSimple Snapshot Manager help in een apart venster te openen. |
| Help-onderwerpen |Klik op **Help-onderwerpen** om Online Help van MMC in een apart venster te openen. |
| Website van TechCenter |Klik **op TechCenter-website** om de startpagina van het Microsoft TechNet Tech Center in een apart venster te openen. |
| Informatie over Microsoft Management Console |Klik **op Microsoft Management Console** om te zien welke versie van de Microsoft Management Console op uw systeem is geïnstalleerd. |
| Over StorSimple Snapshot Manager |Klik **op Over StorSimple Snapshot Manager** om te zien welke versie van de module op uw systeem is geïnstalleerd. |

## <a name="tool-bar"></a>Gereedschapsbalk
De gereedschapsbalk, die zich onder de menubalk bevindt, bevat navigatie- en taakpictogrammen. Elk pictogram is een snelkoppeling naar een specifieke taak.

### <a name="icon-descriptions"></a>Pictogrambeschrijvingen
In de volgende tabel worden de pictogrammen beschreven die op de gereedschapsbalk worden weergegeven. 

| Pictogram | Beschrijving |
|:--- |:--- |
| ![Pijl-links](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Klik op het pijl-linkspictogram om terug te keren naar de vorige pagina. |
| ![Pijl-rechts](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Klik op de rechterpijl om naar de volgende pagina te gaan (als de pijl grijs is, is de actie niet beschikbaar). |
| ![Pictogram Omhoog](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Klik op het pictogram omhoog om één niveau hoger te gaan in de consolestructuur (het **deelvenster Bereik).** |
| ![Consolestructuur weergeven/verbergen](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Klik op het pictogram consolestructuur weergeven/verbergen om het **deelvenster Bereik** weer te geven of te verbergen. |
| ![Exportlijst](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Klik op het pictogram lijst exporteren om een lijst te exporteren naar een CSV-bestand dat u opgeeft. |
| ![Help-pictogram](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Klik op het helppictogram om een online MMC Help-onderwerp te openen. |
| ![Deelvenster Acties weergeven/verbergen](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Klik op het pictogram van het deelvenster **Handelingen** weergeven/verbergen om het deelvenster **Handelingen** weer te geven of te verbergen. |

## <a name="scope-pane"></a>Deelvenster Bereik
Het deelvenster **Scope** is het meest linkse deelvenster in de gebruikersinterface van De Momentopname van StorSimple. Het bevat de console (of knooppunt) boom en is het primaire navigatiemechanisme voor StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Structuur van het deelvenster Scope
Het deelvenster **Scope** bevat een reeks klikbare objecten (knooppunten) die zijn georganiseerd in een structuur structuur. 

![Deelvenster Bereik](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Als u een knooppunt wilt uitvouwen of samenvouwen, klikt u op het pijlpictogram naast de naam van het knooppunt.
* Als u de status of inhoud van een knooppunt wilt weergeven, klikt u op de naam van het knooppunt. De informatie wordt weergegeven in het deelvenster **Resultaten.** 

Het deelvenster **Bereik** bevat de volgende knooppunten: 

* [Knooppunt apparaten](#devices-node) 
* [Volumes knooppunt](#volumes-node) 
* [Knooppunt Volumegroepen](#volume-groups-node) 
* [Knooppunt Back-upbeleid](#backup-policies-node) 
* [Knooppunt Back-upcatalogus](#backup-catalog-node) 
* [Vacatures knooppunt](#jobs-node) 

### <a name="scope-pane-tasks"></a>Taken van het deelvenster Bereik
U het deelvenster **Bereik** gebruiken om een actie op een specifiek knooppunt uit te voeren. Ga als volgt te werk om een taak te selecteren:

* Klik met de rechtermuisknop op het knooppunt en selecteer de taak in het menu dat wordt weergegeven.
* Klik op het knooppunt en klik vervolgens op **Actie** op de menubalk. Selecteer de taak in het menu dat wordt weergegeven.
* Klik op het knooppunt en selecteer de actie in het deelvenster **Handelingen.**

Wanneer u een knooppunt selecteert en een van deze methoden gebruikt om een takenlijst te zien, worden alleen de acties weergegeven die op dat knooppunt kunnen worden uitgevoerd.

### <a name="devices-node"></a>Knooppunt apparaten
Het knooppunt **Apparaten** vertegenwoordigt de StorSimple-apparaten en StorSimple-virtuele apparaten die zijn verbonden met StorSimple Snapshot Manager. Selecteer dit knooppunt om verbinding te maken en een apparaat te configureren en importeer de bijbehorende volumes, volumesgroepen en bestaande back-ups. Meerdere apparaten kunnen op één host worden aangesloten.

* Als u het knooppunt wilt uitvouwen, klikt u op het pijlpictogram naast **Apparaten**.
* Als u een menu met beschikbare acties wilt zien, klikt u met de rechtermuisknop op het knooppunt **Apparaten** of klikt u met de rechtermuisknop op een van de knooppunten die in de uitgebreide weergave worden weergegeven.
* Als u een lijst met geconfigureerde apparaten wilt weergeven, klikt u op **Apparaten** in het **deelvenster Bereik.** De lijst met apparaten, samen met informatie over elk apparaat, wordt weergegeven in het deelvenster **Resultaten.**

### <a name="volumes-node"></a>Volumes knooppunt
Het knooppunt **Volumes** vertegenwoordigt de stations die overeenkomen met de volumes die door de host zijn gemonteerd, inclusief de stations die via iSCSI zijn ontdekt en die via een apparaat zijn ontdekt. Gebruik dit knooppunt om de lijst met beschikbare volumes weer te geven en afzonderlijke volumes toe te wijzen aan volumegroepen.

* Als u het knooppunt wilt uitvouwen, klikt u op het pijlpictogram naast **Volumes**.
* Als u een menu met beschikbare acties wilt zien, klikt u met de rechtermuisknop op het knooppunt **Volumes** of klikt u met de rechtermuisknop op een van de knooppunten die in de uitgebreide weergave worden weergegeven.
* Als u een lijst met volumes wilt weergeven, klikt u op **Volumes** in het deelvenster **Bereik.** De lijst met volumes, samen met informatie over elk volume, wordt weergegeven in het deelvenster **Resultaten.**

### <a name="volume-groups-node"></a>Knooppunt Volumegroepen
Volumegroepen worden ook wel consistentiegroepen genoemd. Elke volumegroep is een pool van toepassingsgerelateerde volumes die helpt om de consistentie van de toepassing tijdens back-upbewerkingen te garanderen. Gebruik het knooppunt **Volumegroepen** om deze groepen te configureren en interactieve back-ups te maken of back-upschema's te maken. 

* Als u het knooppunt wilt uitvouwen, klikt u op het pijlpictogram naast **Volumegroepen**.
* Als u een menu met beschikbare acties wilt zien, klikt u met de rechtermuisknop op het knooppunt **Volumegroepen** of klikt u met de rechtermuisknop op een van de knooppunten die in de uitgebreide weergave worden weergegeven.
* Als u een lijst met volumegroepen wilt bekijken, klikt u op **Volumegroepen** in het **deelvenster Bereik.** De lijst met volumegroepen, samen met informatie over elke volumegroep, wordt weergegeven in het deelvenster **Resultaten.**

### <a name="backup-policies-node"></a>Knooppunt Back-upbeleid
Back-upbeleid zijn taakschema's voor lokale en cloudmomentopnamen. Gebruik het knooppunt **Back-upbeleid** om aan te geven hoe vaak een back-up wordt gemaakt en hoe lang een back-up moet worden bewaard. 

* Als u het knooppunt wilt uitbreiden, klikt u op het pijlpictogram naast **Back-upbeleid**.
* Als u een menu met beschikbare acties wilt zien, klikt u met de rechtermuisknop op het knooppunt **Back-upbeleid** of klikt u met de rechtermuisknop op een van de knooppunten die in de uitgebreide weergave worden weergegeven.
* Als u een lijst met back-upbeleid wilt bekijken, klikt u op **Back-upbeleid** in het deelvenster **Bereik.** De lijst met back-upbeleid en informatie over elk beleid worden weergegeven in het deelvenster **Resultaten.**

> [!NOTE]
> U maximaal 64 back-ups behouden.


### <a name="backup-catalog-node"></a>Knooppunt Back-upcatalogus
Het knooppunt **Back-upcatalogus** bevat lijsten met on-site en off-site back-ups van Azure StorSimple-volumes. Dit knooppunt is georganiseerd op volumegroep en elke volumegroepcontainer bevat afzonderlijke structuren voor lokale momentopnamen (het knooppunt **van Lokale momentopnamen)** en cloudmomentopnamen (het knooppunt **Cloudsnapshots).** In elke volumegroepcontainer worden in elke volumegroepcontainer alle succesvolle back-ups weergegeven die interactief of door een geconfigureerd beleid zijn gemaakt.

* Als u het knooppunt wilt uitbreiden, klikt u op het pijlpictogram naast **Back-upcatalogus**.
* Als u een menu met beschikbare acties wilt zien, klikt u met de rechtermuisknop op het knooppunt **Back-upcatalogus** of klikt u met de rechtermuisknop op een van de knooppunten die in de uitgebreide weergave worden weergegeven.
* Als u een lijst met back-upmomentopnamen wilt bekijken, klikt u op **Back-upcatalogus** in het deelvenster **Bereik.** De lijst met momentopnamen, samen met informatie over elke momentopname, wordt weergegeven in het deelvenster **Resultaten.**

### <a name="local-snapshots-node"></a>Knooppunt Lokale momentopnamen
Het knooppunt **Lokale momentopnamen** bevat lokale momentopnamen voor een specifieke volumegroep. Het knooppunt bevindt zich onder het knooppunt **Back-upcatalogus** in het deelvenster **Bereik.** Lokale momentopnamen zijn point-in-time kopieën van volumegegevens die zijn opgeslagen op het Azure StorSimple-apparaat. Dit type back-up kan doorgaans snel worden gemaakt en hersteld. U een lokale momentopname gebruiken zoals u een lokale back-upkopie zou maken.

* Als u het knooppunt wilt uitvouwen, klikt u op het pijlpictogram naast **Lokale momentopnamen**.
* Als u een menu met beschikbare acties wilt zien, klikt u met de rechtermuisknop op het knooppunt **Lokale momentopnamen** of klikt u met de rechtermuisknop op een van de knooppunten die in de uitgebreide weergave worden weergegeven.
* Als u een lijst met lokale momentopnamen wilt bekijken, klikt u op **Lokale momentopnamen** in het **deelvenster Bereik.** De lijst met momentopnamen, samen met informatie over elke momentopname, wordt weergegeven in het deelvenster **Resultaten.**

### <a name="cloud-snapshots-node"></a>Knooppunt voor cloudmomentopnamen
Het knooppunt **CloudSnapshots** bevat cloudmomentopnamen voor een specifieke volumegroep. Het knooppunt bevindt zich onder het knooppunt **Back-upcatalogus** in het deelvenster **Bereik.** Cloudmomentopnamen zijn point-in-time kopieën van volumegegevens die in de cloud zijn opgeslagen. Een cloudmomentopname is gelijk aan een momentopname die wordt gerepliceerd op een ander, off-site opslagsysteem. Cloudsnapshots zijn vooral handig in scenario's voor noodherstel.

* Als u het knooppunt wilt uitbreiden, klikt u op het pijlpictogram naast **Cloudsnapshots**.
* Als u een menu met beschikbare acties wilt zien, klikt u met de rechtermuisknop op het knooppunt **Cloudmomentopnamen** of klikt u met de rechtermuisknop op een van de knooppunten die in de uitgebreide weergave worden weergegeven.
* Als u een lijst met cloudmomentopnamen wilt bekijken, klikt u op **Cloudmomentopnamen** in het deelvenster **Bereik.** De lijst met momentopnamen, samen met informatie over elke momentopname, wordt weergegeven in het deelvenster **Resultaten.**

### <a name="jobs-node"></a>Vacatures knooppunt
Het knooppunt **Taken** bevat informatie over geplande, lopende en recent voltooide back-uptaken. 

* Als u het knooppunt wilt uitvouwen, klikt u op het pijlpictogram naast **Vacatures**.
* Als u een menu met beschikbare acties wilt zien, klikt u met de rechtermuisknop op het knooppunt **Vacatures** of klikt u met de rechtermuisknop op een van de knooppunten die in de uitgebreide weergave worden weergegeven.
* Als u een lijst met geplande taken wilt weergeven, vouwt u het knooppunt **Taken** uit en klikt u op **Gepland**. De lijst met eerder geconfigureerde taken en informatie over elke taak wordt weergegeven in het deelvenster **Resultaten.** 
* Als u een lijst met onlangs voltooide taken wilt bekijken, vouwt u het knooppunt **Vacatures** uit en klikt u op **Laatste 24 uur**. Er wordt een lijst met taken weergegeven die in de afgelopen 24 uur zijn voltooid in het deelvenster **Resultaten.** Het deelvenster **Resultaten** bevat ook informatie over elke voltooide taak.
* Als u een lijst wilt zien met taken die momenteel worden uitgevoerd, vouwt u het knooppunt **Vacatures** uit en klikt u op **Uitvoeren**. De lijst met momenteel lopende taken en informatie over elke taak wordt weergegeven in het deelvenster **Resultaten.**

## <a name="results-pane"></a>Deelvenster Resultaten
Het deelvenster **Resultaten** is het middelste deelvenster in de gebruikersinterface van De Momentopname van StorSimple. Het bevat lijsten en gedetailleerde statusgegevens voor het knooppunt dat u in het deelvenster **Bereik** hebt geselecteerd.

### <a name="example"></a>Voorbeeld
Als u het volgende voorbeeld wilt zien, klikt u op het knooppunt **Volumegroepen** in het deelvenster **Bereik.** In het deelvenster **Resultaten** wordt een lijst met volumegroepen weergegeven met details over elke groep.

![Deelvenster Resultaten](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

U de details in het deelvenster **Resultaten** configureren: klik met de rechtermuisknop op een knooppunt in het **deelvenster Bereik,** klik op **Weergave**en klik vervolgens op **Kolommen toevoegen/verwijderen**.

## <a name="actions-pane"></a>Deelvenster Acties
Het deelvenster **Handelingen** is het rechterdeelvenster in de gebruikersinterface van De Stijlmomentopnamebeheer. Het bevat een menu met bewerkingen dat u uitvoeren op het knooppunt, de weergave of de gegevens die u selecteert in het deelvenster **Bereik** of het deelvenster **Resultaten.** Het deelvenster **Handelingen** bevat dezelfde opdrachten als de **actiemenu's** die beschikbaar zijn voor items in het deelvenster **Bereik** en het deelvenster **Resultaten.** Zie de tabel in de sectie **Actiemenu** voor een beschrijving van elke actie.

### <a name="examples"></a>Voorbeelden
Als u het volgende **Scope** voorbeeld wilt zien, vouwt u het knooppunt **Taken** uit en klikt u op **Gepland**. In het deelvenster **Handelingen** worden de beschikbare acties voor het **geplande** knooppunt weergegeven.

![Voorbeeld van geplande taken in het deelvenster Acties](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Als u meer opties wilt zien, vouwt u in het deelvenster **Bereik** het knooppunt **Taken** uit, klikt u op **Gepland**en klikt u vervolgens op een geplande taak in het deelvenster **Resultaten.** In het deelvenster **Handelingen** worden de beschikbare acties voor de geplande taak weergegeven, zoals in het volgende voorbeeld wordt weergegeven.

![Voorbeeld van taakacties in het deelvenster Acties](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Toetsenbordnavigatie en sneltoetsen
StorSimple Snapshot Manager maakt de toegankelijkheidsfuncties van het Windows-besturingssysteem en de Microsoft Management Console (MMC) mogelijk. Het bevat ook een aantal functies voor toetsenbordnavigatie en sneltoetsen die specifiek zijn voor de StorSimple Snapshot Manager, zoals beschreven in de volgende secties.

* [Navigatietoetsen toetsenbord](#keyboard-navigation-keys) 
* [Sneltoetsen op de menubalk](#menu-bar-shortcut-keys) 
* [Sneltoetsen in het deelvenster Bereik](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Navigatietoetsen toetsenbord
In de volgende tabel worden de toetsen beschreven die u gebruiken om door de gebruikersinterface van StorSimple Snapshot Manager te navigeren. 

| Navigatiesleutel | Actie |
|:--- |:--- |
| Pijl |Gebruik de pijl-omlaag om verticaal naar het volgende item in een menu of deelvenster te gaan. |
| Enter |Druk op de enter-toets om een actie te voltooien en ga vervolgens door naar de volgende stap. U bijvoorbeeld op Enter drukken om **Volgende**, **OK**of **Maken**te selecteren en vervolgens naar de volgende stap in een wizard gaan. |
| Esc |Druk op de Esc-toets om een menu te sluiten of om een pagina te annuleren en te sluiten. |
| F1 |Druk op de F1-toets om een helponderwerp voor het momenteel actieve venster weer te geven. |
| F5 |Druk op de F5-toets om een knooppunt te vernieuwen. |
| F6 |Druk op de F6-toets om van het deelvenster **Bereik** naar het deelvenster **Resultaten** te gaan. |
| F10 |Druk op de F10-toets om naar de menubalk te gaan. |
| Pijltoets-links |Gebruik de pijl-linksom horizontaal van een menubalknaar de vorige optie te gaan. Wanneer u naar het vorige item op de menubalk gaat, wordt het actiemenu (of context) voor het vorige item weergegeven. |
| Pijltoets-rechts |Gebruik de pijl-rechtsom horizontaal van de ene menubalknaar de volgende te gaan. Wanneer u naar het volgende item op de menubalk gaat, wordt het actiemenu (of context) voor het nieuwe item weergegeven. |
| Tabtoets |Gebruik de Tab-toets om naar het volgende deelvenster op de console of naar het volgende selectie- of tekstvak op een pagina te gaan. |
| Pijl-omhoog |Gebruik de pijl-omhoog om verticaal naar het vorige item in een menu of deelvenster te gaan. |

### <a name="menu-bar-shortcut-keys"></a>Sneltoetsen op de menubalk
In de volgende tabel worden de combinaties van sneltoetsen voor de menubalk beschreven. Nadat u op de sneltoetsen hebt ingedrukt en het menu wordt geopend, u sneltoetsen (de onderstreepte toetsen in het menu) gebruiken. Ga voor meer informatie over de menubalk naar [menubalk.](#menu-bar)

| Snelkoppeling | Resultaat | Sneltoets menu | Resultaat |
|:--- |:--- |:--- |:--- |
| ALT+F |Hiermee opent u het menu **Bestand.** |N |Hiermee opent u een nieuwe console-instantie. |
|  |O |Hiermee opent u de pagina **Systeembeheer.** | |
|  |S |Slaat de StorSimple Snapshot Manager-console op. | |
|  |A |Hiermee opent u de pagina **Opslaan als.** | |
|  |M |Hiermee opent u de modulepagina **toevoegen/verwijderen.** | |
|  |P |Hiermee opent u de pagina **Opties.** | |
|  |H |Opent online Help. | |
| ALT+A |Hiermee opent u het menu **Actie.** |I |Hiermee schakelt u de optie importweergave in en uit. |
|  |W |Hiermee opent u een nieuwe StorSimple Snapshot Manager-console. | |
|  |F |Werkt de StorSimple Snapshot Manager-console bij. | |
|  |L |Hiermee opent u de pagina **Exportlijst.** | |
|  |H |Opent online Help. | |
| ALT+V |Hiermee opent u het menu **Weergave.** |A |Hiermee opent u de pagina **Kolommen toevoegen/verwijderen.** |
|  |U |Hiermee opent u de pagina **Weergave aanpassen.** | |
| ALT+O |Hiermee opent u het menu **Favorieten.** |A |Hiermee opent u de pagina **Toevoegen aan favorieten.** |
|  |O |Hiermee opent u de pagina **Favorieten organiseren.** | |
| ALT+W |Hiermee opent u het menu **Venster.** |N |Hiermee opent u een ander venster Van Eenvoudige Momentopnamebeheer. |
|  |C |Hiermee worden alle geopende consolevensters in een trapsgewijze stijl weergegeven. | |
|  |T |Hiermee worden alle geopende consolevensters weergegeven in een rasterpatroon. | |
|  |I |Rangschikt pictogrammen in een horizontale rij aan de onderkant van het scherm. | |
| ALT+H |Hiermee opent u het **menu Help.** |H |Opent online Help. |
|  |T |Hiermee opent u de webpagina van Microsoft TechNet Tech Center. | |
|  |A |Hiermee opent u de pagina **Over Microsoft Management Console.** | |

### <a name="scope-pane-shortcut-keys"></a>Sneltoetsen in het deelvenster Bereik
In de volgende tabellen worden de combinaties van sneltoetsen voor elk knooppunt in het deelvenster **Bereik** weergegeven. 

* [Sneltoetsen voor het knooppunt apparaten](#devices-node-shortcut-keys)
* [Sneltoetsen voor het knooppunt volumes](#volumes-node-shortcut-keys)
* [Sneltoetsen voor knooppunt volumegroepen](#volume-groups-node-shortcut-keys)
* [Sneltoetsen knooppunt sneltoetsen](#backup-policies-node-shortcut-keys)
* [Sneltoetsen voor back-ups van het knooppunt Voor catalogus](#backup-catalog-node-shortcut-keys)
* [Sneltoetsen voor het knooppunt takenvan taken van het takennet](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Sneltoetsen voor het knooppunt apparaten
| Menusneltoets | Resultaat |
|:--- |:--- |
| C |Hiermee opent u de pagina **Een apparaat configureren.** |
| D |Hiermee wordt de lijst met apparaten en apparaatgegevens vernieuwd. |
| V |Hiermee opent u het menu **Weergave.** |
| W |Hiermee opent u een nieuwe StorSimple Snapshot Manager-console die is gericht op het knooppunt **Details.** |
| F |Werkt de StorSimple Snapshot Manager-console bij. |
| L |Hiermee opent u de pagina **Exportlijst.** |
| H |Opent online Help. |

#### <a name="volumes-node-shortcut-keys"></a>Sneltoetsen voor het knooppunt volumes
| Menusneltoets | Resultaat |
|:--- |:--- |
| V |Hiermee wordt de lijst met volumes bijgewerkt. |
| V (druk twee keer op) |Hiermee opent u het menu **Weergave.** |
| W |Hiermee opent u een nieuwe StorSimple Snapshot Manager-console die is gericht op het knooppunt **Volumes.** |
| F |Werkt de StorSimple Snapshot Manager-console bij. |
| L |Hiermee opent u de pagina **Exportlijst.** |
| H |Opent online Help. |

#### <a name="volume-groups-node-shortcut-keys"></a>Sneltoetsen voor knooppunt volumegroepen
| Menusneltoets | Resultaat |
|:--- |:--- |
| G |Hiermee opent u de pagina **Een volumegroep maken.** |
| V |Hiermee opent u het menu **Weergave.** |
| W |Hiermee opent u een nieuwe StorSimple Snapshot Manager-console die is gericht op het knooppunt **Volumegroepen.** |
| F |Werkt de StorSimple Snapshot Manager-console bij. |
| L |Hiermee opent u de pagina **Exportlijst.** |
| H |Opent online Help. |

#### <a name="backup-policies-node-shortcut-keys"></a>Sneltoetsen knooppunt sneltoetsen
| Menusneltoets | Resultaat |
|:--- |:--- |
| B |Hiermee opent u de pagina **Een beleid maken.** |
| V |Hiermee opent u het menu **Weergave.** |
| W |Hiermee opent u een nieuwe StorSimple Snapshot Manager-console die is gericht op het knooppunt **Volumegroepen.** |
| F |Werkt de StorSimple Snapshot Manager-console bij. |
| L |Hiermee opent u de pagina **Exportlijst **. |
| H |Opent online Help. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Sneltoetsen voor back-ups van het knooppunt Voor catalogus
| Menusneltoets | Resultaat |
|:--- |:--- |
| W |Hiermee opent u een nieuwe StorSimple Snapshot Manager-console die is gericht op het knooppunt **Volumegroepen.** |
| F |Werkt de StorSimple Snapshot Manager-console bij. |
| H |Opent online Help. |

#### <a name="jobs-node-shortcut-keys"></a>Sneltoetsen voor het knooppunt takenvan taken van het takennet
| Menusneltoets | Resultaat |
|:--- |:--- |
| V |Hiermee opent u het menu **Weergave.** |
| W |Hiermee opent u een nieuwe StorSimple Snapshot Manager-console die is gericht op het knooppunt **Vacatures.** |
| F |Werkt de StorSimple Snapshot Manager-console bij. |
| L |Hiermee opent u de pagina **Exportlijst.** |
| H |Opent online Help |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om uw StorSimple-oplossing te beheren.](storsimple-snapshot-manager-admin.md)
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om apparaten te verbinden en te beheren.](storsimple-snapshot-manager-manage-devices.md)

