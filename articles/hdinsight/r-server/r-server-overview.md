---
title: Inleiding tot ML-Services in azure HDInsight
description: Meer informatie over het gebruik van ML Services in HDInsight om toepassingen voor big data analyse te maken.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/12/2019
ms.openlocfilehash: bd9b9edde0f4c5e3bea6e31342298f2df5a2b5fe
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241865"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Wat is ML van services in azure HDInsight

Microsoft Machine Learning Server is beschikbaar als implementatie optie wanneer u HDInsight-clusters maakt in Azure. Het cluster type dat deze optie biedt, wordt **ml Services**genoemd. Deze mogelijkheid biedt gegevens wetenschappers, statistici en R-programmeurs met on-demand toegang tot schaal bare, gedistribueerde analyse methoden op HDInsight.

ML Services op HDInsight biedt de nieuwste mogelijkheden voor R-analyses op gegevens sets van vrijwel elke grootte, geladen in Azure Blob of Data Lake Storage. Omdat het cluster van ML Services is gebouwd op open-source R, kunnen de op R gebaseerde toepassingen die u bouwt, gebruikmaken van de i-R-pakketten van 8000 + open source. De routines in schalen, het big data Analytics-pakket van micro soft zijn ook beschikbaar.

Het Edge-knoop punt van een cluster biedt een handige plaats om verbinding te maken met het cluster en om uw R-scripts uit te voeren. Met een Edge-knoop punt hebt u de mogelijkheid om de geparallelleerde gedistribueerde functies van Scaleer uit te voeren op de kernen van de Edge-knooppunt server. U kunt ze ook uitvoeren op de knoop punten van het cluster met behulp van de Hadoop-toewijzing van de Schaalr of Apache Spark reken contexten.

