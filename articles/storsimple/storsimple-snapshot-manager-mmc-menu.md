---
title: StorSimple Snapshot Manager MMC-menuacties | Microsoft Documenten
description: Beschrijft hoe u de standaard mmc-menuacties (Microsoft Management Console) gebruiken in StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: ced075395c4f2a51308d5d3b0cbe4c0153a17816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931466"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>De menuacties van MMC gebruiken in StorSimple Snapshot Manager

## <a name="overview"></a>Overzicht
In StorSimple Snapshot Manager ziet u de volgende acties in alle actiemenu's en alle varianten van het deelvenster **Handelingen.**

* Weergave
* Nieuw venster vanaf hier 
* Vernieuwen 
* Exportlijst 
* Help 

Deze acties maken deel uit van de Microsoft Management Console (MMC) en zijn niet specifiek voor StorSimple Snapshot Manager. In deze zelfstudie worden deze acties beschreven en wordt uitgelegd hoe u elk van deze acties gebruiken in StorSimple Snapshot Manager.

## <a name="view"></a>Weergave
U de optie **Weergave** gebruiken om de weergave van het deelvenster **Resultaten** te wijzigen en de weergave van het consolevenster te wijzigen. 

#### <a name="to-change-the-results-pane-view"></a>De weergave van het deelvenster Resultaten wijzigen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op een knooppunt of vouw het knooppunt uit en klik met de rechtermuisknop op een item in het deelvenster **Resultaten** en klik vervolgens op de optie **Weergave.** 
3. Als u de kolommen wilt toevoegen of verwijderen die in het deelvenster **Resultaten** worden weergegeven, klikt u op **Kolommen toevoegen/verwijderen**. Het dialoogvenster **Kolommen toevoegen/verwijderen** wordt weergegeven.
   
    ![Kolommen toevoegen of verwijderen uit deelvenster Resultaten](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Vul het formulier als volgt in:
   
   * Selecteer items in de lijst **Beschikbare** kolommen en klik op **Toevoegen** om ze toe te voegen aan de lijst **Weergegeven kolommen.** 
   * Klik op items in de lijst **Weergegeven kolommen** en klik op **Verwijderen** om ze uit de lijst te verwijderen. 
   * Selecteer een item in de lijst **Weergegeven** kolommen en klik op **Omhoog** of **Omlaag verplaatsen** om het item omhoog of omlaag in de lijst te verplaatsen. 
   * Klik **op Standaardwaarden herstellen** om terug te keren naar de standaardconfiguratie van het deelvenster **Resultaten.** 
5. Wanneer u klaar bent met uw selecties, klikt u op **OK**. 

#### <a name="to-change-the-console-window-view"></a>De weergave van het consolevenster wijzigen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op een knooppunt, klik op **Weergave**en klik vervolgens op **Aanpassen**. Het dialoogvenster **Aanpassen** wordt weergegeven.
   
    ![Het consolevenster aanpassen](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Schakel de selectievakjes in of uit om items in het consolevenster weer te geven of te verbergen. Wanneer u klaar bent met uw selecties, klikt u op **OK**.

## <a name="new-window-from-here"></a>Nieuw venster vanaf hier
U de optie **Nieuw venster van hier** gebruiken om een nieuw consolevenster te openen.

#### <a name="to-open-a-new-console-window"></a>Een nieuw consolevenster openen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op een knooppunt en klik vervolgens **op Nieuw venster van hier**. 
   
    Er verschijnt een nieuw venster met alleen het bereik dat u hebt geselecteerd. Als u bijvoorbeeld met de rechtermuisknop op het knooppunt **Back-upbeleid** klikt, wordt in het nieuwe venster alleen het knooppunt **Back-upbeleid** weergegeven in het **deelvenster Bereik** en een lijst met gedefinieerde back-upbeleidsregels in het deelvenster **Resultaten.** Zie het volgende voorbeeld
   
    ![Nieuw venster vanaf hier](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Vernieuwen
U de actie **Vernieuwen** gebruiken om het consolevenster bij te werken.

#### <a name="to-update-the-console-window"></a>Het consolevenster bijwerken
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op een knooppunt of vouw het knooppunt uit en klik met de rechtermuisknop op een item in het deelvenster **Resultaten** en klik vervolgens op **Vernieuwen**. 

## <a name="export-list"></a>Exportlijst
U de actie **Lijst exporteren** gebruiken om een lijst op te slaan in een CSV-bestand (comma-separated value). U bijvoorbeeld de lijst met back-upbeleid of de back-upcatalogus exporteren. U het CSV-bestand vervolgens importeren in een spreadsheettoepassing voor analyse.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Een lijst opslaan in een CSV-bestand (comma-separated value)
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten. 
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op een knooppunt of vouw het knooppunt uit en klik met de rechtermuisknop op een item in het deelvenster **Resultaten** en klik vervolgens op **Lijst exporteren**. 
3. Het dialoogvenster **Exportlijst** wordt weergegeven. Vul het formulier als volgt in: 
   
   1. Typ in het vak **Bestandsnaam** een naam voor het CSV-bestand of klik op de pijl om te selecteren in de vervolgkeuzelijst.
   2. Klik **in** het vak Opslaan als op de pijl en selecteer een bestandstype in de vervolgkeuzelijst.
   3. Als u alleen geselecteerde items wilt opslaan, schakelt u de rijen in en klikt u op Het selectievakje **Alleen geselecteerde rijen opslaan.** Schakel het selectievakje **Alleen geselecteerde rijen opslaan** uit als u alle geëxporteerde lijsten wilt opslaan.
   4. Klik op **Opslaan**.
      
      ![Lijst exporteren als een door komma's gescheiden waardebestand](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Help
U het **Help-menu** gebruiken om de beschikbare online help voor StorSimple Snapshot Manager en het MMC weer te geven.

#### <a name="to-view-available-online-help"></a>Beschikbare online help weergeven
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op een knooppunt of vouw het knooppunt uit en klik met de rechtermuisknop op een item in het deelvenster **Resultaten** en klik vervolgens op **Help**. 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [gebruikersinterface van StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Meer informatie over [het gebruik van StorSimple Snapshot Manager om uw StorSimple-oplossing te beheren.](storsimple-snapshot-manager-admin.md)

