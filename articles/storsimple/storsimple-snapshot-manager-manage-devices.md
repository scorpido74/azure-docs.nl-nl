---
title: Apparaten beheren met StorSimple Snapshot Manager | Microsoft Documenten
description: Beschrijft hoe u de StorSimple Snapshot Manager MMC-module gebruiken om StorSimple-apparaten aan te sluiten en te beheren.
services: storsimple
documentationcenter: ''
author: twooley
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: a037ddc5a43b288349b17dc7d954991a2c634aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254688"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>StorSimple Snapshot Manager gebruiken om StorSimple-apparaten te verbinden en te beheren
## <a name="overview"></a>Overzicht
U knooppunten gebruiken in het **deelvenster** Scope van StorSimple Snapshot Manager om geïmporteerde StorSimple-apparaatgegevens te verifiëren en verbonden opslagapparaten te vernieuwen. Wanneer u op het knooppunt **Apparaten** klikt, ziet u bovendien een lijst met verbonden apparaten en bijbehorende statusgegevens in het deelvenster **Resultaten.**

![Verbonden apparaten](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figuur 1: Verbonden apparaat storSimple Snapshot Manager** 

Afhankelijk van uw **weergaveselecties** worden in het deelvenster **Resultaten** de volgende informatie over elk apparaat weergegeven. (Ga voor meer informatie over het configureren van een weergave naar [het menu Weergeven](storsimple-use-snapshot-manager.md#view-menu).

| Resultatenkolom | Beschrijving |
|:--- |:--- |
| Name |De naam van het apparaat zoals geconfigureerd in de klassieke Azure-portal |
| Model |Het modelnummer van het apparaat |
| Versie |De versie van de software die op het apparaat is geïnstalleerd |
| Status |Of het apparaat beschikbaar is |
| Laatst gesynchroniseerd |Datum en tijd waarop het apparaat voor het laatst is gesynchroniseerd |
| Seriële nr. |Het serienummer van het apparaat |

Als u met de rechtermuisknop op het knooppunt **Apparaten** in het **deelvenster Bereik** klikt, u kiezen uit de volgende acties:

* Een apparaat toevoegen of vervangen
* Een apparaat verbinden en de invoer verifiëren
* Verbonden apparaten vernieuwen

Als u op het knooppunt **Apparaten** klikt en vervolgens met de rechtermuisknop op een apparaatnaam in het deelvenster **Resultaten** klikt, u kiezen uit de volgende acties:

* Een apparaat verifiëren
* Apparaatdetails weergeven
* Een apparaat vernieuwen
* Een apparaatconfiguratie verwijderen
* Een apparaatwachtwoord wijzigen

> [!NOTE]
> Al deze acties zijn ook beschikbaar in het deelvenster **Handelingen.**


In deze zelfstudie wordt uitgelegd hoe u StorSimple Snapshot Manager gebruiken om apparaten te verbinden en te beheren en de volgende taken uit te voeren:

* Een apparaat toevoegen of vervangen
* Een apparaat verbinden en de invoer verifiëren
* Verbonden apparaten vernieuwen
* Een apparaat verifiëren
* Apparaatdetails weergeven
* Een individueel apparaat vernieuwen
* Een apparaatconfiguratie verwijderen
* Een verlopen apparaatwachtwoord wijzigen
* Een mislukt apparaat vervangen

> [!NOTE]
> Ga voor algemene informatie over het gebruik van de StorSimple Snapshot Manager-interface naar [de gebruikersinterface van StorSimple Snapshot Manager.](storsimple-use-snapshot-manager.md)


## <a name="add-or-replace-a-device"></a>Een apparaat toevoegen of vervangen
Gebruik de volgende procedure om een StorSimple-apparaat toe te voegen of te vervangen.

#### <a name="to-add-or-replace-a-device"></a>Een apparaat toevoegen of vervangen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op het knooppunt **Apparaten** en klik vervolgens op **Een apparaat configureren**. Het dialoogvenster **Een apparaat configureren** wordt weergegeven.
   
    ![Een StorSimple-apparaat configureren](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Selecteer in de vervolgkeuzelijst **Apparaat** het IP-adres van het apparaat of virtueel apparaat. 
4. Typ in het tekstvak **Wachtwoord** het wachtwoord Van Eenvoudige Momentopnamebeheer dat u voor het apparaat hebt gemaakt in de klassieke Azure-portal. Klik op **OK**. StorSimple Snapshot Manager zoekt naar het apparaat dat u hebt geïdentificeerd. 
   
   * Als het apparaat beschikbaar is, voegt StorSimple Snapshot Manager een verbinding toe.
   * Als het apparaat om welke reden dan ook niet beschikbaar is, retourneert StorSimple Snapshot Manager een foutbericht. Klik op **OK** om het foutbericht te sluiten en klik vervolgens op **Annuleren** om het dialoogvenster **Een apparaat configureren** te sluiten.

## <a name="connect-a-device-and-verify-imports"></a>Een apparaat verbinden en de invoer verifiëren
Gebruik de volgende procedure om een StorSimple-apparaat aan te sluiten en te controleren of bestaande volumegroepen met bijbehorende back-ups worden geïmporteerd.

#### <a name="to-connect-a-device-and-verify-imports"></a>Een apparaat verbinden en de invoer verifiëren
1. Als u een apparaat wilt aansluiten op StorSimple Snapshot Manager, volgt u de instructies in Een apparaat toevoegen of vervangen. Wanneer het verbinding maakt met een apparaat, reageert StorSimple Snapshot Manager als volgt:
   
   * Als het apparaat om welke reden dan ook niet beschikbaar is, retourneert StorSimple Snapshot Manager een foutbericht. 
   
   * Als het apparaat beschikbaar is, voegt StorSimple Snapshot Manager een verbinding toe. Wanneer u het apparaat selecteert, wordt het weergegeven in het deelvenster **Resultaten** en geeft het statusveld aan dat het apparaat **beschikbaar**is. StorSimple Snapshot Manager importeert alle volumegroepen die voor het apparaat zijn geconfigureerd, op voorwaarde dat de volumegroepen bijbehorende back-ups hebben. Back-upbeleid wordt niet geïmporteerd. Volumegroepen die geen bijbehorende back-ups hebben, worden niet geïmporteerd.
2. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
3. Klik met de rechtermuisknop op het bovenste knooppunt in het **deelvenster Bereik** en klik vervolgens op Weergave **importeren inschakelen**.
   
    ![Weergave importeren selecteren](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Het dialoogvenster **Weergave voor importeren inschakelen** wordt weergegeven met de status van de geïmporteerde volumegroepen en back-ups. Klik op **OK**.

Nadat de volumegroepen en back-ups zijn geïmporteerd, u StorSimple Snapshot Manager gebruiken om ze te beheren, net zoals u volumegroepen en back-ups beheert die u hebt gemaakt en geconfigureerd met StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Verbonden apparaten vernieuwen
Gebruik de volgende procedure om de verbonden StorSimple-apparaten te synchroniseren met StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Verbonden apparaten vernieuwen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op **Apparaten**en klik vervolgens op **Apparaten vernieuwen**. Hiermee worden de verbonden apparaten gesynchroniseerd met StorSimple Snapshot Manager, zodat u de volumegroepen en back-ups bekijken, inclusief eventuele recente toevoegingen. 
   
    ![De StorSimple-apparaten vernieuwen](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Met de actie **Apparaten vernieuwen** worden nieuwe volumegroepen en eventuele bijbehorende back-ups opgehaald van verbonden apparaten. In tegenstelling tot de actie **Rescanvolumes** die beschikbaar zijn voor het knooppunt **Volumes,** herstelt **vernieuwen apparaten** het back-upregister niet.

## <a name="authenticate-a-device"></a>Een apparaat verifiëren
Gebruik de volgende procedure om een StorSimple-apparaat te verifiëren met StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Een apparaat verifiëren
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik op **Apparaten**.
3. Klik **in** het deelvenster Resultaten met de rechtermuisknop op de naam van het apparaat en klik vervolgens op **Verifiëren**.
4. Het dialoogvenster **Verifiëren** wordt weergegeven. Typ het apparaatwachtwoord en klik op **OK**.
   
    ![Dialoogvenster Verifiëren](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Apparaatdetails weergeven
Gebruik de volgende procedure om de details van een StorSimple-apparaat weer te geven en, indien nodig, het apparaat opnieuw te synchroniseren met StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Apparaatgegevens weergeven en opnieuw synchroniseren
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik op **Apparaten**.
3. Klik **in** het deelvenster Resultaten met de rechtermuisknop op de naam van het apparaat en klik vervolgens op **Details**.

4.Het dialoogvenster **Apparaatgegevens** wordt weergegeven. In dit vak worden de naam, het model, de versie, het serienummer, de status, de bisiSCSI-gebruikersnaam (IQN) en de laatste synchronisatiedatum en -tijd weergegeven.

* Klik **op Opnieuw synchroniseren** om het apparaat te synchroniseren.
* Klik **op OK** of **Annuleren** om het dialoogvenster te sluiten.
  
  ![Apparaatgegevens](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Een individueel apparaat vernieuwen
Gebruik de volgende procedure om een afzonderlijk StorSimple-apparaat opnieuw te synchroniseren met StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Een apparaat vernieuwen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten. 
2. Klik **in** het deelvenster Bereik op **Apparaten**. 
3. Klik **in** het deelvenster Resultaten met de rechtermuisknop op de naam van het apparaat en klik vervolgens op **Apparaat vernieuwen**. Hiermee synchroniseert u het apparaat met StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Een apparaatconfiguratie verwijderen
Gebruik de volgende procedure om een afzonderlijke StorSimple-apparaatconfiguratie te verwijderen uit StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Een apparaatconfiguratie verwijderen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik op **Apparaten**. 
3. Klik **in** het deelvenster Resultaten met de rechtermuisknop op de naam van het apparaat en klik vervolgens op **Verwijderen**. 
4. Het volgende bericht wordt weergegeven. Klik **op Ja** om de configuratie te verwijderen of klik op **Nee** om de verwijdering te annuleren.
   
    ![Apparaatconfiguratie verwijderen](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Een verlopen apparaatwachtwoord wijzigen
U moet een wachtwoord invoeren om een StorSimple-apparaat te verifiëren met StorSimple Snapshot Manager. U configureert dit wachtwoord wanneer u de Windows PowerShell-interface gebruikt om het apparaat in te stellen. Het wachtwoord kan echter verlopen. Als dit gebeurt, u de klassieke Azure-portal gebruiken om het wachtwoord te wijzigen. Omdat het apparaat vervolgens is geconfigureerd in StorSimple Snapshot Manager voordat het wachtwoord is verlopen, moet u het apparaat opnieuw verifiëren in StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Het verlopen wachtwoord wijzigen
1. Start de StorSimple Manager-service in de klassieke Azure-portal.
2. Klik **Devices** > **op Apparaten configureren** voor het apparaat.
3. Blader omlaag naar de sectie StorSimple Snapshot Manager. Voer een wachtwoord in dat 14-15 tekens is. Zorg ervoor dat het wachtwoord een mix van hoofdletters, kleine letters, numerieke en speciale tekens bevat.
4. Voer het wachtwoord opnieuw in om het te bevestigen.
5. Klik op **Opslaan** onder aan de pagina.

#### <a name="to-re-authenticate-the-device"></a>Het apparaat opnieuw verifiëren
1. Start StorSimple Snapshot Manager.
2. Klik **in** het deelvenster Bereik op **Apparaten**. Er wordt een lijst met geconfigureerde apparaten weergegeven in het deelvenster **Resultaten.**
3. Selecteer het apparaat, klik met de rechtermuisknop en klik op **Verifiëren**.
4. Voer **in het venster Verifiëren** het nieuwe wachtwoord in.
5. Selecteer het apparaat, klik met de rechtermuisknop en selecteer **Apparaat vernieuwen**. Hiermee synchroniseert u het apparaat met StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Een mislukt apparaat vervangen
Als een StorSimple-apparaat uitvalt en wordt vervangen door een stand-by (failover)-apparaat, gebruikt u de volgende stappen om verbinding te maken met het nieuwe apparaat en de bijbehorende back-ups weer te geven.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Verbinding maken met een nieuw apparaat na een failover
1. Configureer de iSCSI-verbinding met het nieuwe apparaat opnieuw. Ga voor instructies naar 'Stap 7: Monteren, initialiseren en een volume opmaken' in [Uw on-premises StorSimple-apparaat implementeren.](storsimple-8000-deployment-walkthrough-u2.md)

> [!NOTE]
> Als het nieuwe StorSimple-apparaat hetzelfde IP-adres heeft als het oude, u mogelijk de oude configuratie aansluiten.


1. Stop de Microsoft StorSimple-beheerservice:
   
   1. Start Serverbeheer.
   2. Selecteer Services in het dashboard Serverbeheer in **het** menu **Extra**.
   3. Selecteer in het venster **Services** de **Microsoft StorSimple Management Service**.
   4. Klik in het rechterdeelvenster onder **Microsoft StorSimple Management Service**op De service **stoppen**.
2. Verwijder de configuratie-informatie met betrekking tot het oude apparaat:
   
   1. Blader in Verkenner naar C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Verwijder de bestanden in de map BACatalog.
3. Start de Microsoft StorSimple-beheerservice opnieuw:
   
   1. Selecteer Services in het dashboard Serverbeheer in **het** menu **Extra**.
   2. Selecteer in het venster **Services** de **Microsoft StorSimple Management Service**.
   3. Klik in het rechterdeelvenster onder **Microsoft StorSimple Management Service**op De service opnieuw **starten**.
4. Start StorSimple Snapshot Manager.
5. Als u het nieuwe StorSimple-apparaat wilt configureren, voert u de stappen uit in stap 2: Sluit een StorSimple-apparaat aan in [Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Klik met de rechtermuisknop op het knooppunt op het bovenste niveau in het **deelvenster Bereik** (StorSimple Snapshot Manager in het voorbeeld) en klik vervolgens op Weergave importeren op het hoogste **niveau inschakelen**. 
7. Er verschijnt een bericht wanneer de geïmporteerde volumegroepen en back-ups zichtbaar zijn in StorSimple Snapshot Manager. Klik op **OK**.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om uw StorSimple-oplossing te beheren.](storsimple-snapshot-manager-admin.md)
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om volumes weer te geven en te beheren.](storsimple-snapshot-manager-manage-volumes.md)

