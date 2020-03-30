---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 88b19257a6a7d335e6a928a9eaf7526fbfd5b02e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75942767"
---
## <a name="application-performance-indicators"></a>Prestatie-indicatoren voor toepassingen

We beoordelen of een toepassing goed presteert of niet met behulp van prestatie-indicatoren zoals, hoe snel een toepassing een gebruikersverzoek verwerkt, hoeveel gegevens een toepassing per aanvraag verwerkt, hoeveel aanvragen een toepassing verwerken in een specifieke periode, hoe lang een gebruiker moet wachten om een antwoord te krijgen na het indienen van hun verzoek. De technische termen voor deze prestatie-indicatoren zijn IOPS, Doorvoer of Bandbreedte en Latentie.

In deze sectie bespreken we de gemeenschappelijke prestatie-indicatoren in het kader van Premium Storage. In de volgende sectie, Gathering Application Requirements, leert u hoe u deze prestatie-indicatoren voor uw toepassing meten. Later in Het optimaliseren van de toepassingsprestaties leert u over de factoren die van invloed zijn op deze prestatie-indicatoren en aanbevelingen om deze te optimaliseren.

## <a name="iops"></a>IOPS

IOPS, of Input/output Operations Per Second, is het aantal aanvragen dat uw toepassing binnen een seconde naar de opslagschijven stuurt. Een invoer/uitvoerbewerking kan worden gelezen of geschreven, sequentieel of willekeurig. Online Transaction Processing (OLTP) toepassingen zoals een online retail website moeten veel gelijktijdige verzoeken van gebruikers onmiddellijk verwerken. De gebruikersverzoeken zijn het invoegen en bijwerken van intensieve databasetransacties, die de toepassing snel moet verwerken. Daarom vereisen OLTP-toepassingen een zeer hoge IOPS. Dergelijke toepassingen verwerken miljoenen kleine en willekeurige IO-aanvragen. Als u een dergelijke toepassing hebt, moet u de toepassingsinfrastructuur ontwerpen om te optimaliseren voor IOPS. In de latere sectie, *Optimizing Application Performance,* bespreken we in detail alle factoren die u moet overwegen om een hoge IOPS te krijgen.

Wanneer u een premium opslagschijf koppelt aan uw schaalbare VM, biedt Azure u een gegarandeerd aantal IOPS op basis van de schijfspecificatie. Een P50-schijf is bijvoorbeeld goed voor 7500 IOPS. Voor elke schaalbare VM geldt ook een specifieke IOPS-limiet, afhankelijk van de grootte van de VM. Een standaard GS5 VM heeft bijvoorbeeld een IOPS-limiet van 80.000 IOPS.

## <a name="throughput"></a>Doorvoer

Doorvoer of bandbreedte is de hoeveelheid gegevens die uw toepassing in een bepaald interval naar de opslagschijven verzendt. Als uw toepassing invoer-/uitvoerbewerkingen uitvoert met grote IO-eenheidsgroottes, vereist dit een hoge doorvoer. Data warehouse toepassingen hebben de neiging om scan intensieve bewerkingen die toegang tot grote delen van gegevens op een moment en vaak bulk bewerkingen uit te voeren. Met andere woorden, dergelijke toepassingen vereisen een hogere doorvoer. Als u een dergelijke toepassing hebt, moet u de infrastructuur ontwerpen om te optimaliseren voor doorvoer. In het volgende gedeelte bespreken we in detail de factoren die u moet afstemmen om dit te bereiken.

Wanneer u een premium opslagschijf aan een vm op grote schaal koppelt, voorziet Azure in doorvoer volgens die schijfspecificatie. Een P50-schijf biedt bijvoorbeeld een schijfdoorvoer van250 MB per seconde. Voor elke schaalbare VM geldt ook een specifieke doorvoerlimiet, afhankelijk van de grootte van de VM. Een standaard GS5 VM heeft bijvoorbeeld een maximale doorvoer van 2000 MB per seconde.

Er is een relatie tussen doorvoer en IOPS, zoals weergegeven in de onderstaande formule.

