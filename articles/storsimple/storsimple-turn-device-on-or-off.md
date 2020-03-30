---
title: Uw StorSimple 8000-serie apparaat in- of uitschakelen
description: Hiermee wordt uitgelegd hoe u een nieuw StorSimple-apparaat inschakelt, een apparaat inschakelt dat is uitgeschakeld of stroom is verloren en een lopend apparaat uitschakelt.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1319583569a8abc619ad902a87ee551b476f88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254623"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Uw StorSimple 8000-serie apparaat in- of uitschakelen

## <a name="overview"></a>Overzicht
Het afsluiten van een Microsoft Azure StorSimple-apparaat is niet vereist als onderdeel van de normale systeembewerking. Het kan echter nodig zijn om een nieuw apparaat of een apparaat in te schakelen dat moest worden uitgeschakeld. Over het algemeen is een uitschakeling vereist in gevallen waarin u defecte hardware moet vervangen, een apparaat fysiek moet verplaatsen of een apparaat buiten gebruik moet nemen. In deze zelfstudie wordt de vereiste procedure beschreven voor het inschakelen en afsluiten van uw StorSimple-apparaat in verschillende scenario's.

## <a name="turn-on-a-new-device"></a>Een nieuw apparaat inschakelen
De stappen voor het inschakelen van een StorSimple-apparaat voor de eerste keer verschillen afhankelijk van of het apparaat een 8100 of een 8600-model is. De 8100 heeft een enkele primaire behuizing, terwijl de 8600 is een dual-behuizing apparaat met een primaire behuizing en een EBOD behuizing. De gedetailleerde stappen voor beide modellen worden behandeld in de volgende secties.

