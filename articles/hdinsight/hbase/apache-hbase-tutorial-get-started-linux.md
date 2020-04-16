---
title: Zelfstudie - Apache HBase gebruiken in Azure HDInsight
description: Volg deze Apache HBase tutorial om te beginnen met het gebruik van hadoop op HDInsight. Maak tabellen vanuit de HBase-shell en gebruik Hive om query's uit te voeren op de tabellen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/14/2020
ms.openlocfilehash: a601d54ebda074a25a988ac2a115f6418dd5c7ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390265"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Zelfstudie: Apache HBase gebruiken in Azure HDInsight

In deze zelfstudie wordt uitgelegd hoe u een Apache HBase-cluster maakt in Azure HDInsight, HBase-tabellen maakt en querytabellen met Apache Hive gebruikt.  Zie [Overzicht van HDInsight HBase](./apache-hbase-overview.md) voor algemene informatie over HBase.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Apache HBase-cluster maken
> * HBase-tabellen maken en gegevens invoegen
> * Apache Hive gebruiken om Apache HBase op te vragen
> * HBase REST API's gebruiken met Curl
> * De clusterstatus controleren

## <a name="prerequisites"></a>Vereisten

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. De voorbeelden in dit artikel gebruiken de Bash-shell op Windows 10 voor de krulopdrachten. Zie [Windows Subsystem for Linux Installation Guide voor Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) voor installatiestappen.  Andere [Unix schelpen](https://www.gnu.org/software/bash/) zal ook werken.  De krul voorbeelden, met enkele kleine wijzigingen, kan werken op een Windows Command prompt.  Of u de Windows PowerShell-cmdlet [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod)gebruiken.

## <a name="create-apache-hbase-cluster"></a>Apache HBase-cluster maken

De volgende procedure maakt gebruik van een Azure Resource Manager-sjabloon om een HBase-cluster te maken. De sjabloon maakt ook het afhankelijke standaard Azure Storage-account. Zie [Op Linux gebaseerde Hadoop-clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) voor meer inzicht in de parameters die voor deze procedure worden gebruikt en andere methoden voor het maken van clusters.

1. Selecteer de volgende afbeelding om de sjabloon in de Azure-portal te openen. De sjabloon bevindt zich in [Azure quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/)

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Voer in het dialoogvenster **Aangepaste implementatie** de volgende waarden in:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer uw Azure-abonnement dat wordt gebruikt om het cluster te maken.|
    |Resourcegroep|Maak een Azure Resource-beheergroep of gebruik een bestaande groep.|
    |Locatie|Geef de locatie van de resourcegroep op. |
    |Clusternaam|Voer een naam in voor het HBase-cluster.|
    |Gebruikersnaam/Wachtwoord voor clusteraanmeldgegevens|De standaardinlognaam is **beheerder**.|
    |SSH-gebruikersnaam en SSH-wachtwoord|De standaardgebruikersnaam is **sshuser**.|

    Andere parameters zijn optioneel.  

    Elk cluster is afhankelijk van een Azure Storage-account. Nadat u een cluster hebt verwijderd, blijven de gegevens in het opslagaccount. De naam van het standaardopslagaccount voor het cluster is de naam waaraan 'store' is toegevoegd. Het is hardcoded in de sectie sjabloonvariabelen.

3. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** en selecteer vervolgens **Kopen**. Het duurt ongeveer 20 minuten om een cluster te maken.

Nadat een HBase-cluster is verwijderd, kunt u een ander HBase-cluster maken met de dezelfde standaard blob-container. Het nieuwe cluster haalt de HBase-tabellen op die u hebt gemaakt in het oorspronkelijke cluster. Om inconsistenties te voorkomen, wordt u aangeraden de HBase-tabellen uit te schakelen voordat u het cluster verwijdert.

## <a name="create-tables-and-insert-data"></a>Tabellen maken en gegevens invoegen

U kunt SSH gebruiken om verbinding te maken met HBase-clusters en vervolgens [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) gebruiken om HBase-tabellen te maken, gegevens in te voegen, en gegevens te doorzoeken.

Voor de meeste mensen worden de gegevens weergegeven in een tabelindeling:

![HDInsight Apache HBase tabelgegevens](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

In HBase (een implementatie van [Cloud BigTable)](https://cloud.google.com/bigtable/)lijken dezelfde gegevens op:

![HDInsight Apache HBase BigTable-gegevens](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**De HBase-shell gebruiken**

1. Gebruik `ssh` de opdracht om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande `CLUSTERNAME` opdracht door de naam van uw cluster te vervangen en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik `hbase shell` de opdracht om de interactieve shell Van HBase te starten. Voer de volgende opdracht in uw SSH-verbinding in:

    ```bash
    hbase shell
    ```

1. Gebruik `create` de opdracht om een HBase-tabel met families met twee kolommen te maken. De tabel- en kolomnamen zijn hoofdlettergevoelig. Voer de volgende opdracht in:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Gebruik `list` de opdracht om alle tabellen in HBase weer te geven. Voer de volgende opdracht in:

    ```hbase
    list
    ```

1. De `put` opdracht gebruiken om waarden in een bepaalde kolom in een bepaalde rij in een bepaalde tabel in te voegen. Voer de volgende opdrachten in:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Gebruik `scan` de opdracht om `Contacts` de tabelgegevens te scannen en terug te sturen. Voer de volgende opdracht in:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Apache Hadoop HBase shell](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. De `get` opdracht gebruiken om de inhoud van een rij op te halen. Voer de volgende opdracht in:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    U ziet vergelijkbare resultaten `scan` als het gebruik van de opdracht omdat er slechts één rij is.

    Zie [Inleiding tot Apache HBase-schemaontwerp](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf)voor meer informatie over het hbase-tabelschema. Raadpleeg de [Snelzoekgids voor Apache HBase](https://hbase.apache.org/book.html#quickstart) voor meer HBase-opdrachten.

1. Gebruik `exit` de opdracht om de interactieve hbase-shell te stoppen. Voer de volgende opdracht in:

    ```hbaseshell
    exit
    ```

**Gegevens bulksgewijs laden in de HBase-tabel met contacten**

U kunt in HBase verschillende methoden gebruiken om gegevens in tabellen te laden.  Zie [Bulk loading](https://hbase.apache.org/book.html#arch.bulk.load) (Bulkgsgewijs laden) voor meer informatie.

Een voorbeeld van een gegevensbestand is te vinden in een openbare Azure Blob-container, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  De inhoud van het gegevensbestand is:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

U kunt een tekstbestand maken en het bestand desgewenst uploaden naar uw eigen opslagaccount. Zie [Gegevens uploaden voor Apache Hadoop-taken in HDInsight voor](../hdinsight-upload-data.md)de instructies.

Deze procedure `Contacts` maakt gebruik van de HBase-tabel die u in de laatste procedure hebt gemaakt.

1. Voer vanaf uw geopende ssh-verbinding de volgende opdracht uit om het gegevensbestand om te zetten in StoreFiles en op te slaan op een relatief pad dat is opgegeven door `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Voer de volgende opdracht uit `/example/data/storeDataFileOutput` om de gegevens naar de HBase-tabel te uploaden:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. U de HBase-shell `scan` openen en de opdracht gebruiken om de inhoud van de tabel weer te geven.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Apache Hive gebruiken om Apache HBase op te vragen

U gegevens in HBase-tabellen opvragen met [Apache Hive](https://hive.apache.org/). In dit gedeelte maakt u een Hive-tabel die is toegewezen aan de HBase-tabel en deze gebruikt om een query voor de gegevens in uw HBase-tabel uit te voeren.

1. Gebruik de volgende opdracht om Beeline te starten via de volgende opdracht:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Zie voor meer informatie over Beeline [Hive gebruiken met Hadoop in HDInsight met Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Voer het volgende [HiveQL-script](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) uit om een Hive-tabel te maken die wordt toegewezen aan de HBase-tabel. Zorg ervoor dat u de voorbeeldtabel hebt gemaakt waarnaar eerder in dit artikel is verwezen met de shell HBase voordat u deze instructie uitvoert.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Voer het volgende HiveQL-script uit om een query uit te voeren voor de gegevens in de HBase-tabel:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Als u Beeline `!exit`wilt verlaten, gebruikt u .

1. Gebruik . `exit`

## <a name="use-hbase-rest-apis-using-curl"></a>HBase REST API's gebruiken met Curl

De REST API is beveiligd via [basisverificatie](https://en.wikipedia.org/wiki/Basic_access_authentication). U moet aanvragen altijd uitvoeren via een beveiligde HTTP-verbinding (HTTPS). Zo zorgt u ervoor dat uw referenties veilig worden verzonden naar de server.

1. Stel omgevingsvariabele in voor gebruiksgemak. Bewerk de onderstaande opdrachten `MYPASSWORD` door te vervangen door het wachtwoord voor het inloggen van het cluster. Vervang `MYCLUSTERNAME` door de naam van uw HBase-cluster. Voer vervolgens de opdrachten in.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Gebruik de volgende opdracht om een lijst met bestaande HBase-tabellen weer te geven:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Gebruik de volgende opdracht om een nieuwe HBase-tabel met twee kolomfamilies te maken:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Het schema wordt opgegeven in de JSON-indeling.
1. Gebruik de volgende opdracht om enkele gegevens in te voegen:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Base64 codeert de waarden die zijn opgegeven in de -d-schakelaar. In het voorbeeld:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Persoonlijk: Naam
   * Sm9obiBEb2xl: Joep Davids

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) maakt het mogelijk om meerdere waarden (in batch) in te voegen.

1. Gebruik de volgende opdracht om een rij te verkrijgen:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Zie [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_rest) (Snelzoekgids voor Apache HBase) voor meer informatie over HBase REST.

> [!NOTE]  
> Thrift wordt niet ondersteund door HBase in HDInsight.
>
> Wanneer u Curl of een andere REST-communicatie gebruikt met WebHCat, moet u de aanvragen verifiëren door de gebruikersnaam en het wachtwoord voor de beheerder van het HDInsight-cluster op te geven. U moet ook de clusternaam gebruiken als onderdeel van de URI (Uniform Resource Identifier) die wordt gebruikt om de aanvragen naar de server te verzenden:
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Het antwoord dat u ontvangt, is vergelijkbaar met het volgende antwoord:
>   
>        {"status":"ok","version":"v1"}

## <a name="check-cluster-status"></a>De clusterstatus controleren

HBase in HDInsight wordt geleverd met een webgebruikersinterface voor het bewaken van clusters. Met de webgebruikersinterface kunt u statistieken of informatie over regio's aanvragen.

**De HBase-hoofdinterface openen**

1. Meld u aan bij de `https://CLUSTERNAME.azurehdinsight.net` Ambari Web UI op de plaats waar `CLUSTERNAME` de naam van uw HBase-cluster staat.

1. Selecteer **HBase** in het linkermenu.

1. Selecteer **Snelle koppelingen** boven aan de pagina, wijs de actieve koppeling voor het nodeknooppunt zookeeper aan en selecteer vervolgens de **HBase-master-gebruikersinterface**.  De interface wordt in een nieuw browsertabblad geopend:

   ![HDInsight Apache HBase HMaster UI](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   De HBase-hoofdinterface bevat de volgende onderdelen:

   - regioservers
   - back-upmasters
   - tabellen
   - taken
   - softwarekenmerken

## <a name="clean-up-resources"></a>Resources opschonen

Om inconsistenties te voorkomen, wordt u aangeraden de HBase-tabellen uit te schakelen voordat u het cluster verwijdert. U de opdracht `disable 'Contacts'`HBase gebruiken. Als u deze toepassing verder niet meer gebruikt, verwijdert u het HBase-cluster dat u hebt gemaakt, via de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Typ **HDInsight** in het **Zoekvak** bovenaan.
1. Selecteer onder **Services** de optie **HDInsight-clusters**.
1. Klik in de lijst met HDInsight-clusters die wordt weergegeven, op de **...** naast het cluster dat u voor deze zelfstudie hebt gemaakt.
1. Klik **op Verwijderen**. Klik **op Ja**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Apache HBase-cluster maken. En hoe u tabellen maakt en de gegevens in die tabellen bekijkt vanuit de HBase-shell. U hebt ook geleerd hoe u een Hive-query op gegevens in HBase-tabellen gebruiken. En hoe u de HBase C# REST API's gebruiken om een HBase-tabel te maken en gegevens uit de tabel op te halen. Voor meer informatie zie:

> [!div class="nextstepaction"]
> [HDInsight HBase overzicht](./apache-hbase-overview.md)