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
ms.date: 01/28/2020
ms.openlocfilehash: 194bc7983019a616d534a4146f86fff59f9719dc
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990518"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime in Azure Data Factory
De Integratie Runtime (IR) is de rekeninfrastructuur die Azure Data Factory gebruikt om de volgende mogelijkheden voor gegevensintegratie in verschillende netwerkomgevingen te bieden:

- **Gegevens stroom**: een [gegevens stroom](concepts-data-flow-overview.md) uitvoeren in een beheerde Azure Compute-omgeving.  
- **Gegevens verplaatsing**: gegevens kopiëren tussen gegevens archieven in openbaar netwerk en gegevens archieven in een particulier netwerk (on-premises of een virtueel particulier netwerk). Deze optie biedt ondersteuning voor ingebouwde connectors, indelingsconversie, kolomtoewijzing en hoogwaardige en schaalbare gegevensoverdracht.
- **Activiteit verzending**: trans formatie-activiteiten voor verzen ding verzenden en bewaken die worden uitgevoerd op diverse reken Services, zoals Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server en meer.
- **SSIS-pakketuitvoering**: systeemeigen SSIS-pakketten (SQL Server Integration Services) uitvoeren in een beheerde Azure-rekenomgeving.

In de Data Factory definieert een activiteit de actie die moet worden uitgevoerd. Een gekoppelde service definieert een doelgegevensarchief of een rekenservice. Een Integration Runtime vormt de brug tussen de activiteit en de gekoppelde services.  Er wordt naar verwezen door de gekoppelde service of activiteit, en biedt de reken omgeving waar de activiteit wordt uitgevoerd of verzonden. Op deze manier kan de activiteit optimaal worden uitgevoerd in de regio die het dichtst mogelijk bij het doelgegevensarchief of de rekenservice ligt, terwijl wordt voldaan aan vereisten rondom beveiliging en naleving.

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

- Gegevens stromen uitvoeren in azure 
- Het uitvoeren van kopieeractiviteit tussen gegevensarchieven in de cloud
- Het verzenden van de volgende trans formatie-activiteiten in het open bare netwerk: Databricks notebook/jar/python activity, HDInsight component activity, HDInsight varken activiteit, HDInsight MapReduce activity, HDInsight Spark-activiteit, HDInsight Streaming Machine Learning Machine Learning-activiteit, Data Lake Analytics U-SQL-activiteit, aangepaste .NET-activiteit, webactiviteit, opzoek activiteit en activiteiten voor meta gegevens ophalen.

### <a name="azure-ir-network-environment"></a>Azure IR-netwerkomgeving
Azure Integration Runtime ondersteunt het maken van verbinding met gegevens archieven en Compute-Services met open bare eind punten. Gebruik een zelf-hostende Integration Runtime voor een Azure Virtual Network-omgeving.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR-rekenresource en -schalen
Azure Integration Runtime biedt een volledig beheerde, serverloze rekenresource in Azure.  U hoeft zich geen zorgen te maken over het inrichten van de infrastructuur, de software-installatie, patchen of capaciteitsschaling.  Bovendien betaalt u alleen voor het werkelijke gebruik.

Azure Integration Runtime biedt de systeemeigen rekenkracht om gegevens te verplaatsen tussen gegevensarchieven in de cloud op een veilige, betrouwbare en krachtige manier.  U kunt instellen hoeveel eenheden voor gegevensintegratie worden gebruikt in de kopieeractiviteit. De rekenkracht van de Azure IR wordt flexibel opgeschaald om aan uw behoeften te voldoen, zonder dat u de grootte van de Azure Integration Runtime expliciet hoeft aan te passen. 

Verzending van de activiteit is een lichte bewerking om de activiteit naar de doelrekenservice te routeren. U hoeft de rekenkracht niet op te schalen voor dit scenario.

Zie voor informatie over het maken en configureren van een Azure-IR 'Azure IR maken en configureren' bij de gidsen. 

> [!NOTE] 
> Azure Integration runtime heeft eigenschappen die betrekking hebben op Data flow runtime, waarmee de onderliggende Compute-infra structuur wordt gedefinieerd die wordt gebruikt om de gegevens stromen uit te voeren. 

