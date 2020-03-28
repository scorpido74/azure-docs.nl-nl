---
title: 'Zelfstudie: Een fysiek Azure FXT Edge Filer-apparaat installeren'
description: Het fysieke apparaatonderdeel van de hybride opslagcache van Microsoft Azure FXT Edge Filer uitpakken, racken en bekabelen
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7c77e25d0dcabc49f2e6672645c6bc41e8662ec8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551026"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Zelfstudie: Azure FXT Edge Filer installeren 

In deze zelfstudie wordt beschreven hoe u een hardwareknooppunt installeert voor de hybride opslagcache van Azure FXT Edge Filer. U moet ten minste drie hardwareknooppunten installeren om een Azure FXT Edge Filer-cluster te maken.

De installatieprocedure omvat het uitpakken en het monteren van het apparaat, en het bevestigen van de kabelmanagementarm (CMA) en de bezel aan de voorkant. In een aparte zelfstudie wordt uitgelegd dat netwerkkabels en aansluitkracht worden aangesloten. 

Het installeren van een Azure FXT Edge Filer-knooppunt duurt ongeveer een uur. 

Deze zelfstudie bevat de volgende installatiestappen: 

> [!div class="checklist"]
> * Het apparaat uitpakken
> * Zet het apparaat in een rek
> * De bezel aan de voorkant installeren (optioneel)

## <a name="installation-prerequisites"></a>Installatievereisten 

Controleer voordat u begint of het datacenter en het rack dat u gebruikt de volgende functies heeft:

