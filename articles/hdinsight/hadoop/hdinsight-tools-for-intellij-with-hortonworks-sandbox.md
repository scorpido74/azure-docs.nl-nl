---
title: Azure-toolkit voor IntelliJ gebruiken met Hortonworks sandbox
description: Meer informatie over het gebruik van HDInsight-Hulpprogram Ma's in Azure-toolkit voor IntelliJ met de sandbox Hortonworks.
keywords: Hadoop-hulpprogram ma's, Hive-query, IntelliJ, hortonworks sandbox, Azure Toolkit voor IntelliJ
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 783d7fc8bc26ce2c715c774e63ecf60c5b75a439
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076262"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>HDInsight-Hulpprogram Ma's gebruiken voor IntelliJ met Hortonworks sandbox

Meer informatie over het gebruik van HDInsight-Hulpprogram Ma's voor IntelliJ om Apache scala-toepassingen te ontwikkelen en vervolgens de toepassingen te testen op [Hortonworks sandbox](https://hortonworks.com/products/sandbox/) die op uw computer wordt uitgevoerd. 

[INTELLIJ idee](https://www.jetbrains.com/idea/) is een Java Integrated Development Environment (IDE) voor het ontwikkelen van computer software. Nadat u uw toepassingen op Hortonworks sandbox hebt ontwikkeld en getest, kunt u de toepassingen naar [Azure HDInsight](apache-hadoop-introduction.md)verplaatsen.

## <a name="prerequisites"></a>Vereisten

Voordat u met dit artikel begint, moet u beschikken over de volgende items:

- Hortonworks data platform (HDP) 2,4 op Hortonworks sandbox die wordt uitgevoerd op uw lokale computer. Als u HDP wilt instellen, raadpleegt u aan [de slag in het Apache Hadoop ecosysteem met een Hadoop sandbox op een virtuele machine](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > HDInsight-Hulpprogram Ma's voor IntelliJ zijn alleen getest met HDP 2,4. Als u HDP 2,4 wilt ophalen, vouwt u **Hortonworks sandbox Archive** uit op de [Hortonworks-sandbox-download site](https://hortonworks.com/downloads/#sandbox).

- [Jdk-versie 1,8 of hoger van Java Developer Kit](https://aka.ms/azure-jdks). Voor Azure-toolkit voor IntelliJ is JDK vereist.

- [IntelliJ-idee Community-editie](https://www.jetbrains.com/idea/download) met de [scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) -invoeg toepassing en de [Azure-Toolkit voor IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) -invoeg toepassing. HDInsight-Hulpprogram Ma's voor IntelliJ is beschikbaar als onderdeel van Azure-toolkit voor IntelliJ. 

De invoeg toepassingen installeren:

  1. Open IntelliJ IDEA.
  2. Selecteer op de pagina **Welkom** de optie **configureren**en selecteer vervolgens **invoeg toepassingen**.
  3. Selecteer in de linkerbenedenhoek **installeren JetBrains-invoeg toepassing**.
  4. Gebruik de zoek functie om naar **scala**te zoeken en selecteer vervolgens **installeren**.
  5. Selecteer **IntelliJ-idee opnieuw opstarten**om de installatie te volt ooien.
  6. Herhaal stap 4 en 5 om **Azure-Toolkit voor IntelliJ**te installeren. Zie [Install Azure-Toolkit voor IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)(Engelstalig) voor meer informatie.

## <a name="create-an-apache-spark-scala-application"></a>Een Apache Spark scala-toepassing maken

In deze sectie maakt u een voor beeld van een scala-project met behulp van IntelliJ-idee. In de volgende sectie koppelt u IntelliJ-idee aan de Hortonworks-sandbox (emulator) voordat u het project verzendt.

1. Open IntelliJ-idee op uw computer. Voer in het dialoog venster **Nieuw project** de volgende stappen uit:

   1. Selecteer **HDInsight** > **Spark in HDInsight (Scala)** .
   2. Selecteer in de lijst **Build Tool** een van de volgende opties, op basis van uw scenario:

      * **Maven**: Voor ondersteuning van de wizard scala-project maken.
      * **SBT**: Voor het beheren van afhankelijkheden en het maken van het scala-project.

   ![IntelliJ nieuw scala-project maken](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Selecteer **Volgende**.
3. Voer de volgende stappen uit in het dialoog venster volgende **Nieuw project** :

   1. Voer in het vak **project naam** een project naam in.
   2. Voer in het vak **Project locatie** een project locatie in.
   3. Selecteer in de vervolg keuzelijst **Project SDK** de optie **Nieuw**, selecteer **jdk**en geef de map op voor Java JDK versie 1,7 of hoger. Selecteer **Java 1,8** voor het Spark 2. x-cluster. Selecteer **Java 1,7** voor het cluster Spark 1. x. De standaard locatie is C:\Program Files\Java\jdk1.8.x_xxx.
   4. In de vervolg keuzelijst **Spark-versie** integreert de wizard scala-project maken de juiste versie voor de Spark SDK en scala SDK. Selecteer **Spark 1.x** als de Spark-clusterversie ouder is dan 2.0. Selecteer anders **Spark 2.x**. In dit voor beeld wordt Spark 1.6.2 (scala 2.10.5) gebruikt. Zorg ervoor dat u de opslag plaats gebruikt die is gemarkeerd als **scala 2.10. x**. Gebruik de opslag plaats die is gemarkeerd als scala 2.11. x.
    
      ![IntelliJ scala project-eigenschappen maken](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Selecteer **Finish**.
5. Als de **project** weergave nog niet is geopend, drukt u op **ALT + 1** om deze te openen.
6. Vouw in **project Verkenner**het project uit en selecteer vervolgens **src**.
7. Klik met de rechter muisknop op **src**, wijs **Nieuw**aan en selecteer **scala klasse**.
8. Voer in het vak **naam** een naam in. Selecteer in het vak **kind** de optie **object**. Selecteer vervolgens **OK**.

    ![Het dialoog venster nieuwe scala-klasse maken](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. Plak in het. scala-bestand de volgende code:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. Selecteer in het menu **Build** de optie **project maken**. Zorg ervoor dat de compilatie is voltooid.


## <a name="link-to-the-hortonworks-sandbox"></a>Koppeling naar de Hortonworks sandbox

Voordat u een koppeling naar een Hortonworks sandbox (emulator) kunt maken, moet u een bestaande IntelliJ-toepassing hebben.

Een koppeling naar een emulator maken:

1. Open het project in IntelliJ.
2. Selecteer in het menu **weer gave** de optie **extra Windows**en selecteer vervolgens **Azure Explorer**.
3. Vouw **Azure**uit, klik met de rechter muisknop op **HDInsight**en selecteer vervolgens **een emulator koppelen**.
4. In het dialoog venster **een nieuwe emulator koppelen** voert u het wacht woord in dat u hebt ingesteld voor het hoofd account van de sandbox Hortonworks. Voer vervolgens waarden in die vergelijkbaar zijn met die in de volgende scherm afbeelding. Selecteer vervolgens **OK**. 

   ![Het dialoog venster een nieuwe emulator koppelen](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Als u de emulator wilt configureren, selecteert u **Ja**.

Wanneer de emulator is verbonden, wordt de emulator (Hortonworks sandbox) weer gegeven op het HDInsight-knoop punt.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>De Spark scala-toepassing naar de Hortonworks-sandbox verzenden

Nadat u het IntelliJ-idee aan de emulator hebt gekoppeld, kunt u uw project indienen.

Een project verzenden naar een emulator:

1. Klik in **project Verkenner**met de rechter muisknop op het project en selecteer vervolgens **Spark-toepassing verzenden naar HDInsight**.
2. Voltooi de volgende stappen:

    1. Selecteer in de vervolg keuzelijst **Spark-cluster (alleen Linux)** de lokale sandbox met Hortonworks.
    2. Selecteer of typ de naam van de hoofd klasse in het vak **naam van hoofd klasse** . Voor dit artikel is de naam **GroupByTest**.

3. Selecteer **indienen**. De logboeken voor het indienen van taken worden weer gegeven in het hulp programma voor verzen ding van Spark.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van HDInsight-Hulpprogram ma's in azure-Toolkit voor IntelliJ voor het maken van Apache Spark-toepassingen voor een HDInsight Spark Linux-cluster](../spark/apache-spark-intellij-tool-plugin.md).

- Zie voor een video over HDInsight-Hulpprogram Ma's voor IntelliJ [introduce HDInsight-Hulpprogram ma's voor IntelliJ voor Apache Spark ontwikkeling](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Informatie over het op [afstand opsporen van Apache Spark toepassingen op een HDInsight-cluster met Azure-Toolkit voor IntelliJ via SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Meer informatie over het [gebruik van HDInsight-Hulpprogram ma's in azure-Toolkit voor IntelliJ voor het op afstand opsporen van fouten in Apache Spark toepassingen op een HDInsight Spark Linux-cluster](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Meer informatie over het [gebruik van HDInsight-Hulpprogram ma's in azure-Toolkit voor eclipse om Apache Spark-toepassingen te maken](../spark/apache-spark-eclipse-tool-plugin.md).

- Zie [hdinsight-Hulpprogram ma's voor eclips gebruiken om Spark-toepassingen te maken](https://mix.office.com/watch/1rau2mopb6fha)voor een video over HDInsight-hulpprogram Ma's voor eclips.
