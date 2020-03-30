---
title: Problemen met Apache Spark oplossen in Azure HDInsight
description: Antwoorden op veelgestelde vragen over het werken met Apache Spark en Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271939"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Probleem met Apache Spark oplossen met behulp van Azure HDInsight

Meer informatie over de belangrijkste problemen en hun oplossingen bij het werken met Apache Spark payloads in [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Hoe kan ik een Apache Spark-toepassing configureren met behulp van Apache Ambari in clusters?

Spark-configuratiewaarden kunnen worden afgestemd, `OutofMemoryError` om een uitzondering van de Apache Spark-toepassing te voorkomen. In de volgende stappen worden standaardsparkconfiguratiewaarden weergegeven in Azure HDInsight:

1. Meld u aan bij `https://CLUSTERNAME.azurehdidnsight.net` Ambari bij uw clusterreferenties. Het eerste scherm toont een overzichtsdashboard. Er zijn kleine cosmetische verschillen tussen HDInsight 3.6 en 4.0.

1. Navigeer naar **Spark2** > **Configs**.

    ![Het tabblad Configs selecteren](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. Selecteer en vouw **aangepaste spark2-standaardinstellingen**uit in de lijst met configuraties.

1. Zoek naar de waarde-instelling die u moet aanpassen, zoals **spark.executor.memory**. In dit geval is de waarde van **9728m** te hoog.

    ![Aangepaste spark-defaults selecteren](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Stel de waarde in op de aanbevolen instelling. De waarde **2048m** wordt aanbevolen voor deze instelling.

1. Sla de waarde op en sla de configuratie op. Selecteer **Opslaan**.

    ![Waarde wijzigen in 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Schrijf een notitie over de configuratiewijzigingen en selecteer **Opslaan**.

    ![Voer een notitie in over de wijzigingen die u hebt aangebracht](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    U wordt op de hoogte gesteld als configuraties aandacht nodig hebben. Noteer de items en selecteer **Toch doorgaan**.

    ![Selecteer Toch doorgaan](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Wanneer een configuratie wordt opgeslagen, wordt u gevraagd de service opnieuw te starten. Selecteer **Opnieuw starten**.

    ![Opnieuw starten selecteren](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Bevestig de herstart.

    ![Selecteer Alles opnieuw starten bevestigen selecteren](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    U de processen bekijken die worden uitgevoerd.

    ![Lopende processen controleren](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. U configuraties toevoegen. Selecteer in de lijst met configuraties de optie **Custom-spark2-defaults**en selecteer **Eigenschap toevoegen**.

    ![Eigenschap Toevoegen selecteren](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Definieer een nieuwe eigenschap. U één eigenschap definiëren met een dialoogvenster voor specifieke instellingen, zoals het gegevenstype. U ook meerdere eigenschappen definiëren met één definitie per regel.

    In dit voorbeeld wordt de eigenschap **spark.driver.memory** gedefinieerd met een waarde van **4g**.

    ![Nieuwe eigenschap definiëren](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Sla de configuratie op en start de service opnieuw zoals beschreven in stap 6 en 7.

Deze wijzigingen zijn clusterbreed, maar kunnen worden overschreven wanneer u de Spark-taak indient.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hoe kan ik een Apache Spark-toepassing configureren met behulp van een Jupyter-notebook in clusters?

Geef in de eerste cel van het Jupyter-notitieblok na de **%%configure-richtlijn** de Spark-configuraties op in geldige JSON-indeling. Wijzig indien nodig de werkelijke waarden:

![Een configuratie toevoegen](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Hoe kan ik een Apache Spark-toepassing configureren met behulp van Apache Livy in clusters?

Dien de Spark-toepassing in bij Livy met behulp van een REST-client zoals cURL. Gebruik een opdracht die vergelijkbaar is met de volgende opdracht. Wijzig indien nodig de werkelijke waarden:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hoe kan ik een Apache Spark-toepassing configureren met behulp van spark-indiening in clusters?

Start spark-shell met behulp van een opdracht die vergelijkbaar is met de volgende. Wijzig indien nodig de werkelijke waarde van de configuraties:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Aanvullende lezing

[Apache Spark job submission op HDInsight clusters](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* [Overzicht van spark-geheugenbeheer](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debugging Spark-toepassing op HDInsight-clusters](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
