---
title: Zelfstudie voor het instellen van de Azure Data Box Heavy | Microsoft Docs
description: Leer hoe u de Azure Data Box Heavy bekabelt en aansluit
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dbf3531f84d218543b0ab1fb2199a66bdbde2926
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79117252"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Zelfstudie: De Azure Data Box Heavy bekabelen en aansluiten

::: zone-end

::: zone target = "chromeless"

# <a name="get-started-with-azure-data-box-heavy"></a>Aan de slag met de Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

In deze zelfstudie wordt beschreven hoe u de Azure Data Box Heavy bekabelt, aansluit en inschakelt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De Data Box Heavy bekabelen
> * Verbinding maken met uw Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Deze handleiding bevat instructies voor het controleren van vereisten, het bekabelen en aansluiten van uw apparaat, het kopiëren van gegevens, het uploaden naar Azure en het controleren van de geüploade gegevens.

::: zone-end

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de zelfstudie [ Azure Data Box Heavy bestellen](data-box-heavy-deploy-ordered.md).
2. U hebt de Data Box Heavy ontvangen en de orderstatus in de portal is **Geleverd**.
3. U hebt de [veiligheidsrichtlijnen voor de Data Box Heavy](data-box-safety.md) bekeken.
4. U moet toegang hebben tot een vlakke opstellingsplaats in het datacenter met een netwerkverbinding in de buurt die een apparaat met deze footprint kan ondersteunen. Dit apparaat kan niet in een rek worden opgesteld.
5. U hebt vier geaarde stroomkabels ontvangen voor gebruik met uw opslagapparaat.
6. Er is een hostcomputer verbonden met het datacenternetwerk. Data Box Heavy kopieert de gegevens vanaf deze computer. Op de hostcomputer moet een [ondersteund besturingssysteem](data-box-heavy-system-requirements.md) worden uitgevoerd.
7. Uw datacenter moet een netwerk met hoge snelheid hebben. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. 
8. U hebt een laptop met een RJ-45-kabel nodig om verbinding te maken met de lokale gebruikersinterface en het apparaat te configureren. Gebruik de laptop om elk knooppunt van het apparaat eenmalig te configureren.
9. U hebt per apparaatknooppunt één kabel van 40 Gbps of 10 Gbps nodig.
    - Kies kabels die compatibel zijn met de [Mellanox MCX314A- BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)-netwerkinterface.
    - Voor de kabel van 40 Gbps moet het apparaatuiteinde van de kabel QSFP+ zijn.
    - Voor de kabel van 10 Gbps hebt u een SFP+-kabel nodig die op één kant van een 10 Gbps-switch wordt aangesloten, met een QSFP+-naar-SFP+-adapter (of de QSA-adapter) voor het uiteinde dat op het apparaat wordt aangesloten.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Uw apparaat bekabelen voor stroom

Voer de volgende stappen uit om uw apparaat te bekabelen.

