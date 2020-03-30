---
title: 'Azure Data Factory: veelgestelde vragen '
description: Krijg antwoorden op veelgestelde vragen over Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 34972e70039fef17161bdef66f64278cbabf908f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130807"
---
# <a name="azure-data-factory-faq"></a>Veelgestelde vragen over Azure Data Factory
In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Wat is Azure Data Factory? 
Data Factory is een volledig beheerde, cloud-gebaseerde, data-integratie ETL-service die de beweging en transformatie van gegevens automatiseert. Net als een fabriek die apparatuur uitvoert om grondstoffen om te zetten in afgewerkte goederen, orkestreert Azure Data Factory bestaande services die ruwe gegevens verzamelen en omzetten in kant-en-klare informatie. 

Met Azure Data Factory u gegevensgestuurde werkstromen maken om gegevens te verplaatsen tussen on-premises en cloudgegevensopslag. En u gegevens verwerken en transformeren met datastromen. ADF ondersteunt ook externe compute engines voor handgecodeerde transformaties met behulp van compute services zoals Azure HDInsight, Azure Databricks en de SQL Server Integration Services (SSIS) integratieruntime. 

Met Data Factory u uw gegevensverwerking uitvoeren op een cloudservice op basis van Azure of in uw eigen zelfgehoste compute-omgeving, zoals SSIS, SQL Server of Oracle. Nadat u een pijplijn hebt gemaakt die de actie uitvoert die u nodig hebt, u deze plannen om periodiek (bijvoorbeeld per uur, dagelijks of wekelijks) uit te voeren, tijdvensterplanning of de pijplijn te activeren vanuit een gebeurtenisgebeurtenis. Zie voor meer informatie [Inleiding tot Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Controlestromen en schaal 
Om de diverse integratiestromen en -patronen in het moderne datawarehouse te ondersteunen, maakt Data Factory flexibele modellering van datapipelines mogelijk. Dit houdt volledige controlestroom programmering paradigma's, waaronder voorwaardelijke uitvoering, vertakking in data pipelines, en de mogelijkheid om expliciet parameters binnen en over deze stromen. De controlestroom omvat ook het transformeren van gegevens via activiteitsverzending naar externe uitvoeringsengines en gegevensstroommogelijkheden, waaronder gegevensverplaatsing op schaal, via de kopieeractiviteit.

Data Factory biedt de vrijheid om elke stroomstijl te modelleren die nodig is voor gegevensintegratie en die op aanvraag of herhaaldelijk volgens een planning kan worden verzonden. Een paar veelvoorkomende stromen die dit model mogelijk maakt, zijn:   

- Controlestromen:
    - Activiteiten kunnen worden aan elkaar geketend in een reeks binnen een pijplijn.
    - Activiteiten kunnen worden vertakt binnen een pijplijn.
    - Parameters:
        - Parameters kunnen worden gedefinieerd op pijplijnniveau en argumenten kunnen worden doorgegeven terwijl u de pijplijn op aanvraag of vanaf een trigger aanroept.
        - Activiteiten kunnen de argumenten die zijn doorgegeven aan de pijplijn gebruiken.
    - Aangepaste status passeren:
        - Activiteitsuitvoer, inclusief status, kan worden verbruikt door een volgende activiteit in de pijplijn.
    - Looping containers:
        - De activiteit voor elke activiteit zal herhalen over een bepaalde verzameling activiteiten in een lus. 
- Op trigger gebaseerde stromen:
    - Pijpleidingen kunnen worden geactiveerd op aanvraag of door muurkloktijd.
- Deltastromen:
    - Parameters kunnen worden gebruikt om uw hoogwatermarkering voor delta-kopie te definiëren terwijl ze dimensie- of referentietabellen verplaatsen vanuit een relationele winkel, on-premises of in de cloud, om de gegevens in het meer te laden. 

Zie [Zelfstudie: Controlestromen](tutorial-control-flow.md)voor meer informatie.

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Gegevens die op schaal worden getransformeerd met codevrije pijplijnen
De nieuwe browsergebaseerde tooling-ervaring biedt codevrije pijplijncreatie en -implementatie met een moderne, interactieve webgebaseerde ervaring.

Voor ontwikkelaars van visuele gegevens en gegevenstechnici is de Web UI van Data Factory de codevrije ontwerpomgeving die u gebruikt om pijplijnen te bouwen. Het is volledig geïntegreerd met Visual Studio Online Git en biedt integratie voor CI / CD en iteratieve ontwikkeling met debugging opties.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Rijke cross-platform SDKs voor gevorderde gebruikers
Data Factory V2 biedt een uitgebreide set SDK's die kunnen worden gebruikt voor het ontwerpen, beheren en bewaken van pijplijnen met behulp van uw favoriete IDE, waaronder:
* Python SDK
* PowerShell CLI
* C# SDK

Gebruikers kunnen ook gebruik maken van de gedocumenteerde REST API's om te communiceren met Data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iteratieve ontwikkeling en foutopsporing met behulp van visuele hulpmiddelen
De visuele hulpprogramma's van Azure Data Factory maken iteratieve ontwikkeling en foutopsporing mogelijk. U uw pijplijnen maken en testtests doen met behulp van de **foutopsporingsmogelijkheid** in het pijplijncanvas zonder één regel code te schrijven. U de resultaten van uw testruns bekijken in het **uitvoervenster** van uw pijplijncanvas. Nadat uw testrun is geslaagd, u meer activiteiten toevoegen aan uw pijplijn en doorgaan met het foutopsporing op een iteratieve manier. U uw testruns ook annuleren nadat ze aan de gang zijn. 

U hoeft uw wijzigingen in de gegevensfabriekservice niet te publiceren voordat **u Foutopsporing selecteert.** Dit is handig in scenario's waarin u ervoor wilt zorgen dat de nieuwe toevoegingen of wijzigingen werken zoals verwacht voordat u uw workflows in de gegevensfabriek bijwerkt in ontwikkelings-, test- of productieomgevingen. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Mogelijkheid om SSIS-pakketten te implementeren in Azure 
Als u uw SSIS-workloads wilt verplaatsen, u een gegevensfabriek maken en een runtime voor Azure-SSIS-integratie inrichten. Een runtime voor Azure-SSIS-integratie is een volledig beheerd cluster van Azure VM's (knooppunten) die zijn toegewezen aan het uitvoeren van uw SSIS-pakketten in de cloud. Zie de [SSIS-pakketten implementeren voor Azure](tutorial-create-azure-ssis-runtime-portal.md) voor stapsgewijze instructies. 
 
### <a name="sdks"></a>SDK's
Als u een gevorderde gebruiker bent en op zoek bent naar een programmatische interface, biedt Data Factory een uitgebreide set SDK's die u gebruiken om pijplijnen te schrijven, beheren of controleren met behulp van uw favoriete IDE. Taalondersteuning omvat .NET, PowerShell, Python en REST.

### <a name="monitoring"></a>Bewaking
U uw gegevensfabrieken controleren via PowerShell, SDK of de Visual Monitoring Tools in de gebruikersinterface van de browser. U on-demand, trigger-based en klokgestuurde aangepaste stromen op een efficiënte en effectieve manier monitoren en beheren. Bestaande taken annuleren, fouten in één oogopslag bekijken, inzoomen om gedetailleerde foutmeldingen te ontvangen en de problemen debuggen, allemaal vanuit één ruit zonder context te schakelen of heen en weer te navigeren tussen schermen. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nieuwe functies voor SSIS in datafabriek
Sinds de eerste publieke preview-release in 2017 heeft Data Factory de volgende functies voor SSIS toegevoegd:

-    Ondersteuning voor nog drie configuraties/varianten van Azure SQL Database om de SSIS-database (SSISDB) van projecten/pakketten te hosten:
-    SQL-database met eindpunten voor virtuele netwerkservice
-    Beheerd exemplaar
-    Elastische pool
-    Ondersteuning voor een virtueel azure resource-netwerk bovenop een klassiek virtueel netwerk dat in de toekomst moet worden afgeschaft, waarmee u de runtime van uw Azure-SSIS-integratie injecteren/aansluiten bij een virtueel netwerk dat is geconfigureerd voor SQL Database met eindpunten voor virtuele netwerkservices/MI/on-premises gegevenstoegang. Zie ook [Runtime voor Azure-SSIS-integratie deelnemen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md)voor meer informatie.
-    Ondersteuning voor Azure Active Directory -verificatie (Azure AD) en SQL-verificatie om verbinding te maken met de SSISDB, waardoor Azure AD-verificatie mogelijk is met uw door Gegevensfabriek beheerde identiteit voor Azure-bronnen
-    Ondersteuning voor het meenemen van uw eigen on-premises SQL Server-licentie om aanzienlijke kostenbesparingen te behalen met de Azure Hybrid Benefit-optie
-    Ondersteuning voor Enterprise Edition van de runtime azure-SSIS-integratie waarmee u geavanceerde/premium-functies, een aangepaste installatie-interface gebruiken om extra componenten/extensies en een partnerecosysteem te installeren. Zie ook Enterprise [Edition, Custom Setup en 3rd Party Extensibility for SSIS in ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/)voor meer informatie. 
-    Diepere integratie van SSIS in Data Factory waarmee u eersteklas SSIS-pakketactiviteiten uitvoeren in Data Factory-pijplijnen aanroepen/activeren en deze plannen via SSMS. Zie uw [ETL/ELT-workflows ook moderniseren en uitbreiden met SSIS-activiteiten in ADF-pijplijnen.](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)


## <a name="what-is-the-integration-runtime"></a>Wat is de inloop van de integratie?
De runtime voor integratie is de rekeninfrastructuur die Azure Data Factory gebruikt om de volgende gegevensintegratiemogelijkheden te bieden voor verschillende netwerkomgevingen:

- **Gegevensverplaatsing**: Voor gegevensverplaatsing verplaatst de runtime van de integratie de gegevens tussen de bron- en doelgegevensopslag, terwijl ondersteuning wordt geboden voor ingebouwde connectors, opmaakconversie, kolomtoewijzing en performante en schaalbare gegevensoverdracht.
- **Verzendactiviteiten:** Voor transformatie biedt de runtime van de integratie de mogelijkheid om SSIS-pakketten native uit te voeren.
- **SSIS-pakketten uitvoeren:** de integratieruntime voert ssis-pakketten native uit in een beheerde Azure-compute-omgeving. De uitvoeringstijd voor integratie ondersteunt ook het verzenden en bewaken van transformatieactiviteiten die worden uitgevoerd op verschillende compute-services, zoals Azure HDInsight, Azure Machine Learning, SQL Database en SQL Server.

U een of meerdere exemplaren van de implementatieruntime voor integratie implementeren als nodig is om gegevens te verplaatsen en te transformeren. De runtime van de integratie kan worden uitgevoerd op een openbaar Azure-netwerk of op een privénetwerk (on-premises, Azure Virtual Network of Amazon Web Services virtual private cloud [VPC]). 

Zie voor meer informatie [Integration Runtime in Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Wat is de limiet voor het aantal ingebruiklooptijden van de integratie?
Er is geen harde limiet voor het aantal integratieruntime-exemplaren dat u in een gegevensfabriek hebben. Er is echter een limiet op het aantal VM-cores dat de integratieruntime per abonnement kan gebruiken voor SSIS-pakketuitvoering. Zie [Limieten voor gegevensfabrieken](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)voor meer informatie.

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Wat zijn de topconcepten van Azure Data Factory?
Een Azure-abonnement kan een of meer Azure Data Factory-exemplaren (oftewel 'data factory's') hebben. Azure Data Factory bevat vier belangrijke componenten die samenwerken als een platform waarop u gegevensgestuurde werkstromen samenstellen met stappen om gegevens te verplaatsen en te transformeren.

### <a name="pipelines"></a>Pijplijnen
Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn is een logische groepering van activiteiten om een werkeenheid uit te voeren. De activiteiten in een pijplijn voeren samen een taak uit. Een pijplijn kan bijvoorbeeld een groep activiteiten bevatten die gegevens van een Azure-blob inneemt en vervolgens een Hive-query uitvoeren op een HDInsight-cluster om de gegevens te partitioneren. Het voordeel is dat u een pijplijn gebruiken om de activiteiten als een set te beheren in plaats van elke activiteit afzonderlijk te moeten beheren. U de activiteiten in een pijplijn aan elkaar ketenen om ze opeenvolgend te bedienen, of u ze parallel onafhankelijk bedienen.

### <a name="data-flows"></a>Gegevensstromen
Gegevensstromen zijn objecten die u visueel bouwt in Data Factory en die gegevens op schaal transformeren op backend Spark-services. U hoeft de programmering of spark-internals niet te begrijpen. Ontwerp gewoon uw intentie voor gegevenstransformatie met grafieken (Mapping) of spreadsheets (Wrangling).

### <a name="activities"></a>Activiteiten
Activiteiten vertegenwoordigen een verwerkingsstap in een pijplijn. U bijvoorbeeld een activiteit Kopiëren gebruiken om gegevens van het ene gegevensarchief naar een ander gegevensarchief te kopiëren. Op dezelfde manier u een Hive-activiteit gebruiken, waarbij een Hive-query wordt uitgevoerd op een Azure HDInsight-cluster om uw gegevens te transformeren of te analyseren. Data Factory ondersteunt drie soorten activiteiten: activiteiten voor gegevensverplaatsing, activiteiten voor gegevenstransformatie en controleactiviteiten.

### <a name="datasets"></a>Gegevenssets
Gegevenssets vertegenwoordigen gegevensstructuren in de gegevensarchieven die simpelweg verwijzen naar de gegevens die u in uw activiteiten als in- of uitvoer wilt gebruiken. 

### <a name="linked-services"></a>Gekoppelde services
Gekoppelde services zijn te vergelijken met verbindingsreeksen, die de verbindingsinformatie bevatten die Data Factory nodig heeft om verbinding te maken met externe bronnen. Denk aan het op deze manier: Een gekoppelde service definieert de verbinding met de gegevensbron, en een gegevensset vertegenwoordigt de structuur van de gegevens. Een gekoppelde Azure Storage-service geeft bijvoorbeeld de verbindingstekenreeks op om verbinding te maken met het Azure Storage-account. En een Azure blob-gegevensset geeft de blobcontainer en de map op die de gegevens bevat.

Gekoppelde services hebben twee doeleinden in Data Factory:

- Een *gegevensarchief* weergeven dat een on-premises SQL Server-instantie, een Oracle-database-exemplaar, een bestandsshare of een Azure Blob-opslagaccount bevat, maar niet beperkt is. Zie Activiteit kopiëren in Azure [Data Factory](copy-activity-overview.md)voor een lijst met ondersteunde gegevensarchieven.
- Een *rekenbron* weergeven die de uitvoering van een activiteit kan hosten. De HDInsight Hive-activiteit wordt bijvoorbeeld uitgevoerd op een HDInsight Hadoop-cluster. Zie [Gegevens transformeren in Azure Data Factory](transform-data.md)voor een lijst met transformatieactiviteiten en ondersteunde compute-omgevingen.

### <a name="triggers"></a>Triggers
Triggers vertegenwoordigen verwerkingseenheden die bepalen wanneer een pijplijnuitvoering wordt afgetrapt. Er zijn verschillende soorten triggers voor verschillende soorten gebeurtenissen. 

### <a name="pipeline-runs"></a>Pijplijnuitvoeringen
Een pijplijnrun is een instantie van een pijplijnuitvoering. U maakt meestal een pijplijn die wordt uitgevoerd door argumenten door te geven aan de parameters die in de pijplijn zijn gedefinieerd. U de argumenten handmatig of binnen de triggerdefinitie doorgeven.

### <a name="parameters"></a>Parameters
Parameters zijn sleutelwaardeparen in een alleen-lezen configuratie.U definieert parameters in een pijplijn en u geeft de argumenten voor de gedefinieerde parameters door tijdens de uitvoering vanuit een uitvoeringscontext. De runcontext wordt gemaakt door een trigger of van een pijplijn die u handmatig uitvoert. Activiteiten binnen de pijplijn gebruiken de parameterwaarden.

Een gegevensset is een sterk getypte parameter en een entiteit die u hergebruiken of verwijzen. Een activiteit kan verwijzen naar gegevenssets en de eigenschappen die zijn gedefinieerd in de gegevenssetdefinitie.

Een gekoppelde service is ook een sterk getypte parameter die verbindingsgegevens bevat voor een gegevensarchief of een compute-omgeving. Het is ook een entiteit die u hergebruiken of verwijzen.

### <a name="control-flows"></a>Controlestromen
Beheer stromen die pijplijnactiviteiten orkestreren die ketenactiviteiten in een reeks omvatten, vertakkingen, parameters die u definieert op pijplijnniveau en argumenten die u passeert terwijl u de pijplijn op aanvraag of vanuit een trigger aanroept. Controlestromen omvatten ook aangepaste status passeren en lussen containers (dat wil zeggen, foreach iterators).


Zie de volgende artikelen voor meer informatie over Data Factory-concepten:

- [Gegevenssets en gekoppelde services](concepts-datasets-linked-services.md)
- [Pijplijnen en activiteiten](concepts-pipelines-activities.md)
- [Integration Runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Wat is het prijsmodel voor Data Factory?
Zie [Prijsgegevens van Gegevensfabriek](https://azure.microsoft.com/pricing/details/data-factory/)voor prijsgegevens van Azure Data Factory .

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hoe kan ik op de hoogte blijven van informatie over Data Factory?
Ga naar de volgende sites voor de meest recente informatie over Azure Data Factory:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Startpagina documentatie](/azure/data-factory)
- [Startpagina van product](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Technische diepe duik 

### <a name="how-can-i-schedule-a-pipeline"></a>Hoe kan ik een pijplijn plannen? 
U de trigger of de trigger van het tijdvenster gebruiken om een pijplijn te plannen. De trigger maakt gebruik van een kalenderschema voor de muur, dat pijplijnen periodiek of in op agenda gebaseerde terugkerende patronen kan plannen (bijvoorbeeld op maandag om 18:00 uur en donderdag om 21:00 uur). Zie [Pijplijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md) voor meer informatie.

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Kan ik parameters doorgeven aan een pijplijnrun?
Ja, parameters zijn een eersteklas concept op het hoogste niveau in Data Factory. U parameters op pijplijnniveau definiëren en argumenten doorgeven terwijl u de pijplijn uitvoert op aanvraag of met behulp van een trigger.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Kan ik standaardwaarden voor de pijplijnparameters definiëren? 
Ja. U standaardwaarden definiëren voor de parameters in de pijplijnen. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Kan een activiteit in een pijplijn argumenten consumeren die worden doorgegeven aan een pijplijnrun? 
Ja. Elke activiteit in de pijplijn kan de parameterwaarde verbruiken die `@parameter` aan de pijplijn wordt doorgegeven en met de constructie wordt uitgevoerd. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Kan een eigenschap voor activiteitsuitvoer worden verbruikt in een andere activiteit? 
Ja. Een activiteitsuitvoer kan worden verbruikt in `@activity` een volgende activiteit met de constructie.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hoe ga ik op een elegante manier om met null-waarden in een activiteitsuitvoer? 
U `@coalesce` de constructie in de expressies gebruiken om null-waarden op een elegante manier te verwerken. 

## <a name="mapping-data-flows"></a>Toewijzing gegevensstromen

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Ik heb hulp nodig bij het oplossen van problemen met mijn logica voor gegevensstromen. Welke informatie moet ik verstrekken om hulp te krijgen?

Wanneer Microsoft hulp biedt of het oplossen van problemen met gegevensstromen biedt, dient u het Data Flow Script op te geven. Dit is het code-behind script uit uw gegevensstroomgrafiek. Open in de ADF-gebruikersinterface uw gegevensstroom en klik vervolgens op de knop 'Script' in de rechterbovenhoek. Kopieer en plak dit script of sla het op in een tekstbestand.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Hoe krijg ik toegang tot gegevens met de andere 90 gegevenssettypen in Data Factory?

Met de toewijzingsgegevensstroomfunctie kunnen Azure SQL Database, Azure SQL Data Warehouse, afgebakende tekstbestanden van Azure Blob-opslag of Azure Data Lake Storage Gen2 en Parketbestanden van Blob-opslag of Data Lake Storage Gen2 native worden opgeslagen voor bron en gootsteen. 

Gebruik de activiteit Kopiëren om gegevens van een van de andere connectors te fasen en voer vervolgens een activiteit Gegevensstroom uit om gegevens te transformeren nadat deze zijn geënsceneerd. Uw pijplijn wordt bijvoorbeeld eerst gekopieerd naar Blob-opslag en vervolgens gebruikt een gegevensstroomactiviteit een gegevensset in de bron om die gegevens om te zetten.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Is de self-hosted integration runtime beschikbaar voor datastromen?

Zelf gehoste IR is een ADF-pijplijnconstructie die u gebruiken met de kopieeractiviteit om gegevens te verzamelen of te verplaatsen van en naar op basis van on-prem- of VM-gebaseerde gegevensbronnen en sinks. Werk de gegevens eerst in fasen met een kopie, vervolgens gegevensstroom voor transformatie en vervolgens een volgende kopie als u die getransformeerde gegevens terug moet verplaatsen naar het opslagarchief voor de prem.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>Bedient de compute engine voor gegevensstromen meerdere tenants?
Clusters worden nooit gedeeld. Wij garanderen isolatie voor elke taak die wordt uitgevoerd in productieruns. In het geval van debug scenario een persoon krijgt een cluster, en alle debugs zal gaan naar dat cluster die worden geïnitieerd door die gebruiker.

## <a name="wrangling-data-flows"></a>Getouwtrek gegevensstromen

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Wat zijn de ondersteunde regio's voor de datastroom van getouwtrek?

De gegevensstroom van het getouwtrek wordt momenteel ondersteund in gegevensfabrieken die in volgende regio's worden gemaakt:

* Australië - oost
* Canada - midden
* India - centraal
* VS - oost
* VS - oost 2
* Japan - oost
* Europa - noord
* Azië - zuidoost
* VS - zuid-centraal
* Verenigd Koninkrijk Zuid
* VS - west-centraal
* Europa -west
* VS - west
* VS - west 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Wat zijn de beperkingen en beperkingen met getouwtrek data stroom?

Gegevenssetnamen kunnen alleen alfanumerieke tekens bevatten. De volgende gegevensopslag wordt ondersteund:

* DelimitedText-gegevensset in Azure Blob Storage met verificatie van accountsleutels
* DelimitedText-gegevensset in Azure Data Lake Storage gen2 met behulp van accountsleutel of serviceprincipal-verificatie
* DelimitedText-gegevensset in Azure Data Lake Storage gen1 met serviceprincipal-verificatie
* Azure SQL Database en Data Warehouse met sql-verificatie. Zie hieronder ondersteunde SQL-typen. Er is geen PolyBase of staging ondersteuning voor data warehouse.

Op dit moment wordt de linked service Key Vault-integratie niet ondersteund in getouwtrekgegevensstromen.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Wat is het verschil tussen het in kaart brengen en het in kaart brengen van datastromen?

Het toewijzen van gegevensstromen biedt een manier om gegevens op schaal te transformeren zonder dat er codering nodig is. U een taak voor gegevenstransformatie in het zoekom gegevensstromen ontwerpen door een reeks transformaties te maken. Begin met een willekeurig aantal brontransformaties, gevolgd door stappen voor gegevenstransformatie. Voltooi uw gegevensstroom met een gootsteen om uw resultaten in een bestemming te landen. Het in kaart brengen van gegevensstroom is geweldig in het in kaart brengen en transformeren van gegevens met zowel bekende als onbekende schema's in de putten en bronnen.

Met de gegevensstromen u agile gegevensvoorbereiding en -verkenning uitvoeren met behulp van de Power Query Online mashup-editor op schaal via vonkuitvoering. Met de opkomst van datalakes moet je soms alleen maar een dataset verkennen of een dataset in het meer maken. Je bent niet in kaart brengen aan een bekend doelwit. Getouwtrek gegevensstromen worden gebruikt voor minder formele en modelgebaseerde analysescenario's.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Wat is het verschil tussen Power Platform Dataflows en getouwtrek over datastromen?

Power Platform Dataflows stelt gebruikers in staat om gegevens uit een breed scala aan gegevensbronnen te importeren en om te zetten in de Common Data Service en Azure Data Lake om PowerApps-toepassingen, Power BI-rapporten of Flow-automatiseringen te bouwen. Power Platform Dataflows maken gebruik van de gevestigde Power Query-gegevensvoorbereidingservaringen, vergelijkbaar met Power BI en Excel. Power Platform Dataflows maken ook eenvoudig hergebruik binnen een organisatie mogelijk en verwerken automatisch orkestratie (bijvoorbeeld het automatisch vernieuwen van gegevensstromen die afhankelijk zijn van een andere dataflow wanneer de vorige wordt vernieuwd).

Azure Data Factory (ADF) is een managed data integration service waarmee data engineers en citizen data integrator complexe hybrid extract-transform-load (ETL) en extract-load-transform (ELT) workflows kunnen maken. De gegevensstroom in ADF stelt gebruikers in staat om een codevrije, serverloze omgeving te gebruiken die de voorbereiding van gegevens in de cloud vereenvoudigt en schaalt naar elke gegevensgrootte zonder dat er infrastructuurbeheer nodig is. Het maakt gebruik van de Power Query data voorbereidingtechnologie (ook gebruikt in Power Platform dataflows, Excel, Power BI) voor te bereiden en vorm te geven aan de gegevens. Gebouwd om alle complexiteiten en schaaluitdagingen van big data-integratie aan te kunnen, stellen de gegevensstromen van het getouwtrek gebruikers in staat om snel gegevens op schaal voor te bereiden via spark-uitvoering. Gebruikers kunnen veerkrachtige gegevenspijplijnen bouwen in een toegankelijke visuele omgeving met onze browserinterface en ADF de complexiteit van Spark-uitvoering laten afhandelen. Maak schema's voor uw pijplijnen en monitor uw gegevensstroomuitvoeringen vanuit de ADF-bewakingsportal. Beheer eenvoudig SLA's voor de beschikbaarheid van gegevens met adf's uitgebreide beschikbaarheidsbewaking en waarschuwingen en maak gebruik van ingebouwde mogelijkheden voor continue integratie en implementatie om uw stromen in een beheerde omgeving op te slaan en te beheren. Stel waarschuwingen op en bekijk uitvoeringsplannen om te valideren dat uw logica presteert zoals gepland terwijl u uw gegevensstromen afstemt.

### <a name="supported-sql-types"></a>Ondersteunde SQL-typen

Wrangling data flow ondersteunt de volgende gegevenstypen in SQL. U krijgt een validatiefout voor het gebruik van een gegevenstype dat niet wordt ondersteund.

* Korte
* double
* real
* float
* Char
* Nchar
* varchar
* nvarchar
* geheel getal
* int
* bit
* booleaans
* smallint
* tinyint
* bigint
* long
* tekst
* date
* datum/tijd
* datetime2
* smalldatetijd
* tijdstempel
* uniqueidentifier
* xml

Andere gegevenstypen zullen in de toekomst worden ondersteund.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende zelfstudies voor stapsgewijze instructies voor het maken van een gegevensfabriek:

- [Snelstart: een gegevensfabriek maken](quickstart-create-data-factory-dot-net.md)
- [Zelfstudie: Gegevens kopiëren in de cloud](tutorial-copy-data-dot-net.md)
