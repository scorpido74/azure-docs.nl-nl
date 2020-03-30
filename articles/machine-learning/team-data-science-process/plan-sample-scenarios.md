---
title: Scenario's voor Azure Machine Learning - Team Data Science Process identificeren
description: Selecteer de juiste scenario's voor het uitvoeren van geavanceerde voorspellende analyses met het Team Data Science Process.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 48b51c40e5de8f10d9d1d16b02e2c70b045816b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251620"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenario's voor geavanceerde analyses in Azure Machine Learning
In dit artikel wordt een overzicht gegeven van de verscheidenheid aan voorbeeldgegevensbronnen en doelscenario's die kunnen worden behandeld door het [Team Data Science Process (TDSP).](overview.md) Het TDSP biedt teams een systematische aanpak om samen te werken aan het bouwen van intelligente toepassingen. De hier gepresenteerde scenario's illustreren opties die beschikbaar zijn in de werkstroom voor gegevensverwerking die afhankelijk zijn van de gegevenskenmerken, bronlocaties en doelopslagplaatsen in Azure.

De **beslissingsstructuur** voor het selecteren van de voorbeeldscenario's die geschikt zijn voor uw gegevens en doelstelling wordt in de laatste sectie weergegeven.

Elk van de volgende secties presenteert een voorbeeldscenario. Voor elk scenario worden een mogelijke gegevenswetenschaps- of geavanceerde analysestroom en ondersteunende Azure-bronnen weergegeven.

