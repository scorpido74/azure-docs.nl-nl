---
title: Inleiding tot ML-services op Azure HDInsight
description: Meer informatie over het gebruik van ML Services op HDInsight om toepassingen te maken voor big data-analyse.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 04/03/2020
ms.openlocfilehash: 5bf405840de54c4e2399ee73e723201acca9e6bc
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657039"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Wat is ML Services in Azure HDInsight

Microsoft Machine Learning Server is beschikbaar als implementatieoptie wanneer u HDInsight-clusters maakt in Azure. Het clustertype dat deze optie biedt, heet **ML Services.** Deze mogelijkheid biedt on-demand toegang tot aanpasbare, gedistribueerde analysemethoden op HDInsight.

ML Services op HDInsight biedt de nieuwste mogelijkheden voor R-gebaseerde analyses op datasets van vrijwel elke omvang. De gegevenssets kunnen worden geladen op Azure Blob- of Data Lake-opslag. Uw R-gebaseerde applicaties kunnen de 8000+ open-source R-pakketten gebruiken. De routines in ScaleR, Microsoft's big data analytics pakket zijn ook beschikbaar.

Het randknooppunt biedt een handige plek om verbinding te maken met het cluster en uw R-scripts uit te voeren. Met het randknooppunt kunnen de parallelverdeelde gedistribueerde functies van ScaleR over de kernen van de server worden uitgevoerd. U ze ook uitvoeren over de knooppunten van het cluster met behulp van ScaleR's Hadoop Map Reduce. U ook Apache Spark-compute-contexten gebruiken.

