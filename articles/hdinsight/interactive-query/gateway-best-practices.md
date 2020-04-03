---
title: Gateway deep dive en best practices voor Apache Hive in Azure HDInsight
description: Meer informatie over het navigeren door de aanbevolen procedures voor het uitvoeren van Hive-query's via de Azure HDInsight-gateway
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586975"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Gateway deep dive en best practices voor Apache Hive in Azure HDInsight

De Azure HDInsight-gateway (Gateway) is de HTTPS-frontend voor HDInsight-clusters. De gateway is verantwoordelijk voor: verificatie, hostresolutie, servicedetectie en andere handige functies die nodig zijn voor een modern gedistribueerd systeem. De functies van de Gateway resulteren in een aantal overhead waarvoor dit document de beste procedures om te navigeren zal beschrijven. Gateway probleemoplossing technieken worden ook besproken.

## <a name="the-hdinsight-gateway"></a>De HDInsight-gateway

De HDInsight-gateway is het enige deel van een HDInsight-cluster dat openbaar toegankelijk is via internet. De Gateway-service is toegankelijk zonder via het `clustername-int.azurehdinsight.net` openbare internet te gaan met behulp van het interne gateway-eindpunt. Met het interne gateway-eindpunt kunnen verbindingen tot stand worden gebracht met de gatewayservice zonder het virtuele netwerk van het cluster te verlaten. De gateway verwerkt alle aanvragen die naar het cluster worden verzonden en stuurt dergelijke aanvragen door naar de juiste componenten en clusterhosts.

Het volgende diagram geeft een ruwe illustratie van hoe de Gateway een abstractie biedt voor alle verschillende mogelijkheden voor hostresolutie binnen HDInsight.

![Diagram hostresolutie](./media/gateway-best-practices/host-resolution-diagram.png "Diagram hostresolutie")

## <a name="motivation"></a>Motivatie

De motivatie voor het plaatsen van een gateway voor HDInsight clusters is om een interface voor service discovery en gebruikersverificatie te bieden. De verificatiemechanismen van de gateway zijn vooral relevant voor ESP-clusters.

Voor servicedetectie is het voordeel van de gateway dat elk onderdeel binnen het cluster `clustername.azurehdinsight.net/hive2`kan worden geopend als `host:port` een ander eindpunt onder de Gateway-website ( ), in tegenstelling tot een veelheid aan koppelingen.

Voor verificatie kunnen gebruikers met de `username:password` Gateway verifiëren met behulp van een referentiepaar. Voor clusters met ESP is deze referentie de gebruikersnaam en het wachtwoord van het domein van de gebruiker. Verificatie naar HDInsight-clusters via de Gateway vereist niet dat de client een kerberos-ticket aanschaft. Aangezien de Gateway `username:password` referenties accepteert en namens de gebruiker het Kerberos-ticket van de gebruiker verwerft, kunnen beveiligde verbindingen met de Gateway worden gemaakt vanuit elke clienthost, inclusief clients die zijn verbonden met verschillende AA-DDS-domeinen dan het (ESP)-cluster.

## <a name="best-practices"></a>Aanbevolen procedures

De Gateway is één service (load balanced over two hosts) die verantwoordelijk is voor het doorsturen en doorsturen van aanvragen en verificatie. De gateway kan een doorvoerknelpunt worden voor Hive-query's die een bepaalde grootte overschrijden. Queryprestatiedegradatie kan worden waargenomen wanneer zeer grote **SELECT-query's** worden uitgevoerd op de Gateway via ODBC of JDBC. "Zeer groot" betekent query's die meer dan 5 GB aan gegevens bevatten in rijen of kolommen. Deze query kan een lange lijst met rijen en of een groot aantal kolomen bevatten.

De prestatiedegradatie van de gateway rond query's van een groot formaat is omdat de gegevens moeten worden overgedragen van het onderliggende gegevensarchief (ADLS Gen2) naar: de HDInsight Hive Server, de Gateway en ten slotte via de JDBC- of ODBC-stuurprogramma's naar de clienthost.

In het volgende diagram worden de stappen van een SELECT-query geïllustreerd.

![Resultaatdiagram](./media/gateway-best-practices/result-retrieval-diagram.png "Resultaatdiagram")

Apache Hive is een relationele abstractie bovenop een HDFS-compatibel bestandssysteem. Deze abstractie betekent **SELECT-instructies** in Hive die overeenkomen met **READ-bewerkingen** op het bestandssysteem. De **READ** READ-bewerkingen worden vertaald naar het juiste schema voordat ze aan de gebruiker worden gerapporteerd. De latentie van dit proces neemt toe met de grootte van de gegevens en de totale hop die nodig is om de eindgebruiker te bereiken.

