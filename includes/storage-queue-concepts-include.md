---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80151112"
---
## <a name="what-is-queue-storage"></a>Wat is Queue Storage?

Azure Queue Storage is een service voor de opslag van grote aantallen berichten die via HTTP of HTTPS overal vandaan kunnen worden opgevraagd met geverifieerde aanroepen. Een enkel wachtrijbericht mag maximaal 64 KB groot zijn en een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Queue Storage wordt vaak gebruikt om een achterstand te maken voor het asynchroon verwerken van werk.

## <a name="queue-service-concepts"></a>Queue-service concepten

De Azure Queue-service bevat de volgende onderdelen:

![Azure Queue-service-onderdelen](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **URL-indeling:** Wacht rijen zijn adresseerbaar met behulp van de volgende`<storage account>`URL-indeling: http://. Queue.core.Windows.net/`<queue>`
  
    Met de volgende URL wordt een wachtrij in het diagram opgevraagd:  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Opslagaccount:** Alle toegang tot Azure Storage vindt plaats via een opslagaccount. Zie voor meer informatie over opslag accounts [overzicht van opslag account](../articles/storage/common/storage-account-overview.md).
* **Wachtrij:** Een wachtrij bevat een set berichten. Alle berichten moeten zich in een wachtrij bevinden. De naam van een wachtrij mag alleen kleine letters bevatten. Zie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Wachtrijen en metagegevens een naam geven) voor informatie over de naamgeving van wachtrijen.
* **Bericht:** Een bericht in een willekeurige indeling, van maximaal 64 KB. Een bericht kan maximaal 7 dagen in de wachtrij blijven staan. Voor versie 2017-07-29 of hoger kan het maximum aantal time-to-Live elk positief getal zijn of-1 waarmee wordt aangegeven dat het bericht niet verloopt. Als deze para meter wordt wegge laten, is de standaard time-to-Live zeven dagen.

