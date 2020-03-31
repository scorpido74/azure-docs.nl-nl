---
title: Autovacuum optimaliseren - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u automatisch vacuüm optimaliseren op een Azure-database voor PostgreSQL - Single Server
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1917bd6744e100db54fe959292e29486f8a1784b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770183"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Automatisch vacuüm optimaliseren op een Azure-database voor PostgreSQL - Enkele server
In dit artikel wordt beschreven hoe u automatisch vacuüm op een Azure-database voor PostgreSQL-server effectief optimaliseren.

## <a name="overview-of-autovacuum"></a>Overzicht van autovacuüm
PostgreSQL maakt gebruik van multiversion concurrency control (MVCC) om meer database gelijktijdigheid mogelijk te maken. Elke update resulteert in een invoegen en verwijderen, en elke delete resulteert in rijen die worden gemarkeerd voor verwijdering. Soft-marking identificeert dode tuples die later zullen worden gezuiverd. Om deze taken uit te voeren, voert PostgreSQL een vacuümtaak uit.

Een vacuümtaak kan handmatig of automatisch worden geactiveerd. Er bestaan meer dode tuples wanneer de database zware updates of verwijderingsbewerkingen ondervindt. Er bestaan minder dode tuples wanneer de database niet actief is. U moet vaker stofzuigen wanneer de databasebelasting zwaar is, waardoor het uitvoeren van vacuümtaken *handmatig* lastig is.

Autovacuum kan worden geconfigureerd en profiteert van tuning. De standaardwaarden waarmee PostgreSQL wordt verzonden, proberen ervoor te zorgen dat het product op allerlei apparaten werkt. Deze apparaten omvatten Raspberry. De ideale configuratiewaarden zijn afhankelijk van:
- Totale beschikbare resources, zoals SKU en opslaggrootte.
- Resourcegebruik.
- Individuele objectkenmerken.

## <a name="autovacuum-benefits"></a>Autovacuum voordelen
Als u niet van tijd tot tijd stofzuigt, kunnen de dode tuples die zich ophopen resulteren in:
- Gegevens opgeblazen, zoals grotere databases en tabellen.
- Grotere suboptimale indexen.
- Verhoogde I/O.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Bloat bewaken met automatische vacuümquery's
De volgende voorbeeldquery is ontworpen om het aantal dode en levende tuples in een tabel met de naam XYZ te identificeren:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Autovacuümconfiguraties
De configuratieparameters die autovacuüm regelen, zijn gebaseerd op antwoorden op twee belangrijke vragen:
- Wanneer moet het beginnen?
- Hoeveel moet het schoonmaken nadat het begint?

Hier volgen enkele autovacuum configuratieparameters die u bijwerken op basis van de vorige vragen, samen met enkele richtlijnen.

Parameter|Beschrijving|Standaardwaarde
---|---|---
autovacuum_vacuum_threshold|Hiermee geeft u het minimumaantal bijgewerkte of verwijderde tuples op dat nodig is om een vacuümbewerking in één tabel te activeren. De standaardinstelling is 50 tuples. Stel deze parameter alleen in het bestand postgresql.conf of op de opdrachtregel van de server. Als u de instelling voor afzonderlijke tabellen wilt overschrijven, wijzigt u de parameters voor tabelopslag.|50
autovacuum_vacuum_scale_factor|Hiermee geeft u een fractie op van de tabelgrootte die u aan autovacuum_vacuum_threshold moet toevoegen wanneer u beslist of u een vacuümbewerking wilt activeren. De standaardwaarde is 0,2, dat is 20 procent van de tabelgrootte. Stel deze parameter alleen in het bestand postgresql.conf of op de opdrachtregel van de server. Als u de instelling voor afzonderlijke tabellen wilt overschrijven, wijzigt u de parameters voor tabelopslag.|5 procent
autovacuum_vacuum_cost_limit|Hiermee geeft u de kostengrenswaarde op die wordt gebruikt bij automatische vacuümbewerkingen. Als -1 is opgegeven, wat de standaardwaarde is, wordt de normale vacuum_cost_limit waarde gebruikt. Als er meer dan één werknemer is, wordt de waarde proportioneel verdeeld over de lopende autovacuümwerknemers. De som van de limieten voor elke werknemer overschrijdt de waarde van deze variabele niet. Stel deze parameter alleen in het bestand postgresql.conf of op de opdrachtregel van de server. Als u de instelling voor afzonderlijke tabellen wilt overschrijven, wijzigt u de parameters voor tabelopslag.|-1
autovacuum_vacuum_cost_delay|Hiermee geeft u de kostenvertragingswaarde op die wordt gebruikt bij automatische vacuümbewerkingen. Als -1 is opgegeven, wordt de normale vacuum_cost_delay waarde gebruikt. De standaardwaarde is 20 milliseconden. Stel deze parameter alleen in het bestand postgresql.conf of op de opdrachtregel van de server. Als u de instelling voor afzonderlijke tabellen wilt overschrijven, wijzigt u de parameters voor tabelopslag.|20 ms
autovacuum_nap_time|Hiermee geeft u de minimale vertraging op tussen het automatisch vacuüm dat op een bepaalde database wordt uitgevoerd. In elke ronde onderzoekt de daemon de database en geeft VACUÜM- en ANALYZE-opdrachten uit als dat nodig is voor tabellen in die database. De vertraging wordt gemeten in seconden, en de standaard is een minuut (1 min). Stel deze parameter alleen in het bestand postgresql.conf of op de opdrachtregel van de server.|15 s
autovacuum_max_workers|Hiermee geeft u het maximumaantal autovacuümprocessen op, met elkaar anders dan de autovacuumlauncher, die op elk gewenst moment kunnen worden uitgevoerd. De standaardinstelling is drie. Stel deze parameter alleen in bij het starten van de server.|3

