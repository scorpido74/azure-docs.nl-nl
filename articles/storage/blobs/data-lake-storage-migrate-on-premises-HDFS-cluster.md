---
title: Migreren van de On-prem HDFS-winkel naar Azure Storage met Azure-gegevensvak
description: Gegevens migreren van een on-premises HDFS-winkel naar Azure Storage
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: c0c6a8637223727a9b0c88245d939605f6a8530e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301997"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Migreren van de On-prem HDFS-winkel naar Azure Storage met Azure-gegevensvak

U gegevens uit een on-premises HDFS-winkel van uw Hadoop-cluster migreren naar Azure Storage (blobstorage of Data Lake Storage Gen2) met behulp van een Data Box-apparaat. U kiezen uit een 80-TB Data Box of een 770-TB Data Box Heavy.

In dit artikel u deze taken uitvoeren:

> [!div class="checklist"]
> * Bereid u voor om uw gegevens te migreren.
> * Kopieer uw gegevens naar een gegevensvak of een databox zwaar apparaat.
> * Verzend het apparaat terug naar Microsoft.
> * Toegangsmachtigingen toepassen op bestanden en mappen (alleen Data Lake Storage Gen2)

## <a name="prerequisites"></a>Vereisten

Je hebt deze dingen nodig om de migratie te voltooien.

* Een Azure Storage-account.

* Een on-premises Hadoop-cluster met uw brongegevens.

