---
title: Microsoft Azure StorSimple 8100-apparaat installeren
description: Beschrijft hoe u uw StorSimple 8100-apparaat uitpakken, rackmonteren en bekabelen voordat u de software implementeert en configureert.
author: alkohli
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 113b72ddf7e5d508c8a0b577d4004d4fbd83e8e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267584"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Pak uw StorSimple 8100-apparaat uit, reken en kabel

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht
Uw Microsoft Azure StorSimple 8100 is een apparaat met rack. In deze zelfstudie wordt uitgelegd hoe u de storSimple 8100-apparaathardware uitpakken, rackmonteren en bekabelen voordat u het StorSimple-apparaat configureert en implementeert.

## <a name="unpack-your-storsimple-8100-device"></a>Pak uw StorSimple 8100-apparaat uit
De volgende stappen geven duidelijke, gedetailleerde instructies over het uitpakken van uw StorSimple 8100-opslagapparaat. Dit apparaat wordt verzonden in één doos.

### <a name="prepare-to-unpack-your-device"></a>Bereid je voor om je apparaat uit te pakken
Voordat u uw apparaat uitpakt, controleert u de volgende informatie.

![Waarschuwingspictogram](./media/storsimple-safety/IC740879.png)![pictogram](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) zwaar gewicht **pictogram WAARSCHUWING!**

1. Zorg ervoor dat u twee personen beschikbaar hebt om het gewicht van de behuizing te beheren als u deze handmatig verwerkt. Een volledig geconfigureerde behuizing kan tot 32 kg wegen.
2. Leg de doos op een vlak, horizontaal oppervlak.

Voer vervolgens de volgende stappen uit om uw apparaat uit te pakken.

