---
title: Inleiding tot ML-Services in azure HDInsight
description: Meer informatie over het gebruik van ML Services in HDInsight om toepassingen voor big data analyse te maken.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 1dd716a279f7a09e7d9152ee34ff5c7bdac201dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82188239"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Wat is ML van services in azure HDInsight

Microsoft Machine Learning Server is beschikbaar als implementatie optie wanneer u HDInsight-clusters maakt in Azure. Het cluster type dat deze optie biedt, wordt **ml Services**genoemd. Deze mogelijkheid biedt toegang op aanvraag voor aanpas bare, gedistribueerde analyse methoden op HDInsight.

ML Services op HDInsight biedt de nieuwste mogelijkheden voor R-gebaseerde analyses op gegevens sets van vrijwel elke grootte. De gegevens sets kunnen worden geladen in Azure Blob of Data Lake Storage. Uw R-toepassingen kunnen gebruikmaken van de 8000 + open source R-pakketten. De routines in schalen, het big data Analytics-pakket van micro soft zijn ook beschikbaar.

Het Edge-knoop punt biedt een handige plaats om verbinding te maken met het cluster en om uw R-scripts uit te voeren. Met het Edge-knoop punt kunt u de gesegmenteerde gesplitste gedistribueerde functies uitvoeren in de kernen van de-server. U kunt ze ook uitvoeren op de knoop punten van het cluster met behulp van de Hadoop-kaart reductie van de Scaler. U kunt ook Apache Spark Compute-contexten gebruiken.

