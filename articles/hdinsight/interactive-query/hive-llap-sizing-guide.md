---
title: Aanpassings gids voor LLAP (HDInsight Interactive query cluster)
description: LLAP-formaat gids
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: a9b86f09ade0d437436779ef3e4a17fcdede2cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83664961"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Azure HDInsight Interactive query-cluster (Hive LLAP) formaat gids

In dit document wordt de grootte van het HDInsight Interactive query-cluster (Hive LLAP-cluster) beschreven voor een typische werk belasting om redelijke prestaties te behalen. Houd er rekening mee dat de aanbevelingen die in dit document worden gegeven algemene richt lijnen en specifieke werk belastingen mogelijk   specifieke afstemming nodig hebben.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Standaard VM-typen van Azure voor HDInsight Interactive query cluster (LLAP)**

| Knooppunt type      | Exemplaar | Grootte     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vcpu's, 56 GB RAM, 400 GB SSD   |
| Werk   | **D14 v2**        | **16 vcpu's, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 vcpu's, 8 GB RAM, 40 GB SSD       |

***Opmerking: alle aanbevolen configuratie waarden zijn gebaseerd op het werk knooppunt van het D14 v2-type***  

### <a name="configuration"></a>**Configuratie**    
| Configuratiesleutel      | Aanbevolen waarde  | Beschrijving |
| :---        |    :----:   | :---     |
| garens. nodemanager. resource. geheugen-MB | 102400 (MB) | Totale hoeveelheid geheugen in MB, voor alle GARENs in een knoop punt | 
| garens. scheduler. maximum-toewijzing-MB | 102400 (MB) | De maximale toewijzing voor elke container aanvraag op de RM, in MB. Geheugen aanvragen die hoger zijn dan deze waarde, worden niet van kracht |
| garens. scheduler. maximum-toewijzing-vcores | 12 |Het maximum aantal CPU-kernen voor elke container aanvraag in de Resource Manager. Aanvragen die hoger zijn dan deze waarde, worden niet van kracht. |
| garens. nodemanager. resource. CPU-vcores | 12 | Aantal CPU-kernen per NodeManager dat voor containers kan worden toegewezen. |
| garens. scheduler. capacity. root. llap. capacity | 80 (%) | Toewijzing van garen capaciteit voor llap-wachtrij  |
| TEZ. am. resource. Memory. MB | 4096 (MB) | De hoeveelheid geheugen in MB die moet worden gebruikt door de TEZ AppMaster |
| Hive. server2. TEZ. Sessions. per. default. Queue | <number_of_worker_nodes> |Het aantal sessies voor elke wachtrij met de naam in de component. server2. TEZ. default. queues. Dit aantal komt overeen met het aantal query-coördinatoren (TEZ AMs) |
| component. TEZ. container. size | 4096 (MB) | Opgegeven TEZ-container grootte in MB |
| hive.llap.daemon.num.executors | 12 | Aantal uitvoerender per LLAP-daemon | 
| Hive. llap. io. thread pool. grootte | 12 | Grootte van thread pool voor uitvoerendeers |
| Hive. llap. daemon. garens. container. MB | 77824 (MB) | Totaal geheugen in MB dat wordt gebruikt door afzonderlijke LLAP-daemons (geheugen per daemon)
| Hive. llap. io. Memory. size | 235520 (MB) | Cache grootte in MB per LLAP-daemon van SSD-cache is ingeschakeld |
| component. auto. Convert. samen. noconditionaltask. size | 2048 (MB) | geheugen grootte in MB to do koppelings koppeling |

### <a name="llap-daemon-size-estimations"></a>**Schattingen voor de grootte van LLAP-daemon:**  

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. de toewijzing van het totale aantal GARENs in een knoop punt bepalen**    
Configuratie: ***garens. nodemanager. resource. geheugen-MB***  

