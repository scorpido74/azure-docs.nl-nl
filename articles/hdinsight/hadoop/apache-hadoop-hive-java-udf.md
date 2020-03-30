---
title: Java-gebruikersgedefinieerde functie (UDF) met Apache Hive Azure HDInsight
description: Meer informatie over het maken van een op Java gebaseerde gebruikersfunctie (UDF) die werkt met Apache Hive. In dit voorbeeld converteert UDF een tabel met teksttekenreeksen naar kleine letters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/20/2019
ms.openlocfilehash: 73a2a612a4eeb4a59f12abf0660fffb092f0547f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327199"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Gebruik een Java UDF met Apache Hive in HDInsight

Meer informatie over het maken van een op Java gebaseerde gebruikersfunctie (UDF) die werkt met Apache Hive. De Java UDF in dit voorbeeld converteert een tabel met teksttekenreeksen naar tekens met alle kleine letters.

## <a name="prerequisites"></a>Vereisten

* Een Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) versie 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) goed [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een project build systeem voor Java projecten.
* Het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag van uw clusters. Dit zou wasb:// zijn voor Azure Storage, abfs:// voor Azure Data Lake Storage Gen2 of adl:// voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure `wasbs://`Storage, is de URI .  Zie ook, [veilige overdracht](../../storage/common/storage-require-secure-transfer.md).

* Een teksteditor of Java IDE

    > [!IMPORTANT]  
    > Als u de Python-bestanden op een Windows-client maakt, moet u een editor gebruiken die LF als einde van de regel gebruikt. Als u niet zeker weet of uw editor LF of CRLF gebruikt, raadpleegt u de sectie [Probleemoplossing](#troubleshooting) voor stappen bij het verwijderen van het CR-teken.

## <a name="test-environment"></a>Testomgeving

De omgeving die voor dit artikel wordt gebruikt, was een computer met Windows 10.  De opdrachten werden uitgevoerd in een opdrachtprompt en de verschillende bestanden zijn bewerkt met Kladblok. Dienovereenkomstig aanpassen voor uw omgeving.

Voer in een opdrachtprompt de onderstaande opdrachten in om een werkomgeving te maken:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Een voorbeeld van Java UDF maken

1. Maak een nieuw Maven-project door de volgende opdracht in te voeren:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Met deze opdracht `exampleudf`wordt een map gemaakt met de naam , die het Maven-project bevat.

2. Zodra het project is gemaakt, verwijdert u de `exampleudf/src/test` map die is gemaakt als onderdeel van het project door de volgende opdracht in te voeren:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Open `pom.xml` door onderstaande opdracht in te voeren:

    ```cmd
    notepad pom.xml
    ```

    Vervang vervolgens `<dependencies>` de bestaande vermelding door de volgende XML:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Deze vermeldingen geven de versie van Hadoop en Hive vermeld die bij HDInsight 3.6 zijn inbegrepen. U vindt informatie over de versies van Hadoop en Hive voorzien van HDInsight uit het [HDInsight component versioning](../hdinsight-component-versioning.md) document.

    Voeg `<build>` een sectie `</project>` toe voor de regel aan het einde van het bestand. Deze sectie moet de volgende XML bevatten:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    Deze vermeldingen bepalen hoe het project moet worden gebouwd. Met name de versie van Java die het project gebruikt en hoe u een uberjar bouwen voor implementatie naar het cluster.

    Sla het bestand op zodra de wijzigingen zijn aangebracht.

4. Voer de onderstaande opdracht in `ExampleUDF.java`om een nieuw bestand te maken en te openen:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Kopieer en plak vervolgens de javacode hieronder in het nieuwe bestand. Sluit vervolgens het bestand.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Deze code implementeert een UDF die een tekenreekswaarde accepteert en retourneert een kleine versie van de tekenreeks.

## <a name="build-and-install-the-udf"></a>De UDF bouwen en installeren

Vervang in de onderstaande opdrachten `sshuser` de werkelijke gebruikersnaam als deze anders is. Vervang `mycluster` door de werkelijke clusternaam.

1. Compileren en verpakken van de UDF door de volgende opdracht in te voeren:

    ```cmd
    mvn compile package
    ```

    Met deze opdracht wordt de UDF in het `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` bestand gebouwd en verpakt.

2. Gebruik `scp` de opdracht om het bestand naar het HDInsight-cluster te kopiëren door de volgende opdracht in te voeren:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Maak verbinding met het cluster met SSH door de volgende opdracht in te voeren:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Kopieer het jar-bestand vanaf de open SSH-sessie naar HDInsight-opslag.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Gebruik de UDF van Hive

1. Start de Beeline-client vanuit de SSH-sessie door de volgende opdracht in te voeren:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Met deze opdracht wordt ervan uitgegaan dat u de standaardbeheerder **hebt** gebruikt voor het aanmeldingsaccount voor uw cluster.

2. Zodra u bij `jdbc:hive2://localhost:10001/>` de prompt aankomt, voert u het volgende in om de UDF aan Hive toe te voegen en deze als functie bloot te stellen.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Gebruik de UDF om waarden die uit een tabel zijn opgehaald, om te zetten in kleine letterstekenreeksen.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Met deze query selecteert u de status in de tabel, converteert u de tekenreeks naar kleine letters en geeft u deze weer samen met de ongewijzigde naam. De uitvoer lijkt op de volgende tekst:

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer u de korftaak uitvoert, u een fout tegenkomen die vergelijkbaar is met de volgende tekst:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Dit probleem kan worden veroorzaakt door de regeleinde in het Python-bestand. Veel Windows-editors standaard met behulp van CRLF als de lijn eindigt, maar Linux-toepassingen verwachten meestal LF.

U de volgende PowerShell-instructies gebruiken om de CR-tekens te verwijderen voordat u het bestand uploadt naar HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Volgende stappen

Zie [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)voor andere manieren om met Hive te werken.

Zie Apache Hive Operators [and User-Defined Functions](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) van de hive wiki op apache.org voor meer informatie over hive-gebruikersgedefinieerde functies.
