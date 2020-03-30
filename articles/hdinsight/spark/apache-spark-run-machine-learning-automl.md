---
title: Azure Machine Learning-workloads uitvoeren op Apache Spark in HDInsight
description: Meer informatie over het uitvoeren van Azure Machine Learning-workloads met geautomatiseerde machine learning (AutoML) op Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 6fc0d4cfe29e0fb189c44b307576bd08d2da8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638881"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Azure Machine Learning-workloads uitvoeren met geautomatiseerde machine learning op Apache Spark in HDInsight

Azure Machine Learning vereenvoudigt en versnelt het bouwen, trainen en implementeren van machine learning-modellen. In automated machine learning (AutoML) begint u met trainingsgegevens met een gedefinieerde doelfunctie en doorloopt u vervolgens combinaties van algoritmen en functieselecties om automatisch het beste model voor uw gegevens te selecteren op basis van de trainingsscores. HDInsight stelt klanten in staat om clusters te voorzien van honderden knooppunten. Met AutoML die op Spark wordt uitgevoerd in een HDInsight-cluster, kunnen gebruikers rekencapaciteit op deze knooppunten gebruiken om trainingstaken op een schaal-outmanier uit te voeren en meerdere trainingstaken parallel uit te voeren. Hierdoor kunnen gebruikers AutoML-experimenten uitvoeren terwijl ze de compute delen met hun andere big data-workloads.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Azure Machine Learning installeren op een HDInsight-cluster

Zie [Zelfstudie: Gebruik geautomatiseerde machine learning voor](../../machine-learning/tutorial-auto-train-models.md)algemene zelfstudies over geautomatiseerde machine learning om uw regressiemodel te bouwen.
Alle nieuwe HDInsight-Spark clusters zijn vooraf geïnstalleerd met AzureML-AutoML SDK.

> [!Note]
> Azure Machine Learning-pakketten worden geïnstalleerd in de Python3-conda-omgeving. De geïnstalleerde Jupyter-notebook moet worden uitgevoerd met de PySpark3-kernel.

U Zeppelin-laptops ook gebruiken om AutoML te gebruiken.

> [!Note]
> Zeppelin heeft een [bekend probleem](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) waarbij PySpark3 niet de juiste versie van Python kiest. Gebruik de gedocumenteerde work-around.

## <a name="authentication-for-workspace"></a>Verificatie voor werkruimte

Voor het maken en indienen van experimenten is een verificatietoken vereist. Dit token kan worden gegenereerd met behulp van een [Azure AD-toepassing](../../active-directory/develop/app-objects-and-service-principals.md). Een [Azure AD-gebruiker](/azure/python/python-sdk-azure-authenticate) kan ook worden gebruikt om het vereiste verificatietoken te genereren, als multi-factor-verificatie niet is ingeschakeld voor het account.  

In het volgende codefragment wordt een verificatietoken gemaakt met behulp van een **Azure AD-toepassing**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

In het volgende codefragment wordt een verificatietoken gemaakt met behulp van een **Azure AD-gebruiker.**

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Gegevensset laden

Geautomatiseerde machine learning op Spark maakt gebruik van **Dataflows**, die lui worden geëvalueerd, onveranderlijke bewerkingen op gegevens.  Een gegevensstroom kan een gegevensset laden vanuit een blob met openbare leestoegang of vanuit een blob-URL met een SAS-token.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

U het gegevensarchief ook registreren bij de werkruimte met behulp van een eenmalige registratie.

## <a name="experiment-submission"></a>Indiening van experiment

In de [geautomatiseerde machine learning-configuratie](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)moet de eigenschap `spark_context` worden ingesteld voor het pakket dat op gedistribueerde modus wordt uitgevoerd. De `concurrent_iterations`eigenschap , het maximum aantal iteraties dat parallel wordt uitgevoerd, moet worden ingesteld op een getal dat minder is dan de executor-kernen voor de Spark-app.

## <a name="next-steps"></a>Volgende stappen

* Zie [Release-modellen op tempo met behulp van microsoft's geautomatiseerde machine learning voor](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/) meer informatie over de motivatie achter geautomatiseerde machine learning!
* Zie [Nieuwe geautomatiseerde machine learning-mogelijkheden in Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/) voor meer informatie over het gebruik van Azure ML Automated ML-mogelijkheden.
* [AutoML-project van Microsoft Research](https://www.microsoft.com/research/project/automl/)
