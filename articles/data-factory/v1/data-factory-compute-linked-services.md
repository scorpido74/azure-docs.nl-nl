---
title: Compute-omgevingen ondersteund door Azure Data Factory
description: Meer informatie over rekenomgevingen die u gebruiken in Azure Data Factory-pijplijnen (zoals Azure HDInsight) om gegevens te transformeren of te verwerken.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0cc7c3b7d8b364e0bcca671efaff2cf324695428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281546"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Compute-omgevingen ondersteund door Azure Data Factory
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. Zie [Gekoppelde services](../compute-linked-services.md)berekenen in .

In dit artikel worden de compute-omgevingen uitgelegd die u gebruiken om gegevens te verwerken of te transformeren. Het bevat ook details over verschillende configuraties (on-demand versus bring-your-own) die Data Factory ondersteunt wanneer u gekoppelde services configureert die deze compute-omgevingen koppelen aan een Azure-gegevensfabriek.

In de volgende tabel vindt u een lijst met rekenomgevingen die worden ondersteund door Data Factory en de activiteiten die erop kunnen worden uitgevoerd. 

| Compute-omgeving                      | Activiteiten                               |
| ---------------------------------------- | ---------------------------------------- |
| [On-demand Azure HDInsight-cluster](#azure-hdinsight-on-demand-linked-service) of [uw eigen HDInsight-cluster](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-activiteiten: batchuitvoering en resources bijwerken](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), SQL [Server](#sql-server-linked-service) | [Opgeslagen procedureactiviteit](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>HDInsight-versies ondersteund in Data Factory
Azure HDInsight ondersteunt meerdere Hadoop-clusterversies die u op elk gewenst moment implementeren. Elke ondersteunde versie maakt een specifieke versie van de Hortonworks Data Platform (HDP) distributie en een set componenten in de distributie. 

Microsoft werkt de lijst met ondersteunde HDInsight-versies bij met de nieuwste Hadoop-ecosysteemcomponenten en -oplossingen. Zie [Ondersteunde HDInsight-versies](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)voor gedetailleerde informatie.

> [!IMPORTANT]
> Linux-gebaseerde HDInsight versie 3.3 werd op 31 juli 2017 met pensioen gestuurd. Data Factory versie 1 on-demand HDInsight linked services klanten kregen tot 15 december 2017 de tijd om te testen en te upgraden naar een latere versie van HDInsight. HdInsight op basis van Windows wordt op 31 juli 2018 met pensioen gestuurd.
>
> 

### <a name="after-the-retirement-date"></a>Na de pensioendatum 

Na 15 december 2017:

- U niet langer hdinsight-versie 3.3 (of eerdere versies) clusters op basis van Linux maken met behulp van een on-demand HDInsight-gekoppelde service in Data Factory-versie 1. 
- Als de [ **eigenschappen van osType** en **Versie** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) niet expliciet zijn opgegeven in de JSON-definitie voor een bestaande Data Factory-versie 1 on-demand HDInsight gekoppelde service, wordt de standaardwaarde gewijzigd van **Version=3.1, osType=Windows** naar **Version=\<latest HDI default version\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType=Linux**.

Na 31 juli 2018:

- U geen versie van HdInsight-clusters op basis van Windows meer maken met behulp van een on-demand HDInsight-gekoppelde service in Data Factory-versie 1. 

### <a name="recommended-actions"></a>Aanbevolen acties 

- Om ervoor te zorgen dat u de nieuwste Hadoop-ecosysteemcomponenten en -fixes gebruiken, werkt u de [ **osType-** en **versie-eigenschappen** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) van de betreffende Data Factory-versie 1 on-demand HDInsight-gekoppelde servicedefinities bij aan nieuwere HDInsight-versies op basis van Linux (HDInsight 3.6). 
- Test voor 15 december 2017 Data Factory-versie 1 Hive, Pig, MapReduce en Hadoop-streamingactiviteiten die verwijzen naar de betreffende gekoppelde service. Zorg ervoor dat ze compatibel zijn met de standaardwaarden **van osType** en Versie **(Version=3.6**, **osType=Linux)** of de expliciete HDInsight-versie en het besturingssysteem waarnaar u een upgrade uitvoert. **Version** 
  Zie Migreren van een [HdInsight-cluster in Windows naar een op Linux gebaseerd cluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) en Wat zijn de [Hadoop-componenten en -versies beschikbaar met HDInsight voor](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions)meer informatie over compatibiliteit? 
- Als u een on-demand HDInsight-service voor versie 1 on-demand wilt blijven gebruiken om HDInsight-clusters op basis van Windows te maken, stelt u **osType** expliciet in op **Windows** vóór 15 december 2017. We raden u aan vóór 31 juli 2018 te migreren naar HDInsight-clusters op linux. 
- Als u een on-demand HDInsight-gekoppelde service gebruikt om gegevensfabriekversie 1 DotNet Aangepaste activiteit uit te voeren, werkt u de JSON-definitie van DotNet Aangepaste activiteit bij om in plaats daarvan een azure batch-gekoppelde service te gebruiken. Zie [Aangepaste activiteiten gebruiken in een datafabriekpijplijn](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities)voor meer informatie . 

> [!Note]
> Als u uw bestaande, bring-your-own cluster HDInsight gekoppeld apparaat gebruikt in Data Factory versie 1 of een bring-your-own en on-demand HDInsight gekoppelde service in Azure Data Factory, is er geen actie vereist. In die scenario's wordt het nieuwste versieondersteuningsbeleid van HDInsight-clusters al afgedwongen. 
>
> 


## <a name="on-demand-compute-environment"></a>On-demand compute omgeving
In een on-demand configuratie beheert Data Factory de compute-omgeving volledig. Data Factory maakt automatisch de compute-omgeving voordat een taak wordt ingediend voor het verwerken van gegevens. Wanneer de taak is voltooid, verwijdert Data Factory de compute-omgeving. 

U een gekoppelde service maken voor een on-demand compute-omgeving. Gebruik de gekoppelde service om de rekenomgeving te configureren en om gedetailleerde instellingen te beheren voor taakuitvoering, clusterbeheer en bootstrappingacties.

> [!NOTE]
> Momenteel wordt de on-demand configuratie alleen ondersteund voor HDInsight-clusters.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Een gekoppelde Azure HDInsight-service op aanvraag
Data Factory kan automatisch een Op Windows- of Linux-gebaseerd HDInsight-cluster maken voor het verwerken van gegevens. Het cluster wordt gemaakt in dezelfde regio als het opslagaccount dat aan het cluster is gekoppeld. Gebruik de eigenschap JSON **linkedServiceName** om het cluster te maken.

Let op de volgende *belangrijke* punten over on-demand HDInsight gekoppelde service:

* Het on-demand HDInsight-cluster wordt niet weergegeven in uw Azure-abonnement. De Data Factory-service beheert namens u het on-demand HDInsight-cluster.
* De logboeken voor taken die worden uitgevoerd op een on-demand HDInsight-cluster, worden gekopieerd naar het opslagaccount dat is gekoppeld aan het HDInsight-cluster. Ga naar het deelvenster **Details van activiteitsuitvoeren** om toegang te krijgen tot deze logboeken in de Azure-portal. Zie [Pijplijnen controleren en beheren](data-factory-monitor-manage-pipelines.md)voor meer informatie.
* Er worden alleen kosten in rekening gebracht voor de tijd dat het HDInsight-cluster actief is.

> [!IMPORTANT]
> Het duurt meestal *20 minuten* of langer om een on-demand HDInsight-cluster in te richten.
>
> 

### <a name="example"></a>Voorbeeld
De volgende JSON definieert een Linux-gebaseerde on-demand HDInsight linked service. Data Factory maakt automatisch een *HDInsight-cluster op Basis van Linux* wanneer het een dataslice verwerkt. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> Het HDInsight-cluster maakt een *standaardcontainer* in de Azure Blob-opslag die u opgeeft in de eigenschap JSON **linkedServiceName.** Door het ontwerp verwijdert HDInsight deze container niet wanneer het cluster wordt verwijderd. In een on-demand HDInsight-gekoppelde service wordt een HDInsight-cluster gemaakt elke keer dat een segment moet worden verwerkt, tenzij er een bestaand live cluster **(timeToLive)** is. Het cluster wordt verwijderd wanneer de verwerking is voltooid. 
>
> Naarmate er meer segmenten worden verwerkt, ziet u veel containers in uw Blob-opslag. Als u de containers niet nodig hebt voor het oplossen van problemen, u de containers verwijderen om de opslagkosten te verlagen. De namen van deze containers volgen een patroon: `adf<your Data Factory name>-<linked service name>-<date and time>`. U een hulpprogramma als [Microsoft Storage Explorer](https://storageexplorer.com/) gebruiken om containers in Blob-opslag te verwijderen.
>
> 

### <a name="properties"></a>Eigenschappen
| Eigenschap                     | Beschrijving                              | Vereist |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Stel de eigenschap type in op **HDInsightOnDemand**. | Ja      |
| clusterSize                  | Het aantal werknemers- en gegevensknooppunten in het cluster. Het HDInsight-cluster wordt gemaakt met 2 hoofdknooppunten, naast het aantal werknemersknooppunten dat u voor deze eigenschap opgeeft. De knooppunten zijn van grootte Standard_D3, die 4 kernen heeft. Een 4-worker node cluster neemt\*24 cores (4 4 =\*16 cores voor worker nodes, plus 2 4 = 8 cores voor hoofdknooppunten). Zie [Linux-gebaseerde Hadoop-clusters maken in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over de Standard_D3 laag. | Ja      |
| timeToLive                   | De toegestane idle tijd voor het on-demand HDInsight-cluster. Hiermee geeft u op hoe lang het on-demand HDInsight-cluster in leven blijft wanneer een activiteitsrun is voltooid, als er geen andere actieve taken in het cluster zijn.<br /><br />Als een activiteitsrun bijvoorbeeld 6 minuten duurt en **timeToLive** is ingesteld op 5 minuten, blijft het cluster 5 minuten in leven na de 6 minuten van het verwerken van de activiteit. Als een andere activiteitsrun wordt uitgevoerd in het venster van 6 minuten, wordt deze verwerkt door hetzelfde cluster.<br /><br />Het maken van een on-demand HDInsight-cluster is een dure bewerking (het kan even duren). Gebruik deze instelling als dat nodig is om de prestaties van een gegevensfabriek te verbeteren door een on-demand HDInsight-cluster opnieuw te gebruiken.<br /><br />Als u de **timeToLive-waarde** instelt op **0,** wordt het cluster verwijderd zodra de activiteitsrun is voltooid. Als u echter een hoge waarde instelt, blijft het cluster mogelijk inactief, wat onnodig leidt tot hoge kosten. Het is belangrijk om de juiste waarde in te stellen op basis van uw behoeften.<br /><br />Als de **timeToLive-waarde** op de juiste manier is ingesteld, kunnen meerdere pijplijnen de instantie van het on-demand HDInsight-cluster delen. | Ja      |
| versie                      | De versie van het HDInsight-cluster. Zie [Ondersteunde HDInsight-versies](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions)voor toegestane HDInsight-versies. Als deze waarde niet is opgegeven, wordt de [nieuwste STANDAARDVERSIE van HDI](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) gebruikt. | Nee       |
| linkedServiceName            | De azure storage-gekoppelde service die wordt gebruikt door het on-demand cluster voor het opslaan en verwerken van gegevens. Het HDInsight-cluster wordt gemaakt in dezelfde regio als dit opslagaccount.<p>Momenteel u geen on-demand HDInsight-cluster maken dat Azure Data Lake Store als opslag gebruikt. Als u de resultaatgegevens van HDInsight-verwerking wilt opslaan in De Gegevensarchief, gebruikt u Activiteit kopiëren om de gegevens van Blob-opslag naar Data Lake Store te kopiëren. </p> | Ja      |
| extraLinkedServiceNames | Hiermee geeft u extra opslagaccounts op voor de hdInsight-gekoppelde service. Data Factory registreert namens u de opslagaccounts. Deze opslagaccounts moeten zich in dezelfde regio bevinden als het HDInsight-cluster. Het HDInsight-cluster wordt gemaakt in dezelfde regio als het opslagaccount dat is opgegeven door de eigenschap **linkedServiceName.** | Nee       |
| osType                       | Het type besturingssysteem. Toegestane waarden zijn **Linux** en **Windows.** Als deze waarde niet is opgegeven, wordt **Linux** gebruikt.  <br /><br />We raden ten zeerste aan om HDInsight-clusters op basis van Linux te gebruiken. De pensioendatum voor HDInsight op Windows is 31 juli 2018. | Nee       |
| hcatalogLinkedServiceName    | De naam van de Azure SQL-gekoppelde service die verwijst naar de HCatalog-database. Het on-demand HDInsight-cluster wordt gemaakt met behulp van de SQL-database als metastore. | Nee       |

#### <a name="example-linkedservicenames-json"></a>Voorbeeld: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Geavanceerde eigenschappen
Voor een gedetailleerde configuratie van het on-demand HDInsight-cluster u de volgende eigenschappen opgeven:

| Eigenschap               | Beschrijving                              | Vereist |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguratie      | Hiermee geeft u de kernconfiguratieparameters (core-site.xml) op voor het te maken HDInsight-cluster. | Nee       |
| hBaseConfiguration     | Hiermee geeft u de HBase-configuratieparameters (hbase-site.xml) op voor het HDInsight-cluster. | Nee       |
| hdfsConfiguratie      | Hiermee geeft u de HDFS-configuratieparameters (hdfs-site.xml) op voor het HDInsight-cluster. | Nee       |
| hiveConfiguratie      | Hiermee geeft u de detectieparameters van Hive (hive-site.xml) op voor het HDInsight-cluster. | Nee       |
| mapReduceConfiguration | Hiermee geeft u de configuratieparameters mapreduce (mapred-site.xml) op voor het HDInsight-cluster. | Nee       |
| oozieConfiguratie     | Hiermee geeft u de Oozie-configuratieparameters (oozie-site.xml) op voor het HDInsight-cluster. | Nee       |
| stormConfiguratie     | Hiermee geeft u de parameters voor stormconfiguratie (stormsite.xml) op voor het HDInsight-cluster. | Nee       |
| garenConfiguratie      | Hiermee geeft u de YARN-configuratieparameters (yarn-site.xml) op voor het HDInsight-cluster. | Nee       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Voorbeeld: On-demand HDInsight-clusterconfiguratie met geavanceerde eigenschappen

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Knooppuntmaten
Als u de grootte van de knooppunten hoofd, gegevens en ZooKeeper wilt opgeven, gebruikt u de volgende eigenschappen: 

| Eigenschap          | Beschrijving                              | Vereist |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Hiermee stelt u de grootte van het hoofdknooppunt in. De standaardwaarde is **Standard_D3**. Zie [Knooppuntgrootte opgeven](#specify-node-sizes)voor meer informatie . | Nee       |
| dataNodeSize      | Hiermee stelt u de grootte van het gegevensknooppunt in. De standaardwaarde is **Standard_D3**. | Nee       |
| zookeeperNodeSize | Hiermee stelt u de grootte in van het knooppunt ZooKeeper. De standaardwaarde is **Standard_D3**. | Nee       |

#### <a name="specify-node-sizes"></a>Knooppuntgrootte opgeven
Zie [Virtuele machinegroottes](../../virtual-machines/linux/sizes.md)voor tekenreekswaarden die u moet opgeven voor de eigenschappen die in de vorige sectie zijn beschreven. De waarden moeten in overeenstemming zijn met de cmdlets en API's waarnaar in [virtuele machineformaten](../../virtual-machines/linux/sizes.md)wordt verwezen. De grootte van het grote (standaard) gegevensknooppunt heeft 7 GB geheugen. Dit is mogelijk niet voldoende voor uw scenario. 

Als u hoofdknooppunten en werkknooppunten van D4-formaat wilt maken, geeft u **Standard_D4** op als de waarde voor de eigenschappen **headNodeSize** en **dataNodeSize:** 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Als u een onjuiste waarde instelt voor deze eigenschappen, ziet u mogelijk het volgende bericht:

  Kan geen cluster maken. Uitzondering: Kan het cluster niet maken. Bewerking is mislukt met code 400. Cluster heeft status: 'Fout'. Bericht: 'Preclustercreationvalidationfailure'. 
  
Als u dit bericht ziet, moet u ervoor zorgen dat u de cmdlet- en API-namen uit de tabel gebruikt in [virtuele machineformaten.](../../virtual-machines/linux/sizes.md)  

> [!NOTE]
> Momenteel ondersteunt Data Factory geen HDInsight-clusters die Data Lake Store als primaire winkel gebruiken. Gebruik Azure Storage als de primaire winkel voor HDInsight-clusters. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Breng je eigen compute-omgeving
U een bestaande compute-omgeving registreren als gekoppelde service in Data Factory. U beheert de compute-omgeving. De service Data Factory gebruikt de compute-omgeving om activiteiten uit te voeren.

Dit type configuratie wordt ondersteund voor de volgende compute-omgevingen:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL Database, Azure SQL Data Warehouse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Gekoppelde Azure HDInsight-service
U een HDInsight-gekoppelde service maken om uw eigen HDInsight-cluster te registreren bij Data Factory.

### <a name="example"></a>Voorbeeld

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Eigenschappen
| Eigenschap          | Beschrijving                              | Vereist |
| ----------------- | ---------------------------------------- | -------- |
| type              | Stel de eigenschap type in op **HDInsight**. | Ja      |
| clusterUri        | De URI van het HDInsight-cluster.        | Ja      |
| gebruikersnaam          | De naam van het gebruikersaccount dat moet worden gebruikt om verbinding te maken met een bestaand HDInsight-cluster. | Ja      |
| wachtwoord          | Het wachtwoord voor het gebruikersaccount.   | Ja      |
| linkedServiceName | De naam van de gekoppelde opslagservice die verwijst naar de Blob-opslag die wordt gebruikt door het HDInsight-cluster. <p>Momenteel u geen gekoppelde datalakewinkelservice voor deze accommodatie opgeven. Als het HDInsight-cluster toegang heeft tot Data Lake Store, hebt u mogelijk toegang tot gegevens in Data Lake Store vanuit Hive- of Pig-scripts. </p> | Ja      |

## <a name="azure-batch-linked-service"></a>Gekoppelde Azure Batch-service
U een batchservice maken om een batchgroep virtuele machines (VM's) te registreren in een gegevensfabriek. U aangepaste Microsoft .NET-activiteiten uitvoeren met Batch of HDInsight.

Als u de batchservice opnieuw gebruikt:

* Meer informatie over [de basisprincipes van Azure Batch](../../batch/batch-technical-overview.md).
* Meer informatie over de cmdlet [Nieuw-AzureBatchAccount.](https://msdn.microsoft.com/library/mt125880.aspx) Gebruik deze cmdlet om een Batch-account aan te maken. U het Batch-account ook maken met behulp van de [Azure-portal.](../../batch/batch-account-create-portal.md) Zie [PowerShell gebruiken om een Batch-account te beheren voor](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)gedetailleerde informatie over het gebruik van de cmdlet.
* Meer informatie over de cmdlet [Nieuw-AzureBatchPool.](https://msdn.microsoft.com/library/mt125936.aspx) Gebruik deze cmdlet om een batchpool te maken.

### <a name="example"></a>Voorbeeld

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Voor de eigenschap **accountName** u toevoegen **.\< regionaam\> ** op de naam van uw batchaccount. Bijvoorbeeld:

```json
"accountName": "mybatchaccount.eastus"
```

Een andere optie is om het **batchUri-eindpunt** op te geven. Bijvoorbeeld:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Eigenschappen
| Eigenschap          | Beschrijving                              | Vereist |
| ----------------- | ---------------------------------------- | -------- |
| type              | Stel de eigenschap type in op **AzureBatch**. | Ja      |
| accountName       | De naam van de Batch-account.         | Ja      |
| Accesskey         | De toegangssleutel voor het Batch-account.  | Ja      |
| poolNaam          | De naam van de pool van VM's.    | Ja      |
| linkedServiceName | De naam van de gekoppelde opslagservice die is gekoppeld aan deze batchservice. Deze gekoppelde service wordt gebruikt voor het faseren van bestanden die nodig zijn om de activiteit uit te voeren en om activiteitenuitvoeringslogboeken op te slaan. | Ja      |

## <a name="azure-machine-learning-linked-service"></a>Gekoppelde Azure Machine Learning-service
U een machine learning-gekoppelde service maken om een eindpunt voor het scoren van een Machine Learning-batch te registreren in een gegevensfabriek.

### <a name="example"></a>Voorbeeld

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Eigenschappen
| Eigenschap   | Beschrijving                              | Vereist |
| ---------- | ---------------------------------------- | -------- |
| Type       | Stel de eigenschap type in op **AzureML**. | Ja      |
| mlEindpunt | De URL voor batchscores.                   | Ja      |
| apiKey     | De API van het gepubliceerde werkruimtemodel.     | Ja      |

## <a name="azure-data-lake-analytics-linked-service"></a>Gekoppelde Azure Data Lake Analytics-service
U een data lake analytics-gekoppelde service maken om een Data Lake Analytics-computeservice te koppelen aan een Azure-gegevensfabriek. De Data Lake Analytics U-SQL-activiteit in de pijplijn verwijst naar deze gekoppelde service. 

In de volgende tabel worden de algemene eigenschappen beschreven die worden gebruikt in de JSON-definitie:

| Eigenschap                 | Beschrijving                              | Vereist                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Stel de eigenschap type in op **AzureDataLakeAnalytics**. | Ja                                      |
| accountName          | De naam van het Data Lake Analytics-account.  | Ja                                      |
| dataLakeAnalyticsUri | De Data Lake Analytics URI.           | Nee                                       |
| subscriptionId       | De Azure-abonnements-ID.                    | Nee<br /><br />(Als dit niet is opgegeven, wordt het abonnement in de gegevensfabriek gebruikt.) |
| resourceGroupName    | De naam van de Azure-brongroep.                | Nee<br /><br /> (Als dit niet is opgegeven, wordt de brongroep gegevensfabriek gebruikt.) |

### <a name="authentication-options"></a>Verificatieopties
Voor uw data lake analytics-gekoppelde service u kiezen tussen verificatie met behulp van een serviceprincipal of een gebruikersreferentie.

#### <a name="service-principal-authentication-recommended"></a>Serviceprincipal-verificatie (aanbevolen)
Als u serviceprincipal-verificatie wilt gebruiken, registreert u een toepassingsentiteit in Azure Active Directory (Azure AD). Geef Azure AD vervolgens toegang tot Data Lake Store. Zie [Service-to-service-verificatie voor](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)gedetailleerde stappen . Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:
* Toepassings-id
* Toepassingssleutel 
* Tenant-id

Gebruik serviceprincipal-verificatie door de volgende eigenschappen op te geven:

| Eigenschap                | Beschrijving                              | Vereist |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | De client-id van de toepassing.     | Ja      |
| servicePrincipalKey | De sleutel van de toepassing.           | Ja      |
| tenant              | De tenantgegevens (domeinnaam of tenant-id) waar uw toepassing zich bevindt. Als u deze informatie wilt krijgen, houdt u de muis in de rechterbovenhoek van de Azure-portal. | Ja      |

**Voorbeeld: Serviceprincipal-verificatie**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Verificatie van gebruikersreferenties
Geef de volgende eigenschappen op voor verificatie van gebruikersreferenties voor Data Lake Analytics:

| Eigenschap          | Beschrijving                              | Vereist |
| :---------------- | :--------------------------------------- | :------- |
| autorisatie | Selecteer in de editor voor gegevensfabriek de knop **Autoriseren.** Voer de referentie in waarmee de automatisch gegenereerde autorisatie-URL aan deze eigenschap wordt toegedeeld. | Ja      |
| Sessionid     | De OAuth-sessie-ID van de OAuth-autorisatiesessie. Elke sessie-ID is uniek en kan slechts één keer worden gebruikt. Deze instelling wordt automatisch gegenereerd wanneer u Data Factory Editor gebruikt. | Ja      |

**Voorbeeld: Verificatie van gebruikersreferenties**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Token vervaldatum
De autorisatiecode die u hebt gegenereerd door de knop **Autoriseren te** selecteren, vervalt na een ingestelde interval. 

Mogelijk ziet u het volgende foutbericht wanneer het verificatietoken verloopt: 

  Fout in de referentiebewerking: invalid_grant - AADSTS70002: Fout bij het valideren van referenties. AADSTS70008: De verstrekte toegangssubsidie is verlopen of ingetrokken. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlatie-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z

In de volgende tabel worden verlopen per type gebruikersaccount weergegeven: 

| Gebruikerstype                                | Verloopt na                            |
| :--------------------------------------- | :--------------------------------------- |
| Gebruikersaccounts die *niet* worden beheerd door Azure AD (Hotmail, Live, enzovoort) | Twaalf uur.                                 |
| Gebruikersaccounts die *worden* beheerd door Azure AD | 14 dagen na de laatste slice run. <br /><br />90 dagen, als een segment dat is gebaseerd op een oauth-gebaseerde gekoppelde service wordt uitgevoerd ten minste eenmaal per 14 dagen. |

Als u deze fout wilt voorkomen of oplossen, moet u opnieuw autoriseren door de knop **Autoriseren** te selecteren wanneer het token verloopt. Implementeer vervolgens de gekoppelde service opnieuw. U ook programmatisch waarden genereren voor de **sessieId-** en **autorisatie-eigenschappen** met behulp van de volgende code:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Zie voor meer informatie over de klassen Gegevensfabriek die in dit codevoorbeeld worden gebruikt:
* [AzureDataLakeStoreLinkedService- klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService-klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [AuthorizationSessionGetResponse, klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Voeg een verwijzing toe naar Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll voor de klasse **WindowsFormsWebAuthenticationDialog.** 

## <a name="azure-sql-linked-service"></a>Een gekoppelde Azure SQL-service
U een SQL-gekoppelde service maken en deze gebruiken met de [activiteit Opgeslagen procedure](data-factory-stored-proc-activity.md) om een opgeslagen procedure van een Data Factory-pijplijn aan te roepen. Zie [Azure SQL-connector](data-factory-azure-sql-connector.md#linked-service-properties)voor meer informatie.

## <a name="azure-sql-data-warehouse-linked-service"></a>Gekoppelde Azure SQL Data Warehouse-service
U een SQL Data Warehouse-gekoppelde service maken en deze gebruiken met de [activiteit Opgeslagen procedure](data-factory-stored-proc-activity.md) om een opgeslagen procedure van een Data Factory-pijplijn aan te roepen. Zie [Azure SQL Data Warehouse-connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)voor meer informatie.

## <a name="sql-server-linked-service"></a>SQL Server-gekoppelde service
U een SQL Server-gekoppelde service maken en deze gebruiken met de [activiteit Opgeslagen procedure](data-factory-stored-proc-activity.md) om een opgeslagen procedure van een Data Factory-pijplijn aan te roepen. Zie [SQL Server-connector](data-factory-sqlserver-connector.md#linked-service-properties)voor meer informatie .

