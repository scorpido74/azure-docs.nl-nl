---
title: Gegevens verplaatsen naar en van Azure Blob storage - Team Data Science Process
description: Gegevens verplaatsen naar en van Azure Blob-opslag
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7b45f9950ef63bc29d23d78d20909f223cb30350
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998148"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Gegevens verplaatsen naar en van Azure Blob-opslag

Het Team Data Science Process is vereist dat gegevens worden opgenomen of geladen in tal van verschillende opslagomgevingen worden verwerkt of geanalyseerd in de meest geschikte manier in elke fase van het proces.

## <a name="different-technologies-for-moving-data"></a>Verschillende technologieën voor het verplaatsen van gegevens

De volgende artikelen wordt beschreven hoe u gegevens verplaatsen naar en van Azure Blob-opslag met behulp van verschillende technologieën.

* [Azure Storage-Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Methode die voor u geschikt is, is afhankelijk van uw scenario. De [scenario's voor geavanceerde analyses in Azure Machine Learning](plan-sample-scenarios.md) artikel helpt u bepalen welke resources u nodig hebt voor verschillende data science-werkstromen in de geavanceerde analytics-proces gebruikt.

> [!NOTE]
> Raadpleeg voor een volledige Inleiding tot Azure blob-opslag, [grondbeginselen van Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) en [Azure Blob-Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Azure Data Factory gebruiken

Als alternatief kunt u [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) aan: 

* maken en plannen van een pijplijn die gegevens vanuit Azure blob-opslag 
* deze doorgeven aan een gepubliceerde Azure Machine Learning-webservice 
* de resultaten voorspellende analyses en 
* de resultaten uploaden naar storage. 

Zie voor meer informatie, [voorspellende pijplijnen maken met Azure Data Factory en Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u een Azure-abonnement, een storage-account en de bijbehorende opslagsleutel voor dat account hebt. Voordat u gegevens uploadt/downloadt, moet u uw Azure-opslag account naam en de accountsleutel weten.

* Als u een Azure-abonnement instelt, Zie [gratis proefversie van één maand](https://azure.microsoft.com/pricing/free-trial/).
* Zie voor instructies over het maken van een storage-account en voor het ophalen van account en sleutelgegevens [over Azure storage-accounts](../../storage/common/storage-create-storage-account.md).

