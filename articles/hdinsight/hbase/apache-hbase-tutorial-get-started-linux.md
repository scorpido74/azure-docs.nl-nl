---
title: Zelf studie-Apache HBase gebruiken in azure HDInsight
description: Volg deze Apache HBase-zelf studie om Hadoop op HDInsight te gaan gebruiken. Maak tabellen vanuit de HBase-shell en gebruik Hive om query's uit te voeren op de tabellen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/14/2020
ms.openlocfilehash: a601d54ebda074a25a988ac2a115f6418dd5c7ee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81390265"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Zelf studie: Apache HBase gebruiken in azure HDInsight

In deze zelf studie ziet u hoe u een Apache HBase-cluster maakt in azure HDInsight, HBase-tabellen en query tabellen maakt met behulp van Apache Hive.  Zie [Overzicht van HDInsight HBase](./apache-hbase-overview.md) voor algemene informatie over HBase.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Apache HBase-cluster maken
> * HBase-tabellen maken en gegevens invoegen
> * Apache Hive gebruiken voor het opvragen van Apache HBase
> * HBase REST API's gebruiken met Curl
> * De clusterstatus controleren

## <a name="prerequisites"></a>Vereisten

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. In de voor beelden in dit artikel wordt gebruikgemaakt van de bash-shell in Windows 10 voor de krul-opdrachten. Zie het [Windows-subsysteem voor Linux-installatie handleiding voor Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) voor installatie stappen.  Andere [UNIX-shells](https://www.gnu.org/software/bash/) worden ook gebruikt.  De krul-voor beelden, met een kleine wijziging, kunnen werken aan een Windows-opdracht prompt.  U kunt ook de Windows Power shell [-cmdlet invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod)gebruiken.

## <a name="create-apache-hbase-cluster"></a>Apache HBase-cluster maken

In de volgende procedure wordt een Azure Resource Manager-sjabloon gebruikt om een HBase-cluster te maken. Met de sjabloon wordt ook het afhankelijke standaard Azure Storage-account gemaakt. Zie [Op Linux gebaseerde Hadoop-clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) voor meer inzicht in de parameters die voor deze procedure worden gebruikt en andere methoden voor het maken van clusters.

1. Selecteer de volgende afbeelding om de sjabloon te openen in de Azure Portal. De sjabloon bevindt zich in [Azure Quick](https://azure.microsoft.com/resources/templates/)start-sjablonen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Voer in het dialoog venster **aangepaste implementatie** de volgende waarden in:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer uw Azure-abonnement dat wordt gebruikt om het cluster te maken.|
    |Resourcegroep|Maak een Azure-resource beheer groep of gebruik een bestaande.|
    |Locatie|Geef de locatie van de resource groep op. |
    |Clusternaam|Voer een naam in voor het HBase-cluster.|
    |Gebruikersnaam/Wachtwoord voor clusteraanmeldgegevens|De standaard aanmeldings naam is **admin**.|
    |SSH-gebruikersnaam en SSH-wachtwoord|De standaardgebruikersnaam is **sshuser**.|

    Andere parameters zijn optioneel.  

    Elk cluster is afhankelijk van een Azure Storage-account. Nadat u een cluster hebt verwijderd, blijven de gegevens in het opslag account. De naam van het standaardopslagaccount voor het cluster is de naam waaraan 'store' is toegevoegd. Het is hardcoded in de sectie sjabloon variabelen.

3. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** en selecteer vervolgens **Kopen**. Het duurt ongeveer 20 minuten om een cluster te maken.

Nadat een HBase-cluster is verwijderd, kunt u een ander HBase-cluster maken met de dezelfde standaard blob-container. Het nieuwe cluster haalt de HBase-tabellen op die u hebt gemaakt in het oorspronkelijke cluster. Om inconsistenties te voorkomen, wordt u aangeraden de HBase-tabellen uit te schakelen voordat u het cluster verwijdert.

## <a name="create-tables-and-insert-data"></a>Tabellen maken en gegevens invoegen

U kunt SSH gebruiken om verbinding te maken met HBase-clusters en vervolgens [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) gebruiken om HBase-tabellen te maken, gegevens in te voegen, en gegevens te doorzoeken.

Voor de meeste mensen worden de gegevens weergegeven in een tabelindeling:

![Gegevens in tabel vorm van HDInsight Apache HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

In HBase (een implementatie van [Bigtable](https://cloud.google.com/bigtable/)), zien dezelfde gegevens er als volgt uit:

![Gegevens van HDInsight Apache HBase BigTable](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**De HBase-shell gebruiken**

1. Gebruik `ssh` de opdracht om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande opdracht door de `CLUSTERNAME` naam van uw cluster te vervangen en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik `hbase shell` de opdracht om de HBase-interactieve shell te starten. Voer de volgende opdracht in voor uw SSH-verbinding:

    ```bash
    hbase shell
    ```

1. Gebruik `create` de opdracht om een HBase-tabel met twee kolom families te maken. De tabel-en kolom namen zijn hoofdletter gevoelig. Voer de volgende opdracht in:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Gebruik `list` de opdracht om alle tabellen in HBase weer te geven. Voer de volgende opdracht in:

    ```hbase
    list
    ```

1. Gebruik `put` de opdracht om waarden in een opgegeven kolom in een opgegeven rij in een bepaalde tabel in te voegen. Voer de volgende opdrachten in:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Gebruik `scan` de opdracht om de `Contacts` tabel gegevens te scannen en te retour neren. Voer de volgende opdracht in:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Apache Hadoop HBase-shell](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Gebruik `get` de opdracht om de inhoud van een rij op te halen. Voer de volgende opdracht in:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    U ziet vergelijk bare resultaten als met `scan` de opdracht omdat er maar één rij is.

    Zie [Introduction to Apache HBase schema design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf)(Engelstalig) voor meer informatie over het HBase-tabel schema. Raadpleeg de [Snelzoekgids voor Apache HBase](https://hbase.apache.org/book.html#quickstart) voor meer HBase-opdrachten.

1. Gebruik `exit` de opdracht om de HBase-interactieve shell te stoppen. Voer de volgende opdracht in:

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

U kunt een tekstbestand maken en het bestand desgewenst uploaden naar uw eigen opslagaccount. Zie [gegevens uploaden voor Apache Hadoop-taken in HDInsight](../hdinsight-upload-data.md)voor instructies.

Deze procedure maakt gebruik `Contacts` van de HBase-tabel die u in de laatste procedure hebt gemaakt.

1. Voer vanuit uw open SSH-verbinding de volgende opdracht uit om het gegevens bestand te transformeren naar transformeren naar en op te slaan op een `Dimporttsv.bulk.output`relatief pad dat is opgegeven door.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Voer de volgende opdracht uit om de gegevens te `/example/data/storeDataFileOutput` uploaden van naar de HBase-tabel:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. U kunt de HBase-shell openen en de `scan` -opdracht gebruiken om de inhoud van de tabel weer te geven.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Apache Hive gebruiken voor het opvragen van Apache HBase

U kunt gegevens in HBase-tabellen opvragen door gebruik te maken van [Apache Hive](https://hive.apache.org/). In dit gedeelte maakt u een Hive-tabel die is toegewezen aan de HBase-tabel en deze gebruikt om een query voor de gegevens in uw HBase-tabel uit te voeren.

1. Gebruik vanuit uw open SSH-verbinding de volgende opdracht om Beeline te starten:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Zie voor meer informatie over Beeline [Hive gebruiken met Hadoop in HDInsight met Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Voer het volgende [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) -script uit om een Hive-tabel te maken die is toegewezen aan de tabel HBase. Zorg ervoor dat u de voorbeeld tabel hebt gemaakt met behulp van de HBase-shell voordat u deze instructie uitvoert, eerder in dit artikel wordt verwezen.

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

1. Gebruik `!exit`om Beeline af te sluiten.

1. Als u uw SSH-verbinding wilt `exit`afsluiten, gebruikt u.

## <a name="use-hbase-rest-apis-using-curl"></a>HBase REST API's gebruiken met Curl

De REST API is beveiligd via [basisverificatie](https://en.wikipedia.org/wiki/Basic_access_authentication). U moet aanvragen altijd uitvoeren via een beveiligde HTTP-verbinding (HTTPS). Zo zorgt u ervoor dat uw referenties veilig worden verzonden naar de server.

1. Stel de omgevings variabele in voor gebruiks gemak. Bewerk de onderstaande opdrachten door te `MYPASSWORD` vervangen door het wacht woord voor de cluster aanmelding. Vervang `MYCLUSTERNAME` door de naam van uw HBase-cluster. Voer vervolgens de opdrachten in.

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

    Base64 de waarden die zijn opgegeven in de switch-d, worden gecodeerd. In het voorbeeld:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ = =: persoonlijk: naam
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

1. Meld u aan bij de Ambari- `https://CLUSTERNAME.azurehdinsight.net` webgebruikersinterface op waar `CLUSTERNAME` de naam van uw HBase-cluster is.

1. Selecteer **HBase** in het menu links.

1. Selecteer **snelle koppelingen** boven aan de pagina, wijs de actieve Zookeeper-knooppunt koppeling aan en selecteer vervolgens **HBase Master gebruikers interface**.  De interface wordt in een nieuw browsertabblad geopend:

   ![Gebruikers interface voor HDInsight Apache HBase HMaster](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   De HBase-hoofdinterface bevat de volgende onderdelen:

   - regioservers
   - back-upmasters
   - tabellen
   - taken
   - softwarekenmerken

## <a name="clean-up-resources"></a>Resources opschonen

Om inconsistenties te voorkomen, wordt u aangeraden de HBase-tabellen uit te schakelen voordat u het cluster verwijdert. U kunt de HBase-opdracht `disable 'Contacts'`gebruiken. Als u deze toepassing verder niet meer gebruikt, verwijdert u het HBase-cluster dat u hebt gemaakt, via de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Typ **HDInsight** in het **Zoekvak** bovenaan.
1. Selecteer onder **Services** de optie **HDInsight-clusters**.
1. Klik in de lijst met HDInsight-clusters die wordt weergegeven, op de **...** naast het cluster dat u voor deze zelfstudie hebt gemaakt.
1. Klik op **verwijderen**. Klik op **Ja**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een Apache HBase-cluster maakt. En het maken van tabellen en het weer geven van de gegevens in deze tabellen vanuit de HBase-shell. U hebt ook geleerd hoe u een Hive-query kunt gebruiken op gegevens in HBase-tabellen. En hoe u de HBase C# REST-Api's kunt gebruiken om een HBase-tabel te maken en gegevens op te halen uit de tabel. Voor meer informatie zie:

> [!div class="nextstepaction"]
> [Overzicht van HDInsight-HBase](./apache-hbase-overview.md)