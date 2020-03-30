---
title: Integratie-runtime
description: Meer informatie over Integration Runtime in Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/26/2020
ms.openlocfilehash: 4077e1e00b606480ec93feacbad3c841c0de1ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336180"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime in Azure Data Factory
De Integratie Runtime (IR) is de rekeninfrastructuur die Azure Data Factory gebruikt om de volgende mogelijkheden voor gegevensintegratie in verschillende netwerkomgevingen te bieden:

- **Gegevensstroom:** Voer een [gegevensstroom](concepts-data-flow-overview.md) uit in de beheerde Azure-compute-omgeving.  
- **Gegevensverkeer**: Gegevens kopiëren over gegevensarchieven in openbare netwerk- en gegevensarchieven in een privénetwerk (on-premises of virtueel privénetwerk). Deze optie biedt ondersteuning voor ingebouwde connectors, indelingsconversie, kolomtoewijzing en hoogwaardige en schaalbare gegevensoverdracht.
- **Activiteitsverzending:** transformatieactiviteiten verzenden en bewaken die worden uitgevoerd op verschillende compute services, zoals Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server en meer.
- **SSIS-pakketuitvoering**: systeemeigen SSIS-pakketten (SQL Server Integration Services) uitvoeren in een beheerde Azure-rekenomgeving.

In de Data Factory definieert een activiteit de actie die moet worden uitgevoerd. Een gekoppelde service definieert een doelgegevensarchief of een rekenservice. Een Integration Runtime vormt de brug tussen de activiteit en de gekoppelde services.  Het wordt verwezen door de gekoppelde service of activiteit en biedt de compute-omgeving waar de activiteit wordt uitgevoerd of wordt verzonden. Op deze manier kan de activiteit optimaal worden uitgevoerd in de regio die het dichtst mogelijk bij het doelgegevensarchief of de rekenservice ligt, terwijl wordt voldaan aan vereisten rondom beveiliging en naleving.

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

## <a name="azure-integration-runtime"></a>Azure Integration Runtime
Een Azure Integration Runtime is geschikt voor:

- Gegevensstromen uitvoeren in Azure 
- Het uitvoeren van kopieeractiviteit tussen gegevensarchieven in de cloud
- Verzending van de volgende transformatieactiviteiten in het openbare netwerk: Databricks Notebook/ Jar/ Python-activiteit, HDInsight Hive-activiteit, HDInsight Pig-activiteit, HDInsight MapReduce-activiteit, HDInsight Spark-activiteit, HDInsight-streamingactiviteit, Machine Learning Batch Execution-activiteit, Machine Learning Update Resource-activiteiten, Activiteiten van de Opgeslagen procedure, Gegevenslake Analytics U-SQL-activiteit, .NET-aangepaste activiteit, webactiviteit, opzoekactiviteit en activiteit Metagegevens ophalen.

### <a name="azure-ir-network-environment"></a>Azure IR-netwerkomgeving
Azure Integration Runtime ondersteunt het verbinden met gegevensopslag en compute services met openbare toegankelijke eindpunten. Gebruik een zelf-hostende Integration Runtime voor een Azure Virtual Network-omgeving.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR-rekenresource en -schalen
Azure Integration Runtime biedt een volledig beheerde, serverloze rekenresource in Azure.  U hoeft zich geen zorgen te maken over infrastructuurvoorzieningen, software-installatie, patching of capaciteitsschaling.  Bovendien betaalt u alleen voor het werkelijke gebruik.

Azure Integration Runtime biedt de systeemeigen rekenkracht om gegevens te verplaatsen tussen gegevensarchieven in de cloud op een veilige, betrouwbare en krachtige manier.  U kunt instellen hoeveel eenheden voor gegevensintegratie worden gebruikt in de kopieeractiviteit. De rekenkracht van de Azure IR wordt flexibel opgeschaald om aan uw behoeften te voldoen, zonder dat u de grootte van de Azure Integration Runtime expliciet hoeft aan te passen. 