> [!NOTE]
> **Voor alle volgende scenario's moet u het volgende doen:**
> <br/>
> 
> * [Een opslagaccount maken](../../storage/common/storage-account-create.md)
>   <br/>
> * [Een Azure Machine Learning-werkruimte maken](../studio/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Scenario \#1: Kleine tot middelgrote tabelgegevensset in lokale bestanden
![Kleine tot middelgrote lokale bestanden][1]

#### <a name="additional-azure-resources-none"></a>Aanvullende Azure-bronnen: Geen
1. Meld u aan bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Upload een gegevensset.
1. Een Azure Machine Learning-experimentstroom bouwen die begint met geüploade gegevenssets.

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Scenario \#2: Kleine tot middelgrote gegevensset van lokale bestanden waarvoor verwerking vereist is
![Kleine tot middelgrote lokale bestanden met verwerking][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Aanvullende Azure-bronnen: Azure Virtual Machine (IPython Notebook-server)
1. Maak een Azure Virtual Machine met IPython-notitieblok.
1. Gegevens uploaden naar een Azure Storage-container.
1. Verwerk en verschoon gegevens in IPython Notebook en krijg toegang tot gegevens uit de Azure Storage-container.
1. Gegevens omzetten in een gereinigde tabelvorm.
1. Transformeerde gegevens opslaan in Azure-blobs.
1. Meld u aan bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Lees de gegevens van Azure blobs met de module [Gegevens importeren.][import-data]
1. Bouw een Azure Machine Learning-experimentstroom te beginnen met ingenomen gegevenssets.

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Scenario \#3: Grote gegevensset van lokale bestanden, gericht op Azure Blobs
![Grote lokale bestanden][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Aanvullende Azure-bronnen: Azure Virtual Machine (IPython Notebook-server)
1. Maak een Azure Virtual Machine met IPython-notitieblok.
1. Gegevens uploaden naar een Azure Storage-container.
1. Verwerk en verschoon gegevens in IPython Notebook en krijg toegang tot gegevens van Azure blobs.
1. Gegevens zo nodig omzetten in een gereinigde tabelvorm.
1. Verken gegevens en maak functies waar nodig.
1. Haal een klein tot medium gegevensvoorbeeld.
1. Sla de gesamplede gegevens op in Azure-blobs.
1. Meld u aan bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Lees de gegevens van Azure blobs met de module [Gegevens importeren.][import-data]
1. Bouw azure Machine Learning-experimentstroom te beginnen met ingenomen gegevenssets.

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Scenario \#4: Kleine tot middelgrote gegevensset van lokale bestanden, gericht op SQL Server in een Azure Virtual Machine
![Kleine tot middelgrote lokale bestanden naar SQL DB in Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Aanvullende Azure-bronnen: Azure Virtual Machine (SQL Server / IPython Notebook-server)
1. Maak een Azure Virtual Machine met SQL Server + IPython-notitieblok.
1. Gegevens uploaden naar een Azure Storage-container.
1. Vooraf verwerken en schone gegevens in Azure Storage-container met IPython Notebook.
1. Gegevens zo nodig omzetten in een gereinigde tabelvorm.
1. Gegevens opslaan in VM-lokale bestanden (IPython Notebook wordt uitgevoerd op VM, lokale stations verwijzen naar VM-stations).
1. Gegevens laden naar SQL Server-database die wordt uitgevoerd op een Azure-vm.
   
   Optie \#1: SQL Server Management Studio gebruiken.
   
   * Inloggen bij SQL Server VM
   * SQL Server Management Studio uitvoeren.
   * Maak database- en doeltabellen.
   * Gebruik een van de methoden voor het importeren in bulk om de gegevens uit VM-lokale bestanden te laden.
   
   Optie \#2: IPython Notebook gebruiken – niet aan te raden voor middelgrote en grotere datasets
   
   <!-- -->    
   * Gebruik ODBC-verbindingstekenreeks om toegang te krijgen tot SQL Server op VM.
   * Maak database- en doeltabellen.
   * Gebruik een van de methoden voor het importeren in bulk om de gegevens uit VM-lokale bestanden te laden.
1. Verken gegevens, maak functies waar nodig. De functies hoeven niet te worden gematerialiseerd in de databasetabellen. Noteer alleen de benodigde query om ze te maken.
1. Bepaal de som grootte van een gegevenssteekproef, indien nodig en/of gewenst.
1. Meld u aan bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Lees de gegevens rechtstreeks van de SQL Server met behulp van de module [Gegevens importeren.][import-data] Plak de benodigde query die velden uitextraheert, functies maakt en gegevens, indien nodig, indien nodig rechtstreeks in de query [Gegevens importeren.][import-data]
1. Bouw azure Machine Learning-experimentstroom te beginnen met ingenomen gegevenssets.

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Scenario \#5: Grote gegevensset in lokale bestanden, target SQL Server in Azure VM
![Grote lokale bestanden naar SQL DB in Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Aanvullende Azure-bronnen: Azure Virtual Machine (SQL Server / IPython Notebook-server)
1. Maak een Azure Virtual Machine met SQL Server en IPython Notebook server.
1. Gegevens uploaden naar een Azure Storage-container.
1. (Optioneel) Pre-proces en schone gegevens.
   
    a.  Verwerk en verschoon gegevens in IPython Notebook en krijg toegang tot gegevens van Azure blobs.
   
    b.  Gegevens zo nodig omzetten in een gereinigde tabelvorm.
   
    c.  Gegevens opslaan in VM-lokale bestanden (IPython Notebook wordt uitgevoerd op VM, lokale stations verwijzen naar VM-stations).
1. Gegevens laden naar SQL Server-database die wordt uitgevoerd op een Azure-vm.
   
    a.  Meld u aan bij SQL Server VM.
   
    b.  Als gegevens nog niet zijn opgeslagen, downloadt u gegevensbestanden van de Azure-opslagcontainer naar de map lokaal-VM.
   
    c.  SQL Server Management Studio uitvoeren.
   
    d.  Maak database- en doeltabellen.
   
    e.  Gebruik een van de methoden voor het importeren in bulk om de gegevens te laden.
   
    f.  Als tabeljoins vereist zijn, maakt u indexen om joins te versnellen.
   
   > [!NOTE]
   > Voor een snellere belasting van grote gegevensformaten wordt aanbevolen dat u partitietabellen maakt en de gegevens in bulk parallel importeert. Zie [Parallelle gegevens importeren naar SQL-partitietabellen](parallel-load-sql-partitioned-tables.md)voor meer informatie.
   > 
   > 
1. Verken gegevens, maak functies waar nodig. De functies hoeven niet te worden gematerialiseerd in de databasetabellen. Noteer alleen de benodigde query om ze te maken.
1. Bepaal de som grootte van een gegevenssteekproef, indien nodig en/of gewenst.
1. Meld u aan bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Lees de gegevens rechtstreeks van de SQL Server met behulp van de module [Gegevens importeren.][import-data] Plak de benodigde query die velden uitextraheert, functies maakt en gegevens, indien nodig, indien nodig rechtstreeks in de query [Gegevens importeren.][import-data]
1. Eenvoudige Azure Machine Learning-experimentstroom die begint met geüploade gegevensset

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Scenario \#6: Grote gegevensset in een SQL Server-database op locatie, gericht op SQL Server in een Azure Virtual Machine
![Grote SQL DB on-prem naar SQL DB in Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Aanvullende Azure-bronnen: Azure Virtual Machine (SQL Server / IPython Notebook-server)
1. Maak een Azure Virtual Machine met SQL Server en IPython Notebook server.
1. Gebruik een van de methoden voor het exporteren van gegevens om de gegevens van SQL Server te exporteren naar het dumpen van bestanden.
   
   > [!NOTE]
   > Als u besluit om alle gegevens uit de on-premises database te verplaatsen, een alternatieve (snellere) methode om de volledige database te verplaatsen naar de SQL Server-instantie in Azure. Sla de stappen over om gegevens te exporteren, database te maken en gegevens te laden/importeren naar de doeldatabase en volg de alternatieve methode.
   > 
   > 
1. Dumpbestanden uploaden naar de Azure Storage-container.
1. Laad de gegevens naar een SQL Server-database die wordt uitgevoerd op een Azure Virtual Machine.
   
   a.  Meld u aan bij de SQL Server VM.
   
   b.  Download gegevensbestanden uit een Azure Storage-container naar de map lokaal-VM.
   
   c.  SQL Server Management Studio uitvoeren.
   
   d.  Maak database- en doeltabellen.
   
   e.  Gebruik een van de methoden voor het importeren in bulk om de gegevens te laden.
   
   f.  Als tabeljoins vereist zijn, maakt u indexen om joins te versnellen.
   
   > [!NOTE]
   > Voor het sneller laden van grote gegevensformaten maakt u partitietabellen en importeert u de gegevens in bulk parallel. Zie [Parallelle gegevens importeren naar SQL-partitietabellen](parallel-load-sql-partitioned-tables.md)voor meer informatie.
   > 
   > 
1. Verken gegevens, maak functies waar nodig. De functies hoeven niet te worden gematerialiseerd in de databasetabellen. Noteer alleen de benodigde query om ze te maken.
1. Bepaal de som grootte van een gegevenssteekproef, indien nodig en/of gewenst.
1. Meld u aan bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Lees de gegevens rechtstreeks van de SQL Server met behulp van de module [Gegevens importeren.][import-data] Plak de benodigde query die velden uitextraheert, functies maakt en gegevens, indien nodig, indien nodig rechtstreeks in de query [Gegevens importeren.][import-data]
1. Eenvoudige Azure Machine Learning-experimentstroom die begint met geüploade gegevensset.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternatieve methode om een volledige database te kopiëren van een on-premises SQL Server naar Azure SQL Database
![Lokale DB loskoppelen en koppelen aan SQL DB in Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Aanvullende Azure-bronnen: Azure Virtual Machine (SQL Server / IPython Notebook-server)
Als u de volledige SQL Server-database in uw SQL Server VM wilt repliceren, moet u een database van de ene locatie/server naar de andere kopiëren, ervan uitgaande dat de database tijdelijk offline kan worden gehaald. U SQL Server Management Studio Object Explorer gebruiken of de gelijkwaardige Transact-SQL-opdrachten gebruiken.

1. Maak de database los op de bronlocatie. Zie [Een database loskoppelen](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx)voor meer informatie.
1. Kopieer in het venster Windows Verkenner of Windows Opdrachtprompt het losgekoppelde databasebestand of -bestanden en logboekbestanden naar de doellocatie op de SQL Server VM in Azure.
1. Voeg de gekopieerde bestanden toe aan de doelSQL Server-instantie. Zie [Een database bijvoegen](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx)voor meer informatie.

[Een database verplaatsen met losmaken en koppelen (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Scenario \#7: Big data in lokale bestanden, target Hive-database in Azure HDInsight Hadoop-clusters
![Big data in lokaal doel Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Aanvullende Azure-bronnen: Azure HDInsight Hadoop-cluster en Azure Virtual Machine (IPython-laptopserver)
1. Maak een Azure Virtual Machine met IPython Notebook-server.
1. Maak een Azure HDInsight Hadoop-cluster.
1. (Optioneel) Pre-proces en schone gegevens.
   
   a.  Gegevens vooraf verwerken en schoonmaken in IPython-notitieblok en toegang krijgen tot gegevens uit Azure
   
       blobs.
   
   b.  Gegevens zo nodig omzetten in een gereinigde tabelvorm.
   
   c.  Gegevens opslaan in VM-lokale bestanden (IPython Notebook wordt uitgevoerd op VM, lokale stations verwijzen naar VM-stations).
1. Gegevens uploaden naar de standaardcontainer van het Hadoop-cluster geselecteerd in stap 2.
1. Gegevens laden naar Hive-database in azure HDInsight Hadoop-cluster.
   
   a.  Log in op het hoofdknooppunt van het Hadoop-cluster
   
   b.  Open de Hadoop-commandolijn.
   
   c.  Voer de hoofdmap `cd %hive_home%\bin` Van Hive in op opdracht in Hadoop Command Line.
   
   d.  Voer de Hive-query's uit om database- en tabellen te maken en gegevens te laden van blobopslag naar Hive-tabellen.
   
   > [!NOTE]
   > Als de gegevens groot zijn, kunnen gebruikers de Hive-tabel met partities maken. Vervolgens kunnen gebruikers `for` een lus gebruiken in de Hadoop-opdrachtregel op het hoofdknooppunt om gegevens te laden in de Hive-tabel die is verdeeld door partitie.
   > 
   > 
1. Verken gegevens en maak functies waar nodig in Hadoop Command Line. De functies hoeven niet te worden gematerialiseerd in de databasetabellen. Noteer alleen de benodigde query om ze te maken.
   
   a.  Log in op het hoofdknooppunt van het Hadoop-cluster
   
   b.  Open de Hadoop-commandolijn.
   
   c.  Voer de hoofdmap `cd %hive_home%\bin` Van Hive in op opdracht in Hadoop Command Line.
   
   d.  Voer de Hive-query's uit in hadoop-opdrachtregel op het hoofdknooppunt van het Hadoop-cluster om de gegevens te verkennen en functies te maken waar nodig.
1. Indien nodig en/of gewenst, u de gegevens gebruiken die passen in Azure Machine Learning Studio.
1. Meld u aan bij de [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Lees de gegevens `Hive Queries` rechtstreeks vanuit de module [Gegevens importeren.][import-data] Plak de benodigde query die velden uitextraheert, functies maakt en gegevens, indien nodig, indien nodig rechtstreeks in de query [Gegevens importeren.][import-data]
1. Eenvoudige Azure Machine Learning-experimentstroom die begint met geüploade gegevensset.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Beslissingsstructuur voor scenarioselectie
---
In het volgende diagram worden de hierboven beschreven scenario's en de hierboven gemaakte geavanceerde analyseprocessen en -technologiedie u naar elk van de gespecificeerde scenario's brengen. Gegevensverwerking, exploratie, functieengineering en sampling kunnen plaatsvinden in een of meer methoden/omgevingen - bij de bron-, intermediaire en/of doelomgevingen - en kunnen indien nodig iteratief verlopen. Het diagram dient slechts als illustratie van enkele mogelijke stromen en biedt geen uitputtende opsomming.

![Voorbeeld van DS-scenario's voor het doorlopen van ds-processen][8]

### <a name="advanced-analytics-in-action-examples"></a>Geavanceerde analytics in actie Voorbeelden
Zie voor end-to-end Azure Machine Learning-walkthroughs die gebruikmaken van het Advanced Analytics-proces en -technologie met behulp van openbare gegevenssets:

* [Team Data Science Process in actie: sql server gebruiken](sql-walkthrough.md).
* [Team Data Science Process in actie: met HDInsight Hadoop clusters](hive-walkthrough.md).

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