![Relatie van IOPS en doorvoer](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

Daarom is het belangrijk om de optimale doorvoer- en IOPS-waarden te bepalen die uw toepassing vereist. Als je probeert om een te optimaliseren, de andere wordt ook beïnvloed. In een latere sectie, *Optimizing Application Performance,* zullen we bespreken in meer details over het optimaliseren van IOPS en Doorvoer.

## <a name="latency"></a>Latentie

Latentie is de tijd die een toepassing nodig heeft om één aanvraag te ontvangen, deze naar de opslagschijven te sturen en het antwoord naar de client te verzenden. Dit is een kritische maatstaf voor de prestaties van een toepassing naast IOPS en Doorvoer. De latentie van een premium opslagschijf is de tijd die nodig is om de informatie voor een aanvraag op te halen en terug te sturen naar uw toepassing. Premium Storage biedt consistente lage latencies. Premium schijven zijn ontworpen om enkelcijferige milliseconde latencies voor de meeste IO-bewerkingen te bieden. Als u ReadOnly-hostcaching inschakelt op premium opslagschijven, u een veel lagere leeslatentie krijgen. We zullen Disk Caching in meer detail bespreken in een later gedeelte over *het optimaliseren van de toepassingsprestaties.*

Wanneer u uw toepassing optimaliseert om hogere IOPS en doorvoer te krijgen, heeft dit invloed op de latentie van uw toepassing. Na het afstemmen van de toepassingsprestaties evalueert u altijd de latentie van de toepassing om onverwacht gedrag met hoge latentie te voorkomen.

De volgende besturingsvlakbewerkingen op beheerde schijven kunnen betrekking hebben op verplaatsing van de schijf van de ene opslaglocatie naar de andere. Dit wordt georkestreerd via een achtergrondkopie van gegevens die enkele uren in beslag kunnen nemen, meestal minder dan 24 uur, afhankelijk van de hoeveelheid gegevens in de schijven. Gedurende die tijd kan uw toepassing ervaring hoger dan de gebruikelijke leeslatentie als sommige leest kan krijgen doorgestuurd naar de oorspronkelijke locatie en kan langer duren om te voltooien. Er is geen invloed op de schrijflatentie tijdens deze periode.

- Werk het opslagtype bij.
- Maak een schijf los en bevestig deze van de ene VM aan de andere.
- Maak een beheerde schijf vanaf een VHD.
- Maak een beheerde schijf op basis van een momentopname.
- Onbeheerde schijven converteren naar beheerde schijven.

## <a name="performance-application-checklist-for-disks"></a>Checklist voor prestatietoepassingen voor schijven

De eerste stap in het ontwerpen van krachtige toepassingen die worden uitgevoerd op Azure Premium Storage is inzicht in de prestatievereisten van uw toepassing. Nadat u prestatievereisten hebt verzameld, u uw toepassing optimaliseren om de meest optimale prestaties te bereiken.

In het vorige deel legden we de gemeenschappelijke prestatie-indicatoren, IOPS, Doorvoer en Latentie uit. U moet bepalen welke van deze prestatie-indicatoren van cruciaal belang zijn voor uw toepassing om de gewenste gebruikerservaring te bieden. Hoge IOPS is bijvoorbeeld het belangrijkst voor OLTP-toepassingen die miljoenen transacties in een seconde verwerken. Terwijl hoge doorvoer van cruciaal belang is voor Data Warehouse-toepassingen die grote hoeveelheden gegevens in een seconde verwerken. Extreem lage latentie is cruciaal voor real-time toepassingen zoals live video streaming websites.

Meet vervolgens de maximale prestatie-eisen van uw toepassing gedurende de gehele levensduur. Gebruik de onderstaande voorbeeldchecklist als start. Noteer de maximale prestatievereisten tijdens normale werkbelastingsperioden buiten kantooruren. Door vereisten voor alle workloadsniveaus te identificeren, u de algehele prestatievereiste van uw toepassing bepalen. Bijvoorbeeld, de normale werklast van een e-commerce website zal de transacties die het dient tijdens de meeste dagen in een jaar. De piekwerklast van de website zal de transacties die zij dient tijdens de feestdagen of speciale verkoop evenementen. De piekwerkbelasting wordt doorgaans gedurende een beperkte periode ervaren, maar kan vereisen dat uw toepassing twee of meer keer de normale werking schaalt. Ontdek de 50 percentiel, 90 percentiel en 99 percentielvereisten. Dit helpt bij het filteren van eventuele uitschieters in de prestatie-eisen en u uw inspanningen richten op het optimaliseren voor de juiste waarden.

## <a name="application-performance-requirements-checklist"></a>Checklist voor toepassingsprestatievereisten

| **Prestatie-eisen** | **50 Percentiel** | **90 Percentiel** | **99 Percentiel** |
| --- | --- | --- | --- |
| Met maximaal Transacties per seconde | | | |
| % Leesbewerkingen | | | |
| % Schrijfbewerkingen | | | |
| % Willekeurige bewerkingen | | | |
| % Opeenvolgende bewerkingen | | | |
| IO-aanvraaggrootte | | | |
| Gemiddelde doorvoer | | | |
| Met maximaal Doorvoer | | | |
| Min. Latentie | | | |
| Gemiddelde latentie | | | |
| Met maximaal CPU | | | |
| Gemiddelde CPU | | | |
| Met maximaal Geheugen | | | |
| Gemiddeld geheugen | | | |
| Wachtrijdiepte | | | |

> [!NOTE]
> U moet overwegen deze getallen te schalen op basis van de verwachte toekomstige groei van uw toepassing. Het is een goed idee om van tevoren te plannen voor groei, omdat het moeilijker zou kunnen zijn om de infrastructuur te veranderen om de prestaties later te verbeteren.

Als u een bestaande toepassing hebt en wilt overstappen naar Premium Storage, bouwt u eerst de bovenstaande checklist voor de bestaande toepassing. Bouw vervolgens een prototype van uw toepassing op Premium Storage en ontwerp de toepassing op basis van richtlijnen die zijn beschreven in *Het optimaliseren van toepassingsprestaties* in een later deel van dit document. In het volgende artikel worden de hulpprogramma's beschreven die u gebruiken om de prestatiemetingen te verzamelen.

### <a name="counters-to-measure-application-performance-requirements"></a>Tellers voor het meten van toepassingsprestatievereisten

De beste manier om de prestatievereisten van uw toepassing te meten, is door gebruik te maken van prestatiebewakingstools die worden geleverd door het besturingssysteem van de server. U PerfMon voor Windows en iostat voor Linux gebruiken. Deze tools vangen tellers die overeenkomen met elke maatregel uitgelegd in de bovenstaande sectie. U moet de waarden van deze tellers vastleggen wanneer uw toepassing de normale workloads voor piek- en buitenkantoorwerk uitvoert.

De PerfMon-tellers zijn beschikbaar voor processor, geheugen en elke logische schijf en fysieke schijf van uw server. Wanneer u premium opslagschijven met een VM gebruikt, zijn de fysieke schijftellers voor elke premium opslagschijf en zijn logische schijftellers voor elk volume dat op de premium opslagschijven wordt gemaakt. U moet de waarden vastleggen voor de schijven die uw toepassingsworkload hosten. Als er een één-op-één toewijzing is tussen logische en fysieke schijven, u verwijzen naar fysieke schijftellers. anders verwijzen naar de logische schijftellers. Op Linux genereert de iostat-opdracht een CPU- en schijfgebruiksrapport. Het rapport over schijfgebruik bevat statistieken per fysiek apparaat of partitie. Als u een databaseserver met zijn gegevens en logboeken op afzonderlijke schijven hebt, verzamelt u deze gegevens voor beide schijven. Onderstaande tabel beschrijft tellers voor schijven, processors en geheugen:

| Prestatiemeteritem | Beschrijving | Perfmon | Iostat Iostat |
| --- | --- | --- | --- |
| **IOPS of Transacties per seconde** |Aantal I/O-aanvragen dat per seconde aan de opslagschijf wordt afgegeven. |Schijfleest/sec <br> Schijfschrijft/sec |Tps <br> r/s <br> w/s |
| **Schijf leest en schrijft** |% van de lees- en schrijfbewerkingen die op de schijf worden uitgevoerd. |% schijfleestijd <br> % schrijftijd voor schijven |r/s <br> w/s |
| **Doorvoer** |Hoeveelheid gegevens die per seconde van de schijf worden gelezen of naar de schijf zijn geschreven. |Bytes voor schijflezen per seconde <br> Bytes voor schijfschrijfschrijfberichten per seconde |kB_read/s <br> kB_wrtn/s |
| **Latentie** |Totale tijd om een SCHIJF IO-aanvraag te voltooien. |Gemiddelde schijf seconde/gelezen <br> Gemiddelde schijf sec/write |Wachten <br> svctm svctm |
| **IO-grootte** |De grootte van I/O vraagt problemen aan de opslagschijven. |Gemiddelde schijfbytes/gelezen <br> Gemiddelde schijfbytes/schrijven |avgrq-sz |
| **Wachtrijdiepte** |Aantal openstaande I/O-aanvragen die wachten om te worden gelezen of naar de opslagschijf worden geschreven. |Huidige schijfwachtrijlengte |avgqu-sz |
| **Max. Geheugen** |Hoeveelheid geheugen die nodig is om de toepassing soepel uit te voeren |Percentage toegewezen bytes in gebruik |Vmstat gebruiken |
| **Max. Cpu** |Bedrag CPU nodig om toepassing soepel uit te voeren |% Processortijd |%util |

Meer informatie over [iostat](https://linux.die.net/man/1/iostat) en [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).



## <a name="optimize-application-performance"></a>Toepassingsprestaties optimaliseren

De belangrijkste factoren die van invloed zijn op de prestaties van een toepassing die wordt uitgevoerd op Premium Storage zijn De aard van IO-aanvragen, VM-grootte, Schijfgrootte, Aantal schijven, schijfcache, multithreading en wachtrijdiepte. U een aantal van deze factoren bedienen met knoppen die door het systeem worden geleverd. De meeste toepassingen geven u mogelijk geen optie om de IO-grootte en wachtrijdiepte rechtstreeks te wijzigen. Als u bijvoorbeeld SQL Server gebruikt, u de IO-grootte en de wachtrijdiepte niet kiezen. SQL Server kiest de optimale IO-grootte en wachtrijdieptewaarden om de meeste prestaties te krijgen. Het is belangrijk om de effecten van beide soorten factoren op de prestaties van uw toepassing te begrijpen, zodat u de juiste resources inrichten om aan de prestatiebehoeften te voldoen.

Raadpleeg in deze sectie de checklist voor toepassingsvereisten die u hebt gemaakt om te bepalen hoeveel u nodig hebt om de prestaties van uw toepassing te optimaliseren. Op basis daarvan u bepalen welke factoren uit deze sectie u moet afstemmen. Als u getuige wilt zijn van de effecten van elke factor op de prestaties van uw toepassing, voert u benchmarkingtools uit op de installatie van uw toepassing. Raadpleeg het Benchmarking-artikel, dat aan het einde is gekoppeld, voor stappen om gemeenschappelijke benchmarkingtools op Windows- en Linux-VM's uit te voeren.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>IOPS, doorvoer en latentie in één oogopslag optimaliseren

De onderstaande tabel geeft een overzicht van prestatiefactoren en de stappen die nodig zijn om IOPS, doorvoer en latentie te optimaliseren. De secties na deze samenvatting zal beschrijven elke factor is veel meer diepte.

Zie [Linux VM-formaten](../articles/virtual-machines/linux/sizes.md) of [Windows VM-formaten](../articles/virtual-machines/windows/sizes.md)voor meer informatie over vm-formaten en over de IOPS- doorvoer en latentie die beschikbaar zijn voor elk type VM.

| &nbsp; | **IOPS** | **Doorvoer** | **Latentie** |
| --- | --- | --- | --- |
| **Voorbeeldscenario** |Enterprise OLTP-toepassing die zeer hoge transacties per seconde tarief vereist. |Enterprise Data warehousing applicatie verwerken van grote hoeveelheden gegevens. |Near real-time applicaties die onmiddellijke reacties op verzoeken van gebruikers vereisen, zoals online gaming. |
| Prestatiefactoren | &nbsp; | &nbsp; | &nbsp; |
| **IO-grootte** |Kleinere IO-grootte levert een hogere IOPS op. |Grotere IO-grootte om hogere doorvoer te leveren. | &nbsp;|
| **VM-grootte** |Gebruik een VM-grootte die IOPS biedt dat groter is dan uw toepassingsvereiste. |Gebruik een VM-grootte met doorvoerlimiet die groter is dan de vereiste toepassingen. |Gebruik een VM-grootte die schaallimieten biedt die groter zijn dan de vereiste toepassingen. |
| **Schijfgrootte** |Gebruik een schijfgrootte die IOPS biedt die groter is dan uw toepassingsvereiste. |Gebruik een schijfgrootte met doorvoerlimiet die groter is dan de vereiste toepassingen. |Gebruik een schijfgrootte die schaallimieten biedt die groter zijn dan de vereiste toepassingen. |
| **VM- en schijfschaallimieten** |De IOPS-limiet van de gekozen VM-grootte moet groter zijn dan de totale IOPS die wordt aangestuurd door opslagschijven die eraan zijn gekoppeld. |De doorvoerlimiet van de gekozen VM-grootte moet groter zijn dan de totale doorvoer die wordt aangedreven door premium-opslagschijven die eraan zijn gekoppeld. |Schaallimieten van de gekozen VM-grootte moeten groter zijn dan de totale schaallimieten van gekoppelde premium-opslagschijven. |
| **Schijfcache** |Schakel ReadOnly Cache in op premium opslagschijven met Read heavy operations om hogere Read IOPS te krijgen. | &nbsp; |Schakel ReadOnly Cache in op premium opslagschijven met Ready zware bewerkingen om zeer lage leeslatencies te krijgen. |
| **Schijfstriping** |Gebruik meerdere schijven en streep ze samen om een gecombineerde hogere IOPS en doorvoer limiet te krijgen. De gecombineerde limiet per VM moet hoger zijn dan de gecombineerde limieten van bijgevoegde premium schijven. | &nbsp; | &nbsp; |
| **Streepgrootte** |Kleinere streepgrootte voor willekeurig klein IO-patroon in OLTP-toepassingen. Gebruik bijvoorbeeld de streepgrootte van 64 KB voor SQL Server OLTP-toepassing. |Grotere streepgrootte voor sequentiële grote IO-patroon gezien in Data Warehouse-toepassingen. Gebruik bijvoorbeeld de streepgrootte van 256 KB voor sql servergegevensmagazijntoepassing. | &nbsp; |
| **Multithreading** |Gebruik multithreading om een hoger aantal aanvragen naar Premium Storage te pushen, wat zal leiden tot een hogere IOPS en doorvoer. Stel bijvoorbeeld op SQL Server een hoge MAXDOP-waarde in om meer CPU's toe te wijzen aan SQL Server. | &nbsp; | &nbsp; |
| **Wachtrijdiepte** |Grotere wachtrijdiepte levert hogere IOPS op. |Grotere wachtrijdiepte levert een hogere doorvoer op. |Kleinere wachtrijdiepte levert lagere latencies op. |

## <a name="nature-of-io-requests"></a>Aard van IO-verzoeken

Een IO-aanvraag is een eenheid van invoer/uitvoerbewerking die uw toepassing zal uitvoeren. Het identificeren van de aard van IO-verzoeken, willekeurig of sequentieel, lezen of schrijven, klein of groot, zal u helpen bij het bepalen van de prestatievereisten van uw toepassing. Het is belangrijk om de aard van IO-verzoeken te begrijpen, om de juiste beslissingen te nemen bij het ontwerpen van uw toepassingsinfrastructuur. IOs moeten gelijkmatig worden verdeeld om de best mogelijke prestaties te bereiken.

IO grootte is een van de belangrijkste factoren. De IO-grootte is de grootte van de invoer/uitvoerbewerkingsaanvraag die door uw toepassing wordt gegenereerd. De IO-grootte heeft een aanzienlijke invloed op de prestaties, vooral op de IOPS en bandbreedte die de toepassing kan bereiken. De volgende formule toont de relatie tussen IOPS, IO-grootte en Bandbreedte/doorvoer.  
    ![](media/premium-storage-performance/image1.png)

In sommige toepassingen u hun IO-grootte wijzigen, terwijl sommige toepassingen dat niet doen. SQL Server bepaalt bijvoorbeeld zelf de optimale IO-grootte en biedt gebruikers geen knoppen om deze te wijzigen. Oracle biedt daarentegen een parameter genaamd [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) waarmee u de I/O-aanvraaggrootte van de database configureren.

Als u een toepassing gebruikt, waarmee u de IO-grootte niet wijzigen, gebruikt u de richtlijnen in dit artikel om de prestatie-KPI te optimaliseren die het meest relevant is voor uw toepassing. Bijvoorbeeld:

* Een OLTP-toepassing genereert miljoenen kleine en willekeurige IO-aanvragen. Als u dit soort IO-aanvragen wilt afhandelen, moet u uw toepassingsinfrastructuur ontwerpen om een hogere IOPS te krijgen.  
* Een toepassing voor gegevensopslag genereert grote en sequentiële IO-aanvragen. Als u dit soort IO-aanvragen wilt afhandelen, moet u uw toepassingsinfrastructuur ontwerpen om een hogere bandbreedte of doorvoer te krijgen.

Als u een toepassing gebruikt waarmee u de IO-grootte wijzigen, gebruikt u deze vuistregel voor de IO-grootte naast andere prestatierichtlijnen,

* Kleinere IO-grootte om hogere IOPS te krijgen. Bijvoorbeeld 8 KB voor een OLTP-toepassing.  
* Grotere IO-grootte om hogere bandbreedte/ doorvoer te krijgen. Bijvoorbeeld 1024 KB voor een data warehouse applicatie.

Hier is een voorbeeld over hoe u de IOPS en doorvoer/bandbreedte voor uw toepassing berekenen. Overweeg een toepassing met behulp van een P30-schijf. De maximale IOPS en doorvoer/bandbreedte die een P30-schijf kan bereiken is respectievelijk 5000 IOPS en 200 MB per seconde. Nu, als uw toepassing vereist de maximale IOPS van de P30-schijf en u gebruik maken van een kleinere IO-grootte zoals 8 KB, de resulterende bandbreedte die u in staat zal zijn om te krijgen is 40 MB per seconde. Als uw toepassing echter de maximale doorvoer/bandbreedte van de P30-schijf vereist en u een grotere IO-grootte zoals 1024 KB gebruikt, zal de resulterende IOPS minder, 200 IOPS zijn. Stem daarom de IO-grootte zo af dat deze voldoet aan zowel de IOPS- als de doorvoer/bandbreedte-eis van uw toepassing. In de volgende tabel worden de verschillende IO-formaten en de bijbehorende IOPS- en Doorvoerdoorvoer voor een P30-schijf samengevat.

| Toepassingsvereiste | I/O-grootte | IOPS | Doorvoer/bandbreedte |
| --- | --- | --- | --- |
| Max. IOPS |8 kB |5.000 |40 MB per seconde |
| Maximale doorvoer |1024 KB |200 |200 MB per seconde |
| Maximale doorvoer + hoge IOPS |64 kB |3,200 |200 MB per seconde |
| Max IOPS + hoge doorvoer |32 KB |5.000 |160 MB per seconde |

Als u IOPS en Bandbreedte hoger wilt krijgen dan de maximale waarde van een enkele premium-opslagschijf, gebruikt u meerdere premium schijven die aan elkaar zijn gestreept. Streep bijvoorbeeld twee P30-schijven om een gecombineerde IOPS van 10.000 IOPS of een gecombineerde doorvoer van 400 MB per seconde te krijgen. Zoals uitgelegd in de volgende sectie, moet u een VM-grootte gebruiken die de gecombineerde schijf-IOPS en doorvoer ondersteunt.

> [!NOTE]
> Als u iops of doorvoer de andere verhoogt ook verhoogt, zorg ervoor dat u niet hit doorvoer of IOPS grenzen van de schijf of VM bij het verhogen van een van beide.

Om getuige te zijn van de effecten van IO-grootte op de prestaties van toepassingen, u benchmarkingtools uitvoeren op uw VM en schijven. Maak meerdere testruns en gebruik voor elke uitvoering verschillende IO-grootte om de impact te zien. Raadpleeg het Benchmarking-artikel, dat aan het einde is gekoppeld, voor meer details.

## <a name="high-scale-vm-sizes"></a>Vm-formaten op grote schaal

Wanneer u begint met het ontwerpen van een toepassing, kiest u een van de eerste dingen die u moet doen een VM om uw toepassing te hosten. Premium Storage wordt geleverd met high scale VM-formaten die toepassingen kunnen uitvoeren die een hoger rekenvermogen vereisen en een hoge lokale schijf-I/O-prestaties. Deze VM's bieden snellere processors, een hogere geheugen-kernverhouding en een Solid-State Drive (SSD) voor de lokale schijf. Voorbeelden van high scale VM's die Premium Storage ondersteunen zijn de VM's uit de DS- en GS-serie.

Vm's met grote schaal zijn beschikbaar in verschillende formaten met een verschillend aantal CPU-cores, geheugen, besturingssysteem en tijdelijke schijfgrootte. Elke VM-grootte heeft ook een maximum aantal gegevensschijven dat u aan de VM koppelen. Daarom is de gekozen VM-grootte van invloed op de beschikbaarheid van de verwerkings-, geheugen- en opslagcapaciteit voor uw toepassing. Het heeft ook invloed op de compute- en opslagkosten. Hieronder staan bijvoorbeeld de specificaties van de grootste VM-grootte in een DS-serie en een GS-serie:

| VM-grootte | CPU-kernen | Geheugen | VM-schijfformaten | Met maximaal gegevensschijven | Cachegrootte | IOPS | IO-limieten voor bandbreedtecache |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> Lokale SSD = 224 GB |32 |576 GB |50.000 IOPS <br> 512 MB per seconde |4.000 IOPS en 33 MB per seconde |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> Lokale SSD = 896 GB |64 |4224 GB |80.000 IOPS <br> 2.000 MB per seconde |5.000 IOPS en 50 MB per seconde |

Als u een volledige lijst met alle beschikbare Azure VM-formaten wilt bekijken, raadpleegt u [Windows VM-formaten](../articles/virtual-machines/windows/sizes.md) of [Linux VM-formaten](../articles/virtual-machines/linux/sizes.md). Kies een VM-grootte die kan voldoen aan en schaal naar de gewenste toepassingsprestatie-eisen. Houd daarnaast rekening met belangrijke overwegingen bij het kiezen van VM-formaten.

*Schaallimieten*  
De maximale IOPS-limieten per VM en per schijf zijn verschillend en onafhankelijk van elkaar. Zorg ervoor dat de toepassing IOPS binnen de grenzen van de VM en de premium schijven die eraan zijn gekoppeld, aandrijft. Anders zullen toepassingsprestaties throttling ervaren.

Stel bijvoorbeeld dat een aanvraagvereiste maximaal 4.000 IOPS is. Om dit te bereiken, indient u een P30-schijf op een DS1 VM. De P30-schijf kan tot 5.000 IOPS leveren. De DS1 VM is echter beperkt tot 3.200 IOPS. Bijgevolg worden de toepassingsprestaties beperkt door de VM-limiet bij 3.200 IOPS en zullen de prestaties worden afgebroken. Als u deze situatie wilt voorkomen, kiest u een VM- en schijfgrootte die beide voldoen aan de toepassingsvereisten.

*Exploitatiekosten*  
In veel gevallen is het mogelijk dat uw totale bedrijfskosten met Behulp van Premium Storage lager zijn dan met standaardopslag.

Overweeg bijvoorbeeld een toepassing waarvoor 16.000 IOPS vereist is. Om deze prestaties te bereiken,\_hebt u een Standaard D14 Azure IaaS VM nodig, die een maximale IOPS van 16.000 kan geven met behulp van 32 standaard opslag 1 TB schijven. Elke standaard opslagschijf van 1 TB kan maximaal 500 IOPS bereiken. De geschatte kosten van deze VM per maand zullen $ 1.570 bedragen. De maandelijkse kosten van 32 standaard opslagschijven zullen $ 1.638 bedragen. De geschatte totale maandelijkse kosten zullen $ 3.208 zijn.

Als u echter dezelfde toepassing op Premium Storage hebt gehost, hebt u een kleiner VM-formaat en minder premium opslagschijven nodig, waardoor de totale kosten worden verlaagd. Een\_Standaard DS13 VM kan voldoen aan de 16.000 IOPS-vereisten met behulp van vier P30-schijven. De DS13 VM heeft een maximale IOPS van 25.600 en elke P30 schijf heeft een maximale IOPS van 5.000. In totaal kan deze configuratie 5.000 x 4 = 20.000 IOPS bereiken. De geschatte kosten van deze VM per maand zullen $ 1.003 bedragen. De maandelijkse kosten van vier P30 premium opslagschijven zullen $ 544.34 zijn. De geschatte totale maandelijkse kosten zullen $1.544 zijn.

Onderstaande tabel geeft een overzicht van de kostenverdeling van dit scenario voor standaard- en premiumopslag.

| &nbsp; | **Standaard** | **Premium** |
| --- | --- | --- |
| **Kosten van VM per maand** |$ 1.570,58\_(Standaard D14) |$ 1.003,66\_(Standaard DS13) |
| **Kosten van schijven per maand** |$ 1.638,40 (schijven van 32 x 1 TB) |$ 544,34 (4 x P30-schijven) |
| **Totale kosten per maand** |$ 3.208,98 |$ 1.544,34 |

*Linux Distro's*  

Met Azure Premium Storage krijgt u hetzelfde prestatieniveau voor VM's met Windows en Linux. Wij ondersteunen vele smaken van Linux distro's, en u de volledige lijst [hier](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)zien. Het is belangrijk op te merken dat verschillende distro's beter geschikt zijn voor verschillende soorten workloads. U ziet verschillende prestatieniveaus, afhankelijk van de distro waarop uw werkbelasting wordt uitgevoerd. Test de Linux distro's met uw toepassing en kies degene die het beste werkt.

Controleer bij het uitvoeren van Linux met Premium Storage de nieuwste updates over vereiste stuurprogramma's om hoge prestaties te garanderen.

## <a name="premium-storage-disk-sizes"></a>Premium opslagschijfformaten

Azure Premium Storage biedt verschillende formaten, zodat u er een kiezen die het beste bij uw behoeften past. Elke schijfgrootte heeft een andere schaallimiet voor IOPS, bandbreedte en opslag. Kies de juiste premium-opslagschijfgrootte, afhankelijk van de toepassingsvereisten en de grote VM-grootte. De onderstaande tabel toont de grootte van de schijven en hun mogelijkheden. P4-, P6-, P15-, P60-, P70- en P80-formaten worden momenteel alleen ondersteund voor beheerde schijven.

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Hoeveel schijven u kiest, is afhankelijk van de gekozen schijfgrootte. U één P50-schijf of meerdere P10-schijven gebruiken om aan uw toepassingsvereisten te voldoen. Houd rekening met de onderstaande overwegingen bij het maken van de keuze.

*Schaallimieten (IOPS en doorvoer)*  
De IOPS- en doorvoerlimieten van elke Premium-schijfgrootte zijn verschillend en onafhankelijk van de VM-schaallimieten. Zorg ervoor dat de totale IOPS en doorvoer van de schijven binnen schaalgrenzen van de gekozen VM-grootte vallen.

Als een toepassingsvereiste bijvoorbeeld maximaal 250 MB/sec-doorvoer bedraagt en u een DS4-vm gebruikt met één P30-schijf. De DS4 VM kan tot 256 MB/sec doorvoer geven. Een enkele P30-schijf heeft echter doorvoerlimiet van 200 MB/sec. Bijgevolg wordt de toepassing beperkt tot 200 MB/sec vanwege de schijflimiet. Als u deze limiet wilt overschrijden, u meer dan één gegevensschijven aan de virtuele machine inrichten of het formaat van uw schijven wijzigen in P40 of P50.

> [!NOTE]
> Reads geserveerd door de cache zijn niet opgenomen in de schijf IOPS en doorvoer, dus niet onderworpen aan schijflimieten. Cache heeft zijn aparte IOPS- en doorvoerlimiet per VM.
>
> Bijvoorbeeld, in eerste instantie uw leest en schrijft zijn 60MB/sec en 40MB/sec respectievelijk. Na verloop van tijd, de cache opwarmt en serveert meer en meer van de leest uit de cache. Vervolgens u hogere schrijfdoorvoer van de schijf krijgen.

*Aantal schijven*  
Bepaal het aantal schijven dat u nodig hebt door toepassingsvereisten te beoordelen. Elke VM-grootte heeft ook een limiet op het aantal schijven dat u aan de VM koppelen. Meestal is dit twee keer het aantal cores. Controleer of de door u gekozen VM-grootte het aantal schijven dat nodig is, kan ondersteunen.

Vergeet niet dat de Premium Storage-schijven hogere prestatiemogelijkheden hebben in vergelijking met standaardopslagschijven. Als u uw toepassing migreert van Azure IaaS VM met standaardopslag naar Premium Storage, hebt u waarschijnlijk minder premium schijven nodig om dezelfde of hogere prestaties voor uw toepassing te bereiken.

## <a name="disk-caching"></a>Schijfcaching

Vm's met hoge schaal die gebruikmaken van Azure Premium Storage hebben een multi-tier caching-technologie genaamd BlobCache. BlobCache maakt gebruik van een combinatie van de Virtual Machine RAM en lokale SSD voor caching. Deze cache is beschikbaar voor de permanente schijven van Premium Storage en de lokale VM-schijven. Standaard is deze cacheinstelling ingesteld op Lezen/schrijven voor OS-schijven en ReadOnly voor gegevensschijven die worden gehost op Premium Storage. Met schijfcache ingeschakeld op de Premium Storage-schijven, kunnen de vm's op grote schaal extreem hoge prestaties bereiken die de onderliggende schijfprestaties overtreffen.

> [!WARNING]
> Disk Caching wordt niet ondersteund voor schijven 4 TiB en groter. Als er meerdere schijven aan uw VM zijn gekoppeld, ondersteunt elke schijf die kleiner is dan 4 TiB caching.
>
> Als u de cache-instelling van een Azure-schijf wijzigt, wordt de doelschijf losgekoppeld en opnieuw hecht. Als het de schijf van het besturingssysteem is, wordt de VM opnieuw gestart. Stop alle toepassingen en services die kunnen worden beïnvloed door deze onderbreking voordat u de schijfcache-instelling wijzigt.

Raadpleeg het inside [Azure Premium Storage-blogbericht](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) voor meer informatie over hoe BlobCache werkt.

Het is belangrijk om cache op de juiste set schijven in te schakelen. Of u schijfcacheop een premium schijf moet inschakelen of niet, hangt af van het werkbelastingpatroon dat de schijf verwerkt. Onderstaande tabel toont de standaardcache-instellingen voor besturingssysteem- en gegevensschijven.

| **Schijftype** | **Standaardcacheinstelling** |
| --- | --- |
| Besturingssysteemschijf |ReadWrite |
| Gegevensschijf |ReadOnly |

Hieronder volgen de aanbevolen instellingen voor schijfcache voor gegevensschijven,

| **Instelling voor schijfcache** | **aanbeveling over wanneer deze instelling moet worden gebruikt** |
| --- | --- |
| Geen |Configureer hostcache als Geen voor alleen-schrijven en schrijfzware schijven. |
| ReadOnly |Configureer hostcache als ReadOnly voor alleen-lezen en lees-schrijfschijven. |
| ReadWrite |Configureer hostcache alleen als ReadWrite als uw toepassing de schrijfgegevens in cache correct verwerkt naar permanente schijven wanneer dat nodig is. |

*ReadOnly*  
Door ReadOnly caching op Premium Storage-gegevensschijven te configureren, u een lage leeslatentie bereiken en een zeer hoge Lees-IOPS- en doorvoerdoorvoer voor uw toepassing krijgen. Dit is te wijten aan twee redenen,

1. Reads uitgevoerd vanuit cache, die op het VM-geheugen en lokale SSD staat, zijn veel sneller dan de gegevensschijf leest, die zich op de Azure blob-opslag bevindt.  
1. Premium Storage telt de Reads die vanuit de cache worden geserveerd niet mee naar de schijf IOPS en Doorvoer. Daarom is uw toepassing in staat om een hogere totale IOPS en doorvoer te bereiken.

*ReadWrite*  
Standaard hebben de BE-schijven ReadWrite-caching ingeschakeld. We hebben onlangs ook ondersteuning toegevoegd voor ReadWrite caching op dataschijven. Als u ReadWrite-caching gebruikt, moet u een goede manier hebben om de gegevens van cache naar permanente schijven te schrijven. SQL Server verwerkt bijvoorbeeld zelf gegevens in de cache naar de permanente opslagschijven. Het gebruik van ReadWrite-cache met een toepassing die de vereiste gegevens niet verwerkt, kan leiden tot gegevensverlies als de VM vastloopt.

*Geen*  
Momenteel wordt **Geen** alleen ondersteund op gegevensschijven. Het wordt niet ondersteund op OS-schijven. Als u **Geen** instelt op een osschijf, wordt dit intern overschreven en ingesteld op **Alleenlezen**.

U deze richtlijnen bijvoorbeeld toepassen op SQL Server die op Premium Storage wordt uitgevoerd door het volgende te doen:

1. Configureer de cache 'ReadOnly' op premium opslagschijven die gegevensbestanden hosten.  
   a.  De snelle leest uit cache lager de SQL Server query tijd, omdat gegevenspagina's worden opgehaald veel sneller uit de cache in vergelijking met rechtstreeks van de gegevensschijven.  
   b.  Het serveren van leest uit de cache betekent dat er extra doorvoer beschikbaar is vanaf premium dataschijven. SQL Server kan deze extra doorvoer gebruiken voor het ophalen van meer gegevenspagina's en andere bewerkingen, zoals back-up/herstel, batchbelastingen en indexrevisies.  
1. Configureer 'Geen'-cache op premium opslagschijven die de logboekbestanden hosten.  
   a.  Log bestanden hebben voornamelijk write-zware bewerkingen. Daarom profiteren ze niet van de ReadOnly-cache.

## <a name="optimize-performance-on-linux-vms"></a>Prestaties optimaliseren op Linux VM's

Voor alle premium SSD's of ultraschijven met cache ingesteld op **ReadOnly** of **None,** moet u "barrières" uitschakelen wanneer u het bestandssysteem monteert. U hebt in dit scenario geen barrières nodig, omdat de schrijfbewerkingen naar premium opslagschijven duurzaam zijn voor deze cache-instellingen. Wanneer de schrijfaanvraag is voltooid, zijn de gegevens naar het permanente archief geschreven. Als u 'barrières' wilt uitschakelen, gebruikt u een van de volgende methoden. Kies de optie voor uw bestandssysteem:
  
* Voor **reiserFS**, om barrières `barrier=none` uit te schakelen, gebruik maken van de mount optie. (Gebruik .) `barrier=flush`
* Voor **ext3/ext4**, om barrières `barrier=0` uit te schakelen, gebruik maken van de mount optie. (Gebruik .) `barrier=1`
* Gebruik **XFS**de `nobarrier` houderoptie om de barrières uit te schakelen om de barrières uit te schakelen. (Gebruik .) `barrier`
* Voor premium opslagschijven met cacheingesteld op **ReadWrite,** u barrières voor schrijfduurzaamheid inschakelen.
* Als u volumelabels wilt blijven gebruiken nadat u de VM opnieuw hebt opgestart, moet u /etc/fstab bijwerken met de universeel unieke uuid-verwijzingen naar de schijven. Zie [Een beheerde schijf toevoegen aan een Linux-vm voor](../articles/virtual-machines/linux/add-disk.md)meer informatie.

De volgende Linux distributies zijn gevalideerd voor premium SSD's. Voor betere prestaties en stabiliteit met premium SSD's raden we u aan uw VM's te upgraden naar een van deze versies of nieuwere versies. 

Voor sommige versies zijn de nieuwste Linux Integration Services (LIS), v4.0, voor Azure vereist. Als u een distributie wilt downloaden en installeren, volgt u de koppeling in de volgende tabel. We voegen afbeeldingen toe aan de lijst terwijl we de validatie voltooien. Onze validaties laten zien dat de prestaties per afbeelding verschillen. De prestaties zijn afhankelijk van de werkbelastingkenmerken en uw afbeeldingsinstellingen. Verschillende afbeeldingen zijn afgestemd op verschillende soorten workloads.

| Distributie | Versie | Ondersteunde kernel | Details |
| --- | --- | --- | --- |
| Ubuntu | 12.04 of nieuwer| 3.2.0-75.110+ | &nbsp; |
| Ubuntu | 14.04 of nieuwer| 3.13.0-44.73+  | &nbsp; |
| Debian | 7.x, 8.x of nieuwer| 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12 of nieuwer| 3.12.36-38,1+ | &nbsp; |
| SUSE | SLES 11 SP4 of nieuwer| 3.0.101-0,63,1+ | &nbsp; |
| CoreOS | 584.0.0+ of nieuwer| 3.18.4+ | &nbsp; |
| CentOS | 6,5, 6,6, 6,7, 7,0 of nieuwer| &nbsp; | [LIS4 vereist](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Opmerking in de volgende sectie bekijken* |
| CentOS | 7.1+ of nieuwer| 3.10.0-229.1.2.el7+ | [LIS4 aanbevolen](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Opmerking in de volgende sectie bekijken* |
| Red Hat Enterprise Linux (RHEL) | 6,8+, 7,2+ of nieuwer | &nbsp; | &nbsp; |
| Oracle | 6,0+, 7,2+ of nieuwer | &nbsp; | UEK4 of RHCK |
| Oracle | 7.0-7.1 of nieuwer | &nbsp; | UEK4 of RHCK w/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |
| Oracle | 6.4-6.7 of nieuwer | &nbsp; | UEK4 of RHCK w/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |

### <a name="lis-drivers-for-openlogic-centos"></a>LIS-stuurprogramma's voor OpenLogic CentOS

Als u OpenLogic CentOS VM's uitvoert, voert u de volgende opdracht uit om de nieuwste stuurprogramma's te installeren:

```
sudo yum remove hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
sudo reboot
```

In sommige gevallen zal de bovenstaande opdracht de kernel ook upgraden. Als een kernel-update nodig is, moet u de bovenstaande opdrachten mogelijk opnieuw uitvoeren na het opnieuw opstarten om het microsoft-hyper-v-pakket volledig te installeren.


## <a name="disk-striping"></a>Schijfstriping

Wanneer een vm op grote schaal is gekoppeld aan verschillende permanente premium-opslagschijven, kunnen de schijven worden samengevoegd om hun IMP's, bandbreedte en opslagcapaciteit samen te voegen.

In Windows u opslagruimten gebruiken om schijven samen te strepen. U moet één kolom configureren voor elke schijf in een groep. Anders kunnen de algehele prestaties van het gestreepte volume lager zijn dan verwacht, als gevolg van een ongelijke verdeling van het verkeer over de schijven.

Belangrijk: met de gebruikersinterface van Serverbeheer u het totale aantal kolommen instellen tot 8 voor een gestreept volume. Wanneer u meer dan acht schijven bevestigt, gebruikt u PowerShell om het volume te maken. Met PowerShell u het aantal kolommen instellen dat gelijk is aan het aantal schijven. Als er bijvoorbeeld 16 schijven in één streepset zijn; geef 16 kolommen op in de parameter *NumberOfColumns* van de cmdlet *Nieuw-VirtualDisk* PowerShell.

Gebruik op Linux het MDADM-hulpprogramma om schijven samen te strepen. Voor gedetailleerde stappen op striping schijven op Linux verwijzen naar [Software CONFIGUREREN RAID op Linux](../articles/virtual-machines/linux/configure-raid.md).

*Streepgrootte*  
Een belangrijke configuratie in schijfstriping is de streepgrootte. De streepgrootte of blokgrootte is het kleinste stuk gegevens dat toepassing kan richten op een gestreept volume. De streepgrootte die u configureert, is afhankelijk van het type toepassing en het aanvraagpatroon. Als u de verkeerde streepgrootte kiest, kan dit leiden tot io-verkeerde uitlijning, wat leidt tot verminderde prestaties van uw toepassing.

Als een IO-aanvraag die door uw toepassing wordt gegenereerd bijvoorbeeld groter is dan de grootte van de schijfstreep, schrijft het opslagsysteem deze over de grenzen van de stripe-eenheid op meer dan één schijf. Wanneer het tijd is om toegang te krijgen tot die gegevens, moet het zoeken in meer dan een streep eenheden om de aanvraag te voltooien. Het cumulatieve effect van dergelijk gedrag kan leiden tot aanzienlijke prestatiedegradatie. Aan de andere kant, als de io-aanvraag grootte kleiner is dan streep grootte, en als het willekeurig van aard is, de IO-aanvragen kunnen optellen op dezelfde schijf waardoor een knelpunt en uiteindelijk vernederende de IO-prestaties.

Afhankelijk van het type werkbelasting dat uw toepassing uitvoert, kiest u een geschikte streepgrootte. Voor willekeurige kleine IO-aanvragen gebruikt u een kleinere streepgrootte. Terwijl voor grote opeenvolgende IO-aanvragen een grotere streepgrootte wordt gebruikt. Ontdek de aanbevelingen voor strepengrootte voor de toepassing die u op Premium Storage gaat uitvoeren. Configureer voor SQL Server de streepgrootte van 64 KB voor OLTP-workloads en 256 KB voor workloads voor gegevensopslag. Zie [Aanbevolen procedures voor prestaties voor SQL Server op Azure VM's](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) voor meer informatie.

> [!NOTE]
> U maximaal 32 premium opslagschijven op een VM uit de DS-serie en 64 premium opslagschijven op een VM uit gs-serie samenvoegen.

## <a name="multi-threading"></a>Multithreading

Azure heeft het Premium Storage-platform ontworpen om enorm parallel te lopen. Daarom bereikt een multi-threaded toepassing veel hogere prestaties dan een single-threaded toepassing. Een multi-threaded toepassing splitst zijn taken op over meerdere threads en verhoogt de efficiëntie van de uitvoering ervan door maximaal gebruik te maken van de VM- en schijfbronnen.

Als uw toepassing bijvoorbeeld op één core VM draait met twee threads, kan de CPU schakelen tussen de twee threads om efficiëntie te bereiken. Terwijl een thread wacht op een schijf IO te voltooien, kan de CPU overschakelen naar de andere draad. Op deze manier kunnen twee threads meer bereiken dan een enkele thread zou doen. Als de VM meer dan één kern heeft, vermindert deze de looptijd verder omdat elke kern taken parallel kan uitvoeren.

Het is mogelijk dat u de manier waarop een kant-en-klare toepassing single threading of multi-threading implementeert, niet wijzigen. SQL Server is bijvoorbeeld geschikt voor het verwerken van multi-CPU en multi-core. SQL Server bepaalt echter onder welke voorwaarden het een of meer threads zal gebruiken om een query te verwerken. Het kan query's uitvoeren en indexen bouwen met behulp van multi-threading. Voor een query waarbij grote tabellen worden samenvoegen en gegevens worden gesorteerd voordat u terugkeert naar de gebruiker, gebruikt SQL Server waarschijnlijk meerdere threads. Een gebruiker kan echter niet bepalen of SQL Server een query uitvoert met één thread of meerdere threads.

Er zijn configuratie-instellingen die u wijzigen om deze multi-threading of parallelle verwerking van een toepassing te beïnvloeden. In het geval van SQL Server is dit bijvoorbeeld de maximale parallelconfiguratie. Met deze instelling, MAXDOP genaamd, u het maximum aantal processors configureren dat SQL Server kan gebruiken bij parallelle verwerking. U MAXDOP configureren voor afzonderlijke query's of indexbewerkingen. Dit is handig wanneer u resources van uw systeem in evenwicht wilt brengen voor een prestatiekritieke toepassing.

Stel dat uw toepassing met SQL Server tegelijkertijd een grote query en een indexbewerking uitvoert. Laten we aannemen dat u wilde dat de indexbewerking beter performant zou zijn in vergelijking met de grote query. In een dergelijk geval u de MAXDOP-waarde van de indexbewerking instellen als hoger dan de MAXDOP-waarde voor de query. Op deze manier heeft SQL Server meer processors die het kan gebruiken voor de indexbewerking in vergelijking met het aantal processors dat het kan besteden aan de grote query. Vergeet niet dat u geen controle hebt over het aantal threads dat SQL Server voor elke bewerking gebruikt. U het maximum aantal processors dat wordt toegewezen voor multi-threading beheren.

Meer informatie over [graden van parallelisme](https://technet.microsoft.com/library/ms188611.aspx) in SQL Server. Ontdek dergelijke instellingen die van invloed zijn op multi-threading in uw toepassing en hun configuraties om de prestaties te optimaliseren.

## <a name="queue-depth"></a>Wachtrijdiepte

De wachtrijdiepte of wachtrijlengte of wachtrijgrootte is het aantal in behandeling zijnde IO-aanvragen in het systeem. De waarde van wachtrijdiepte bepaalt hoeveel IO-bewerkingen uw toepassing kan opstellen, die de opslagschijven verwerken. Het beïnvloedt alle drie de toepassingsprestatie-indicatoren die we in dit artikel hebben besproken, namelijk IOPS, doorvoer en latentie.

Wachtrijdiepte en multi-threading zijn nauw met elkaar verbonden. De waarde wachtrijdiepte geeft aan hoeveel multi-threading door de toepassing kan worden bereikt. Als de wachtrijdiepte groot is, kan de toepassing meer bewerkingen tegelijk uitvoeren, met andere woorden, meer multi-threading. Als de wachtrijdiepte klein is, hoewel de toepassing multithreaded is, worden er niet genoeg aanvragen opgesteld voor gelijktijdige uitvoering.

Typisch, off the shelf toepassingen niet toestaan dat u de wachtrij diepte te veranderen, want als verkeerd ingesteld zal meer kwaad dan goed doen. Toepassingen stellen de juiste waarde in van de wachtrijdiepte om de optimale prestaties te krijgen. Het is echter belangrijk om dit concept te begrijpen, zodat u prestatieproblemen met uw toepassing oplossen. U ook de effecten van wachtrijdiepte observeren door benchmarkingtools op uw systeem uit te voeren.

Sommige toepassingen bieden instellingen om de wachtrijdiepte te beïnvloeden. Bijvoorbeeld, de MAXDOP (maximale mate van parallellisme) instelling in SQL Server uitgelegd in vorige sectie. MAXDOP is een manier om wachtrijdiepte en multi-threading te beïnvloeden, hoewel het niet direct de queue depth-waarde van SQL Server wijzigt.

*Hoge wachtrijdiepte*  
Een hoge wachtrijdiepte maakt meer bewerkingen op de schijf. De schijf kent de volgende aanvraag in de wachtrij van tevoren. Hierdoor kan de schijf bewerkingen van tevoren plannen en deze in een optimale volgorde verwerken. Aangezien de toepassing meer aanvragen naar de schijf verzendt, kan de schijf meer parallelle IOs verwerken. Uiteindelijk zal de applicatie in staat zijn om een hogere IOPS te bereiken. Aangezien de toepassing meer aanvragen verwerkt, neemt ook de totale doorvoer van de toepassing toe.

Typisch, een toepassing kan bereiken maximale doorvoer met 8-16 + uitstaande IOs per aangesloten schijf. Als een wachtrijdiepte één is, duwt de toepassing niet genoeg IOs aan het systeem, en het zal minder hoeveelheid in een bepaalde periode verwerken. Met andere woorden, minder doorvoer.

In SQL Server stelt u bijvoorbeeld de MAXDOP-waarde voor een query in op '4' sql server dat deze maximaal vier kernen kan gebruiken om de query uit te voeren. SQL Server bepaalt wat de beste wachtrijdieptewaarde is en het aantal cores voor de queryuitvoering.

*Optimale wachtrijdiepte*  
Zeer hoge wachtrij diepte waarde heeft ook zijn nadelen. Als de waarde van de wachtrijdiepte te hoog is, probeert de toepassing zeer hoge IOPS te rijden. Tenzij toepassing permanente schijven met voldoende ingerichte IOPS heeft, kan dit een negatieve invloed hebben op de latencies van toepassingen. De volgende formule toont de relatie tussen IOPS, latentie en wachtrijdiepte.  
    ![](media/premium-storage-performance/image6.png)

U moet wachtrijdiepte niet configureren op een hoge waarde, maar op een optimale waarde, die voldoende IOPS voor de toepassing kan leveren zonder dat dit gevolgen heeft voor latencies. Als de toepassingslatentie bijvoorbeeld 1 milliseconde moet zijn, is de wachtrijdiepte die nodig is om 5.000 IOPS te bereiken, QD = 5000 x 0,001 = 5.

*Wachtrijdiepte voor gestreept volume*  
Voor een gestreept volume behoudt u een hoog genoeg wachtrijdiepte, zodat elke schijf een piekwachtrijdiepte afzonderlijk heeft. Overweeg bijvoorbeeld een toepassing die een wachtrijdiepte van 2 duwt en er zijn vier schijven in de streep. De twee IO-aanvragen gaan naar twee schijven en de overige twee schijven worden niet actief. Configureer daarom de wachtrijdiepte zodanig dat alle schijven bezet kunnen zijn. Formule hieronder laat zien hoe u de wachtrijdiepte van gestreepte volumes bepalen.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Beperking

Azure Premium Storage-bepalingen opgegeven aantal IOPS en Doorvoer, afhankelijk van de VM-formaten en schijfformaten die u kiest. Wanneer uw toepassing probeert iops of doorvoer te stimuleren boven deze limieten van wat de VM of schijf aankan, zal Premium Storage het beperken. Dit manifesteert zich in de vorm van gedegradeerde prestaties in uw toepassing. Dit kan leiden tot hogere latentie, lagere doorvoer of lagere IOPS. Als Premium Storage niet gas geeft, kan uw toepassing volledig mislukken door meer te bereiken wat de resources kunnen bereiken. Dus, om prestatieproblemen als gevolg van beperking te voorkomen, altijd voldoende resources beschikbaar te stellen voor uw toepassing. Houd rekening met wat we hierboven hebben besproken in de secties VM-formaten en schijfgroottes. Benchmarking is de beste manier om erachter te komen welke resources u nodig hebt om uw toepassing te hosten.

## <a name="next-steps"></a>Volgende stappen