#### <a name="to-unpack-your-device"></a>Uw apparaat uitpakken
1. Controleer de doos en het verpakkingsmateriaal op deuken, scheuren, waterschade of andere duidelijk zichtbare schade. Als de doos of het verpakkingsmateriaal ernstig beschadigd is, moet u de doos niet openen. Neem [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) om te beoordelen of het apparaat in goede staat is.
2. Pak de doos uit. De volgende afbeelding toont de uitgepakte weergave van uw StorSimple-apparaat.
   
     ![Uw opslagapparaat uitpakken](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Onverpakte weergave van uw opslagapparaat**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Verpakkingsdoos |
   |   2 |Bodemschuim |
   |   3 |Apparaat |
   |   4 |Top schuim |
   |   5 |Accessoiredoos |
3. Controleer of de inhoud van de doos bestaat uit de volgende items:
   
   * 1 enkele behuizing
   * 2 stroomkabels
   * 1 crossover Ethernet-kabel
   * 2 seriële consolekabels
   * 1 seriële USB-converter voor seriële toegang
   * 1 tamper-proof T10 schroevendraaier
   * 4 QSFP-naar-SFP+ adapters voor gebruik met 10 GbE netwerkinterfaces
   * 1 rack-mount kit (2 zijrails met montagehardware)
   * Documentatie aan de slag
     
     Als u geen van de bovenstaande items hebt ontvangen, neemt u [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md).

De volgende stap is het in het rek monteren van uw apparaat.

## <a name="rack-mount-your-storsimple-8100-device"></a>Rack-mount uw StorSimple 8100 apparaat
Volg de volgende stappen om uw StorSimple 8100-opslagapparaat te installeren in een standaard 19-inch rack met voor- en achterpalen. Het StorSimple 8100-apparaat heeft één primaire behuizing.

De installatie bestaat uit meerdere stappen, die elk in de volgende procedures worden besproken.

> [!IMPORTANT]
> StorSimple-apparaten moeten voor een goede werking worden gemonteerd.
> 
> 

### <a name="prepare-the-site"></a>De site voorbereiden
Het apparaat moet worden geïnstalleerd in een standaard 19-inch rack dat zowel voor- als achterpalen heeft. Gebruik de volgende procedure om u voor te bereiden op de installatie van het rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>De site voorbereiden op rackinstallatie
1. Het apparaat staat veilig op een vlak, stabiel en horizontaal werkoppervlak (of vergelijkbaar).
2. Controleer of de site waar u wilt instellen standaard wisselstroom heeft van een onafhankelijke bron of een rackpowerdistribution unit (PDU) met een niet-onderbreekbare voeding (UPS).
3. Zorg ervoor dat er één 2U-sleuf beschikbaar is op het rek waarin u het apparaat wilt monteren.

![Waarschuwingspictogram](./media/storsimple-safety/IC740879.png)![pictogram](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) zwaar gewicht **pictogram WAARSCHUWING!**

Zorg ervoor dat u twee personen beschikbaar hebt om het gewicht te beheren als u de installatie van het apparaat handmatig verwerkt. Een volledig geconfigureerde behuizing kan tot 32 kg wegen.

### <a name="rack-prerequisites"></a>Vereisten voor rack
De 8100 behuizing is ontworpen voor installatie in een standaard 19-inch rackkast met:

* Minimale diepte van 27,84 inch van rack post tot post.
* Maximaal gewicht van 32 kg voor het apparaat
* Maximale tegendruk van 5 Pascal (0,5 mm watermeter).

### <a name="rack-mounting-rail-kit"></a>Rack-montage railkit
Een set van montage rails is voorzien voor gebruik met de 19-inch rack kast. De rails zijn getest om het maximale gewicht van de behuizing te verwerken. Deze rails zullen ook de installatie van meerdere behuizingen mogelijk maken zonder ruimteverlies in het rek.

#### <a name="to-install-the-device-on-the-rails"></a>Het apparaat op de rails installeren
1. Voer deze stap alleen uit als er geen binnenrails op uw apparaat zijn geïnstalleerd. Typisch, de binnenste rails zijn geïnstalleerd in de fabriek. Als rails niet zijn geïnstalleerd, installeer dan de linkerrail- en rechterrailglijbanen aan de zijkanten van het chassis van de behuizing. Ze bevestigen met behulp van zes metrische schroeven aan elke kant. Om te helpen met oriëntatie, de rail dia's zijn gemarkeerd **LH - Front** en RH - **Front**, en het uiteinde dat is aangebracht aan de achterzijde van de behuizing heeft een taps toelopende einde.<br/>
   
    ![Railglijbanen aan behuizingschassis bevestigen](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Het bevestigen van binnenrailglijbanen aan de zijkanten van de behuizing**
   
    Label | Beschrijving
    ----- | -----------
    1     | M 3x4 knoopschroeven
    2     | Chassisdia's

2. Bevestig de buitenste linkerrail en buitenste rechter railsamenstellingen aan de verticale onderdelen van de rekkast. De beugels zijn gemarkeerd **LH,** **RH**, en **Deze kant omhoog** om u te begeleiden door de juiste oriëntatie.
3. Zoek de pennen van de rails aan voor- en achterzijde van de rail. Verleng de rail om tussen de rackpalen te passen en steek de pinnen in de voorste en achterste rack post verticale lid gaten. Zorg ervoor dat de railmontage vlak is.
4. Gebruik twee van de meegeleverde metrische schroeven om de railmontage aan de verticale leden van het rek te bevestigen. Gebruik een schroef aan de voorkant en een aan de achterzijde.
5. Herhaal deze stappen voor de andere railassemblage.<br/>
   
     ![Railglijbanen aan de rekkast bevestigen](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Het bevestigen van buitenste railassemblages aan het rek**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Klemschroef |
   |   2 |Vierkante gat voorste rack post schroef |
   |   3 |Linker spoorvoorplaatsspelden |
   |   4 |Klemschroef |
   |   5 |Linker spoor achter plaatsspelden |

### <a name="mounting-the-device-in-the-rack"></a>Montage van het apparaat in het rek
Met behulp van de rack rails die net zijn geïnstalleerd, voert u de volgende stappen uit om het apparaat in het rek te monteren.

#### <a name="to-mount-the-device"></a>Het apparaat monteren
1. Til met een assistent de behuizing op en lijn deze uit op de rackrails.
2. Plaats het apparaat voorzichtig in de rails en duw het apparaat vervolgens volledig in de rekkast.<br/>
   
    ![Invoegen apparaat in het rek](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montage van het apparaat in het rek**
3. Verwijder de linker- en rechterflensdoppen door de doppen vrij te trekken. De flensdoppen klikken gewoon op de flenzen.
4. Zet de behuizing in het rek door het installeren van een meegeleverde Phillips-head schroef door elke flens, links en rechts.
5. Installeer de flensdoppen door ze op hun plaats te drukken en op hun plaats te klikken.<br/>
   
     ![Flensdoppen installeren](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Het installeren van de flensdoppen**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Behuizing bevestigingsschroef |

De volgende stap is om uw apparaat te bekabelen voor stroom-, netwerk- en seriële toegang.

## <a name="cable-your-storsimple-8100-device"></a>Uw StorSimple 8100-apparaat bekabelen
In de volgende procedures wordt uitgelegd hoe u uw StorSimple 8100-apparaat bekabelen voor stroom-, netwerk- en seriële verbindingen.

### <a name="prerequisites"></a>Vereisten
Voordat u begint met de bekabeling van uw apparaat hebt u het volgende nodig:

* Uw opslagapparaat, volledig uitgepakt en rack gemonteerd.
* 2 stroomkabels die bij uw apparaat zijn geleverd
* Toegang tot 2 Power Distribution Units (aanbevolen).
* Netwerkkabels
* Meegeleverde seriële kabels
* Seriële USB-converter met het juiste stuurprogramma geïnstalleerd op uw pc (indien nodig)
* Voorzien van 4 QSFP-naar-SFP+ adapters voor gebruik met 10 GbE netwerkinterfaces
* [Ondersteunde hardware voor de 10 GbE-netwerkinterfaces op uw StorSimple-apparaat](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Bekabeling
Uw apparaat bevat redundante power- en koelmodules (PCM's). Beide PCM's moeten worden geïnstalleerd en aangesloten op verschillende stroombronnen om een hoge beschikbaarheid te garanderen.

Voer de volgende stappen uit om uw apparaat te bekabelen voor stroom.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Netwerkbekabeling
Uw apparaat is een active-stand-by configuratie: op elk moment is één controllermodule actief en verwerkt alle schijf- en netwerkbewerkingen terwijl de andere controllermodule stand-by staat. Als een controller uitvalt, wordt de stand-by controller onmiddellijk geactiveerd en wordt alle schijf- en netwerkbewerkingen voortgezet.

Als u deze redundante controllerfailover wilt ondersteunen, moet u uw apparaatnetwerk bekabelen zoals beschreven in de volgende stappen.

#### <a name="to-cable-for-network-connection"></a>Naar kabel voor netwerkverbinding
1. Uw apparaat heeft zes netwerkinterfaces op elke controller: vier 1 Gbps en twee 10 Gbps Ethernet-poorten. Identificeer de verschillende gegevenspoorten op de backplane van uw apparaat.
   
    ![Backplane van 8100-apparaat](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Achterkant van het apparaat met gegevenspoorten**
   
   | Label | Beschrijving |
   | --- | --- |
   |   0,1,4,5 |1 GbE-netwerkinterfaces |
   |   2,3 |10 GbE-netwerkinterfaces |
   |   6 |Seriële poorten |
2. Zie het volgende diagram voor netwerkbekabeling. (De minimale netwerkconfiguratie wordt weergegeven door effen blauwe lijnen. Extra configuratie vereist voor hoge beschikbaarheid en prestaties wordt weergegeven door stippellijnen.)

    ![Kabel uw 2U-apparaat voor netwerk](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Netwerkbekabeling voor uw apparaat**

   |Label | Beschrijving |
   |----- | ----------- |
   | A    | LAN met internettoegang |
   | B    | Controller 0 |
   | C    | PCM 0 |
   | D    | Controller 1 |
   | E    | PCM 1 |
   | F, G | Hosts |
   | 0-5  | Netwerkinterfaces |



Bij het bezetten van het apparaat vereist de minimale configuratie:

* Ten minste twee netwerkinterfaces die op elke controller zijn verbonden met één voor cloudtoegang en één voor iSCSI. De DATA 0-poort wordt automatisch ingeschakeld en geconfigureerd via de seriële console van het apparaat. Naast DATA 0 moet er ook een andere datapoort worden geconfigureerd via de klassieke Azure-portal. Sluit in dit geval DATA 0-poort aan op het primaire LAN (netwerk met internettoegang). De andere datapoorten kunnen worden aangesloten op het VLAN-segment (SAN)-segment (SAN) van het netwerk, afhankelijk van de beoogde rol.
* Identieke interfaces op elke controller die op hetzelfde netwerk is aangesloten om de beschikbaarheid te garanderen als er een mislukte controller optreedt. Als u bijvoorbeeld kiest voor het aansluiten van DATA 0 en DATA 3 voor een van de controllers, moet u de bijbehorende DATA 0 en DATA 3 aansluiten op de andere controller.

Houd rekening met hoge beschikbaarheid en prestaties:

* Configureer indien mogelijk een netwerkinterface voor cloudtoegang (1 GbE) en een ander paar voor iSCSI (10 GbE aanbevolen) op elke controller.
* Sluit waar mogelijk netwerkinterfaces van elke controller aan op twee verschillende switches om de beschikbaarheid tegen een storing van een switch te garanderen. De figuur illustreert de twee 10 GbE-netwerkinterfaces, DATA 2 en DATA 3, van elke controller die is aangesloten op twee verschillende switches.

Raadpleeg voor meer informatie de **netwerkinterfaces** onder de [vereisten voor hoge beschikbaarheid voor uw StorSimple-apparaat.](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple)

> [!NOTE]
> Als u SFP+ transceivers gebruikt met uw 10 GbE-netwerkinterfaces, gebruikt u de meegeleverde QSFP-SFP+ adapters. Ga voor meer informatie naar [ondersteunde hardware voor de 10 GbE-netwerkinterfaces op uw StorSimple-apparaat.](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)
> 
> 

### <a name="serial-port-cabling"></a>Seriële bekabeling
Voer de volgende stappen uit om uw seriële poort te bekabelen.

#### <a name="to-cable-for-serial-connection"></a>Naar kabel voor seriële verbinding
1. Uw apparaat heeft een seriële poort op elke controller die wordt geïdentificeerd door een moersleutelpictogram. Raadpleeg de afbeelding in het gedeelte [Netwerkbekabeling](#network-cabling) om de seriële poorten op de backplane van uw apparaat te vinden.
2. Identificeer de actieve controller in de backplane van uw apparaat. Een knipperende blauwe LED geeft aan dat de controller actief is.
3. Gebruik de meegeleverde seriële kabels (indien nodig de USB-seriële converter voor uw laptop) en sluit uw console of computer (met terminalemulatie aan op het apparaat) aan op de seriële poort van de actieve controller.
4. Installeer de seriële USB-stuurprogramma's (verzonden met het apparaat) op uw computer.
5. Stel de seriële verbinding als volgt in: 115.200 baud, 8 databits, 1 stop bit, geen pariteit en stroomregeling ingesteld op Geen.
6. Controleer of de verbinding werkt door op Enter op de console te drukken. Er moet een seriële consolemenu worden weergegeven.

> [!NOTE]
> **Lights-Out Management:** Wanneer het apparaat is geïnstalleerd in een extern datacenter of in een computerruimte met beperkte toegang, moet u ervoor zorgen dat de seriële verbindingen met beide controllers altijd zijn verbonden met een seriële console-schakelaar of soortgelijke apparatuur. Dit maakt out-of-band afstandsbediening en ondersteuning operaties mogelijk als er netwerkonderbrekingen of onverwachte storingen zijn.
> 
> 

Uw apparaat is nu bekabeld voor stroom, netwerktoegang en seriële connectiviteit. De volgende stap is het configureren van de software en het implementeren van uw apparaat.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren en configureren van uw on-premises StorSimple-apparaat.](storsimple-8000-deployment-walkthrough-u2.md)

