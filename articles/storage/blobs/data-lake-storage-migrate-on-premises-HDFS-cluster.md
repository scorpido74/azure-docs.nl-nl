---
title: Gebruik Azure Data Box om gegevens van on-premises HDFS-opslag te migreren naar Azure Storage
description: Gegevens migreren van een on-premises HDFS-opslag naar Azure Storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 508c67f73bc0e11330b5772b1c1ba3f9bee5e231
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255683"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Gebruik Azure Data Box om gegevens van een on-premises HDFS-Store te migreren naar Azure Storage

U kunt gegevens migreren van een on-premises HDFS-opslag van uw Hadoop-cluster naar Azure Storage (Blob Storage of Data Lake Storage Gen2) door gebruik te maken van een Data Box apparaat. U kunt kiezen uit een 80-TB Data Box of een 770-TB Data Box Heavy.

Dit artikel helpt u bij het uitvoeren van deze taken:

> [!div class="checklist"]
> * Bereid u voor op het migreren van uw gegevens.
> * Kopieer uw gegevens naar een Data Box of een Data Box Heavy apparaat.
> * Verzend het apparaat terug naar micro soft.
> * Verplaats de gegevens naar Data Lake Storage Gen2.

## <a name="prerequisites"></a>Vereisten

U hebt deze dingen nodig om de migratie te volt ooien.

* Twee opslag accounts; een met een hiërarchische naam ruimte die niet is ingeschakeld.

* Een on-premises Hadoop-cluster dat de bron gegevens bevat.