Vergelijkbaar gedrag kan optreden bij het uitvoeren van **CREATE-** of **INSERT-instructies** van grote gegevens, omdat deze opdrachten overeenkomen met **WRITE-bewerkingen** in het onderliggende bestandssysteem. Overweeg gegevens, zoals raw ORC, naar het bestandssysteem/datalake te schrijven in plaats van deze te laden met **INSERT** of **LOAD**.

In clusters met Enterprise Security Pack kan voldoende complex Apache Ranger-beleid leiden tot een vertraging in de compilatietijd van query's, wat kan leiden tot een time-out van de gateway. Als een gateway-time-out wordt opgemerkt in een ESP-cluster, u overwegen het aantal ranger-beleidsregels te verminderen of te combineren.

## <a name="troubleshooting-techniques"></a>Technieken voor probleemoplossing

Er zijn meerdere locaties voor het verzachten en begrijpen van prestatieproblemen die worden bereikt als onderdeel van het bovenstaande gedrag. Gebruik de volgende checklist wanneer u queryprestaties verslechteringen via de HDInsight-gateway:

* Gebruik de clausule **LIMIT** bij het uitvoeren van grote **SELECT-query's.** De **LIMIET-clausule** vermindert de totale rijen die aan de clienthost worden gerapporteerd. De **LIMIET-component** is alleen van invloed op het genereren van resultaten en wijzigt het queryplan niet. Als u de **clausule LIMIET** wilt toepassen `hive.limit.optimize.enable`op het queryplan, gebruikt u de configuratie . **LIMIT** kan worden gecombineerd met een verschuiving met behulp van het argumentformulier **LIMIT x,y**.

* Geef uw kolommen van belang bij het uitvoeren van **SELECT-query's** in plaats van **SELECT \* **. Als u minder kolommen selecteert, wordt de hoeveelheid gelezen gegevens verlaagd.

* Probeer de query van belang uit te voeren via Apache Beeline. Als resultaat ophalen via Apache Beeline duurt een langere periode van tijd, verwachten vertragingen bij het ophalen van dezelfde resultaten via externe tools.

* Test een basisHive-query om ervoor te zorgen dat een verbinding met de HDInsight Gateway kan worden gemaakt. Probeer een basisquery uit te voeren van twee of meer externe hulpprogramma's om ervoor te zorgen dat er geen afzonderlijk hulpprogramma problemen ondervindt.

* Bekijk alle Apache Ambari Alerts om ervoor te zorgen dat HDInsight-services gezond zijn. Ambari Alerts kunnen worden geïntegreerd met Azure Monitor voor rapportage en analyse.

* Als u een externe Hive Metastore gebruikt, controleert u of de Azure SQL DB DTU voor de Hive Metastore de limiet niet heeft bereikt. Als de DTU zijn limiet nadert, moet u de databasegrootte vergroten.

* Zorg ervoor dat tools van derden, zoals PBI of Tableau, pagination gebruiken om tabellen of databases weer te geven. Raadpleeg uw ondersteuningspartners voor deze tools voor hulp bij pagination. Het belangrijkste hulpmiddel dat wordt gebruikt `fetchSize` voor paginatie is de PARAMETER JDBC. Een kleine fetch-grootte kan leiden tot verminderde gatewayprestaties, maar een te grote ophalensgrootte kan resulteren in een gateway-time-out. Fetch size tuning moet worden gedaan op basis van werkbelasting.

* Als uw gegevenspijplijn gaat om het lezen van grote hoeveelheden gegevens uit de onderliggende opslag van het HDInsight-cluster, u overwegen een hulpprogramma te gebruiken dat rechtstreeks met Azure Storage wordt gekoppeld, zoals Azure Data Factory

* Overweeg Apache Hive LLAP te gebruiken bij het uitvoeren van interactieve workloads, omdat LLAP een soepelere ervaring kan bieden voor het snel retourneren van queryresultaten

* Overweeg het aantal threads dat beschikbaar is voor `hive.server2.thrift.max.worker.threads`de Hive Metastore-service te verhogen met behulp van . Deze instelling is vooral relevant wanneer een groot aantal gelijktijdige gebruikers query's indient op het cluster

* Verminder het aantal nieuwe pogingen dat wordt gebruikt om de Gateway te bereiken vanuit externe hulpprogramma's. Als er meerdere nieuwe pogingen worden gebruikt, u overwegen een exponentieel back-off-beleid te volgen

* Overweeg compressiehive in te `hive.exec.compress.output` `hive.exec.compress.intermediate`schakelen met behulp van de configuraties en .

## <a name="next-steps"></a>Volgende stappen

* [Apache Beeline op HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [Stappen voor time-outproblemen van HDInsight-gateway](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [Virtuele netwerken voor HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [HDInsight met Express Route](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)