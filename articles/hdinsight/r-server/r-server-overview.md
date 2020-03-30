---
title: Inleiding tot ML-services op Azure HDInsight
description: Meer informatie over het gebruik van ML Services op HDInsight om toepassingen te maken voor big data-analyse.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 02/10/2020
ms.openlocfilehash: a77771880da962298f6e80782e5f3e251f5f4641
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77122368"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Wat is ML Services in Azure HDInsight

Microsoft Machine Learning Server is beschikbaar als implementatieoptie wanneer u HDInsight-clusters maakt in Azure. Het clustertype dat deze optie biedt, heet **ML Services.** Deze mogelijkheid biedt data scientists, statistici en R-programmeurs on-demand toegang tot schaalbare, gedistribueerde analysemethoden op HDInsight.

ML Services op HDInsight biedt de nieuwste mogelijkheden voor R-gebaseerde analyses op gegevenssets van vrijwel elke grootte, geladen op Azure Blob- of Data Lake-opslag. Aangezien het ML Services-cluster is gebouwd op open-source R, kunnen de R-gebaseerde toepassingen die u bouwt gebruikmaken van een van de meer dan 8000 open-source R-pakketten. De routines in ScaleR, Microsoft's big data analytics pakket zijn ook beschikbaar.

Het randknooppunt van een cluster biedt een handige plek om verbinding te maken met het cluster en uw R-scripts uit te voeren. Met een randknooppunt hebt u de mogelijkheid om de parallelle gedistribueerde functies van ScaleR over de kernen van de edge node-server uit te voeren. U ze ook uitvoeren op de knooppunten van het cluster met behulp van ScaleR's Hadoop Map Reduce of Apache Spark compute contexten.