* [Nieuw apparaat met alleen primaire behuizing](#new-device-with-primary-enclosure-only)
* [Nieuw apparaat met EBOD-behuizing](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nieuw apparaat met alleen primaire behuizing
De StorSimple 8100 model is een enkele behuizing apparaat. Uw apparaat bevat redundante power- en koelmodules (PCM's). Beide PCM's moeten worden geïnstalleerd en aangesloten op verschillende stroombronnen om een hoge beschikbaarheid te garanderen.

Voer de volgende stappen uit om uw apparaat te bekabelen voor stroom.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Ga voor volledige installatie- en bekabelingsinstructies naar [Uw StorSimple 8100-apparaat installeren.](storsimple-8100-hardware-installation.md) Zorg ervoor dat u de instructies precies opvolgt.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Nieuw apparaat met EBOD-behuizing
Het StorSimple 8600-model heeft zowel een primaire behuizing als een EBOD-behuizing. Dit vereist dat de units worden samengevoegd voor Serial Attached SCSI (SAS) connectiviteit en stroom.

Wanneer u dit apparaat voor de eerste keer instelt, voert u eerst de stappen uit voor SAS-bekabeling en voert u vervolgens de stappen voor de stroombekabeling uit.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Ga voor volledige installatie- en bekabelingsinstructies naar [Installeer uw StorSimple 8600-apparaat.](storsimple-8600-hardware-installation.md) Zorg ervoor dat u de instructies precies opvolgt.

## <a name="turn-on-a-device-after-shutdown"></a>Een apparaat inschakelen na het afsluiten
De stappen voor het inschakelen van een StorSimple-apparaat nadat het is uitgeschakeld, zijn verschillend, afhankelijk van of het apparaat een 8100 of een 8600-model is. De 8100 heeft een enkele primaire behuizing, terwijl de 8600 is een dual-behuizing apparaat met een primaire behuizing en een EBOD behuizing.

* [Apparaat met alleen primaire behuizing](#device-with-primary-enclosure-only)
* [Apparaat met EBOD-behuizing](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Apparaat met alleen primaire behuizing
Gebruik na het afsluiten de volgende procedure om een StorSimple-apparaat met een primaire behuizing en zonder EBOD-behuizing in te schakelen.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Een apparaat met een primaire behuizing alleen inschakelen
1. Zorg ervoor dat de stroomschakelaars op zowel power- als koelmodules (PCM's) zich in de UIT-positie bevinden. Als de schakelaars zich niet in de UIT-positie bevinden, draai ze dan naar de UIT-positie en wacht tot de lichten uitgaan.
2. Schakel het apparaat in door de aan/uit-schakelaars op beide PCM's naar de ON-positie te draaien. Het apparaat moet worden ingeschakeld.
3. Controleer het volgende om te controleren of het apparaat volledig is ingeschakeld:
   
   1. De OK LED's op beide PCM-modules zijn groen.
   2. De status LED's op beide controllers zijn effen groen.
   3. De blauwe LED op een van de controllers knippert, wat aangeeft dat de controller actief is.
      
      Als niet aan een van deze voorwaarden wordt voldaan, is uw apparaat niet gezond. Neem [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Apparaat met EBOD-behuizing
Gebruik na het afsluiten de volgende procedure om een StorSimple-apparaat met een primaire behuizing en een EBOD-behuizing in te schakelen. Voer elke stap in volgorde precies zoals beschreven. Als u dit niet doet, kan dit leiden tot gegevensverlies.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Een apparaat inschakelen met een primaire en een EBOD-behuizing
1. Zorg ervoor dat de EBOD-behuizing is aangesloten op de primaire behuizing. Zie [Uw StorSimple 8600-apparaat installeren](storsimple-8600-hardware-installation.md)voor meer informatie.
2. Zorg ervoor dat de power- en coolingmodules (PCM's) op zowel de EBOD- als de primaire behuizingen in de UIT-positie staan. Als de schakelaars zich niet in de UIT-positie bevinden, draai ze dan naar de UIT-positie en wacht tot de lichten uitgaan.
3. Schakel eerst de EBOD-behuizing in door de aan/uit-schakelaars op beide PCM's om te draaien naar de ON-positie. De PCM-LED's moeten groen zijn. Een groene EBOD controller LED op dit toestel geeft aan dat de EBOD behuizing is ingeschakeld.
4. Schakel de primaire behuizing in door de aan/uit-schakelaars op beide PCM's naar de ON-positie te draaien. Het hele systeem zou nu moeten staan.
5. Controleer of de SAS LED's groen zijn, wat ervoor zorgt dat de verbinding tussen de EBOD-behuizing en de primaire behuizing goed is.

## <a name="turn-on-a-device-after-a-power-loss"></a>Een apparaat inschakelen na een stroomuitval
Een stroomstoring of onderbreking kan een StorSimple-apparaat uitschakelen. De stroomstoring kan gebeuren op een van de voedingen of beide voedingen. De herstelstappen zijn verschillend, afhankelijk van of het apparaat een 8100 of een 8600-model is. De 8100 heeft een enkele primaire behuizing, terwijl de 8600 is een dual-behuizing apparaat met een primaire behuizing en een EBOD behuizing. In deze sectie wordt de herstelprocedure voor elk scenario beschreven.

* [Apparaat met alleen primaire behuizing](#8100)
* [Apparaat met EBOD-behuizing](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Apparaat met alleen primaire behuizing<a name="8100">
Het systeem kan zijn normale werking voortzetten als er stroomuitval is aan een van de voedingen. Echter, om een hoge beschikbaarheid van het apparaat te garanderen, herstel de stroom naar de voeding zo snel mogelijk.

Als er een stroomstoring of stroomonderbreking is op beide voedingen, wordt het systeem op een ordelijke en gecontroleerde manier afgesloten. Wanneer de stroom is hersteld, wordt het systeem automatisch ingeschakeld.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Apparaat met EBOD-behuizing<a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Stroomuitval op één voeding
Het systeem kan zijn normale werking voortzetten als er stroomuitval is van een van de voedingen op de primaire behuizing of de EBOD-behuizing. Echter, om een hoge beschikbaarheid van het apparaat te garanderen, u de stroom naar de voeding zo snel mogelijk herstellen.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Vermogensverlies op beide voedingen op primaire en EBOD-behuizingen
Als er een stroomstoring of stroomonderbreking is op beide voedingen, wordt de EBOD-behuizing onmiddellijk afgesloten en wordt de primaire behuizing op een ordelijke en gecontroleerde manier afgesloten. Wanneer de stroom is hersteld, wordt het apparaat automatisch gestart.

Als de stroom handmatig wordt uitgeschakeld, neemt u de volgende stappen om de stroom naar het systeem te herstellen.

1. Schakel de EBOD-behuizing in.
2. Nadat de EBOD-behuizing is ingeschakeld, schakelt u de primaire behuizing in.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Vermogensverlies op beide voedingen op EBOD-behuizing
Wanneer u uw kabels instelt, moet u ervoor zorgen dat de EBOD nooit alleen is aangesloten op een aparte PDU. Als de EBOD en de primaire behuizing tegelijkertijd uitvallen, herstelt het systeem.

Als alleen de EBOD-behuizing op beide voedingen uitvalt, herstelt het systeem niet automatisch. Neem de volgende stappen om het systeem in te schakelen en het in een gezonde staat te herstellen:

1. Als de primaire behuizing is ingeschakeld, schakelt u zowel stroom- als koelmodules (PCM's) uit.
2. Wacht een paar minuten tot het systeem is afgesloten.
3. Schakel de EBOD-behuizing in.
4. Nadat de EBOD-behuizing is ingeschakeld, schakelt u de primaire behuizing in.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Een apparaat inschakelen nadat de primaire en EBOD-koppeling verloren is gegaan
Als de verbinding tussen de stand-bycontroller en de bijbehorende EBOD-controller verloren gaat, blijft het apparaat werken. Als de verbinding tussen de actieve controller van het systeem en de bijbehorende EBOD-controller verloren gaat, moet er een failover plaatsvinden en moet het apparaat gewoon blijven werken.

Wanneer beide SCSI-kabels (Serial Attached) worden verwijderd of de verbinding tussen de EBOD-behuizing en de primaire behuizing wordt verbroken, werkt het apparaat niet meer. Voer op dit moment de volgende stappen uit.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Het apparaat inschakelen nadat de verbinding is verbroken
1. Toegang tot de achterkant van het apparaat.
2. Als de SAS-kabelverbinding tussen de EBOD-behuizing en de primaire behuizing kapot is, zijn alle SAS-rijstrook-LED's op de EBOD-behuizing uitgeschakeld.
3. Schakel zowel stroom- als koelmodules (PCM's) uit op de EBOD-behuizing en de primaire behuizing.
4. Wacht tot alle lichten aan de achterkant van beide behuizingen uitte gaan.
5. Plaats de SAS-kabels opnieuw en zorg ervoor dat er een goede verbinding is tussen de EBOD-behuizing en de primaire behuizing.
6. Schakel eerst de EBOD-behuizing in door beide PCM-switches naar de ON-positie te draaien.
7. Zorg ervoor dat de EBOD-behuizing is ingeschakeld door te controleren of de groene LED is ingeschakeld.
8. Schakel de primaire behuizing in.
9. Zorg ervoor dat de primaire behuizing is ingeschakeld door te controleren of de groene LED van de controller is ingeschakeld.
10. Controleer of de EBOD-behuizingsverbinding met de primaire behuizing goed is door te controleren of de SAS-rijstrook-LED's (vier per EBOD-controller) allemaal aan staan.

> [!IMPORTANT]
> Als de SAS-kabels defect zijn of de verbinding tussen de EBOD-behuizing en de primaire behuizing niet goed is, gaat het systeem in de herstelmodus. Neem [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) als dit gebeurt.


## <a name="turn-off-a-running-device"></a>Een lopend apparaat uitschakelen
Een draaiend StorSimple-apparaat moet mogelijk worden uitgeschakeld als het wordt verplaatst, buiten gebruik wordt gesteld of een defect onderdeel heeft dat moet worden vervangen. De stappen zijn verschillend, afhankelijk van of de StorSimple apparaat is een 8100 of een 8600 model. De 8100 heeft een enkele primaire behuizing, terwijl de 8600 is een dual-behuizing apparaat met een primaire behuizing en een EBOD behuizing. In deze sectie worden de stappen beschreven om een lopend apparaat af te sluiten.

* [Apparaat met primaire behuizing](#8100a)
* [Apparaat met EBOD-behuizing](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Apparaat met primaire behuizing<a name="8100a">
Als u het apparaat op een ordelijke en gecontroleerde manier wilt afsluiten, u dit doen via de Azure-portal of via de Windows PowerShell voor StorSimple. 

> [!IMPORTANT]
> Schakel een lopend apparaat niet uit met de aan/uit-knop aan de achterkant van het apparaat.
> 
> Controleer voordat u het apparaat afsluit, of alle apparaatonderdelen in orde zijn. Navigeer in de Azure-portal naar de**status Hardware**van **apparatenmonitor** > **Monitor** > en controleer of de status van alle onderdelen groen is. Dit geldt alleen voor een gezond systeem. Als het systeem wordt afgesloten om een defecte component te vervangen, ziet u een mislukte (rode) of gedegradeerde (gele) status voor het betreffende onderdeel in de **hardwarestatus**.
> 
> 

Nadat u de Windows PowerShell voor StorSimple of de Azure-portal hebt geopend, voert u de stappen uit om [een StorSimple-apparaat af te sluiten.](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Apparaat met EBOD-behuizing<a name="8600a">
> [!IMPORTANT]
> Voordat u de primaire behuizing en de EBOD-behuizing afsluit, moet u ervoor zorgen dat alle apparaatonderdelen in orde zijn. Navigeer in de Azure-portal naar de status **Van de** > **monitorhardware** > **Hardware health**van apparaten en controleer of alle onderdelen in orde zijn.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Een lopend apparaat uitschakelen met EBOD-behuizing
1. Volg alle stappen die zijn vermeld bij [het afsluiten van een StorSimple-apparaat](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) voor de primaire behuizing.
2. Nadat de primaire behuizing is uitgeschakeld, schakelt u de EBOD uit door zowel de power- als de koelmodule (PCM) uit te schakelen.
3. Controleer of het EBOD is uitgeschakeld, of alle lampjes aan de achterkant van de EBOD-behuizing zijn uitgeschakeld.

> [!NOTE]
> De SAS-kabels die worden gebruikt om de EBOD-behuizing op de primaire behuizing aan te sluiten, mogen pas worden verwijderd nadat het systeem is uitgeschakeld.

## <a name="next-steps"></a>Volgende stappen
[Neem contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) als u problemen ondervindt bij het in- of uitschakelen van een StorSimple-apparaat.

