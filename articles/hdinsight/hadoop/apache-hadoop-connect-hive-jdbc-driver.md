---
title: Query Apache Hive door het JDBC-stuurprogramma - Azure HDInsight
description: Gebruik de JDBC-driver vanuit een Java-toepassing om Apache Hive-query's in te dienen bij Hadoop op HDInsight. Sluit programmatisch en vanuit de SQuirrel SQL-client.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 8129239f152f6b359b930e56466052da12ef4d42
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437031"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Query uitvoeren op Apache Hive via het JDBC-stuurprogramma in HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Meer informatie over het gebruik van het JDBC-stuurprogramma vanuit een Java-toepassing om Apache Hive-query's in te dienen bij Apache Hadoop in Azure HDInsight. De informatie in dit document laat zien hoe u programmatisch verbinding maken en vanuit de SQuirreL SQL-client.

Zie [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)voor meer informatie over de Hive JDBC-interface.

## <a name="prerequisites"></a>Vereisten

* Een HDInsight Hadoop cluster. Zie [Aan de slag met Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)om er een te maken. Controleer of de service HiveServer2 wordt uitgevoerd.
* De [Java Developer Kit (JDK) versie 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) of hoger.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL is een JDBC client applicatie.

## <a name="jdbc-connection-string"></a>JDBC-verbindingsreeks

JDBC-verbindingen met een HDInsight-cluster op Azure worden gemaakt via poort 443 en het verkeer wordt beveiligd met TLS/SSL. De openbare gateway waar de clusters achter zitten, leidt het verkeer om naar de poort waar HiveServer2 daadwerkelijk op luistert. In de volgende verbindingstekenreeks wordt de indeling weergegeven die moet worden gebruikt voor HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Vervang `CLUSTERNAME` door de naam van uw HDInsight-cluster.

Of u de verbinding krijgen via **Ambari UI > Hive > Configs > Advanced.**

