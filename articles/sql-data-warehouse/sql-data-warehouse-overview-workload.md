---
title: Meer informatie over Azure SQL Data Warehouse bewerkingen | Microsoft Docs
description: "In SQL Data Warehouse kunt u de rekencapaciteit naar wens vergroten, verkleinen of onderbreken door het aantal DWU's (Data Warehouse Units) aan te passen met een schuifregelaar. In dit artikel wordt beschreven welke datawarehouse-metrieken er zijn en hoe deze in verhouding staan tot het aantal DWU's. "
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 47e04cb6b58a185e84120fe73a8468834d2e57fd
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907929"
---
# <a name="data-warehouse-workload"></a>Datawarehouse-workload
De workload van een datawarehouse wordt gevormd door alle bewerkingen die op het desbetreffende datawarehouse worden uitgevoerd. De datawarehouse-workload omvat het hele proces van het laden van gegevens in het warehouse tot het uitvoeren van analyses en rapporten met betrekking tot het datawarehouse, het beheren van de gegevens in het datawarehouse en het exporteren van gegevens uit het datawarehouse. De omvang en complexiteit van dergelijke bewerkingen zijn vaak evenredig met de uitgebreidheid van het datawarehouse.

## <a name="new-to-data-warehousing"></a>Bent u niet bekend met datawarehousing?
Een datawarehouse is een verzameling gegevens die is geladen uit een of meer gegevensbronnen en wordt gebruikt voor Business Intelligence-taken zoals rapportages en analyses.

Op datawarehouses worden vaak query's uitgevoerd waarmee een groot aantal rijen of gegevensbereiken wordt gescand en relatief veel resultaten worden geretourneerd voor analyse- en rapportagedoeleinden. Bij datawarehouses worden meestal ook relatief veel gegevens geladen in plaats van ingevoegd, bijgewerkt of verwijderd.

* Een datawarehouse werkt het beste als de opslag van gegevens is geoptimaliseerd voor het uitvoeren van query's waarbij een groot aantal rijen of grote gegevensreeksen moeten worden gescand. Deze manier van scannen werkt het beste als de gegevens zijn opgeslagen en worden doorzocht per kolom in plaats van per rij.

> [!NOTE]
> De in-memory columnstore-index, de kolomopslagindex in het geheugen, biedt tot wel tien keer betere compressie en tot wel honderd keer betere prestaties dan traditionele binaire structuren voor query's voor rapportage en analyse. We beschouwen kolomopslagindexen als de norm voor het opslaan en scannen van grote hoeveelheden gegevens in een datawarehouse.
> 
> 

* Aan een datawarehouse worden andere eisen gesteld dan aan een systeem dat is geoptimaliseerd voor onlinetransactieverwerking (OLTP, Online Transaction Processing). In een OLTP-systeem worden veel gegevens ingevoegd, bijgewerkt en verwijderd. Bij deze bewerkingen wordt gezocht naar specifieke rijen in de tabel. Tabelzoekopdrachten werken het beste als de gegevens per rij zijn opgeslagen. De gegevens kunnen worden gesorteerd en snel worden doorzocht volgens een verdeel-en-heersstrategie die ook wel een binaire-structuur- of btree-zoekopdracht wordt genoemd.

## <a name="data-loading"></a>Gegevens laden
Een groot deel van de workload van een datawarehouse bestaat uit het laden van gegevens. Bedrijven hebben meestal een zwaar belast OLTP-systeem voor het bijhouden van wijzigingen als klanten gedurende de hele dag zakelijke transacties genereren. Deze transacties worden periodiek, vaak 's nachts tijdens een onderhoudsvenster, naar het datawarehouse verplaatst of gekopieerd. Als de gegevens eenmaal in het datawarehouse zijn opgenomen, kunnen analisten analyses uitvoeren en zakelijke beslissingen nemen op basis van die gegevens.

* Dit laadproces wordt vaak aangeduid met de term 'ETL' (Extract, Transform and Load: extraheren, transformeren en laden). Gegevens moeten gewoonlijk worden getransformeerd zodat deze consistent zijn met andere gegevens in het datawarehouse. Voorheen gebruikten bedrijven meestal specifieke ETL-servers om de transformaties uit te voeren. Maar omdat MPP (Massively Parallel Processing) zo snel is, kunt u de gegevens nu eerst in SQL Data Warehouse laden en daarna de transformaties uitvoeren. Dit proces wordt aangeduid met de term 'ELT' (Extract, Load and Transform: extraheren, laden en transformeren) en is hard op weg om de nieuwe norm voor datawarehouse-workload te worden.