* Een [Azure Data Box-apparaat](https://azure.microsoft.com/services/storage/databox/).

  * [Bestel uw Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) of [Data Box Heavy.](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered) 

  * Kabel en sluit uw [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) of Data [Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) aan op een on-premises netwerk.

Als je er klaar voor bent, laten we beginnen.

## <a name="copy-your-data-to-a-data-box-device"></a>Uw gegevens naar een gegevensvakapparaat kopiëren

Als uw gegevens in één Data Box-apparaat passen, kopieert u de gegevens naar het Data Box-apparaat. 

Als uw gegevensgrootte de capaciteit van het gegevensvakapparaat overschrijdt, gebruikt u de [optionele procedure om de gegevens over meerdere Gegevensbox-apparaten te splitsen](#appendix-split-data-across-multiple-data-box-devices) en deze stap uit te voeren. 

Als u de gegevens van uw on-premises HDFS-winkel wilt kopiëren naar een Data Box-apparaat, stelt u een aantal dingen in en gebruikt u vervolgens het gereedschap [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

Volg deze stappen om gegevens via de REST API's van Blob/Object-opslag naar uw Data Box-apparaat te kopiëren. De REST API-interface zorgt ervoor dat het apparaat wordt weergegeven als een HDFS-archief voor uw cluster.

1. Voordat u de gegevens via REST kopieert, identificeert u de beveiligings- en verbindingsprimitieven om verbinding te maken met de REST-interface op de gegevensbox of gegevensvak zwaar. Meld u aan bij de lokale webgebruikersinterface van gegevensvak en ga naar Verbinding maken en de pagina **kopiëren.** Zoek en selecteer **REST** **onder**het Azure-opslagaccount voor uw apparaat.

    ![Pagina 'Verbinden en kopiëren'](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Kopieer in het dialoogvenster Access-opslagaccount en uploadgegevens het eindpunt van de **Blob-service** en de **accountsleutel Opslag**. Laat in het eindpunt van `https://` de blobservice de en de slepende slash weg.

    In dit geval is het `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`eindpunt: . Het hostgedeelte van de URI dat `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`u zult gebruiken, is: . Zie bijvoorbeeld hoe u [verbinding maken met REST via http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Dialoogvenster 'Toegangsopslagaccount en uploadgegevens'](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Voeg het eindpunt en het IP-adres van het `/etc/hosts` gegevensvak of het gegevensvak zwaar knooppunt toe aan elk knooppunt.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Als u een ander mechanisme voor DNS gebruikt, moet u ervoor zorgen dat het eindpunt van de Data Box kan worden opgelost.

4. Stel de `azjars` shell variabele op `hadoop-azure` `azure-storage` de locatie van de en jar bestanden. U deze bestanden vinden onder de Hadoop installatie directory.

    Als u wilt bepalen of deze `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`bestanden bestaan, gebruikt u de volgende opdracht: . Vervang `<hadoop_install_dir>` de tijdelijke aanduiding door het pad naar de map waar u Hadoop hebt geïnstalleerd. Zorg ervoor dat u volledig gekwalificeerde paden gebruikt.

    Voorbeelden:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Maak de opslagcontainer die u wilt gebruiken voor gegevenskopiëren. U moet ook een doelmap opgeven als onderdeel van deze opdracht. Dit kan een dummy bestemming directory op dit punt.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Vervang `<blob_service_endpoint>` de tijdelijke aanduiding door de naam van het eindpunt van uw blobservice.

    * Vervang `<account_key>` de tijdelijke aanduiding door de toegangssleutel van uw account.

    * Vervang `<container-name>` de tijdelijke aanduiding door de naam van uw container.

    * Vervang `<destination_directory>` de tijdelijke aanduiding door de naam van de map waarnaar u uw gegevens wilt kopiëren.

6. Voer een lijstopdracht uit om ervoor te zorgen dat uw container en map zijn gemaakt.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Vervang `<blob_service_endpoint>` de tijdelijke aanduiding door de naam van het eindpunt van uw blobservice.

   * Vervang `<account_key>` de tijdelijke aanduiding door de toegangssleutel van uw account.

   * Vervang `<container-name>` de tijdelijke aanduiding door de naam van uw container.

7. Kopieer gegevens van de Hadoop HDFS naar De Blob-opslag van de gegevensbox naar de container die u eerder hebt gemaakt. Als de map waarnaar u kopieert niet wordt gevonden, wordt de opdracht automatisch gemaakt.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Vervang `<blob_service_endpoint>` de tijdelijke aanduiding door de naam van het eindpunt van uw blobservice.

    * Vervang `<account_key>` de tijdelijke aanduiding door de toegangssleutel van uw account.

    * Vervang `<container-name>` de tijdelijke aanduiding door de naam van uw container.

    * Vervang `<exlusion_filelist_file>` de tijdelijke aanduiding door de naam van het bestand dat uw lijst met bestandsuitsluitingen bevat.

    * Vervang `<source_directory>` de tijdelijke aanduiding door de naam van de map die de gegevens bevat die u wilt kopiëren.

    * Vervang `<destination_directory>` de tijdelijke aanduiding door de naam van de map waarnaar u uw gegevens wilt kopiëren.

    De `-libjars` optie wordt gebruikt `hadoop-azure*.jar` om `azure-storage*.jar` de en `distcp`de afhankelijke bestanden beschikbaar te maken voor . Dit kan al gebeuren voor sommige clusters.

    In het volgende `distcp` voorbeeld ziet u hoe de opdracht wordt gebruikt om gegevens te kopiëren.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Ga als het gaat om het verbeteren van de kopieersnelheid:

    * Probeer het aantal mappers te wijzigen. (Het bovenstaande `m` voorbeeld gebruikt = 4 mappers.)

    * Probeer meerdere `distcp` parallel lopen.

    * Vergeet niet dat grote bestanden beter presteren dan kleine bestanden.

## <a name="ship-the-data-box-to-microsoft"></a>De gegevensbox verzenden naar Microsoft

Volg deze stappen om het Data Box-apparaat voor te bereiden en naar Microsoft te verzenden.

1. Ten eerste, [Bereid je voor om te verzenden op uw Data Box of Data Box Heavy.](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)

2. Nadat de voorbereiding van het apparaat is voltooid, downloadt u de BOM-bestanden. U gebruikt deze bom- of manifestbestanden later om de naar Azure geüploade gegevens te verifiëren.

3. Sluit het apparaat af en verwijder de kabels.

4. Maak een afspraak met UPS om het pakket op te laten halen.

    * Zie [Uw gegevensvak verzenden](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)voor gegevensboxapparaten.

    * Zie Uw databox zwaar verzenden voor zware apparaten in de [gegevensbox.](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)

5. Nadat Microsoft uw apparaat heeft ontvangen, is het verbonden met het datacenternetwerk en worden de gegevens geüpload naar het opslagaccount dat u hebt opgegeven toen u de apparaatorder hebt geplaatst. Controleer tegen de BOM-bestanden of al uw gegevens naar Azure worden geüpload. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Toegangsmachtigingen toepassen op bestanden en mappen (alleen Data Lake Storage Gen2)

U hebt de gegevens al in uw Azure Storage-account. Nu pas je toegangsrechten toe op bestanden en mappen.

> [!NOTE]
> Deze stap is alleen nodig als u Azure Data Lake Storage Gen2 als uw gegevensarchief gebruikt. Als u slechts een blob-opslagaccount gebruikt zonder hiërarchische naamruimte als uw gegevensarchief, u deze sectie overslaan.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Een serviceprincipal maken voor uw Azure Data Lake Storage Gen2-account

Als u een serviceprincipal wilt maken, raadpleegt u [Hoe: Gebruik de portal om een Azure AD-toepassing en serviceprincipal te maken die toegang heeft tot bronnen.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

* Wanneer u de stappen uitvoert in de sectie [De toepassing toewijzen aan een rol](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) van het artikel, moet u ervoor zorgen dat de rol **Gegevensbijdrager voor opslagblob** is toegewezen aan de service-principal.

* Wanneer u de stappen uitvoert in het gedeelte [Waarden downloaden voor aanmelden in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) het artikel, slaat u toepassings-id en clientgeheime waarden op in een tekstbestand. U hebt deze binnenkort nodig.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Een lijst met gekopieerde bestanden genereren met hun machtigingen

Voer de opdracht uit vanuit het on-premises Hadoop-cluster:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Met deze opdracht genereert u een lijst met gekopieerde bestanden met hun machtigingen.

> [!NOTE]
> Afhankelijk van het aantal bestanden in de HDFS kan het lang duren voordat deze opdracht is uitgevoerd.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Een lijst met identiteiten genereren en deze toewijzen aan Azure Active Directory-identiteiten (ADD)

1. Download `copy-acls.py` het script. Bekijk de [helperscripts downloaden en stel uw randknooppunt in om ze](#download-helper-scripts) in dit artikel uit te voeren.

2. Voer deze opdracht uit om een lijst met unieke identiteiten te genereren.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Dit script genereert `id_map.json` een bestand met de naam dat de identiteiten bevat die u moet toewijzen aan op ADD gebaseerde identiteiten.

3. Open `id_map.json` het bestand in een teksteditor.

4. Werk voor elk JSON-object dat `target` in het bestand wordt weergegeven het kenmerk van een AAD-gebruikersnaam (UPN) of ObjectId (OID) bij met de juiste toegewezen identiteit. Sla het bestand op nadat u klaar bent. Je hebt dit bestand nodig in de volgende stap.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Machtigingen toepassen op gekopieerde bestanden en identiteitstoewijzingen toepassen

Voer deze opdracht uit om machtigingen toe te passen op de gegevens die u hebt gekopieerd naar het Data Lake Storage Gen2-account:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Vervang de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

* Vervang `<container-name>` de tijdelijke aanduiding door de naam van uw container.

* Vervang `<application-id>` de `<client-secret>` tijdelijke aanduidingen en tijdelijke aanduidingen door de toepassings-id en het clientgeheim dat u hebt verzameld toen u de serviceprincipal hebt gemaakt.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Bijlage: Gegevens splitsen over meerdere Data Box-apparaten

Voordat u uw gegevens naar een Data Box-apparaat verplaatst, moet u een aantal helperscripts downloaden, ervoor zorgen dat uw gegevens zijn geordend om op een Data Box-apparaat te passen en onnodige bestanden uitsluiten.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Helperscripts downloaden en uw randknooppunt instellen om ze uit te voeren

1. Voer de opdracht uit vanaf uw rand of hoofdknooppunt van uw on-premises Hadoop-cluster:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Met deze opdracht wordt de GitHub-repository gekloond die de helperscripts bevat.

2. Zorg ervoor dat het [jq-pakket](https://stedolan.github.io/jq/) op uw lokale computer is geïnstalleerd.

   ```bash
   
   sudo apt-get install jq
   ```

3. Installeer het [Python-pakket Aanvragen.](https://2.python-requests.org/en/master/)

   ```bash
   
   pip install requests
   ```

4. Voermachtigingen in op de vereiste scripts.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Ervoor zorgen dat uw gegevens zijn geordend om op een Data Box-apparaat te passen

Als de grootte van uw gegevens groter is dan de grootte van één Gegevensvakapparaat, u bestanden opsplitsen in groepen die u op meerdere Gegevensvakapparaten opslaan.

Als uw gegevens de grootte van een singe Data Box-apparaat niet overschrijden, u doorgaan naar de volgende sectie.

1. Voer met verhoogde machtigingen `generate-file-list` het script uit dat u hebt gedownload door de richtlijnen in de vorige sectie te volgen.

   Hier is een beschrijving van de opdrachtparameters:

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

2. Kopieer de gegenereerde bestandslijsten naar HDFS, zodat deze toegankelijk zijn voor de taak [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Onnodige bestanden uitsluiten

Je moet een aantal mappen uitsluiten van de DisCp baan. Sluit bijvoorbeeld mappen uit die statusgegevens bevatten die het cluster draaiende houden.

Maak op het on-premises Hadoop-cluster waar u van plan bent de distcp-taak te starten, een bestand dat de lijst met mappen opgeeft die u wilt uitsluiten.

Hier volgt een voorbeeld:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Data Lake Storage Gen2 werkt met HDInsight-clusters. Zie [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
