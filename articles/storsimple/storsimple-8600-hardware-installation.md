---
title: Microsoft Azure StorSimple 8600-apparaat installeren
description: Beschrijft hoe u uw StorSimple 8600-apparaat uitpakken, rackmounten en kabelen voordat u de software implementeert en configureert.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254753"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Pak uw StorSimple 8600-apparaat uit, reken en kabel

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht
Uw Microsoft Azure StorSimple 8600 is een apparaat met dubbele behuizing en bestaat uit een primaire en een EBOD-behuizing. In deze zelfstudie wordt uitgelegd hoe u de StorSimple 8600-apparaathardware uitpakken, rackmonteren en bekabelen voordat u de StorSimple-software configureert.

## <a name="unpack-your-storsimple-8600-device"></a>Pak uw StorSimple 8600-apparaat uit
De volgende stappen geven duidelijke, gedetailleerde instructies over het uitpakken van uw StorSimple 8600-opslagapparaat. Dit apparaat wordt verzonden in twee dozen, een voor de primaire behuizing en een andere voor de EBOD-behuizing. Deze twee dozen worden vervolgens in één doos geplaatst.

### <a name="prepare-to-unpack-your-device"></a>Bereid je voor om je apparaat uit te pakken
Voordat u uw apparaat uitpakt, controleert u de volgende informatie.

![Waarschuwingspictogram](./media/storsimple-safety/IC740879.png)![pictogram](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) zwaar gewicht **pictogram WAARSCHUWING!**

1. Zorg ervoor dat u twee personen beschikbaar hebt om het gewicht van het apparaat te beheren als u het handmatig verwerkt. Een volledig geconfigureerde behuizing kan tot 32 kg wegen.
2. Leg de doos op een vlak, horizontaal oppervlak.

Voer vervolgens de volgende stappen uit om uw apparaat uit te pakken.

