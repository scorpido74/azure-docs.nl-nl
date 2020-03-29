---
title: Gegevens verplaatsen van en naar Azure Blob-opslag - Team Data Science-proces
description: Gegevens verplaatsen van en naar Azure Blob-opslag met Azure Storage Explorer, AzCopy, Python en SSIS.
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
ms.openlocfilehash: fc58651bcb3b266b981fb953fd7341427d47fb2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717587"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Gegevens verplaatsen van en naar Azure Blob-opslag

Het Team Data Science Process vereist dat gegevens worden ingenomen of geladen in een verscheidenheid van verschillende opslagomgevingen die in elke fase van het proces op de meest geschikte manier moeten worden verwerkt of geanalyseerd.

## <a name="different-technologies-for-moving-data"></a>Verschillende technologieën voor het verplaatsen van gegevens

In de volgende artikelen wordt beschreven hoe u gegevens van en naar Azure Blob-opslag verplaatst met behulp van verschillende technologieën.

* [Azure Storage-Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy (AzCopy)](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Welke methode het beste voor u is, hangt af van uw scenario. Met [het artikel Scenario's voor geavanceerde analyses in het](plan-sample-scenarios.md) artikel Azure Machine Learning u bepalen welke resources u nodig hebt voor een verscheidenheid aan data science-workflows die worden gebruikt in het geavanceerde analyseproces.

> [!NOTE]
> Raadpleeg Azure [Blob Basics](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) en Azure [Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)voor een volledige inleiding tot Azure blob-opslag.
> 
> 

## <a name="using-azure-data-factory"></a>Azure Data Factory gebruiken

Als alternatief u [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) gebruiken om: 

* een pijplijn maken en plannen die gegevens uit Azure blob-opslag downloadt, 
* doorgeven aan een gepubliceerde Azure Machine Learning-webservice, 
* de resultaten van voorspellende analyses ontvangen, en 
* uploaden van de resultaten naar opslag. 

Zie [Voorspellende pijplijnen maken met Azure Data Factory en Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u een Azure-abonnement, een opslagaccount en de bijbehorende opslagsleutel voor dat account hebt. Voordat u gegevens uploadt/downloadt, moet u de naam en de accountsleutel van Azure Storage kennen.

* Zie Gratis proefperiode van één maand voor het instellen van een [Azure-abonnement.](https://azure.microsoft.com/pricing/free-trial/)
* Zie [Over Azure Storage-accounts](../../storage/common/storage-create-storage-account.md)voor instructies over het maken van een opslagaccount en voor het verkrijgen van account- en sleutelgegevens.

