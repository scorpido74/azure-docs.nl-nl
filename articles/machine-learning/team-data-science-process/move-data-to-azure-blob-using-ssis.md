---
title: Blob storage-gegevens met SSIS-connectors - Team Data Science Process verplaatsen
description: Meer informatie over het verplaatsen van gegevens naar of van Azure Blob Storage met SQL Server Integration Services Feature Pack voor Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 77bfd9d5bcae7bedd673354e32464d5f59bdc9b4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720868"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Gegevens verplaatsen naar of van Azure Blob Storage met behulp van SSIS-connectors
Het [SQL Server Integration Services Feature Pack voor Azure](https://msdn.microsoft.com/library/mt146770.aspx) biedt onderdelen om verbinding te maken met Azure, gegevens over te dragen tussen Azure-en on-premises gegevens bronnen en gegevens die zijn opgeslagen in azure te verwerken.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Zodra klanten on-premises gegevens naar de Cloud hebben verplaatst, hebben ze toegang tot hun gegevens vanuit elke Azure-service om gebruik te kunnen maken van de volledige kracht van de Azure-technologieën. De gegevens kunnen vervolgens worden gebruikt, bijvoorbeeld in Azure Machine Learning of op een HDInsight-cluster.

Voor beelden van het gebruik van deze Azure-resources zijn te vinden in de [SQL](sql-walkthrough.md) -en [HDInsight](hive-walkthrough.md) -scenario's.

Zie [meer met SQL Server Integration Services Feature Pack voor Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blog voor een bespreking van canonieke scenario's die gebruikmaken van SSIS om bedrijfs behoeften te bereiken die gemeen schappelijk zijn voor hybride gegevens integratie scenario's.

> [!NOTE]
> Raadpleeg Azure Blob- [basis beginselen](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) en [Azure Blob service](https://msdn.microsoft.com/library/azure/dd179376.aspx)voor een volledige inleiding tot Azure Blob-opslag.
> 
> 

## <a name="prerequisites"></a>Vereisten
Als u de taken wilt uitvoeren die in dit artikel worden beschreven, moet u een Azure-abonnement en een Azure Storage-account instellen. U hebt de Azure Storage-account naam en de account sleutel nodig om gegevens te uploaden of te downloaden.

* Zie [een gratis proef versie van één maand](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie over het instellen van een **Azure-abonnement**.
* Zie [over Azure Storage-accounts](../../storage/common/storage-create-storage-account.md)voor instructies over het maken van een **opslag account** en voor het ophalen van account-en sleutel gegevens.

Als u de **SSIS-connectors**wilt gebruiken, moet u het volgende downloaden:

* **SQL Server 2014 of 2016 Standard (of hoger)** : de installatie bevat SQL Server Integration Services.
* **Microsoft SQL Server 2014 of 2016 Integration Services Feature Pack voor Azure**: deze Connectors kunnen respectievelijk worden gedownload via de pagina's [SQL Server 2014 integration services](https://www.microsoft.com/download/details.aspx?id=47366) en [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) .

> [!NOTE]
> SSIS met SQL Server is geïnstalleerd, maar is niet opgenomen in de Express-versie. Zie [SQL Server-edities](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/) voor meer informatie over welke toepassingen zijn opgenomen in de verschillende edities van SQL Server.
> 
> 

Voor trainings materiaal op SSIS, Zie [handen voor training voor SSIS](https://www.microsoft.com/sql-server/training-certification)

Zie [SSIS-zelf studie: een eenvoudig ETL-pakket maken](https://msdn.microsoft.com/library/ms169917.aspx)voor informatie over het ophalen van en het uitvoeren van SISS voor het bouwen van eenvoudige pakketten voor het uitpakken, transformeren en laden (ETL).

## <a name="download-nyc-taxi-dataset"></a>Gegevensset NYC over taxi's downloaden
In het voor beeld dat hier wordt beschreven, wordt een openbaar beschik bare gegevensset gebruikt: de NYC van de [taxi trips](https://www.andresmh.com/nyctaxitrips/) . De gegevensset bestaat uit over 173 miljoen taxi ritjes in NYC in het jaar 2013. Er zijn twee soorten gegevens: reis details gegevens en fare gegevens. Omdat er voor elke maand een bestand is, zijn er 24 bestanden, die elk ongeveer 2 GB ongecomprimeerd zijn.

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure blob-opslag
Als u gegevens wilt verplaatsen met behulp van het SSIS-functie pakket van on-premises naar Azure Blob-opslag, wordt een exemplaar van de [**Azure Blob upload-taak**](https://msdn.microsoft.com/library/mt146776.aspx)gebruikt, zoals hier wordt weer gegeven:

![Configureer-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

De parameters die gebruikmaakt van de taak worden hier beschreven:

| Veld | Beschrijving |
| --- | --- |
| **AzureStorageConnection** |Hiermee geeft u een bestaande Azure Storage verbindings beheer of maakt u een nieuwe die verwijst naar een Azure Storage-account waarnaar wordt verwezen naar de host van de BLOB-bestanden. |
| **Zijn** |Hiermee geeft u de naam op van de BLOB-container die de geüploade bestanden als blobs bevat. |
| **BlobDirectory** |Hiermee geeft u de blob-directory waar het geüploade bestand wordt opgeslagen als een blok-blob. De blob-map is een virtuele hiërarchische structuur. Als de blob al bestaat, it ia vervangen. |
| **LocalDirectory** |Hiermee geeft u de lokale map waarin de bestanden worden geüpload. |
| **Bestands** |Hiermee geeft u een naamfilter om bestanden met het patroon van de opgegeven naam te selecteren. Zo bevat MySheet\*. xls\* bestanden zoals MySheet001. xls en MySheetABC. xlsx |
| **TimeRangeFrom/TimeRangeTo** |Hiermee geeft u een filter tijdsbereik. Bestanden die zijn gewijzigd na *TimeRangeFrom* en voordat *TimeRangeTo* zijn opgenomen. |

> [!NOTE]
> De **AzureStorageConnection** -referenties moeten juist zijn en de **zijn** moeten bestaan voordat de overdracht wordt uitgevoerd.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Gegevens uit Azure blob-opslag downloaden
Als u gegevens van Azure Blob-opslag naar on-premises opslag met SSIS wilt downloaden, gebruikt u een exemplaar van de [Azure Blob-Download taak](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Meer geavanceerde SSIS-Azure-scenario 's
Het SSIS featurepack kunt u complexere stromen moet worden verwerkt door taken voor pakketten samen. Bijvoorbeeld, kan de blob-gegevens rechtstreeks in een HDInsight-cluster, waarvan de uitvoer kan worden gedownload naar een blob en vervolgens naar on-premises opslag-feed. SSIS uitvoeren Hive en Pig-taken op een HDInsight-cluster met behulp van aanvullende SSIS-connectors:

* Als u een Hive-script op een Azure HDInsight-cluster met SSIS wilt uitvoeren, gebruikt u de [component taak van Azure hdinsight](https://msdn.microsoft.com/library/mt146771.aspx).
* Als u een Pig-script wilt uitvoeren op een Azure HDInsight-cluster met SSIS, gebruikt u de [Azure Hdinsight Pig-taak](https://msdn.microsoft.com/library/mt146781.aspx).