De modellen of voorspellingen die het resultaat zijn van analyse kunnen worden gedownload voor on-premises gebruik. Ze kunnen ook elders in Azure worden geoperationaliseerd. In het bijzonder via [Azure Machine Learning Studio (klassiek)](https://studio.azureml.net)en [webservice](../../machine-learning/studio/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Aan de slag met ML Services op HDInsight

Als u een ML Services-cluster in HDInsight wilt maken, selecteert u het clustertype **ML Services.** Het clustertype ML Services bevat ML Server op de gegevensknooppunten en randknooppunt. Het randknooppunt dient als een landingszone voor ml-servicesgebaseerde analyses. Zie [Apache Hadoop-clusters maken met behulp van de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) voor een walkthrough over het maken van het cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Waarom kiezen voor ML Services in HDInsight?

ML Services in HDInsight biedt de volgende voordelen:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI-innovatie van Microsoft en open-source

  ML Services bevat zeer aanpasbare, gedistribueerde reeks algoritmen zoals [RevoscaleR,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler) [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)en [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package). Deze algoritmen kunnen werken op gegevensformaten die groter zijn dan de grootte van fysiek geheugen. Ze draaien ook op een breed scala van platforms op een gedistribueerde manier. Meer informatie over de verzameling aangepaste [R-pakketten](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) en [Python-pakketten](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) van Microsoft die bij het product zijn inbegrepen.
  
  ML Services overbrugt deze Microsoft-innovaties en bijdragen afkomstig van de open-source community (R-, Python- en AI-toolkits). Dit alles bovenop één enterprise-grade platform. Elk R- of Python-open-source machine learning-pakket kan naast elke eigen innovatie van Microsoft werken.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Eenvoudige, veilige en grootschalige operationalisering en administratie

  Ondernemingen die vertrouwen op traditionele paradigma's en omgevingen investeren veel tijd en moeite in de richting van operationalisering. Deze actie leidt tot te hoge kosten en vertragingen, waaronder de vertaaltijd voor: modellen, iteraties om ze geldig en actueel te houden, wettelijke goedkeuring en beheer van machtigingen.

  ML Services biedt [operationalisatie van](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)bedrijfskwaliteit. Nadat een machine learning-model is voltooid, duurt het slechts een paar klikken om API's voor webservices te genereren. Deze [webservices](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) worden gehost op een serverraster in de cloud en kunnen worden geïntegreerd met line-of-business toepassingen. Met de mogelijkheid om te implementeren op een elastisch raster u naadloos schalen met de behoeften van uw bedrijf, zowel voor batch- als realtime scores. Zie ML [Services op HDInsight operationiseren](r-server-operationalize.md)voor instructies.

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
| R-enabled | [R-pakketten](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) voor oplossingen die in R zijn geschreven, met een open-source distributie van R, en run-time infrastructuur voor scriptuitvoering. |
| Python-ingeschakeld | [Python-modules](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) voor oplossingen die zijn geschreven in Python, met een open-source distributie van Python en run-time infrastructuur voor scriptuitvoering.
| [Vooraf getrainde modellen](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Voor visuele analyse en tekstsentimentanalyse, klaar om gegevens te scoren die u verstrekt. |
| [Implementeren en consumeren](r-server-operationalize.md) | Operationaliseer uw server en implementeer oplossingen als een webservice. |
| [Uitvoering op afstand](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Start externe sessies op het ML Services-cluster op uw netwerk vanaf uw clientwerkstation. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opties voor gegevensopslag voor ML Services op HDInsight

Standaardopslag voor het HDFS-bestandssysteem kan een Azure Storage-account of Azure Data Lake Storage zijn. Geüploade gegevens naar clusteropslag tijdens de analyse worden persistent gemaakt. De gegevens zijn beschikbaar, zelfs nadat het cluster is verwijderd. Verschillende tools kunnen de gegevensoverdracht naar opslag verwerken. De tools omvatten de portal-gebaseerde upload faciliteit van de opslag account en de [AzCopy](../../storage/common/storage-use-azcopy.md) nut.

U toegang inschakelen tot extra Blob- en Data lake-winkels tijdens het maken van het cluster. U wordt niet beperkt door de primaire opslagoptie die wordt gebruikt.  Zie [Azure Storage-opties voor ML Services op HDInsight-artikel](./r-server-storage.md) voor meer informatie over het gebruik van meerdere opslagaccounts.

U [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) ook gebruiken als opslagoptie voor gebruik op het randknooppunt. Azure Files maakt bestandsshares die in Azure Storage zijn gemaakt, mogelijk voor het Linux-bestandssysteem. Zie [Azure Storage-opties voor ML Services op HDInsight voor](r-server-storage.md)meer informatie.

## <a name="access-ml-services-edge-node"></a>Toegangs voor het randknooppunt van ML Services

U verbinding maken met Microsoft ML Server op het randknooppunt via een browser of SSH/PuTTY. De R-console is standaard geïnstalleerd tijdens het maken van het cluster.  

## <a name="develop-and-run-r-scripts"></a>R-scripts ontwikkelen en uitvoeren

Uw R-scripts kunnen een van de meer dan 8000 open-source R-pakketten gebruiken. U ook de parallelle en gedistribueerde routines uit de ScaleR-bibliotheek gebruiken. Scripts worden uitgevoerd op het randknooppunt dat binnen de R-tolk op dat knooppunt wordt uitgevoerd. Behalve stappen die ScaleR-functies aanroepen met een Map Reduce (RxHadoopMR) of Spark (RxSpark) rekencontext. De functies worden gedistribueerd uitgevoerd over de gegevensknooppunten die aan de gegevens zijn gekoppeld. Zie [Basiscontextopties voor ML Services op HDInsight voor](r-server-compute-contexts.md)meer informatie over contextopties.

## <a name="operationalize-a-model"></a>Een model operationeel maken

Wanneer uw gegevensmodellering is voltooid, u het model operationeel maken om voorspellingen te doen voor nieuwe gegevens, hetzij vanuit Azure, hetzij on-premises. Dit proces staat bekend als scoren. Scoren kan in HDInsight, Azure Machine Learning of on-premises.

### <a name="score-in-hdinsight"></a>Score in HDInsight

Om te scoren in HDInsight, schrijf een R-functie. De functie roept uw model aan om voorspellingen te doen voor een nieuw gegevensbestand dat u op uw opslagaccount hebt geladen. Sla vervolgens de voorspellingen terug naar het opslagaccount. U deze routine on-demand uitvoeren op het randknooppunt van uw cluster of met behulp van een geplande taak.

### <a name="score-in-azure-machine-learning-aml"></a>Score in Azure Machine Learning (AML)

Als u wilt scoren met Azure Machine Learning, gebruikt u het open-source Azure Machine Learning R-pakket, [AzureML,](https://cran.r-project.org/src/contrib/Archive/AzureML/) om uw model te publiceren als een Azure-webservice. Voor het gemak is dit pakket vooraf geïnstalleerd op het randknooppunt. Gebruik vervolgens de faciliteiten in Azure Machine Learning om een gebruikersinterface voor de webservice te maken en bel de webservice indien nodig voor het scoren. Converteer vervolgens ScaleR-modelobjecten naar gelijkwaardige open-sourcemodelobjecten voor gebruik met de webservice. Gebruik ScaleR-dwangfuncties, `as.randomForest()` zoals voor ensemblemodellen, voor deze conversie.

### <a name="score-on-premises"></a>Score on-premises

On-premises scoren na het maken van uw model: serialiseren van het model in R, downloaden, de-serialiseren en vervolgens gebruiken voor het scoren van nieuwe gegevens. U nieuwe gegevens scoren met behulp van de aanpak die eerder in [Score in HDInsight](#score-in-hdinsight) is beschreven of met behulp van [webservices.](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)

## <a name="maintain-the-cluster"></a>Het cluster onderhouden

### <a name="install-and-maintain-r-packages"></a>R-pakketten installeren en onderhouden

De meeste R-pakketten die u gebruikt, zijn vereist op het randknooppunt, omdat de meeste stappen van uw R-scripts daar worden uitgevoerd. Als u extra R-pakketten op het randknooppunt wilt installeren, u de `install.packages()` methode in R gebruiken.

Als u alleen scaler-bibliotheekroutines gebruikt, hebt u meestal geen extra R-pakketten nodig. Mogelijk hebt u extra pakketten nodig voor **de uitvoering van rxExec** of **RxDataStep** op de gegevensknooppunten.

De extra pakketten kunnen worden geïnstalleerd met een scriptactie nadat u het cluster hebt gemaakt. Zie [ML Services beheren in het hdinsight-cluster voor](r-server-hdinsight-manage.md)meer informatie.

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Apache Hadoopmap wijzigenVerlaag geheugeninstellingen

Het beschikbare geheugen voor ML Services kan worden gewijzigd wanneer een mapreduce-taak wordt uitgevoerd. Als u een cluster wilt wijzigen, gebruikt u de Gebruikersinterface van Apache Ambari voor uw cluster. Zie [HDInsight-clusters beheren met behulp van de Ambari Web UI](../hdinsight-hadoop-manage-ambari.md)voor gebruikersinterface van Ambari.

Het beschikbare geheugen voor ML Services kan worden gewijzigd door Hadoop-switches te gebruiken in de oproep naar **RxHadoopMR:**

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Uw cluster schalen

Een bestaand ML Services-cluster op HDInsight kan via de portal worden opgeschaald of omlaag. Door op te schalen, krijgt u extra capaciteit voor grotere verwerkingstaken. U een cluster terugschalen als het niet actief is. Zie CLUSTERS beheren van [HDInsight](../hdinsight-administer-use-portal-linux.md)voor instructies over het schalen van een cluster.

### <a name="maintain-the-system"></a>Het systeem onderhouden

OS Onderhoud wordt gedaan op de onderliggende Linux VM's in een HDInsight cluster tijdens off-hours. Normaal gesproken wordt het onderhoud elke maandag en donderdag om 03:30 uur (lokale tijd van VM) uitgevoerd. Updates hebben geen invloed op meer dan een kwart van het cluster tegelijk.

Het uitvoeren van taken kan vertragen tijdens het onderhoud. Ze moeten echter nog steeds tot voltooiing worden uitgevoerd. Aangepaste software of lokale gegevens die u hebt, worden bewaard voor deze onderhoudsgebeurtenissen, tenzij er een catastrofale fout optreedt waarvoor een cluster moet worden hersteld.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-opties voor ML Services op HDInsight

Het Linux-randknooppunt van een HDInsight-cluster is de landingszone voor R-gebaseerde analyse. Recente versies van HDInsight bieden een browser-gebaseerde IDE van RStudio Server op de rand knooppunt. RStudio Server is productiever dan de R-console voor ontwikkeling en uitvoering.

Een desktop-IDE heeft toegang tot het cluster via een externe MapReduce- of Spark-compute-context. Opties zijn: Microsoft's [R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio, en Walware's Eclipse-gebaseerde [StatET](http://www.walware.de/goto/statet).

Toegang tot de R-console op het randknooppunt door **R** te typen bij de opdrachtprompt. Wanneer u de console-interface gebruikt, is het handig om R-script te ontwikkelen in een teksteditor. Knip en plak vervolgens delen van uw script naar behoefte in de R-console.

## <a name="pricing"></a>Prijzen

De prijzen die zijn gekoppeld aan een ML Services HDInsight-cluster zijn op dezelfde manier gestructureerd als andere HDInsight-clustertypen. Ze zijn gebaseerd op de grootte van de onderliggende VM's voor de naam, gegevens en randknooppunten. Core-uur uplifts ook. Zie [HDInsight-prijzen voor](https://azure.microsoft.com/pricing/details/hdinsight/)meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het gebruik van ML Services op HDInsight-clusters:

* [Een R-script uitvoeren op een ML Services-cluster in Azure HDInsight met RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Opties voor compute-context voor ML Services-cluster in HDInsight](r-server-compute-contexts.md)
* [Opslagopties voor het ML Services-cluster op HDInsight](r-server-storage.md)
