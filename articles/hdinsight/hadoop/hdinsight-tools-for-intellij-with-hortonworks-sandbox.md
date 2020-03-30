---
title: Azure Toolkit gebruiken voor IntelliJ met Hortonworks Sandbox
description: Meer informatie over het gebruik van HDInsight-hulpprogramma's in Azure Toolkit voor IntelliJ met Hortonworks Sandbox.
keywords: hadoop tools, hive query, intellij, hortonworks sandbox, azure toolkit voor intellij
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 65a15a8506b88e95e14af8c87bcbe33087301519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647829"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Gebruik HDInsight Tools voor IntelliJ met Hortonworks Sandbox

Leer hoe u HDInsight Tools voor IntelliJ gebruiken om Apache Scala-toepassingen te ontwikkelen en vervolgens de toepassingen te testen op [Hortonworks Sandbox](https://hortonworks.com/products/sandbox/) die op uw computer wordt uitgevoerd. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) is een Java geïntegreerde ontwikkelomgeving (IDE) voor het ontwikkelen van computersoftware. Nadat u uw toepassingen hebt ontwikkeld en getest op Hortonworks Sandbox, u de toepassingen verplaatsen naar [Azure HDInsight.](apache-hadoop-introduction.md)

## <a name="prerequisites"></a>Vereisten

Voordat u met dit artikel begint, moet u de volgende objecten hebben:

- Hortonworks Data Platform (HDP) 2.4 op Hortonworks Sandbox draait op uw lokale computer. Zie [Aan de slag in het Apache Hadoop-ecosysteem met een Hadoop-sandbox op een virtuele machine](apache-hadoop-emulator-get-started.md)om HDP op te zetten. 
    > [!NOTE]
    > HDInsight Tools voor IntelliJ is alleen getest met HDP 2.4. Om HDP 2.4 te krijgen, breidt **Hortonworks Sandbox Archive** uit op de [Hortonworks Sandbox downloads site](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) versie 1.8 of hoger](https://aka.ms/azure-jdks). Azure Toolkit voor IntelliJ vereist JDK.

- [IntelliJ IDEA community edition](https://www.jetbrains.com/idea/download) met de [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) plug-in en de Azure Toolkit [voor IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) plug-in. HDInsight Tools voor IntelliJ is beschikbaar als onderdeel van Azure Toolkit voor IntelliJ. 

Ga als bedoeld als het gaat om het installeren van de plug-ins:

  1. Open IntelliJ IDEA.
  2. Selecteer **op** de welkomstpagina **Configureren**en selecteer **Plug-ins**.
  3. Selecteer **JetBrains-plug-in installeren**in de linkerbenedenhoek .
  4. Gebruik de zoekfunctie om naar **Scala**te zoeken en selecteer **Installeren**.
  5. Als u de installatie wilt voltooien, selecteert u **IntelliJ IDEA opnieuw starten**.
  6. Herhaal stap 4 en 5 om **Azure Toolkit voor IntelliJ**te installeren. Zie [Azure Toolkit voor IntelliJ installeren voor](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)meer informatie.

## <a name="create-an-apache-spark-scala-application"></a>Een Toepassing Apache Spark Scala maken

In deze sectie maakt u een voorbeeld van Scala-project met IntelliJ IDEA. In het volgende gedeelte koppelt u IntelliJ IDEA aan de Hortonworks Sandbox (emulator) voordat u het project indient.

1. Open IntelliJ IDEA op uw computer. Voer in het dialoogvenster **Nieuw project** de volgende stappen uit:

   1. Selecteer **HDInsight** > **Spark op HDInsight (Scala)**.
   2. Selecteer in de lijst **Met gereedschap bouwen** een van de volgende opties op basis van uw scenario:

      * **Maven**: Voor Scala project-creatie wizard ondersteuning.
      * **SBT**: Voor het beheren van afhankelijkheden en bouwen voor het Scala-project.

   ![Intellij creëert nieuw scala-project](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Selecteer **Volgende**.
3. Voer in het volgende dialoogvenster **Nieuw project** de volgende stappen uit:

   1. Voer in het vak **Projectnaam** een projectnaam in.
   2. Voer in het vak **Projectlocatie** een projectlocatie in.
   3. Selecteer naast de vervolgkeuzelijst **Project SDK** de optie **Nieuw,** selecteer **JDK**en geef vervolgens de map op voor Java JDK-versie 1.7 of hoger. Selecteer **Java 1.8** voor het Spark 2.x-cluster. Selecteer **Java 1.7** voor het Spark 1.x-cluster. De standaardlocatie is C:\Program Files\Java\jdk1.8.x_xxx.
   4. In de vervolgkeuzelijst **Spark-versie** integreert de wizard Scala voor het maken van projecten de juiste versie voor de Spark SDK en Scala SDK. Selecteer **Spark 1.x** als de Spark-clusterversie ouder is dan 2.0. Selecteer anders **Spark 2.x**. In dit voorbeeld wordt Spark 1.6.2 (Scala 2.10.5) gebruikt. Controleer of u de opslagplaats met de vermelding **Scala 2.10.x**gebruikt. Gebruik de repository met de tag Scala 2.11.x niet.
    
      ![IntelliJ Scala-projecteigenschappen maken](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Selecteer **Finish**.
5. Als de **projectweergave** nog niet is geopend, drukt u op **Alt+1** om deze te openen.
6. Vouw in **Project Explorer**het project uit en selecteer **vervolgens src**.
7. Klik met de rechtermuisknop op **src,** wijs **Nieuw**aan en selecteer de **klasse Scala**.
8. Voer in het vak **Naam** een naam in. Selecteer **Object**in het vak **Vriendelijk** . Selecteer vervolgens **OK**.

    ![Het dialoogvenster Nieuwe Scala-klasse maken](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. Plak in het .scala-bestand de volgende code:

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

10. Selecteer project **bouwen**in het menu **Bouwen** . Zorg ervoor dat de compilatie is voltooid.


## <a name="link-to-the-hortonworks-sandbox"></a>Link naar de Hortonworks Sandbox

Voordat u linken naar een Hortonworks Sandbox (emulator), moet u een bestaande IntelliJ-toepassing hebben.

Linken naar een emulator:

1. Open het project in IntelliJ.
2. Selecteer **in het** menu Weergave de optie **Extra Windows**en selecteer vervolgens **Azure Explorer**.
3. Vouw **Azure**uit, klik met de rechtermuisknop op **HDInsight**en selecteer **vervolgens Een Emulator koppelen**.
4. Voer in het dialoogvenster **Een nieuwe emulator** koppelen het wachtwoord in dat u hebt ingesteld voor het hoofdaccount van de Sandbox van Hortonworks. Voer vervolgens waarden in die vergelijkbaar zijn met die welke in de volgende schermafbeelding worden gebruikt. Selecteer vervolgens **OK**. 

   ![Het dialoogvenster Een nieuwe emulator koppelen](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Als u de emulator wilt configureren, selecteert u **Ja**.

Wanneer de emulator met succes is verbonden, wordt de emulator (Hortonworks Sandbox) vermeld op het HDInsight-knooppunt.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Dien de Spark Scala-aanvraag in bij de Hortonworks Sandbox

Nadat u IntelliJ IDEA aan de emulator hebt gekoppeld, u uw project indienen.

Ga als lid van het project naar een emulator:

1. Klik in **Project Explorer**met de rechtermuisknop op het project en selecteer **Spark-toepassing verzenden naar HDInsight**.
2. Voltooi de volgende stappen:

    1. Selecteer in de vervolgkeuzelijst **Spark-cluster (alleen Linux)** de lokale Hortonworks Sandbox.
    2. Selecteer of voer in het **vak Hoofdklasse** naam in of voer de naam van de hoofdklasse in. Voor dit artikel is de naam **GroupByTest**.

3. Selecteer **Indienen**. De logboeken voor het indienen van vacatures worden weergegeven in het venster voor het indienen van vonken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van HDInsight-hulpprogramma's in Azure Toolkit voor IntelliJ om Apache Spark-toepassingen te maken voor een HDInsight Spark Linux-cluster.](../spark/apache-spark-intellij-tool-plugin.md)

- Zie [HDInsight Tools voor IntelliJ introduceren voor](https://www.youtube.com/watch?v=YTZzYVgut6c)een video over HDInsight Tools voor de ontwikkeling van Apache Spark.

- Meer informatie over het [op afstand debuggen van Apache Spark-toepassingen op een HDInsight-cluster met Azure Toolkit voor IntelliJ via SSH.](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)

- Meer informatie over het [gebruik van HDInsight-hulpprogramma's in Azure Toolkit voor IntelliJ om Apache Spark-toepassingen op afstand te debuggen op een HDInsight Spark Linux-cluster.](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

- Meer informatie over het [gebruik van HDInsight-tools in Azure Toolkit voor Eclipse om Apache Spark-toepassingen te maken.](../spark/apache-spark-eclipse-tool-plugin.md)

