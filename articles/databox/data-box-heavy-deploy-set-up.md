---
title: Zelf studie voor het instellen van Azure Data Box Heavy | Microsoft Docs
description: Meer informatie over het bekabelen en aansluiten van uw Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: b6b353e0e01f3f598048e5fbb2682603045b1037
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164463"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Zelfstudie: Kabel en verbinding maken met uw Azure Data Box Heavy

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Aan de slag met Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

In deze zelf studie wordt beschreven hoe u uw Azure Data Box Heavy kunt bekabelen, verbinden en inschakelen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw Data Box Heavy bekabelen
> * Verbinding maken met uw Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Deze hand leiding bevat instructies voor het controleren van vereisten, het bekabelen en aansluiten van uw apparaat, het kopiëren van gegevens, het uploaden naar Azure en het controleren van de geüploade gegevens.

::: zone-end

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de zelfstudie [ Volg orde](data-box-heavy-deploy-ordered.md)Azure data Box Heavy.
2. U hebt uw Data Box Heavy ontvangen en de status van de bestelling in dePortal is afgeleverd.
3. U hebt de [Data Box Heavy veiligheids richtlijnen](data-box-safety.md)gecontroleerd.
4. U moet toegang hebben tot een platte site in het Data Center met nabij een beschik bare netwerk verbinding die een apparaat met deze footprint kan ondersteunen. Dit apparaat kan niet worden gekoppeld in een rek.
5. U hebt vier gegronde netstroom kabels ontvangen die u kunt gebruiken met uw opslag apparaat.
6. Er is een hostcomputer verbonden met het datacenternetwerk. Uw Data Box Heavy kopieert de gegevens van deze computer. Op de hostcomputer moet een [ondersteund besturings systeem](data-box-heavy-system-requirements.md)worden uitgevoerd.
7. Uw datacenter moet een netwerk met hoge snelheid hebben. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. 
8. U moet een laptop met RJ-45-kabel hebben om verbinding te maken met de lokale gebruikers interface en het apparaat te configureren. Gebruik de laptop om elk knoop punt van het apparaat één keer te configureren.
9. U hebt een 1 40-Gbps-kabel of een 10 Gbps-kabel per apparaatapparaat nodig.
    - Kies de kabels die compatibel zijn met de [MELLANOX MCX314A-BCCT-](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) netwerk interface.
    - Voor de 40-Gbps-kabel moet het apparaat uiteinde van de kabel QSFP + zijn.
    - Voor de 10-Gbps-kabel hebt u een SFP +-kabel nodig die aan een kant van een 10 Gbps-switch wordt aangesloten, met een QSFP + tot SFP +-adapter (of de QSA-adapter) voor het einde dat op het apparaat wordt aangesloten.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Uw apparaat bekabelen voor energie

Voer de volgende stappen uit om uw apparaat te bekabelen.