De modellen of voor spellingen die het resultaat zijn van analyses, kunnen worden gedownload voor on-premises gebruik. Ze kunnen ook ergens `operationalized` anders in azure zijn. Met name via [Azure machine learning Studio (klassiek)](https://studio.azureml.net)en de [webservice](../../machine-learning/studio/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Aan de slag met ML Services in HDInsight

Als u een ML Services-cluster in HDInsight wilt maken, selecteert u het cluster type van de **milliliters Services** . Het cluster type van de MILLILITERs Services bevat ML Server op de gegevens knooppunten en het Edge-knoop punt. Het Edge-knoop punt fungeert als een aanvoer zone voor analyses op basis van MILLILITERs Services. Zie [Apache Hadoop clusters maken met behulp van de Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) voor een overzicht van het maken van het cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Waarom zou u in HDInsight MILLILITERs Services kiezen?

ML Services in HDInsight biedt de volgende voor delen:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI-innovatie van micro soft en open-source

  ML Services bevat uiterst aanpas bare, gedistribueerde set algoritmen, zoals [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)en [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package). Deze algoritmen kunnen worden gebruikt voor gegevens grootte die groter is dan de grootte van het fysieke geheugen. Ze kunnen ook op een gedistribueerde manier worden uitgevoerd op een groot aantal verschillende platformen. Meer informatie over de verzameling van de aangepaste [R-pakketten](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) en [Python-pakketten](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) van micro soft die zijn opgenomen in het product.
  
  ML-Services conbrugt deze micro soft-innovaties en-bijdragen die afkomstig zijn van de open-source community (R-, python-en AI-werkset). Alles op één platform voor bedrijfs kwaliteit. Een open-source machine learning-pakket van R of python kan naast eigen innovatie van micro soft worden gebruikt.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Eenvoudige, veilige en grootschalige uitoefening en beheer

  Ondernemingen die gebruikmaken van traditionele modellen en omgevingen investeren veel tijd en inspanningen op uitoefening. Deze actie resulteert in ingrijpende kosten en vertragingen, met inbegrip van de Vertaal tijd voor: modellen, iteraties om ze geldig te blijven en huidige, gereglementeerde goed keuring en beheer machtigingen.

  ML Services biedt [uitoefening](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)voor ondernemingen. Nadat een machine learning model is voltooid, kunt u met slechts enkele klikken Api's voor Web Services genereren. Deze [webservices](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) worden gehost op een server raster in de Cloud en kunnen worden geïntegreerd met line-of-business-toepassingen. Met de mogelijkheid om te implementeren in een elastisch raster kunt u naadloos schalen met de behoeften van uw bedrijf, zowel voor batch-als realtime-scores. Zie [operationeel maken ml Services in HDInsight](r-server-operationalize.md)voor instructies.

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Het cluster type van de ML Services op HDInsight wordt alleen ondersteund op HDInsight 3,6. HDInsight 3,6 is gepland om op 31 december 2020 te worden ingetrokken.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Belangrijkste functies van ML Services in HDInsight

De volgende functies zijn opgenomen in ML Services op HDInsight.

| Functie categorie | Beschrijving |
|------------------|-------------|
| R-ingeschakeld | [R-pakketten](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) voor oplossingen die zijn geschreven in R, met een open-source distributie van R en runtime-infra structuur voor het uitvoeren van scripts. |
| Python-ingeschakeld | [Python-modules](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) voor oplossingen die zijn geschreven in Python, met een open-source distributie van python en runtime-infra structuur voor het uitvoeren van scripts.
| [Vooraf getrainde modellen](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Voor visuele analyse en tekst sentiment analyse kunt u de gegevens die u opgeeft, gereed maken voor scores. |
| [Implementeren en gebruiken](r-server-operationalize.md) | `Operationalize`uw server en implementeer oplossingen als een webservice. |
| [Externe uitvoering](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Start externe sessies op het cluster van ML-Services op uw netwerk vanaf uw client werkstation. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opties voor gegevens opslag voor de MILLILITERs Services in HDInsight

De standaard opslag voor het HDFS-bestands systeem kan een Azure Storage account of Azure Data Lake Storage zijn. Het uploaden van gegevens naar de cluster opslag tijdens de analyse is permanent gemaakt. De gegevens zijn beschikbaar, zelfs nadat het cluster is verwijderd. Met verschillende hulpprogram ma's kan de gegevens overdracht naar opslag worden verwerkt. De hulpprogram ma's omvatten de op portal gebaseerde upload functie van het opslag account en het AzCopy-hulp programma.

U kunt toegang tot extra Blob-en data Lake-archieven inschakelen tijdens het maken van het cluster. U bent niet beperkt tot de primaire opslag optie die in gebruik is.  Zie [Azure Storage opties voor de services](./r-server-storage.md) van het HDInsight-artikel voor meer informatie over het gebruik van meerdere opslag accounts.

U kunt Azure Files ook gebruiken als een opslag optie voor gebruik op het Edge-knoop punt. Azure Files maakt bestands shares die zijn gemaakt in Azure Storage naar het Linux-bestands systeem. Zie [Azure Storage opties voor de services van ml op HDInsight](r-server-storage.md)voor meer informatie.

## <a name="access-ml-services-edge-node"></a>Edge-knoop punt voor toegang tot services

U kunt verbinding maken met micro soft ML Server op het Edge-knoop punt met behulp van een browser of SSH/PuTTy. De R-console wordt standaard geïnstalleerd tijdens het maken van het cluster.  

## <a name="develop-and-run-r-scripts"></a>R-scripts ontwikkelen en uitvoeren

Uw R-scripts kunnen elk van de i/o-bron R-pakketten van 8000 + gebruiken. U kunt ook de geevenwijdige en gedistribueerde routines uit de Scaleer-bibliotheek gebruiken. Scripts die worden uitgevoerd op het Edge-knoop punt worden uitgevoerd in de R-interpreter op dat knoop punt. Behalve voor stappen die Scaleer-functies aanroepen met een RxHadoopMR-of Spark (RxSpark)-Compute-context. De functies worden uitgevoerd op een gedistribueerde manier over de gegevens knooppunten die aan de gegevens zijn gekoppeld. Zie [Compute-context opties voor milliliter Services in HDInsight](r-server-compute-contexts.md)voor meer informatie over context opties.

## <a name="operationalize-a-model"></a>`Operationalize`een model

Wanneer uw gegevens modellering is voltooid `operationalize` , is het model voor het maken van voor spellingen voor nieuwe gegevens in azure of on-premises. Dit proces staat bekend als score. U kunt een score in HDInsight, Azure Machine Learning of on-premises uitvoeren.

### <a name="score-in-hdinsight"></a>Score in HDInsight

Als u een score wilt gebruiken in HDInsight, schrijft u een R-functie. De functie roept uw model op om voor spellingen te maken voor een nieuw gegevens bestand dat u hebt geladen in uw opslag account. Sla de voor spellingen vervolgens terug op naar het opslag account. U kunt deze routine op aanvraag uitvoeren op het Edge-knoop punt van uw cluster of met behulp van een geplande taak.

### <a name="score-in-azure-machine-learning-aml"></a>Score in Azure Machine Learning (AML)

Als u de score wilt gebruiken met behulp van Azure Machine Learning, gebruikt u het open source Azure Machine Learning R-pakket met de term [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) om uw model te publiceren als een Azure-webservice. Voor het gemak is dit pakket vooraf geïnstalleerd op het Edge-knoop punt. Gebruik vervolgens de functies in Azure Machine Learning om een gebruikers interface voor de webservice te maken en roep vervolgens de webservice aan die nodig is voor de score. Converteer Scaleer-model objecten vervolgens naar gelijkwaardige open-source model objecten voor gebruik met de webservice. Gebruik functies voor het afdwingen van schalen, `as.randomForest()` zoals voor op ensembles gebaseerde modellen, voor deze conversie.

### <a name="score-on-premises"></a>On-premises Score

Als u on-premises wilt beoordelen nadat u uw model hebt gemaakt, kunt u het model serialiseren in R, dit downloaden, deserialiseren en het vervolgens gebruiken voor het scoren van nieuwe gegevens. U kunt nieuwe gegevens beoordelen met behulp van de aanpak die eerder in de score in HDInsight is beschreven of met behulp van [webservices](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Het cluster onderhouden

### <a name="install-and-maintain-r-packages"></a>R-pakketten installeren en onderhouden

De meeste R-pakketten die u gebruikt, zijn vereist op het Edge-knoop punt, omdat de meeste stappen van uw R-scripts hier worden uitgevoerd. Als u extra R-pakketten wilt installeren op het Edge-knoop punt `install.packages()` , kunt u de methode gebruiken in R.

Als u alleen met Scaleer-bibliotheek routines werkt, hebt u doorgaans geen extra R-pakketten nodig. Mogelijk hebt u aanvullende pakketten nodig voor **rxExec** of **RxDataStep** uitvoering op de gegevens knooppunten.

De extra pakketten kunnen worden geïnstalleerd met een script actie nadat u het cluster hebt gemaakt. Zie voor meer informatie [beheer van milliliters Services in HDInsight-cluster](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Apache Hadoop MapReduce-geheugen instellingen wijzigen

Beschikbaar geheugen voor ML-Services kan worden gewijzigd wanneer er een MapReduce-taak wordt uitgevoerd. Als u een cluster wilt wijzigen, gebruikt u de Apache Ambari-gebruikers interface voor uw cluster. Zie [HDInsight-clusters beheren met de Ambari-webgebruikersinterface](../hdinsight-hadoop-manage-ambari.md)voor instructies voor Ambari-gebruikers interface.

Beschikbaar geheugen voor ML-Services kan worden gewijzigd met behulp van Hadoop-switches in de aanroep van **RxHadoopMR**:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Uw cluster schalen

Een bestaand ML Services-cluster in HDInsight kan omhoog of omlaag worden geschaald via de portal. Door omhoog te schalen krijgt u extra capaciteit voor grotere verwerkings taken. U kunt een cluster terugschalen wanneer deze niet actief is. Zie [HDInsight-clusters beheren](../hdinsight-administer-use-portal-linux.md)voor instructies over het schalen van een cluster.

### <a name="maintain-the-system"></a>Het systeem onderhouden

Het onderhoud van het besturings systeem wordt uitgevoerd op de onderliggende virtuele Linux-machines in een HDInsight-cluster gedurende buiten kantoor uren. Normaal gesp roken wordt onderhoud uitgevoerd om 3:30 uur (lokale tijd van de VM) elke maandag en donderdag. Updates zijn niet van invloed op meer dan een kwart van het cluster tegelijk.

Het uitvoeren van taken kan vertragen tijdens het onderhoud. Ze moeten echter wel worden uitgevoerd om te worden voltooid. Aangepaste software of lokale gegevens die u in deze onderhouds gebeurtenissen blijft bewaren, tenzij er een onherstelbare fout optreedt waarvoor een cluster opnieuw moet worden gebouwd.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-opties voor MILLILITERs Services in HDInsight

Het Linux Edge-knoop punt van een HDInsight-cluster is de aanvoer zone voor op R gebaseerde analyses. Recente versies van HDInsight bieden een op een browser gebaseerde IDE van RStudio-server op het Edge-knoop punt. RStudio server is productiever dan de R-console voor ontwikkeling en uitvoering.

Een Desktop-IDE kan toegang krijgen tot het cluster via een externe MapReduce of een Spark-Compute-context. De volgende opties zijn beschikbaar: micro soft [R tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio en Walware op basis van eclips.

Open de R-console op het Edge-knoop punt door **R** te typen bij de opdracht prompt. Als u de-console interface gebruikt, is het handig om R-script te ontwikkelen in een tekst editor. Knip en plak zo nodig secties van uw script in de R-console.

## <a name="pricing"></a>Prijzen

De prijzen die zijn gekoppeld aan een HDInsight-cluster van milliliters van ML, worden op dezelfde manier gestructureerd als andere HDInsight-cluster typen. Ze zijn gebaseerd op de grootte van de onderliggende Vm's over de naam, de gegevens en de Edge-knoop punten. De kern-uur-uptilen. Zie [prijzen voor HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van ML-Services op HDInsight-clusters:

* [Een R-script uitvoeren op een ML Services-cluster in azure HDInsight met behulp van de RStudio-server](machine-learning-services-quickstart-job-rstudio.md)
* [Opties voor compute-context voor ML Services-cluster in HDInsight](r-server-compute-contexts.md)
* [Opslag opties voor een cluster van ML Services in HDInsight](r-server-storage.md)
