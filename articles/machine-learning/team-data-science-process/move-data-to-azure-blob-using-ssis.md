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
De [SQL Server Integration Services Feature Pack voor Azure](https://msdn.microsoft.com/library/mt146770.aspx) onderdelen verbinding maken met Azure, gegevens overdragen tussen Azure en on-premises gegevensbronnen en gegevens verwerken die zijn opgeslagen in Azure biedt.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Zodra klanten on-premises gegevens naar de Cloud hebben verplaatst, hebben ze toegang tot hun gegevens vanuit elke Azure-service om gebruik te kunnen maken van de volledige kracht van de Azure-technologieën. De gegevens kunnen vervolgens worden gebruikt, bijvoorbeeld in Azure Machine Learning of op een HDInsight-cluster.

Voor beelden van het gebruik van deze Azure-resources zijn te vinden in de [SQL](sql-walkthrough.md) -en [HDInsight](hive-walkthrough.md) -scenario's.

Zie voor een bespreking van de canonieke scenario's die gebruikmaken van SSIS om uit te voeren van de behoeften van uw bedrijf gebruikt in scenario's voor hybride gegevensintegratie, [doen meer met SQL Server Integration Services Feature Pack voor Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blog.

> [!NOTE]
> Raadpleeg voor een volledige Inleiding tot Azure blob-opslag, [grondbeginselen van Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) en [Azure Blob-Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Vereisten
Als u de taken wilt uitvoeren die in dit artikel worden beschreven, moet u een Azure-abonnement en een Azure Storage-account instellen. U hebt de Azure Storage-account naam en de account sleutel nodig om gegevens te uploaden of te downloaden.

* Voor het instellen van een **Azure-abonnement**, Zie [gratis proefversie van één maand](https://azure.microsoft.com/pricing/free-trial/).
* Zie [over Azure Storage-accounts](../../storage/common/storage-create-storage-account.md)voor instructies over het maken van een **opslag account** en voor het ophalen van account-en sleutel gegevens.

Gebruik de **SSIS-connectors**, moet u het downloaden:

* **SQL Server 2014 of 2016 Standard (of hoger)** : installatie van SQL Server Integration Services bevat.
* **Microsoft SQL Server 2014 of 2016 Integration Services Feature Pack voor Azure**: deze Connectors kunnen respectievelijk worden gedownload via de pagina's [SQL Server 2014 integration services](https://www.microsoft.com/download/details.aspx?id=47366) en [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) .

> [!NOTE]
> SSIS met SQL Server is geïnstalleerd, maar is niet opgenomen in de Express-versie. Zie voor informatie over welke toepassingen zijn opgenomen in de verschillende edities van SQL Server, [edities van SQL Server](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Zie voor het trainingsmateriaal van SSIS, [handen op Training voor SSIS](https://www.microsoft.com/sql-server/training-certification)

Voor meer informatie over het ophalen van de boven-en-die wordt uitgevoerd met behulp van SISS aan het bouwen van eenvoudige extractie, transformatie en laden (ETL)-pakketten, Zie [SSIS-zelfstudie: het maken van een eenvoudige ETL-pakket](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Gegevensset NYC over taxi's downloaden
Het voorbeeld beschreven hier een openbaar beschikbare gegevensset--gebruiken de [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) gegevensset. De gegevensset bestaat uit over 173 miljoen taxi ritjes in NYC in het jaar 2013. Er zijn twee soorten gegevens: reis details gegevens en fare gegevens. Omdat er voor elke maand een bestand is, zijn er 24 bestanden, die elk ongeveer 2 GB ongecomprimeerd zijn.

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure blob-opslag
Voor het verplaatsen van gegevens met behulp van de SSIS-functiepakket van on-premises naar Azure blob-opslag, gebruiken we een exemplaar van de [ **Azure Blob uploaden taak**](https://msdn.microsoft.com/library/mt146776.aspx), die hier worden weergegeven:

![Configureer-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

De parameters die gebruikmaakt van de taak worden hier beschreven:

| Veld | Beschrijving |
| --- | --- |
| **AzureStorageConnection** |Hiermee geeft u een bestaande Azure Storage verbindings beheer of maakt u een nieuwe die verwijst naar een Azure Storage-account waarnaar wordt verwezen naar de host van de BLOB-bestanden. |
| **BlobContainer** |Hiermee geeft u de naam op van de BLOB-container die de geüploade bestanden als blobs bevat. |
| **BlobDirectory** |Hiermee geeft u de blob-directory waar het geüploade bestand wordt opgeslagen als een blok-blob. De blob-map is een virtuele hiërarchische structuur. Als de blob al bestaat, it ia vervangen. |
| **LocalDirectory** |Hiermee geeft u de lokale map waarin de bestanden worden geüpload. |
| **FileName** |Hiermee geeft u een naamfilter om bestanden met het patroon van de opgegeven naam te selecteren. Bijvoorbeeld, MySheet\*xls\* bestanden zoals MySheet001.xls en MySheetABC.xlsx bevat |
| **TimeRangeFrom/TimeRangeTo** |Hiermee geeft u een filter tijdsbereik. Bestanden gewijzigd na *TimeRangeFrom* en vóór *TimeRangeTo* zijn opgenomen. |

> [!NOTE]
> De **AzureStorageConnection** referenties moeten correct zijn en de **BlobContainer** moet bestaan voordat de overdracht wordt uitgevoerd.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Gegevens uit Azure blob-opslag downloaden
Als u wilt downloaden van gegevens uit Azure blob storage naar on-premises opslag met SSIS, gebruikt u een exemplaar van de [Azure Blob downloaden taak](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Meer geavanceerde SSIS-Azure-scenario 's
Het SSIS featurepack kunt u complexere stromen moet worden verwerkt door taken voor pakketten samen. Bijvoorbeeld, kan de blob-gegevens rechtstreeks in een HDInsight-cluster, waarvan de uitvoer kan worden gedownload naar een blob en vervolgens naar on-premises opslag-feed. SSIS uitvoeren Hive en Pig-taken op een HDInsight-cluster met behulp van aanvullende SSIS-connectors:

* Als u wilt een Hive-script uitvoeren op een Azure HDInsight-cluster met SSIS, gebruikt u [Azure HDInsight Hive-taak](https://msdn.microsoft.com/library/mt146771.aspx).
* Als u wilt een Pig-script uitvoeren op een Azure HDInsight-cluster met SSIS, gebruikt u [Azure HDInsight Pig-taak](https://msdn.microsoft.com/library/mt146781.aspx).