* Een beschikbare 1U-sleuf op het rek waar u van plan bent het apparaat te monteren.
* Ac-voeding en koelsystemen die voldoen aan de behoeften van de Azure FXT Edge Filer. (Lees [de energie- en thermische specificaties](fxt-specs.md#power-and-thermal-specifications) voor de planning en grootte van de installatie.)  

  > [!NOTE] 
  > Om optimaal gebruik te maken van de twee redundante voedingen (PSU's), gebruikt u stroomverdeeleenheden op twee verschillende vertakkingscircuits bij het bevestigen van wisselstroom. Lees [Kabels verbinden](fxt-network-power.md#connect-power-cables) voor meer informatie.  

## <a name="unpack-the-hardware-node"></a>Het hardwareknooppunt uitpakken 

Elke Azure FXT Edge Filer-knooppunt wordt in één doos verzonden. Voer deze stappen uit om een apparaat uit te pakken.

1. Leg de doos op een vlak, horizontaal oppervlak.

2. Controleer de doos en het verpakkingsmateriaal op deuken, scheuren, waterschade of andere duidelijk zichtbare schade. Als de doos of het verpakkingsmateriaal ernstig beschadigd is, moet u de doos niet openen. Neem contact op met Microsoft Support om in overleg te bepalen of het apparaat nog in goede staat zal zijn.

3. Pak de doos uit. Zorg ervoor dat de volgende items worden bevat:
   * Eén FXT-behuizing-apparaat
   * Twee netsnoeren
   * Eén bezel aan de voorkant en toets
   * Eén railkitmontage
   * Eén kabelmanagementarm (CMA)
   * CMA installatieinstructiesboekje
   * Rek installatieinstructiesboekje
   * Boekje informatie over veiligheid, milieu en regelgeving

Als u niet alle genoemde items hebt ontvangen, neemt u contact op met de apparaatleverancier voor ondersteuning. 

Zorg ervoor dat het apparaat voldoende tijd heeft gehad om dezelfde temperatuur te bereiken als de ruimte voordat u het installeert of aangeeft. Als u condensatie op een deel van het apparaat opmerkt, wacht u minstens 24 uur voordat u het installeert.

De volgende stap is het in het rek monteren van uw apparaat.

## <a name="rack-the-device"></a>Het apparaat in het rek monteren

Het Azure FXT Edge Filer-apparaat moet worden geïnstalleerd in een standaard 19-inch rack. 

De hybride opslagcache van Azure FXT Edge Filer bestaat uit drie of meer Azure FXT Edge Filer-apparaten. Herhaal de installatiestappen voor het rack voor elk apparaat dat deel uitmaakt van uw systeem. 

### <a name="rack-install-prerequisites"></a>Vereisten voor het installeren van rackvereisten

* Lees voordat u begint de veiligheidsinstructies in het boekje Veiligheids-, Milieu- en Regelgevingsinformatie dat met uw apparaat is verzonden.

  > [!NOTE]
  > Gebruik altijd twee personen bij het optillen van het knooppunt, ook wanneer u het in een rek installeert of uit het rek haalt. 

* Identificeer het type railinstallatie dat wordt gebruikt met uw apparatuurrek. 
  * Voor vierkante of ronde gat snap-in rekken, volg de tool-less rail instructies.
  * Voor schroefdraad gat rekken, volg de tooled rail instructies. 
  
    Voor de gereedschapsrailmontageconfiguratie moet u acht schroeven leveren, type 10-32, 12-24, M5 of M6. De kopdiameter van de schroeven moet minder dan 10 mm (0,4" zijn).

### <a name="identify-the-rail-kit-contents"></a>De inhoud van de railkit identificeren

Zoek de onderdelen voor het installeren van de railkitassemblage:

1. Twee A7 Dell ReadyRails II schuifrailsamenstellingen (1)
1. Twee haak- en lusriemen (2)

![Genummerde tekening van de inhoud van de railkit](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Rail montage - gereedschap-minder rails (Vierkant gat of ronde gat rekken)

Voor rekken met snap-in vierkante of ronde gaten, volg deze procedure te monteren en installeren van de rails. 

1. Plaats de linker en rechter rail eindstukken gelabeld **FRONT** naar binnen gericht. Plaats elk eindstuk zo dat het in de gaten aan de voorzijde van de verticale rekflenzen plaatst. (1)

2. Lijn elk eindstuk in de onderste en bovenste gaten van het rek in de ruimte die u wilt monteren. (2)

3. Schakel de achterkant van de rail in totdat deze volledig op de verticale rekflens zit en de vergrendeling op zijn plaats klikt. Herhaal deze stappen om het voorste uiteinde stuk op de verticale rekflens te plaatsen en te plaatsen. (3)

> [!TIP]
> Als u de rails wilt verwijderen, trekt u aan de vergrendelingsknop op het middelpunt van het eindstuk (4) en ontseatt u elke rail.

![Diagram voor het installeren en verwijderen van gereedschapsloze rails, met genummerde stappen](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Rail montage - gereedschap rails (Schroefdraad gat rekken)

Voor rekken met schroefdraad gaten, volg deze procedure te monteren en installeren van de rails.

1. Verwijder de pinnen van de voorste en achterste montagebeugels met een platte schroevendraaier. (1)
1. Trek en draai de railvergrendeling subassemblies om ze te verwijderen uit de montagebeugels. (2)
1. Bevestig de linker- en rechtermontagerails aan de voorste verticale rekflenzen met behulp van twee paar schroeven. (3)
1. Schuif de linker- en rechterachterbeugels naar voren tegen de verticale achterste rekflenzen en bevestig ze met behulp van twee paar schroeven. (4)

![Diagram voor het installeren en verwijderen van bedienrails, met genummerde stappen](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Installeer het systeem in het rek

Volg deze stappen om het Azure FXT Edge Filer-apparaat in het rack te monteren.

1. Trek de binnenste schuifrails uit het rek totdat ze op hun plaats worden vergrendeld. (1)
1. Plaats de achterste railstandoff aan elke kant van het apparaat en laat ze in de achterste J-sleuven op de schuifsamenstellingen zakken. (2) 
1. Draai het apparaat naar beneden totdat alle railstandoffs in de J-sleuven zitten. (3)
1. Duw het apparaat naar binnen totdat de vergrendelingshendels op hun plaats klikken.
1. Druk op de schuifvergrendelingsknoppen op beide rails (4) en schuif het apparaat in het rek.

![Installatiesysteem in een rackdiagram, met stappen genummerd](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Het systeem uit het rek halen

Als u het apparaat uit het rek wilt verwijderen, volgt u deze procedure. 

1. Plaats de vergrendelingshendels aan de zijkanten van de binnenrails (1).
2. Ontgrendel elke hendel door deze naar de ontgrendelingspositie te draaien (2).
3. Pak de zijkanten van het systeem stevig vast en trek het naar voren totdat de railstandoffs zich aan de voorzijde van de J-sleuven bevinden. Til het systeem op en uit de buurt van het rek en plaats het op een vlakke ondergrond (3).

![Illustratie van het verwijderen van een systeem uit het rek, met genummerde stappen](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Schakel de slagklink in

1. Naar voren, zoek de slam klink (1) aan weerszijden van het systeem.
2. De vergrendelingen gaan automatisch in werking als het systeem in het rek wordt geduwd. 

Als u de vergrendelingen wilt loslaten bij het verwijderen van het systeem, trekt u ze omhoog (2).

Optionele hard-mount schroeven zijn aanwezig om het systeem te beveiligen aan het rek voor verzending of in andere onstabiele omgevingen. Zoek de schroef onder elke vergrendeling en draai ze aan met een #2 Phillips schroevendraaier (3).

![Genummerde illustratie van het in dienst nemen en het vrijgeven van de slagklink](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>De kabelmanagementarm installeren 

Een optionele kabelmanagementarm (CMA) is voorzien van de FXT Edge Filer. Gedrukte instructies voor het installeren van het zijn voorzien in de verpakking. 

1. Pak de onderdelen van de kabelmanagementarmkit uit en identificeer deze:
   * CMA-lade (1)
   * CMA (2)
   * Nylon kabelbinderwraps (3)
   * CMA-bevestigingsbeugels (4)
   * Statusindicatorkabel (5) 

   > [!TIP] 
   > Om de CMA veilig te stellen voor verzending in het rek, lus de das wraps rond beide manden en lade en cinch ze stevig. Als u de CMA op deze manier vastzet, wordt uw systeem ook beveiligd in onstabiele omgevingen.

   ![Illustratie van CMA-onderdelen](media/fxt-install/cma-kit-400.png)

2. Installeer de CMA-lade.

   De CMA tray biedt ondersteuning en fungeert als een voorschot voor de CMA. 

   1. Lijn aan weerszijden van de lade uit en schakel deze in met de beugelvan de ontvanger aan de binnenranden van de rails. 
   1. Duw de lade naar voren totdat deze op zijn plaats klikt. (1)
   1. Als u de lade wilt verwijderen, knijpt u de knoppen voor het loslaten van de vergrendeling naar het midden en trekt u de lade uit de beugelvan de ontvanger (2).

   ![Illustratie van de installatie van de cma-lade](media/fxt-install/cma-tray-install-400.png)

3. Installeer de CMA-bevestigingsbeugels. 

   > [!NOTE]
   >
   > * U de CMA aan de rechter- of linkermontagerail bevestigen, afhankelijk van hoe u kabels uit het systeem wilt routeren. 
   > * Monteer voor het gemak de CMA aan de zijkant tegenover de voedingen (kant A). Als deze aan zijde B is gemonteerd, moet de CMA worden losgekoppeld om de buitenvoeding te verwijderen. 
   > * Verwijder de lade altijd voordat u de voedingen verwijdert. 

   ![Illustratie van de installatie van de steun van CMA](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Selecteer de juiste CMA-bevestigingsbeugel voor de zijkant waar u de CMA wilt monteren (zijde B of zijde A).
   1. Installeer de CMA bevestigingsbeugel met de bijbehorende zij-A- of zij-B-markering aan de achterkant van de schuifrail.
   1. Lijn de gaten op de beugel uit met de pinnen op de schuifrail. Duw de beugel naar beneden totdat deze op zijn plaats wordt vergrendeld. 

4. Installeer de CMA.

   1. Aan de achterkant van het systeem, past de vergrendeling op de voorkant van de CMA op de binnenste beugel van de dia assemblage totdat de vergrendeling inschakelt (1). 
   1. Plaats de andere vergrendeling aan het uiteinde van de buitenste beugel totdat de vergrendeling inschakelt (2). 
   1. Als u de CMA wilt verwijderen, schakelt u beide vergrendelingen uit door op de CMA-ontgrendelknoppen aan de bovenkant van de binnen- en buitenvergrendelingsbehuizingen te drukken (3).

   ![Illustratie van de belangrijkste INSTALLATIE CMA](media/fxt-install/cma-install-400.png)

   De CMA kan worden gedraaid uit de buurt van het systeem voor toegang en service. Til aan het scharnierende uiteinde de CMA uit de buurt van de lade om hem los te maken (1). Nadat deze uit de lade is gehaald, slingert u de CMA uit de buurt van het systeem (2).

   ![Illustratie van CMA die open voor de dienst wordt gedraaid](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>De bezel aan de voorkant installeren (optioneel)

In dit gedeelte wordt uitgelegd hoe u de bezel aan de voorkant (voorplaat) voor de Azure FXT Edge Filer-hardware installeert en verwijdert. 

Ga als het gaat om het installeren van de bezel aan de voorkant: 

1. Zoek en verwijder de bezeltoets, die in het bezelpakket zit. 
1. Lijn de bezel uit met de voorkant van het chassis en steek de pinnen aan de rechterkant van de lunette in de gaten aan de rechterzijde van het knooppunt. 
1. Plaats het linkeruiteinde van de lunette op het chassis. Druk op de bezel totdat de knop aan de linkerkant op zijn plaats klikt.
1. Vergrendel de bezel met de sleutel.

Ga als het gaat om het verwijderen van de bezel aan de voorkant: 
1. Ontgrendel de bezel met behulp van de bezeltoets.
1. Druk op de ontspanknop aan de linkerkant en trek het linkeruiteinde van de lunette uit de buurt van het chassis.
1. Sluit het juiste uiteinde los en verwijder de bezel.
   
   ![Afbeelding met de ontspanknop aan de linkerkant van de bezel en hoe deze te verwijderen door vanaf de linkerkant naar buiten te trekken](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u het apparaat hebt uitgepakt en gerackt, u de installatie voortzetten door netwerkkabels aan te sluiten en ac-stroom aan te sluiten op de Azure FXT Edge Filer.

> [!div class="nextstepaction"]
> [Kabel de netwerkpoorten en de voeding](fxt-network-power.md)