Als u de instellingen voor afzonderlijke tabellen wilt overschrijven, wijzigt u de parameters voor tabelopslag. 

## <a name="autovacuum-cost"></a>Autovacuüm kosten
Hier zijn de "kosten" van het uitvoeren van een vacuüm operatie:

- De gegevenspagina's waarop het vacuüm wordt uitgevoerd, zijn vergrendeld.
- Rekenkracht en geheugen worden gebruikt wanneer een vacuümtaak wordt uitgevoerd.

Als gevolg hiervan, niet draaien vacuüm taken, hetzij te vaak of te zelden. Een vacuümtaak moet zich aanpassen aan de werkdruk. Test alle autovacuum parameter veranderingen als gevolg van de afwegingen van elk.

## <a name="autovacuum-start-trigger"></a>Autovacuum start trigger
Autovacuum wordt geactiveerd wanneer het aantal dode tuples groter is dan autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Hier, reltuples is een constante.

Opruimen van autovacuum moet gelijke tred houden met de database belasting. Anders u geen opslagruimte meer hebben en een algemene vertraging in query's ervaren. Afgeschreven na verloop van tijd, de snelheid waarmee een vacuüm operatie reinigt dode tuples moet gelijk zijn aan de snelheid waarmee dode tuples worden gemaakt.

Databases met veel updates en deletes hebben meer dode tuples en hebben meer ruimte nodig. Over het algemeen profiteren databases met veel updates en verwijderingen van lage waarden van autovacuum_vacuum_scale_factor en autovacuum_vacuum_threshold. De lage waarden voorkomen langdurige ophoping van dode tuples. U hogere waarden gebruiken voor beide parameters met kleinere databases omdat de noodzaak voor stofzuigen minder dringend is. Frequent stofzuigen gaat ten koste van rekenkracht en geheugen.

De standaard schaalfactor van 20 procent werkt goed op tabellen met een laag percentage dode tuples. Het werkt niet goed op tafels met een hoog percentage dode tuples. Bijvoorbeeld, op een 20-GB tabel, deze schaalfactor vertaalt naar 4 GB dode tuples. Op een 1-TB tafel, het is 200 GB dode tuples.

Met PostgreSQL u deze parameters instellen op tabelniveau of instantieniveau. Vandaag u deze parameters alleen op tabelniveau instellen in Azure Database voor PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Schatting van de kosten van autovacuum
Het uitvoeren van autovacuüm is "kostbaar", en er zijn parameters voor het regelen van de runtime van vacuümbewerkingen. De volgende parameters helpen bij het schatten van de kosten van het uitvoeren van vacuüm:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Het vacuümproces leest fysieke pagina's en controleert op dode tuples. Elke pagina in shared_buffers wordt beschouwd als een kostprijs van 1 (vacuum_cost_page_hit). Alle andere pagina's worden beschouwd als een kostprijs van 20 (vacuum_cost_page_dirty), als dode tuples bestaan, of 10 (vacuum_cost_page_miss), als er geen dode tuples bestaan. De vacuümbewerking stopt wanneer het proces de autovacuum_vacuum_cost_limit overschrijdt. 

Nadat de limiet is bereikt, wordt het proces gedurende de duur bepaald door de parameter autovacuum_vacuum_cost_delay voordat deze opnieuw begint. Als de limiet niet is bereikt, wordt automatisch vacuüm gestart na de waarde die is opgegeven door de parameter autovacuum_nap_time.

Samengevat bepalen de autovacuum_vacuum_cost_delay- en autovacuum_vacuum_cost_limit parameters hoeveel gegevens opruimen per tijdseenheid is toegestaan. Houd er rekening mee dat de standaardwaarden te laag zijn voor de meeste prijsniveaus. De optimale waarden voor deze parameters zijn afhankelijk van de prijsniveau en moeten dienovereenkomstig worden geconfigureerd.

De parameter autovacuum_max_workers bepaalt het maximum aantal autovacuümprocessen dat gelijktijdig kan worden uitgevoerd.

Met PostgreSQL u deze parameters instellen op tabelniveau of instantieniveau. Vandaag u deze parameters alleen op tabelniveau instellen in Azure Database voor PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Automatisch vacuüm per tabel optimaliseren
U alle vorige configuratieparameters per tabel configureren. Hier volgt een voorbeeld:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum is een synchroon proces per tafel. Hoe groter het percentage dode tuples dat een tabel heeft, hoe hoger de "kosten" om autovacuum. U tabellen met een hoog aantal updates splitsen en in meerdere tabellen worden verwijderd. Het splitsen van tabellen helpt om autovacuum te paralleliseren en de "kosten" te verminderen om autovacuum op één tafel te voltooien. U ook het aantal parallelle autovacuum werknemers om ervoor te zorgen dat werknemers zijn royaal gepland.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende PostgreSQL-documentatie voor meer informatie over het gebruik en het afstemmen van automatisch vacuüm:

 - [Hoofdstuk 18, Serverconfiguratie](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [Hoofdstuk 24, Onderhoudstaken van routinegegevens](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
