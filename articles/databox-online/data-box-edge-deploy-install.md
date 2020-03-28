---
title: Zelfstudie om te installeren - Uitpakken, rack, kabel Azure Data Box Edge fysiek apparaat | Microsoft Documenten
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76263945"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Zelfstudie: Azure Data Box Edge installeren

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

* U hebt alle stappen voltooid in [Voorbereiden om Azure Data Box Edge te implementeren.](data-box-edge-deploy-prep.md)
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
    - Eén gegevensboxrandapparaat met één behuizing
    - Twee netsnoeren
    - Eén railkitmontage
    - Een informatieboekje voor veiligheid, milieu en regelgeving

Als een of meer van de bovenstaande items ontbreken, neemt u contact op met de Data Box Edge-ondersteuning. De volgende stap is het in het rek monteren van uw apparaat.


## <a name="rack-the-device"></a>Het apparaat in het rek monteren

Het apparaat moet worden geïnstalleerd in een standaard 19-inch rek. Gebruik de volgende procedure om uw apparaat op een standaard 19-inch rack te monteren.

> [!IMPORTANT]
> Data Box Edge-apparaten moeten voor een goede werking in een rek worden gemonteerd.


### <a name="prerequisites"></a>Vereisten

- Lees voordat u begint de veiligheidsinstructies in uw boekje met veiligheids-, milieu- en regelgevingsinformatie. Dit boekje is verzonden met het apparaat.
- Begin met het installeren van de rails in de toegewezen ruimte die het dichtst bij de onderkant van de rackbehuizing ligt.
- Voor de opstelling van de gereedschapsrail:
    -  Je moet acht schroeven leveren: #10-32, #12-24, #M5 of #M6. De kopdiameter van de schroeven moet minder dan 10 mm (0,4" zijn).
    -  Je hebt een platte schroevendraaier nodig.

### <a name="identify-the-rail-kit-contents"></a>De inhoud van de railkit identificeren

