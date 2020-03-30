---
title: Gegevens uit Azure Storage-blobs kopiëren naar Data Lake Storage Gen1
description: AdlCopy-tool gebruiken om gegevens uit Azure Storage Blobs naar Azure Data Lake Storage Gen1 te kopiëren
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: ad408df140be49da2e50ef810285dd850e9da6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638864"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Gegevens uit Azure Storage Blobs kopiëren naar Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [DistCp gebruiken](data-lake-store-copy-data-wasb-distcp.md)
> * [AdlCopy gebruiken](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 biedt een command-line tool, [AdlCopy,](https://www.microsoft.com/download/details.aspx?id=50358)om gegevens te kopiëren uit de volgende bronnen:

* Van Azure Storage blobs naar Data Lake Storage Gen1. U AdlCopy niet gebruiken om gegevens uit Data Lake Storage Gen1 te kopiëren naar Azure Storage-blobs.
* Tussen twee Data Lake Storage Gen1-accounts.

U het gereedschap AdlCopy ook in twee verschillende modi gebruiken:

* **Zelfstandig**, waar het hulpprogramma gegevens lake storage Gen1 resources gebruikt om de taak uit te voeren.
* **Met behulp van een Data Lake Analytics-account,** waar de eenheden die zijn toegewezen aan uw Data Lake Analytics-account worden gebruikt om de kopieerbewerking uit te voeren. U deze optie gebruiken wanneer u de kopieertaken op een voorspelbare manier wilt uitvoeren.

## <a name="prerequisites"></a>Vereisten

Voordat u dit artikel gaat lezen, moet u beschikken over het volgende:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Storage blobs** container met een aantal gegevens.
* **Een Data Lake Storage Gen1-account**. Zie [Aan de slag met Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) voor instructies over het maken van een account.
* **Data Lake Analytics-account (optioneel)** - Zie [Aan de slag met Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) voor instructies over het maken van een Data Lake Analytics-account.
* **AdlCopy, gereedschap**. Installeer het [gereedschap AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntaxis van het gereedschap AdlCopy

De volgende syntaxis gebruiken om met het gereedschap AdlCopy te werken

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

De parameters in de syntaxis worden hieronder beschreven:

| Optie | Beschrijving |
| --- | --- |
| Bron |Hiermee geeft u de locatie op van de brongegevens in de Azure-opslagblob. De bron kan een blobcontainer, een blob of een ander Data Lake Storage Gen1-account zijn. |
| Dest |Hiermee geeft u de bestemming Data Lake Storage Gen1 op waarnaar u wilt kopiëren. |
| SourceKey |Hiermee geeft u de opslagtoegangssleutel op voor de Azure-opslagblobbron. Dit is alleen vereist als de bron een blobcontainer of een blob is. |
| Account |**Optioneel**. Gebruik dit als u het Azure Data Lake Analytics-account wilt gebruiken om de kopieertaak uit te voeren. Als u de optie /Account in de syntaxis gebruikt, maar geen Data Lake Analytics-account opgeeft, gebruikt AdlCopy een standaardaccount om de taak uit te voeren. Als u deze optie gebruikt, moet u de bron (Azure Storage Blob) en de bestemming (Azure Data Lake Storage Gen1) toevoegen als gegevensbronnen voor uw Data Lake Analytics-account. |
| Eenheden |Hiermee geeft u het aantal Data Lake Analytics-eenheden op dat voor de kopieertaak wordt gebruikt. Deze optie is verplicht als u de optie **/Account** gebruikt om het Data Lake Analytics-account op te geven. |
| Patroon |Hiermee geeft u een regex-patroon op dat aangeeft welke blobs of bestanden moeten worden gekopieerd. AdlCopy maakt gebruik van case-sensitive matching. Het standaardpatroon wanneer er geen patroon is opgegeven, is het kopiëren van alle items. Het opgeven van meerdere bestandspatronen wordt niet ondersteund. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>AdlCopy (als zelfstandige) gebruiken om gegevens uit een Azure Storage-blob te kopiëren

1. Open een opdrachtprompt en navigeer naar de map waar `%HOMEPATH%\Documents\adlcopy`AdlCopy is geïnstalleerd, meestal .
1. Voer de volgende opdracht uit om een specifieke blob van de broncontainer naar een Map Data Lake Storage Gen1 te kopiëren:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Bijvoorbeeld:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE]
    >De syntaxis hierboven geeft het bestand op dat moet worden gekopieerd naar een map in het Gen1-account gegevensopslagopslag. Het gereedschap AdlCopy maakt een map als de opgegeven mapnaam niet bestaat.

    U wordt gevraagd de referenties in te voeren voor het Azure-abonnement waaronder u uw Data Lake Storage Gen1-account hebt. U ziet een uitvoer die vergelijkbaar is met de volgende:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. U ook alle blobs van één container naar het Data Lake Storage Gen1-account kopiëren met de volgende opdracht:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Bijvoorbeeld:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Prestatieoverwegingen

Als u kopieert vanuit een Azure Blob Storage-account, wordt u mogelijk beperkt tijdens het kopiëren aan de blob-opslagzijde. Dit zal de prestaties van uw kopieerwerk verslechteren. Zie Azure Storage-limieten bij [Azure-abonnements- en servicelimieten](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over de limieten van Azure Blob Storage.

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>AdlCopy (als zelfstandige) gebruiken om gegevens uit een ander Data Lake Storage Gen1-account te kopiëren

U AdlCopy ook gebruiken om gegevens te kopiëren tussen twee Data Lake Storage Gen1-accounts.

1. Open een opdrachtprompt en navigeer naar de map waar `%HOMEPATH%\Documents\adlcopy`AdlCopy is geïnstalleerd, meestal .
1. Voer de volgende opdracht uit om een specifiek bestand van het ene Data Lake Storage Gen1-account naar het andere te kopiëren.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Bijvoorbeeld:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > De syntaxis hierboven geeft het bestand op dat moet worden gekopieerd naar een map in het gen1-account van doelgegevensgegevensmeeropslag. Het gereedschap AdlCopy maakt een map als de opgegeven mapnaam niet bestaat.
   >
   >

    U wordt gevraagd de referenties in te voeren voor het Azure-abonnement waaronder u uw Data Lake Storage Gen1-account hebt. U ziet een uitvoer die vergelijkbaar is met de volgende:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
1. Met de volgende opdracht worden alle bestanden uit een specifieke map in het Bron Data Lake Storage Gen1-account gekopieerd naar een map in het doelGegevens Lake Storage Gen1-account.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Prestatieoverwegingen

Wanneer u AdlCopy als zelfstandig hulpmiddel gebruikt, wordt de kopie uitgevoerd op gedeelde, door Azure beheerde resources. De prestaties die u in deze omgeving krijgen, zijn afhankelijk van de systeembelasting en de beschikbare resources. Deze modus wordt het best gebruikt voor kleine transfers op ad hoc basis. Er hoeven geen parameters te worden afgestemd bij het gebruik van AdlCopy als een standalone tool.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>AdlCopy gebruiken (met Data Lake Analytics-account) om gegevens te kopiëren

U uw Data Lake Analytics-account ook gebruiken om de AdlCopy-taak uit te voeren om gegevens van Azure-opslagblobs naar Data Lake Storage Gen1 te kopiëren. U zou deze optie meestal gebruiken wanneer de te verplaatsen gegevens zich in het bereik van gigabytes en terabytes bevinden en u een betere en voorspelbare prestatiedoorvoer wilt.

Als u uw Data Lake Analytics-account met AdlCopy wilt gebruiken om te kopiëren vanuit een Azure Storage Blob, moet de bron (Azure Storage Blob) worden toegevoegd als gegevensbron voor uw Data Lake Analytics-account. Zie [Gegevenslake Analytics-accountgegevensbronnen beheren](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources)voor instructies over het toevoegen van aanvullende gegevensbronnen aan uw Data Lake Analytics-account.

> [!NOTE]
> Als u vanuit een Azure Data Lake Storage Gen1-account als bron kopieert met een Data Lake Analytics-account, hoeft u het Data Lake Storage Gen1-account niet te koppelen aan het Data Lake Analytics-account. De vereiste om het bronarchief te koppelen aan het Data Lake Analytics-account is alleen wanneer de bron een Azure Storage-account is.
>
>

Voer de volgende opdracht uit om vanuit een Azure Storage-blob naar een Data Lake Storage Gen1-account te kopiëren met het Data Lake Analytics-account:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Bijvoorbeeld:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Voer ook de volgende opdracht uit om alle bestanden uit een specifieke map in het brongegevensmeeropslag Gen1-account te kopiëren naar een map in het doelGegevensLake Storage Gen1-account met het Data Lake Analytics-account:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Prestatieoverwegingen

Wanneer u gegevens kopieert in het bereik van terabytes, biedt het gebruik van AdlCopy met uw eigen Azure Data Lake Analytics-account betere en meer voorspelbare prestaties. De parameter die moet worden afgestemd, is het aantal Azure Data Lake Analytics-eenheden dat moet worden gebruikt voor de kopieertaak. Het verhogen van het aantal eenheden zal de prestaties van uw kopieertaak verhogen. Elk te kopiëren bestand kan maximaal één eenheid gebruiken. Het opgeven van meer eenheden dan het aantal bestanden dat wordt gekopieerd, verhoogt de prestaties niet.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>AdlCopy gebruiken om gegevens te kopiëren met patroonmatching

In deze sectie leert u hoe u AdlCopy gebruikt om gegevens uit een bron te kopiëren (in ons voorbeeld hieronder gebruiken we Azure Storage Blob) naar een doelGegevenslake Storage Gen1-account met behulp van patroonmatching. U bijvoorbeeld de onderstaande stappen gebruiken om alle bestanden met .csv-extensie van de bronblob naar de bestemming te kopiëren.

1. Open een opdrachtprompt en navigeer naar de map waar `%HOMEPATH%\Documents\adlcopy`AdlCopy is geïnstalleerd, meestal .
1. Voer de volgende opdracht uit om alle bestanden met *.csv-extensie te kopiëren van een specifieke blob van de broncontainer naar een map Data Lake Storage Gen1:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Bijvoorbeeld:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Billing

* Als u het AdlCopy-hulpprogramma als zelfstandige gebruikt, wordt u gefactureerd voor uitgaande kosten voor het verplaatsen van gegevens, als het azure-opslagaccount van de bron zich niet in dezelfde regio bevindt als het Data Lake Storage Gen1-account.
* Als u de AdlCopy-tool gebruikt met uw Data Lake Analytics-account, zijn de [standaard factureringstarieven van Data Lake Analytics](https://azure.microsoft.com/pricing/details/data-lake-analytics/) van toepassing.

## <a name="considerations-for-using-adlcopy"></a>Overwegingen voor het gebruik van AdlCopy

* AdlCopy (voor versie 1.0.5) ondersteunt het kopiëren van gegevens uit bronnen die gezamenlijk meer dan duizenden bestanden en mappen bevatten. Als u echter problemen ondervindt bij het kopiëren van een grote gegevensset, u de bestanden/mappen in verschillende submappen distribueren en het pad naar die submappen als bron gebruiken.

## <a name="performance-considerations-for-using-adlcopy"></a>Prestatieoverwegingen voor het gebruik van AdlCopy

AdlCopy ondersteunt het kopiëren van gegevens die duizenden bestanden en mappen bevatten. Als u echter problemen ondervindt bij het kopiëren van een grote gegevensset, u de bestanden/mappen distribueren naar kleinere submappen. AdlCopy is gebouwd voor ad hoc exemplaren. Als u gegevens herhaaldelijk probeert te kopiëren, u azure [datafactory](../data-factory/connector-azure-data-lake-store.md) gebruiken dat volledig beheer biedt rond de kopieerbewerkingen.

## <a name="release-notes"></a>Releaseopmerkingen

* 1.0.13 - Als u gegevens kopieert naar hetzelfde Azure Data Lake Storage Gen1-account voor meerdere adlcopy-opdrachten, hoeft u uw referenties voor elke uitvoering niet meer opnieuw in te voeren. Adlcopy slaat die informatie nu in de cache op meerdere uitvoeringen.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
