---
title: 'Zelf studie: een fysiek Azure FXT edge-apparaat installeren'
description: Het uitpakken, het rek en de kabel van het fysieke apparaat onderdeel van de Microsoft Azure FXT Edge filer hybride opslag cache
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7c77e25d0dcabc49f2e6672645c6bc41e8662ec8
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551026"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Zelf studie: Azure FXT Edge-Bestandsr installeren 

In deze zelf studie wordt beschreven hoe u een hardware-knoop punt installeert voor de Azure FXT Edge-bestands cache hybride opslag. U moet ten minste drie hardwareconfiguraties installeren om een Azure FXT Edge-bestands cluster te maken.

Bij de installatie procedure wordt het apparaat uitgepakt en gerackd en wordt het CMA (kabel management arm) en de voor omlijsting vooraan bevestigd. In een afzonderlijke zelf studie wordt uitgelegd hoe netwerk kabels worden gekoppeld en de stroom wordt verbonden. 

Het duurt ongeveer een uur voordat een Azure FXT Edge-knoop punt is geïnstalleerd. 

Deze zelf studie bevat de volgende installatie stappen: 

> [!div class="checklist"]
> * Het apparaat uitpakken
> * Het apparaat in een rek koppelen
> * De omlijsting vooraan installeren (optioneel)

## <a name="installation-prerequisites"></a>Installatievereisten 

Voordat u begint, moet u ervoor zorgen dat het Data Center en het rek dat u wilt gebruiken, over de volgende functies beschikken:

* Een beschik bare 1U-sleuf in het rek waar u het apparaat wilt koppelen.
* Netvoeding en koel systemen die voldoen aan de behoeften van de Azure FXT Edge-Bestandsr. (Lees [voedings-en thermische specificaties](fxt-specs.md#power-and-thermal-specifications) voor hulp bij het plannen en aanpassen van de grootte van de installatie.)  

  > [!NOTE] 
  > Als u optimaal gebruik wilt maken van de twee redundante voedings eenheden (PSUs), gebruikt u energie distributie-eenheden op twee verschillende vertakkings circuits bij het koppelen van netstroom. Lees de [voedings kabels](fxt-network-power.md#connect-power-cables) voor meer informatie.  

## <a name="unpack-the-hardware-node"></a>Het hardware-knoop punt uitpakken 

Elk Azure FXT Edge-Bestandsr knoop punt wordt in één doos verzonden. Voltooi deze stappen om een apparaat uit te pakken.

1. Leg de doos op een vlak, horizontaal oppervlak.

2. Controleer de doos en het verpakkingsmateriaal op deuken, scheuren, waterschade of andere duidelijk zichtbare schade. Als de doos of het verpakkingsmateriaal ernstig beschadigd is, moet u de doos niet openen. Neem contact op met Microsoft Support om in overleg te bepalen of het apparaat nog in goede staat zal zijn.

3. Pak de doos uit. Zorg ervoor dat deze de volgende items bevat:
   * Eén FXT-apparaat met één behuizing
   * Twee netsnoeren
   * Eén omlijstend en sleutel
   * Eén Rail Kit-assembly
   * Een arm voor kabel beheer (CMA)
   * Boekje met CMA-installatie-instructies
   * Brochure installatie-instructies voor rack
   * Het boekje met veiligheid, milieu en reglementaire informatie

Als u niet alle items in de lijst hebt ontvangen, neemt u contact op met de leverancier van het apparaat voor ondersteuning. 

Zorg ervoor dat het apparaat voldoende tijd heeft om dezelfde Tempe ratuur te bereiken als de ruimte voordat u deze installeert of uitschakelt. Als u op een wille keurig deel van het apparaat condensatie ziet, wacht u ten minste 24 uur voordat u de installatie uitvoert.

De volgende stap is het in het rek monteren van uw apparaat.

## <a name="rack-the-device"></a>Het apparaat in het rek monteren

Het Azure FXT edge-apparaat moet zijn geïnstalleerd in een standaard 19-inch rack. 

De Azure FXT Edge filer hybride opslag cache bestaat uit drie of meer Azure FXT Edge-bestands apparaten. Herhaal de stappen voor de installatie van het rek voor elk apparaat dat deel uitmaakt van uw systeem. 

### <a name="rack-install-prerequisites"></a>Vereisten voor rack installatie

* Lees voordat u begint de veiligheids instructies in het boekje beveiliging, milieu en reglementaire informatie dat is verzonden met uw apparaat.

  > [!NOTE]
  > Gebruik altijd twee personen bij het opheffen van het knoop punt, ook wanneer u het in een rek installeert of uit het rek verwijdert. 

* Bepaal het type spoorweg installatie dat wordt gebruikt met uw materieel rek. 
  * Volg de instructies voor het gebruik van vier Kante en afgeronde weg voor racks.
  * Volg de instructies in het onderhouds spoorwegnet voor threaded hole-racks. 
  
    Voor de configuratie van het hulp programma voor spoorstaaf montage moet u acht schroeven opgeven, typt u 10-32, 12-24, M5 of M6. De hoofd diameter van de schroeven moet kleiner zijn dan 10 mm (0,4).

### <a name="identify-the-rail-kit-contents"></a>De inhoud van de Rail Kit identificeren

Zoek de onderdelen voor het installeren van de Rail Kit-assembly:

1. Twee A7 Dell ReadyRails II-Rapid-rail-onderdelen (1)
1. Twee Hook-en loop riemen (2)

![Genummerde tekening van de inhoud van de Rail Kit](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Rails (vier Kante rails) met een rek

Voor racks met snap-in Square of ronde gaten, volgt u deze procedure om de rails samen te stellen en te installeren. 

1. Plaats het linker-en rechter uiteinde van de eind stukken met het label aan de **voor grond** . Plaats elk eind stuk zodanig dat het stoelen in de gaten aan de voor zijde van de verticale rack flenss. (1)

2. U kunt elk eind stuk in de onderste en bovenste gaten van het rek uitlijnen in de ruimte die u wilt koppelen. (2)

3. Zet de back-end van de Rail totdat deze volledig is gekoppeld aan de verticale rack flens en klik op de vergren deling. Herhaal deze stappen om het front-end-gedeelte op de verticale rack flens te plaatsen en positioneren. (3)

> [!TIP]
> Als u de rails wilt verwijderen, geeft u de knop voor de vergren deling op het eind punt van het gedeelte (4) en de afzonderlijke rails ongedaan.

![Diagram van het installeren en verwijderen van hulp programma-minder rails, met genummerde stappen](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Rails voor trein apparatuur (gethreadd hole-racks)

Voor racks met geschroefde gaten moet u deze procedure volgen om de rails samen te voegen en te installeren.

1. Verwijder de pincodes van de voor-en hand haakjes met een vlakke draaier. (1)
1. Haal de subassemblages van de Rail hendel op en Roteer ze om ze te verwijderen van de beugels. (2)
1. Koppel de linker-en rechter-koppel rails aan de voor zijde verticale rack flenss met behulp van twee combi Naties van schroeven. (3)
1. Schuif de linker-en rechter kant omhoog en klik op de pijl naar rechts op de verticale rack flens en voeg deze toe met behulp van twee combi Naties van schroeven. (4)

![Diagram van het installeren en verwijderen van rails met hulp middelen, met genummerde stappen](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Het systeem in het rek installeren

Volg deze stappen om het Azure FXT edge-apparaat te koppelen aan het rek.

1. Haal de binnenste schuif rails uit het rek totdat ze zijn vergrendeld. (1)
1. Zoek de achterstandoff aan de achterzijde van het apparaat en verlaag deze naar de eerste J-sleuven op de dia-verzamelingen. (2) 
1. Het apparaat omlaag draaien totdat alle Rail standoffs in de J-sleuven zijn geplaatst. (3)
1. Push het apparaat naar binnen totdat de vergren delingen worden geplaatst.
1. Druk op de vergrendelings knoppen van de schuif regelaar op beide rails (4) en schuif het apparaat naar het rek.

![Installeer het systeem in een rekdiagram, waarbij de stappen zijn genummerd](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Het systeem uit het rek verwijderen

Als u het apparaat uit het rek wilt verwijderen, volgt u deze procedure. 

1. Zoek de Lock-hendels aan de zijkanten van de interne rails (1).
2. Ontgrendel elke hendel door deze naar de release positie te verplaatsen (2).
3. Begrijpt de zijkanten van het systeem stevig af en haal het door, totdat de Rail standoffs vooraan in de J-sleuven staan. Til het systeem omhoog en weg van het rek en plaats het op een niveau vlak (3).

![Afbeelding van het verwijderen van een systeem uit het rek, waarbij de stappen zijn genummerd](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>De Slam-hendel benaderen

1. Zoek de Slam-hendel (1) aan beide zijden van het systeem.
2. De vergren delingen worden automatisch ingeschakeld wanneer het systeem naar het rek wordt gepusht. 

Als u de hendels wilt vrijgeven bij het verwijderen van het systeem, haalt u deze op (2).

Er zijn optionele schroeven voor hard koppelen ter beveiliging van het systeem naar het rek voor verzen ding of in andere instabiele omgevingen. Zoek de schroef onder elke hendel en Verschuif ze met een #2 Phillips draaier (3).

![Genummerde afbeelding van het aan-en uitbrengen van de Slam-vergren deling](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>De arm voor kabel beheer installeren 

Er wordt een optionele arm voor kabel beheer (CMA) met de FXT-Edge-bestand meegeleverd. Gedrukte instructies voor de installatie van deze zijn opgenomen in het pakket. 

1. De onderdelen van de arm-Kit voor kabel beheer uitpakken en identificeren:
   * CMA-lade (1)
   * CMA (2)
   * Vastlopen van nylon kabel (3)
   * CMA-bijlage haken (4)
   * Status indicator kabel (5) 

   > [!TIP] 
   > Als u de CMA voor verzen ding in het rek wilt beveiligen, moet u de binds door lopen rond manden en tray en deze op de moeiteloos Fiat zetten. Als u de CMA op deze manier beveiligt, wordt uw systeem ook in instabiele omgevingen beveiligd.

   ![Afbeelding van CMA-onderdelen](media/fxt-install/cma-kit-400.png)

2. Installeer de CMA-lade.

   De CMA-lade biedt ondersteuning en fungeert als Behouder voor de CMA. 

   1. Lijn de zijkanten van de lade af en deel ze aan de rand van de rails. 
   1. De lade voorwaarts pushen tot deze op plaats klikt. (1)
   1. Als u de lade wilt verwijderen, maakt u de vergrendelings knoppen in het midden en haalt u de lade op van de haakjes van de ontvanger (2).

   ![Afbeelding van de installatie van CMA tray](media/fxt-install/cma-tray-install-400.png)

3. Installeer de CMA-bijlage haken. 

   > [!NOTE]
   >
   > * U kunt de CMA koppelen aan de linker-of linkse montagekit, afhankelijk van hoe u kabels van het systeem wilt routeren. 
   > * Voor het gemak koppelt u de CMA aan de zijde tegenover de voeding (Side A). Als deze is gekoppeld aan zijde B, moet de CMA worden losgekoppeld om de buitenste voeding te kunnen verwijderen. 
   > * Verwijder de lade altijd voordat u de Power voorraden verwijdert. 

   ![Afbeelding van de installatie van CMA beugel](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Selecteer de juiste CMA voor de kant waar u de CMA (zijde B of side A) wilt koppelen.
   1. Installeer de CMA-bijlage beugel met de overeenkomstige kant A-of Side B-markering aan de achtergrond van de diapresentatie.
   1. Lijn de gaten op het haakje uit met de pincodes op de schuif Rail. Duw het haakje omlaag totdat het is vergrendeld. 

4. Installeer de CMA.

   1. Op de achterkant van het systeem past u de vergren deling aan de front-end van de CMA op de binnenste haak van de dia-assembly toe tot de vergren deling (1). 
   1. Plaats de andere hendel aan het einde van de buitenste beugel tot de vergren deling (2). 
   1. Als u de CMA wilt verwijderen, moet u beide vergren delingen opheffen door op de knop met de CMA loslaten te drukken boven aan de binnenkant van de binnenste en buitenste hendels (3).

   ![Afbeelding van de installatie van de hoofd-CMA](media/fxt-install/cma-install-400.png)

   De CMA kan worden geroteerd van het systeem voor toegang en service. Laat het CMA van het scharnieren aan het einde van de lade weg (1). Nadat het is verwijderd uit de lade, wordt de CMA van het systeem gedraaid (2).

   ![Afbeelding van CMA geroteerd open voor service](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>De omlijsting vooraan installeren (optioneel)

In deze sectie wordt uitgelegd hoe u de voor omlijsting (faceplate) voor de FXT edge-hardware van Azure kunt installeren en verwijderen. 

De omlijsting vooraan installeren: 

1. Zoek en verwijder de omlijstte sleutel, die wordt vermeld in het omlijstings pakket. 
1. Lijn de omlijst met de voor zijde van het chassis uit en plaats de pinnen aan de rechter kant van de omlijsting in de gaten op de koppelings flens van het knoop punt. 
1. Aan de linkerkant van de omlijsting op het chassis passen. Druk op de omlijsting totdat de knop aan de linkerkant wordt geklikt.
1. Vergrendel de omlijsting met de sleutel.

De omlijsting vooraan verwijderen: 
1. Ontgrendel de omlijst met behulp van de sleutel omlijstend.
1. Klik aan de linkerkant op de knop release en haal de linkerkant van de omlijsting weg van het chassis.
1. Verwijder de rechter kant en de omlijsting verwijderen.
   
   ![Afbeelding van de knop voor de introductie aan de linkerkant van de omlijst en hoe u deze kunt verwijderen door vanuit de linkerkant naar buiten te halen](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u het apparaat hebt uitgepakt en gerek, gaat u door met de installatie door netwerk kabels te koppelen en netstroom te verbinden met de Azure FXT Edge-Bestandsr.

> [!div class="nextstepaction"]
> [De netwerk poorten bekabelen en energie leveren](fxt-network-power.md)