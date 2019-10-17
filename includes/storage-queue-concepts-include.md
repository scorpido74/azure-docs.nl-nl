---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 92f638666d9ac832ee5e6a7d4dccf9a9e669f908
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428015"
---
## <a name="what-is-queue-storage"></a>Wat is Queue Storage?

Azure Queue Storage is een service voor de opslag van grote aantallen berichten die via HTTP of HTTPS overal vandaan kunnen worden opgevraagd met geverifieerde aanroepen. Een enkel wachtrijbericht mag maximaal 64 KB groot zijn en een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Queue Storage wordt vaak gebruikt om een achterstand te maken voor het asynchroon verwerken van werk.

## <a name="queue-service-concepts"></a>Queue-service concepten

De Azure Queue-service bevat de volgende onderdelen:

![Azure Queue-service-onderdelen](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **URL-indeling:** Wachtrijen kunnen worden opgevraagd met de volgende URL-indeling:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Met de volgende URL wordt een wachtrij in het diagram opgevraagd:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Opslagaccount:** Alle toegang tot Azure Storage vindt plaats via een opslagaccount. Zie [Azure Storage Scalability and Performance Targets](../articles/storage/common/storage-scalability-targets.md) (Schaalbaarheids- en prestatiedoeleinden in Azure Storage) voor meer informatie over opslagaccountcapaciteit.
* **Wachtrij:** Een wachtrij bevat een set berichten. Alle berichten moeten zich in een wachtrij bevinden. De naam van een wachtrij mag alleen kleine letters bevatten. Zie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Wachtrijen en metagegevens een naam geven) voor informatie over de naamgeving van wachtrijen.
* **Bericht:** Een bericht in een willekeurige indeling, van maximaal 64 KB. Een bericht kan maximaal 7 dagen in de wachtrij blijven staan.