* Een [Azure data Box-apparaat](https://azure.microsoft.com/services/storage/databox/).

  * [Bestel uw data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) of [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Denk tijdens het best Ellen van uw apparaat aan dat u een opslag account kiest waarvoor **geen** hiërarchische naam ruimten zijn ingeschakeld. Dit komt doordat Data Box-apparaten nog geen rechtstreekse opname in Azure Data Lake Storage Gen2 ondersteunen. U moet een opslag account kopiëren en vervolgens een tweede kopie naar het ADLS Gen2-account. Instructies hiervoor vindt u in de volgende stappen.

  * Kabel en sluit uw [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) of [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) aan op een on-premises netwerk.

Als u klaar bent, kunt u beginnen.

## <a name="copy-your-data-to-a-data-box-device"></a>Uw gegevens naar een Data Box apparaat kopiëren

Als uw gegevens in één Data Box apparaat passen, kopieert u de gegevens naar het Data Box-apparaat. 

Als uw gegevens grootte de capaciteit van het Data Box apparaat overschrijdt, gebruikt u de [optionele procedure om de gegevens te splitsen op meerdere data Box apparaten](#appendix-split-data-across-multiple-data-box-devices) en voert u deze stap uit. 

Als u de gegevens van uw on-premises HDFS-archief wilt kopiëren naar een Data Box apparaat, stelt u een aantal zaken in en gebruikt u vervolgens het hulp programma [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) .

Volg deze stappen om gegevens te kopiëren via de REST-Api's van blob/object Storage naar uw Data Box-apparaat. Met de REST API-interface wordt het apparaat weer gegeven als een HDFS-archief naar uw cluster.

1. Voordat u de gegevens via REST kopieert, identificeert u de beveiligings-en verbindings primitieven om verbinding te maken met de REST-interface op de Data Box of Data Box Heavy. Meld u aan bij de lokale web-UI van Data Box en ga naar de pagina **verbinding maken en kopiëren** . Zoek in het Azure-opslag account voor uw apparaat, onder **toegangs instellingen**, naar en selecteer **rest**.

    ![De pagina verbinding maken en kopiëren](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Kopieer in het dialoog venster opslag account voor toegang en gegevens uploaden het **BLOB service-eind punt** en de sleutel van het **opslag account**. Laat het `https://` en de afsluitende slash weg van het eind punt van de BLOB-service.

    In dit geval is het eind punt: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Het host-gedeelte van de URI die u wilt gebruiken, is: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Zie How to [Connect to rest over http](/azure/databox/data-box-deploy-copy-data-via-rest)(Engelstalig) voor een voor beeld. 

     ![Dialoog venster toegang tot opslag account en gegevens uploaden](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Voeg het eind punt en het IP-adres van het Data Box-of Data Box Heavy knooppunt toe aan `/etc/hosts` op elk knoop punt.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Als u een ander mechanisme voor DNS gebruikt, moet u ervoor zorgen dat het Data Box-eind punt kan worden omgezet.

4. Stel de shell-variabele `azjars` in op de locatie van de `hadoop-azure` en de `azure-storage` jar-bestanden. U vindt deze bestanden in de map Hadoop-installatie.

    Als u wilt bepalen of deze bestanden bestaan, gebruikt u de volgende opdracht: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Vervang de tijdelijke aanduiding voor @no__t 0 door het pad naar de map waarin u Hadoop hebt geïnstalleerd. Zorg ervoor dat u volledig gekwalificeerde paden gebruikt.

    Voorbeelden:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Maak de opslag container die u wilt gebruiken voor het kopiëren van gegevens. U moet ook een doelmap opgeven als onderdeel van deze opdracht. Dit kan op dit moment een dummy doel directory zijn.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Vervang de tijdelijke aanduiding `<blob_service_endpoint>` door de naam van het eind punt van de BLOB-service.

    * Vervang de tijdelijke aanduiding `<account_key>` door de toegangs sleutel van uw account.

    * Vervang de tijdelijke aanduiding `<container-name>` door de naam van de container.

    * Vervang de tijdelijke aanduiding `<destination_directory>` door de naam van de map waarnaar u uw gegevens wilt kopiëren.

6. Voer een lijst opdracht uit om ervoor te zorgen dat uw container en map zijn gemaakt.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Vervang de tijdelijke aanduiding `<blob_service_endpoint>` door de naam van het eind punt van de BLOB-service.

   * Vervang de tijdelijke aanduiding `<account_key>` door de toegangs sleutel van uw account.

   * Vervang de tijdelijke aanduiding `<container-name>` door de naam van de container.

7. Kopieer gegevens van de Hadoop HDFS naar Data Box Blob-opslag naar de container die u eerder hebt gemaakt. Als de map waarnaar u kopieert, niet wordt gevonden, wordt deze automatisch gemaakt met de opdracht.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Vervang de tijdelijke aanduiding `<blob_service_endpoint>` door de naam van het eind punt van de BLOB-service.

    * Vervang de tijdelijke aanduiding `<account_key>` door de toegangs sleutel van uw account.

    * Vervang de tijdelijke aanduiding `<container-name>` door de naam van de container.

    * Vervang de tijdelijke aanduiding `<exlusion_filelist_file>` door de naam van het bestand dat de lijst met uitsluitingen van bestanden bevat.

    * Vervang de tijdelijke aanduiding `<source_directory>` door de naam van de map die de gegevens bevat die u wilt kopiëren.

    * Vervang de tijdelijke aanduiding `<destination_directory>` door de naam van de map waarnaar u uw gegevens wilt kopiëren.

    De optie `-libjars` wordt gebruikt om de `hadoop-azure*.jar` en de afhankelijke `azure-storage*.jar`-bestanden beschikbaar te maken voor `distcp`. Dit kan al voor sommige clusters optreden.

    In het volgende voor beeld ziet u hoe de `distcp`-opdracht wordt gebruikt om gegevens te kopiëren.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    De Kopieer snelheid verbeteren:

    * Wijzig het aantal mappers. (In het bovenstaande voor beeld wordt gebruikgemaakt van `m` = 4 mappers.)

    * Probeer meerdere `distcp` parallel uit te voeren.

    * Houd er rekening mee dat grote bestanden beter worden uitgevoerd dan kleine bestanden.

## <a name="ship-the-data-box-to-microsoft"></a>De Data Box verzenden naar micro soft

Volg deze stappen om het Data Box-apparaat voor te bereiden en te verzenden naar micro soft.

1. Eerst [voorbereiding voor verzending op uw data box of Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Nadat de voor bereiding van het apparaat is voltooid, kunt u de stuk lijst bestanden downloaden. U gebruikt deze stuk lijst of manifest bestanden later om te controleren of de gegevens zijn geüpload naar Azure.

3. Sluit het apparaat af en verwijder de kabels.

4. Maak een afspraak met UPS om het pakket op te laten halen.

    * Zie [uw data Box verzenden](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)voor data Box apparaten.

    * Zie [uw data Box Heavy verzenden](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)voor data Box Heavy apparaten.

5. Nadat micro soft uw apparaat heeft ontvangen, is het verbonden met het Data Center-netwerk en worden de gegevens geüpload naar het opslag account dat u hebt opgegeven (met hiërarchische naam ruimten uitgeschakeld) wanneer u de volg orde van het apparaat hebt geplaatst. Controleer de stuk lijst bestanden op basis waarvan al uw gegevens naar Azure worden geüpload. U kunt deze gegevens nu verplaatsen naar een Data Lake Storage Gen2 Storage-account.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>De gegevens verplaatsen naar Azure Data Lake Storage Gen2

U hebt de gegevens al in uw Azure Storage-account. Nu kopieert u de gegevens naar uw Azure Data Lake-opslag account en past u toegangs machtigingen toe voor bestanden en mappen.

> [!NOTE]
> Deze stap is nodig als u Azure Data Lake Storage Gen2 gebruikt als uw gegevens archief. Als u alleen een Blob Storage-account zonder hiërarchische naam ruimte gebruikt als uw gegevens opslag, kunt u deze sectie overs Laan.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Gegevens kopiëren naar het Azure Data Lake Storage gen 2-account

U kunt gegevens kopiëren met behulp van Azure Data Factory of door gebruik te maken van uw Hadoop-cluster op basis van Azure.

* Zie [Azure Data Factory om gegevens te verplaatsen naar ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2)om Azure Data Factory te gebruiken. Zorg ervoor dat u **Azure Blob Storage** als bron opgeeft.

* Als u uw Hadoop-cluster op basis van Azure wilt gebruiken, voert u de volgende DistCp-opdracht uit:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Vervang de tijdelijke aanduidingen `<source_account>` en `<dest_account>` door de namen van de bron-en doel opslag accounts.

    * Vervang de tijdelijke aanduidingen `<source_container>` en `<dest_container>` door de namen van de bron-en doel containers.

    * Vervang de tijdelijke aanduidingen `<source_path>` en `<dest_path>` door de paden van de bron-en doel directory.

    * Vervang de tijdelijke aanduiding `<source_account_key>` door de toegangs sleutel van het opslag account dat de gegevens bevat.

    Met deze opdracht kopieert u zowel gegevens als meta gegevens van uw opslag account naar uw Data Lake Storage Gen2-opslag account.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Een service-principal maken voor uw Azure Data Lake Storage Gen2-account

Als u een Service-Principal wilt maken, raadpleegt u [How to: gebruik de portal om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Wanneer u de stappen uitvoert in de sectie [De toepassing toewijzen aan een rol](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) van het artikel, moet u ervoor zorgen dat de rol **Gegevensbijdrager voor opslagblob** is toegewezen aan de service-principal.

* Bij het uitvoeren van de stappen in de sectie [waarden ophalen voor aanmelden in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) het artikel, toepassings-id en client geheime waarden opslaan in een tekst bestand. U hebt deze binnenkort nodig.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Een lijst met gekopieerde bestanden met hun machtigingen genereren

Voer in het on-premises Hadoop-cluster de volgende opdracht uit:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Met deze opdracht wordt een lijst met gekopieerde bestanden met hun machtigingen gegenereerd.

> [!NOTE]
> Afhankelijk van het aantal bestanden in HDFS kan deze opdracht veel tijd in beslag nemen.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Een lijst met identiteiten genereren en deze toewijzen aan Azure Active Directory-identiteiten (toevoegen)

1. Down load het script `copy-acls.py`. Zie de [Help-scripts downloaden en het Edge-knoop punt instellen om deze uit te voeren](#download-helper-scripts) in dit artikel.

2. Voer deze opdracht uit om een lijst met unieke identiteiten te genereren.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Met dit script wordt een bestand met de naam `id_map.json` gegenereerd dat de identiteiten bevat die u nodig hebt om toe te wijzen aan identiteiten op basis van een toepassing.

3. Open het `id_map.json`-bestand in een tekst editor.

4. Voor elk JSON-object dat in het bestand wordt weer gegeven, moet u het kenmerk `target` van een AAD User Principal Name (UPN) of ObjectId (OID) met de juiste toegewezen identiteit bijwerken. Wanneer u klaar bent, slaat u het bestand op. U hebt dit bestand nodig in de volgende stap.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Machtigingen Toep assen op gekopieerde bestanden en identiteits toewijzingen Toep assen

Voer deze opdracht uit om machtigingen toe te passen op de gegevens die u hebt gekopieerd naar het Data Lake Storage Gen2-account:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Vervang de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

* Vervang de tijdelijke aanduiding `<container-name>` door de naam van de container.

* Vervang de tijdelijke aanduidingen `<application-id>` en `<client-secret>` door de toepassings-ID en het client geheim dat u hebt verzameld tijdens het maken van de Service-Principal.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Bijlage: gegevens splitsen op meerdere Data Box apparaten

Voordat u uw gegevens naar een Data Box apparaat verplaatst, moet u sommige Help-scripts downloaden, ervoor zorgen dat uw gegevens zijn geordend op een Data Box apparaat en overbodige bestanden uitsluiten.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Help-scripts downloaden en het Edge-knoop punt instellen om ze uit te voeren

1. Voer de volgende opdracht uit vanaf uw rand-of hoofd knooppunt van uw on-premises Hadoop-cluster:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Met deze opdracht wordt de GitHub-opslag plaats met de Help-scripts gekloond.

2. Zorg ervoor dat het [JQ](https://stedolan.github.io/jq/) -pakket op uw lokale computer is geïnstalleerd.

   ```bash
   
   sudo apt-get install jq
   ```

3. Installeer het python-pakket [aanvragen](http://docs.python-requests.org/en/master/) .

   ```bash
   
   pip install requests
   ```

4. Stel uitvoerings machtigingen in voor de vereiste scripts.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Zorg ervoor dat uw gegevens zijn geordend zodat deze passen op een Data Box apparaat

Als de grootte van uw gegevens groter is dan de grootte van één Data Box apparaat, kunt u bestanden splitsen in groepen die u op meerdere Data Box apparaten kunt opslaan.

Als uw gegevens niet groter zijn dan de grootte van een Data Box apparaat, kunt u door gaan naar de volgende sectie.

1. Voer met verhoogde machtigingen het `generate-file-list`-script uit dat u hebt gedownload door de instructies in de vorige sectie te volgen.

   Hier volgt een beschrijving van de opdracht parameters:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Kopieer de gegenereerde bestands lijsten naar HDFS zodat ze toegankelijk zijn voor de [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) -taak.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Onnodige bestanden uitsluiten

U moet enkele mappen uitsluiten van de DisCp-taak. U kunt bijvoorbeeld mappen uitsluiten die status informatie bevatten waardoor het cluster actief blijft.

Maak in het on-premises Hadoop-cluster waar u van plan bent de DistCp-taak te initiëren een bestand dat de lijst met mappen opgeeft die u wilt uitsluiten.

Hier volgt een voorbeeld:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Data Lake Storage Gen2 werkt met HDInsight-clusters. Zie [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
