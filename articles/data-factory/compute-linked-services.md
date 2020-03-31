---
title: Compute-omgevingen ondersteund door Azure Data Factory
description: Meer informatie over rekenomgevingen die u gebruiken in Azure Data Factory-pijplijnen (zoals Azure HDInsight) om gegevens te transformeren of te verwerken.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2019
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 4545a75cc2082c21dcb87986eba819ebe39adf7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246342"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Compute-omgevingen ondersteund door Azure Data Factory
In dit artikel worden verschillende compute-omgevingen uitgelegd die u gebruiken om gegevens te verwerken of te transformeren. Het biedt ook details over verschillende configuraties (on-demand vs. breng uw eigen) ondersteund door Data Factory bij het configureren van gekoppelde services die deze compute-omgevingen koppelen aan een Azure-gegevensfabriek.

In de volgende tabel vindt u een lijst met rekenomgevingen die worden ondersteund door Data Factory en de activiteiten die erop kunnen worden uitgevoerd. 

| Compute-omgeving                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [On-demand HDInsight-cluster](#azure-hdinsight-on-demand-linked-service) of [uw eigen HDInsight-cluster](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Aangepast](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Machine Learning-activiteiten: batchuitvoering en resources bijwerken](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning-uitvoerpijplijn](transform-data-machine-learning-service.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning-uitvoerpijplijn](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), SQL [Server](#sql-server-linked-service) | [Opgeslagen procedure](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Azure Functions](#azure-function-linked-service)         | [Azure-functieactiviteit](control-flow-azure-function-activity.md)
>  

## <a name="on-demand-hdinsight-compute-environment"></a>On-demand HDInsight compute environment
In dit type configuratie wordt de computeromgeving volledig beheerd door de Azure Data Factory-service. Het wordt automatisch gemaakt door de Service Gegevensfabriek voordat een taak wordt ingediend om gegevens te verwerken en wordt verwijderd wanneer de taak is voltooid. U een gekoppelde service maken voor de on-demand compute-omgeving, deze configureren en gedetailleerde instellingen beheren voor taakuitvoering, clusterbeheer en bootstrapping-acties.

> [!NOTE]
> De on-demand configuratie wordt momenteel alleen ondersteund voor Azure HDInsight-clusters. Azure Databricks ondersteunt ook on-demand taken met behulp van taakclusters, verwijzen naar [Azure databricks linked service](#azure-databricks-linked-service) voor meer details.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Een gekoppelde Azure HDInsight-service op aanvraag
De Azure Data Factory-service kan automatisch een on-demand HDInsight-cluster maken om gegevens te verwerken. Het cluster wordt gemaakt in dezelfde regio als het opslagaccount (eigenschap linkedServiceName in het JSON) dat aan het cluster is gekoppeld. Het opslagaccount moet een standaard Azure-opslagaccount voor algemene doeleinden zijn. 

Let op de volgende **belangrijke** punten over on-demand HDInsight gekoppelde service:

* Het on-demand HDInsight-cluster wordt gemaakt onder uw Azure-abonnement. U het cluster in uw Azure-portal zien wanneer het cluster operationeel is. 
* De logboeken voor taken die worden uitgevoerd op een on-demand HDInsight-cluster, worden gekopieerd naar het opslagaccount dat is gekoppeld aan het HDInsight-cluster. Het clusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword gedefinieerd in uw gekoppelde servicedefinitie worden gebruikt om in te loggen op het cluster voor diepgaande probleemoplossing tijdens de levenscyclus van het cluster. 
* Er worden alleen kosten in rekening gebracht voor de tijd dat het HDInsight-cluster actief is.
* U een **Scriptactie** gebruiken met de azure HDInsight on-demand gekoppelde service.  

> [!IMPORTANT]
> Het duurt meestal **20 minuten** of langer om een Azure HDInsight-cluster op aanvraag in te richten.

### <a name="example"></a>Voorbeeld
De volgende JSON definieert een Linux-gebaseerde on-demand HDInsight linked service. De Data Factory-service maakt automatisch een **HDInsight-cluster op Basis van Linux** om de vereiste activiteit te verwerken. 

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
> Naarmate er meer activiteit wordt uitgevoerd, ziet u veel containers in uw Azure blob-opslag. Als u deze niet nodig hebt voor het oplossen van problemen met taken, kunt u ze verwijderen om de opslagkosten te verlagen. De namen van deze containers volgen een patroon: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Gebruik hulpprogramma's zoals [Microsoft Opslagverkenner](https://storageexplorer.com/) om containers in uw Azure-blobopslag te verwijderen.
>
> 

### <a name="properties"></a>Eigenschappen
| Eigenschap                     | Beschrijving                              | Vereist |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | De eigenschap type moet worden ingesteld op **HDInsightOnDemand**. | Ja      |
| clusterSize                  | Aantal werknemers/gegevensknooppunten in het cluster. Het HDInsight-cluster wordt gemaakt met 2 hoofdknooppunten samen met het aantal werknemersknooppunten dat u voor deze eigenschap opgeeft. De knooppunten zijn van grootte Standard_D3 die 4 cores heeft, dus een\*cluster met 4 werknemersknooppunten neemt\*24 cores (4 4 = 16 cores voor werknemersknooppunten, plus 2 4 = 8 cores voor hoofdknooppunten). Zie [Clusters instellen in HDInsight met Hadoop, Spark, Kafka en meer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie. | Ja      |
| linkedServiceName            | Azure Storage-gekoppelde service die wordt gebruikt door het on-demand cluster voor het opslaan en verwerken van gegevens. Het HDInsight-cluster wordt gemaakt in dezelfde regio als dit Azure Storage-account. Voor Azure HDInsight geldt een beperking voor het totale aantal kernen dat u kunt gebruiken in elke Azure-regio die wordt ondersteund. Zorg ervoor dat u voldoende kernquota in dat Azure-gebied hebt om aan de vereiste clusterGrootte te voldoen. Zie clusters [instellen in HDInsight met Hadoop, Spark, Kafka en meer voor meer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) informatie<p>Momenteel u geen on-demand HDInsight-cluster maken dat een Azure Data Lake Store als opslag gebruikt. Als u de resultaatgegevens van HDInsight-verwerking wilt opslaan in een Azure Data Lake Store, gebruikt u een kopieeractiviteit om de gegevens uit de Azure Blob Storage naar de Azure Data Lake Store te kopiëren. </p> | Ja      |
| clusterResourceGroep         | Het cluster HDInsight wordt gemaakt in deze brongroep. | Ja      |
| timetolive timetolive                   | De toegestane idle tijd voor het on-demand HDInsight-cluster. Hiermee geeft u op hoe lang het on-demand HDInsight-cluster in leven blijft na voltooiing van een activiteitsrun als er geen andere actieve taken in het cluster zijn. De minimale toegestane waarde is 5 minuten (00:05:00).<br/><br/>Als een activiteitsrun bijvoorbeeld 6 minuten duurt en timetolive is ingesteld op 5 minuten, blijft het cluster 5 minuten in leven na de 6 minuten van het verwerken van de activiteit. Als een andere activiteitsrun wordt uitgevoerd met het venster van 6 minuten, wordt deze verwerkt door hetzelfde cluster.<br/><br/>Het maken van een on-demand HDInsight-cluster is een dure bewerking (kan even duren), dus gebruik deze instelling als dat nodig is om de prestaties van een gegevensfabriek te verbeteren door een on-demand HDInsight-cluster opnieuw te gebruiken.<br/><br/>Als u de timetolive-waarde instelt op 0, wordt het cluster verwijderd zodra de activiteit is voltooid. Terwijl, als u een hoge waarde instelt, het cluster kan blijven inactief voor u om in te loggen voor een aantal problemen met het oplossen van problemen, maar het kan leiden tot hoge kosten. Daarom is het belangrijk dat u de juiste waarde instelt op basis van uw behoeften.<br/><br/>Als de waarde van de eigenschap timetolive op de juiste manier is ingesteld, kunnen meerdere pijplijnen de instantie van het on-demand HDInsight-cluster delen. | Ja      |
| clusterType                  | Het type hdinsight-cluster dat moet worden gemaakt. Toegestane waarden zijn "hadoop" en "spark". Als dit niet is opgegeven, is de standaardwaarde hadoop. Enterprise Security Package enabled cluster kan niet on-demand worden gemaakt, in plaats daarvan gebruik maken van een [bestaand cluster / breng uw eigen compute](#azure-hdinsight-linked-service). | Nee       |
| versie                      | Versie van het HDInsight-cluster. Als dit niet is opgegeven, wordt de huidige hdInsight-standaardversie gebruikt. | Nee       |
| hostSubscriptionId           | De Azure-abonnements-ID die wordt gebruikt om het HDInsight-cluster te maken. Als dit niet is opgegeven, wordt de abonnements-id van uw Azure-inlogcontext gebruikt. | Nee       |
| clusterNaamVoorvoegsel           | Het voorvoegsel van de HDI-clusternaam, een tijdstempel wordt automatisch toegevoegd aan het einde van de clusternaam| Nee       |
| sparkVersion                 | De versie van spark als het clustertype 'Spark' is | Nee       |
| extraLinkedServiceNames | Hiermee geeft u extra opslagaccounts op voor de hdinsight-gekoppelde service, zodat de Data Factory-service deze namens u kan registreren. Deze opslagaccounts moeten zich in dezelfde regio bevinden als het HDInsight-cluster, dat is gemaakt in dezelfde regio als het opslagaccount dat is opgegeven door linkedServiceName. | Nee       |
| osType                       | Type besturingssysteem. Toegestane waarden zijn: Linux en Windows (alleen voor HDInsight 3.3). Standaard is Linux. | Nee       |
| hcatalogLinkedServiceName    | De naam van azure SQL-gekoppelde service die naar de HCatalog-database wijst. Het on-demand HDInsight-cluster wordt gemaakt met behulp van de Azure SQL-database als metastore. | Nee       |
| connectVia                   | De Integratieruntime die moet worden gebruikt om de activiteiten naar deze HDInsight-gekoppelde service te verzenden. Voor on-demand HDInsight-gekoppelde service ondersteunt deze alleen Azure Integration Runtime. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee       |
| clusterGebruikersnaam                   | De gebruikersnaam om toegang te krijgen tot het cluster. | Nee       |
| clusterPassword                   | Het wachtwoord in het type beveiligde tekenreeks om toegang te krijgen tot het cluster. | Nee       |
| clusterSshUserName         | De gebruikersnaam van SSH maakt op afstand verbinding met het knooppunt van het cluster (voor Linux). | Nee       |
| clusterSshPassword         | Het wachtwoord in het type beveiligde tekenreeks naar SSH verbindt op afstand het knooppunt van het cluster (voor Linux). | Nee       |
| scriptActies | Geef script op voor [HDInsight-clusteraanpassingen](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) tijdens het maken van on-demandcluster. <br />Momenteel ondersteunt het ontwerpprogramma voor de gebruikersinterface van Azure Data Factory het opgeven van slechts 1 scriptactie, maar u deze beperking doorlopen in de JSON (meerdere scriptacties opgeven in het JSON). | Nee |


> [!IMPORTANT]
> HDInsight ondersteunt meerdere Hadoop-clusterversies die kunnen worden geïmplementeerd. Elke versiekeuze maakt een specifieke versie van de HDP-distributie (Hortonworks Data Platform) en een set componenten die zich in die distributie bevinden. De lijst met ondersteunde HDInsight-versies wordt steeds bijgewerkt om de nieuwste Hadoop-ecosysteemcomponenten en -oplossingen te bieden. Zorg ervoor dat u altijd verwijst naar de nieuwste informatie van [de ondersteunde HDInsight-versie en het OS-type](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) om ervoor te zorgen dat u de ondersteunde versie van HDInsight gebruikt. 
>
> [!IMPORTANT]
> Momenteel ondersteunen HDInsight linked services geen HBase, Interactive Query (Hive LLAP), Storm. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON voorbeeld

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Verificatie van service-principal

De on-demand HDInsight gekoppelde service vereist een serviceprincipal-verificatie om HDInsight-clusters namens u te maken. Als u serviceprincipal-verificatie wilt gebruiken, registreert u een toepassingsentiteit in Azure Active Directory (Azure AD) en verleent u deze de **rol inzender** van het abonnement of de brongroep waarin het HDInsight-cluster is gemaakt. Zie [Portal gebruiken om een Azure Active Directory-toepassing en serviceprincipal te maken die toegang heeft tot bronnen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)voor gedetailleerde stappen. Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

- Toepassings-id
- Toepassingssleutel 
- Tenant-id

Gebruik serviceprincipal-verificatie door de volgende eigenschappen op te geven:

| Eigenschap                | Beschrijving                              | Vereist |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Geef de client-id van de toepassing op.     | Ja      |
| **servicePrincipalKey** | Geef de sleutel van de toepassing op.           | Ja      |
| **Huurder**              | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. U deze ophalen door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja      |

### <a name="advanced-properties"></a>Geavanceerde eigenschappen

U ook de volgende eigenschappen opgeven voor de gedetailleerde configuratie van het on-demand HDInsight-cluster.

| Eigenschap               | Beschrijving                              | Vereist |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguratie      | Hiermee geeft u de kernconfiguratieparameters op (zoals in core-site.xml) voor het te maken HDInsight-cluster. | Nee       |
| hBaseConfiguration     | Hiermee geeft u de HBase-configuratieparameters (hbase-site.xml) op voor het HDInsight-cluster. | Nee       |
| hdfsConfiguratie      | Hiermee geeft u de HDFS-configuratieparameters (hdfs-site.xml) op voor het HDInsight-cluster. | Nee       |
| hiveConfiguratie      | Hiermee geeft u de detectieparameters voor bijenkorfconfiguratie (hive-site.xml) op voor het HDInsight-cluster. | Nee       |
| mapReduceConfiguration | Hiermee geeft u de configuratieparameters mapreduce (mapred-site.xml) op voor het HDInsight-cluster. | Nee       |
| oozieConfiguratie     | Hiermee geeft u de Oozie-configuratieparameters (oozie-site.xml) op voor het HDInsight-cluster. | Nee       |
| stormConfiguratie     | Hiermee geeft u de parameters voor stormconfiguratie (stormsite.xml) op voor het HDInsight-cluster. | Nee       |
| garenConfiguratie      | Hiermee geeft u de configuratieparameters garen (garen-site.xml) op voor het HDInsight-cluster. | Nee       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Voorbeeld : On-demand HDInsight-clusterconfiguratie met geavanceerde eigenschappen

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

### <a name="node-sizes"></a>Knooppuntmaten
U de grootte van hoofd-, gegevens- en zookeeperknooppunten opgeven met behulp van de volgende eigenschappen: 

| Eigenschap          | Beschrijving                              | Vereist |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Hiermee geeft u de grootte van het hoofdknooppunt op. De standaardwaarde is: Standard_D3. Zie de sectie **Knooppuntgrootte opgeven** voor meer informatie. | Nee       |
| dataNodeSize      | Hiermee geeft u de grootte van het gegevensknooppunt op. De standaardwaarde is: Standard_D3. | Nee       |
| zookeeperNodeSize | Hiermee geeft u de grootte van het knooppunt Dierenwachter op. De standaardwaarde is: Standard_D3. | Nee       |

#### <a name="specifying-node-sizes"></a>Knooppuntgroottes opgeven
Zie het artikel [Grootte van virtuele machines](../virtual-machines/linux/sizes.md) voor tekenreekswaarden die u moet opgeven voor de eigenschappen die in de vorige sectie worden vermeld. De waarden moeten voldoen aan de **CMDLETs & API's waarnaar** in het artikel wordt verwezen. Zoals u zien in het artikel, de gegevens knooppunt van Grote (standaard) grootte heeft 7-GB geheugen, die mogelijk niet goed genoeg voor uw scenario. 

Als u hoofdknooppunten en werkknooppunten met D4-formaat wilt maken, geeft u **Standard_D4** op als de waarde voor eigenschappen headNodeSize en dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Als u een verkeerde waarde voor deze eigenschappen opgeeft, ontvangt u mogelijk de volgende **fout:** Kan cluster niet maken. Uitzondering: Kan het cluster niet maken. Bewerking is mislukt met code 400. Cluster heeft status: 'Fout'. Bericht: 'Preclustercreationvalidationfailure'. Wanneer u deze fout ontvangt, moet u ervoor zorgen dat u de naam **CMDLET & APIS** uit de tabel in het artikel [Grootte van virtuele machines](../virtual-machines/linux/sizes.md) gebruikt.        

## <a name="bring-your-own-compute-environment"></a>Breng uw eigen compute omgeving
In dit type configuratie kunnen gebruikers een reeds bestaande computeromgeving registreren als gekoppelde service in Data Factory. De computeromgeving wordt beheerd door de gebruiker en de Data Factory-service gebruikt deze om de activiteiten uit te voeren.

Dit type configuratie wordt ondersteund voor de volgende compute-omgevingen:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Gekoppelde Azure HDInsight-service
U een Azure HDInsight-gekoppelde service maken om uw eigen HDInsight-cluster te registreren bij Data Factory.

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
| Eigenschap          | Beschrijving                                                  | Vereist |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | De eigenschap type moet worden ingesteld op **HDInsight**.            | Ja      |
| clusterUri        | De URI van het HDInsight-cluster.                            | Ja      |
| gebruikersnaam          | Geef de naam op van de gebruiker die moet worden gebruikt om verbinding te maken met een bestaand HDInsight-cluster. | Ja      |
| wachtwoord          | Geef het wachtwoord voor het gebruikersaccount op.                       | Ja      |
| linkedServiceName | Naam van de gekoppelde Azure Storage-service die verwijst naar de Azure blob-opslag die wordt gebruikt door het HDInsight-cluster. <p>Momenteel u geen gekoppelde Azure Data Lake Store-service voor deze eigenschap opgeven. Als het HDInsight-cluster toegang heeft tot de Data Lake Store, hebt u mogelijk toegang tot gegevens in de Azure Data Lake Store vanuit Hive/Pig-scripts. </p> | Ja      |
| isEspEnabled      | Geef *'true' op*als het HDInsight-cluster [enterprise security package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture) is ingeschakeld. Standaard is '*false*'. | Nee       |
| connectVia        | De integratieruntijd die moet worden gebruikt om de activiteiten naar deze gekoppelde service te verzenden. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. <br />Voor Enterprise Security Package (ESP) ingeschakeld HDInsight cluster gebruik maken van een self-hosted integratie runtime, die een lijn van het zicht naar het cluster heeft of het moet worden ingezet binnen hetzelfde virtuele netwerk als de ESP HDInsight cluster. | Nee       |

> [!IMPORTANT]
> HDInsight ondersteunt meerdere Hadoop-clusterversies die kunnen worden geïmplementeerd. Elke versiekeuze maakt een specifieke versie van de HDP-distributie (Hortonworks Data Platform) en een set componenten die zich in die distributie bevinden. De lijst met ondersteunde HDInsight-versies wordt steeds bijgewerkt om de nieuwste Hadoop-ecosysteemcomponenten en -oplossingen te bieden. Zorg ervoor dat u altijd verwijst naar de nieuwste informatie van [de ondersteunde HDInsight-versie en het OS-type](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) om ervoor te zorgen dat u de ondersteunde versie van HDInsight gebruikt. 
>
> [!IMPORTANT]
> Momenteel ondersteunen HDInsight linked services geen HBase, Interactive Query (Hive LLAP), Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Gekoppelde Azure Batch-service

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U een Azure Batch-gekoppelde service maken om een batchgroep met virtuele machines (VM's) te registreren in een gegevensfabriek. U aangepaste activiteit uitvoeren met Azure Batch.

Zie de volgende artikelen als u nieuw bent bij de Azure Batch-service:

* [Basisbeginselen van Azure Batch](../batch/batch-technical-overview.md) voor een overzicht van de Azure Batch-service.
* De cmdlet van [Nieuw-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) om een Azure Batch-account (of) [Azure-portal](../batch/batch-account-create-portal.md) te maken om het Azure Batch-account te maken met Azure-portal. Zie PowerShell gebruiken om het artikel [over Azure Batch-account te beheren](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) voor gedetailleerde instructies over het gebruik van de cmdlet.
* De cmdlet [nieuw-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) om een Azure Batch-pool te maken.

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
| Eigenschap          | Beschrijving                              | Vereist |
| ----------------- | ---------------------------------------- | -------- |
| type              | De eigenschap type moet worden ingesteld op **AzureBatch**. | Ja      |
| accountName       | Naam van het Azure Batch-account.         | Ja      |
| Accesskey         | Toegangssleutel voor het Azure Batch-account.  | Ja      |
| batchUri          | URL naar uw Azure Batch-account, in opmaak van https://*batchaccountname.region*.batch.azure.com. | Ja      |
| poolNaam          | Naam van de pool van virtuele machines.    | Ja      |
| linkedServiceName | Naam van de gekoppelde Azure Storage-service die is gekoppeld aan deze gekoppelde Azure Batch-service. Deze gekoppelde service wordt gebruikt voor het uitvoeren van bestanden die nodig zijn om de activiteit uit te voeren. | Ja      |
| connectVia        | De integratieruntijd die moet worden gebruikt om de activiteiten naar deze gekoppelde service te verzenden. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee       |

## <a name="azure-machine-learning-studio-linked-service"></a>Gekoppelde Azure Machine Learning Studio-service
U maakt een azure machine learning studio-gekoppelde service om een eindpunt voor batchscoring voor Machine Learning te registreren in een gegevensfabriek.

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
| Eigenschap               | Beschrijving                              | Vereist                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                   | De eigenschap type moet worden ingesteld op: **AzureML**. | Ja                                      |
| mlEindpunt             | De URL voor batchscores.                   | Ja                                      |
| apiKey                 | De API van het gepubliceerde werkruimtemodel.     | Ja                                      |
| updateResourceEndpoint | De URL van de updatebron voor een Azure Machine Learning Web Service-eindpunt dat wordt gebruikt om de voorspellende webservice bij te werken met een getraind modelbestand | Nee                                       |
| servicePrincipalId     | Geef de client-id van de toepassing op.     | Vereist als updateResourceEndpoint is opgegeven |
| servicePrincipalKey    | Geef de sleutel van de toepassing op.           | Vereist als updateResourceEndpoint is opgegeven |
| tenant                 | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. U deze ophalen door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Vereist als updateResourceEndpoint is opgegeven |
| connectVia             | De integratieruntijd die moet worden gebruikt om de activiteiten naar deze gekoppelde service te verzenden. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee                                       |

## <a name="azure-machine-learning-linked-service"></a>Gekoppelde Azure Machine Learning-service
U maakt een Azure Machine Learning-gekoppelde service om een Azure Machine Learning-werkruimte te verbinden met een gegevensfabriek.

> [!NOTE]
> Momenteel wordt alleen serviceprincipal-verificatie ondersteund voor de gekoppelde Azure Machine Learning-service.

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
| Eigenschap               | Beschrijving                              | Vereist                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                   | De eigenschap type moet worden ingesteld op: **AzureMLService**. | Ja                                      |
| subscriptionId         | Azure-abonnements-ID              | Ja                                      |
| resourceGroupName      | name | Ja                                      |
| mlWorkspaceName        | Naam azure Machine Learning-werkruimte | Ja  |
| servicePrincipalId     | Geef de client-id van de toepassing op.     | Nee |
| servicePrincipalKey    | Geef de sleutel van de toepassing op.           | Nee |
| tenant                 | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. U deze ophalen door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Vereist als updateResourceEndpoint is opgegeven | Nee |
| connectVia             | De integratieruntijd die moet worden gebruikt om de activiteiten naar deze gekoppelde service te verzenden. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee |    

## <a name="azure-data-lake-analytics-linked-service"></a>Gekoppelde Azure Data Lake Analytics-service
U maakt een **azure Data Lake Analytics-gekoppelde** service om een Azure Data Lake Analytics-computeservice te koppelen aan een Azure-gegevensfabriek. De Data Lake Analytics U-SQL-activiteit in de pijplijn verwijst naar deze gekoppelde service. 

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

| Eigenschap             | Beschrijving                              | Vereist                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | De eigenschap type moet worden ingesteld op: **AzureDataLakeAnalytics**. | Ja                                      |
| accountName          | De naam van het Azure Data Lake Analytics-account.  | Ja                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics URI.           | Nee                                       |
| subscriptionId       | Azure-abonnements-ID                    | Nee                                       |
| resourceGroupName    | Naam van Azure-resourcegroep                | Nee                                       |
| servicePrincipalId   | Geef de client-id van de toepassing op.     | Ja                                      |
| servicePrincipalKey  | Geef de sleutel van de toepassing op.           | Ja                                      |
| tenant               | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. U deze ophalen door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja                                      |
| connectVia           | De integratieruntijd die moet worden gebruikt om de activiteiten naar deze gekoppelde service te verzenden. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee                                       |



## <a name="azure-databricks-linked-service"></a>Gekoppelde Azure Databricks-service
U **azure databricks-gekoppelde service** maken om de werkruimte Databricks te registreren die u gebruikt om de Databricks-workloads (notebook, jar, python) uit te voeren. 
> [!IMPORTANT]
> Databricks linked services ondersteunt [Instantie-groepen.](https://aka.ms/instance-pools) 

### <a name="example---using-new-job-cluster-in-databricks"></a>Voorbeeld - Nieuwe taakcluster gebruiken in Databricks

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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Voorbeeld - Bestaande interactieve cluster gebruiken in Databricks

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

| Eigenschap             | Beschrijving                              | Vereist                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| name                 | Naam van de gekoppelde service               | Ja   |
| type                 | De eigenschap type moet worden ingesteld op: **Azure Databricks**. | Ja                                      |
| domein               | Geef het Azure-gebied dienovereenkomstig op basis van het gebied van de werkruimte Databricks. Voorbeeld: https://eastus.azuredatabricks.net | Ja                                 |
| accessToken          | Toegangstoken is vereist voor Data Factory om te verifiëren naar Azure Databricks. Toegangstoken moet worden gegenereerd vanuit de werkruimte van databricks. Meer gedetailleerde stappen om het toegangstoken te vinden zijn [hier](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token) te vinden  | Ja                                       |
| bestaandeClusterId    | Cluster-ID van een bestaand cluster om alle taken hierop uit te voeren. Dit moet een reeds gemaakt interactief cluster zijn. Mogelijk moet u het cluster handmatig opnieuw starten als het niet meer reageert. Databricks suggereren dat taken worden uitgevoerd op nieuwe clusters voor een grotere betrouwbaarheid. U de cluster-id van een interactief cluster op de werkruimte Databricks -> Clusters -> Interactieve clusternaam -> Configuration ->-tags vinden. [Meer informatie](https://docs.databricks.com/user-guide/clusters/tags.html) | Nee 
| instancePoolId    | Instantie groep-id van een bestaande groep in de werkruimte databricks.  | Nee  |
| newClusterVersion    | De Spark-versie van het cluster. Het zal een taakcluster in databricks maken. | Nee  |
| newClusterNumOfWorker| Aantal werknemersknooppunten dat dit cluster moet hebben. Een cluster heeft één Spark-stuurprogramma en num_workers executors voor een totaal van num_workers + 1 Spark-knooppunten. Een tekenreeks opgemaakt Int32, zoals "1" betekent dat numOfWorker 1 of "1:10" betekent autoschaal van 1 als min en 10 als max.  | Nee                |
| newClusterNodeType   | Dit veld codeert, door middel van één waarde, de resources die beschikbaar zijn voor elk van de Spark-knooppunten in dit cluster. De Spark-knooppunten kunnen bijvoorbeeld worden ingericht en geoptimaliseerd voor geheugen- of rekenintensieve workloads. Dit veld is vereist voor een nieuw cluster                | Nee               |
| newClusterSparkConf  | een set optionele, door de gebruiker opgegeven Spark-configuratiesleutelparen. Gebruikers kunnen ook in een reeks van extra JVM opties doorgeven aan de bestuurder en de uitvoerders via spark.driver.extraJavaOptions en spark.executor.extraJavaOptions respectievelijk. | Nee  |
| newClusterInitScripts| een set optionele, door de gebruiker gedefinieerde initialisatiescripts voor het nieuwe cluster. Het DBFS-pad opgeven naar de init-scripts. | Nee  |


## <a name="azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service
U maakt een Azure SQL-gekoppelde service en gebruikt deze met de [activiteit Opgeslagen procedure](transform-data-using-stored-procedure.md) om een opgeslagen procedure van een Data Factory-pijplijn aan te roepen. Zie [het azure SQL Connector-artikel](connector-azure-sql-database.md#linked-service-properties) voor meer informatie over deze gekoppelde service.

## <a name="azure-sql-data-warehouse-linked-service"></a>Gekoppelde Azure SQL Data Warehouse-service
U maakt een Azure SQL Data Warehouse-gekoppelde service en gebruikt deze met de [activiteit Opgeslagen procedure](transform-data-using-stored-procedure.md) om een opgeslagen procedure van een Data Factory-pijplijn aan te roepen. Zie het artikel [Azure SQL Data Warehouse Connector](connector-azure-sql-data-warehouse.md#linked-service-properties) voor meer informatie over deze gekoppelde service.

## <a name="sql-server-linked-service"></a>SQL Server-gekoppelde service
U maakt een SQL Server-gekoppelde service en gebruikt deze met de [activiteit Opgeslagen procedure](transform-data-using-stored-procedure.md) om een opgeslagen procedure van een Data Factory-pijplijn aan te roepen. Zie [SQL Server-connectorartikel](connector-sql-server.md#linked-service-properties) voor meer informatie over deze gekoppelde service.

## <a name="azure-function-linked-service"></a>Gekoppelde Azure-functieservice
U maakt een Azure-functiegekoppelde service en gebruikt deze met de [Azure-functieactiviteit](control-flow-azure-function-activity.md) om Azure-functies uit te voeren in een pijplijn voor gegevensfabrieken. Het retourtype van de Azure-functie `JObject`moet geldig zijn. (Houd er rekening mee *not* dat `JObject` [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) geen .) Elk retourtype `JObject` dat niet is mislukt en de inhoud van de reactievan de gebruikersfout *verhoogt, is geen geldig JObject*.

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| type   | De eigenschap type moet zijn ingesteld op: **AzureFunction** | ja |
| url van functie-app | URL voor de Azure Function App. Opmaak `https://<accountname>.azurewebsites.net`is . Deze URL is de waarde onder **de SECTIE URL** bij het bekijken van uw functie-app in de Azure-portal  | ja |
| functiesleutel | Toegangssleutel voor de Azure-functie. Klik op de sectie **Beheren** voor de betreffende functie en kopieer de **functiesleutel** of de **hostsleutel.** Lees hier meer: [Http-triggers en bindingen voor Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ja |
|   |   |   |

## <a name="next-steps"></a>Volgende stappen
Zie [Gegevens transformeren](transform-data.md)voor een lijst met de transformatieactiviteiten die worden ondersteund door Azure Data Factory.
