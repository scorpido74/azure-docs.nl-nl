---
title: Platformen en hulpprogramma's voor data science-projecten - Team Data Science Process
description: Vindt u een specificatie en worden de gegevens en analyses resources beschikbaar voor ondernemingen standaardiseren het Team Data Science Process besproken.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 97dafe292ff3ed0ef5fa46eb895136e3b32e62c5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978506"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformen en hulpprogramma's voor data science-projecten

Microsoft biedt een volledige spectrum van gegevens en analyses services en resources voor zowel de cloud of on-premises platforms. Ze kunnen worden geïmplementeerd als u de uitvoering van uw data science-projecten efficiënter en schaalbare. Richtlijnen voor het implementeren van data science-projecten in een herleidbare teams, versie beheerd en gezamenlijke manier wordt geleverd door de [Team Data Science Process](overview.md) (TDSP).  Zie voor een overzicht van de rollen personeel en de bijbehorende taken die worden beheerd door een data science team standaardiseren over dit proces, [Team Data Science Process rollen en taken](roles-tasks.md).

De gegevens en analyses services beschikbaar zijn voor data scienceteams met behulp van de TDSP zijn onder andere:

- Data Science Virtual Machines (zowel Windows als Linux CentOS)
- HDInsight Spark-Clusters
- SQL Data Warehouse
- Azure Data Lake
- Hive met HDInsight-Clusters
- Azure File Storage
- SQL Server 2016 R Services

In dit document, we kort beschrijven de resources en bevatten koppelingen naar de zelfstudies en scenario's de TDSP-teams hebt gepubliceerd. Ze kunnen helpen u informatie over het gebruik van deze stap voor stap en gebruik worden genomen om uw intelligente toepassingen te bouwen. Meer informatie over deze resources is beschikbaar op de pagina's met product. 

## <a name="data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM)

De data science virtual machine die wordt aangeboden op zowel Windows als Linux door Microsoft, bevat populaire hulpprogramma's voor data science modelleren en ontwikkeling. Het bevat hulpprogramma's zoals:

- Microsoft R Server Developer Edition 
- Anaconda Python-distributie
- Jupyter-notebooks voor Python / R 
- Visual Studio Community-editie met Python en R-hulpprogramma's op Windows / Eclipse op Linux
- Power BI desktop voor Windows
- SQL Server 2016 Developer Edition op Windows / Postgres op Linux

Het bevat ook **ml-en AI-hulpprogram ma's** zoals xgboost, Mxnet en Vowpal Wabbit.

DSVM is momenteel beschikbaar in **Windows** en **Linux CentOS** besturingssystemen. Kies de grootte van uw DSVM (aantal CPU-kernen) en de hoeveelheid geheugen op basis van de behoeften van de data science-projecten die u van plan bent om uit te voeren op het. 

