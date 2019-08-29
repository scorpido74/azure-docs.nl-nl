---
title: Bewaking en probleem oplossing van HANA-zijde op SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Bewaking en probleem oplossing van HANA-zijde op SAP HANA in een Azure (grote instanties).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2c596a876817f0a501025c37e463a7eebb55cf2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099824"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Bewaken en problemen oplossen vanaf de HANA-zijde

Om het effectief analyseren van problemen met betrekking tot SAP HANA op Azure (grote instanties), is het handig om de hoofd oorzaak van een probleem te beperken. SAP heeft een grote hoeveelheid documentatie gepubliceerd om u te helpen.

De toepasselijke Veelgestelde vragen met betrekking tot de SAP HANA prestaties vindt u in de volgende SAP-opmerkingen:

- [SAP-Opmerking #2222200 – Veelgestelde vragen: SAP HANA netwerk](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP-Opmerking #2100040 – Veelgestelde vragen: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP-Opmerking #199997 – Veelgestelde vragen: SAP HANA geheugen](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP-Opmerking #200000 – Veelgestelde vragen: Optimalisatie van SAP HANA prestaties](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP-Opmerking #199930 – Veelgestelde vragen: SAP HANA I/O Analysis](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP-Opmerking #2177064 – Veelgestelde vragen: Opnieuw opstarten en crashes van SAP HANA-service](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA waarschuwingen

Controleer als eerste stap de huidige SAP HANA waarschuwings Logboeken. Ga in SAP Hana Studio naar de **beheer console: Berichten Weer geven: alle**waarschuwingen. Op dit tabblad worden alle SAP HANA waarschuwingen weer gegeven voor specifieke waarden (vrij fysiek geheugen, CPU-gebruik, enzovoort) die buiten de ingestelde minimum-en maximum drempelwaarde vallen. Standaard worden controles automatisch elke 15 minuten vernieuwd.

![Ga in SAP HANA Studio naar de beheer console: Berichten Weer geven: alle waarschuwingen](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

Voor een waarschuwing die wordt geactiveerd door een onjuiste drempel waarde, moet een oplossing worden ingesteld op de standaard waarde of een redelijke drempelwaarde.

![Opnieuw instellen op de standaard waarde of een redelijke drempel waarde](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

De volgende waarschuwingen kunnen duiden op CPU-resource problemen:

- CPU-gebruik van host (waarschuwing 5)
- Meest recente opslag punt bewerking (waarschuwing 28)
- Duur van opslag punt (waarschuwing 54)

U kunt een hoog CPU-verbruik in uw SAP HANA-data base opmerken van een van de volgende:

- Waarschuwing 5 (host-CPU-gebruik) is geactiveerd voor het huidige of het vorige CPU-gebruik
- Het weer gegeven CPU-gebruik in het scherm overzicht

![Weer gegeven CPU-gebruik in het scherm overzicht](./media/troubleshooting-monitoring/image3-cpu-usage.png)

De laad grafiek kan een hoog CPU-verbruik of hoog verbruik in het verleden weer geven:

![De laad grafiek kan een hoog CPU-verbruik of hoog verbruik in het verleden weer geven](./media/troubleshooting-monitoring/image4-load-graph.png)

Een waarschuwing die wordt geactiveerd als gevolg van een hoog CPU-gebruik kan verschillende oorzaken hebben, waaronder, maar niet beperkt tot: uitvoering van bepaalde trans acties, het laden van gegevens, het uitvoeren van taken die niet reageren, langlopende SQL-instructies en slechte query prestaties (bijvoorbeeld met BW op HANA-kubussen).

Raadpleeg het [SAP Hana probleem oplossing: Aan CPU gerelateerde oorzaken en](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) oplossingen site voor gedetailleerde probleemoplossings stappen.

## <a name="operating-system"></a>Besturingssysteem

Een van de belangrijkste controles voor SAP HANA op Linux is om ervoor te zorgen dat transparante, enorme pagina's worden uitgeschakeld. Zie [SAP Note #2131662 – transparent enorme Pages (THP) op SAP Hana servers](https://launchpad.support.sap.com/#/notes/2131662)voor meer informatie.

- U kunt controleren of transparante enorme pagina's zijn ingeschakeld via de volgende Linux-opdracht: **kat\_/sys/kernel/mm/transparent hugepage/ingeschakeld**
- Als er _altijd_ tussen vier Kante haken staan zoals hieronder, betekent dit dat de transparante enorme pagina's zijn ingeschakeld: [always] madvise nooit; Als er _nooit_ tussen vier Kante haken staan zoals hieronder, betekent dit dat de transparante enorme pagina's zijn uitgeschakeld: altijd madvise [nooit]

De volgende Linux-opdracht zou niets kunnen retour neren: **rpm-QA | grep-ulimit.** Als wordt weer gegeven dat _ulimit_ is geïnstalleerd, verwijdert u dit onmiddellijk.

## <a name="memory"></a>Geheugen

U kunt zien dat de hoeveelheid geheugen die is toegewezen door de SAP HANA data base hoger is dan verwacht. De volgende waarschuwingen wijzen op problemen met een hoog geheugen gebruik:

- Fysiek geheugen gebruik van host (waarschuwing 1)
- Geheugen gebruik van naam server (waarschuwing 12)
- Totaal geheugen gebruik van Column Store-tabellen (waarschuwing 40)
- Geheugen gebruik van Services (waarschuwing 43)
- Geheugen gebruik van de hoofd opslag van Column Store-tabellen (waarschuwing 45)
- Runtime dump bestanden (waarschuwing 46)

Raadpleeg het [SAP Hana probleem oplossing: Site geheugen](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) problemen voor gedetailleerde stappen voor probleem oplossing.

## <a name="network"></a>Netwerk

Raadpleeg [SAP note #2081065: problemen met SAP Hana netwerk oplossen](https://launchpad.support.sap.com/#/notes/2081065) en de stappen voor het oplossen van problemen met het netwerk uitvoeren in deze SAP-notitie.

1. De round-trip tijd tussen de server en de client analyseren.
  A. Voer het SQL- [_script\_Hana\__ ](https://launchpad.support.sap.com/#/notes/1969700)-netwerkclients uit _._
  
2. Analyseer communicatie tussen knoop punten.
  A. Voer SQL script [_Hana\_-\_netwerk services_](https://launchpad.support.sap.com/#/notes/1969700)uit _._

3. Voer Linux-opdracht **ifconfig** uit (de uitvoer geeft aan of er pakket verlies optreedt).
4. Voer Linux-opdracht **tcpdump**uit.

U kunt ook het open source [IPERF](https://iperf.fr/) -hulp programma (of vergelijkbaar) gebruiken om de prestaties van het werkelijke toepassings netwerk te meten.

Raadpleeg het [SAP Hana probleem oplossing: Netwerk prestaties en connectiviteits](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) problemen site voor gedetailleerde probleemoplossings stappen.

## <a name="storage"></a>Storage

Vanuit het oogpunt van eind gebruikers loopt een toepassing (of het systeem als geheel) traag, reageert niet of kan zelfs reageren als er problemen zijn met de I/O-prestaties. Op het tabblad **volumes** in SAP Hana Studio ziet u de bijgevoegde volumes en welke volumes door elke service worden gebruikt.

![Op het tabblad volumes in SAP HANA Studio ziet u de bijgevoegde volumes en welke volumes door elke service worden gebruikt.](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Bijgevoegde volumes in het onderste gedeelte van het scherm kunt u de details van de volumes, zoals bestanden en I/O-statistieken, bekijken.

![Bijgevoegde volumes in het onderste gedeelte van het scherm kunt u de details van de volumes, zoals bestanden en I/O-statistieken, bekijken](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Raadpleeg het [SAP Hana probleem oplossing: I/O-gerelateerde hoofd oorzaken en](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) oplossingen [en SAP Hana probleem oplossing: Schijf gerelateerde hoofd oorzaken en oplossingen](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) site voor gedetailleerde probleemoplossings stappen.

## <a name="diagnostic-tools"></a>Diagnostische Hulpprogram Ma's

Voer een SAP Hana Health Check uit via\_de\_Hana-configuratie Minichecks. Met dit hulp programma worden mogelijk kritieke technische problemen geretourneerd die al zouden worden gegenereerd als waarschuwingen in SAP HANA Studio.

Raadpleeg [SAP Note #1969700-verzameling van SQL-instructies voor SAP Hana](https://launchpad.support.sap.com/#/notes/1969700) en down load het zip-bestand van SQL statements dat aan deze opmerking is gekoppeld. Sla dit zip-bestand op de lokale harde schijf op.

Klik in SAP HANA Studio met de rechter muisknop in de kolom **naam** op het tabblad **systeem informatie** en selecteer **SQL-instructies importeren**.

![Klik in SAP HANA Studio met de rechter muisknop in de kolom naam op het tabblad systeem informatie en selecteer SQL-instructies importeren](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Selecteer de SQL-instructies. zip-bestand lokaal opgeslagen en er wordt een map met de bijbehorende SQL-instructies geïmporteerd. Op dit punt kunnen de vele verschillende diagnostische controles worden uitgevoerd met deze SQL-instructies.

Als u bijvoorbeeld de bandbreedte vereisten voor SAP Hana systeem replicatie wilt testen, klikt u met de rechter **muisknop op de **band breedte** -instructie onder replicatie: Band** breedte en selecteer **openen** in SQL-console.

De volledige SQL-instructie opent het toestaan van invoer parameters (sectie wijzigen) om deze te wijzigen en vervolgens uit te voeren.

![De volledige SQL-instructie opent het toestaan van invoer parameters (sectie wijzigen) die moeten worden gewijzigd en vervolgens uitgevoerd](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Een ander voor beeld is het klikken met de rechter **muisknop op de instructies onder replicatie: Overzicht**. Selecteer **uitvoeren** in het context menu:

![Een ander voor beeld is het klikken met de rechter muisknop op de instructies onder replicatie: Krijgt. Selecteer uitvoeren in het context menu](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Dit resulteert in informatie die helpt bij het oplossen van problemen:

![Dit leidt tot informatie die helpt bij het oplossen van problemen](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Doe hetzelfde voor de Hana\_-\_configuratie Minichecks en controleer of er _X_ -tekens in de kolom _C_ (kritiek) staan.

Voorbeeld uitvoer:

**Hana\_-configuratie\_MiniChecks\_Rev 102.01 + 1** voor algemene SAP Hana controles.

![Hana\_-\_configuratieMiniChecks\_Rev 102.01 + 1 voor algemene SAP Hana controles](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**Overzicht\_van\_Hana-Services** voor een overzicht van de SAP Hana services die momenteel worden uitgevoerd.

![Overzicht\_van\_Hana-Services voor een overzicht van de SAP Hana services die momenteel worden uitgevoerd](./media/troubleshooting-monitoring/image12-services-overview.png)

**Hana\_-Services\_statistieken** voor SAP Hana service-informatie (CPU, geheugen, enzovoort).

![Statistieken\_voor\_Hana-Services voor informatie over SAP Hana-service](./media/troubleshooting-monitoring/image13-services-statistics.png)

**Hana\_-configuratie\_Overview\_Rev110 +** voor algemene informatie over het SAP Hana-exemplaar.

![Hana\_-\_configuratieOverview\_Rev110 + voor algemene informatie over het SAP Hana-exemplaar](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**Hana\_-configuratie\_parameters\_Rev70 +** om SAP Hana parameters te controleren.

![Hana\_-\_configuratieparameters\_Rev70 + om SAP Hana parameters te controleren](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Volgende stappen**

- Raadpleeg [de maximale Beschik baarheid die in SuSE is ingesteld met behulp van de STONITH](ha-setup-with-stonith.md).