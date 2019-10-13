---
title: Reken omgevingen die worden ondersteund door Azure Data Factory | Microsoft Docs
description: Meer informatie over reken omgevingen die u in Azure Data Factory-pijp lijnen (zoals Azure HDInsight) kunt gebruiken om gegevens te transformeren of te verwerken.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: ae3350b14ca1073a5fbb1a353b9301c57e7f1ea4
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298319"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Reken omgevingen die worden ondersteund door Azure Data Factory
In dit artikel worden verschillende reken omgevingen uitgelegd die u kunt gebruiken om gegevens te verwerken of te transformeren. Ook vindt u hier informatie over verschillende configuraties (op aanvraag versus uw eigen configuratie) die door Data Factory worden ondersteund bij het configureren van gekoppelde services die deze reken omgevingen koppelen aan een Azure data factory.

De volgende tabel bevat een lijst met reken omgevingen die worden ondersteund door Data Factory en de activiteiten die hierop kunnen worden uitgevoerd. 

| Compute-omgeving                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Hdinsight-cluster op aanvraag](#azure-hdinsight-on-demand-linked-service) of [uw eigen hdinsight-cluster](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [varken](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop-streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Aangepast](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Machine Learning-activiteiten: batchuitvoering en resources bijwerken](transform-data-using-machine-learning.md) |
| [Azure Machine Learning-service](#azure-machine-learning-service-linked-service) | [Pijp lijn Azure Machine Learning uitvoeren](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Opgeslagen procedure](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [jar](transform-data-databricks-jar.md), [python](transform-data-databricks-python.md) |

>  

## <a name="on-demand-hdinsight-compute-environment"></a>HDInsight-Compute-omgeving op aanvraag
In dit type configuratie wordt de computer omgeving volledig beheerd door de Azure Data Factory-service. Het wordt automatisch gemaakt door de Data Factory-service voordat een taak wordt verzonden om gegevens te verwerken en te worden verwijderd wanneer de taak is voltooid. U kunt een gekoppelde service maken voor de berekenings omgeving op aanvraag, deze configureren en gedetailleerde instellingen beheren voor taak uitvoering, Cluster beheer en acties voor Boots trap.

> [!NOTE]
> De configuratie op aanvraag wordt momenteel alleen ondersteund voor Azure HDInsight-clusters. Azure Databricks ook taken op aanvraag ondersteunt met behulp van taak clusters, Raadpleeg de [gekoppelde Azure Databricks-service](#azure-databricks-linked-service) voor meer informatie.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Gekoppelde Azure HDInsight-service op aanvraag
De Azure Data Factory-service kan automatisch een HDInsight-cluster op aanvraag maken voor het verwerken van gegevens. Het cluster wordt gemaakt in dezelfde regio als het opslag account (linkedServiceName-eigenschap in de JSON) die aan het cluster is gekoppeld. Het opslag account moet een standaard Azure-opslag account voor algemeen gebruik zijn. 

Houd rekening met de volgende **belang rijke** punten over gekoppelde HDInsight-service op aanvraag:

* Het HDInsight-cluster op aanvraag wordt gemaakt onder uw Azure-abonnement. U kunt het cluster in uw Azure Portal zien wanneer het cluster actief is. 
* De logboeken voor taken die worden uitgevoerd op een HDInsight-cluster op aanvraag worden gekopieerd naar het opslag account dat is gekoppeld aan het HDInsight-cluster. De clusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword die in de definitie van de gekoppelde service zijn gedefinieerd, worden gebruikt voor aanmelding bij het cluster voor uitgebreidere probleem oplossing tijdens de levens cyclus van het cluster. 
* Er worden alleen kosten in rekening gebracht voor het moment waarop het HDInsight-cluster taken uitvoert.
* U kunt een **script actie** gebruiken met de gekoppelde Azure HDInsight-service op aanvraag.  

> [!IMPORTANT]
> Het vergt meestal **20 minuten** of meer om een Azure HDInsight-cluster op aanvraag in te richten.

### <a name="example"></a>Voorbeeld
De volgende JSON definieert een gekoppelde HDInsight-service op aanvraag van Linux. De Data Factory-service maakt automatisch een HDInsight-cluster op **basis van Linux** om de vereiste activiteit te verwerken. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> Het HDInsight-cluster maakt een **standaardcontainer** in de blobopslag die u hebt opgegeven in de JSON (**linkedServiceName**). HDInsight verwijdert deze container niet wanneer het cluster wordt verwijderd. Dit gedrag is standaard. Met een gekoppelde on-demand HDInsight-service wordt er steeds een HDInsight-cluster gemaakt wanneer er een segment moet worden verwerkt, tenzij er een bestaand livecluster is (**timeToLive**). Het cluster wordt verwijderd wanneer het verwerken is voltooid. 
>
> Naarmate er meer activiteit wordt uitgevoerd, ziet u veel containers in uw Azure Blob-opslag. Als u deze niet nodig hebt voor het oplossen van problemen met taken, kunt u ze verwijderen om de opslagkosten te verlagen. De namen van deze containers volgen een patroon: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Gebruik hulpprogramma's zoals [Microsoft Opslagverkenner](https://storageexplorer.com/) om containers in uw Azure-blobopslag te verwijderen.
>
> 

### <a name="properties"></a>Eigenschappen
| Eigenschap                     | Beschrijving                              | Verplicht |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | De eigenschap type moet worden ingesteld op **HDInsightOnDemand**. | Ja      |
| clusterSize                  | Aantal werk-en gegevens knooppunten in het cluster. Het HDInsight-cluster wordt gemaakt met 2 hoofd knooppunten, samen met het aantal worker-knoop punten dat u voor deze eigenschap opgeeft. De knoop punten hebben een grootte van Standard_D3 met vier kernen, dus een cluster knooppunt van 4 heeft 24 kernen (4 @ no__t-04 = 16 kernen voor worker-knoop punten, plus 2 @ no__t-14 = 8 kernen voor hoofd knooppunten). Zie [clusters in HDInsight instellen met Hadoop, Spark, Kafka en meer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie. | Ja      |
| linkedServiceName            | Azure Storage gekoppelde service die moet worden gebruikt door het cluster op aanvraag om gegevens op te slaan en te verwerken. Het HDInsight-cluster wordt gemaakt in dezelfde regio als deze Azure Storage-account. Voor Azure HDInsight geldt een beperking voor het totale aantal kernen dat u kunt gebruiken in elke Azure-regio die wordt ondersteund. Zorg ervoor dat de Azure-regio voldoende kern quota heeft om aan de vereiste clusterSize te voldoen. Raadpleeg [clusters in HDInsight instellen met Hadoop, Spark, Kafka en meer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie<p>Op dit moment kunt u geen HDInsight-cluster op aanvraag maken dat gebruikmaakt van een Azure Data Lake Store als opslag. Als u de resultaat gegevens van HDInsight-verwerking in een Azure Data Lake Store wilt opslaan, gebruikt u een Kopieer activiteit om de gegevens van de Azure-Blob Storage naar de Azure Data Lake Store te kopiëren. </p> | Ja      |
| clusterResourceGroup         | Het HDInsight-cluster wordt gemaakt in deze resource groep. | Ja      |
| timetolive                   | De toegestane tijd niet-actief voor het HDInsight-cluster op aanvraag. Hiermee geeft u op hoe lang het HDInsight-cluster op aanvraag actief blijft na voltooiing van een uitvoering van een activiteit als er geen andere actieve taken in het cluster zijn. De mini maal toegestane waarde is 5 minuten (00:05:00).<br/><br/>Als bijvoorbeeld het uitvoeren van een activiteit zes minuten duurt en timetolive is ingesteld op 5 minuten, blijft het cluster 5 minuten na de 6 minuten van verwerking van de uitvoering van de activiteit actief. Als er een andere uitvoering van de activiteit wordt uitgevoerd met het 6-minuten venster, wordt deze verwerkt door hetzelfde cluster.<br/><br/>Het maken van een HDInsight-cluster op aanvraag is een dure bewerking (kan enige tijd duren). Gebruik deze instelling daarom zo nodig om de prestaties van een data factory te verbeteren door opnieuw gebruik te maken van een on-demand HDInsight-cluster.<br/><br/>Als u timetolive waarde instelt op 0, wordt het cluster verwijderd zodra de uitvoering van de activiteit is voltooid. Als u een hoge waarde instelt, kan het cluster niet actief blijven om u aan te melden voor het oplossen van problemen, maar dit kan leiden tot hoge kosten. Daarom is het belang rijk dat u de juiste waarde instelt op basis van uw behoeften.<br/><br/>Als de waarde van de eigenschap timetolive is ingesteld op de juiste wijze, kunnen meerdere pijp lijnen het exemplaar van het HDInsight-cluster op aanvraag delen. | Ja      |
| clusterType                  | Het type HDInsight-cluster dat moet worden gemaakt. Toegestane waarden zijn ' Hadoop ' en ' Spark '. Als niet wordt opgegeven, is de standaard waarde Hadoop. Enterprise Security Package ingeschakeld cluster kan niet op aanvraag worden gemaakt. gebruik in plaats daarvan een [bestaand cluster/zet uw eigen reken kracht](#azure-hdinsight-linked-service). | Nee       |
| versie                      | De versie van het HDInsight-cluster. Als u niets opgeeft, wordt de huidige standaard versie van HDInsight gebruikt. | Nee       |
| hostSubscriptionId           | De ID van het Azure-abonnement dat wordt gebruikt om een HDInsight-cluster te maken. Als u niets opgeeft, wordt de abonnements-ID van uw Azure-aanmeldings context gebruikt. | Nee       |
| clusterNamePrefix           | Het voor voegsel van de naam van het HDI-cluster wordt automatisch een tijds tempel toegevoegd aan het einde van de cluster naam| Nee       |
| sparkVersion                 | De versie van Spark als het cluster type Spark is | Nee       |
| additionalLinkedServiceNames | Hiermee worden extra opslag accounts voor de gekoppelde HDInsight-service opgegeven, zodat de Data Factory-service namens u kan worden geregistreerd. Deze opslag accounts moeten zich in dezelfde regio bevinden als het HDInsight-cluster, dat wordt gemaakt in dezelfde regio als het opslag account dat is opgegeven door linkedServiceName. | Nee       |
| osType                       | Type besturings systeem. Toegestane waarden zijn: Linux en Windows (alleen voor HDInsight 3,3). De standaard waarde is Linux. | Nee       |
| hcatalogLinkedServiceName    | De naam van de gekoppelde Azure SQL-service die verwijst naar de HCatalog-data base. Het HDInsight-cluster op aanvraag wordt gemaakt met behulp van de Azure-SQL database als de meta Store. | Nee       |
| connectVia                   | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde HDInsight-service. Voor de gekoppelde on-demand HDInsight-service ondersteunt deze alleen Azure Integration Runtime. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee       |
| clusterUserName                   | De gebruikers naam voor toegang tot het cluster. | Nee       |
| clusterPassword                   | Het wacht woord in het type beveiligde teken reeks voor toegang tot het cluster. | Nee       |
| clusterSshUserName         | De gebruikers naam voor SSH maakt extern verbinding met het knoop punt van het cluster (voor Linux). | Nee       |
| clusterSshPassword         | Het wacht woord in het type beveiligde teken reeks naar SSH extern verbinding maken met het knoop punt van het cluster (voor Linux). | Nee       |
| scriptActions | Geef het script voor [aanpassingen van HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) op tijdens het maken van een cluster op aanvraag. <br />Het hulp programma voor het ontwerpen van gebruikers interfaces van Azure Data Factory biedt momenteel ondersteuning voor het opgeven van slechts 1 script actie, maar u kunt deze beperking door lopen in de JSON (meerdere script acties in de JSON opgeven). | Nee |


> [!IMPORTANT]
> HDInsight ondersteunt meerdere Hadoop-cluster versies die kunnen worden geïmplementeerd. Met elke versie keuze maakt u een specifieke versie van de HDP-distributie (Hortonworks data platform) en een set onderdelen die zijn opgenomen in die distributie. De lijst met ondersteunde HDInsight-versies blijft bijgewerkt met de nieuwste Hadoop ecosysteem-onderdelen en-oplossingen. Raadpleeg altijd de nieuwste informatie over de [ondersteunde hdinsight-versie en het type besturings systeem](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) om ervoor te zorgen dat u een ondersteunde versie van hdinsight gebruikt. 
>
> [!IMPORTANT]
> Op dit moment biedt HDInsight gekoppelde services geen ondersteuning voor HBase, interactieve Query's (Hive LLAP), storm. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON-voor beeld

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Verificatie van service-principal

Voor de gekoppelde on-demand HDInsight-service is een Service-Principal-verificatie vereist voor het maken van HDInsight-clusters in uw naam. Als u Service-Principal-verificatie wilt gebruiken, registreert u een toepassings entiteit in Azure Active Directory (Azure AD) en verleent u deze de rol **Inzender** van het abonnement of de resource groep waarin het HDInsight-cluster wordt gemaakt. Zie [Portal gebruiken om een Azure Active Directory toepassing en Service-Principal te maken die toegang hebben tot resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)voor gedetailleerde stappen. Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

- Toepassings-id
- Toepassings sleutel 
- Tenant-id

Gebruik Service-Principal-verificatie door de volgende eigenschappen op te geven:

| Eigenschap                | Beschrijving                              | Verplicht |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Geef de client-ID van de toepassing op.     | Ja      |
| **servicePrincipalKey** | Geef de sleutel van de toepassing op.           | Ja      |
| **tenant**              | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja      |

### <a name="advanced-properties"></a>Geavanceerde eigenschappen

U kunt ook de volgende eigenschappen opgeven voor de gedetailleerde configuratie van het HDInsight-cluster op aanvraag.

| Eigenschap               | Beschrijving                              | Verplicht |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Hiermee geeft u de para meters voor de kern configuratie op (zoals in bestand core-site. XML) voor het HDInsight-cluster dat moet worden gemaakt. | Nee       |
| hBaseConfiguration     | Hiermee geeft u de HBase-configuratie parameters (hbase-site. XML) voor het HDInsight-cluster. | Nee       |
| hdfsConfiguration      | Hiermee geeft u de HDFS-configuratie parameters (hdfs-site. XML) voor het HDInsight-cluster. | Nee       |
| hiveConfiguration      | Hiermee geeft u de Hive-configuratie parameters (Hive-site. XML) op voor het HDInsight-cluster. | Nee       |
| mapReduceConfiguration | Hiermee geeft u de MapReduce-configuratie parameters (mapred-site. XML) voor het HDInsight-cluster. | Nee       |
| oozieConfiguration     | Hiermee geeft u de Oozie-configuratie parameters (oozie-site. XML) voor het HDInsight-cluster. | Nee       |
| stormConfiguration     | Hiermee geeft u de Storm-configuratie parameters (Storm-site. XML) voor het HDInsight-cluster. | Nee       |
| yarnConfiguration      | Hiermee geeft u de garen configuratie parameters (Yarn-site. XML) voor het HDInsight-cluster. | Nee       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Voor beeld: HDInsight-cluster configuratie op aanvraag met geavanceerde eigenschappen

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
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
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>Knooppunt grootten
U kunt de grootte van de hoofd-, gegevens-en Zookeeper-knoop punten opgeven met behulp van de volgende eigenschappen: 

| Eigenschap          | Beschrijving                              | Verplicht |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Hiermee wordt de grootte van het hoofd knooppunt opgegeven. De standaard waarde is: Standard_D3. Zie de sectie **knooppunt grootten opgeven** voor meer informatie. | Nee       |
| dataNodeSize      | Hiermee wordt de grootte van het gegevens knooppunt opgegeven. De standaard waarde is: Standard_D3. | Nee       |
| zookeeperNodeSize | Hiermee geeft u de grootte van het Zoo keeper-knoop punt. De standaard waarde is: Standard_D3. | Nee       |

#### <a name="specifying-node-sizes"></a>Grootte van knoop punten opgeven
Bekijk de [grootten van virtual machines](../virtual-machines/linux/sizes.md) artikel voor teken reeks waarden die u moet opgeven voor de eigenschappen die worden vermeld in de vorige sectie. De waarden moeten voldoen aan de **cmdlets & api's** waarnaar in het artikel wordt verwezen. Zoals u in het artikel kunt zien, heeft het gegevens knooppunt van groot (standaard) een capaciteit van 7 GB. Dit is mogelijk niet voldoende voor uw scenario. 

Als u de grootte van het hoofd knooppunt en werk knooppunten van D4 wilt maken, geeft u **Standard_D4** op als de waarde voor de eigenschappen HeadNodeSize en dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Als u een verkeerde waarde voor deze eigenschappen opgeeft, wordt mogelijk de volgende fout weer gegeven **:** kan het cluster niet maken. Uitzondering: Kan het cluster niet maken. Bewerking is mislukt met code 400. Cluster heeft status: 'Fout'. Bericht: ' PreClusterCreationValidationFailure '. Wanneer u dit fout bericht ontvangt, moet u ervoor zorgen dat u de **CMDLET-&-api's** naam uit de tabel gebruikt in de [grootte van virtual machines](../virtual-machines/linux/sizes.md) artikel.        

## <a name="bring-your-own-compute-environment"></a>Uw eigen reken omgeving meenemen
In dit type configuratie kunnen gebruikers een reeds bestaande computer omgeving registreren als een gekoppelde service in Data Factory. De computer omgeving wordt beheerd door de gebruiker en de Data Factory-service gebruikt deze om de activiteiten uit te voeren.

Dit type configuratie wordt ondersteund voor de volgende reken omgevingen:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Gekoppelde Azure HDInsight-service
U kunt een gekoppelde Azure HDInsight-service maken om uw eigen HDInsight-cluster te registreren bij Data Factory.

### <a name="example"></a>Voorbeeld

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Eigenschappen
| Eigenschap          | Beschrijving                                                  | Verplicht |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | De eigenschap type moet worden ingesteld op **HDInsight**.            | Ja      |
| clusterUri        | De URI van het HDInsight-cluster.                            | Ja      |
| gebruikersnaam          | Geef de naam op van de gebruiker die moet worden gebruikt om verbinding te maken met een bestaand HDInsight-cluster. | Ja      |
| wachtwoord          | Geef het wacht woord voor het gebruikers account op.                       | Ja      |
| linkedServiceName | De naam van de gekoppelde Azure Storage-service die verwijst naar de Azure Blob-opslag die wordt gebruikt door het HDInsight-cluster. <p>Op dit moment kunt u geen Azure Data Lake Store gekoppelde service opgeven voor deze eigenschap. Als het HDInsight-cluster toegang heeft tot de Data Lake Store, kunt u toegang krijgen tot gegevens in de Azure Data Lake Store van Hive-en Pig-scripts. </p> | Ja      |
| isEspEnabled      | Geef*waar*op als het HDInsight-cluster [Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture) ingeschakeld. De standaard waarde is*False*. | Nee       |
| connectVia        | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde service. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. <br />Voor Enterprise Security Package (ESP) ingeschakeld HDInsight-cluster gebruikt u een zelf-hostende Integration runtime die een regel van het gezichts punt naar het cluster heeft of moet worden geïmplementeerd in dezelfde Virtual Network als het ESP HDInsight-cluster. | Nee       |

> [!IMPORTANT]
> HDInsight ondersteunt meerdere Hadoop-cluster versies die kunnen worden geïmplementeerd. Met elke versie keuze maakt u een specifieke versie van de HDP-distributie (Hortonworks data platform) en een set onderdelen die zijn opgenomen in die distributie. De lijst met ondersteunde HDInsight-versies blijft bijgewerkt met de nieuwste Hadoop ecosysteem-onderdelen en-oplossingen. Raadpleeg altijd de nieuwste informatie over de [ondersteunde hdinsight-versie en het type besturings systeem](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) om ervoor te zorgen dat u een ondersteunde versie van hdinsight gebruikt. 
>
> [!IMPORTANT]
> Op dit moment biedt HDInsight gekoppelde services geen ondersteuning voor HBase, interactieve Query's (Hive LLAP), storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Azure Batch gekoppelde service

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt een Azure Batch gekoppelde service maken om een batch-pool van virtuele machines (Vm's) te registreren bij een data factory. U kunt aangepaste activiteit uitvoeren met behulp van Azure Batch.

Zie de volgende onderwerpen als u geen ervaring hebt met Azure Batch-service:

* [Azure batch basis principes](../batch/batch-technical-overview.md) voor een overzicht van de Azure batch-service.
* De cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) om een Azure batch-account (of) te maken [Azure Portal](../batch/batch-account-create-portal.md) het Azure batch-account te maken met behulp van Azure Portal. Zie het onderwerp [Azure batch account beheren met Power shell](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) voor meer informatie over het gebruik van de cmdlet.
* De cmdlet [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) om een Azure batch groep te maken.

### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Eigenschappen
| Eigenschap          | Beschrijving                              | Verplicht |
| ----------------- | ---------------------------------------- | -------- |
| type              | De eigenschap type moet worden ingesteld op **AzureBatch**. | Ja      |
| accountName       | De naam van het Azure Batch-account.         | Ja      |
| accessKey         | De toegangs sleutel voor het Azure Batch-account.  | Ja      |
| batchUri          | URL naar uw Azure Batch-account, in de indeling https://*batchaccountname. Region*. batch.Azure.com. | Ja      |
| poolName          | De naam van de pool van virtuele machines.    | Ja      |
| linkedServiceName | De naam van de Azure Storage gekoppelde service die is gekoppeld aan deze Azure Batch gekoppelde service. Deze gekoppelde service wordt gebruikt voor tijdelijke bestanden die vereist zijn om de activiteit uit te voeren. | Ja      |
| connectVia        | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde service. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee       |

## <a name="azure-machine-learning-studio-linked-service"></a>Azure Machine Learning Studio gekoppelde service
U maakt een Azure Machine Learning Studio gekoppelde service om een Machine Learning batch Score-eind punt te registreren bij een data factory.

### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Eigenschappen
| Eigenschap               | Beschrijving                              | Verplicht                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                   | De eigenschap type moet worden ingesteld op: **AzureML**. | Ja                                      |
| mlEndpoint             | De batch Score-URL.                   | Ja                                      |
| apiKey                 | De API van het gepubliceerde werkruimte model.     | Ja                                      |
| updateResourceEndpoint | De bron-URL van de update voor een Azure Machine Learning-webservice-eind punt dat wordt gebruikt om de voorspellende webservice bij te werken met het getrainde model bestand | Nee                                       |
| servicePrincipalId     | Geef de client-ID van de toepassing op.     | Vereist als updateResourceEndpoint is opgegeven |
| servicePrincipalKey    | Geef de sleutel van de toepassing op.           | Vereist als updateResourceEndpoint is opgegeven |
| bouw                 | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Vereist als updateResourceEndpoint is opgegeven |
| connectVia             | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde service. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee                                       |

## <a name="azure-machine-learning-service-linked-service"></a>Gekoppelde service van Azure Machine Learning-service
U maakt een gekoppelde service van de Azure Machine Learning-service om een Azure Machine Learning service-werk ruimte te verbinden met een data factory.

> [!NOTE]
> Momenteel wordt alleen Service-Principal-verificatie ondersteund voor de gekoppelde service van de Azure Machine Learning-service.

### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Eigenschappen
| Eigenschap               | Beschrijving                              | Verplicht                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                   | De eigenschap type moet worden ingesteld op: **AzureMLService**. | Ja                                      |
| subscriptionId         | Azure-abonnements-ID              | Ja                                      |
| resourceGroupName      | name | Ja                                      |
| mlWorkspaceName        | Naam van Azure Machine Learning service werkruimte | Ja  |
| servicePrincipalId     | Geef de client-ID van de toepassing op.     | Nee |
| servicePrincipalKey    | Geef de sleutel van de toepassing op.           | Nee |
| bouw                 | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Vereist als updateResourceEndpoint is opgegeven | Nee |
| connectVia             | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde service. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee |    

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics gekoppelde service
U maakt een **Azure data Lake Analytics** gekoppelde service om een Azure data Lake Analytics compute-service aan een Azure Data Factory te koppelen. De Data Lake Analytics U-SQL-activiteit in de pijp lijn verwijst naar deze gekoppelde service. 

### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription ID of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Eigenschappen

| Eigenschap             | Beschrijving                              | Verplicht                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | De eigenschap type moet worden ingesteld op: **AzureDataLakeAnalytics**. | Ja                                      |
| accountName          | Azure Data Lake Analytics account naam.  | Ja                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics-URI.           | Nee                                       |
| subscriptionId       | Azure-abonnements-ID                    | Nee                                       |
| resourceGroupName    | Naam van Azure-resourcegroep                | Nee                                       |
| servicePrincipalId   | Geef de client-ID van de toepassing op.     | Ja                                      |
| servicePrincipalKey  | Geef de sleutel van de toepassing op.           | Ja                                      |
| bouw               | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja                                      |
| connectVia           | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde service. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks gekoppelde service
U kunt **Azure Databricks gekoppelde service** maken om de Databricks-werk ruimte te registreren die u gaat gebruiken om de Databricks-workloads (notebook, jar, python) uit te voeren. 
> [!IMPORTANT]
> Databricks gekoppelde services ondersteunen [instanties groepen](https://aka.ms/instance-pools). 

### <a name="example---using-new-job-cluster-in-databricks"></a>Voor beeld: een nieuw taak cluster gebruiken in Databricks

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Voor beeld-een bestaand interactief cluster gebruiken in Databricks

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Eigenschappen

| Eigenschap             | Beschrijving                              | Verplicht                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| name                 | De naam van de gekoppelde service               | Ja   |
| type                 | De eigenschap type moet worden ingesteld op: **AzureDatabricks**. | Ja                                      |
| Domeinen               | Geef de Azure-regio op op basis van de regio van de Databricks-werk ruimte. Voorbeeld: https://eastus.azuredatabricks.net | Ja                                 |
| accessToken          | Er is een toegangs token vereist om Data Factory te verifiëren bij Azure Databricks. Het toegangs token moet worden gegenereerd op basis van de databricks-werk ruimte. Meer gedetailleerde stappen om het toegangs token te vinden, vindt u [hier](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Ja                                       |
| existingClusterId    | De cluster-ID van een bestaand cluster voor het uitvoeren van alle taken. Dit moet een al gemaakt interactief cluster zijn. Mogelijk moet u het cluster hand matig opnieuw opstarten als het niet meer reageert. Databricks suggereert het uitvoeren van taken op nieuwe clusters voor een grotere betrouw baarheid. U vindt de cluster-ID van een interactief cluster op Databricks werkruimte-> clusters-> interactieve cluster naam-> Configuratie->-Tags. [Meer Details](https://docs.databricks.com/user-guide/clusters/tags.html) | Nee 
| instancePoolId    | ID van de exemplaar groep van een bestaande groep in de databricks-werk ruimte.  | Nee  |
| newClusterVersion    | De Spark-versie van het cluster. Er wordt een taak cluster gemaakt in databricks. | Nee  |
| newClusterNumOfWorker| Het aantal worker-knoop punten dat dit cluster moet hebben. Een cluster heeft één Spark-stuur programma en num_workers-uitvoerders voor een totaal van num_workers + 1 Spark-knoop punten. Een teken reeks met een Int32-notatie, zoals ' 1 ' betekent dat numOfWorker 1 is of ' 1:10 ' betekent dat de schaal automatisch wordt aangepast van 1 tot en met 10 als Max.  | Nee                |
| newClusterNodeType   | Dit veld codeert, via één waarde, de bronnen die beschikbaar zijn voor elk van de Spark-knoop punten in dit cluster. De Spark-knoop punten kunnen bijvoorbeeld worden ingericht en geoptimaliseerd voor geheugen of computerintensieve werk belastingen dit veld is vereist voor het nieuwe cluster                | Nee               |
| newClusterSparkConf  | een set optionele, door de gebruiker opgegeven Spark-configuratie sleutel-waardeparen. Gebruikers kunnen ook een reeks extra JVM-opties door geven aan het stuur programma en de uitvoerers via Spark. driver. extraJavaOptions en Spark. Execute. extraJavaOptions respectievelijk. | Nee  |
| newClusterInitScripts| een set optionele, door de gebruiker gedefinieerde initialisatie scripts voor het nieuwe cluster. Het DBFS-pad naar de init-scripts opgeven. | Nee  |


## <a name="azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service
U maakt een gekoppelde Azure SQL-service en gebruikt deze met de [opgeslagen procedure activiteit](transform-data-using-stored-procedure.md) om een opgeslagen procedure vanuit een Data Factory pijp lijn aan te roepen. Zie het artikel over [Azure SQL connector](connector-azure-sql-database.md#linked-service-properties) voor meer informatie over deze gekoppelde service.

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse gekoppelde service
U maakt een Azure SQL Data Warehouse gekoppelde service en gebruikt deze met de [opgeslagen procedure activiteit](transform-data-using-stored-procedure.md) om een opgeslagen procedure vanuit een Data Factory pijp lijn aan te roepen. Zie [Azure SQL Data Warehouse connector](connector-azure-sql-data-warehouse.md#linked-service-properties) -artikel voor meer informatie over deze gekoppelde service.

## <a name="sql-server-linked-service"></a>SQL Server gekoppelde service
U maakt een SQL Server gekoppelde service en gebruikt deze met de [opgeslagen procedure activiteit](transform-data-using-stored-procedure.md) om een opgeslagen procedure vanuit een Data Factory pijp lijn aan te roepen. Zie [SQL Server connector](connector-sql-server.md#linked-service-properties) -artikel voor meer informatie over deze gekoppelde service.

## <a name="next-steps"></a>Volgende stappen
Zie [gegevens transformeren](transform-data.md)voor een lijst van de transformatie activiteiten die door Azure Data Factory worden ondersteund.
