---
title: Scenario's identificeren voor Azure Machine Learning - Team Data Science Process
description: Selecteer de juiste scenario's voor geavanceerde voorspellende analyses met het Team Data Science Process.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3e7d747901fb73afa78b6162316709d7d2e78927
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981120"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenario's voor geavanceerde analyses in Azure Machine Learning
In dit artikel bevat een overzicht van de verschillende gegevensbronnen van voorbeeld en doelscenario's mogelijk die kunnen worden verwerkt door de [Team Data Science Process (TDSP)](overview.md). De TDSP biedt een systematische benadering voor teams kunnen samenwerken op het bouwen van intelligente toepassingen. De scenario's die hier wordt gepresenteerd illustreren opties beschikbaar zijn in de werkstroom gegevensverwerking die afhankelijk van de gegevenskenmerken, bronlocaties en doel-opslagplaatsen in Azure zijn.

De **beslissingsstructuur** voor selecteren van de voorbeeld-scenario's die geschikt is voor uw gegevens en het doel wordt weergegeven in de laatste sectie.

Elk van de volgende secties geeft een voorbeeldscenario. Voor elk scenario, een mogelijke data science of geavanceerde analyses stroom en de ondersteunende Azure-resources worden weergegeven.

> [!NOTE]
> **Voor alle van de volgende scenario's moet u naar:**
> <br/>
> 
> * [Een opslagaccount maken](../../storage/common/storage-account-create.md)
>   <br/>
> * [Een Azure Machine Learning-werkruimte maken](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Scenario \#1: kleine tot middelgrote gegevensset in tabelvorm in een lokale bestanden
![Kleine tot middelgrote lokale bestanden][1]

#### <a name="additional-azure-resources-none"></a>Extra Azure-resources: geen
1. Aanmelden bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Upload een gegevensset.
1. Een Azure Machine Learning-experiment-stroom vanaf geüploade gegevensset (s) maken.

## <a name="smalllocalprocess"></a>Scenario \#2: kleine tot middelgrote gegevensset van lokale bestanden die moeten worden verwerkt
![Kleine tot middelgrote lokale bestanden met verwerking in][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Extra Azure-resources: Azure virtuele Machine (IPython Notebook-server)
1. Maak een virtuele Machine van Azure IPython Notebook.
1. Gegevens uploaden naar een Azure storage-container.
1. Voorverwerken en opschonen van gegevens in IPython Notebook, toegang tot gegevens uit Azure storage-container.
1. Transformeer gegevens gereinigd, tabelweergave.
1. Getransformeerde gegevens opslaan in Azure-blobs.
1. Aanmelden bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. De gegevens uit Azure-blobs lezen met de module [gegevens importeren][import-data] .
1. Een Azure Machine Learning-experiment-stroom vanaf opgenomen gegevensset (s) maken.

## <a name="largelocal"></a>Scenario \#3: grote gegevensset van lokale bestanden, die zijn gericht op Azure-Blobs
![Grote lokale bestanden][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Extra Azure-resources: Azure virtuele Machine (IPython Notebook-server)
1. Maak een virtuele Machine van Azure IPython Notebook.
1. Gegevens uploaden naar een Azure storage-container.
1. Voorverwerken en opschonen van gegevens in IPython Notebook, toegang tot gegevens uit Azure-blobs.
1. Transformeer gegevens gereinigd, tabelweergave, indien nodig.
1. Gegevens verkennen en functies maken indien nodig.
1. Een voorbeeld van kleine tot middelgrote gegevens extraheren.
1. De sample gegevens opslaan in Azure-blobs.
1. Aanmelden bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. De gegevens uit Azure-blobs lezen met de module [gegevens importeren][import-data] .
1. Azure Machine Learning-experiment stroom beginnen met opgenomen gegevensset (s) maken.

## <a name="smalllocaltodb"></a>Scenario \#4: kleine tot middelgrote gegevensset van lokale bestanden, die gericht is op SQL Server in een Azure Virtual machines
![Kleine tot middelgrote lokale bestanden naar SQL DB in Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Extra Azure-resources: Azure virtuele Machine (SQL Server / IPython Notebook-server)
1. Maak een virtuele Machine van Azure SQL-Server + IPython Notebook.
1. Gegevens uploaden naar een Azure storage-container.
1. Voorverwerken en opschonen van gegevens in Azure storage-container met behulp van IPython Notebook.
1. Transformeer gegevens gereinigd, tabelweergave, indien nodig.
1. Gegevens opslaan naar VM-local-bestanden (IPython Notebook op de virtuele machine wordt uitgevoerd, lokale stations verwijzen naar de VM-schijven).
1. Gegevens laden in SQL Server-database die wordt uitgevoerd op een Azure-VM.
   
   Optie \#1: met behulp van SQL Server Management Studio.
   
   * Meld u aan bij SQL Server-machine
   * Voer SQL Server Management Studio.
   * Database- en doel-tabellen maken.
   * Gebruik een van de bulksgewijs importeren methoden voor het laden van de gegevens uit de VM-lokale bestanden.
   
   Optie \#2: met behulp van IPython Notebook – niet aangeraden voor middelgrote en grote gegevenssets
   
   <!-- -->    
   * ODBC-verbindingsreeks gebruiken voor toegang tot SQL Server op virtuele machine.
   * Database- en doel-tabellen maken.
   * Gebruik een van de bulksgewijs importeren methoden voor het laden van de gegevens uit de VM-lokale bestanden.
1. Gegevens verkennen, functies maken die nodig is. Houd er rekening mee dat de functies niet hoeft te worden gematerialiseerd in de databasetabellen. Alleen Houd er rekening mee de query die nodig zijn om deze te maken.
1. Beslissen over een steekproefomvang gegevens als nodig is en/of gewenst is.
1. Aanmelden bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Lees de gegevens rechtstreeks vanuit het SQL Server met behulp van de module [gegevens importeren][import-data] . Plak de benodigde query waarmee velden worden geëxtraheerd, maakt functies en voor beelden van gegevens, indien dit rechtstreeks nodig is in de query [gegevens importeren][import-data] .
1. Azure Machine Learning-experiment stroom beginnen met opgenomen gegevensset (s) maken.

## <a name="largelocaltodb"></a>Scenario \#5: grote gegevensset in een lokale bestanden, gericht op SQL Server in virtuele Azure-machine
![Grote lokale bestanden naar SQL DB in Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Extra Azure-resources: Azure virtuele Machine (SQL Server / IPython Notebook-server)
1. Maak een Azure-virtuele Machine met SQL Server en IPython Notebook-server.
1. Gegevens uploaden naar een Azure storage-container.
1. (Optioneel) Voorverwerken en opschonen van gegevens.
   
   a.  Voorverwerken en opschonen van gegevens in IPython Notebook, toegang tot gegevens van Azure
   
       blobs.
   
   b.  Transformeer gegevens gereinigd, tabelweergave, indien nodig.
   
   c.  Gegevens opslaan naar VM-local-bestanden (IPython Notebook op de virtuele machine wordt uitgevoerd, lokale stations verwijzen naar de VM-schijven).
1. Gegevens laden in SQL Server-database die wordt uitgevoerd op een Azure-VM.
   
   a.  Meld u aan met SQL Server-machine.
   
   b.  Als gegevens niet al opgeslagen, downloaden van gegevensbestanden van Azure
   
       storage container to local-VM folder.
   
   c.  Voer SQL Server Management Studio.
   
   d.  Database- en doel-tabellen maken.
   
   e.  Gebruik een van de bulksgewijs importeren methoden om de gegevens te laden.
   
   f.  Als tabelsamenvoegingen vereist zijn, maakt u indexen joins versnellen.
   
   > [!NOTE]
   > Voor het sneller laden van grote gegevenshoeveelheden verdient het aanbeveling dat u gepartitioneerde tabellen maken en grote hoeveelheden gegevens parallel importeren. Zie voor meer informatie, [parallelle gegevens importeren in SQL-tabellen gepartitioneerd](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Gegevens verkennen, functies maken die nodig is. Houd er rekening mee dat de functies niet hoeft te worden gematerialiseerd in de databasetabellen. Alleen Houd er rekening mee de query die nodig zijn om deze te maken.
1. Beslissen over een steekproefomvang gegevens als nodig is en/of gewenst is.
1. Aanmelden bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Lees de gegevens rechtstreeks vanuit het SQL Server met behulp van de module [gegevens importeren][import-data] . Plak de benodigde query waarmee velden worden geëxtraheerd, maakt functies en voor beelden van gegevens, indien dit rechtstreeks nodig is in de query [gegevens importeren][import-data] .
1. Eenvoudige stroom met Azure Machine Learning-experiment beginnen met geüploade gegevensset

## <a name="largedbtodb"></a>Scenario \#6: grote gegevensset in een SQL Server-data base on-premises, gericht op SQL Server in een virtuele machine van Azure
![Grote SQL DB on-premises naar SQL DB in Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Extra Azure-resources: Azure virtuele Machine (SQL Server / IPython Notebook-server)
1. Maak een Azure-virtuele Machine met SQL Server en IPython Notebook-server.
1. Gebruik een van de gegevens exporteren methoden om de gegevens exporteren uit SQL Server naar dumpbestanden.
   
   > [!NOTE]
   > Als u besluit alle gegevens van de on-premises data base te verplaatsen, een alternatieve (snellere) methode om de volledige data base te verplaatsen naar het SQL Server-exemplaar in Azure. Sla de stappen voor het exporteren van gegevens, -database maken en laden/importeren van gegevens naar de doeldatabase en volgt u de alternatieve methode.
   > 
   > 
1. Dumpbestanden uploaden naar Azure storage-container.
1. De gegevens laden in een SQL Server-database op een Azure-machine.
   
   a.  Meld u aan met de SQL Server-machine.
   
   b.  Downloaden van gegevensbestanden van een Azure storage-container naar de lokale VM-map.
   
   c.  Voer SQL Server Management Studio.
   
   d.  Database- en doel-tabellen maken.
   
   e.  Gebruik een van de bulksgewijs importeren methoden om de gegevens te laden.
   
   f.  Als tabelsamenvoegingen vereist zijn, maakt u indexen joins versnellen.
   
   > [!NOTE]
   > Voor sneller laden van grote gegevenshoeveelheden, gepartitioneerde tabellen maken en grote hoeveelheden gegevens parallel importeren. Zie voor meer informatie, [parallelle gegevens importeren in SQL-tabellen gepartitioneerd](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Gegevens verkennen, functies maken die nodig is. Houd er rekening mee dat de functies niet hoeft te worden gematerialiseerd in de databasetabellen. Alleen Houd er rekening mee de query die nodig zijn om deze te maken.
1. Beslissen over een steekproefomvang gegevens als nodig is en/of gewenst is.
1. Aanmelden bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Lees de gegevens rechtstreeks vanuit het SQL Server met behulp van de module [gegevens importeren][import-data] . Plak de benodigde query waarmee velden worden geëxtraheerd, maakt functies en voor beelden van gegevens, indien dit rechtstreeks nodig is in de query [gegevens importeren][import-data] .
1. Eenvoudige Azure Machine Learning-experiment stroom beginnen met geüploade gegevensset.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternatieve methode om te kopiëren van een volledige database uit een on-premises SQL Server naar Azure SQL Database
![Lokale DB loskoppelen en koppelen aan SQL-database in Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Extra Azure-resources: Azure virtuele Machine (SQL Server / IPython Notebook-server)
Als u wilt repliceren van de volledige SQL Server-database in uw SQL Server-VM, u moet een database kopiëren vanaf één locatie of de server naar een andere, uitgaande van de database kan worden gehouden die tijdelijk offline. Dit doet u in de SQL Server Management Studio-Objectverkenner of met behulp van de gelijkwaardige Transact-SQL-opdrachten.

1. Ontkoppel de database op de bronlocatie. Zie voor meer informatie, [loskoppelen van een database](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. In Windows Explorer- of Windows-opdrachtprompt venster, kopieert u de losgekoppelde databasebestand of de bestanden en het logboekbestand of de bestanden naar de doellocatie op de SQL Server-VM in Azure.
1. De gekopieerde bestanden toevoegen aan de doel-SQL Server-exemplaar. Zie voor meer informatie, [koppelen van een Database](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Verplaatsen van een Database met loskoppelen en koppelen (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Scenario \#7: Big data in lokale bestanden doel Hive-database in Azure HDInsight Hadoop-clusters
![BIG data in lokale doel Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Extra Azure-resources: Azure HDInsight Hadoop-Cluster en virtuele Azure-Machine (IPython Notebook-server)
1. Maak een virtuele Machine van Azure IPython Notebook-server.
1. Maak een Azure HDInsight Hadoop-cluster.
1. (Optioneel) Voorverwerken en opschonen van gegevens.
   
   a.  Voorverwerken en opschonen van gegevens in IPython Notebook, toegang tot gegevens van Azure
   
       blobs.
   
   b.  Transformeer gegevens gereinigd, tabelweergave, indien nodig.
   
   c.  Gegevens opslaan naar VM-local-bestanden (IPython Notebook op de virtuele machine wordt uitgevoerd, lokale stations verwijzen naar de VM-schijven).
1. Gegevens uploaden naar de standaardcontainer van de Hadoop-cluster dat is geselecteerd in stap 2.
1. Gegevens laden in Hive-database in Azure HDInsight Hadoop-cluster.
   
   a.  Meld u aan bij het hoofdknooppunt van het Hadoop-cluster
   
   b.  Open de Hadoop-opdrachtregel.
   
   c.  Voer in de hoofdmap van de Hive met opdracht `cd %hive_home%\bin` in Hadoop vanaf de opdrachtregel.
   
   d.  Voer de Hive-query's voor het maken van de database en tabellen en gegevens uit blob-opslag laden in Hive-tabellen.
   
   > [!NOTE]
   > Als de gegevens groot is, kunnen gebruikers de Hive-tabel maken met partities. Vervolgens kunt gebruikers gebruiken een `for` lus in de Hadoop-opdrachtregels op het hoofdknooppunt gegevens laden in de Hive-tabel die is gepartitioneerd op basis van de partitie.
   > 
   > 
1. Gegevens verkennen en functies maken indien nodig in Hadoop vanaf de opdrachtregel. Houd er rekening mee dat de functies niet hoeft te worden gematerialiseerd in de databasetabellen. Alleen Houd er rekening mee de query die nodig zijn om deze te maken.
   
   a.  Meld u aan bij het hoofdknooppunt van het Hadoop-cluster
   
   b.  Open de Hadoop-opdrachtregel.
   
   c.  Voer in de hoofdmap van de Hive met opdracht `cd %hive_home%\bin` in Hadoop vanaf de opdrachtregel.
   
   d.  In het Hadoop-opdrachtregels de Hive-query's in te voeren op het hoofdknooppunt van het Hadoop-cluster voor de gegevens verkennen en functies maken indien nodig.
1. Als nodig is en/of gewenst is, voorbeeld van de gegevens naar wens aanpassen in Azure Machine Learning Studio.
1. Aanmelden bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Lees de gegevens rechtstreeks vanuit het `Hive Queries` met behulp van de module [gegevens importeren][import-data] . Plak de benodigde query waarmee velden worden geëxtraheerd, maakt functies en voor beelden van gegevens, indien dit rechtstreeks nodig is in de query [gegevens importeren][import-data] .
1. Eenvoudige Azure Machine Learning-experiment stroom beginnen met geüploade gegevensset.

## <a name="decisiontree"></a>Beslissingsstructuur voor de selectie van het scenario
---
Het volgende diagram geeft een overzicht van de scenario's die hierboven worden beschreven en de Advanced Analytics Process and technologiekeuzes die u voor elk van de gespecificeerde scenario's kunt uitvoeren. Houd er rekening mee dat gegevensverwerking, verkennen, feature-engineering en steekproeven kunnen duren voordat plaats in een of meer methode/omgeving--op de bronlocatie, tussenliggende, en/of doelomgevingen – en iteratief indien nodig kan doorgaan. Het diagram alleen fungeert als een afbeelding van een aantal mogelijke stromen en biedt een volledige inventarisatie.

![Voorbeeldscenario's DS proces scenario][8]

### <a name="advanced-analytics-in-action-examples"></a>Advanced Analytics in actie voorbeelden
Zie voor end-to-end Azure Machine Learning-scenario's die gebruikmaken van de Advanced Analytics Process and Technology met behulp van openbare gegevenssets:

* [Team Data Science Process in actie: met behulp van SQL Server](sql-walkthrough.md).
* [Team Data Science Process in actie: met behulp van HDInsight Hadoop-clusters](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
