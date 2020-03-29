---
title: Apache Phoenix in HDInsight - Azure HDInsight
description: Overzicht van Apache Phoenix
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: b1d81296c996ab09cb6482cb970496779ccf8bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435498"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix in Azure HDInsight

[Apache Phoenix](https://phoenix.apache.org/) is een open source, massaal parallelle relationele database laag gebouwd op [Apache HBase](hbase/apache-hbase-overview.md). Met Phoenix u SQL-achtige query's via HBase gebruiken. Phoenix maakt gebruik van JDBC-stuurprogramma's eronder om gebruikers in staat te stellen SQL-tabellen, indexen, weergaven en sequenties en upsertrijen afzonderlijk en in bulk te maken, te verwijderen, te wijzigen. Phoenix gebruikt noSQL native compilatie in plaats van MapReduce te gebruiken om query's samen te stellen, waardoor toepassingen met lage latentie bovenop HBase kunnen worden gemaakt. Phoenix voegt coprocessors toe ter ondersteuning van het uitvoeren van door de client geleverde code in de adresruimte van de server, waarbij de code wordt uitgevoerd die bij de gegevens is gekoppeld. Deze aanpak minimaliseert de overdracht van gegevens over client/server.

Apache Phoenix opent big data-query's voor niet-ontwikkelaars die een SQL-achtige syntaxis kunnen gebruiken in plaats van programmeren. Phoenix is sterk geoptimaliseerd voor HBase, in tegenstelling tot andere tools zoals [Apache Hive](hadoop/hdinsight-use-hive.md) en Apache Spark SQL. Het voordeel voor ontwikkelaars is het schrijven van zeer performante query's met veel minder code.

Wanneer u een SQL-query indient, compileert Phoenix de query naar HBase-native calls en voert de scan (of plan) parallel uit voor optimalisatie. Deze abstractielaag bevrijdt de ontwikkelaar van het schrijven van MapReduce-taken, om zich in plaats daarvan te concentreren op de bedrijfslogica en de workflow van hun toepassing rond de big data-opslag van Phoenix.

## <a name="query-performance-optimization-and-other-features"></a>Optimalisatie van queryprestaties en andere functies

Apache Phoenix voegt verschillende prestatieverbeteringen en functies toe aan HBase-query's.

### <a name="secondary-indexes"></a>Secundaire indexen

HBase heeft één index die lexicografisch wordt gesorteerd op de primaire rijtoets. Deze records zijn alleen toegankelijk via de rijsleutel. Als u toegang krijgt tot records via een andere kolom dan de rijsleutel, moeten alle gegevens worden gescand terwijl u het vereiste filter toepast. In een secundaire index vormen de kolommen of expressies die zijn geïndexeerd een alternatieve rijsleutel, waarmee opzoekingen en bereikscans op die index kunnen worden weergegeven.

Maak een secundaire `CREATE INDEX` index met de opdracht:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Deze benadering kan een aanzienlijke prestatietoename opleveren ten opzichte van het uitvoeren van query's met één geïndexeerde zoekopdrachten. Dit type secundaire index is een **dekkingsindex,** die alle kolommen bevat die in de query zijn opgenomen. Daarom is het opzoeken van de tabel niet vereist en voldoet de index aan de hele query.

### <a name="views"></a>Weergaven

Phoenix-weergaven bieden een manier om een HBase-beperking te overwinnen, waarbij de prestaties beginnen te verslechteren wanneer u meer dan 100 fysieke tabellen maakt. Phoenix-weergaven stellen meerdere *virtuele tabellen* in staat om één onderliggende fysieke HBase-tabel te delen.

Het maken van een Phoenix-weergave is vergelijkbaar met het gebruik van standaard SQL-weergavesyntaxis. Een verschil is dat u kolommen voor uw weergave definiëren, naast de kolommen die zijn overgenomen van de basistabel. U ook `KeyValue` nieuwe kolommen toevoegen.

Hier is bijvoorbeeld een fysieke `product_metrics` tabel met de volgende definitie:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Definieer een weergave boven deze tabel met extra kolommen:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Als u later meer `ALTER VIEW` kolommen wilt toevoegen, gebruikt u de instructie.

### <a name="skip-scan"></a>Scan overslaan

Scan overslaan gebruikt een of meer kolommen van een samengestelde index om afzonderlijke waarden te vinden. In tegenstelling tot een bereikscan, implementeert skip scan scannen binnen de rij scannen, wat [betere prestaties](https://phoenix.apache.org/performance.html#Skip-Scan)oplevert. Tijdens het scannen wordt de eerste overeenkomende waarde samen met de index overgeslagen totdat de volgende waarde is gevonden.

Een skip scan `SEEK_NEXT_USING_HINT` maakt gebruik van de opsomming van het HBase-filter. Met `SEEK_NEXT_USING_HINT`behulp van , de scan overslaan houdt bij welke set sleutels, of bereiken van sleutels, worden gezocht in elke kolom. De skip scan neemt vervolgens een sleutel die is doorgegeven aan het tijdens de filter evaluatie, en bepaalt of het een van de combinaties. Zo niet, dan evalueert de skip-scan de volgende hoogste toets om naar toe te springen.

### <a name="transactions"></a>Transacties

Terwijl HBase transacties op rijniveau biedt, integreert Phoenix met [Tephra](https://tephra.io/) om cross-row en cross-table transactieondersteuning toe te voegen met volledige ACID-semantiek. [ACID](https://en.wikipedia.org/wiki/ACID)

Net als bij traditionele SQL-transacties u met transacties die via de Phoenix-transactiemanager worden geleverd, ervoor zorgen dat een atoomeenheid van gegevens met succes wordt bijgewerkt, waardoor de transactie wordt teruggedraaid als de upsert-bewerking mislukt op een tabel met transactie-ingeschakelde gegevens.

Zie de [transactiedocumentatie van Apache Phoenix](https://phoenix.apache.org/transactions.html)om Phoenix-transacties in te schakelen.

Als u een nieuwe tabel wilt `TRANSACTIONAL` maken `true` waarin `CREATE` transacties zijn ingeschakeld, stelt u de eigenschap in een overzicht in:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Als u een bestaande tabel wilt wijzigen om `ALTER` transactioneel te zijn, gebruikt u dezelfde eigenschap in een overzicht:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> U een transactionele tabel niet terugzetten naar niet-transactioneel zijn.

### <a name="salted-tables"></a>Gezouten tafels

*Regioserverhotspotting* kan optreden bij het schrijven van records met opeenvolgende sleutels naar HBase. Hoewel u mogelijk meerdere regioservers in uw cluster hebt, vinden uw schrijfbewerkingen allemaal plaats op slechts één. Deze concentratie creëert de hotspotting probleem waar, in plaats van uw schrijf werkbelasting wordt verdeeld over alle beschikbare regio servers, slechts een is het hanteren van de belasting. Aangezien elke regio een vooraf gedefinieerde maximale grootte heeft, wanneer een regio die groottelimiet bereikt, is het opgesplitst in twee kleine regio's. Wanneer dat gebeurt, neemt een van deze nieuwe regio's alle nieuwe records, steeds de nieuwe hotspot.

Om dit probleem te beperken en betere prestaties te bereiken, pre-split tabellen, zodat alle van de regio servers worden even gebruikt. Phoenix biedt *gezouten tafels,* transparant het toevoegen van de zouten byte aan de rij sleutel voor een bepaalde tabel. De tabel is vooraf gesplitst op de zoutbytegrenzen om te zorgen voor gelijke belastingverdeling tussen regioservers tijdens de eerste fase van de tabel. Deze aanpak verdeelt de schrijfworkload over alle beschikbare regioservers, waardoor de schrijf- en leesprestaties worden verbeterd. Als u een tabel `SALT_BUCKETS` wilt zoutmaken, geeft u de tabeleigenschap op wanneer de tabel wordt gemaakt:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Phoenix inschakelen en afstemmen met Apache Ambari

Een HDInsight HBase-cluster bevat de [Ambari-gebruikersinterface](hdinsight-hadoop-manage-ambari.md) voor het aanbrengen van configuratiewijzigingen.

1. Als u Phoenix wilt in- of uitschakelen en de time-outinstellingen van Phoenix`https://YOUR_CLUSTER_NAME.azurehdinsight.net`wilt beheren, meldt u zich aan bij de Ambari Web UI ( ) met uw Hadoop-gebruikersreferenties.

2. Selecteer **HBase** in de lijst met services in het linkermenu en selecteer vervolgens het tabblad **Configs.**

    ![Apache Ambari HBase-configuraties](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png)

3. Zoek de sectie **Phoenix SQL-configuratie** om phoenix in te schakelen of uit te schakelen en stel de time-out van de query in.

    ![Sectie Ambari Phoenix SQL-configuratie](./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png)

## <a name="see-also"></a>Zie ook

* [Apache Phoenix gebruiken met Op Linux gebaseerde HBase-clusters in HDInsight](hbase/apache-hbase-query-with-phoenix.md)

* [Apache Zeppelin gebruiken om Apache Phoenix-query's uit te voeren via Apache HBase in Azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)
