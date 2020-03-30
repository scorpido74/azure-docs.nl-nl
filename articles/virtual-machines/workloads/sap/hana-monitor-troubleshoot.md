---
title: Monitoring en probleemoplossing van HANA-kant op SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Monitoring en probleemoplossing van HANA-kant op SAP HANA op een Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 047ea4d07f2b497ac8c7deb90c056d63976094f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617081"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Bewaken en problemen oplossen vanaf de HANA-zijde

Om problemen met betrekking tot SAP HANA op Azure (Large Instances) effectief te analyseren, is het handig om de hoofdoorzaak van een probleem te beperken. SAP heeft een grote hoeveelheid documentatie gepubliceerd om u te helpen.

Toepasselijke veelgestelde vragen met betrekking tot SAP HANA-prestaties zijn te vinden in de volgende SAP Notes:

- [SAP Note #2222200 – VEELGESTELDE VRAGEN: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Note #2100040 – VEELgestelde vragen: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Note #199997 – VEELgestelde vragen: SAP HANA-geheugen](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – VEELgestelde vragen: SAP HANA Performance Optimization](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Note #199930 – VEELGESTELDE VRAGEN: SAP HANA I/O-analyse](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 – VEELgestelde vragen: SAP HANA-service herstart en crasht](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA-waarschuwingen

Controleer als eerste stap de huidige SAP HANA-waarschuwingslogboeken. Ga in SAP HANA Studio naar **Administration Console: Alerts: Show: all alerts**. Op dit tabblad worden alle SAP HANA-waarschuwingen weergegeven voor specifieke waarden (gratis fysiek geheugen, CPU-gebruik, enz.) die buiten de ingestelde minimum- en maximumdrempels vallen. Standaard worden controles elke 15 minuten automatisch vernieuwd.

![Ga in SAP HANA Studio naar Administration Console: Waarschuwingen: Toon: alle waarschuwingen](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

Voor een waarschuwing die wordt geactiveerd vanwege onjuiste drempelinstelling, moet een oplossing worden gereset naar de standaardwaarde of een redelijkere drempelwaarde.

![De standaardwaarde of een redelijkere drempelwaarde opnieuw instellen](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

De volgende waarschuwingen kunnen wijzen op CPU-bronproblemen:

- CPU-gebruik host (Waarschuwing 5)
- Meest recente savepoint-bewerking (Waarschuwing 28)
- Duur van savepoint (Waarschuwing 54)

U een hoog CPU-verbruik op uw SAP HANA-database opmerken uit een van de volgende opties:

- Alert 5 (Host CPU-gebruik) wordt verhoogd voor huidig of afgelopen CPU-gebruik
- Het weergegeven CPU-gebruik op het overzichtsscherm

![Weergegeven CPU-gebruik op het overzichtsscherm](./media/troubleshooting-monitoring/image3-cpu-usage.png)

De grafiek Laden kan een hoog CPU-verbruik of een hoog verbruik in het verleden weergeven:

![De grafiek Laden kan een hoog CPU-verbruik of een hoog verbruik in het verleden weergeven](./media/troubleshooting-monitoring/image4-load-graph.png)

Een waarschuwing die wordt geactiveerd als gevolg van een hoog CPU-gebruik kan worden veroorzaakt door verschillende redenen, waaronder, maar niet beperkt tot: uitvoering van bepaalde transacties, het laden van gegevens, taken die niet reageren, langlopende SQL-instructies en slechte queryprestaties (bijvoorbeeld met BW op HANA kubussen).

Raadpleeg de [SAP HANA Troubleshooting: CPU Related Causes and Solutions-site](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) voor gedetailleerde stappen voor het oplossen van problemen.

## <a name="operating-system"></a>Besturingssysteem

Een van de belangrijkste controles voor SAP HANA op Linux is om ervoor te zorgen dat Transparante Grote Pagina's worden uitgeschakeld, zie [SAP Note #2131662 – Transparent Huge Pages (THP) op SAP HANA Servers](https://launchpad.support.sap.com/#/notes/2131662).

- U controleren of transparante enorme pagina's zijn ingeschakeld via de volgende Linux-opdracht: **cat /sys/kernel/mm/transparent\_hugepage/enabled**
- Als _altijd_ is ingesloten tussen haakjes zoals hieronder, betekent dit dat de transparante enorme pagina's zijn ingeschakeld: [altijd] madvise nooit; als _nooit_ is ingesloten tussen haakjes zoals hieronder, betekent dit dat de transparante grote pagina's zijn uitgeschakeld: altijd madvise [nooit]

De volgende Linux opdracht moet niets **retourneren: rpm -qa | grep ulimit.** Als blijkt _dat Ulimit_ is geïnstalleerd, verwijdert u deze onmiddellijk.

## <a name="memory"></a>Geheugen

U vaststellen dat de hoeveelheid geheugen die door de SAP HANA-database wordt toegewezen hoger is dan verwacht. De volgende waarschuwingen geven problemen met een hoog geheugengebruik aan:

- Fysiek geheugengebruik hosten (Waarschuwing 1)
- Geheugengebruik van naamserver (Waarschuwing 12)
- Totaal geheugengebruik van Kolomarchieftabellen (Waarschuwing 40)
- Geheugengebruik van services (Alert 43)
- Geheugengebruik van de hoofdopslag van kolomarchieftabellen (Waarschuwing 45)
- Runtime-dumpbestanden (Waarschuwing 46)

Raadpleeg de [SAP HANA Troubleshooting: Memory Problems-site](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) voor gedetailleerde stappen voor het oplossen van problemen.

## <a name="network"></a>Netwerk

Raadpleeg [SAP Note #2081065 – Sap HANA-netwerk oplossen en](https://launchpad.support.sap.com/#/notes/2081065) voer de stappen voor het oplossen van problemen in het netwerk uit in deze SAP Note.

1. Het analyseren van de retourtijd tussen server en client.
  A. Voer het SQL-script [_\_\_HANA-netwerkclients uit_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analyseer internode communicatie.
  A. Sql-script [_\_HANA\_Network Services_](https://launchpad.support.sap.com/#/notes/1969700)uitvoeren _._

3. Voer **Linux-opdrachtifconfig uit** (de uitvoer geeft aan of er pakketverliezen optreden).
4. Run Linux opdracht **tcpdump**.

Gebruik ook de open source [IPERF-tool](https://iperf.fr/) (of iets dergelijks) om de prestaties van het echte toepassingsnetwerk te meten.

Raadpleeg de site [SAP HANA Troubleshooting: Networking Performance and Connectivity Problems](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) voor gedetailleerde stappen voor het oplossen van problemen.

## <a name="storage"></a>Storage

Vanuit het oogpunt van de eindgebruiker, een applicatie (of het systeem als geheel) draait traag, reageert niet, of kan zelfs lijken te stoppen met reageren als er problemen zijn met I / O prestaties. Op het tabblad **Volumes** in SAP HANA Studio ziet u de bijgevoegde volumes en welke volumes door elke service worden gebruikt.

![Op het tabblad Volumes in SAP HANA Studio ziet u de bijgevoegde volumes en welke volumes door elke service worden gebruikt](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Bijgevoegde volumes in het onderste deel van het scherm u details van de volumes, zoals bestanden en I / O-statistieken.

![Bijgevoegde volumes in het onderste deel van het scherm u details van de volumes zien, zoals bestanden en I/O-statistieken](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Raadpleeg de [SAP HANA Troubleshooting: I/O Related Root Causes and Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) en SAP [HANA Troubleshooting: Disk Related Root Causes and Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) site voor gedetailleerde stappen voor het oplossen van problemen.

## <a name="diagnostic-tools"></a>Diagnostische hulpprogramma's

Voer een SAP HANA Health\_\_Check uit via HANA Configuration Minichecks. Deze tool retourneert potentieel kritieke technische problemen die al als waarschuwingen in SAP HANA Studio hadden moeten worden aangekaart.

Raadpleeg [SAP Note #1969700 – SQL-instructieverzameling voor SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) en download het SQL Statements.zip-bestand dat aan die notitie is gekoppeld. Sla dit zip-bestand op de lokale harde schijf op.

Klik in SAP HANA Studio op het tabblad **Systeemgegevens** met de rechtermuisknop in de kolom **Naam** en selecteer **SQL-instructies importeren**.

![Klik in SAP HANA Studio op het tabblad Systeemgegevens met de rechtermuisknop in de kolom Naam en selecteer SQL-instructies importeren](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Selecteer het SQL Statements.zip-bestand dat lokaal is opgeslagen en er wordt een map met de bijbehorende SQL-instructies geïmporteerd. Op dit punt kunnen de vele verschillende diagnostische controles worden uitgevoerd met deze SQL-instructies.

Als u bijvoorbeeld de bandbreedtevereisten voor SAP HANA-systeemreplicatie wilt testen, klikt u met de rechtermuisknop op de **bandbreedteinstructie** onder **Replicatie: Bandbreedte** en selecteert u **Openen** in SQL-console.

De volledige SQL-instructie wordt geopend, zodat invoerparameters (wijzigingssectie) kunnen worden gewijzigd en vervolgens kunnen worden uitgevoerd.

![De volledige SQL-instructie wordt geopend, zodat invoerparameters (wijzigingssectie) kunnen worden gewijzigd en vervolgens kunnen worden uitgevoerd](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Een ander voorbeeld is met de rechtermuisknop op de instructies onder **Replicatie: Overzicht**. Selecteer **Uitvoeren** in het contextmenu:

![Een ander voorbeeld is met de rechtermuisknop op de instructies onder Replicatie: Overzicht. Uitvoeren selecteren in het contextmenu](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Dit resulteert in informatie die helpt bij het oplossen van problemen:

![Dit zal resulteren in informatie die zal helpen bij het oplossen van problemen](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Doe hetzelfde voor\_\_HANA Configuration Minichecks en controleer op _X-markeringen_ in de kolom _C_ (Critical).

Voorbeelduitgangen:

**HANA\_\_Configuration\_MiniChecks Rev102.01+1** voor algemene SAP HANA-controles.

![HANA\_\_Configuration\_MiniChecks Rev102.01+1 voor algemene SAP HANA-controles](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_\_Services Overzicht** voor een overzicht van wat SAP HANA-services momenteel draaien.

![HANA\_\_Services Overzicht voor een overzicht van welke SAP HANA-services momenteel actief zijn](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_\_Services Statistieken** voor SAP HANA service informatie (CPU, geheugen, enz.).

![HANA\_\_Services Statistieken voor SAP HANA service informatie](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_\_Configuratieoverzicht\_Rev110+** voor algemene informatie over het SAP HANA-exemplaar.

![HANA\_\_Configuratieoverzicht\_Rev110+ voor algemene informatie over het SAP HANA-exemplaar](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_\_Configuration\_Parameters Rev70+** om SAP HANA-parameters te controleren.

![HANA\_\_Configuration\_Parameters Rev70+ om SAP HANA-parameters te controleren](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Volgende stappen**

- Doorverwijzen [Hoge beschikbaarheid die in SUSE is ingesteld met behulp van de STONITH.](ha-setup-with-stonith.md)