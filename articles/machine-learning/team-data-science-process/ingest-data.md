---
title: Gegevens laden in azure storage-omgevingen-team data Science process
description: Meer informatie over het opnemen van gegevens in verschillende doel omgevingen waarin de gegevens worden opgeslagen en verwerkt.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 515decfafe46ad0c1b5b90743688abc26a975903
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053267"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Gegevens voor analysedoeleinden in opslagomgevingen laden

Voor het proces van de team data Science moeten gegevens worden opgenomen of geladen in verschillende opslag omgevingen, zodat ze op de meest geschikte manier in elke fase van het proces kunnen worden verwerkt of geanalyseerd. Gegevens bestemmingen die vaak worden gebruikt voor verwerking zijn onder andere Azure Blob Storage, SQL Azure data bases SQL Server op Azure VM, HDInsight (Hadoop) en Azure Machine Learning. 

In de volgende artikelen wordt beschreven hoe u gegevens opneemt in verschillende doel omgevingen waarin de gegevens worden opgeslagen en verwerkt.

* Naar/van [Azure Blob Storage](move-azure-blob.md)
* [SQL Server op Azure VM](move-sql-server-virtual-machine.md)
* Naar [Azure SQL database](move-sql-azure.md)
* In [Hive-tabellen](move-hive-tables.md)
* Naar [SQL-gepartitioneerde tabellen](parallel-load-sql-partitioned-tables.md)
* Van [on-premises SQL Server](move-sql-azure-adf.md)

Technische en bedrijfs behoeften, evenals de oorspronkelijke locatie, indeling en grootte van uw gegevens, bepalen de doel omgevingen waarin de gegevens moeten worden opgenomen om de doel stellingen van uw analyse te bereiken. Het is niet ongebruikelijk dat een scenario vereist dat gegevens tussen verschillende omgevingen worden verplaatst om de verschillende taken die nodig zijn voor het bouwen van een voorspellend model te bereiken. Deze reeks taken kan bijvoorbeeld betrekking hebben op het verkennen van gegevens, het vooraf verwerken, schoonmaken, bemonsteren en model trainingen.