De modellen of voorspellingen die het resultaat zijn van analyse kunnen worden gedownload voor on-premises gebruik. Ze kunnen ook elders in Azure worden geoperationaliseerd, met name via [Azure Machine Learning Studio (klassieke)](https://studio.azureml.net) [webservice.](../../machine-learning/studio/deploy-a-machine-learning-web-service.md)

## <a name="get-started-with-ml-services-on-hdinsight"></a>Aan de slag met ML Services op HDInsight

Als u een ML Services-cluster in Azure HDInsight wilt maken, selecteert u het clustertype **ML Services** bij het maken van een HDInsight-cluster met behulp van de Azure-portal. Het clustertype ML Services bevat ML Server op de gegevensknooppunten van het cluster en op een randknooppunt, dat fungeert als een bestemmingszone voor ml-servicesgebaseerde analyses. Zie [Apache Hadoop-clusters maken met behulp van de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) voor een walkthrough over het maken van het cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Waarom kiezen voor ML Services in HDInsight?

ML Services in HDInsight biedt de volgende voordelen:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI-innovatie van Microsoft en open-source

  ML Services bevat zeer schaalbare, gedistribueerde reeks algoritmen zoals [RevoscaleR,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler) [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)en [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) die kunnen werken op gegevensformaten die groter zijn dan de grootte van fysiek geheugen, en op een breed scala aan platforms op een gedistribueerde manier kunnen worden uitgevoerd. Meer informatie over de verzameling aangepaste [R-pakketten](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) en [Python-pakketten](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) van Microsoft die bij het product zijn inbegrepen.
  
  ML Services overbrugt deze Microsoft-innovaties en bijdragen afkomstig van de open-source community (R-, Python- en AI-toolkits) allemaal bovenop één enterprise-grade platform. Elk R- of Python-open-source machine learning-pakket kan naast elke eigen innovatie van Microsoft werken.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Eenvoudige, veilige en grootschalige operationalisering en administratie

  Ondernemingen die vertrouwen op traditionele paradigma's en omgevingen investeren veel tijd en moeite in de richting van operationalisering. Dit resulteert in te hoge kosten en vertragingen, waaronder de vertaaltijd voor modellen, iteraties om ze geldig en actueel te houden, wettelijke goedkeuring en het beheren van machtigingen door operationalisering.

  ML Services biedt [operationalisering van](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)bedrijfskwaliteit, omdat het slechts een paar klikken nodig heeft om API's voor webservices te genereren nadat een machine learning-model is voltooid. Deze [webservices](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) worden gehost op een serverraster in de cloud en kunnen worden geïntegreerd met line-of-business toepassingen. Met de mogelijkheid om te implementeren op een elastisch raster u naadloos schalen met de behoeften van uw bedrijf, zowel voor batch- als realtime scores. Zie ML [Services op HDInsight operationiseren](r-server-operationalize.md)voor instructies.

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Het clustertype ML Services op HDInsight wordt alleen ondersteund op HDInsight 3.6. HDInsight 3.6 gaat op 31 december 2020 met pensioen.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Belangrijkste functies van ML Services op HDInsight

De volgende functies zijn opgenomen in ML Services op HDInsight.

| Functiecategorie | Beschrijving |
|------------------|-------------|
| R-enabled | [R-pakketten](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) voor oplossingen die in R zijn geschreven, met een open source-distributie van R en run-time-infrastructuur voor scriptuitvoering. |
| Python-ingeschakeld | [Python-modules](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) voor oplossingen die zijn geschreven in Python, met een open source-distributie van Python en run-time-infrastructuur voor het uitvoeren van scripts.
| [Vooraf getrainde modellen](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Voor visuele analyse en tekstsentimentanalyse, klaar om gegevens te scoren die u verstrekt. |
| [Implementeren en consumeren](r-server-operationalize.md) | Operationaliseer uw server en implementeer oplossingen als een webservice. |
| [Uitvoering op afstand](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Start externe sessies op het ML Services-cluster op uw netwerk vanaf uw clientwerkstation. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opties voor gegevensopslag voor ML Services op HDInsight

Standaardopslag voor het HDFS-bestandssysteem van HDInsight-clusters kan worden gekoppeld aan een Azure Storage-account of een Azure Data Lake Storage. Deze koppeling zorgt ervoor dat alle gegevens die tijdens de analyse naar de clusteropslag worden geüpload, blijvend worden gemaakt en dat de gegevens beschikbaar zijn, zelfs nadat het cluster is verwijderd. Er zijn verschillende tools voor het verwerken van de gegevensoverdracht naar de opslagoptie die u selecteert, waaronder de portal-gebaseerde uploadfaciliteit van het opslagaccount en het [AzCopy-hulpprogramma.](../../storage/common/storage-use-azcopy.md)

U hebt de mogelijkheid om toegang in te schakelen tot extra Blob- en Data lake-winkels tijdens het clusterinrichtingsproces, ongeacht de primaire opslagoptie die wordt gebruikt.  Zie [Azure Storage-opties voor ML Services op HDInsight-artikel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) voor meer informatie over het gebruik van meerdere opslagaccounts.

U [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) ook gebruiken als opslagoptie voor gebruik op het randknooppunt. Met Azure Files u een bestandsshare die in Azure Storage is gemaakt, monteren op het Linux-bestandssysteem. Zie [Azure Storage-opties voor ML Services op HDInsight-cluster voor](r-server-storage.md)meer informatie over deze opties voor gegevensopslag voor ML-services op HDInsight.

## <a name="access-ml-services-edge-node"></a>Toegangs voor het randknooppunt van ML Services

U verbinding maken met Microsoft ML Server op het randknooppunt met behulp van een browser. Het wordt standaard geïnstalleerd tijdens het maken van het cluster.  U ook verbinding maken met het clusterrandknooppunt vanaf de opdrachtregel met Behulp van SSH/PuTTY om toegang te krijgen tot de R-console.

## <a name="develop-and-run-r-scripts"></a>R-scripts ontwikkelen en uitvoeren

De R-scripts die u maakt en uitvoert, kunnen een van de 8000+ open-source R-pakketten gebruiken naast de parallelle en gedistribueerde routines die beschikbaar zijn in de ScaleR-bibliotheek. In het algemeen wordt een script dat wordt uitgevoerd met ML Services op het randknooppunt uitgevoerd binnen de R-interpreter op dat knooppunt. De uitzonderingen zijn de stappen die een ScaleR-functie moeten aanroepen met een rekencontext die is ingesteld op Hadoop Map Reduce (RxHadoopMR) of Spark (RxSpark). In dit geval wordt de functie op een gedistribueerde manier uitgevoerd over de gegevens (taak)knooppunten van het cluster die zijn gekoppeld aan de gegevens waarnaar wordt verwezen. Zie [Opties voor compute context voor ML Services op HDInsight voor](r-server-compute-contexts.md)meer informatie over de verschillende opties voor compute context.

## <a name="operationalize-a-model"></a>Een model operationeel maken

Wanneer uw gegevensmodellering is voltooid, u het model operationeel maken om voorspellingen te doen voor nieuwe gegevens, hetzij vanuit Azure, hetzij on-premises. Dit proces staat bekend als scoren. Scoren kan in HDInsight, Azure Machine Learning of on-premises.

### <a name="score-in-hdinsight"></a>Score in HDInsight

Als u wilt scoren in HDInsight, schrijft u een R-functie die uw model aanroept om voorspellingen te doen voor een nieuw gegevensbestand dat u op uw opslagaccount hebt geladen. Sla vervolgens de voorspellingen terug naar het opslagaccount. U deze routine on-demand uitvoeren op het randknooppunt van uw cluster of met behulp van een geplande taak.

### <a name="score-in-azure-machine-learning-aml"></a>Score in Azure Machine Learning (AML)

Als u wilt scoren met Azure Machine Learning, gebruikt u het open-source Azure Machine Learning R-pakket, [AzureML,](https://cran.r-project.org/src/contrib/Archive/AzureML/) om uw model te publiceren als een Azure-webservice. Voor het gemak is dit pakket vooraf geïnstalleerd op het randknooppunt. Gebruik vervolgens de faciliteiten in Azure Machine Learning om een gebruikersinterface voor de webservice te maken en bel de webservice indien nodig voor het scoren.

Als u deze optie kiest, moet u alle scaler-modelobjecten converteren naar gelijkwaardige open-sourcemodelobjecten voor gebruik met de webservice. Gebruik ScaleR-dwangfuncties, `as.randomForest()` zoals voor ensemblemodellen, voor deze conversie.

### <a name="score-on-premises"></a>Score on-premises

Als u on-premises wilt scoren nadat u uw model hebt gemaakt, u het model in R serialiseren, downloaden, deserialiseren en vervolgens gebruiken voor het scoren van nieuwe gegevens. U nieuwe gegevens scoren met behulp van de aanpak die eerder in [Score in HDInsight](#score-in-hdinsight) is beschreven of met behulp van [webservices.](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)

## <a name="maintain-the-cluster"></a>Het cluster onderhouden

### <a name="install-and-maintain-r-packages"></a>R-pakketten installeren en onderhouden

De meeste R-pakketten die u gebruikt, zijn vereist op het randknooppunt, omdat de meeste stappen van uw R-scripts daar worden uitgevoerd. Als u extra R-pakketten op het randknooppunt wilt installeren, u de `install.packages()` methode in R gebruiken.

Als u alleen routines van de ScaleR-bibliotheek in het cluster gebruikt, hoeft u meestal geen extra R-pakketten op de gegevensknooppunten te installeren. Het is echter mogelijk dat u aanvullende pakketten nodig hebt om het gebruik van **rxExec-** of **RxDataStep-uitvoering** op de gegevensknooppunten te ondersteunen.

In dergelijke gevallen kunnen de extra pakketten worden geïnstalleerd met een scriptactie nadat u het cluster hebt gemaakt. Zie [ML Services beheren in het hdinsight-cluster voor](r-server-hdinsight-manage.md)meer informatie.

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Apache Hadoopmap wijzigenVerlaag geheugeninstellingen

Een cluster kan worden gewijzigd om de hoeveelheid geheugen te wijzigen die beschikbaar is voor ML Services wanneer het een mapReduce-taak uitvoert. Als u een cluster wilt wijzigen, gebruikt u de Apache Ambari-gebruikersinterface die beschikbaar is via het Azure-portalblad voor uw cluster. Zie [HDInsight-clusters beheren met de Ambari Web UI](../hdinsight-hadoop-manage-ambari.md)voor instructies over hoe u toegang krijgen tot de Ambari-gebruikersinterface voor uw cluster.

Het is ook mogelijk om de hoeveelheid geheugen die beschikbaar is voor ML Services te wijzigen met behulp van Hadoop-switches in de oproep naar **RxHadoopMR** als volgt:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Uw cluster schalen

Een bestaand ML Services-cluster op HDInsight kan via de portal worden opgeschaald of omlaag. Door op te schalen, u de extra capaciteit krijgen die u nodig hebt voor grotere verwerkingstaken, of u een cluster terugschalen wanneer het niet actief is. Zie CLUSTERS beheren van [HDInsight](../hdinsight-administer-use-portal-linux.md)voor instructies over het schalen van een cluster.

### <a name="maintain-the-system"></a>Het systeem onderhouden

Onderhoud om OS-patches en andere updates toe te passen, wordt uitgevoerd op de onderliggende Linux VM's in een HDInsight-cluster tijdens off-hours. Normaal gesproken wordt het onderhoud elke maandag en donderdag om 03:30 uur (op basis van de lokale tijd voor de VM) uitgevoerd. Updates worden uitgevoerd op een zodanige wijze dat ze niet meer dan een kwart van het cluster tegelijk beïnvloeden.

Aangezien de hoofdknooppunten redundant zijn en niet alle gegevensknooppunten worden beïnvloed, kunnen alle taken die gedurende deze periode worden uitgevoerd, vertragen. Ze moeten echter nog steeds tot voltooiing worden uitgevoerd. Aangepaste software of lokale gegevens die u hebt, worden bewaard voor deze onderhoudsgebeurtenissen, tenzij er een catastrofale fout optreedt waarvoor een cluster moet worden hersteld.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-opties voor ML Services op HDInsight

Het Linux-randknooppunt van een HDInsight-cluster is de landingszone voor R-gebaseerde analyse. Recente versies van HDInsight bieden een standaardinstallatie van RStudio Server op het randknooppunt als een browsergebaseerde IDE. Het gebruik van RStudio Server als IDE voor de ontwikkeling en uitvoering van R-scripts kan aanzienlijk productiever zijn dan alleen het gebruik van de R-console.

Bovendien u een desktop-IDE installeren en deze gebruiken om toegang te krijgen tot het cluster door gebruik te maken van een externe MapReduce- of Spark-compute-context. Opties zijn onder andere Microsoft's [R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio en Walware's Eclipse-gebaseerde [StatET](http://www.walware.de/goto/statet).

Daarnaast heb je toegang tot de R-console op het randknooppunt door **R** te typen op de Linux-opdrachtprompt nadat je verbinding hebt gemaakt via SSH of PuTTY. Wanneer u de console-interface gebruikt, is het handig om een teksteditor voor R-scriptontwikkeling in een ander venster uit te voeren en secties van uw script naar behoefte in de R-console te knippen en te plakken.

## <a name="pricing"></a>Prijzen

De prijzen die zijn gekoppeld aan een ML Services HDInsight-cluster zijn op dezelfde manier gestructureerd als de prijzen voor andere HDInsight-clustertypen. Ze zijn gebaseerd op de grootte van de onderliggende VM's over de naam, gegevens en randknooppunten, met de toevoeging van een core-uur uplift. Zie [HDInsight-prijzen voor](https://azure.microsoft.com/pricing/details/hdinsight/)meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over het gebruik van ML Services op HDInsight-clusters:

* [Een R-script uitvoeren op een ML Services-cluster in Azure HDInsight met RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Opties voor compute-context voor ML Services-cluster in HDInsight](r-server-compute-contexts.md)
* [Opslagopties voor het ML Services-cluster op HDInsight](r-server-storage.md)