> [!NOTE]
> Met SQL Server 2016 kunt u nu in realtime analyses uitvoeren op een OLTP-tabel. U hebt nog steeds een datawarehouse nodig voor het opslaan en analyseren van gegevens, maar het biedt wel mogelijkheden voor analyses in realtime.
> 
> 

### <a name="reporting-and-analysis-queries"></a>Query's voor rapportage en analyse
Query's voor rapportage en analyse worden vaak onderverdeeld in kleine, middelgrote en grote query's op basis van een aantal criteria. Meestal gebeurt dit op basis van tijd. In de meeste datawarehouses bestaat de workload uit een combinatie van kort- en langlopende query's. Het is in elk geval belangrijk om de verdeling en frequentie van de query's (elk uur, dagelijks, einde van de maand, einde van het kwartaal, enzovoort) te bepalen . Alleen met een goed inzicht in de gemengde workload van query's, in combinatie met de gelijktijdigheid van taken, kan de juiste capaciteit voor een datawarehouse worden gepland.

* Het plannen van de capaciteit kan erg ingewikkeld zijn bij een workload met gemengde query's, vooral wanneer u veel aanlooptijd voor het toevoegen van capaciteit aan het datawarehouse nodig hebt. Met SQL Data Warehouse verdwijnt de noodzaak tot het plannen van capaciteit, omdat u de rekencapaciteit op elk gewenst moment kunt vergroten of verkleinen en de opslag- en rekencapaciteit afzonderlijk kunnen worden aangepast.

### <a name="data-management"></a>Gegevensbeheer
Gegevensbeheer is belangrijk, met name om te weten wanneer u mogelijk niet meer over voldoende schijfruimte beschikt in de nabije toekomst. Gegevens worden in datawarehouses gewoonlijk ingedeeld in logische bereiken, die worden opgeslagen als partities in een tabel. In alle producten op basis van SQL Server kunt u partities in en uit de tabel verplaatsen. Zo kunt u oudere gegevens naar minder dure opslag verplaatsen en de meest recente gegevens beschikbaar houden voor online-opslag.

* Kolomopslagindexen bieden ondersteuning voor gepartitioneerde tabellen. Bij kolomopslagindexen worden gepartitioneerde tabellen gebruikt voor het beheren en archiveren van gegevens. Bij tabellen die per rij zijn opgeslagen, spelen partities een grotere rol in de prestaties van query's.  
* PolyBase speelt een belangrijke rol bij het beheren van gegevens. Met PolyBase hebt u de mogelijkheid om oudere gegevens te archiveren naar Hadoop- of Azure Blob-opslag.  Dit biedt veel mogelijkheden omdat de gegevens dan nog steeds online zijn.  Het duurt misschien wat langer om gegevens op te halen uit Hadoop, maar de langere ophaaltijd weegt mogelijk wel op tegen de lagere opslagkosten.

### <a name="exporting-data"></a>Gegevens exporteren
Een manier om gegevens beschikbaar te maken voor analyse en rapportage is om gegevens uit het datawarehouse te verzenden naar specifiek toegewezen servers voor het uitvoeren van rapporten en analyses. Deze servers worden datamarts genoemd. U kunt bijvoorbeeld rapportgegevens vooraf verwerken en ze vervolgens vanuit het datawarehouse exporteren naar verschillende servers over de hele wereld om ze algemeen beschikbaar te maken voor klanten en analisten.

* Voor het genereren van rapporten kunt u elke nacht alleen-lezen rapportageservers vullen met een momentopname van de gegevens van die dag. Dit resulteert in een hogere bandbreedte voor klanten, terwijl er ook minder rekencapaciteit is vereist voor het datawarehouse. Als bijkomend beveiligingsvoordeel zijn er bij het gebruik van datamarts ook minder gebruikers met toegangsrechten voor het datawarehouse nodig.
* Voor analyses kunt u een analysekubus van het datawarehouse maken en analyses op het datawarehouse uitvoeren, of gegevens vooraf verwerken en naar de analyseserver exporteren voor verdere analyse.

## <a name="next-steps"></a>Volgende stappen
Nu u een en ander weet over SQL Data Warehouse, kunt u leren hoe u snel [een SQL Data Warehouse maakt][create a SQL Data Warehouse] en [voorbeeldgegevens laden][load sample data].

<!--Image references-->

<!--Article references-->
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
