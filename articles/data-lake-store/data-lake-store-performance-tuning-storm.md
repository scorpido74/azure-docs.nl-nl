---
title: Richtlijnen voor prestatieafstemming azure Data Lake Storage Gen1 Storm | Microsoft Documenten
description: Richtlijnen voor prestatieafstemming azure Data Lake Storage Gen1 Storm
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 8066a759cf80be6e9ca232bcd3693a5fa4d2f2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436474"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Richtlijnen voor prestatieafstemming voor Storm op HDInsight en Azure Data Lake Storage Gen1

Begrijp de factoren waarmee rekening moet worden gehouden wanneer u de prestaties van een Azure Storm-topologie afstemt. Bijvoorbeeld, het is belangrijk om de kenmerken van het werk gedaan door de tuiten en de bouten (of het werk is I / O of geheugen intensief) te begrijpen. Dit artikel behandelt een reeks richtlijnen voor het afstemmen van prestaties, waaronder het oplossen van veelvoorkomende problemen.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure Data Lake Storage Gen1-account**. Zie [Aan de slag met Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)voor instructies over het maken van een account.
* **Een Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen1-account. Zie [Een HDInsight-cluster maken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Zorg ervoor dat u Extern bureaublad inschakelt voor het cluster.
* **Een cluster van Storm uitvoeren op Data Lake Storage Gen1**. Zie [Storm op HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview)voor meer informatie.
* **Richtlijnen voor prestatieafstemming voor Data Lake Storage Gen1**.  Zie Data Lake [Storage Gen1 Performance Tuning Guidance](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)voor algemene prestatieconcepten.  

## <a name="tune-the-parallelism-of-the-topology"></a>Het parallellisme van de topologie afstemmen

U de prestaties mogelijk verbeteren door de gelijktijdigheid van de I/O van en naar Data Lake Storage Gen1 te verhogen. Een stormtopologie heeft een reeks configuraties die het parallellisme bepalen:
* Aantal werkprocessen (de werknemers zijn gelijkmatig verdeeld over de VM's).
* Aantal exemplaren van de tuituitvoerder.
* Aantal gevallen van boutuitvoerder.
* Aantal tuittaken.
* Aantal bouttaken.

Overweeg bijvoorbeeld op een cluster met 4 VM's en 4 werkprocessen, 32 tuituitvoerders en 32 tuittaken en 256 boutuitvoerders en 512 bouttaken het volgende:

Elke supervisor, dat is een werknemer knooppunt, heeft een enkele werknemer Java virtuele machine (JVM) proces. Dit JVM-proces beheert 4 uitloopdraden en 64 boutdraden. Binnen elke thread worden taken opeenvolgend uitgevoerd. Met de vorige configuratie heeft elke uitloopdraad 1 taak en elke boutdraad heeft 2 taken.

In Storm, hier zijn de verschillende componenten betrokken, en hoe ze van invloed op het niveau van parallellisme je hebt:
* Het hoofdknooppunt (Nimbus in Storm genoemd) wordt gebruikt om taken in te dienen en te beheren. Deze knooppunten hebben geen invloed op de mate van parallellisme.
* De supervisor knooppunten. In HDInsight komt dit overeen met een Azure VM voor een werknemersknooppunt.
* De werktaken zijn Stormprocessen die worden uitgevoerd in de VM's. Elke werktaak komt overeen met een JVM-exemplaar. Storm verdeelt het aantal werkprocessen dat u opgeeft zo gelijkmatig mogelijk naar de werknemersknooppunten.
* Tout- en boutexecutor-instanties. Elke executeurinstantie komt overeen met een thread die binnen de werknemers wordt uitgevoerd (JVM's).
* Stormtaken. Dit zijn logische taken die elk van deze threads uitvoeren. Dit verandert niets aan het niveau van parallellisme, dus u moet evalueren of u meerdere taken per executeur nodig hebt of niet.

### <a name="get-the-best-performance-from-data-lake-storage-gen1"></a>Profiteer van de beste prestaties van Data Lake Storage Gen1

Wanneer u met Data Lake Storage Gen1 werkt, krijgt u de beste prestaties als u het volgende doet:
* Samensmelten uw kleine voegtin grotere maten (idealiter 4 MB).
* Doe zoveel gelijktijdige verzoeken als je. Omdat elke bout draad doet blokkeren leest, wilt u ergens in het bereik van 8-12 draden per kern. Dit houdt de NIC en de CPU goed gebruikt. Een grotere VM maakt meer gelijktijdige aanvragen mogelijk.  

### <a name="example-topology"></a>Voorbeeldtopologie

Stel dat u een cluster met een cluster met een D13v2 Azure VM hebt. Deze VM heeft 8 cores, dus onder de 8 worker nodes heb je in totaal 64 cores.

Laten we zeggen dat we doen 8 bout draden per kern. Gezien 64 cores, dat betekent dat we willen 512 totale bout executor exemplaren (dat wil zeggen, threads). In dit geval, laten we zeggen dat we beginnen met een JVM per VM, en vooral gebruik maken van de draad gelijktijdigheid binnen de JVM om gelijktijdigheid te bereiken. Dat betekent dat we 8 werktaken (één per Azure VM) en 512 boutuitvoerders nodig hebben. Gezien deze configuratie probeert Storm de werknemers gelijkmatig te verdelen over werknemersknooppunten (ook wel supervisorknooppunten genoemd), waardoor elke werknemer node 1 JVM krijgt. Nu binnen de toezichthouders, Storm probeert om de uitvoerders gelijkmatig verdelen tussen toezichthouders, waardoor elke supervisor (dat wil zeggen, JVM) 8 threads elk.

## <a name="tune-additional-parameters"></a>Aanvullende parameters afstemmen
Nadat u de basistopologie hebt, u overwegen of u een van de parameters wilt aanpassen:
* **Aantal JVM's per werknemersknooppunt.** Als u een grote gegevensstructuur (bijvoorbeeld een opzoektabel) hebt die u host in het geheugen, heeft elke JVM een afzonderlijke kopie nodig. U de gegevensstructuur ook in veel threads gebruiken als u minder JVM's hebt. Voor de i/o van de bout maakt het aantal JVM's niet zo veel verschil als het aantal threads dat over die JVM's wordt toegevoegd. Voor de eenvoud is het een goed idee om één JVM per werknemer te hebben. Afhankelijk van wat uw bout doet of welke applicatieverwerking u nodig hebt, moet u dit nummer mogelijk wijzigen.
* **Aantal tuituitvoerders.** Omdat het voorgaande voorbeeld bouten gebruikt om naar Data Lake Storage Gen1 te schrijven, is het aantal uitlooppunten niet direct relevant voor de boutprestaties. Echter, afhankelijk van de hoeveelheid verwerking of I / O gebeurt in de tuit, is het een goed idee om de tuiten af te stemmen voor de beste prestaties. Zorg ervoor dat je genoeg tuiten hebt om de bouten bezig te houden. De uitgangssnelheden van de uitste moeten overeenkomen met de doorvoer van de bouten. De werkelijke configuratie is afhankelijk van de tuit.
* **Aantal taken.** Elke bout loopt als een enkele draad. Extra taken per bout bieden geen extra gelijktijdigheid. De enige keer dat ze van voordeel zijn is als uw proces van de erkenning van de tuple neemt een groot deel van uw bout uitvoeringstijd. Het is een goed idee om veel tuples te groeperen in een grotere toetewijze voordat u een bevestiging van de bout stuurt. Dus, in de meeste gevallen, meerdere taken bieden geen extra voordeel.
* **Lokale of shuffle groepering.** Wanneer deze instelling is ingeschakeld, worden tuples verzonden naar bouten binnen hetzelfde werkproces. Dit vermindert interprocescommunicatie en netwerkgesprekken. Dit wordt aanbevolen voor de meeste topologieën.

Dit basisscenario is een goed uitgangspunt. Test met uw eigen gegevens om de voorgaande parameters aan te passen om optimale prestaties te bereiken.

## <a name="tune-the-spout"></a>Stem de tuit af

U de volgende instellingen wijzigen om de uitloop af te stemmen.

- **Tuple time-out: topology.message.timeout.secs**. Deze instelling bepaalt de tijd die een bericht nodig heeft om te voltooien en ontvangt bevestiging voordat het als mislukt wordt beschouwd.

- **Maximaal geheugen per werkproces: worker.childopts**. Met deze instelling u extra opdrachtregelparameters opgeven aan de Java-werknemers. De meest gebruikte instelling hier is XmX, die het maximale geheugen dat aan de heap van een JVM is toegewezen, bepaalt.

- **Max tuit in afwachting: topology.max.tout.pending**. Deze instelling bepaalt het aantal tuples dat kan worden in de vlucht (nog niet erkend op alle knooppunten in de topologie) per tuit draad op elk gewenst moment.

  Een goede berekening te doen is het schatten van de grootte van elk van uw tuples. Zoek dan uit hoeveel geheugen een uitloop draad heeft. Het totale geheugen dat aan een thread is toegewezen, gedeeld door deze waarde, moet u de bovengrens geven voor de maximale uitloop in afwachting van parameter.

## <a name="tune-the-bolt"></a>Stem de bout af
Wanneer u naar Data Lake Storage Gen1 schrijft, stelt u een synchronisatiebeleid voor grootte (buffer aan de clientzijde) in op 4 MB. Een spoel- of hsync() wordt dan alleen uitgevoerd wanneer de buffergrootte de waarde bij deze waarde is. Het Data Lake Storage Gen1-stuurprogramma op de worker VM doet deze buffering automatisch, tenzij u expliciet een hsync() uitvoert.

De standaard Data Lake Storage Gen1 Storm-bout heeft een parameter voor het synchronisatiebeleid (fileBufferSize) die kan worden gebruikt om deze parameter af te stemmen.

In I/O-intensieve topologieën is het een goed idee om elke boutdraad naar zijn eigen bestand te laten schrijven en een bestandsrotatiebeleid (fileRotationSize) in te stellen. Wanneer het bestand een bepaalde grootte bereikt, wordt de stream automatisch gespoeld en wordt er een nieuw bestand naar geschreven. De aanbevolen bestandsgrootte voor rotatie is 1 GB.

### <a name="handle-tuple-data"></a>Tuple-gegevens verwerken

In Storm, een tuit houdt op een tuple totdat het expliciet wordt erkend door de bout. Als een tuple is gelezen door de bout, maar is nog niet erkend, de tuit misschien niet hebben aangehouden in Data Lake Storage Gen1 back-end. Na een tuple wordt erkend, kan de tuit worden gegarandeerd persistentie door de bout, en kan vervolgens verwijderen van de brongegevens uit welke bron het leest uit.  

Voor de beste prestaties op Data Lake Storage Gen1, hebben de bout buffer 4 MB van tuple gegevens. Schrijf vervolgens naar de Data Lake Storage Gen1 back-end als een 4-MB schrijven. Nadat de gegevens met succes naar de winkel zijn geschreven (door hflush()aan te roepen), kan de bout de gegevens terug naar de tuit bevestigen. Dit is wat het voorbeeld bout hier geleverd doet. Het is ook aanvaardbaar om een groter aantal tuples te houden voordat de hflush() oproep wordt gedaan en de tuples erkend. Echter, dit verhoogt het aantal tuples tijdens de vlucht dat de tuit moet houden, en dus verhoogt de hoeveelheid geheugen die nodig is per JVM.

> [!NOTE]
> Toepassingen kunnen een vereiste hebben om tuples vaker te erkennen (bij gegevensformaten van minder dan 4 MB) om andere niet-prestatiegerichte redenen. Dit kan echter gevolgen hebben voor de I/O-doorvoer naar de back-end van de opslag. Weeg deze afweging zorgvuldig af tegen de I/O-prestaties van de bout.

Als de inkomende snelheid van tuples niet hoog is, zodat de 4-MB buffer duurt een lange tijd te vullen, overweeg dan te beperken dit door:
* Het verminderen van het aantal bouten, dus er zijn minder buffers te vullen.
* Het hebben van een time-based of count-based beleid, waarbij een hflush() wordt geactiveerd elke x flushes of elke y milliseconden, en de tuples verzameld tot nu toe worden erkend terug.

Houd er rekening mee dat de doorvoer in dit geval lager is, maar met een langzame snelheid van gebeurtenissen, maximale doorvoer is niet de grootste doelstelling toch. Deze oplossingen helpen u de totale tijd die nodig is voor een tuple te stromen door naar de winkel te verminderen. Dit kan er toe doen als u een real-time pijplijn wilt, zelfs met een laag gebeurtenispercentage. Houd er ook rekening mee dat als uw inkomende tuple-snelheid laag is, u de parameter topology.message.timeout_secs moet aanpassen, zodat de tuples geen time-out hebben terwijl ze worden gebufferd of verwerkt.

## <a name="monitor-your-topology-in-storm"></a>Houd uw topologie in Storm in de gaten  
Terwijl uw topologie wordt uitgevoerd, u deze controleren in de gebruikersinterface van Storm. Hier zijn de belangrijkste parameters om naar te kijken:

* **Totale procesuitvoeringlatentie.** Dit is de gemiddelde tijd die een tuple neemt om te worden uitgestoten door de tuit, verwerkt door de bout, en erkend.

* **Totale latentie van het boutproces.** Dit is de gemiddelde tijd doorgebracht door de tuple op de bout totdat het een bevestiging ontvangt.

* **Totale bout uitvoeren latentie.** Dit is de gemiddelde tijd die de bout in de uitvoermethode doorgeeft.

* **Aantal mislukkingen.** Dit verwijst naar het aantal tuples dat niet volledig is verwerkt voordat ze een time-out hebben.

* **Capaciteit.** Dit is een maat voor hoe druk uw systeem is. Als dit nummer 1 is, werken uw bouten zo snel als ze kunnen. Als het minder dan 1 is, verhoog dan het parallellisme. Als het groter is dan 1, vermindert het parallellisme.

## <a name="troubleshoot-common-problems"></a>Veelvoorkomende problemen oplossen
Hier volgen een paar veelvoorkomende scenario's voor het oplossen van problemen.
* **Veel tuples zijn timing uit.** Kijk naar elk knooppunt in de topologie om te bepalen waar het knelpunt zich bevindt. De meest voorkomende reden hiervoor is dat de bouten niet in staat zijn om gelijke tred te houden met de tuiten. Dit leidt tot tuples verstopping van de interne buffers in afwachting van worden verwerkt. Overweeg de time-outwaarde te verhogen of de max uitloop in behandeling te verlagen.

* **Er is een hoge totale procesuitvoering latentie, maar een lage bout proces latentie.** In dit geval is het mogelijk dat de tuples niet snel genoeg worden erkend. Controleer of er voldoende erkenningen zijn. Een andere mogelijkheid is dat ze te lang in de rij staan voordat de bouten ze gaan verwerken. Verlaag de maximale uitloop in behandeling.

* **Er is een hoge bout uit te voeren latentie.** Dit betekent dat de uitvoer() methode van uw bout te lang duurt. Optimaliseer de code of bekijk schrijfformaten en spoelgedrag.

### <a name="data-lake-storage-gen1-throttling"></a>Data Lake Storage Gen1 throttling
Als u de bandbreedtelimieten van Data Lake Storage Gen1 bereikt, ziet u mogelijk taakfouten. Controleer taaklogboeken op beperkingsfouten. U het parallellisme verminderen door de containergrootte te vergroten.    

Als u wilt controleren of u wordt gewurgd, schakelt u de foutopsporingslogboekregistratie aan de clientzijde in:

1. In **Ambari** > **Storm** > **Config** > Advanced**storm-worker-log4j**wijzigt u ** &lt;rootlevel="info"&gt; ** in ** &lt;root level="debug".&gt;** Start alle knooppunten/service opnieuw om de configuratie van kracht te laten worden.
2. Controleer de stormtopologielogboeken op werknemersknooppunten (onder&lt;/var/log/storm/worker-artifacts/ TopologyName-poort&gt;/&lt;&gt;/worker.log) voor uitzonderingen op Data Lake Storage Gen1 throttling.

## <a name="next-steps"></a>Volgende stappen
Extra prestaties tuning voor Storm kan worden verwezen in [deze blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Zie deze voor een extra voorbeeld [op GitHub](https://github.com/hdinsight/storm-performance-automation).
