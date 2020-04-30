---
title: Zelf studie voor installatie-unpack, Rack, kabel Azure Data Box Edge fysiek apparaat | Microsoft Docs
description: De tweede zelfstudie over het installeren van Azure Data Box Edge gaat over het uitpakken, het in een rek monteren en het bekabelen van het fysieke apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: fe74db34e62a80935954c6cfc2e591d49a84b0b7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76263945"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Zelf studie: Azure Data Box Edge installeren

In deze zelfstudie wordt beschreven hoe u een fysiek Data Box Edge-apparaat installeert. De installatieprocedure omvat het uitpakken, het in een rek monteren en het bekabelen van het apparaat. 

De installatie duurt ongeveer twee uur.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het apparaat uitpakken
> * Het apparaat in een rek monteren
> * Het apparaat bekabelen

## <a name="prerequisites"></a>Vereisten

De vereisten voor het installeren van een fysiek apparaat zijn als volgt:

### <a name="for-the-data-box-edge-resource"></a>Voor de Data Box Edge-resource

Zorg voordat u begint voor het volgende:

* U hebt alle stappen voor het voorbereiden van de [implementatie van Azure data Box Edge](data-box-edge-deploy-prep.md)voltooid.
    * U hebt een Data Box Edge-resource gemaakt voor het implementeren van uw apparaat.
    * U hebt de activeringscode gegenereerd voor het activeren van uw apparaat met de Data Box Edge-resource.

 
### <a name="for-the-data-box-edge-physical-device"></a>Voor het fysieke Data Box Edge-apparaat

Voordat u een apparaat implementeert, controleert u het volgende:

- Het apparaat staat veilig op een vlak, stabiel en horizontaal werkoppervlak.
- De locatie site waar u het apparaat wilt opstellen, voldoet aan deze voorwaarden:
    - Standaardnetstroom aanwezig van een onafhankelijke bron

        OF
    - Een PDU (Power Distribution Unit) aanwezig met een noodvoeding (UPS)
    - Een vrij 1U-slot in het rek waarin u het apparaat wilt monteren

### <a name="for-the-network-in-the-datacenter"></a>Voor het netwerk in het datacenter

Voordat u begint:

