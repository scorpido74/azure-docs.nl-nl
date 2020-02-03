---
title: Gegevens verplaatsen naar en van Azure Blob storage - Team Data Science Process
description: Gegevens verplaatsen van en naar Azure Blob-opslag met behulp van Azure Storage Explorer, AzCopy, python en SSIS.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717587"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Gegevens verplaatsen naar en van Azure Blob-opslag

Het Team Data Science Process is vereist dat gegevens worden opgenomen of geladen in tal van verschillende opslagomgevingen worden verwerkt of geanalyseerd in de meest geschikte manier in elke fase van het proces.

## <a name="different-technologies-for-moving-data"></a>Verschillende technologieën voor het verplaatsen van gegevens

De volgende artikelen wordt beschreven hoe u gegevens verplaatsen naar en van Azure Blob-opslag met behulp van verschillende technologieën.

* [Azure Storage-Verkenner](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Methode die voor u geschikt is, is afhankelijk van uw scenario. De [scenario's voor geavanceerde analyses in azure machine learning](plan-sample-scenarios.md) -artikel helpen u bij het bepalen van de bronnen die u nodig hebt voor diverse data Science-werk stromen die worden gebruikt in het geavanceerde analyse proces.

> [!NOTE]
> Raadpleeg Azure Blob- [basis beginselen](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) en [Azure Blob service](https://msdn.microsoft.com/library/azure/dd179376.aspx)voor een volledige inleiding tot Azure Blob-opslag.
> 
> 

## <a name="using-azure-data-factory"></a>Azure Data Factory gebruiken

Als alternatief kunt u [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) gebruiken voor het volgende: 

* maken en plannen van een pijplijn die gegevens vanuit Azure blob-opslag 
* deze doorgeven aan een gepubliceerde Azure Machine Learning-webservice 
* de resultaten voorspellende analyses en 
* de resultaten uploaden naar storage. 

Zie [voorspellende pijp lijnen maken met Azure Data Factory en Azure machine learning](../../data-factory/transform-data-using-machine-learning.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u een Azure-abonnement, een storage-account en de bijbehorende opslagsleutel voor dat account hebt. Voordat u gegevens kunt uploaden/downloaden, moet u de account naam en de account sleutel van Azure Storage kennen.

* Zie [een gratis proef versie van één maand](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie over het instellen van een Azure-abonnement.
* Zie [over Azure Storage-accounts](../../storage/common/storage-create-storage-account.md)voor instructies over het maken van een opslag account en voor het ophalen van account-en sleutel gegevens.

