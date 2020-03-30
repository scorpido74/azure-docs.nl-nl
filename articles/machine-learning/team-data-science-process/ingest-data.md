---
title: Gegevens laden in Azure Storage-omgevingen - Team Data Science Process
description: Meer informatie over het innemen van gegevens in verschillende doelomgevingen waar de gegevens worden opgeslagen en verwerkt.
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
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720534"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Gegevens voor analysedoeleinden in opslagomgevingen laden

Het Team Data Science Process vereist dat gegevens in elke fase op de meest geschikte manier worden ingenomen of geladen. Gegevensbestemmingen kunnen Azure Blob Storage, SQL Azure-databases, SQL Server op Azure VM, HDInsight (Hadoop), Synapse Analytics en Azure Machine Learning omvatten. 

In de volgende artikelen wordt beschreven hoe u gegevens opnemen in verschillende doelomgevingen waar de gegevens worden opgeslagen en verwerkt.

* Van/naar [Azure Blob-opslag](move-azure-blob.md)
* Naar [SQL Server op Azure VM](move-sql-server-virtual-machine.md)
* Naar [Azure SQL-database](move-sql-azure.md)
* Naar [Hive-tabellen](move-hive-tables.md)
* Naar [SQL-partitietabellen](parallel-load-sql-partitioned-tables.md)
* Van [on-premises SQL Server](move-sql-azure-adf.md)

Technische en zakelijke behoeften, evenals de initiële locatie, indeling en grootte van uw gegevens bepalen het beste data-opnameplan. Het is niet ongewoon voor een beste plan om verschillende stappen te hebben. Deze reeks taken kan bijvoorbeeld gegevensverkenning, voorverwerking, reiniging, down-sampling en modeltraining omvatten.  Azure Data Factory is een aanbevolen Azure-bron om gegevensverplaatsing en -transformatie te orkestreren.
