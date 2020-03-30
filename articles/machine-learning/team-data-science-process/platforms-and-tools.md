---
title: Platforms en tools voor data science projecten - Team Data Science Process
description: Itemizes en bespreekt de gegevens en analysebronnen die beschikbaar zijn voor ondernemingen die standaardiseren in het Team Data Science-proces.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3297319c67ad2b7c94371356cde49113c7ef737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251607"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platforms en tools voor data science projecten

Microsoft biedt een volledig spectrum van analysebronnen voor zowel cloud- als on-premises platforms. Ze kunnen worden ingezet om de uitvoering van uw data science-projecten efficiënt en schaalbaar te maken. Richtlijnen voor teams die data science-projecten op een traceerbare, versiebeheerde en collaboratieve manier implementeren, worden geleverd door het [Team Data Science Process](overview.md) (TDSP).  Zie [Team Data Science Process rollen en taken](roles-tasks.md)voor een overzicht van de personeelsrollen en de bijbehorende taken die worden uitgevoerd door een data science-team dat dit proces standaardiseert.

De analysebronnen die beschikbaar zijn voor data science-teams die gebruikmaken van de TDSP zijn:

- Virtuele machines voor Data Science (zowel Windows als Linux CentOS)
- HDInsight Spark-clusters
- Synapse Analytics
- Azure Data Lake
- HDInsight Hive Clusters
- Azure File Storage
- SQL Server 2019 R en Python Services
- Azure Databricks

In dit document beschrijven we kort de bronnen en bieden we links naar de tutorials en walkthroughs die de TDSP-teams hebben gepubliceerd. Ze kunnen u helpen te leren hoe ze stap voor stap te gebruiken en beginnen ze te gebruiken om uw intelligente toepassingen te bouwen. Meer informatie over deze bronnen is beschikbaar op hun productpagina's. 

## <a name="data-science-virtual-machine-dsvm"></a>Virtuele machine data science (DSVM)

De data science virtuele machine aangeboden op zowel Windows en Linux door Microsoft, bevat populaire tools voor data science modellering en ontwikkeling activiteiten. Het bevat tools zoals:

- Microsoft R Server Developer Edition 
- Anaconda Python-distributie
- Jupyter-notitieblokken voor Python en R 
- Visual Studio Community Edition met Python en R Tools op Windows / Eclipse op Linux
- Power BI-bureaublad voor Windows
- SQL Server 2016 Developer Edition op Windows / Postgres op Linux

Het bevat ook **ML- en AI-tools** zoals xgboost, mxnet en Vowpal Wabbit.

Momenteel is DSVM beschikbaar in **Windows-** en **Linux CentOS-besturingssystemen.** Kies de grootte van uw DSVM (aantal CPU-cores en de hoeveelheid geheugen) op basis van de behoeften van de data science-projecten die u ervan van plan bent uit te voeren. 

