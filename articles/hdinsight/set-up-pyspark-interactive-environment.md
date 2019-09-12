---
title: Azure HDInsight-Hulpprogram Ma's-PySpark Interactive Environment for Visual Studio code
description: Meer informatie over het gebruik van de Azure HDInsight-Hulpprogram Ma's voor Visual Studio code voor het maken en verzenden van query's en scripts.
keywords: VScode, Azure HDInsight tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interactieve query
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: d220d81b8dc57541113f7ef1e477bb77d394e206
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879288"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>De PySpark Interactive Environment instellen voor Visual Studio code

De volgende stappen laten zien hoe u de PySpark Interactive environment in VS code kunt instellen.

We gebruiken **python/pip-** opdracht voor het maken van een virtuele omgeving in uw start traject. Als u een andere versie wilt gebruiken, moet u de standaard versie van de opdracht **python/pip** hand matig wijzigen. Zie [update-alternatieven](https://linux.die.net/man/8/update-alternatives)voor meer informatie.

1. Installeer [python](https://www.python.org/downloads/) en [PIP](https://pip.pypa.io/en/stable/installing/).
   
   + Installeer python vanuit [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + PIP installeren vanaf [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (als deze niet is geïnstalleerd vanuit de python-installatie).
   + Controleer of python en PIP zijn geïnstalleerd met behulp van de volgende opdrachten. Beschrijving
 
        ![Python pip-versie](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Het is raadzaam python hand matig te installeren in plaats van de standaard versie macOS te gebruiken.


2. Installeer **virtualenv** door de onderstaande opdracht uit te voeren.
   
   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Andere pakketten

Als er een fout bericht wordt weer gegeven, installeert u de vereiste pakketten door de volgende opdrachten uit te voeren:

   ![libkrb5-pakket](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

Start VS code opnieuw en ga terug naar de script editor waarop HDInsight wordt uitgevoerd **: PySpark Interactive**.

## <a name="next-steps"></a>Volgende stappen

### <a name="demo"></a>Demo
* HDInsight voor VS code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen
* [Azure HDInsight-hulp programma voor Visual Studio code gebruiken](hdinsight-for-vscode.md)
* [Azure-toolkit voor IntelliJ gebruiken om Apache Spark scala-toepassingen te maken en in te dienen](spark/apache-spark-intellij-tool-plugin.md)
* [Azure-toolkit voor IntelliJ gebruiken om fouten op te lossen Apache Spark toepassingen op afstand via SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Azure-toolkit voor IntelliJ gebruiken om fouten op te lossen Apache Spark toepassingen op afstand via VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Gebruik HDInsight-Hulpprogram Ma's in Azure-toolkit voor Eclipse om Apache Spark-toepassingen te maken](spark/apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter notebook in een Apache Spark-cluster voor HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Apache Hive-gegevens visualiseren met Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Apache Zeppelin gebruiken voor het uitvoeren van Apache Hive-query's in Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