Zoek de onderdelen voor het installeren van de railkitassemblage:
1. Twee A7 Dell ReadyRails II schuifrailassemblages
2. Twee haak- en lusriemen

    ![Inhoud van de railkit identificeren](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Gereedschapsloze rails installeren en verwijderen (vierkante gat- of ronde gatrekken)

> [!TIP]
> Deze optie is gereedschapsloos omdat er geen gereedschap nodig is om de rails in de niet-gedraalde vierkante of ronde gaten in de rekken te installeren en te verwijderen.

1. Plaats de linker en rechter rail eindstukken gelabeld **FRONT** naar binnen gericht en oriënteer elk eindstuk te zitten in de gaten aan de voorzijde van de verticale rekflenzen.
2. Lijn elk eindstuk in de onderste en bovenste gaten van de gewenste U-ruimten uit.
3. Schakel de achterkant van de rail in totdat deze volledig op de verticale rekflens zit en de vergrendeling op zijn plaats klikt. Herhaal deze stappen om het voorste uiteinde stuk op de verticale rekflens te plaatsen en te plaatsen.
4. Als u de rails wilt verwijderen, trekt u aan de knop voor het loslaten van het eindstuk en ontseatt u elke rail.

    ![Rails zonder gereedschap installeren en verwijderen](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Gereedschapsrails installeren en verwijderen (Threaded hole racks)

> [!TIP]
> Deze optie is uitgerust omdat het een gereedschap _(een platte schroevendraaier)_ nodig heeft om de rails in de schroefdraad ronde gaten in de rekken te installeren en te verwijderen.

1. Verwijder de pinnen van de voorste en achterste montagebeugels met behulp van een platte schroevendraaier.
2. Trek en draai de railvergrendeling subassemblies om ze te verwijderen uit de montagebeugels.
3. Bevestig de linker- en rechtermontagerails aan de voorste verticale rekflenzen met behulp van twee paar schroeven.
4. Schuif de linker- en rechterachterbeugels naar voren tegen de verticale achterste rekflenzen en bevestig ze met behulp van twee paar schroeven.

    ![Gereedschapsrails installeren en verwijderen](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Het systeem in een rack installeren

1. Trek de binnenste schuifrails uit het rek totdat ze op hun plaats worden vergrendeld.
2. Zoek de achterste rail standoff aan elke kant van het systeem en laat ze in de achterste J-slots op de dia samenstellingen. Draai het systeem naar beneden totdat alle railstandoffs in de J-sleuven zitten.
3. Duw het systeem naar binnen totdat de slothendels op hun plaats klikken.
4. Druk op de schuifontgrendelingsvergrendelingsknoppen op beide rails en schuif het systeem in het rek.

    ![Installatiesysteem in een rack](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Het systeem uit het rek halen

1. Plaats de slothendels aan de zijkanten van de binnenrails.
2. Ontgrendel elke hendel door deze naar de ontgrendelingspositie te draaien.
3. Pak de zijkanten van het systeem stevig vast en trek het naar voren totdat de railstandoffs zich aan de voorzijde van de J-sleuven bevinden. Til het systeem op en uit de buurt van het rek en plaats het op een vlakke ondergrond.

    ![Systeem uit het rek verwijderen](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Schakel de slagklink in en laat deze los

> [!NOTE]
> Voor systemen die niet zijn uitgerust met slam-vergrendelingen, u het systeem beveiligen met schroeven, zoals beschreven in stap 3 van deze procedure.

1. Naar voren, zoek de slam klink aan weerszijden van het systeem.
2. De vergrendelingen gaan automatisch in werking als het systeem in het rek wordt geduwd en worden losgelaten door op de vergrendelingen te trekken.
3. Om het systeem te beveiligen voor verzending in het rek of voor andere onstabiele omgevingen, zoek de hard-mount schroef onder elke vergrendeling en draai elke schroef met een #2 Phillips schroevendraaier.

    ![Schakel en laat slamvergrendeling los](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Het apparaat bekabelen

Leid de kabels en kabel vervolgens met uw apparaat. In de volgende procedures wordt uitgelegd hoe u uw Data Box Edge-apparaat bekabelen voor stroom en netwerk.

Voordat u begint met de bekabeling van uw apparaat, zorgt u ervoor dat u over de volgende zaken beschikt:

- Uw databox rand fysiek apparaat, uitgepakt, en rack gemonteerd.
- Twee netsnoeren.
- Ten minste één 1-GbE RJ-45-netwerkkabel voor verbinding met de beheerinterface. Er zijn twee 1-GbE-netwerkinterfaces, één beheerinterface en één gegevensinterface, op het apparaat aanwezig.
- Eén koperen 25-GbE SFP+-kabel voor elke te configureren netwerkinterface. Ten minste één netwerkinterface (PORT 2, PORT 3, PORT 4, PORT 5 of PORT 6) moet worden verbonden met internet (en met Azure).  
- Toegang tot twee Power Distribution Units (aanbevolen).

> [!NOTE]
> - Als u slechts één datanetwerkinterface verbindt, raden we u aan een 25/10 GbE-netwerkinterface te gebruiken, zoals PORT 3, PORT 4, PORT 5 of PORT 6 om gegevens naar Azure te verzenden. 
> - Voor de beste prestaties en voor het verwerken van grote gegevensvolumes, kunt u eventueel alle gegevenspoorten verbinden.
> - Het Data Box Edge-apparaat moet worden aangesloten op het datacenternetwerk, zodat het gegevens kan opnemen van gegevensbronservers.

Op uw Data Box Edge-apparaat:

- Het voorpaneel heeft schijven en een aan/uit-knop.

    - Er zijn 10 schijfsleuven aan de voorkant van uw apparaat.
    - Slot 0 heeft een 240 GB SATA-station dat wordt gebruikt als een besturingssysteemschijf. Slot 1 is leeg en slots 2 tot 9 zijn NVMe SSD's die als dataschijven worden gebruikt.
- Het achterste vlak bevat redundante voedingen (PSU's).
- Het achtervlak heeft zes netwerkinterfaces:

    - Twee interfaces van 1 Gbps.
    - Vier 25-Gbps interfaces die ook kunnen dienen als 10-Gbps interfaces.
    - Een baseboard management controller (BMC).

- Het achterste vlak heeft twee netwerkkaarten die overeenkomen met de 6 poorten:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

Ga voor een volledige lijst van ondersteunde kabels, switches en transceivers voor deze netwerkkaarten naar [Cavium FastlinQ 41000-serie interoperabiliteitsmatrix.](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)
 
Voer de volgende stappen uit om uw apparaat te bekabelen voor stroom en verbinding met het netwerk.

1. Identificeer de verschillende poorten op het achtervlak van uw apparaat.

    ![Achtervlak van een bekabeld apparaat](./media/data-box-edge-deploy-install/backplane-cabled.png)

2. Zoek de schijfsleuven en de aan/uit-knop aan de voorkant van het apparaat.

    ![Voorvlak van een apparaat](./media/data-box-edge-deploy-install/device-front-plane-labeled-1.png)

3. Sluit de netsnoeren aan op de PSU's in de behuizing. Voor een hoge beschikbaarheid moet u beide PSU's verbinden met verschillende stopcontacten.
4. Sluit de netsnoeren aan op de PDU's van het rek. Zorg ervoor dat de twee PSU's op verschillende stopcontacten zijn aangesloten.
5. Druk op de aan/uit-knop om het apparaat in te schakelen.
6. Verbind PORT 1 van de 1-GbE netwerkinterface met de computer die wordt gebruikt voor het configureren van het fysieke apparaat. PORT 1 is de gereserveerde beheerinterface.
7. Verbind één of meer interfaces, bijvoorbeeld PORT 2, PORT 3, PORT 4, PORT 5 of PORT 6 aan het netwerk van het datacenter of internet.

    - Gebruik de RJ-45-netwerkkabel als u PORT 2 verbindt.
    - Gebruik voor de 10/25 GbE-netwerkinterfaces de SFP+ koperkabels.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Data Box Edge, zoals:

> [!div class="checklist"]
> * Het apparaat uitpakken
> * Het apparaat in het rek monteren
> * Het apparaat bekabelen

Ga naar de volgende zelfstudie voor informatie over het verbinden, instellen en activeren van uw apparaat.

> [!div class="nextstepaction"]
> [Data Box Edge aansluiten en instellen](./data-box-edge-deploy-connect-setup-activate.md)
