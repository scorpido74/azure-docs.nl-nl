---
title: Integration Runtime in Azure Data Factory
description: Meer informatie over Integration Runtime in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: abnarain
ms.openlocfilehash: 0b137edbfb5ca439d4ba15614225ec0973511763
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218814"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime in Azure Data Factory
De Integration Runtime (IR) is de rekeninfrastructuur die Azure Data Factory gebruikt om de volgende mogelijkheden voor gegevensintegratie in verschillende netwerkomgevingen te bieden:

- **Data Flow**: Execute a [Data Flow](concepts-data-flow-overview.md) in managed Azure compute environment.  
- **Data movement**: Copy data across data stores in public network and data stores in private network (on-premises or virtual private network). Deze optie biedt ondersteuning voor ingebouwde connectors, indelingsconversie, kolomtoewijzing en hoogwaardige en schaalbare gegevensoverdracht.
- **Activity dispatch**:  Dispatch and monitor transformation activities running on a variety of compute services such as Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, and more.
- **SSIS-pakketuitvoering**: systeemeigen SSIS-pakketten (SQL Server Integration Services) uitvoeren in een beheerde Azure-rekenomgeving.

In de Data Factory definieert een activiteit de actie die moet worden uitgevoerd. Een gekoppelde service definieert een doelgegevensarchief of een rekenservice. Een Integration Runtime vormt de brug tussen de activiteit en de gekoppelde services.  It is referenced by the linked service or activity, and provides the compute environment where the activity either runs on or gets dispatched from. Op deze manier kan de activiteit optimaal worden uitgevoerd in de regio die het dichtst mogelijk bij het doelgegevensarchief of de rekenservice ligt, terwijl wordt voldaan aan vereisten rondom beveiliging en naleving.

## <a name="integration-runtime-types"></a>Typen Integration Runtime
Data Factory biedt drie typen Integration Runtime. Kies het type dat het beste aansluit op de mogelijkheden voor de gegevensintegratie en de behoeften op het gebied van de netwerkomgeving.  Deze drie typen zijn:

- Azure
- Zelf-hostend
- Azure-SSIS

De volgende tabel beschrijft de mogelijkheden en de netwerkondersteuning voor de drie typen Integration Runtime:

IR-type | Openbaar netwerk | Privénetwerk
------- | -------------- | ---------------
Azure | Gegevensstroom<br/>Gegevensverplaatsing<br/>Verzending van de activiteit | &nbsp;
Zelf-hostend | Gegevensverplaatsing<br/>Verzending van de activiteit | Gegevensverplaatsing<br/>Verzending van de activiteit
Azure-SSIS | Uitvoering van SSIS-pakket | Uitvoering van SSIS-pakket

Het volgende diagram toont hoe verschillende typen Integration Runtime gecombineerd kunnen worden gebruikt om geavanceerde mogelijkheden voor gegevensintegratie en netwerkondersteuning te bieden:

![Verschillende typen Integration Runtime](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure-integratieruntime
Een Azure Integration Runtime is geschikt voor:

- Running Data Flows in Azure 
- Het uitvoeren van kopieeractiviteit tussen gegevensarchieven in de cloud
- Dispatching the following transform activities in public network: Databricks Notebook/ Jar/ Python activity, HDInsight Hive activity, HDInsight Pig activity, HDInsight MapReduce activity, HDInsight Spark activity, HDInsight Streaming activity, Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure activity, Data Lake Analytics U-SQL activity, .NET custom activity, Web activity, Lookup activity, and Get Metadata activity.

### <a name="azure-ir-network-environment"></a>Azure IR-netwerkomgeving
Azure Integration Runtime supports connecting to data stores and compute services with public accessible endpoints. Gebruik een zelf-hostende Integration Runtime voor een Azure Virtual Network-omgeving.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR-rekenresource en -schalen
Azure Integration Runtime biedt een volledig beheerde, serverloze rekenresource in Azure.  U hoeft zich geen zorgen te maken over het inrichten van de infrastructuur, de software-installatie, patchen of capaciteitsschaling.  Bovendien betaalt u alleen voor het werkelijke gebruik.

Azure Integration Runtime biedt de systeemeigen rekenkracht om gegevens te verplaatsen tussen gegevensarchieven in de cloud op een veilige, betrouwbare en krachtige manier.  U kunt instellen hoeveel eenheden voor gegevensintegratie worden gebruikt in de kopieeractiviteit. De rekenkracht van de Azure IR wordt flexibel opgeschaald om aan uw behoeften te voldoen, zonder dat u de grootte van de Azure Integration Runtime expliciet hoeft aan te passen. 

Verzending van de activiteit is een lichte bewerking om de activiteit naar de doelrekenservice te routeren. U hoeft de rekenkracht niet op te schalen voor dit scenario.

Zie voor informatie over het maken en configureren van een Azure-IR 'Azure IR maken en configureren' bij de gidsen. 

> [!NOTE] 
> Azure Integration runtime has properties related to Data Flow runtime, which defines the underlying compute infrastructure that would be used to run the data flows on. 

## <a name="self-hosted-integration-runtime"></a>Zelf-hostende integratieruntime
Een zelf-hostende IR is geschikt voor:

- Het uitvoeren van kopieeractiviteit tussen een gegevensarchief in de cloud en een gegevensarchief in een privénetwerk.
- Dispatching the following transform activities against compute resources in on-premises or Azure Virtual Network: HDInsight Hive activity (BYOC-Bring Your Own Cluster), HDInsight Pig activity (BYOC), HDInsight MapReduce activity (BYOC), HDInsight Spark activity (BYOC), HDInsight Streaming activity (BYOC), Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure activity, Data Lake Analytics U-SQL activity, Custom activity (runs on Azure Batch), Lookup activity, and Get Metadata activity.

> [!NOTE] 
> Use self-hosted integration runtime to support data stores that requires bring-your-own driver such as SAP Hana, MySQL, etc.  For more information, see [supported data stores](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) is a dependency of Self Hosted IR. Please make sure you have JRE installed on the same host.

### <a name="self-hosted-ir-network-environment"></a>Zelf-hostende IR-netwerkomgeving
Als u gegevensintegratie veilig wilt uitvoeren in een privénetwerkomgeving zonder rechtstreekse zichtbaarheid vanuit de openbare cloudomgeving, kunt u een zelf-hostende on-premises IR installeren achter de firewall van het bedrijf of in een virtueel privénetwerk.  De zelf-hostende Integration Runtime maakt alleen uitgaande HTTP-gebaseerde verbindingen met het openbare internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Zelf-hostende IR-rekenresource en -schalen
Zelf-hostende IR moet worden geïnstalleerd op een on-premises computer of op een virtuele machine in een privénetwerk. Op dit moment ondersteunen we alleen zelf-hostende IR op een Windows-besturingssysteem.  

Voor hoge beschikbaarheid en schaalbaarheid kunt u de zelf-hostende IR uitbreiden door het logische exemplaar te koppelen aan meerdere on-premises computers in de modus actief-actief.  For more information, see how to [create and configure self-hosted IR](create-self-hosted-integration-runtime.md) article under how to guides for details.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime
Als u de bestaande SSIS-werkbelasting wilt opheffen of verplaatsen, kunt u een Azure-SSIS IR maken voor het uitvoeren van systeemeigen SSIS-pakketten.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR-netwerkomgeving
Azure-SSIS IR kan worden ingericht in een openbaar netwerk of privénetwerk.  Toegang tot on-premises gegevens wordt ondersteund door Azure SSIS IR te koppelen aan een virtueel netwerk dat is verbonden met uw on-premises netwerk.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR-rekenresource en -schalen
Azure-SSIS IR is een volledig beheerd cluster met virtuele Azure-machines die uw SSIS-pakketten uitvoeren. You can bring your own Azure SQL Database or Managed Instance server to host the catalog of SSIS projects/packages (SSISDB) that is going to be attached to it. U kunt de rekenkracht opschalen door de grootte van het knooppunt op te geven en opschalen door het aantal knooppunten in het cluster aan te geven. U kunt de kosten van het uitvoeren van de uw Azure-SSIS Integration Runtime beheren door naar wens te stoppen en starten.

Zie voor meer informatie het artikel 'Azure-SSIS IR maken en configureren' bij de gidsen.  Wanneer u de Azure SSIS IR hebt gemaakt, kunt u uw bestaande SSIS-pakketten implementeren en beheren met weinig of geen wijzigingen met behulp van bekende hulpprogramma's zoals SQL Server Data Tools (SSDT) en SQL Server Management Studio (SSMS), net als bij on-premises gebruik van SSIS.

Zie de volgende artikelen voor meer informatie over Azure-SSIS Runtime: 

- [Zelfstudie: SSIS-pakketten implementeren in Azure](tutorial-create-azure-ssis-runtime-portal.md). Dit artikel biedt stapsgewijze instructies voor het maken van een Azure-SSIS IR en maakt gebruik van een Azure SQL-database voor het hosten van de SSIS-catalogus. 
- [Procedure: Een Azure SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). This article expands on the tutorial and provides instructions on using Azure SQL Database Managed Instance and joining the IR to a virtual network. 
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over een Azure-SSIS IR ophaalt. Daarnaast bevat het artikel beschrijvingen van statuswaarden die worden gebruikt in de geretourneerde informatie. 
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel leest u hoe u een Azure-SSIS IR stopt, start of verwijdert. Er wordt ook uitgelegd hoe u een Azure-SSIS IR kunt uitschalen door meer knooppunten toe te voegen aan de IR. 
- [Een Azure-SSIS-integratieruntime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat algemene informatie over het toevoegen van een Azure-SSIS IR aan een virtueel netwerk van Azure. Er wordt ook beschreven hoe u Azure Portal gebruikt om een virtueel netwerk te configureren voor het deelnemen van Azure-SSIS IR aan het virtueel netwerk. 

## <a name="integration-runtime-location"></a>Locatie van Integration Runtime
De Data Factory-locatie is waar de metagegevens van de data factory worden opgeslagen en waar van het activeren van de pijplijn wordt gestart. Ondertussen heeft een data factory wel toegang tot gegevensarchieven en Compute Services in andere Azure-regio’s om gegevens te verplaatsen tussen gegevensarchieven of om gegevens te verwerken middels Compute Services. Dit gedrag wordt gerealiseerd via de [IR die algemeen beschikbaar](https://azure.microsoft.com/global-infrastructure/services/) is om de gegevensnaleving, efficiëntie en verminderde kosten voor uitgaand netwerkverkeer te realiseren.

De locatie van de IR definieert de locatie van de back-endrekenkracht en in wezen de locatie waar de gegevensverplaatsing, het verzenden van activiteit en de uitvoering van het SSIS-pakket worden uitgevoerd. De locatie van de IR kan afwijken van de locatie van de data factory waar hij bij hoort. 

### <a name="azure-ir-location"></a>Locatie van Azure IR
U kunt een bepaalde locatie van een Azure IR instellen, in welk geval de gegevensverplaatsing of verzendactiviteit in die specifieke regio plaatsvindt. 

If you choose to use the **auto-resolve Azure IR** which is the default, 

- Voor kopieeractiviteit probeert ADF om automatisch uw sink- en brongegevensopslag te detecteren om de beste locatie te vinden in dezelfde regio, indien beschikbaar, of in de dichtstbijzijnde regio in dezelfde geografie. Indien niet detecteerbaar, wordt de regio van de data factory gebruikt.

- For Lookup/GetMetadata/Delete activity execution (also known as Pipeline activities), transformation activity dispatching (also known as External activities), and authoring operations (test connection, browse folder list and table list, preview data), ADF will use the IR in the data factory region.

- For Data Flow, ADF will use the IR in the data factory region. 

  > [!TIP] 
  > A good practice would be to ensure Data flow runs in the same region as your corresponding data stores (if possible). You can either achieve this by auto-resolve Azure IR (if data store location is same as Data Factory location), or by creating a new Azure IR instance in the same region as your data stores and then execute the data flow on it. 

U kunt controleren welke IR-locatie van kracht wordt tijdens het uitvoeren van activiteiten in de weergave voor het controleren van de pijplijnactiviteit in de gebruikersinterface of nettolading voor het controleren van activiteiten.

>[!TIP]
>Als u strikte gegevensnalevingsvereisten hebt en u ervoor moet zorgen moet dat gegevens een bepaalde geografie niet verlaten, kunt u expliciet een Azure IR maken in een bepaalde regio en de gekoppelde service naar deze IR laten wijzen met behulp van de eigenschap ConnectVia. Als u bijvoorbeeld gegevens uit Blob in UK - zuid naar SQL DW in UK - zuid wilt kopiëren en u ervoor wilt zorgen dat de gegevens het UK niet verlaten, maakt u een Azure IR en koppelt u beide gekoppelde services aan deze IR.

### <a name="self-hosted-ir-location"></a>Locatie zelf-hostende IR
De zelf-hostende IR is logisch geregistreerd bij de Data Factory en de rekenkracht die wordt gebruikt ter ondersteuning van de functionaliteiten die u hebt opgegeven. Er bestaat daarom geen expliciete locatie-eigenschap voor de zelf-hostende IR. 

Wanneer de zelf-hostende IR wordt gebruikt voor het uitvoeren van de gegevensverplaatsing, haalt deze gegevens uit de bron en schrijft naar de bestemming.

### <a name="azure-ssis-ir-location"></a>Locatie Azure-SSIS IR
Het selecteren van de juiste locatie voor uw Azure-SSIS IR is essentieel voor het bereiken van hoge prestaties in uw ETL-werkstromen (extract-transform-load).

- The location of your Azure-SSIS IR does not need be the same as the location of your data factory, but it should be the same as the location of your own Azure SQL Database/Managed Instance server where SSISDB is to be hosted. Op deze manier heeft uw Azure-SSIS Integration Runtime eenvoudig toegang tot SSISDB, zonder overmatig verkeer tussen verschillende locaties.
- If you do not have an existing Azure SQL Database/Managed Instance server to host SSISDB, but you have on-premises data sources/destinations, you should create a new Azure SQL Database/Managed Instance server in the same location of a virtual network connected to your on-premises network.  This way, you can create your Azure-SSIS IR using the new Azure SQL Database/Managed Instance server and joining that virtual network, all in the same location, effectively minimizing data movements across different locations.
- If the location of your existing Azure SQL Database/Managed Instance server where SSISDB is hosted is not the same as the location of a virtual network connected to your on-premises network, first create your Azure-SSIS IR using an existing Azure SQL Database/Managed Instance server and joining another virtual network in the same location, and then configure a virtual network to virtual network connection between different locations.

Het volgende diagram toont de locatie-instellingen van Data Factory en het aantal keren dat de integratie wordt uitgevoerd:

![Locatie van Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Bepalen welke IR u moet gebruiken

### <a name="copy-activity"></a>Kopieeractiviteit

Voor de kopieeractiviteit, zijn de gekoppelde bron- en sinkservices vereist voor het definiëren van de richting van de gegevensstroom. De volgende logica wordt gebruikt om te bepalen welk exemplaar van Integration Runtime wordt gebruikt voor het uitvoeren van de kopieeractiviteit: 

- **Kopiëren tussen twee gegevensbronnen in de cloud:** : wanneer zowel de gekoppelde bronservices als de sinkservices Azure IR gebruiken, gebruikt ADF de regionale Azure IR die u hebt opgegeven of wordt automatisch een locatie van Azure bepaald als u ervoor kiest de IR voor automatisch oplossen (standaardwaarde) te gebruiken, zoals beschreven in de sectie [Locatie van Integration Runtime](#integration-runtime-location).
- **Kopiëren tussen een gegevensbron in de cloud en een gegevensbron in een privénetwerk**: als de gekoppelde bron- of sinkservice verwijst naar een zelf-hostende IR, wordt de kopieerbewerking uitgevoerd op die zelf-hostende Integration Runtime.
- **Kopiëren tussen twee gegevensbronnen in een privénetwerk**: zowel de gekoppelde bronservice als de sinkservice moet verwijzen naar hetzelfde exemplaar van Integration Runtime, en deze wordt gebruikt voor het uitvoeren van de kopieeractiviteit.

### <a name="lookup-and-getmetadata-activity"></a>Activiteit Lookup en GetMetadata

De activiteit Lookup en GetMetadata wordt uitgevoerd voor de integratieruntime die is gekoppeld aan de service die aan de gegevensopslag is gekoppeld.

### <a name="transformation-activity"></a>Transformatieactiviteit

Elke transformatieactiviteit heeft een gekoppelde doelrekenservice die naar een Integration Runtime verwijst. Vanuit dit exemplaar van de Integration Runtime wordt de transformatieactiviteit verzonden.

### <a name="data-flow-activity"></a>Data Flow activity

Data Flow activity is executed on the integration runtime associated to it. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Create Azure integration runtime](create-azure-integration-runtime.md)
- [Zelf-hostende integratie-runtime maken](create-self-hosted-integration-runtime.md)
- [Een Azure-SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). This article expands on the tutorial and provides instructions on using Azure SQL Database Managed Instance and joining the IR to a virtual network. 