Activiteitsverzending is een lichtgewicht bewerking om de activiteit door te sturen naar de doelcomputeservice, zodat de rekengrootte voor dit scenario niet hoeft te worden opschaald.

Zie voor informatie over het maken en configureren van een Azure-IR 'Azure IR maken en configureren' bij de gidsen. 

> [!NOTE] 
> Azure Integration runtime heeft eigenschappen met betrekking tot Data Flow runtime, die de onderliggende compute-infrastructuur definieert die zou worden gebruikt om de gegevensstromen op uit te voeren. 

## <a name="self-hosted-integration-runtime"></a>Zelf-hostende Integration Runtime
Een zelf-hostende IR is geschikt voor:

- Het uitvoeren van kopieeractiviteit tussen een gegevensarchief in de cloud en een gegevensarchief in een privénetwerk.
- De volgende transformatieactiviteiten verzenden tegen compute resources in on-premises of Azure Virtual Network: HDInsight Hive activity (BYOC-Bring Your Own Cluster), HDInsight Pig activity (BYOC), HDInsight MapReduce activity (BYOC), HDInsight Spark activiteit (BYOC), HDInsight Streaming activity (BYOC), Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure activity, Data Lake Analytics U-SQL activity, Custom activity (runs on Azure Batch), Lookup activiteit en activiteit met metagegevens.