De modellen of voor spellingen die het resultaat zijn van analyses, kunnen worden gedownload voor on-premises gebruik. Ze kunnen ook elders in Azure worden operationeel, met name door de [webservice](../../machine-learning/studio/publish-a-machine-learning-web-service.md)van [Azure machine learning Studio (klassiek)](https://studio.azureml.net) .

## <a name="get-started-with-ml-services-on-hdinsight"></a>Aan de slag met ML Services in HDInsight

Als u een ML Services-cluster in azure HDInsight wilt maken, selecteert u het cluster type van het **ml-Services** bij het maken van een HDInsight-cluster met behulp van de Azure Portal. Het cluster type van de MILLILITERs Services bevat ML Server op de gegevens knooppunten van het cluster en op een Edge-knoop punt, dat fungeert als een aanvoer zone voor analyses op basis van ML Services. Zie [Apache Hadoop clusters maken met behulp van de Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) voor een overzicht van het maken van het cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Waarom zou u in HDInsight MILLILITERs Services kiezen?

ML Services in HDInsight biedt de volgende voor delen:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI-innovatie van micro soft en open-source

  ML Services bevat uiterst schaal bare, gedistribueerde set algoritmen, zoals [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)en [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) die kunnen werken met gegevens grootten die groter zijn dan de grootte van het fysieke geheugen en die worden uitgevoerd op een groot aantal verschillende platformen in een gedistribueerde manier. Meer informatie over de verzameling van de aangepaste [R-pakketten](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) en [Python-pakketten](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) van micro soft die zijn opgenomen in het product.
  
  ML-Services conbrugt deze micro soft-innovaties en bijdragen die afkomstig zijn van de open-source community (R-, python-en AI-werkset), die allemaal op één platform op bedrijfs niveau zijn gebaseerd. Een open-source machine learning-pakket van R of python kan naast eigen innovatie van micro soft worden gebruikt.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Eenvoudige, veilige en grootschalige uitoefening en beheer

  Ondernemingen die gebruikmaken van traditionele modellen en omgevingen investeren veel tijd en inspanningen op uitoefening. Dit leidt tot ongestructureerde kosten en vertragingen, inclusief de omzettings tijd voor modellen, iteraties om ze geldig te blijven en huidige, gereglementeerde goed keuring en beheer machtigingen via uitoefening.

  ML Services biedt [uitoefening](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)voor ondernemingen, in dat geval nadat een machine learning model is voltooid, zijn er maar een paar klikken om Web Services-api's te genereren. Deze [webservices](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) worden gehost op een server raster in de Cloud en kunnen worden geïntegreerd met line-of-business-toepassingen. Met de mogelijkheid om te implementeren in een elastisch raster kunt u naadloos schalen met de behoeften van uw bedrijf, zowel voor batch-als realtime-scores. Zie [operationeel maken ml Services in HDInsight](r-server-operationalize.md)voor instructies.

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Belangrijkste functies van ML Services in HDInsight

De volgende functies zijn opgenomen in ML Services op HDInsight.

| Functie categorie | Beschrijving |
|------------------|-------------|
| R-ingeschakeld | [R-pakketten](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) voor oplossingen die zijn geschreven in R, met een open-source distributie van R en runtime-infra structuur voor het uitvoeren van scripts. |
| Python-ingeschakeld | [Python-modules](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) voor oplossingen die zijn geschreven in Python, met een open-source distributie van python en runtime-infra structuur voor het uitvoeren van scripts.
| [Vooraf getrainde modellen](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Voor visuele analyse en tekst sentiment analyse kunt u de gegevens die u opgeeft, gereed maken voor scores. |
| [Implementeren en gebruiken](r-server-operationalize.md) | Operationeel maken uw server en implementeer oplossingen als een webservice. |
| [Externe uitvoering](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Start externe sessies op het cluster van ML-Services op uw netwerk vanaf uw client werkstation. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opties voor gegevens opslag voor de MILLILITERs Services in HDInsight

Standaard opslag voor het HDFS-bestands systeem van HDInsight-clusters kan worden gekoppeld aan een Azure Storage-account of een Azure Data Lake Storage. Deze koppeling zorgt ervoor dat alle gegevens die tijdens de analyse naar de cluster opslag worden geüpload, permanent worden gemaakt en de gegevens beschikbaar zijn, zelfs nadat het cluster is verwijderd. Er zijn verschillende hulpprogram ma's voor het afhandelen van de gegevens overdracht naar de opslag optie die u selecteert, met inbegrip van de op portal gebaseerde upload functie van het opslag account en het [AzCopy](../../storage/common/storage-use-azcopy.md) -hulp programma.

U hebt de mogelijkheid om toegang tot extra Blob-en data Lake Stores in te scha kelen tijdens het cluster-inrichtings proces, ongeacht de primaire opslag optie die in gebruik is.  Zie [Azure Storage opties voor de services](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) van het HDInsight-artikel voor meer informatie over het gebruik van meerdere opslag accounts.

U kunt [Azure files](../../storage/files/storage-how-to-use-files-linux.md) ook gebruiken als een opslag optie voor gebruik op het Edge-knoop punt. Met Azure Files kunt u een bestands share die is gemaakt in Azure Storage koppelen aan het Linux-bestands systeem. Zie [Azure Storage opties voor de services in hdinsight](r-server-storage.md)voor meer informatie over deze opties voor gegevens opslag voor milliliters Services in hdinsight-clusters.

## <a name="access-ml-services-edge-node"></a>Edge-knoop punt voor toegang tot services

U kunt verbinding maken met micro soft ML Server op het Edge-knoop punt met behulp van een browser. Het wordt standaard geïnstalleerd tijdens het maken van het cluster.  U kunt ook verbinding maken met het knoop punt cluster Edge via de opdracht regel met behulp van SSH/PuTTy om toegang te krijgen tot de R-console.

## <a name="develop-and-run-r-scripts"></a>R-scripts ontwikkelen en uitvoeren

De R-scripts die u maakt en uitvoert, kunnen gebruikmaken van een van de 8000 + open source R-pakketten, naast de geparallelle en gedistribueerde routines die beschikbaar zijn in de Scaleer-bibliotheek. In het algemeen wordt een script dat wordt uitgevoerd met ML Services op het Edge-knoop punt uitgevoerd in de R-interpreter op dat knoop punt. De uitzonde ringen zijn de stappen die nodig zijn om een Scaleer-functie aan te roepen met een berekenings context die is ingesteld op Hadoop-toewijzings reductie (RxHadoopMR) of Spark (RxSpark). In dit geval wordt de functie in een gedistribueerde manier uitgevoerd voor de knoop punten van die gegevens (taak) van de cluster die zijn gekoppeld aan de gegevens waarnaar wordt verwezen. Voor meer informatie over de verschillende opties voor de compute-context, Zie [Compute-context opties voor ml Services in HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Een model operationeel maken

Als uw gegevens modellering is voltooid, kunt u het model operationeel maken om voor spellingen te doen voor nieuwe gegevens, hetzij vanuit Azure of on-premises. Dit proces staat bekend als score. U kunt een score in HDInsight, Azure Machine Learning of on-premises uitvoeren.

### <a name="score-in-hdinsight"></a>Score in HDInsight

Als u een score in HDInsight wilt gebruiken, schrijft u een R-functie die uw model aanroept voor het maken van voor spellingen voor een nieuw gegevens bestand dat u hebt geladen in uw opslag account. Sla de voor spellingen vervolgens terug op naar het opslag account. U kunt deze routine op aanvraag uitvoeren op het Edge-knoop punt van uw cluster of met behulp van een geplande taak.

### <a name="score-in-azure-machine-learning-aml"></a>Score in Azure Machine Learning (AML)

Als u de score wilt gebruiken met behulp van Azure Machine Learning, gebruikt u het open source Azure Machine Learning R-pakket met de term [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) om uw model te publiceren als een Azure-webservice. Voor het gemak is dit pakket vooraf geïnstalleerd op het Edge-knoop punt. Gebruik vervolgens de functies in Azure Machine Learning om een gebruikers interface voor de webservice te maken en roep vervolgens de webservice aan die nodig is voor de score.

Als u deze optie kiest, moet u alle Scaleer-model objecten converteren naar gelijkwaardige open-source model objecten voor gebruik met de webservice. Gebruik functies voor het afdwingen van schalen, zoals `as.randomForest()` voor op ensemble gebaseerde modellen, voor deze conversie.

### <a name="score-on-premises"></a>On-premises Score

Als u een on-premises score wilt gebruiken nadat u het model hebt gemaakt, kunt u het model serialiseren in R, dit bestand downloaden, deserialiseren en het vervolgens gebruiken voor het scoren van nieuwe gegevens. U kunt nieuwe gegevens beoordelen met behulp van de aanpak die eerder in de [Score in HDInsight](#score-in-hdinsight) is beschreven of met behulp van [webservices](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Het cluster onderhouden

### <a name="install-and-maintain-r-packages"></a>R-pakketten installeren en onderhouden

De meeste R-pakketten die u gebruikt, zijn vereist op het Edge-knoop punt, omdat de meeste stappen van uw R-scripts hier worden uitgevoerd. Als u extra R-pakketten wilt installeren op het Edge-knoop punt, kunt u de methode `install.packages()` gebruiken in R.

Als u alleen routines uit de Schaalset in het cluster gebruikt, hoeft u niet doorgaans extra R-pakketten te installeren op de gegevens knooppunten. Het is echter mogelijk dat u aanvullende pakketten nodig hebt om het gebruik van **rxExec** of **RxDataStep** -uitvoering op de gegevens knooppunten te ondersteunen.

In dergelijke gevallen kunnen de extra pakketten met een script actie worden geïnstalleerd nadat u het cluster hebt gemaakt. Zie voor meer informatie [beheer van milliliters Services in HDInsight-cluster](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Apache Hadoop MapReduce-geheugen instellingen wijzigen

Een cluster kan worden gewijzigd om de hoeveelheid geheugen die beschikbaar is voor MILLILITER services te wijzigen wanneer er een MapReduce-taak wordt uitgevoerd. Als u een cluster wilt wijzigen, gebruikt u de Apache Ambari-gebruikers interface die beschikbaar is via de Blade Azure Portal voor uw cluster. Zie [HDInsight-clusters beheren met de Ambari-webgebruikersinterface](../hdinsight-hadoop-manage-ambari.md)voor instructies over het verkrijgen van toegang tot de Ambari-gebruikers interface voor uw cluster.

Het is ook mogelijk om de hoeveelheid geheugen die beschikbaar is voor de MILLILITER services te wijzigen door gebruik te maken van Hadoop-switches in de aanroep van **RxHadoopMR** als volgt:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Een cluster schalen

Een bestaand ML Services-cluster in HDInsight kan omhoog of omlaag worden geschaald via de portal. Door omhoog te schalen, kunt u de extra capaciteit krijgen die u mogelijk nodig hebt voor grotere verwerkings taken, of u kunt een cluster terugschalen wanneer het niet actief is. Zie [HDInsight-clusters beheren](../hdinsight-administer-use-portal-linux.md)voor instructies over het schalen van een cluster.

### <a name="maintain-the-system"></a>Het systeem onderhouden

Onderhoud voor het Toep assen van besturingssysteem patches en andere updates wordt uitgevoerd op de onderliggende virtuele Linux-machines in een HDInsight-cluster tijdens buiten kantoor uren. Normaal gesp roken wordt onderhoud uitgevoerd om 3:30 uur (op basis van de lokale tijd voor de virtuele machine) elke maandag en donderdag. Updates worden uitgevoerd op een zodanige manier dat ze niet meer van invloed zijn op meer dan een kwart van het cluster tegelijk.

Omdat de hoofd knooppunten overbodig zijn en niet alle gegevens knooppunten worden beïnvloed, kunnen taken die tijdens deze tijd worden uitgevoerd, vertragen. Ze moeten echter wel worden uitgevoerd om te worden voltooid. Alle aangepaste software of lokale gegevens die u hebt, blijven behouden in deze onderhouds gebeurtenissen, tenzij er een onherstelbare fout optreedt waarvoor een cluster opnieuw moet worden gebouwd.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-opties voor MILLILITERs Services in HDInsight

Het Linux Edge-knoop punt van een HDInsight-cluster is de aanvoer zone voor op R gebaseerde analyses. Recente versies van HDInsight bieden een standaard installatie van de RStudio-server op het Edge-knoop punt als een op een browser gebaseerd IDE. Het gebruik van RStudio server als een IDE voor de ontwikkeling en uitvoering van R-scripts kan aanzienlijk productiever zijn dan alleen de R-console gebruiken.

Daarnaast kunt u een Desktop-IDE installeren en deze gebruiken om toegang te krijgen tot het cluster via een externe MapReduce of een Spark-Compute-context. Opties zijn onder andere micro soft [R tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), RStudio en Walware op basis van de op eclips gebaseerde [status](http://www.walware.de/goto/statet).

Daarnaast kunt u toegang krijgen tot de R-console op het Edge-knoop punt door **R** te typen bij de Linux-opdracht prompt nadat u verbinding hebt gemaakt via SSH of putty. Wanneer u de-console interface gebruikt, is het handig om een tekst editor uit te voeren voor het ontwikkelen van R-scripts in een ander venster, en de secties van uw script naar behoefte te knippen en te plakken in de R-console.

## <a name="pricing"></a>Prijzen

De prijzen die zijn gekoppeld aan een HDInsight-cluster van milliliters van ML, zijn vergelijkbaar met de prijzen voor andere HDInsight-cluster typen. Ze zijn gebaseerd op de grootte van de onderliggende Vm's over de naam, gegevens en Edge-knoop punten, met toevoeging van een die van het basis uur. Zie [prijzen voor HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over het gebruik van ML-Services in HDInsight-clusters:

* [Een R-script uitvoeren op een ML Services-cluster in azure HDInsight met behulp van de RStudio-server](machine-learning-services-quickstart-job-rstudio.md)
* [Opties voor compute-context voor ML Services-cluster in HDInsight](r-server-compute-contexts.md)
* [Opslag opties voor een cluster van ML Services in HDInsight](r-server-storage.md)