Deze waarde wijst op een maximum hoeveelheid geheugen in MB die kan worden gebruikt door de garen containers op elk knoop punt. De opgegeven waarde moet kleiner zijn dan de totale hoeveelheid fysiek geheugen op het knoop punt.   
Totaal geheugen voor alle GARENs op een knoop punt = [totaal fysiek geheugen] – [geheugen voor OS + andere services]  
Stel deze waarde in op ~ 90% van de beschik bare RAM-grootte.  
Voor D14 v2 is de aanbevolen waarde **102400 MB**. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. het bepalen van de maximale hoeveelheid geheugen per garen-container aanvraag**  
Configuratie: ***garens. scheduler. maximum-toewijzing-MB***

Met deze waarde wordt de maximale toewijzing aangegeven voor elke container aanvraag in de Resource Manager, in MB. Geheugen aanvragen die hoger zijn dan de opgegeven waarde, worden niet van kracht. De Resource Manager kan geheugen toewijzen aan containers in stappen van *garens. scheduler. minimum-toewijzing-MB* en mag niet groter zijn dan de grootte die is opgegeven door *garens. scheduler. maximum-toewijzing-MB*. De opgegeven waarde mag niet groter zijn dan het totale gegeven geheugen voor alle containers op het knoop punt dat is opgegeven door *garens. nodemanager. resource. geheugen-MB*.    
Voor D14 v2-worker-knoop punten is de aanbevolen waarde **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. het bepalen van de maximale hoeveelheid vcores per garen-container aanvraag**  
Configuratie: ***garens. scheduler. maximum-toewijzing-vcores***  

Deze waarde geeft het maximum aantal virtuele CPU-kernen aan voor elke container aanvraag in de Resource Manager. Als u een hoger aantal vcores wilt aanvragen, wordt deze waarde niet van kracht. Het is een wereld wijde eigenschap van de garen planner. Voor de LLAP-daemon-container kan deze waarde worden ingesteld op 75% van de totale beschik bare vcores. De resterende 25% moet worden gereserveerd voor NodeManager, DataNode en andere services die worden uitgevoerd op de worker-knoop punten.  
Er zijn 16 vcores op D14 v2-Vm's en 75% van het totale aantal 16 vcores kunnen worden gebruikt door de LLAP daemon-container.  
Voor D14 v2 is de aanbevolen waarde **12**.  

#### <a name="4-number-of-concurrent-queries"></a>**4. aantal gelijktijdige query's**  
Configuratie: ***Hive. server2. TEZ. Sessions. per. default. Queue***

Deze configuratie waarde bepaalt het aantal TEZ-sessies dat parallel kan worden gestart. Deze TEZ-sessies worden gestart voor elk van de wacht rijen die zijn opgegeven met de component. server2. TEZ. default. queues. Dit komt overeen met het aantal TEZ-AMs (query-coördinatoren). Het is raadzaam hetzelfde te zijn als het aantal worker-knoop punten. Het aantal TEZ-AMs kan hoger zijn dan het aantal LLAP-daemon-knoop punten. De primaire verantwoordelijkheid van TEZ is het coördineren van de uitvoering van de query en het toewijzen van query plan fragmenten aan bijbehorende LLAP-daemons voor uitvoering. Bewaar deze waarde als een veelvoud van een aantal LLAP-daemon-knoop punten om een hogere door voer te krijgen.  

Het standaard HDInsight-cluster bevat vier LLAP-daemons die worden uitgevoerd op vier worker-knoop punten, dus de aanbevolen waarde is **4**.  

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. TEZ-container en TEZ-toepassings hoofd grootte**    
Configuratie: ***TEZ. am. resource. Memory. MB, component. TEZ. container. size***  

*TEZ. am. resource. Memory. MB* : Hiermee wordt de grootte van de TEZ-toepassings Master gedefinieerd.  
De aanbevolen waarde is **4096 MB**.
   