## <a name="self-hosted-integration-runtime"></a>Zelf-hostende integratieruntime
Een zelf-hostende IR is geschikt voor:

- Het uitvoeren van kopieeractiviteit tussen een gegevensarchief in de cloud en een gegevensarchief in een privénetwerk.
- Het verzenden van de volgende trans formatie-activiteiten voor reken resources in on-premises of Azure Virtual Network: HDInsight Hive-activiteit (BYOC uw eigen cluster), HDInsight Pig-activiteit (BYOC), HDInsight MapReduce activity (BYOC), HDInsight Spark activiteit (BYOC), HDInsight streaming activity (BYOC), Machine Learning-activiteit voor batch uitvoering, Machine Learning update van resource activiteiten, opgeslagen procedure activiteit, Data Lake Analytics U-SQL-activiteit, aangepaste activiteit (uitgevoerd op Azure Batch), opzoeken activiteit en ophalen van meta gegevens.

> [!NOTE] 
> Gebruik zelf-hostende Integration runtime voor de ondersteuning van gegevens archieven waarvoor u een eigen stuur programma nodig hebt, zoals SAP Hana, MySQL, enzovoort.  Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor meer informatie.

> [!NOTE] 
> Java Runtime Environment (JRE) is een afhankelijkheid van zelf-Hostende IR. Zorg ervoor dat JRE is geïnstalleerd op dezelfde host.

### <a name="self-hosted-ir-network-environment"></a>Zelf-hostende Azure IR-netwerkomgeving
Als u gegevensintegratie veilig wilt uitvoeren in een privénetwerkomgeving zonder rechtstreekse zichtbaarheid vanuit de openbare cloudomgeving, kunt u een zelf-hostende on-premises IR installeren achter de firewall van het bedrijf of in een virtueel privénetwerk.  De zelf-hostende Integration Runtime maakt alleen uitgaande HTTP-gebaseerde verbindingen met het openbare internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Zelf-hostende IR-rekenresource en -schalen
Zelf-hostende IR moet worden geïnstalleerd op een on-premises computer of op een virtuele machine in een privénetwerk. Op dit moment ondersteunen we alleen zelf-hostende IR op een Windows-besturingssysteem.  

Voor hoge beschikbaarheid en schaalbaarheid kunt u de zelf-hostende IR uitbreiden door het logische exemplaar te koppelen aan meerdere on-premises computers in de modus actief-actief.  Zie voor meer informatie het [maken en configureren van een zelf-hostend IR](create-self-hosted-integration-runtime.md) -artikel onder How to guides for details.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime
Als u de bestaande SSIS-werkbelasting wilt opheffen of verplaatsen, kunt u een Azure-SSIS IR maken voor het uitvoeren van systeemeigen SSIS-pakketten.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR-netwerkomgeving
Azure-SSIS IR kan worden ingericht in een openbaar netwerk of privénetwerk.  Toegang tot on-premises gegevens wordt ondersteund door Azure SSIS IR te koppelen aan een virtueel netwerk dat is verbonden met uw on-premises netwerk.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR-rekenresource en -schalen
Azure-SSIS IR is een volledig beheerd cluster met virtuele Azure-machines die uw SSIS-pakketten uitvoeren. U kunt uw eigen Azure SQL Database of Managed instance server de catalogus van SSIS-projecten/-pakketten (SSISDB) die eraan worden gekoppeld, hosten. U kunt de rekenkracht opschalen door de grootte van het knooppunt op te geven en opschalen door het aantal knooppunten in het cluster aan te geven. U kunt de kosten van het uitvoeren van de uw Azure-SSIS Integration Runtime beheren door naar wens te stoppen en starten.

Zie voor meer informatie het artikel 'Azure-SSIS IR maken en configureren' bij de gidsen.  Wanneer u de Azure SSIS IR hebt gemaakt, kunt u uw bestaande SSIS-pakketten implementeren en beheren met weinig of geen wijzigingen met behulp van bekende hulpprogramma's zoals SQL Server Data Tools (SSDT) en SQL Server Management Studio (SSMS), net als bij on-premises gebruik van SSIS.

