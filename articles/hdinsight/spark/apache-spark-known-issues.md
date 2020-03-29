---
title: Problemen met het Apache Spark-cluster in Azure HDInsight oplossen
description: Meer informatie over problemen met betrekking tot Apache Spark-clusters in Azure HDInsight en hoe u deze omzeilen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.author: hrasheed
ms.openlocfilehash: 2c153d818136c5d8804dae72004dfaf17fd1bf7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494531"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Bekende problemen voor Apache Spark cluster op HDInsight

Dit document houdt alle bekende problemen bij voor de hdInsight Spark public preview.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy lekt interactieve sessie
Wanneer [Apache Livy](https://livy.incubator.apache.org/) opnieuw wordt opgestart (van [Apache Ambari](https://ambari.apache.org/) of vanwege headnode 0 virtual machine reboot) met een interactieve sessie die nog in leven is, is een interactieve jobsessie uitgelekt. Als gevolg hiervan kunnen nieuwe banen worden vastgelopen in de geaccepteerde status.

**Mitigatie:**

Gebruik de volgende procedure om het probleem te omzeilen:

1. Ssh in headnode. Zie [SSH-sleutels gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

2. Voer de volgende opdracht uit om de toepassings-id's van de interactieve taken te vinden die zijn gestart via Livy.

        yarn application –list

    De standaardfunctienamen zijn Livy als de taken zijn gestart met een interactieve Livy-sessie zonder expliciete namen opgegeven. Voor de Livy sessie gestart door [Jupyter](https://jupyter.org/) `remotesparkmagics_*`Notebook , de naam van de baan begint met .

3. Voer het volgende commando uit om die taken te doden.

        yarn application –kill <Application ID>

Nieuwe taken beginnen te lopen.

## <a name="spark-history-server-not-started"></a>Spark History Server is niet gestart
Spark History Server wordt niet automatisch gestart nadat een cluster is gemaakt.  

**Mitigatie:**

Start de geschiedenisserver handmatig vanuit Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Machtigingsprobleem in spark-logboekmap
hdiuser krijgt de volgende fout bij het indienen van een taak met behulp van spark-submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

En er is geen bestuurderslogboek geschreven.

**Mitigatie:**

1. Voeg hdiuser toe aan de Hadoop-groep.
2. Geef 777 machtigingen op /var/log/spark na het maken van het cluster.
3. Werk de spark log locatie met Ambari om een directory met 777 machtigingen.  
4. Voer spark-submit uit als sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark-Phoenix-connector wordt niet ondersteund

HDInsight Spark-clusters ondersteunen de Spark-Phoenix-connector niet.

**Mitigatie:**

In plaats daarvan moet u de Spark-HBase-connector gebruiken. Zie [Spark-HBase-connector gebruiken voor](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/)de instructies .

## <a name="issues-related-to-jupyter-notebooks"></a>Problemen met betrekking tot Jupyter-notitieblokken

Hieronder volgen enkele bekende problemen met betrekking tot Jupyter-notitieblokken.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notitieblokken met niet-ASCII-tekens in bestandsnamen

Gebruik niet-ASCII-tekens niet in de bestandsnamen van Jupyter-notitiebloks. Als u een bestand probeert te uploaden via de Jupyter-gebruikersinterface, die een niet-ASCII-bestandsnaam heeft, mislukt dit zonder foutbericht. Jupyter laat je het bestand niet uploaden, maar het gooit ook geen zichtbare fout.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Fout bij het laden van notitieblokken met grotere afmetingen

Mogelijk ziet u **`Error loading notebook`** een fout wanneer u notitieblokken laadt die groter zijn.  

**Mitigatie:**

Als u deze fout krijgt, betekent dit niet dat uw gegevens beschadigd of verloren zijn gegaan.  Uw notitieblokken staan `/var/lib/jupyter`nog steeds op schijf en u SSH in het cluster gebruiken om er toegang toe te krijgen. Zie [SSH-sleutels gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

Zodra u verbinding hebt gemaakt met het cluster met SSH, u uw notitieblokken van uw cluster kopiëren naar uw lokale machine (met BEHULP van SCP of WinSCP) als back-up om het verlies van belangrijke gegevens in het notitieblok te voorkomen. U dan SSH tunnel in uw headnode op poort 8001 om jupyter toegang zonder te gaan via de gateway.  Van daaruit u de uitvoer van uw notitieblok wissen en opnieuw opslaan om de grootte van de notebook te minimaliseren.

Om te voorkomen dat deze fout zich in de toekomst voordoet, moet u een aantal aanbevolen procedures volgen:

* Het is belangrijk om de grootte van de notebook klein te houden. Elke uitvoer van uw Spark-taken die naar Jupyter wordt teruggestuurd, blijft bestaan in het notitieblok.  Het is een best practice met Jupyter in het algemeen om te voorkomen dat draait `.collect()` op grote RDD's of dataframes; In plaats daarvan, als u wilt gluren naar `.take()` de `.sample()` inhoud van een RDD, overwegen uitgevoerd of zo dat uw output niet te groot.
* Wanneer u een notitieblok opslaat, wist u ook alle uitvoercellen om de grootte te verkleinen.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Het opstarten van notebooks duurt langer dan verwacht

De eerste codeverklaring in Jupyter-notebook met Spark-magie kan meer dan een minuut duren.  

**Uitleg:**

Dit gebeurt omdat wanneer de eerste codecel wordt uitgevoerd. Op de achtergrond initieert dit sessieconfiguratie en spark-, SQL- en Hive-contexten zijn ingesteld. Nadat deze contexten zijn ingesteld, wordt de eerste instructie uitgevoerd en dit geeft de indruk dat de instructie lang heeft geduurd.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Time-out voor Jupyter-notitieblok bij het maken van de sessie

Wanneer spark-cluster geen resources meer heeft, zullen de Spark- en PySpark-kernels in het Jupyter-notitieblok een time-out hebben om de sessie te maken.

**Mitigaties:**

1. Maak enkele bronnen in uw Spark-cluster vrij door:

   * Andere Spark-notitieblokken stoppen door naar het menu Sluiten en Stoppen te gaan of op Afsluiten in de notitieblokreiziger te klikken.
   * Het stoppen van andere Spark-toepassingen van YARN.

2. Start het notitieblok opnieuw dat u probeerde op te starten. Er moeten voldoende bronnen beschikbaar zijn om nu een sessie te maken.

## <a name="see-also"></a>Zie ook

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve data-analyse uitvoeren met Spark in HDInsight met BI-tools](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analyse met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [HdInsight Tools Plugin gebruiken voor IntelliJ IDEA om Apache Spark-toepassingen op afstand te debuggen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster op HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels beschikbaar voor Jupyter-laptop in Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)