Zie voor meer informatie over Windows-editie van DSVM [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) op Azure marketplace. Zie voor de Linux-versie van de DSVM, [Linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Zie voor meer informatie over enkele van de algemene datatechnologietaken efficiënt uitvoeren op de DSVM, [tien dingen die u op de virtuele Machine voor datatechnologie doen kunt](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark-clusters

Apache Spark is een open-source parallelle verwerkingsframework die ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor gegevensanalyse van big data te verbeteren. De Spark-verwerkingsengine is gebouwd voor snelheid, gebruiksgemak, en geavanceerde analyses. In-memory-rekencapaciteiten van Spark kunnen u een goede keuze voor zich herhalende algoritmen in machine learning en voor graph-berekeningen. Spark is ook compatibel met Azure Blob storage (WASB), zodat uw bestaande gegevens die zijn opgeslagen in Azure, gemakkelijk worden verwerkt met behulp van Spark.

Wanneer u in HDInsight een Spark-cluster maakt, maakt u rekenresources in Azure, waarbij Spark is geïnstalleerd en geconfigureerd. Het duurt ongeveer 10 minuten om te maken van een Spark-cluster in HDInsight. De gegevens die moeten worden verwerkt in Azure Blob-opslag Store. Zie voor meer informatie over het gebruik van Azure Blob-opslag met een cluster [HDFS-compatibele Azure Blob storage gebruiken met Hadoop in HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP-team van Microsoft die beschikbaar heeft gesteld twee end-to-end-scenario's voor het gebruik van Azure HDInsight Spark-Clusters voor het bouwen van oplossingen voor big data, één met Python en de andere Scala. Voor meer informatie over Azure HDInsight **Spark-Clusters**, Zie [overzicht: Apache Spark in HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Voor meer informatie over het bouwen van een data science oplossing met **Python** op een Azure HDInsight Spark-Cluster, Zie [overzicht van Gegevenswetenschap met Spark op Azure HDInsight](spark-overview.md). Voor meer informatie over het bouwen van een data science oplossing met **Scala** op een Azure HDInsight Spark-Cluster, Zie [Gegevenswetenschap met Scala en Spark op Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse kunt u de schaal van compute-resources eenvoudig en in seconden, zonder de capaciteit in te richten of te veel te moeten betalen. Het biedt ook de unieke mogelijkheid om te onderbreken van het gebruik van rekenresources, beschikt u over de vrijheid om uw cloudkosten beter te beheren. De mogelijkheid om schaalbare Computing-resources te implementeren, is het mogelijk om al uw gegevens in Azure SQL Data Warehouse. Kosten voor opslag zijn minimaal en u kunt gebruik Computing alleen op de onderdelen van gegevenssets die u wilt analyseren. 

Zie voor meer informatie over Azure SQL Data Warehouse de [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) website. Zie voor meer informatie over het bouwen van geavanceerde analyses van end-to-end-oplossingen met SQL Data Warehouse, [het Team Data Science Process in actie: met behulp van SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure data lake is als een bedrijfsbrede opslagruimte voor elk soort gegevens die zijn verzameld op één locatie, voordat u een formele vereisten of schema wordt opgelegd. Deze flexibiliteit kunt elk soort gegevens worden bewaard in een data lake, ongeacht het formaat of de structuur of hoe snel worden opgenomen. Organisaties kunnen vervolgens gebruiken voor Hadoop of geavanceerde analyses om te zoeken, patronen in deze data lakes. Datalakes kunnen ook dienen als een opslagplaats voor goedkopere gegevens voor te bereiden voordat de inrichting van de gegevens en u deze verplaatst naar een datawarehouse.

Zie voor meer informatie over Azure Data Lake [Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Zie voor meer informatie over het bouwen van een schaalbare end-to-end data science-oplossing met Azure Data Lake, [schaalbare Gegevenswetenschap in Azure Data Lake: een end-to-end-overzicht](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Hive met Azure HDInsight (Hadoop)-clusters

Apache Hive is een systeem voor het datawarehouse van gegevens voor Hadoop, waarmee gegevenssamenvatting, query's en de analyse van gegevens met behulp van HiveQL, een querytaal die vergelijkbaar is met SQL. Hive kan worden gebruikt met het interactief verkennen van uw gegevens of herbruikbare batch verwerking jobs maken.

Hive kunt u de projectstructuur van het op grotendeels niet-gestructureerde gegevens. Nadat u de structuur te definiëren, kunt u Hive kunt gebruiken om op te vragen die gegevens in een Hadoop-cluster zonder te hoeven gebruiken, of zelfs weet, Java- of MapReduce. HiveQL (Hive query language) kunt u met de instructies die vergelijkbaar met T-SQL zijn-query's schrijven.

Voor data scientists, kunt Hive Python User-Defined functies (UDF's) uitvoeren in Hive-query's voor het verwerken van records. Deze mogelijkheid breidt de mogelijkheden van Hive-query's in data-analyse aanzienlijk. Hierdoor specifiek, kan de datawetenschappers voor het uitvoeren van schaalbare feature-engineering in talen die ze vooral bekend zijn met zijn: de SQL-achtige HiveQL en Python. 

Zie voor meer informatie over Azure HDInsight Hive-Clusters, [Hive en HiveQL met Hadoop in HDInsight gebruiken](../../hdinsight/hadoop/hdinsight-use-hive.md). Zie voor meer informatie over het bouwen van een schaalbare end-to-end data science-oplossing met Azure HDInsight Hive-Clusters, [het Team Data Science Process in actie: met behulp van HDInsight Hadoop-clusters](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage is een service die bestandsshares in de cloud die het Server Message Block (SMB)-Protocol biedt. Zowel SMB 2.1 als SMB 3.0 wordt ondersteund. Met Azure File Storage kunt u oudere toepassingen die afhankelijk zijn van bestandsshares, snel en zonder kostbare regeneraties naar Azure migreren. Toepassingen die worden uitgevoerd op virtuele machines of in cloudservices van Azure, of op on-premises clients, kunnen een bestandsshare in de cloud koppelen, op dezelfde manier zoals een bureaubladtoepassing een gewone SMB-share koppelt. Daarna kan een willekeurig aantal toepassingsonderdelen de File Storage-share tegelijkertijd koppelen en gebruiken.

Met name nuttig voor data science-projecten is de mogelijkheid om te maken van een archief voor Azure als het om projectgegevens te delen met uw Projectteamleden. Elk van deze vervolgens heeft toegang tot hetzelfde exemplaar van de gegevens in de Azure file storage. Ze kunnen deze bestandsopslag ook gebruiken voor het delen van de functiesets die zijn gegenereerd tijdens het uitvoeren van het project. Als het project de betrokkenheid van een client is, kan uw clients een Azure-bestandsopslag onder hun eigen Azure-abonnement met u delen van de gegevens van een project en functies kunnen maken. Op deze manier kan heeft de client volledige controle over de project-gegevensassets. Zie voor meer informatie over Azure File Storage [aan de slag met Azure File storage in Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) en [Azure File Storage gebruiken met Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

R Services (In-database) biedt een platform voor het ontwikkelen en implementeren van intelligente toepassingen die nieuwe inzichten kunnen verwerven op basis. De uitgebreide en krachtige R-taal, met inbegrip van de vele pakketten geleverd door de community R, kunt u modellen maken en genereren van voorspellingen van uw SQL Server-gegevens. Omdat de R-taal R Services (In-database) met SQL Server integreren, worden analytics dicht bij de gegevens, zodat geen van de kosten en beveiligingsrisico's die zijn gekoppeld aan het verplaatsen van gegevens bewaard.

R Services (In-database) ondersteuning voor de open source R-taal met een uitgebreide set met SQL Server-hulpprogramma's en technologieën. Ze bieden uitstekende prestaties, beveiliging, betrouwbaarheid en beheermogelijkheden. U kunt R-oplossingen met behulp van handige en vertrouwde hulpprogramma's kunt implementeren. Uw productietoepassingen kunnen aanroepen van de R-runtime en ophalen van de voorspellings- en visuele elementen met behulp van Transact-SQL. U kunt ook de ScaleR-bibliotheken gebruiken voor het verbeteren van de schaal en prestaties van uw R-oplossingen. Zie voor meer informatie, [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

Twee end-to-end-scenario's die laten zien hoe u oplossingen voor big data in SQL Server 2016 R Services bouwen die de TDSP-team van Microsoft beschikbaar heeft gesteld: één voor R-programmeurs en één voor SQL-ontwikkelaars. Voor **R-programmeurs**, Zie [Data Science End-to-End scenario](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Voor **SQL ontwikkelaars**, Zie [In Database Advanced Analytics voor SQL-ontwikkelaars (zelfstudie)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>Bijlage: Hulpprogramma's voor het instellen van wetenschappelijke gegevensprojecten

### <a name="install-git-credential-manager-on-windows"></a>Git Credential Manager installeren op Windows

Als u de TDSP op **Windows**, moet u voor het installeren van de **Git Credential Manager (GCM)** om te communiceren met de Git-opslagplaatsen. Voor het installeren van GCM, moet u eerst installeren **Chocolaty**. Als u wilt installeren Chocolaty en de GCM, kunt u de volgende opdrachten uitvoeren in Windows PowerShell als een **beheerder**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Installeer Git op machines voor Linux (CentOS)

Voer de volgende bash-opdracht uit om Git te installeren op Linux (CentOS) virtuele machines:

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Openbare SSH-sleutel op machines voor Linux (CentOS)

Als u van machines voor Linux (CentOS gebruikmaakt) de git-opdrachten uitvoeren, moet u de openbare SSH-sleutel van uw computer toevoegen aan uw Azure DevOps-Services, zodat deze computer wordt herkend door het Azure DevOps-Services. Eerst moet u een openbare SSH-sleutel genereren en de sleutel toevoegen aan de openbare SSH-sleutels in de instellingenpagina van uw Azure DevOps-Services-beveiliging. 

1. Voer de volgende twee opdrachten voor het genereren van de SSH-sleutel: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Opdrachten voor het genereren van de SSH-sleutel](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Kopieer de gehele ssh key inclusief *ssh-rsa*. 
1. Meld u aan bij uw Azure DevOps-Services. 
1. Klik op **< naam van uw\>**  in de rechterbovenhoek van de pagina en klik op **security**. 
    
   ![Klik op uw naam en klik vervolgens op beveiliging](./media/platforms-and-tools/resources-2-user-setting.png)

1. Klik op **openbare SSH-sleutels**, en klikt u op **+ toevoegen**. 

   ![Klik op de openbare SSH-sleutels en vervolgens klikt u op + toevoegen](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Plak de ssh sleutel hebt gekopieerd in het tekstvak en opslaan.


## <a name="next-steps"></a>Volgende stappen

End-to-end-scenario's die laten zien van alle de stappen in het proces voor het volledige **specifieke scenario's** worden ook gegeven. Ze worden weergegeven en die is gekoppeld met miniaturen beschrijvingen in de [voorbeeld walkthroughs](walkthroughs.md) onderwerp. Ze laten zien hoe u naar de cloud, on-premises hulpprogramma's en services combineren in een werkstroom of een pijplijn te maken van een intelligente toepassingen. 

Voor voor beelden die laten zien hoe u de stappen in het team data Science process uitvoert met behulp van Azure Machine Learning Studio (klassiek), raadpleegt u het leer traject [van Azure ml](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) .