> [!NOTE] 
> Gebruik zelf gehoste runtime voor integratie om gegevensopslag te ondersteunen waarvoor bring-your-own driver nodig is, zoals SAP Hana, MySQL, enz.  Zie [ondersteunde gegevensarchieven voor](copy-activity-overview.md#supported-data-stores-and-formats)meer informatie .

> [!NOTE] 
> Java Runtime Environment (JRE) is een afhankelijkheid van Self Hosted IR. Zorg ervoor dat u JRE op dezelfde host hebt geïnstalleerd.

### <a name="self-hosted-ir-network-environment"></a>Zelf-hostende Azure IR-netwerkomgeving
Als u gegevensintegratie veilig wilt uitvoeren in een privénetwerkomgeving zonder rechtstreekse zichtbaarheid vanuit de openbare cloudomgeving, kunt u een zelf-hostende on-premises IR installeren achter de firewall van het bedrijf of in een virtueel privénetwerk.  De zelf-hostende Integration Runtime maakt alleen uitgaande HTTP-gebaseerde verbindingen met het openbare internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Zelf-hostende IR-rekenresource en -schalen
Zelf-hostende IR moet worden geïnstalleerd op een on-premises computer of op een virtuele machine in een privénetwerk. Op dit moment ondersteunen we alleen zelf-hostende IR op een Windows-besturingssysteem.  

Voor hoge beschikbaarheid en schaalbaarheid kunt u de zelf-hostende IR uitbreiden door het logische exemplaar te koppelen aan meerdere on-premises computers in de modus actief-actief.  Zie voor meer informatie hoe [u een zelfgehost IR-artikel maakt en configureert](create-self-hosted-integration-runtime.md) onder hoe u voor meer informatie gidsen.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime
Als u de bestaande SSIS-werkbelasting wilt opheffen of verplaatsen, kunt u een Azure-SSIS IR maken voor het uitvoeren van systeemeigen SSIS-pakketten.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR-netwerkomgeving
Azure-SSIS IR kan worden ingericht in een openbaar netwerk of privénetwerk.  Toegang tot on-premises gegevens wordt ondersteund door Azure SSIS IR te koppelen aan een virtueel netwerk dat is verbonden met uw on-premises netwerk.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR-rekenresource en -schalen
Azure-SSIS IR is een volledig beheerd cluster met virtuele Azure-machines die uw SSIS-pakketten uitvoeren. U uw eigen Azure SQL Database of Managed Instance-server meenemen om de catalogus met SSIS-projecten/-pakketten (SSISDB) te hosten die eraan zullen worden gekoppeld. U kunt de rekenkracht opschalen door de grootte van het knooppunt op te geven en opschalen door het aantal knooppunten in het cluster aan te geven. U kunt de kosten van het uitvoeren van de uw Azure-SSIS Integration Runtime beheren door naar wens te stoppen en starten.

Zie voor meer informatie het artikel 'Azure-SSIS IR maken en configureren' bij de gidsen.  Wanneer u de Azure SSIS IR hebt gemaakt, kunt u uw bestaande SSIS-pakketten implementeren en beheren met weinig of geen wijzigingen met behulp van bekende hulpprogramma's zoals SQL Server Data Tools (SSDT) en SQL Server Management Studio (SSMS), net als bij on-premises gebruik van SSIS.

Zie de volgende artikelen voor meer informatie over Azure-SSIS Runtime: 

- [Zelfstudie: SSIS-pakketten implementeren in Azure](tutorial-create-azure-ssis-runtime-portal.md). In dit artikel worden stapsgewijze instructies gegeven voor het maken van een Azure-SSIS IR en wordt een Azure SQL-database gebruikt om de SSIS-catalogus te hosten. 
- [Procedure: Een Azure SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelfstudie uitgebreid en worden instructies gegeven over het gebruik van Azure SQL Database Managed Instance en het samenvoegen van de IR bij een virtueel netwerk. 
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over een Azure-SSIS IR ophaalt. Daarnaast bevat het artikel beschrijvingen van statuswaarden die worden gebruikt in de geretourneerde informatie. 
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel leest u hoe u een Azure-SSIS IR stopt, start of verwijdert. Er wordt ook uitgelegd hoe u een Azure-SSIS IR kunt uitschalen door meer knooppunten toe te voegen aan de IR. 
- [Een Azure-SSIS-integratieruntime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat algemene informatie over het toevoegen van een Azure-SSIS IR aan een virtueel netwerk van Azure. Er wordt ook beschreven hoe u Azure Portal gebruikt om een virtueel netwerk te configureren voor het deelnemen van Azure-SSIS IR aan het virtueel netwerk. 

## <a name="integration-runtime-location"></a>Locatie van Integration Runtime
De Data Factory-locatie is waar de metagegevens van de data factory worden opgeslagen en waar van het activeren van de pijplijn wordt gestart. Ondertussen heeft een data factory wel toegang tot gegevensarchieven en Compute Services in andere Azure-regio’s om gegevens te verplaatsen tussen gegevensarchieven of om gegevens te verwerken middels Compute Services. Dit gedrag wordt gerealiseerd via de [IR die algemeen beschikbaar](https://azure.microsoft.com/global-infrastructure/services/) is om de gegevensnaleving, efficiëntie en verminderde kosten voor uitgaand netwerkverkeer te realiseren.

De locatie van de IR definieert de locatie van de back-end rekenkracht en in wezen de locatie waar de verplaatsing van gegevens, het verzenden van activiteit en de uitvoering van het SSIS-pakket worden uitgevoerd. De locatie van de IR kan afwijken van de locatie van de data factory waar hij bij hoort. 

### <a name="azure-ir-location"></a>Locatie van Azure IR
U kunt een bepaalde locatie van een Azure IR instellen, in welk geval de gegevensverplaatsing of verzendactiviteit in die specifieke regio plaatsvindt. 

>[!TIP]
>Als u strikte gegevensnalevingsvereisten hebt en u ervoor moet zorgen moet dat gegevens een bepaalde geografie niet verlaten, kunt u expliciet een Azure IR maken in een bepaalde regio en de gekoppelde service naar deze IR laten wijzen met behulp van de eigenschap ConnectVia. Als u bijvoorbeeld gegevens uit Blob in VK - zuid naar SQL DW in VK - zuid wilt kopiëren en u ervoor wilt zorgen dat de gegevens het UK niet verlaten, maakt u een Azure IR en koppelt u beide gekoppelde services aan deze IR.

Als u ervoor kiest om de Azure IR automatisch op te **lossen,** wat de standaardinstelling is, 

- Voor kopieeractiviteit zal ADF alles in het werk stellen om de locatie van uw sinkdataarchief automatisch te detecteren en vervolgens de IR in dezelfde regio te gebruiken indien beschikbaar of de dichtstbijzijnde in dezelfde geografie; als de regio van het sinkdataarchief niet detecteerbaar is, wordt IR in het gegevensfabriekgebied als alternatief gebruikt.

  U hebt bijvoorbeeld uw fabriek gemaakt in Oost-VS, 
  
  - Wanneer gegevens naar Azure Blob in West-VS worden gekopieerd, als ADF met succes heeft gedetecteerd dat de Blob zich in West-VS bevindt, wordt kopieeractiviteit uitgevoerd op IR in West-VS; als de regiodetectie mislukt, wordt kopieeractiviteit uitgevoerd op IR in Oost-VS.
  - Wanneer gegevens naar Salesforce worden gekopieerd waarvan de regio niet detecteerbaar is, wordt kopieeractiviteit uitgevoerd op IR in Oost-VS.

- Voor activiteitsuitvoering op zoek/getmetadata/delete (ook wel pijplijnactiviteiten genoemd), verzending van transformatieactiviteiten (ook bekend als externe activiteiten) en ontwerpbewerkingen (testverbinding, lijst met bladermappen en tabellijst, voorbeeldgegevens), gebruikt ADF de IR in het gegevensfabrieksgebied.

- Voor Data Flow gebruikt ADF de IR in het gegevensfabriekgebied. 

  > [!TIP] 
  > Een goede praktijk zou zijn om ervoor te zorgen datastroom draait in dezelfde regio als uw bijbehorende gegevens winkels (indien mogelijk). U dit bereiken door Azure IR automatisch op te lossen (als de locatie van het gegevensarchief gelijk is aan de locatie van Gegevensfabriek), of door een nieuwe Azure IR-instantie te maken in dezelfde regio als uw gegevensopslag en vervolgens de gegevensstroom op deze locatie uit te voeren. 

U kunt controleren welke IR-locatie van kracht wordt tijdens het uitvoeren van activiteiten in de weergave voor het controleren van de pijplijnactiviteit in de gebruikersinterface of nettolading voor het controleren van activiteiten.

### <a name="self-hosted-ir-location"></a>Locatie zelf-hostende IR
De zelf-hostende IR is logisch geregistreerd bij de Data Factory en de rekenkracht die wordt gebruikt ter ondersteuning van de functionaliteiten die u hebt opgegeven. Er bestaat daarom geen expliciete locatie-eigenschap voor de zelf-hostende IR. 

Wanneer de zelf-hostende IR wordt gebruikt voor het uitvoeren van de gegevensverplaatsing, haalt deze gegevens uit de bron en schrijft naar de bestemming.

### <a name="azure-ssis-ir-location"></a>Locatie Azure-SSIS IR
Het selecteren van de juiste locatie voor uw Azure-SSIS IR is essentieel voor het bereiken van hoge prestaties in uw ETL-werkstromen (extract-transform-load).

- De locatie van uw Azure-SSIS IR hoeft niet dezelfde te zijn als de locatie van uw gegevensfabriek, maar moet hetzelfde zijn als de locatie van uw eigen Azure SQL Database of Managed Instance-server waar SSISDB moet worden gehost. Op deze manier heeft uw Azure-SSIS Integration Runtime eenvoudig toegang tot SSISDB, zonder overmatig verkeer tussen verschillende locaties.
- Als u geen bestaande Azure SQL Database of Managed Instance-server hebt om SSISDB te hosten, maar u on-premises gegevensbronnen/bestemmingen hebt, moet u een nieuwe Azure SQL Database of Managed Instance-server maken op dezelfde locatie van een virtueel netwerk dat is verbonden met uw on-premises netwerk.  Op deze manier u uw Azure-SSIS IR maken met behulp van de nieuwe Azure SQL Database of Managed Instance-server en dat virtuele netwerk samenvoegen, allemaal op dezelfde locatie, waardoor gegevensbewegingen op verschillende locaties effectief worden geminimaliseerd.
- Als de locatie van uw bestaande Azure SQL Database of Managed Instance-server waar SSISDB wordt gehost niet dezelfde is als de locatie van een virtueel netwerk dat is verbonden met uw on-premises netwerk, maakt u eerst uw Azure-SSIS IR met behulp van een bestaande Azure SQL Database of Beheerde instantieserver en lid worden van een ander virtueel netwerk op dezelfde locatie en configureer vervolgens een virtueel netwerk naar virtuele netwerkverbinding tussen verschillende locaties.

Het volgende diagram toont de locatie-instellingen van Data Factory en het aantal keren dat de integratie wordt uitgevoerd:

![Locatie van Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Bepalen welke IR u moet gebruiken

### <a name="copy-activity"></a>Kopieeractiviteit

Voor de kopieeractiviteit, zijn de gekoppelde bron- en sinkservices vereist voor het definiëren van de richting van de gegevensstroom. De volgende logica wordt gebruikt om te bepalen welk exemplaar van Integration Runtime wordt gebruikt voor het uitvoeren van de kopieeractiviteit: 

- **Kopiëren tussen twee gegevensbronnen in de cloud: **: wanneer zowel de gekoppelde bronservices als de sinkservices Azure IR gebruiken, gebruikt ADF de regionale Azure IR die u hebt opgegeven of wordt automatisch een locatie van Azure bepaald als u ervoor kiest de IR voor automatisch oplossen (standaardwaarde) te gebruiken, zoals beschreven in de sectie [Locatie van Integration Runtime](#integration-runtime-location).
- **Kopiëren tussen een gegevensbron in de cloud en een gegevensbron in een privénetwerk**: als de gekoppelde bron- of sinkservice verwijst naar een zelf-hostende IR, wordt de kopieerbewerking uitgevoerd op die zelf-hostende Integration Runtime.
- **Kopiëren tussen twee gegevensbronnen in het privénetwerk:** zowel de bron- als sink Linked Service moet naar hetzelfde exemplaar van de runtime van integratie wijzen en dat de runtime van integratie wordt gebruikt om de kopieeractiviteit uit te voeren.

### <a name="lookup-and-getmetadata-activity"></a>Activiteit Lookup en GetMetadata

De activiteit Lookup en GetMetadata wordt uitgevoerd voor de integratieruntime die is gekoppeld aan de service die aan de gegevensopslag is gekoppeld.

### <a name="external-transformation-activity"></a>Externe transformatieactiviteit

Elke externe transformatieactiviteit die gebruik maakt van een externe compute engine heeft een doel compute Linked Service, wat wijst op een integratieruntijd. Deze instantie voor de in-integratieruntime bepaalt de locatie waar die externe met de hand gecodeerde transformatieactiviteit wordt verzonden.

### <a name="data-flow-activity"></a>Activiteit gegevensstroom

Dataflow-activiteiten worden uitgevoerd op de Azure-integratieruntime die eraan is gekoppeld. De Spark-compute die wordt gebruikt door gegevensstromen wordt bepaald door de gegevensstroomeigenschappen in uw Azure Integration Runtime en wordt volledig beheerd door ADF.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Runtime voor Azure-integratie maken](create-azure-integration-runtime.md)
- [Runtime voor zelfgehoste integratie maken](create-self-hosted-integration-runtime.md)
- [Een Azure-SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelfstudie uitgebreid en worden instructies gegeven over het gebruik van Azure SQL Database Managed Instance en het samenvoegen van de IR bij een virtueel netwerk. 