Zie [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) op de Azure Marketplace voor meer informatie over de Windows-editie van DSVM. Voor de Linux-editie van de DSVM, zie [Linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Zie 10 dingen die u doen op de Virtuele Machine voor gegevenswetenschap op de DSVM voor meer informatie over het efficiënt uitvoeren van enkele van de algemene taken op het gebied van [gegevenswetenschap op de](../data-science-virtual-machine/vm-do-ten-things.md) DSVM


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark-clusters

Apache Spark is een open-source framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. De Spark processing engine is gebouwd voor snelheid, gebruiksgemak en geavanceerde analyses. Spark's in-memory rekenmogelijkheden maken het een goede keuze voor iteratieve algoritmen in machine learning en voor grafiekberekeningen. Spark is ook compatibel met Azure Blob storage (WASB), zodat uw bestaande gegevens die in Azure zijn opgeslagen eenvoudig kunnen worden verwerkt met Spark.

Wanneer u in HDInsight een Spark-cluster maakt, maakt u rekenresources in Azure, waarbij Spark is geïnstalleerd en geconfigureerd. Het duurt ongeveer 10 minuten om een Spark-cluster in HDInsight te maken. Sla de gegevens op die moeten worden verwerkt in Azure Blob-opslag. Zie [Azure Blob-opslag met HDFS-compatibele Azure Blob-opslag gebruiken met Hadoop in HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)voor informatie over het gebruik van Azure Blob Storage met een cluster.

Het TDSP-team van Microsoft heeft twee end-to-end walkthroughs gepubliceerd over het gebruik van Azure HDInsight Spark Clusters om data science-oplossingen te bouwen, een met Python en de andere Scala. Voor meer informatie over Azure HDInsight **Spark Clusters,** zie [Overzicht: Apache Spark op HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Zie [Overzicht van Gegevenswetenschap met Spark op Azure HDInsight](spark-overview.md)voor meer informatie over het bouwen van een data science-oplossing met **Python** op een Azure HDInsight Spark-cluster. Zie [Data Science met Scala en Spark op Azure](scala-walkthrough.md)voor meer informatie over het bouwen van een data science-oplossing met **Scala** op een Azure HDInsight Spark-cluster. 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Met Azure SQL Data Warehouse u compute resources eenvoudig en in enkele seconden schalen, zonder overprovisioning of over-paying. Het biedt ook de unieke optie om het gebruik van compute resources te pauzeren, zodat u de vrijheid hebt om uw cloudkosten beter te beheren. De mogelijkheid om schaalbare compute resources te implementeren maakt het mogelijk om al uw gegevens in Azure SQL Data Warehouse te brengen. De opslagkosten zijn minimaal en u alleen berekenen uitvoeren op de delen van gegevenssets die u wilt analyseren. 

Zie de SQL [Data Warehouse-website](https://azure.microsoft.com/services/sql-data-warehouse) voor meer informatie over Azure SQL Data Warehouse. Zie [The Team Data Science Process in actie voor](sqldw-walkthrough.md)meer informatie over het bouwen van end-to-end geavanceerde analyseoplossingen met SQL Data Warehouse.


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake is als een bedrijfsbrede opslagplaats van elk type gegevens dat op één locatie wordt verzameld, voordat formele vereisten of schema's worden opgelegd. Deze flexibiliteit maakt het mogelijk om elk type gegevens in een gegevensmeer te bewaren, ongeacht de grootte of structuur of hoe snel het wordt ingenomen. Organisaties kunnen dan Hadoop of geavanceerde analyses gebruiken om patronen in deze gegevensmeren te vinden. Gegevensmeren kunnen ook dienen als opslagplaats voor het opstellen van goedkopere gegevens voordat u de gegevens curating en verplaatst naar een gegevensmagazijn.

Zie Azure Data Lake introduceren voor meer informatie over Azure Data [Lake.](https://azure.microsoft.com/blog/introducing-azure-data-lake/) Zie [Scalable Data Science in Azure Data Lake: een end-to-end walkthrough](data-lake-walkthrough.md) voor meer informatie over het bouwen van een schaalbare end-to-end data science-oplossing met Azure Data Lake


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive (Hadoop) clusters

Apache Hive is een data warehouse systeem voor Hadoop, waarmee gegevens optellen, query's en de analyse van gegevens met behulp van HiveQL, een query taal vergelijkbaar met SQL. Hive kan worden gebruikt om uw gegevens interactief te verkennen of om herbruikbare batchverwerkingstaken te maken.

Met Hive u structuur projecteren op grotendeels ongestructureerde gegevens. Nadat u de structuur hebt gedefinieerd, u Hive gebruiken om die gegevens op te vragen in een Hadoop-cluster zonder Java of MapReduce te hoeven gebruiken of zelfs te weten. Met HiveQL (de Hive-querytaal) u query's schrijven met instructies die vergelijkbaar zijn met T-SQL.

Voor gegevenswetenschappers kan Hive Python-gebruikersgedefinieerde functies (UDF's) uitvoeren in Hive-query's om records te verwerken. Deze mogelijkheid breidt de mogelijkheden van Hive-query's in gegevensanalyse aanzienlijk uit. Concreet kunnen gegevenswetenschappers schaalbare functie-engineering uitvoeren in talen die ze meestal kennen: de SQL-achtige HiveQL en Python. 

Zie [Hive en HiveQL gebruiken met Hadoop in HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md)voor meer informatie over Azure HDInsight Hive-clusters. Zie [The Team Data Science Process in actie voor](hive-walkthrough.md)meer informatie over het bouwen van een schaalbare end-to-end data science-oplossing met Azure HDInsight Hive Clusters.


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage is een service die bestandsshares in de cloud aanbiedt met behulp van het standaard SMB-protocol (Server Message Block). Zowel SMB 2.1 als SMB 3.0 wordt ondersteund. Met Azure File Storage kunt u oudere toepassingen die afhankelijk zijn van bestandsshares, snel en zonder kostbare regeneraties naar Azure migreren. Toepassingen die worden uitgevoerd op virtuele machines of in cloudservices van Azure, of op on-premises clients, kunnen een bestandsshare in de cloud koppelen, op dezelfde manier zoals een bureaubladtoepassing een gewone SMB-share koppelt. Daarna kan een willekeurig aantal toepassingsonderdelen de File Storage-share tegelijkertijd koppelen en gebruiken.

Vooral handig voor data science-projecten is de mogelijkheid om een Azure-bestandsarchief te maken als de plek om projectgegevens te delen met uw projectteamleden. Elk van hen heeft dan toegang tot dezelfde kopie van de gegevens in de Azure-bestandsopslag. Ze kunnen deze bestandsopslag ook gebruiken om functiesets te delen die tijdens de uitvoering van het project zijn gegenereerd. Als het project een klantbetrokkenheid is, kunnen uw klanten een Azure-bestandsopslag maken onder hun eigen Azure-abonnement om de projectgegevens en -functies met u te delen. Op deze manier heeft de klant volledige controle over de activa van de projectgegevens. Zie [Aan de slag met Azure File storage in Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) en Azure File Storage with [Linux](../../storage/files/storage-how-to-use-files-linux.md)voor meer informatie over Azure File Storage.


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R en Python Services

R Services (In-database) biedt een platform voor het ontwikkelen en implementeren van intelligente applicaties die nieuwe inzichten kunnen ontdekken. U de rijke en krachtige R-taal, inclusief de vele pakketten die door de R-community worden geleverd, gebruiken om modellen te maken en voorspellingen te genereren uit uw SQL Server-gegevens. Omdat R Services (In-database) de R-taal integreert met SQL Server, worden analyses dicht bij de gegevens gehouden, waardoor de kosten en beveiligingsrisico's die verbonden zijn aan het verplaatsen van gegevens worden geëlimineerd.

R Services (In-database) ondersteunt de open source R-taal met een uitgebreide set SQL Server-tools en -technologieën. Ze bieden superieure prestaties, beveiliging, betrouwbaarheid en beheerbaarheid. U R-oplossingen implementeren met handige en vertrouwde tools. Uw productietoepassingen kunnen de R-runtime aanroepen en voorspellingen en visuals ophalen met Transact-SQL. U gebruikt ook de ScaleR-bibliotheken om de schaal en prestaties van uw R-oplossingen te verbeteren. Zie [SQL Server R-services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)voor meer informatie.

Het TDSP-team van Microsoft heeft twee end-to-end walkthroughs gepubliceerd die laten zien hoe je data science-oplossingen bouwen in SQL Server 2016 R Services: een voor R-programmeurs en een voor SQL-ontwikkelaars. Voor **R-programmeurs,** zie [Data Science End-to-End Walkthrough](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Zie [In-Database Advanced Analytics voor SQL-ontwikkelaars (Zelfstudie) voor](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers) **SQL-ontwikkelaars.**


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Bijlage: Hulpmiddelen voor het opzetten van data science-projecten

### <a name="install-git-credential-manager-on-windows"></a>Git Credential Manager installeren in Windows

Als u de TDSP op **Windows**volgt, moet u de **Git Credential Manager (GCM)** installeren om te communiceren met de Git-repositories. Om GCM te installeren, moet u **eerst Chocolaty**installeren. Als u Chocolaty en de GCM wilt installeren, voert u de volgende opdrachten uit in Windows PowerShell als **beheerder:**  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Git installeren op Linux (CentOS) machines

Voer de volgende bash-opdracht uit om Git te installeren op Linux -machines (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Openbare SSH-toets genereren op Linux -machines (CentOS)

Als u Linux-machines (CentOS) gebruikt om de git-opdrachten uit te voeren, moet u de openbare SSH-sleutel van uw machine toevoegen aan uw Azure DevOps-services, zodat deze machine wordt herkend door de Azure DevOps-services. Eerst moet u een openbare SSH-sleutel genereren en de sleutel toevoegen aan openbare SSH-sleutels op uw beveiligingspagina voor Azure DevOps Services. 

1. Voer de volgende twee opdrachten uit om de SSH-toets te genereren: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Opdrachten voor het genereren van de SSH-toets](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Kopieer de hele ssh-toets inclusief *ssh-rsa.* 
1. Meld u aan bij uw Azure DevOps Services. 
1. Klik **<\> uw naam** in de rechterbovenhoek van de pagina en klik op **beveiliging**. 
    
   ![Klik op uw naam en klik vervolgens op beveiliging](./media/platforms-and-tools/resources-2-user-setting.png)

1. Klik op **openbare ssh-toetsen**en klik op **+Toevoegen**. 

   ![Klik op openbare ssh-toetsen en klik vervolgens op +Toevoegen](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Plak de ssh-toets die in het tekstvak is gekopieerd en sla op.


## <a name="next-steps"></a>Volgende stappen

Volledige end-to-end walkthroughs die alle stappen in het proces voor **specifieke scenario's** demonstreren, worden ook verstrekt. Ze worden weergegeven en gekoppeld aan miniatuurbeschrijvingen in het onderwerp [Voorbeeld van walkthroughs.](walkthroughs.md) Ze illustreren hoe cloud- en on-premises tools en services kunnen worden gecombineerd in een workflow of pijplijn om een intelligente toepassing te maken. 

Zie het leerpad [Met Azure ML](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) voor voorbeelden die laten zien hoe u stappen in het Team Data Science-proces uitvoert met Azure Machine Learning Studio (klassiek).
