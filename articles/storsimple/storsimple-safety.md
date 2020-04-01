---
title: Installeer en bedien het StorSimple-apparaat veilig
description: Beschrijft veiligheidsconventies, richtlijnen en overwegingen en legt uit hoe u uw StorSimple-apparaat veilig installeren en bedienen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: dae6d535-1ca2-4d2b-b221-6819043aa068
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: alkohli
ms.openlocfilehash: 480875c17d5ff5bb5c0d42d827b5477f45bf30f4
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396467"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Installeer en bedien uw StorSimple-apparaat veilig

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

![Waarschuwingspictogram](./media/storsimple-safety/IC740879.png)
![Lees veiligheidsberichtpictogram](./media/storsimple-safety/IC740885.png) LEES **VEILIGHEIDS- EN GEZONDHEIDSINFORMATIE**

Lees alle informatie over veiligheid en gezondheid in dit artikel die van toepassing is op uw Microsoft Azure StorSimple-apparaat. Houd alle gedrukte gidsen verzonden met uw StorSimple-apparaat voor toekomstige referentie. Het niet opvolgen van instructies en het goed instellen, gebruiken en verzorgen van dit product kan het risico op ernstig letsel of overlijden of schade aan het apparaat of de apparaten verhogen. Een [downloadbare versie van deze gids](https://www.microsoft.com/download/details.aspx?id=44233) is ook beschikbaar.

## <a name="safety-icon-conventions"></a>Conventies voor veiligheidspictograms
Dit zijn de pictogrammen die u zult vinden wanneer u de veiligheidsmaatregelen bekijkt die moeten worden nageleefd bij het instellen en uitvoeren van uw Microsoft Azure StorSimple-apparaat.

| Pictogram | Beschrijving |
|:--- |:--- |
| ![Gevaar](./media/storsimple-safety/IC740879.png) Icoon **GEVAAR!** |Wijst op een gevaarlijke situatie die, indien niet vermeden, zal leiden tot de dood of ernstig letsel. Dit signaal woord moet worden beperkt tot de meest extreme situaties. |
| ![Waarschuwing](./media/storsimple-safety/IC740879.png) Pictogram **WAARSCHUWING!** |Wijst op een gevaarlijke situatie die, indien niet vermeden, kan leiden tot de dood of ernstig letsel. |
| ![Waarschuwing](./media/storsimple-safety/IC740879.png) Pictogram **LET!** |Wijst op een gevaarlijke situatie die, indien niet vermeden, kan leiden tot lichte of matige verwondingen. |
| ![Aankondiging](./media/storsimple-safety/IC740881.png) Pictogram **KENNISGEVING:** |Geeft informatie aan die belangrijk wordt geacht, maar niet met gevaar. |
| ![Elektrisch schokpictogram](./media/storsimple-safety/IC740882.png) **elektrisch schokgevaar** |Hoogspanning |
| ![Zwaar gewicht](./media/storsimple-safety/IC740883.png) Icoon **zwaar gewicht** | |
| ![Geen User Serviceable](./media/storsimple-safety/IC740879.png) Parts Icon **Geen user serviceable parts** |Geen toegang, tenzij goed opgeleid. |
| ![Lees veiligheidsbericht pictogram](./media/storsimple-safety/IC740885.png)**Lees alle instructies eerst** | |
| ![Tip Hazard](./media/storsimple-safety/IC740886.png) Icon **Tip Hazard** | |

## <a name="handling-precautions"></a>Behandelingsvoorzorgsmaatregelen
![Waarschuwing](./media/storsimple-safety/IC740879.png) ![Pictogram Zwaar](./media/storsimple-safety/IC740883.png) Gewicht Icon **WAARSCHUWING!** 

Om het risico op letsel te verminderen:

* Een volledig geconfigureerde behuizing kan tot 32 kg wegen; probeer het niet zelf op te tillen.
* Zorg er voor het verplaatsen van de behuizing altijd voor dat er twee personen beschikbaar zijn om het gewicht aan te kunnen. Houd er rekening mee dat een persoon die probeert om dit gewicht te heffen kan verwondingen oplopen.
* Til de behuizing niet op aan de handgrepen van de power- en koelmodules (PCM's) aan de achterzijde van het apparaat. Deze zijn niet ontworpen om het gewicht te nemen.

## <a name="connection-precautions"></a>Verbindingsvoorzorgsmaatregelen
![Waarschuwing](./media/storsimple-safety/IC740879.png) ![Pictogram Elektrische](./media/storsimple-safety/IC740882.png) Schok Icon **WAARSCHUWING!**

Om de kans op letsel, elektrische schokken of de dood te verminderen:

* Wanneer u wordt aangedreven door meerdere AC-bronnen, koppelt u alle voeding los voor volledige isolatie.
* Trek het apparaat definitief los voordat u het verplaatst of als u denkt dat het op enigerlei wijze beschadigd is geraakt.
* Zorg voor een veilige elektrische aardeverbinding met de stroomkabels. Controleer of de aarding van de behuizing voldoet aan de nationale en lokale eisen voordat de stroom wordt toegepast.
* Zorg ervoor dat de stroomaansluiting altijd wordt losgekoppeld voordat een PCM uit de behuizing wordt verwijderd.
* Gezien het feit dat de stekker op het voedingskabel het hoofdloskoppelingsapparaat is, moet u ervoor zorgen dat de stopcontacten zich in de buurt van de apparatuur bevinden en gemakkelijk toegankelijk zijn.

![Waarschuwing](./media/storsimple-safety/IC740879.png) ![Pictogram Elektrische](./media/storsimple-safety/IC740882.png) Schok Icon **WAARSCHUWING!**

Om de kans op oververhitting of brand door de elektrische aansluitingen te verkleinen:

* Zorg voor een geschikte voedingsbron met elektrische overbelastingsbeveiliging om te voldoen aan de eisen die in de technische specificatie worden beschreven.
* Gebruik geen gespleten netsnoeren ("Y" leads).
* Om te voldoen aan de toepasselijke veiligheids-, emissie- en thermische voorschriften, mogen geen afdekkingen worden verwijderd en moeten alle baaien worden gevuld met plug-inmodules of schijfblanks.
* Zorg ervoor dat de apparatuur wordt gebruikt op een door de fabrikant gespecificeerde wijze. Indien deze apparatuur wordt gebruikt op een wijze die niet door de fabrikant is gespecificeerd, kan de door de apparatuur geboden bescherming worden aangetast.

![Aankondiging](./media/storsimple-safety/IC740881.png) Pictogram **KENNISGEVING:**

Voor de goede werking van uw apparatuur en om productschade te voorkomen:

* De RJ45-poorten aan de achterkant van het apparaat zijn alleen voor een Ethernet-verbinding. Deze mogen niet worden aangesloten op een telecommunicatienetwerk.
* Zorg ervoor dat u het apparaat installeert in een rack dat geschikt is voor een koelontwerp van voor naar achter.
* Alle plug-in modules en lege platen maken deel uit van de systeembehuizing. Deze mogen alleen worden verwijderd wanneer een vervanging onmiddellijk kan worden toegevoegd. Het systeem mag niet worden uitgevoerd zonder alle modules of spaties op zijn plaats.

## <a name="rack-system-precautions"></a>Voorzorgsmaatregelen van het racksysteem
Bij het monteren van het apparaat in een rekkast moet rekening worden gehouden met de volgende veiligheidseisen.

![Waarschuwing](./media/storsimple-safety/IC740879.png) ![Pictogram Tip](./media/storsimple-safety/IC740886.png) Gevaar Pictogram **WAARSCHUWING WAARSCHUWING!**

Om de kans op letsel door een kantelen te verminderen:

* Het ontwerp van het rek moet het totale gewicht van de geïnstalleerde behuizingen ondersteunen en moet voorzien zijn van stabiliserende functies die geschikt zijn om te voorkomen dat het rek kantelt of overdeint tijdens de installatie of normaal gebruik.
* Vul bij het laden van een rek het rek van ondernaar boven en leeg vanaf de bovenkant naar beneden.
* Schuif niet meer dan één behuizing tegelijk uit het rek om het gevaar te vermijden dat het rek omvalt.

![Waarschuwing](./media/storsimple-safety/IC740879.png) ![Pictogram Elektrische](./media/storsimple-safety/IC740882.png) Schok Icon **WAARSCHUWING!**

Om de kans op letsel, elektrische schokken of de dood te verminderen:

* Het rek moet een veilig elektrisch distributiesysteem hebben. Het moet overstroombescherming bieden voor de behuizing en mag niet worden overbelast door het totale aantal geïnstalleerde behuizingen. De op het naamplaatje weergegeven elektrische stroomverbruik moet in acht worden genomen.
* Het elektrische distributiesysteem moet voor elke behuizing in het rek een betrouwbare ondergrond bieden.
* Bij het ontwerp van het elektrische distributiesysteem moet rekening worden gehouden met de totale grondlekkagestroom van alle voedingen in alle behuizingen. Houd er rekening mee dat elke voeding in elke behuizing een grondlekstroom heeft van maximaal 1,0 mA bij 60 Hz, 264 volt. Het rek kan etikettering met "HOGE LEKKAGESTROOM vereisen. Grond (aarde) verbinding is essentieel voor het aansluiten van een levering."
* Het rek moet, wanneer het is geconfigureerd met de behuizingen, voldoen aan de veiligheidseisen van UL 60950-1 en IEC 60950-1/EN 60950-1.

![Aankondiging](./media/storsimple-safety/IC740881.png) Pictogram **KENNISGEVING:**

Voor de juiste koeling van uw racksysteem:

* Zorg ervoor dat het ontwerp van het rek rekening houdt met de maximale omgevingstemperatuur van 35 graden Celsius (95 graden Fahrenheit).
* Het systeem wordt bediend met lagedruk- en uitlaatinstallatie (tegendruk die wordt veroorzaakt door rekdeuren en obstakels die niet hoger zijn dan 5 Pascal [0,5 mm watermeter]).

## <a name="power-cooling-module-pcm-precautions"></a>Energiebesparende energiebesparende maatregelen
Het apparaat is ontworpen om te werken met twee PCM's. Elk van de PCM's heeft een voeding en een dual-axis ventilator. Tijdens een kritieke toestand zorgt het systeem voor een storing van één voeding terwijl de normale werking wordt voortgezet. Er moeten altijd twee PCM's (en dus voedingen) worden geïnstalleerd. Een enkele PCM biedt geen redundante stroom. Daarom kan het uitvallen van zelfs één PCM leiden tot downtime of mogelijk gegevensverlies.

![Waarschuwing](./media/storsimple-safety/IC740879.png) ![Pictogram Elektrische](./media/storsimple-safety/IC740882.png) Schok Icon **WAARSCHUWING!**

Om de kans op letsel, elektrische schokken of de dood te verminderen:

* Verwijder de deksels niet uit het PCM. Er bestaat gevaar voor een elektrische schok binnenin. Neem [contact op met Microsoft Support om](storsimple-contact-microsoft-support.md)de PCM terug te sturen en een vervanging te verkrijgen.

![Aankondiging](./media/storsimple-safety/IC740881.png) Pictogram **KENNISGEVING:**

Voor de goede werking van uw apparatuur en om productschade te voorkomen:

* U moet de mislukte PCM binnen 24 uur vervangen. Nadat een PCM is verwijderd voor vervanging, moet de vervanging binnen 10 minuten na verwijdering zijn voltooid.
* Verwijder een PCM niet tenzij een vervanging onmiddellijk kan worden geïnstalleerd. De behuizing mag niet zonder alle modules worden bediend.

## <a name="electrostatic-discharge-esd-precautions"></a>Voorzorgsmaatregelen voor elektrostatische ontlading (ESD)
![Aankondiging](./media/storsimple-safety/IC740881.png) Pictogram **KENNISGEVING:**

Houd rekening met de volgende ESD-gerelateerde voorzorgsmaatregelen.

* Zorg ervoor dat u een geschikte antistatische pols- of enkelband hebt geïnstalleerd en gecontroleerd.
* Let op alle conventionele ESD-voorzorgsmaatregelen bij het hanteren van modules en componenten.
* Vermijd contact met backplane componenten en module connectoren.
* ESD schade valt niet onder de garantie.

## <a name="battery-disposal-precautions"></a>Voorzorgsmaatregelen voor het verwijderen van batterijen
De voeding maakt gebruik van een speciale batterij om de inhoud van het geheugen te beschermen tijdens tijdelijke, korte termijn stroomuitval. De batterij zit in het PCM. Houd rekening met de volgende informatie over de batterij.

![Waarschuwing](./media/storsimple-safety/IC740879.png) Pictogram **WAARSCHUWING!**

Om het risico op korte broek, brand, explosie, letsel of de dood te verminderen:

* Gooi gebruikte batterijen weg in overeenstemming met de nationale/regionale regelgeving.
* Niet demonteren, verpletteren, of warmte boven 60 graden Celsius (140 graden Fahrenheit) of verbranden. Vervang de PCM-batterij alleen door een meegeleverde batterij. Het gebruik van een andere batterij kan brand- of explosiegevaar opleveren.
* Gebruik beschermende einddoppen op de batterijen als deze uit de voeding worden verwijderd.

![Aankondiging](./media/storsimple-safety/IC740881.png) Pictogram **KENNISGEVING:**

Volg bij het verzenden of anderszins vervoeren van de batterijen door de lucht het IATA Lithium Battery Guidance document beschikbaar op[https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Nadat u deze veiligheidsberichten hebt bekeken, zijn de volgende stappen het uitpakken, rekken en bekabelen van uw apparaat.

## <a name="next-steps"></a>Volgende stappen
* Ga voor een apparaat met 8100 naar [Uw StorSimple 8100-apparaat installeren.](storsimple-8100-hardware-installation.md)
* Ga voor een apparaat met 8600 naar [Uw StorSimple 8600-apparaat installeren.](storsimple-8600-hardware-installation.md)

