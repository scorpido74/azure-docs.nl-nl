---
title: Blob-opslaggegevens verplaatsen met SSIS-connectors - Team Data Science Process
description: Meer informatie over het verplaatsen van gegevens naar of vanuit Azure Blob Storage met SQL Server Integration Services Feature Pack voor Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720868"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Gegevens verplaatsen naar of vanuit Azure Blob Storage met SSIS-connectors
Het [SQL Server Integration Services Feature Pack voor Azure](https://msdn.microsoft.com/library/mt146770.aspx) biedt componenten om verbinding te maken met Azure, gegevens over te dragen tussen Azure en on-premises gegevensbronnen en gegevens te verwerken die zijn opgeslagen in Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Zodra klanten on-premises gegevens naar de cloud hebben verplaatst, hebben ze toegang tot hun gegevens vanuit elke Azure-service om de volledige kracht van de suite van Azure-technologieën te benutten. De gegevens kunnen vervolgens worden gebruikt, bijvoorbeeld in Azure Machine Learning of in een HDInsight-cluster.

Voorbeelden voor het gebruik van deze Azure-resources bevinden zich in de [SQL-](sql-walkthrough.md) en HDInsight-walkthroughs. [HDInsight](hive-walkthrough.md)

Zie Meer doen [met SQL Server Integration Services Feature Pack voor Azure-blog voor](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) een bespreking van canonieke scenario's die SSIS gebruiken om zakelijke behoeften te bereiken.

> [!NOTE]
> Raadpleeg Azure [Blob Basics](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) en Azure [Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)voor een volledige inleiding tot Azure blob-opslag.
> 
> 

## <a name="prerequisites"></a>Vereisten
Als u de in dit artikel beschreven taken wilt uitvoeren, moet u een Azure-abonnement en een Azure Storage-account hebben ingesteld. U hebt de naam en de accountsleutel van Azure Storage nodig om gegevens te uploaden of te downloaden.

* Zie Gratis proefperiode van één maand als u een **Azure-abonnement wilt** [instellen.](https://azure.microsoft.com/pricing/free-trial/)
* Zie [Over Azure Storage-accounts](../../storage/common/storage-create-storage-account.md)voor instructies over het maken van een **opslagaccount** en voor het verkrijgen van account- en sleutelgegevens.

Als u de **SSIS-connectors wilt**gebruiken, moet u het:

* **SQL Server 2014 of 2016 Standard (of hoger):** Installeren omvat SQL Server Integration Services.
* **Microsoft SQL Server 2014 of 2016 Integration Services Feature Pack voor Azure:** deze connectors kunnen worden gedownload van de [pagina's SQL Server 2014 Integration Services](https://www.microsoft.com/download/details.aspx?id=47366) en [SQL Server 2016 Integration Services.](https://www.microsoft.com/download/details.aspx?id=49492)

> [!NOTE]
> SSIS is geïnstalleerd met SQL Server, maar is niet opgenomen in de Express-versie. Zie [SQL Server Editions](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/) voor informatie over welke toepassingen zijn opgenomen in verschillende edities van SQL Server
> 
> 

Zie [Hands On Training voor SSIS voor](https://www.microsoft.com/sql-server/training-certification) trainingsmateriaal op SSIS

Zie [SSIS-zelfstudie: Een eenvoudig ETL-pakket maken](https://msdn.microsoft.com/library/ms169917.aspx)voor informatie over het up-and-running maken van SISS om eenvoudige pakketten voor extractie, transformatie en belasting (ETL) te maken.

## <a name="download-nyc-taxi-dataset"></a>NYC Taxi-gegevensset downloaden
Het voorbeeld dat hier wordt beschreven, gebruikt een openbaar beschikbare gegevensset - de NYC Taxi Trips-gegevensset. [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) De dataset bestaat uit ongeveer 173 miljoen taxiritten in NYC in het jaar 2013. Er zijn twee soorten gegevens: gegevens over reisgegevens en tariefgegevens. Aangezien er een bestand voor elke maand, hebben we 24 bestanden, die elk is ongeveer 2 GB ongecomprimeerd.

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure blob-opslag
Als u gegevens wilt verplaatsen met het SSIS-functiepakket van on-premises naar Azure blob-opslag, gebruiken we een instantie van de [**Azure Blob Upload-taak**](https://msdn.microsoft.com/library/mt146776.aspx), die hier wordt weergegeven:

![configure-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

De parameters die de taak gebruikt, worden hier beschreven:

| Veld | Beschrijving |
| --- | --- |
| **AzureStorage-verbinding** |Hiermee geeft u een bestaand Azure Storage Connection Manager op of maakt u een nieuw account dat verwijst naar een Azure Storage-account dat verwijst naar waar de blobbestanden worden gehost. |
| **BlobContainer BlobContainer** |Hiermee geeft u de naam op van de blobcontainer die de geüploade bestanden als blobs bevat. |
| **BlobDirectory** |Hiermee geeft u de blobmap op waarin het geüploade bestand is opgeslagen als een blokblob. De blobmap is een virtuele hiërarchische structuur. Als de blob al bestaat, is deze vervangen. |
| **LocalDirectory (LocalDirectory)** |Hiermee geeft u de lokale map op die de bestanden bevat die moeten worden geüpload. |
| **Bestandsnaam** |Hiermee geeft u een naamfilter op om bestanden met het opgegeven naampatroon te selecteren. MySheet\*.xls\* bevat bijvoorbeeld bestanden zoals MySheet001.xls en MySheetABC.xlsx |
| **Timerangefrom/timerangeto** |Hiermee geeft u een tijdbereikfilter op. Bestanden die zijn gewijzigd na *TimeRangeFrom* en vóór *TimeRangeTo* zijn opgenomen. |

> [!NOTE]
> De **AzureStorageConnection-referenties** moeten correct zijn en de **BlobContainer** moet bestaan voordat de overdracht wordt geprobeerd.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Gegevens downloaden uit Azure blob-opslag
Als u gegevens wilt downloaden van Azure blob-opslag naar on-premises opslag met SSIS, gebruikt u een instantie van de [Azure Blob-downloadtaak](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Meer geavanceerde SSIS-Azure-scenario's
Het SSIS-functiepakket maakt het mogelijk om complexere stromen samen te verwerken door verpakkingstaken. De blobgegevens kunnen bijvoorbeeld rechtstreeks worden ingevoerd in een HDInsight-cluster, waarvan de uitvoer kan worden gedownload naar een blob en vervolgens naar on-premises opslag. SSIS kan Hive- en Pig-taken uitvoeren op een HDInsight-cluster met behulp van aanvullende SSIS-connectors:

* Als u een Hive-script wilt uitvoeren op een Azure HDInsight-cluster met SSIS, gebruikt u [Azure HDInsight Hive-taak](https://msdn.microsoft.com/library/mt146771.aspx).
* Als u een Pig-script wilt uitvoeren op een Azure HDInsight-cluster met SSIS, gebruikt u [Azure HDInsight Pig-taak](https://msdn.microsoft.com/library/mt146781.aspx).

