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
## <a name="what-is-queue-storage"></a>Wat is wachtrijopslag?

Azure Queue Storage is een service voor de opslag van grote aantallen berichten die via HTTP of HTTPS overal vandaan kunnen worden opgevraagd met geverifieerde aanroepen. Een enkel wachtrijbericht mag maximaal 64 KB groot zijn en een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Wachtrijopslag wordt vaak gebruikt om een werkachterstand te maken om asynchroon te verwerken.

## <a name="queue-service-concepts"></a>Serviceconcepten voor wachtrijen

De Azure Queue-service bevat de volgende onderdelen:

![Azure Queue-servicecomponenten](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **URL-indeling:** Wachtrijen zijn adresseerbaar`<storage account>`met de volgende URL-indeling: http:// .queue.core.windows.net/`<queue>`
  
    Met de volgende URL wordt een wachtrij in het diagram opgevraagd:  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Opslagaccount:** Alle toegang tot Azure Storage vindt plaats via een opslagaccount. Zie [Overzicht van opslagaccounts](../articles/storage/common/storage-account-overview.md)voor meer informatie over opslagaccounts.
* **Wachtrij:** Een wachtrij bevat een set berichten. Alle berichten moeten zich in een wachtrij bevinden. De naam van een wachtrij mag alleen kleine letters bevatten. Zie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Wachtrijen en metagegevens een naam geven) voor informatie over de naamgeving van wachtrijen.
* **Bericht:** Een bericht in een willekeurige indeling, van maximaal 64 KB. Een bericht kan maximaal 7 dagen in de wachtrij blijven staan. Voor versie 2017-07-29 of hoger kan de maximale time-to-live een positief getal zijn, of -1 die aangeeft dat het bericht niet verloopt. Als deze parameter wordt weggelaten, is de standaard time-to-live zeven dagen.