#### <a name="to-unpack-your-device"></a>Uw apparaat uitpakken
1. Controleer de doos en het verpakkingsmateriaal op deuken, scheuren, waterschade of andere duidelijk zichtbare schade. Als de doos of het verpakkingsmateriaal ernstig beschadigd is, moet u de doos niet openen. Neem [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) om te beoordelen of het apparaat in goede staat is.
2. Open de buitendoos en haal de twee vakken die overeenkomen met primaire en EBOD-behuizingen. U nu de primaire en EBOD-behuizingen uitpakken. De volgende afbeelding toont de uitgepakte weergave van een van de behuizingen.
   
    ![Uw opslagapparaat uitpakken](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Onverpakte weergave van uw opslagapparaat**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Verpakkingsdoos |
   |   2 |SAS-kabels (in accessoires en kabelslade) |
   |   3 |Bodemschuim |
   |   4 |Apparaat |
   |   5 |Top schuim |
   |   6 |Accessoiredoos |
3. Na het uitpakken van de twee dozen, zorg ervoor dat je:
   
   * 1 primaire behuizing (de primaire behuizing en EBOD-behuizing bevinden zich in twee afzonderlijke vakken)
   * 1 EBOD-behuizing
   * 4 netsnoeren, 2 in elke doos
   * 2 SAS-kabels (om de primaire behuizing aan te sluiten op de EBOD-behuizing)
   * 1 crossover Ethernet-kabel
   * 2 seriële consolekabels
   * 1 seriële USB-converter voor seriële toegang
   * 4 QSFP-naar-SFP+ adapters voor gebruik met 10 GbE netwerkinterfaces
   * 2 rack mount kits (4 zijrails met montagehardware, 2 elk voor de primaire behuizing en EBOD behuizing), 1 in elke doos
   * Documentatie aan de slag
     
     Als u geen van de bovenstaande items hebt ontvangen, neemt u [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md).  

De volgende stap is het in het rek monteren van uw apparaat.

## <a name="rack-mount-your-storsimple-8600-device"></a>Rack-mount uw StorSimple 8600 apparaat
Volg de volgende stappen om uw StorSimple 8600-opslagapparaat te installeren in een standaard 19-inch rack met voor- en achterpalen. Dit apparaat wordt geleverd met twee behuizingen: een primaire behuizing en een EBOD-behuizing. Beide moeten worden opeengemonteerd.

De installatie bestaat uit meerdere stappen, die elk in de volgende procedures worden besproken.

> [!IMPORTANT]
> StorSimple-apparaten moeten voor een goede werking worden gemonteerd.
> 
> 

### <a name="site-preparation"></a>Voorbereiding van de site
De behuizingen moeten worden geïnstalleerd in een standaard 19-inch rack dat zowel voor- als achterpalen heeft. Gebruik de volgende procedure om u voor te bereiden op de installatie van het rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>De site voorbereiden op rackinstallatie
1. Zorg ervoor dat de primaire en EBOD-behuizingen veilig rusten op een vlak, stabiel en vlak werkoppervlak (of iets dergelijks).
2. Controleer of de site waar u wilt instellen standaard wisselstroom heeft van een onafhankelijke bron of een rack Power Distribution Unit (PDU) met een niet-onderbreekbare voeding (UPS).
3. Zorg ervoor dat er één 4U -sleuf (2 X 2U) beschikbaar is op het rek waarin u de behuizingen wilt monteren.

![Waarschuwingspictogram](./media/storsimple-safety/IC740879.png)![pictogram](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) zwaar gewicht **pictogram WAARSCHUWING!**

 Zorg ervoor dat u twee personen beschikbaar hebt om het gewicht te beheren als u de installatie van het apparaat handmatig verwerkt. Een volledig geconfigureerde behuizing kan tot 32 kg wegen.

### <a name="rack-prerequisites"></a>Vereisten voor rack
De behuizingen zijn ontworpen voor installatie in een standaard 19-inch rackkast met:

* Minimale diepte van 27,84 inch van rack post tot post
* Maximaal gewicht van 32 kg voor het apparaat
* Maximale tegendruk van 5 Pascal (0,5 mm watermeter)

### <a name="rack-mounting-rail-kit"></a>Rack-montage railkit
Een set van montage rails zal worden voorzien voor gebruik met de 19-inch rack kast. De rails zijn getest om het maximale gewicht van de behuizing te verwerken. Deze rails zullen ook de installatie van meerdere behuizingen mogelijk maken zonder ruimteverlies in het rek. Installeer eerst de EBOD-behuizing.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>De EBOD-behuizing op de rails installeren
1. Voer deze stap alleen uit als er geen binnenrails op uw apparaat zijn geïnstalleerd. Typisch, de binnenste rails zijn geïnstalleerd in de fabriek. Als rails niet zijn geïnstalleerd, installeer dan de linkerrail- en rechterrailglijbanen aan de zijkanten van het chassis van de behuizing. Ze bevestigen met behulp van zes metrische schroeven aan elke kant. Om te helpen met oriëntatie, de rail dia's zijn gemarkeerd **LH - Front** en RH - **Front**, en het uiteinde dat is aangebracht aan de achterzijde van de behuizing heeft een taps toelopende einde.
   
    ![Railglijbanen aan behuizingschassis bevestigen](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Railglijbanen aan de zijkanten van de behuizing bevestigen**
   
   | Label | Beschrijving |
   | --- | --- |
   |  1 |M 3x4 knoopschroeven |
   |  2 |Chassisdia's |
2. Bevestig de linkerrail- en rechterrailsamenstellingen aan de verticale onderdelen van de rekkast. De beugels zijn gemarkeerd **LH,** **RH**, en **Deze kant omhoog** om u te begeleiden door de juiste oriëntatie.
3. Zoek de pennen van de rails aan voor- en achterzijde van de rail. Verleng de rail om tussen de rackpalen te passen en steek de pinnen in de voorste en achterste-rack post verticale lid gaten. Zorg ervoor dat de railmontage vlak is.
4. Zet de railmontage vast aan de verticale rackleden met behulp van twee van de meegeleverde metrische schroeven. Gebruik een schroef aan de voorkant en een aan de achterzijde.
5. Herhaal deze stappen voor de andere railassemblage.
   
     ![Railglijbanen aan de rekkast bevestigen](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Railassemblages aan het rek bevestigen**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Klemschroef |
   |   2 |Vierkante gat voorste rack post schroef |
   |   3 |Linker voorste spoorplaatsspelden |
   |   4 |Klemschroef |
   |   5 |Links achter spoor locatie pinnen |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montage van de EBOD-behuizing in het rek
Met behulp van de rack rails die net zijn geïnstalleerd, voert u de volgende stappen uit om de EBOD-behuizing in het rek te monteren.

#### <a name="to-mount-the-ebod-enclosure"></a>De EBOD-behuizing monteren
1. Til met een assistent de behuizing op en lijn deze uit op de rackrails.
2. Plaats de behuizing voorzichtig in de rails en duw deze vervolgens volledig in de rekkast.
   
    ![Invoegen apparaat in het rek](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montage van de behuizing in het rek**
3. Verwijder de linker- en rechterflensdoppen door de doppen vrij te trekken. De flensdoppen klikken gewoon op de flenzen.
4. Zet de behuizing in het rek door het installeren van een meegeleverde Phillips-head schroef door elke flens, links en rechts.
5. Installeer de flensdoppen door ze op hun plaats te drukken en op hun plaats te klikken.
   
     ![Flensdoppen installeren](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Het installeren van de flensdoppen**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Behuizing bevestigingsschroef |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montage van de primaire behuizing in het rek
Nadat u klaar bent met het monteren van de EBOD-behuizing, moet u de primaire behuizing na dezelfde stappen monteren.

> [!NOTE]
> * Het is mogelijk om een paar lege sleuven in het rek tussen de primaire behuizing en de EBOD behuizing.
> * Gebruik de meegeleverde 2m SAS-kabel om de primaire behuizing aan te sluiten op de EBOD-behuizing.
> * Er zijn geen beperkingen op de relatieve plaatsing van de hoofdeenheid naar de EBOD-eenheid. Daarom kan de primaire behuizing in de bovenste sleuf en de EBOD-behuizing hieronder worden geplaatst - of vice versa.
> 
> 

De volgende stap is om uw apparaat te bekabelen voor stroom-, netwerk- en seriële toegang.

## <a name="cable-your-storsimple-8600-device"></a>Uw StorSimple 8600-apparaat bekabelen
In de volgende procedures wordt uitgelegd hoe u uw StorSimple 8600-apparaat bekabelen voor stroom-, netwerk- en seriële verbindingen.

### <a name="prerequisites"></a>Vereisten
Voordat u begint met het bekabelen van uw apparaat, moet u het volgende doen:

* Uw primaire behuizing en de EBOD-behuizing, volledig uitgepakt
* 4 stroomkabels (elk 2 voor de primaire en de EBOD-behuizing) die bij uw apparaat zijn geleverd
* 2 SAS-kabels die bij het apparaat worden geleverd om de EBOD-behuizing aan te sluiten op de primaire behuizing
* Toegang tot 2 Power Distribution Units (PDU's) (aanbevolen)
* Netwerkkabels
* Meegeleverde seriële kabels
* Seriële USB-converter met het juiste stuurprogramma op uw pc geïnstalleerd (indien nodig)
* Voorzien van 4 QSFP-naar-SFP+ adapters voor gebruik met 10 GbE netwerkinterfaces
* [Ondersteunde hardware voor de 10 GbE-netwerkinterfaces op uw StorSimple-apparaat](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS- en Power bekabeling
Uw apparaat heeft zowel een primaire behuizing als een EBOD-behuizing. Dit vereist dat de units worden samengevoegd voor Serial Attached SCSI (SAS) connectiviteit en stroom.

Wanneer u dit apparaat voor de eerste keer instelt, voert u eerst de stappen uit voor SAS-bekabeling en voert u vervolgens de stappen voor de stroombekabeling uit.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Netwerkbekabeling
Uw apparaat bevindt zich in een actieve stand-byconfiguratie: op elk moment is één controllermodule actief en verwerkt alle schijf- en netwerkbewerkingen terwijl de andere controllermodule stand-by staat. Als er een controllerfout optreedt, wordt de stand-bycontroller onmiddellijk geactiveerd en wordt alle schijf- en netwerkbewerkingen voortgezet.

Als u deze redundante controllerfailover wilt ondersteunen, moet u uw apparaatnetwerk kabelen, zoals in de volgende stappen wordt weergegeven.

#### <a name="to-cable-for-network-connection"></a>Naar kabel voor netwerkverbinding
1. Uw apparaat heeft zes netwerkinterfaces op elke controller: vier 1 Gbps en twee 10 Gbps Ethernet-poorten. Raadpleeg de volgende illustratie om de gegevenspoorten op de backplane van uw apparaat te identificeren.
   
     ![Backplane van 8600 apparaat](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Achterkant van uw apparaat met de gegevenspoorten**
   
   | Label | Beschrijving |
   | --- | --- |
   |   0,1,4,5 |1 GbE-netwerkinterfaces |
   |   2,3 |10 GbE-netwerkinterfaces |
   |   6 |Seriële poorten |
2. Zie het volgende diagram voor netwerkbekabeling. (De minimale netwerkconfiguratie wordt weergegeven door effen blauwe lijnen. Voor hoge beschikbaarheid en prestaties wordt extra configuratie weergegeven door stippellijnen.)

![Kabel uw 4U-apparaat voor netwerk](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Netwerkbekabeling voor uw apparaat**

| Label | Beschrijving |
| --- | --- |
| A |LAN met internettoegang |
| B |Controller 0 |
| C |PCM 0 |
| D |Controller 1 |
| E |PCM 1 |
| F |EBOD-controller 0 |
| G |EBOD-controller 1 |
| H. |Hosts (bijvoorbeeld bestandsservers) |
| 0-5 |Netwerkinterfaces |
| 6 |Primaire behuizing |
| 7 |EBOD-behuizing |

Bij het bezetten van het apparaat vereist de minimale configuratie:

* Ten minste twee netwerkinterfaces die op elke controller zijn verbonden met één voor cloudtoegang en één voor iSCSI. De DATA 0-poort wordt automatisch ingeschakeld en geconfigureerd via de seriële console van het apparaat. Naast DATA 0 moet er ook een andere datapoort worden geconfigureerd via de klassieke Azure-portal. Sluit in dit geval DATA 0-poort aan op het primaire LAN (netwerk met internettoegang). De andere datapoorten kunnen worden aangesloten op het VLAN-segment (SAN)-segment (SAN) van het netwerk, afhankelijk van de beoogde rol.
* Identieke interfaces op elke controller die op hetzelfde netwerk is aangesloten om de beschikbaarheid te garanderen als er een mislukte controller optreedt. Als u bijvoorbeeld kiest voor het aansluiten van DATA 0 en DATA 3 voor een van de controllers, moet u de bijbehorende DATA 0 en DATA 3 aansluiten op de andere controller.

Houd rekening met hoge beschikbaarheid en prestaties:

* Configureer indien mogelijk een netwerkinterface voor cloudtoegang (1 GbE) en een ander paar voor iSCSI (10 GbE aanbevolen) op elke controller.
* Sluit waar mogelijk netwerkinterfaces van elke controller aan op twee verschillende switches om de beschikbaarheid tegen een storing van een switch te garanderen. De figuur illustreert de twee 10 GbE-netwerkinterfaces, DATA 2 en DATA 3, van elke controller die is aangesloten op twee verschillende switches. Raadpleeg voor meer informatie de **netwerkinterfaces** onder de [vereisten voor hoge beschikbaarheid voor uw StorSimple-apparaat.](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple)

> [!NOTE]
> Als u SFP+ transceivers gebruikt met uw 10 GbE-netwerkinterfaces, gebruikt u de meegeleverde QSFP-SFP+-adapters. Ga voor meer informatie naar [ondersteunde hardware voor de 10 GbE-netwerkinterfaces op uw StorSimple-apparaat.](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)
> 
> 

### <a name="serial-port-cabling"></a>Seriële bekabeling
Voer de volgende stappen uit om uw seriële poort te bekabelen.

#### <a name="to-cable-for-serial-connection"></a>Naar kabel voor seriële verbinding
1. Uw apparaat heeft een seriële poort op elke controller die wordt geïdentificeerd door een moersleutelpictogram. Als u de seriële poorten wilt vinden, raadpleegt u de afbeelding met de gegevenspoorten op de achterkant van uw apparaat.
2. Identificeer de actieve controller in de backplane van uw apparaat. Een knipperende blauwe LED geeft aan dat de controller actief is.
3. Gebruik de meegeleverde seriële kabel (indien nodig de USB-seriële converter voor uw laptop) en sluit uw console of computer (met terminalemulatie aan op het apparaat) aan op de seriële poort van de actieve controller.
4. Installeer de seriële USB-stuurprogramma's (verzonden met het apparaat) op uw computer.
5. Stel de seriële verbinding als volgt in:
   
   * 115.200 baud
   * 8 gegevensbits
   * 1 stop bit
   * Geen pariteit
   * Stroombesturingselement ingesteld op **Geen**
6. Controleer of de verbinding werkt door op Enter op de console te drukken. Er moet een seriële consolemenu worden weergegeven.

> [!NOTE]
> **Lights-Out Management:** Wanneer het apparaat is geïnstalleerd in een extern datacenter of in een computerruimte met beperkte toegang, moet u ervoor zorgen dat de seriële verbindingen met beide controllers altijd zijn verbonden met een seriële console-switch of vergelijkbare apparatuur. Dit maakt out-of-band afstandsbediening en ondersteuning operaties in geval van netwerkverstoring of onverwachte storingen.
> 
> 

U hebt de bekabeling van uw apparaat voltooid voor stroom, netwerktoegang en seriële verbinding. De volgende stap is het configureren van de software op uw apparaat.

## <a name="next-steps"></a>Volgende stappen
U bent nu klaar om [uw on-premises StorSimple-apparaat](storsimple-8000-deployment-walkthrough-u2.md)te implementeren en te configureren.