1. Controleer of er met het apparaat is geknoeid en of het is beschadigd. Als er met het apparaat is geknoeid of het ernstig is beschadigd, gaat u niet verder. [Neem direct contact op met Microsoft Ondersteuning](data-box-disk-contact-microsoft-support.md) om te bepalen of het apparaat in een goede staat is of dat er een vervangend apparaat moet worden verzonden.
2. Verplaats het apparaat naar plaats waar deze moet worden geïnstalleerd.

    ![Installatieplaats voor Data Box Heavy-apparaat](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Vergrendel de achterste zwenkwieltjes op het apparaat, zoals hieronder wordt weergegeven.

    ![Vergrendelde zwenkwieltjes op het Data Box Heavy-apparaat](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Zoek de knoppen waarmee de voor- en achterzijde van het apparaat worden ontgrendeld. Ontgrendel het voorpaneel en verplaats deze tot deze met de zijkant van het apparaat is uitgelijnd. Herhaal dit voor het achterpaneel.
    Beide panelen moeten open blijven als het apparaat in bedrijf is om een optimale luchtstroom van de voor- naar de achterzijde door het apparaat mogelijk te maken.

    ![Data Box Heavy met geopende panelen](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. De lade aan de achterkant van het apparaat moet vier stroomkabels hebben. Verwijder alle kabels uit de lade en leg ze aan de kant.

    ![Stroomkabels van de Data Box Heavy in lade](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. De volgende stap bestaat uit het identificeren van de verschillende poorten aan de achterzijde van het apparaat. Er zijn twee apparaatknooppunten: **NODE1** en **NODE2**. Elk knooppunt heeft vier netwerkinterfaces: **MGMT**, **DATA1**, **DATA2** en **DATA3**. **MGMT** wordt gebruikt om tijdens de eerste configuratie van het apparaat het beheer te configureren. **DATA1**-**DATA3** zijn gegevenspoorten. De poorten **MGMT** en **DATA3** zijn 1 Gbps, terwijl **DATA1**, **DATA2** als 40-Gbps- of 10-Gbps-poorten dienst kunnen doen. Onder aan de twee knooppunten bevinden zich vier voedingen (PSU's), die over de twee apparaatknooppunten worden verdeeld. Vanaf de voorzijde van het apparaat bekeken, bestaan de **PSU's** van links naar rechts uit **PSU1**, **PSU2**, **PSU3** en **PSU4**.

    ![Data Box Heavy-poorten](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Sluit de vier stroomkabels aan op de voeding van het apparaat. De groene leds beginnen groen te knipperen.
8. Gebruik de aan/uit-knoppen aan de voorzijde om de apparaatknooppunten in te schakelen. Houd de aan/uit-knop enkele seconden ingedrukt totdat de blauwe lampjes worden ingeschakeld. Alle groene leds voor de voedingen aan de achterzijde van het apparaat moeten nu continu branden. Het voorste bedieningspaneel van het apparaat bevat ook storingsleds. Wanneer een storingsled brandt, duidt dit op een defecte PSU of ventilator of een probleem met de schijfstations.  

    ![Voorste bedieningspaneel van de Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Eerste knooppunt bekabelen voor netwerk

Voer de volgende stappen uit met een van de knooppunten van het apparaat om het netwerk te bekabelen.

1. Gebruik een CAT 6 RJ-45-netwerkkabel (rechtsboven in de afbeelding, gekoppeld aan een plug met label MGMT) om de hostcomputer te verbinden met de 1-Gbps-beheerpoort.
2. Gebruik een QSFP+-kabel (glasvezel of koper) om ten minste één 40 Gbps-netwerkinterfaceverbinding (verdient de voorkeur boven 1 Gbps) voor gegevens te maken. Als u een 10 Gbps-switch gebruikt, gebruikt u een SFP+-kabel met een QSFP+-naar-SFP+-adapter (de QSA-adapter) om verbinding te maken met de 40 Gbps-netwerkinterface voor gegevens.

    ![Bekabelde Data Box Heavy-poorten](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA1 en DATA2 worden verwisseld en komen niet overeen met wat er in de lokale gebruikersinterface wordt weergegeven.
    > De 40 Gbps-kabeladapter maakt verbinding als deze wordt aangesloten zoals hieronder is weergegeven.

    ![40 Gbps-Data Box Heavy-kabeladapter](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Eerste knooppunt configureren

Voer de volgende stappen uit om uw apparaat in te stellen met behulp van de lokale configuratie en de Azure-portal.

1. Download de referenties van het apparaat vanuit de portal. Ga naar **Algemeen > Apparaatdetails**. Kopieer het **Apparaatwachtwoord**. Deze wachtwoorden zijn aan een specifieke volgorde in de portal onderhevig. In de Data Box Heavy ziet u de twee apparaatserienummers die met de twee knooppunten overeenkomen. Het beheerderswachtwoord voor het apparaat is voor beide knooppunten hetzelfde.

    ![Referenties van het Data Box Heavy-apparaat](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Verbind uw clientwerkstation met het apparaat via een CAT6 RJ-45-netwerkkabel.
3. Configureer de Ethernet-adapter op de computer waarmee u verbinding maakt met het apparaat met statisch IP-adres `192.168.100.5` en subnet `255.255.255.0`.

    ![De Data Box Heavy maakt verbinding met de lokale gebruikersinterface](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Maak verbinding met de lokale gebruikersinterface van het apparaat via de volgende URL: `http://192.168.100.10`. Klik op **Geavanceerd** en vervolgens op **Doorgaan naar 192.168.100.10 (onveilig)** .
5. U ziet een pagina **Aanmelden** voor de lokale webgebruikersinterface.
    
    - Een van de serienummers van het knooppunt is dezelfde in zowel de gebruikersinterface van de portal als de lokale gebruikersinterface. Noteer het knooppuntnummer dat overeenkomt met het serienummer. In de portal bevinden zich twee knooppunten en twee apparaatserienummers. Aan de hand van deze overeenkomst weet u welk knooppunt bij welk serienummer behoort.
    - Het apparaat is op dit moment vergrendeld.
    - Geef het beheerderswachtwoord voor het apparaat op dat u in de vorige stap hebt verkregen om u bij het apparaat aan te melden. Klik op **Aanmelden**.

    ![Aanmelden bij de lokale gebruikersinterface van de Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Zorg er voor dat de netwerkinterfaces op het dashboard zijn geconfigureerd. Er bevinden zich vier netwerkinterfaces op het apparaatknooppunt, twee van 1 Gbps en twee van 40 Gbps. Een van de 1 Gbps-interfaces is een beheerinterface en kan dus niet door de gebruiker worden geconfigureerd. De overige drie netwerkinterfaces zijn voor gegevens bedoeld en kunnen door de gebruiker worden geconfigureerd.

- Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd.
- Als DHCP niet is ingeschakeld, gaat u naar Netwerkinterfaces instellen en wijst u zo nodig statische IP-adressen toe.

    ![Knooppunt 1 op Data Box Heavy-dashboard](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Tweede knooppunt configureren

Voer de stappen uit die worden beschreven in [Eerste knooppunt configureren](#configure-first-node) voor het tweede knooppunt van het apparaat.

![Knooppunt 2 op Data Box Heavy-dashboard](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Zodra de installatie van het apparaat is voltooid, kunt u verbinding maken met de apparaatshares en de gegevens van uw computer naar het apparaat kopiëren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn verschillende onderwerpen besproken over de Azure Data Box Heavy, zoals:

> [!div class="checklist"]
> * De Data Box Heavy bekabelen
> * Verbinding maken met uw Data Box Heavy

Ga naar de volgende zelfstudie om te lezen hoe u gegevens kopieert naar uw Data Box Heavy-schijf.

> [!div class="nextstepaction"]
> [Uw gegevens naar Azure Data Box kopiëren](./data-box-heavy-deploy-copy-data.md)

::: zone-end