![JDBC-verbindingstekenreeks door Ambari halen](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Hostnaam in verbindingstekenreeks

Hostnaam 'CLUSTERNAME.azurehdinsight.net' in de verbindingstekenreeks is hetzelfde als de URL van uw cluster. U het krijgen via Azure portal. 

### <a name="port-in-connection-string"></a>Poort in verbindingstekenreeks

U **poort 443** alleen gebruiken om vanaf sommige plaatsen buiten het virtuele Azure-netwerk verbinding te maken met het cluster. HDInsight is een managed service, wat betekent dat alle verbindingen met het cluster worden beheerd via een beveiligde Gateway. U geen verbinding maken met HiveServer 2 rechtstreeks op poorten 10001 of 10000 omdat deze poorten niet aan de buitenkant zijn blootgesteld. 

## <a name="authentication"></a>Verificatie

Bij het tot stand brengen van de verbinding moet u de naam en het wachtwoord van de HDInsight-clusterbeheerder gebruiken om te verifiëren naar de clustergateway. Wanneer u verbinding maakt met JDBC-clients zoals SQuirreL SQL, moet u de naam en het wachtwoord van de beheerder invoeren in clientinstellingen.

Vanuit een Java-toepassing moet u de naam en het wachtwoord gebruiken bij het tot stand brengen van een verbinding. De volgende Java-code opent bijvoorbeeld een nieuwe verbinding met de verbindingstekenreeks, de beheerdersnaam en het wachtwoord:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Verbinding maken met SQuirreL SQL-client

SQuirreL SQL is een JDBC-client die kan worden gebruikt om op afstand Hive-query's uit te voeren met uw HDInsight-cluster. In de volgende stappen wordt ervan uitgegaan dat u SQuirreL SQL al hebt geïnstalleerd.

1. Maak een map met bepaalde bestanden die uit uw cluster moeten worden gekopieerd.

2. Vervang in het `sshuser` volgende script de naam van het SSH-gebruikersaccount voor het cluster.  Vervang `CLUSTERNAME` door de clusternaam HDInsight.  Wijzig vanuit een opdrachtregel uw werkmap in de map die in de vorige stap is gemaakt en voer de volgende opdracht in om bestanden uit een HDInsight-cluster te kopiëren:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Start de SQuirreL SQL-toepassing. Selecteer **Drivers**links van het venster .

    ![Tabblad Stuurprogramma's aan de linkerkant van het venster](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Selecteer in de pictogrammen boven aan het **+** dialoogvenster **Stuurprogramma's** het pictogram om een stuurprogramma te maken.

    ![Pictogram SQuirreL SQL-toepassingsstuurprogramma's](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. Voeg in het dialoogvenster Stuurprogramma toevoegen de volgende gegevens toe:

    |Eigenschap | Waarde |
    |---|---|
    |Name|Hive|
    |Voorbeeld-URL|jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2|
    |Extra klassepad|Gebruik de knop **Toevoegen** om alle eerder gedownloade jar-bestanden toe te voegen.|
    |Klassennaam|org.apache.hive.jdbc.HiveDriver|

   ![dialoogvenster stuurprogramma met parameters toevoegen](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Selecteer **OK** om deze instellingen op te slaan.

6. Selecteer **Aliassen**aan de linkerkant van het SQuirreL SQL-venster . Selecteer vervolgens **+** het pictogram om een verbindingsalias te maken.

    ![Dialoogvenster SQuirreL SQL toevoegen](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Gebruik de volgende waarden voor het dialoogvenster **Alias toevoegen:**

    |Eigenschap |Waarde |
    |---|---|
    |Name|Hive op HDInsight|
    |Stuurprogramma|Gebruik de vervolgkeuzelijst om het **Hive-stuurprogramma** te selecteren.|
    |URL|jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2. Vervang **CLUSTERNAME** door de naam van uw HDInsight-cluster.|
    |Gebruikersnaam|De naam van het clusteraccount voor uw HDInsight-cluster. De standaardinstelling is **beheerder**.|
    |Wachtwoord|Het wachtwoord voor het clusteraccount.|

    ![aliasdialoogvenster met parameters toevoegen](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Gebruik de knop **Testen** om te controleren of de verbinding werkt. Wanneer **verbinding maken met: Hive op HDInsight** wordt weergegeven, selecteert u **Verbinding maken** om de test uit te voeren. Als de test slaagt, ziet u een dialoogvenster **Verbinding met succes.** Zie [Probleemoplossing](#troubleshooting)als er een fout optreedt.

    Als u de verbindingsalias wilt opslaan, gebruikt u de knop **Ok** onder aan het dialoogvenster **Alias toevoegen.**

8. Selecteer **Hive op HDInsight**van de **Connect naar** de vervolgkeuzelijst boven aan SQuirreL SQL. Selecteer **Verbinding maken**wanneer u daarom wordt gevraagd.

    ![verbindingsdialoogvenster met parameters](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Voer, nadat deze is verbonden, de volgende query in het dialoogvenster SQL-query in en selecteert u het pictogram **Uitvoeren** (een lopende persoon). In het resultaatgebied moeten de resultaten van de query worden weergegeven.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![dialoogvenster SQL-query, inclusief resultaten](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Verbinding maken vanuit een voorbeeld java-toepassing

Een voorbeeld van het gebruik van een Java-client om Hive op HDInsight te bevragen is beschikbaar op [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Volg de instructies in de opslagplaats om het voorbeeld te bouwen en uit te voeren.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Er is onverwachte fout opgetreden bij het openen van een SQL-verbinding

**Symptomen:** Wanneer u verbinding maakt met een HDInsight-cluster dat versie 3.3 of hoger is, ontvangt u mogelijk een fout die zich onverwacht heeft voorgedaan. De stacktracering voor deze fout begint met de volgende regels:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Oorzaak:** Deze fout wordt veroorzaakt door een oudere versie commons-codec.jar bestand opgenomen met SQuirreL.

**Oplossing:** Als u deze fout wilt oplossen, gebruikt u de volgende stappen:

1. Exit SQuirreL, en ga dan naar de directory waar SQuirreL is geïnstalleerd op uw systeem, misschien `C:\Program Files\squirrel-sql-4.0.0\lib`. Vervang in de SquirreL-map onder de `lib` map de bestaande commons-codec.jar door de pot die is gedownload van het HDInsight-cluster.

1. Start SQuirreL opnieuw op. De fout mag niet meer optreden bij het aansluiten op Hive op HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Verbinding verbroken door HDInsight

**Symptomen**: Wanneer u probeert om enorme hoeveelheid gegevens te downloaden (bijvoorbeeld meerdere GB's) via JDBC / ODBC, wordt de verbinding onverwacht verbroken door HDInsight tijdens het downloaden. 

**Oorzaak:** Deze fout wordt veroorzaakt door de beperking van gatewayknooppunten. Bij het ophalen van gegevens van JDBC/ODBC moeten alle gegevens door het Gateway-knooppunt gaan. Een gateway is echter niet ontworpen om een enorme hoeveelheid gegevens te downloaden, dus de verbinding kan worden gesloten door de Gateway als deze het verkeer niet aankan.

**Resolutie:** Vermijd het gebruik van JDBC /ODBC-stuurprogramma om enorme hoeveelheden gegevens te downloaden. Kopieer in plaats daarvan rechtstreeks gegevens uit blob-opslag.


## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u JDBC gebruiken om met Hive te werken, gebruikt u de volgende koppelingen om andere manieren te verkennen om met Azure HDInsight te werken.

* [Visualiseer Apache Hive-gegevens met Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualiseer interactieve queryhive-gegevens met Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Gebruik Apache Zeppelin om Apache Hive-query's uit te voeren in Azure HDInsight.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Verbind Excel met HDInsight met de Microsoft Hive ODBC-driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Sluit Excel aan op Apache Hadoop met Power Query](apache-hadoop-connect-excel-power-query.md).
* [Maak verbinding met Azure HDInsight en voer Apache Hive-query's uit met Data Lake Tools voor Visual Studio.](apache-hadoop-visual-studio-tools-get-started.md)
* [Gebruik Azure HDInsight Tool voor Visual Studio Code](../hdinsight-for-vscode.md).
* [Gegevens uploaden naar HDInsight](../hdinsight-upload-data.md)
* [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)
* [Apache Pig gebruiken met HDInsight](hdinsight-use-pig.md)
* [MapReduce-taken gebruiken met HDInsight](hdinsight-use-mapreduce.md)