- Controleer de netwerkvereisten voor het implementeren van een Data Box Edge en configureer het netwerk van het datacenter aan de hand van die vereisten. Zie [Netwerkvereisten voor Data Box Edge](data-box-edge-system-requirements.md#networking-port-requirements) voor meer informatie.

- Voor een optimale werking van het apparaat heeft internet een minimale bandbreedte van 20 Mbps nodig.


## <a name="unpack-the-device"></a>Het apparaat uitpakken

Dit apparaat wordt verzonden in één doos. Voer de volgende stappen uit om het apparaat uit te pakken. 

1. Leg de doos op een vlak, horizontaal oppervlak.
2. Controleer de doos en het verpakkingsmateriaal op deuken, scheuren, waterschade of andere duidelijk zichtbare schade. Als de doos of het verpakkingsmateriaal ernstig beschadigd is, moet u de doos niet openen. Neem contact op met Microsoft Support om in overleg te bepalen of het apparaat nog in goede staat zal zijn.
3. Pak de doos uit. Controleer of de inhoud van de doos bestaat uit de volgende items:
    - Eén behuizing Data Box Edge apparaat
    - Twee netsnoeren
    - Eén Rail Kit-assembly
    - Een boekje met veiligheids-, omgevings-en regelgevings informatie

Als een of meer van de bovenstaande items ontbreken, neemt u contact op met de Data Box Edge-ondersteuning. De volgende stap is het in het rek monteren van uw apparaat.


## <a name="rack-the-device"></a>Het apparaat in het rek monteren

Het apparaat moet worden geïnstalleerd in een standaard 19-inch rek. Gebruik de volgende procedure om uw apparaat te koppelen aan een standaard 19-inch rack.

> [!IMPORTANT]
> Data Box Edge-apparaten moeten voor een goede werking in een rek worden gemonteerd.


### <a name="prerequisites"></a>Vereisten

- Lees voordat u begint de veiligheids instructies in uw boekje met veiligheid, milieu en reglementaire informatie. Dit boekje is geleverd bij het apparaat.
- Beginnen met de installatie van de rails in de toegewezen ruimte die het dichtst bij de onderkant van de rack behuizing ligt.
- Voor de configuratie van het hulp programma voor spoorstaaf montage:
    -  U moet acht schroeven opgeven: #10-32, #12-24, #M5 of #M6. De hoofd diameter van de schroeven moet kleiner zijn dan 10 mm (0,4).
    -  U hebt een draaier met een vlakke kanteling nodig.

### <a name="identify-the-rail-kit-contents"></a>De inhoud van de Rail Kit identificeren

Zoek de onderdelen voor het installeren van de Rail Kit-assembly:
1. Twee A7 Dell ReadyRails II-schuif rails
2. Twee Hook-en loop riemen

    ![Inhoud van de Rail Kit identificeren](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Hulp programma-minder rails installeren en verwijderen (vier Kante openingen of racks met ronde openingen)

> [!TIP]
> Deze optie is minder goed, omdat deze geen hulpprogram ma's nodig heeft om de rails te installeren en verwijderen in de niet-gethreadde vier Kante of afgeronde gaten in de racks.

1. Plaats de eind stukken van de linker-en rechter lijn met het label aan de **voor zijde** naar rechts en zet elk eind stuk naar seat in de gaten aan de voor zijde van de verticale rack flenss.
2. Elk eind stuk in de onderste en bovenste gaten van de gewenste U ruimten uitlijnen.
3. Zet de back-end van de Rail totdat deze volledig is gekoppeld aan de verticale rack flens en klik op de vergren deling. Herhaal deze stappen om het front-end-gedeelte op de verticale rack flens te plaatsen en positioneren.
4. Als u de rails wilt verwijderen, geeft u de vergren deling van de vergrendelings knop op het eind punt van het gedeelte op en verwijdert u elke rail.

    ![Hulp programma-minder rails installeren en verwijderen](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Uitgewerkte rails installeren en verwijderen (gethreadd hole-racks)

> [!TIP]
> Deze optie is alleen mogelijk als er een hulp programma (_een platte draaier_) is vereist voor het installeren en verwijderen van de rails in de geschroefde ronde gaten in de racks.

1. Verwijder de pincodes van de voor-en hand beugels met een vlakke draaier.
2. Haal de subassemblages van de Rail hendel op en Roteer ze om ze te verwijderen van de beugels.
3. Koppel de linker-en rechter-koppel rails aan de voor zijde verticale rack flenss met behulp van twee combi Naties van schroeven.
4. Schuif de linker-en rechter kant omhoog en klik op de pijl naar rechts op de verticale rack flens en voeg deze toe met behulp van twee combi Naties van schroeven.

    ![Hulpprogram ma's voor het hulp programma installeren en verwijderen](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Het systeem in een rek installeren

1. Haal de binnenste schuif rails uit het rek totdat ze zijn vergrendeld.
2. Zoek de Standoff aan de achterzijde op elke zijde van het systeem en verlaag deze naar de eerste J-sleuven op de dia-verzamelingen. Roteer het systeem omlaag totdat alle Rail standoffs in de J-sleuven zijn geplaatst.
3. Push het systeem naar binnen totdat de vergren delingen worden geplaatst.
4. Druk op de knoppen voor het vergren delen van de schuif regelaar op beide rails en schuif het systeem in het rek.

    ![Systeem in een rek installeren](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Het systeem uit het rek verwijderen

1. Zoek de vergrendelings hendels aan de zijkanten van de interne rails.
2. Ontgrendel elke hendel door deze naar de release positie te verplaatsen.
3. Begrijpt de zijkanten van het systeem stevig af en haal het door, totdat de Rail standoffs vooraan in de J-sleuven staan. Til het systeem omhoog en weg van het rek en plaats het op een niveau vlak.

    ![Het systeem uit het rek verwijderen](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>De Slam-hendel benaderen en vrijgeven

> [!NOTE]
> Voor systemen die niet zijn uitgerust met Slam-hendels, kunt u het systeem beveiligen met schroeven, zoals beschreven in stap 3 van deze procedure.

1. Zoek de Slam-hendel aan beide zijden van het systeem.
2. De vergren delingen worden automatisch geactiveerd wanneer het systeem wordt gepusht naar het rek en wordt vrijgegeven door de vergren delingen op te halen.
3. Als u het systeem voor verzen ding in het rek of voor andere onstabiele omgevingen wilt beveiligen, zoekt u de vaste montagekit onder elke hendel en draait u elke schroef met een #2 Phillips draaier.

    ![Slam-hendel benaderen en vrijgeven](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Het apparaat bekabelen

De kabels routeren en vervolgens uw apparaat bekabelen. In de volgende procedures wordt uitgelegd hoe u uw Data Box Edge apparaat kunt bekabelen voor energie en netwerk.

Voordat u begint met de bekabeling van uw apparaat, zorgt u ervoor dat u over de volgende zaken beschikt:

- Uw Data Box Edge fysiek apparaat, uitgepakt en rek gekoppeld.
- Twee netsnoeren.
- Ten minste één 1-GbE RJ-45-netwerkkabel voor verbinding met de beheerinterface. Er zijn twee 1-GbE-netwerkinterfaces, één beheerinterface en één gegevensinterface, op het apparaat aanwezig.
- Eén koperen 25-GbE SFP+-kabel voor elke te configureren netwerkinterface. Ten minste één netwerkinterface (PORT 2, PORT 3, PORT 4, PORT 5 of PORT 6) moet worden verbonden met internet (en met Azure).  
- Toegang tot twee Power Distribution Units (aanbevolen).

> [!NOTE]
> - Als u slechts één gegevens netwerk interface verbindt, raden we u aan om een 25/10-GbE-netwerk interface te gebruiken, zoals poort 3, poort 4, poort 5 of poort 6 voor het verzenden van gegevens naar Azure. 
> - Voor de beste prestaties en voor het verwerken van grote gegevensvolumes, kunt u eventueel alle gegevenspoorten verbinden.
> - Het Data Box Edge apparaat moet worden verbonden met het datacenter netwerk zodat het gegevens kan opnemen van gegevens bron servers.

Op uw Data Box Edge-apparaat:

- Het voor paneel heeft schijf stations en een aan/uit-knop.

    - Er zijn 10 schijf sleuven aan de voor zijde van het apparaat.
    - Sleuf 0 heeft een 240-GB SATA-schijf dat wordt gebruikt als een besturingssysteem schijf. Sleuf 1 is leeg en sleuven van 2 tot 9 zijn NVMe Ssd's gebruikt als gegevens schijven.
- Het back-vlak bevat redundante voedings eenheden (PSUs).
- Het back-upvlak heeft zes netwerk interfaces:

    - Twee 1-Gbps-interfaces.
    - 4 25-Gbps-interfaces die ook kunnen fungeren als 10-Gbps-interfaces.
    - Een Base Board management controller (BMC).

- Het back-upvlak heeft twee netwerk kaarten die overeenkomen met de 6 poorten:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

Voor een volledige lijst met ondersteunde kabels, switches en transceivers voor deze netwerk kaarten gaat u naar de [Cavium FastlinQ-serie](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)van de 41000-reeks.
 
Voer de volgende stappen uit om uw apparaat te bekabelen voor stroom en verbinding met het netwerk.

1. Identificeer de verschillende poorten op het achtergrond vlak van uw apparaat.

    ![Achtergrond vlak van een aangesloten apparaat](./media/data-box-edge-deploy-install/backplane-cabled.png)

2. Zoek de schijf sleuven en de aan/uit-knop aan de voor zijde van het apparaat.

    ![Voor vlak van een apparaat](./media/data-box-edge-deploy-install/device-front-plane-labeled-1.png)

3. Sluit de netsnoeren aan op de PSU's in de behuizing. Voor een hoge beschikbaarheid moet u beide PSU's verbinden met verschillende stopcontacten.
4. Sluit de netsnoeren aan op de PDU's van het rek. Zorg ervoor dat de twee PSU's op verschillende stopcontacten zijn aangesloten.
5. Druk op de aan/uit-knop om het apparaat in te scha kelen.
6. Verbind PORT 1 van de 1-GbE netwerkinterface met de computer die wordt gebruikt voor het configureren van het fysieke apparaat. PORT 1 is de gereserveerde beheerinterface.
7. Verbind één of meer interfaces, bijvoorbeeld PORT 2, PORT 3, PORT 4, PORT 5 of PORT 6 aan het netwerk van het datacenter of internet.

    - Gebruik de RJ-45-netwerkkabel als u PORT 2 verbindt.
    - Gebruik de SFP + koper kabels voor de netwerk interfaces van 10/25 GbE.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Data Box Edge, zoals:

> [!div class="checklist"]
> * Het apparaat uitpakken
> * Het apparaat in het rek monteren
> * Het apparaat bekabelen

Ga naar de volgende zelfstudie voor informatie over het verbinden, instellen en activeren van uw apparaat.

> [!div class="nextstepaction"]
> [Data Box Edge aansluiten en instellen](./data-box-edge-deploy-connect-setup-activate.md)