Zie de volgende artikelen voor meer informatie over Azure-SSIS Runtime: 

- [Zelfstudie: SSIS-pakketten implementeren in Azure](tutorial-create-azure-ssis-runtime-portal.md). Dit artikel biedt stapsgewijze instructies voor het maken van een Azure-SSIS IR en maakt gebruik van een Azure SQL-database voor het hosten van de SSIS-catalogus. 
- [Procedure: Een Azure SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelf studie uitgebreid en vindt u instructies voor het gebruik van Azure SQL Database beheerde instantie en het toevoegen van de IR aan een virtueel netwerk. 
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over een Azure-SSIS IR ophaalt. Daarnaast bevat het artikel beschrijvingen van statuswaarden die worden gebruikt in de geretourneerde informatie. 
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel leest u hoe u een Azure-SSIS IR stopt, start of verwijdert. Er wordt ook uitgelegd hoe u een Azure-SSIS IR kunt uitschalen door meer knooppunten toe te voegen aan de IR. 
- [Een Azure-SSIS-integratieruntime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat algemene informatie over het toevoegen van een Azure-SSIS IR aan een virtueel netwerk van Azure. Er wordt ook beschreven hoe u Azure Portal gebruikt om een virtueel netwerk te configureren voor het deelnemen van Azure-SSIS IR aan het virtueel netwerk. 

## <a name="integration-runtime-location"></a>Locatie van Integration Runtime
De Data Factory-locatie is waar de metagegevens van de data factory worden opgeslagen en waar van het activeren van de pijplijn wordt gestart. Ondertussen heeft een data factory wel toegang tot gegevensarchieven en Compute Services in andere Azure-regio’s om gegevens te verplaatsen tussen gegevensarchieven of om gegevens te verwerken middels Compute Services. Dit gedrag wordt gerealiseerd via de [IR die algemeen beschikbaar](https://azure.microsoft.com/global-infrastructure/services/) is om de gegevensnaleving, efficiëntie en verminderde kosten voor uitgaand netwerkverkeer te realiseren.

De locatie van de IR definieert de locatie van de back-end rekenkracht en in wezen de locatie waar de verplaatsing van gegevens, het verzenden van activiteit en de uitvoering van het SSIS-pakket worden uitgevoerd. De locatie van de IR kan afwijken van de locatie van de data factory waar hij bij hoort. 

### <a name="azure-ir-location"></a>Locatie van Azure IR
U kunt een bepaalde locatie van een Azure IR instellen, in welk geval de gegevensverplaatsing of verzendactiviteit in die specifieke regio plaatsvindt. 

Als u ervoor kiest om de **Azure IR automatisch oplossen** te gebruiken dat de standaard is, 

- Voor kopieeractiviteit probeert ADF om automatisch uw sink- en brongegevensopslag te detecteren om de beste locatie te vinden in dezelfde regio, indien beschikbaar, of in de dichtstbijzijnde regio in dezelfde geografie. Indien niet detecteerbaar, wordt de regio van de data factory gebruikt.

- Voor het uitvoeren van opzoek-GetMetadata/het verwijderen van de activiteit (ook wel pijplijn activiteiten genoemd), het verzenden van trans formatie-activiteiten (ook wel externe activiteiten genoemd) en het maken van bewerkingen (verbinding testen, bladeren in mappen lijst en tabel lijst, preview-gegevens), wordt de IR in de data factory regio gebruikt.

- Voor de gegevens stroom maakt ADF gebruik van de IR in de regio data factory. 

  > [!TIP] 
  > Het is een goed idee om ervoor te zorgen dat de gegevens stroom wordt uitgevoerd in dezelfde regio als de bijbehorende gegevens archieven (indien mogelijk). U kunt dit doen door Azure IR automatisch op te lossen (als de locatie van de gegevens opslag hetzelfde is als Data Factory locatie) of door een nieuw Azure IR exemplaar te maken in dezelfde regio als uw gegevens archieven en vervolgens de gegevens stroom op het bestand uit te voeren. 

U kunt controleren welke IR-locatie van kracht wordt tijdens het uitvoeren van activiteiten in de weergave voor het controleren van de pijplijnactiviteit in de gebruikersinterface of nettolading voor het controleren van activiteiten.

>[!TIP]
>Als u strikte gegevensnalevingsvereisten hebt en u ervoor moet zorgen moet dat gegevens een bepaalde geografie niet verlaten, kunt u expliciet een Azure IR maken in een bepaalde regio en de gekoppelde service naar deze IR laten wijzen met behulp van de eigenschap ConnectVia. Als u bijvoorbeeld gegevens uit Blob in UK - zuid naar SQL DW in UK - zuid wilt kopiëren en u ervoor wilt zorgen dat de gegevens het UK niet verlaten, maakt u een Azure IR en koppelt u beide gekoppelde services aan deze IR.

### <a name="self-hosted-ir-location"></a>Locatie zelf-hostende IR
De zelf-hostende IR is logisch geregistreerd bij de Data Factory en de rekenkracht die wordt gebruikt ter ondersteuning van de functionaliteiten die u hebt opgegeven. Er bestaat daarom geen expliciete locatie-eigenschap voor de zelf-hostende IR. 

Wanneer de zelf-hostende IR wordt gebruikt voor het uitvoeren van de gegevensverplaatsing, haalt deze gegevens uit de bron en schrijft naar de bestemming.

### <a name="azure-ssis-ir-location"></a>Locatie Azure-SSIS IR
Het selecteren van de juiste locatie voor uw Azure-SSIS IR is essentieel voor het bereiken van hoge prestaties in uw ETL-werkstromen (extract-transform-load).

- De locatie van uw Azure-SSIS IR hoeft niet hetzelfde te zijn als de locatie van uw data factory, maar moet hetzelfde zijn als de locatie van uw eigen Azure SQL Database of Managed instance server waar SSISDB moet worden gehost. Op deze manier heeft uw Azure-SSIS Integration Runtime eenvoudig toegang tot SSISDB, zonder overmatig verkeer tussen verschillende locaties.
- Als u geen bestaande Azure SQL Database of Managed instance server hebt om SSISDB te hosten, maar u beschikt over on-premises gegevens bronnen/-bestemmingen, moet u een nieuwe Azure SQL Database of beheerde exemplaar server maken op dezelfde locatie als een virtueel netwerk dat is verbonden met uw on-premises netwerk.  Op deze manier kunt u uw Azure-SSIS IR maken met behulp van de nieuwe Azure SQL Database of Managed instance server en dat virtuele netwerk samen voegen, allemaal op dezelfde locatie, waardoor gegevens verkeer tussen verschillende locaties effectief wordt geminimaliseerd.
- Als de locatie van uw bestaande Azure SQL Database of Managed instance server waar SSISDB wordt gehost niet hetzelfde is als de locatie van een virtueel netwerk dat is verbonden met uw on-premises netwerk, maakt u uw Azure-SSIS IR eerst met een bestaande Azure SQL Database of Beheerde exemplaar server en toevoegen van een ander virtueel netwerk op dezelfde locatie en vervolgens configureert u een virtueel netwerk naar een virtueel netwerk verbinding tussen verschillende locaties.

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

### <a name="external-transformation-activity"></a>Externe trans formatie-activiteit

Elke externe transformatie activiteit die gebruikmaakt van een externe Compute-engine heeft een gekoppelde doel compute-service, die verwijst naar een Integration runtime. Dit exemplaar van Integration runtime bepaalt de locatie waar de externe hand code van de transformatie activiteit wordt verzonden.

### <a name="data-flow-activity"></a>Activiteit gegevens stroom

Gegevens stroom activiteiten worden uitgevoerd op de Azure Integration runtime die eraan is gekoppeld. De mousserende Compute die wordt gebruikt door gegevens stromen, wordt bepaald door de eigenschappen van de gegevens stroom in uw Azure Integration Runtime en worden volledig beheerd door ADF.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Azure Integration runtime maken](create-azure-integration-runtime.md)
- [Zelf-hostende integratie-runtime maken](create-self-hosted-integration-runtime.md)
- [Een Azure-SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelf studie uitgebreid en vindt u instructies voor het gebruik van Azure SQL Database beheerde instantie en het toevoegen van de IR aan een virtueel netwerk. 