*Hive. TEZ. container. size* : Hiermee definieert u de hoeveelheid geheugen die is opgegeven voor de TEZ-container. Deze waarde moet worden ingesteld tussen de minimale container grootte van het garen (*garens. scheduler. minimum-toewijzings MB*) en de maximale container grootte van de garens (*garens. scheduler. maximum-toewijzing-MB*). De LLAP daemon-uitvoeringen gebruiken deze waarde voor het beperken van het geheugen gebruik per uitvoerder.  
De aanbevolen waarde is **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. toewijzing van LLAP-wachtrij capaciteit**   
Configuratie: ***garens. scheduler. capacity. root. llap. capacity***  

Met deze waarde wordt een percentage van de capaciteit aangegeven dat is opgegeven voor de llap-wachtrij. De capaciteits toewijzingen kunnen verschillende waarden voor verschillende werk belastingen hebben, afhankelijk van de configuratie van de garen-wacht rijen. Als uw werk belasting alleen-lezen is, stelt u deze in op Maxi maal 90% van de capaciteit zou moeten werken. Als uw werk belasting echter een combi natie is van update/delete/merge-bewerkingen met behulp van beheerde tabellen, is het raadzaam om 80% van de capaciteit voor llap-wachtrij te geven. De resterende capaciteit van 20% kan worden gebruikt door andere taken, zoals compressie, enzovoort om containers toe te wijzen vanuit de standaard wachtrij. Op die manier kunnen de taken in de standaard wachtrij geen GARENs meer afnemen.    
Voor D14v2 worker-knoop punten is de aanbevolen waarde voor de llap-wachtrij **80**.   
(Voor ReadOnly-workloads kan het worden verhoogd tot 90 als geschikt.)  

#### <a name="7-llap-daemon-container-size"></a>**7. LLAP daemon-container grootte**    
Configuratie: ***Hive. llap. daemon. garens. container. MB***  
   
LLAP-daemon wordt uitgevoerd als een GARENve container op elk worker-knoop punt. De totale geheugen grootte voor de LLAP-daemon-container is afhankelijk van de volgende factoren:    
*  Configuraties van de grootte van de garen container (garens. scheduler. minimum-toewijzing-MB, garens. scheduler. maximum-toewijzing-MB, garens. nodemanager. resource. geheugen-MB)
*  Aantal TEZ-AMs op een knoop punt
*  Totaal geheugen geconfigureerd voor alle containers op een knoop punt en LLAP-wachtrij capaciteit  

Het geheugen dat nodig is voor TEZ Application Masters (TEZ AM) kan als volgt worden berekend.  
Voor het interactieve HDInsight-cluster is er standaard één TEZ per worker-knoop punt dat fungeert als een query coördinator. Het aantal TEZ AMs kan worden geconfigureerd op basis van een aantal gelijktijdige query's die moeten worden geleverd.
Het is raadzaam 4 GB geheugen per TEZ uur te hebben.

TEZ AM Memory per knoop punt = [ceil (aantal TEZ AMs/aantal LLAP-daemon-knoop punten)] x [TEZ AM container size]  
Voor D14 v2 heeft de standaard configuratie vier TEZ-AMs en vier LLAP-daemon-knoop punten.  
TEZ AM-geheugen per knoop punt = (CEIL (4/4) x 4 GB) = 4 GB

Het totale beschik bare geheugen voor de LLAP-wachtrij per werk knooppunt kan als volgt worden berekend: deze waarde is afhankelijk van de totale hoeveelheid geheugen die beschikbaar is voor alle GARENs in een knoop punt (*garens. nodemanager. resource. geheugen-MB*) en het percentage capaciteit dat is geconfigureerd voor de LLAP-wachtrij (*garens. scheduler. capacity. root. LLAP. capacity*)  
Totaal geheugen voor LLAP-wachtrij op worker-knoop punt = totaal geheugen beschikbaar voor alle GARENs van een knoop punt x capaciteit voor LLAP-wachtrij.  
Voor D14 v2 is deze waarde [100 GB x 0,80] = 80 GB.

De grootte van de LLAP-daemon-container wordt als volgt berekend:

**Grootte van LLAP-daemon-container = [totale hoeveelheid geheugen die beschikbaar is voor LLAP-wachtrij] – [TEZ AM Memory per knoop punt]**  
    
Voor D14 v2 worker-knoop punt HDI 4,0-de aanbevolen waarde is (80 GB-4 GB)) = **76 GB**   
(HDI 3,6, aanbevolen waarde is **74 GB** , omdat u extra moet reserveren ~ 2 GB voor schuifregelaar am.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. bepalen van het aantal uitvoerender per LLAP-daemon**  
Configuratie: ***hive.llap.daemon.num.executors***, ***Hive. llap. io. thread pool. size***

***hive.llap.daemon.num.executors***:   
Deze configuratie bepaalt het aantal uitvoerender dat taken parallel kan uitvoeren per LLAP-daemon. Deze waarde is afhankelijk van het aantal vcores, de hoeveelheid geheugen die is opgegeven per uitvoerder en de hoeveelheid geheugen die beschikbaar is voor LLAP daemon. Normaal gesp roken hebben we deze waarde zo dicht mogelijk bij het aantal vcores.
Er zijn 16 vcores op D14 v2-Vm's. Niet alle vcores kunnen echter worden uitgevoerd omdat andere services, zoals NodeManager, DataNode, metrische gegevens, ook een deel van de beschik bare vcores nodig hebben. 

Als u het aantal uitvoerender wilt aanpassen, kunt u het beste 4 GB geheugen per uitvoerder overwegen, zoals opgegeven door *Hive. TEZ. container. size* en ervoor te zorgen dat het totale geheugen dat nodig is voor alle uitvoerers, niet groter is dan het totale geheugen dat beschikbaar is voor de LLAP daemon-container.  
Deze waarde kan Maxi maal 75% van het totale aantal beschik bare vcores op dat knoop punt worden geconfigureerd.  
Voor D14 v2 is de aanbevolen waarde (. 75 X 16) = **12**

***Hive. llap. io. thread pool. grootte***:   
Met deze waarde wordt de thread pool grootte voor uitvoerende toepassingen opgegeven. Omdat de uitvoeringen van de toepassing zijn hersteld, is deze hetzelfde als het aantal uitvoerender per LLAP-daemon.   
Voor D14 v2 is de aanbevolen waarde **12**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. de cache grootte van de LLAP-daemon bepalen**  
Configuratie: ***Hive. llap. io. Memory. size***

Het LLAP-daemon-container geheugen bestaat uit de volgende onderdelen:
*  Hoofd kamer
*  Heap-geheugen dat wordt gebruikt door de (xmx)
*  In-memory cache per daemon (de geheugen grootte van de buiten-heap, niet van toepassing wanneer de SSD-cache is ingeschakeld)
*  Grootte van meta gegevens in cache geheugen (alleen van toepassing wanneer SSD-cache is ingeschakeld)

Beschik bare **grootte**: deze grootte geeft een deel van een off-heap geheugen aan dat wordt gebruikt voor de overhead van Java-vm's (meta-ruimte, threads stack, GC-gegevens structuren enzovoort). Over het algemeen is deze overhead ongeveer 6% van de Heap-grootte (xmx). Om aan de veiligste zijde te zijn, kan deze waarde worden berekend als 6% van de totale grootte van het LLAP-daemon-geheugen.  
Voor D14 v2 is de aanbevolen waarde ceil (76 GB x 0,06) ~ = **5 GB**.  

**Heap-grootte (xmx)**: dit is de hoeveelheid geheugen die beschikbaar is voor alle uitvoerders.
Totale grootte van heap = aantal uitvoerender x 4 GB  
Voor D14 v2 is deze waarde 12 x 4 GB = **48 GB**  

Als SSD-cache is uitgeschakeld, is de in-memory cache de hoeveelheid geheugen die wordt overgelaten na het uitvallen van de grootte van de ruimte en de Heap-grootte van de LLAP-daemon-container grootte.

De berekening van de cache grootte wijkt af wanneer SSD-cache is ingeschakeld.
Als *Hive. llap. io. allocator. mmap* = True wordt ingesteld, wordt SSD-caching ingeschakeld.
Wanneer SSD-cache is ingeschakeld, wordt een gedeelte van het geheugen gebruikt voor het opslaan van meta gegevens voor de SSD-cache. De meta gegevens worden opgeslagen in het geheugen en er wordt naar verwachting ongeveer 10% van de SSD-cache grootte.   
Grootte van de meta gegevens van SSD-cache = [grootte van LLAP daemon-container]-[head room + Heap-grootte]  
Voor D14 v2, met HDI 4,0, de grootte van de meta gegevens in het geheugen van SSD cache = [76 GB]-[5 GB + 48 GB] = **23 GB**  
Voor D14 v2, met HDI 3,6, de grootte van de meta gegevens in het geheugen van SSD cache = [76 GB]-[5 GB + 48 GB + 2 GB schuif regelaar] = **21 GB**

Gezien de grootte van het beschik bare geheugen voor het opslaan van de meta gegevens van de SSD-cache, kunnen we de grootte van de SSD-cache berekenen die kan worden ondersteund.  
Grootte van de meta gegevens in het geheugen voor SSD-cache = 10% van de grootte van de SSD-cache       
Grootte van SSD-cache = grootte van de meta gegevens in het geheugen voor SSD-cache x 10  

Voor D14 v2 en HDI 4,0, de aanbevolen cache grootte van SSD = 23 GB x 10 = **230 GB**  
Voor D14 v2 en HDI 3,6, de aanbevolen cache grootte van SSD = 21 GB x 10 = **210 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. het geheugen voor het koppelen van kaarten aanpassen**   
Configuratie: ***component. auto. Convert. samen voegen. noconditionaltask. size***

Zorg ervoor dat u *component. auto. Convert. noconditionaltask* hebt ingeschakeld om deze para meter van kracht te laten worden.
Met deze configuratie kan de gebruiker de grootte opgeven van de tabellen die in het geheugen passen om de toewijzings koppeling uit te voeren. Als de som van de grootte van n-1-tabellen of-partities voor n-Way-samen voeging kleiner is dan de geconfigureerde waarde, wordt de kaart koppeling gekozen. De geheugen grootte van de LLAP-uitvoerder moet worden gebruikt voor het berekenen van de drempel waarde voor automatisch converteren naar kaart koppelen.
Er wordt van uitgegaan dat elke uitvoerder 4 GB aan Heap-grootte heeft, maar niet alle is beschikbaar voor toewijzings deelname. Er wordt een geheugen van een heap gebruikt voor sorteer buffers, wille keurige buffers, hash-tabellen, enzovoort. ook door andere bewerkingen. U kunt dus 50% van de geheugen ruimte van 4 GB voor de toewijzings koppeling opgeven.  
Opmerking: deze waarde moet mogelijk aanpassingen hebben die geschikt zijn voor uw werk belasting. Als u deze waarde te laag instelt, wordt de functie autoconvert niet gebruikt. En als u het te hoog instelt, kan dit leiden tot onvoldoende geheugen-uitzonde ringen of GC-onderbrekingen die kunnen leiden tot nadelige prestaties.  
Voor D14 v2, met een geheugen van 4 GB per uitvoerder, wordt aangeraden deze waarde in te stellen op **2048 MB**.


#### <a name="next-steps"></a>**Volgende stappen**
Als het probleem niet is opgelost met deze waarden, gaat u naar een van de volgende...

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).  

* ##### <a name="other-references"></a>**Andere verwijzingen:**
  * [Andere eigenschappen van LLAP configureren](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [De Heap-grootte van de Hive-server configureren](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Geheugen grootte van kaart koppelen voor LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Eigenschappen van TEZ-uitvoerings engine](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [LLAP grondige kennis van Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)  
