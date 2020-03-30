---
title: Interactieve PySpark-omgeving met Azure HDInsight-hulpprogramma's
description: Meer informatie over het gebruik van de Azure HDInsight-hulpprogramma's voor Visual Studio-code om query's en scripts te maken en in te dienen.
keywords: VScode,Azure HDInsight Tools,Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactieve query
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: db2336fb79207ada24b71e0e64f0aaaab543e4da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73241554"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>De interactieve PySpark-omgeving instellen voor Visual Studio Code

In de volgende stappen ziet u hoe u de interactieve PySpark-omgeving in VS-code instelt.

We gebruiken **python/pip** commando om virtuele omgeving te bouwen in uw Home pad. Als u een andere versie wilt gebruiken, moet u de standaardversie van **de python/pip-opdracht** handmatig wijzigen. Meer details zie [update-alternatieven](https://linux.die.net/man/8/update-alternatives).

1. Installeer [Python](https://www.python.org/downloads/) en [pip](https://pip.pypa.io/en/stable/installing/).

   + Installeer Python [https://www.python.org/downloads/](https://www.python.org/downloads/)vanaf .
   + Installeer pip [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) van (als het niet is geïnstalleerd vanuit de Python-installatie).
   + Valideer dat Python en pip met succes zijn geïnstalleerd met behulp van de volgende opdrachten. (Optioneel)

        ![Python-versie controleren, opdracht](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Het wordt aanbevolen om Python handmatig te installeren in plaats van de standaardversie van macOS te gebruiken.

2. Installeer **virtualenv** door onderstaande opdracht uit te voeren.

   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Andere pakketten

Als u een foutbericht tegenkomt, installeert u de vereiste pakketten door de volgende opdrachten uit te voeren:

   ![Installeer libkrb5 pakket voor python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

Start VS-code opnieuw op en ga terug naar de scripteditor waarop **HDInsight: PySpark Interactive**wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

### <a name="demo"></a>Demo
* HDInsight voor VS-code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen
* [Azure HDInsight-tool gebruiken voor visual studiocode](hdinsight-for-vscode.md)
* [Azure Toolkit voor IntelliJ gebruiken om Apache Spark Scala-toepassingen te maken en in te dienen](spark/apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen op afstand te debuggen via SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen op afstand te debuggen via VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HdInsight-hulpprogramma's gebruiken in Azure Toolkit voor Eclipse om Apache Spark-toepassingen te maken](spark/apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster op HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels beschikbaar voor Jupyter-laptop in een Apache Spark-cluster voor HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Apache Hive-gegevens visualiseren met Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Apache Zeppelin gebruiken voor het uitvoeren van Apache Hive-query's in Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