1. Controleer of er met het apparaat is geknoeid en of het is beschadigd. Als er met het apparaat is geknoeid of het ernstig is beschadigd, gaat u niet verder. [Neem direct contact op met Microsoft ondersteuning](data-box-disk-contact-microsoft-support.md) om u te helpen bepalen of het apparaat in goede staat werkt en of u een vervanging wilt verzenden.
2. Verplaats het apparaat naar de installatie site.

    ![Site voor installatie van Data Box Heavy apparaat](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Vergrendel de achterste Casters op het apparaat, zoals hieronder wordt weer gegeven.

    ![Vergrendelde apparaat Casters Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Zoek de knoppen die de voor-en achterkant van het apparaat ontgrendelen. Ontgrendel de voor deur en verplaats deze naar de zijkant van het apparaat. Herhaal dit ook met de achterdeur.
    Beide deuren moeten geopend blijven wanneer het apparaat operationeel is om een optimale front-to-back-lucht stroom via het apparaat mogelijk te maken.

    ![Open Data Box Heavy deuren](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. De lade aan de achterkant van het apparaat moet vier stroom kabels hebben. Verwijder alle kabels uit de lade en plaats deze opzij.

    ![Data Box Heavy stroom kabels in lade](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. De volgende stap is het identificeren van de verschillende poorten aan de achterkant van het apparaat. Er zijn twee apparaat knooppunten: **Knooppunt1** en **Knooppunt2**. Elk knoop punt heeft vier netwerk interfaces, beheer, **bestand1**, **bestand2**, **Data3**. Beheer wordt gebruikt om tijdens de eerste configuratie van het apparaat het management te configureren. Bestand1-**Data3** zijn gegevens poorten. Beheer-en **Data3** -poorten zijn 1 Gbps, terwijl **bestand1**, **bestand2** kan werken als 40-Gbps-poorten of 10-Gbps-poorten. Onder aan de twee knoop punten zijn vier energie-eenheden (PSUs) die worden gedeeld via de twee knoop punten van het apparaat. Wanneer u dit apparaat begezicht, zijn de **PSUs** **PSU1**, **PSU2**, **PSU3**en **PSU4** van links naar rechts.

    ![Data Box Heavy poorten](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Verbind alle vier stroom kabels met de voedings eenheden van het apparaat. De groene Led's worden in-en uitgeschakeld.
8. Gebruik de aan/uit-knoppen in het voorste vlak om de knoop punten van het apparaat in te scha kelen. Houd de knop aan/uit om een paar seconden ingedrukt te houden totdat de blauwe lichten zijn ingeschakeld. Alle groene Led's voor de voedings eenheden aan de achterkant van het apparaat moeten nu effen zijn. Het voor paneel van het apparaat bevat ook fout-Led's. Wanneer een fout LED brandt, duidt dit op een defecte PSU of een ventilator of een probleem met de schijf stations.  

    ![Data Box Heavy front OPS-paneel](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Kabel eerste knoop punt voor netwerk

Voer de volgende stappen uit op een van de knoop punten van het apparaat voor netwerk kabel.

1. Gebruik een CAT 6 RJ-45-netwerk kabel (blauwe kabel in de afbeelding) om de hostcomputer te verbinden met de 1-Gbps-beheer poort.
2. Gebruik een QSFP +-kabel (glas vezel of koper) om ten minste 1 40-Gbps-netwerk interface (voor keur voor 1 Gbps) verbinding te maken met gegevens. Als u een 10-Gbps-switch gebruikt, gebruikt u een SFP +-kabel met een QSFP +-naar-SFP +-adapter (de QSA-adapter) om verbinding te maken met de netwerk interface van 40 Gbps voor gegevens.

    ![Bekabelde poorten Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > De gegevens 1 en BESTAND2 worden overgeschakeld en komen niet overeen met wat er in de lokale webgebruikersinterface wordt weer gegeven.
    > De kabel adapter van 40 Gbps maakt verbinding wanneer deze is ingevoegd zoals hieronder wordt weer gegeven.

    ![Data Box Heavy 40-Gbps-kabel adapter](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Eerste knoop punt configureren

Voer de volgende stappen uit om uw apparaat in te stellen met behulp van de lokale configuratie en de Azure Portal.

1. Download de referenties van het apparaat vanuit de portal. Ga naar **Algemeen > Apparaatdetails**. Kopieer het **Apparaatwachtwoord**. Deze wacht woorden zijn gekoppeld aan een specifieke volg orde in de portal. Die overeenkomt met de twee knoop punten in Data Box Heavy, ziet u de twee serie nummers van apparaten. Het beheerders wachtwoord voor de knoop punten is hetzelfde.

    ![Referenties van Data Box Heavy apparaat](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Verbind uw client werkstation met het apparaat via een CAT6 RJ-45-netwerk kabel.
3. Configureer de Ethernet-adapter op de computer die u gebruikt om verbinding te maken met een apparaat met een `192.168.100.5` statisch IP `255.255.255.0`-adres van en subnet.

    ![Data Box Heavy maakt verbinding met de lokale web-UI](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Verbinding maken met de lokale web-UI van het apparaat op de volgende `http://192.168.100.10`URL:. Klik op **Geavanceerd** en klik vervolgens op **door gaan naar 192.168.100.10 (onveilig)** .
5. U ziet een pagina **Aanmelden** voor de lokale webgebruikersinterface.
    
    - Een van de serie nummers van knoop punten op deze pagina komt overeen met zowel de gebruikers interface van de portal als de lokale web-UI. Noteer het knooppunt nummer aan de toewijzing van het serie nummer. De portal bevat twee knoop punten en twee serie nummers van apparaten. Deze toewijzing helpt u te begrijpen welk knoop punt overeenkomt met het serie nummer.
    - Het apparaat is op dit moment vergrendeld.
    - Geef het beheerders wachtwoord voor het apparaat op dat u in de vorige stap hebt verkregen om u aan te melden bij het apparaat. Klik op **Aanmelden**.

    ![Aanmelden bij Data Box Heavy lokale web-UI](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Zorg ervoor dat de netwerk interfaces zijn geconfigureerd op het dash board. Er zijn vier netwerk interfaces op het knoop punt van het apparaat, twee 1 Gbps en 2 40 Gbps. Een van de 1-Gbps-interface is een beheer interface en kan daarom niet door de gebruiker worden geconfigureerd. De overige drie netwerk interfaces zijn toegewezen aan gegevens en kunnen door de gebruiker worden geconfigureerd.

- Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd.
- Als DHCP niet is ingeschakeld, gaat u naar netwerk interfaces instellen en wijst u statische IP-adressen toe, indien nodig.

    ![Knoop punt van Data Box Heavy dash board 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Tweede knoop punt configureren

Voer de stappen uit die worden beschreven in het [eerste knoop punt configureren](#configure-first-node) voor het tweede knoop punt van het apparaat.

![Dash board-knoop punt 2 Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Zodra de installatie van het apparaat is voltooid, kunt u verbinding maken met de apparaatshares en de gegevens van uw computer naar het apparaat kopiëren.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd over Azure Data Box Heavy-onderwerpen, zoals:

> [!div class="checklist"]
> * Uw Data Box Heavy bekabelen
> * Verbinding maken met uw Data Box Heavy

Ga naar de volgende zelf studie voor meer informatie over het kopiëren van gegevens op uw Data Box Heavy.

> [!div class="nextstepaction"]
> [Uw gegevens naar Azure Data Box kopiëren](./data-box-heavy-deploy-copy-data.md)

::: zone-end
